## 6.1 컬렉터란 무엇인가?

함수형 프로그래밍에서는 무엇을 원하는지 직접 명시할 수 있어서 명령형 프로그래밍에 비해 가독성, 유지보수 면에서 뛰어나다.
스트림에서 `collect` 함수를 이용하여 스트림을 리스트로 바꾸는 `toList`를 썼지만 이 외에도 다양한 요소 누적 방식을 인수로 받아서 스트림을 최종 결과로 도출하는 리듀싱 연산을 수행할 수 있음을 알아보려고 한다.

### 고급 리듀싱 기능을 수행하는 컬렉터

앞에서 말한 다양한 요소 누적 방식은 `Collector` 인터페이스에 정의되어 있다. `Collector` 인터페이스의 메서드를 어떻게 구현하느냐에 따라 스트림에 어떤 리듀싱 연산을 수행할지 결정된다.
예를 들어 가장 많이 사용하는 정적 팩토리 메서드로 `toList`가 있다.

### 미리 정의된 컬렉터

`groupingBy`와 같이 `Collectors` 구현 클래스에서 제공하는 팩토리 메서드를 의미한다. `Collectors`에서 제공하는 메서드의 기능은 크게 3가지로 구분할 수 있다.

- 스트림 요소를 하나의 값으로 리듀스하고 요약
- 요소 그룹화
- 요소 분할

## 6.2 리듀싱과 요약

- **`counting()`** : 요소의 개수를 반환한다.

```Java
long howManyDishes = menu.stream().collect(Collectors.counting());
```

### 스트림값에서 최댓값과 최솟값 검색

`maxBy` , `minBy` : Stream의 요소를 비교하는 데 사용할 `Comparator`를 인수로 받는다.

```Java
Comparator<Dish> dishCaloriesComparator = Comparator.comparingInt(Dish::getCalrories);

Optional<Dish> mostCalorieDish = menu.stream().collect(maxBy(dishCaloriesComparator));
```

### 요약 연산

스트림에 있는 객체의 숫자 필드의 합계나 평균 등을 반환하는 연산을 요약이라고 한다.

- 단순 합계
  - `summingInt` : 객체를 `int`로 매핑하는 함수를 인수로 받는다.
  - `Collectors.summingLong`과 `Collectors.summingDouble`도 있다.
  ```Java
  int totalCalories = menu.stream().collect(summingInt(Dish::getCalrories))
  ```
- 평균
  - `averagingInt`, `averagingLong`, `averageDouble` 등등...
  ```Java
  double avgCalrories = menu.stream().collect(averagingInt(Dish::getCalrories))
  ```
- 합계, 평균 등 두 개 이상의 연산을 한 번에 수행해야할 때는 `summarizingInt` 사용
  ```Java
  IntSummaryStatistics menuStatistics = menu.stream().collect(summarizingInt(Dish::getCalrories));
  // menuStatistics : IntSummaryStatistics{count=9, sum=4300, min=120, average=477.778, max=800}
  ```

### 문자열 연결

컬렉터에 `joining` 팩터리 메서드를 이용하면 스트림의 각 객체에 `toString` 메서드를 호출해서 추출한 모든 문자열을 하나의 문자열로 연결해서 반환한다.

```Java
String shortMenu = menu.stream().map(Dish::getName).collect(joining(", "));
```

```
pork, beef, chicken, french fries, ...
```

### 범용 리듀싱 요약 연산

앞에서 살펴본 모든 컬렉터를 `Collectors.reducing` 로 정의할 수 있다.
컬렉션 프레임워크는 같은 연산도 다양한 방식으로 수행할 수 있으므로 상황에 맞는 최적의 해법을 선택해야 한다.

### 6.3 그룹화

`Collectors.groupingBy` 정적 팩토리 메서드를 이용하면 쉽게 그룹화할 수 있다.

```Java
/**
  * 그룹화 groupingBy
  * 생선, 고기 그 밖의 것들로 그룹화
  */
Map<Type, List<Dish>> dishesByType = menu.stream().collect(groupingBy(Dish::getType));
```

```Java
// 칼로리로 구분
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel = menu.stream().collect(
  groupingBy(dish -> {
    if(dish.getCalories() <= 400) return CaloricLevel.DIET;
    else if(dish.getCalories() <= 700) return CaloricLevel.NORMAL;
    else return CaloricLevel.FAT;
  }));
```

```Java
// 500칼로리가 넘는 요리만 타입과 종류로 그룹화
Map<Type, List<Dish>> caloricDishesByType = menu.stream().filter(dish -> dish.getCalroies() > 500)
  .collect(groupingBy(Dish::getType));
/**
  * 결과
  * {OTHER=[french fries, pizza], MEAT=[pork, beef]}
  * 위 코드의 단점은 위 filter 프레디케이트를 만족하는 값이 없을 경우 키값 자체가 제외되서 맵에 담지 못한다.
  * 해결책 : Collectors 클래스의 정적 팩터리 메서드인 filtering 사용
  */

// 해결
Map<Type, List<Dish>> caloricDishesByType = menu.stream().collect(groupingBy
  (Dish::getType, filtering(dish -> dish.getCalories() > 500, toList())));
// 결과 : {OTHER=[french fries, pizza], MEAT=[pork, beef], FISH=[]}
```

### 다수준 그룹화

`Collectors.groupingBy` 정적 팩토리 메서드를 이용하면 항목을 다수준으로 그룹화할 수 있다.

```Java
Map<Dish.Type, Map<CaloricLevel, List<Dish>>> groupDishedByTypeAndCaloricLevel =
menu.stream().collect(
    groupingBy(Dish::getType,
        groupingBy((Dish dish) -> {
          if (dish.getCalories() <= 400) {
            return CaloricLevel.DIET;
          }
          else if (dish.getCalories() <= 700) {
            return CaloricLevel.NORMAL;
          }
          else {
            return CaloricLevel.FAT;
          }
        })
    )
);
```

### 서브그룹으로 데이터 수집

`Collectors.collectingAndThen` 팩토리 메서드는 적용할 컬렉터와 변환 함수를 인수로 받아 다른 컬렉터를 반환한다.

```Java
Map<Type, Dish> mostCaloricByType =
  menu.stream()
    .collect(groupingBy(Dish::getType, collectingAndThen(maxBy(comparingInt(Dish::getCaloreis)), Optioanl::get)));
```

`Optional::get`으로 반환된 `Optional`에 포함된 값을 추출한다.

## 6.4 분할

분할 함수(partitioning function)라고 불리는 프레디케이트를 분류 함수로 사용하는 특수한 그룹화 기능이다.
분함 함수는 불리언을 반환하므로 맵의 키 형식은 `Boolean`이다. 결과적으로 그룹화 맵은 최대 두개의 그룹으로 나뉜다.

요리를 채식요리와 채식요리가 아닌 것으로 분류하는 코드는 다음과 같다.

```Java
Map<Boolean, List<Dish>> partitionedMenu =
    menu.stream().collect(partitioningBy(Dish::isVegetarian));
```

```
{false=[pork, beef, chicken, prawns, salmon], true = [french fries, rice, season fruit, pizza]}
```

### 분할의 장점

분할 함수가 반환하는 참, 거짓 두 가지 요소의 스트림 리스트를 모두 유지한다는 점이 분할의 장점이다.
분할를 사용한다면 스트림 작업을 한 번만 수행하여 결과를 두 개의 그룹으로 분류할 수 있어 가독성 면에서 더 뛰어나다.
또한 두번째 인수를 전달할 수 있는 `partitioningBy` 메서드를 사용한다면 더욱 세부적으로 리스트를 생성할 수 있다.

```Java
Map<Boolean, Map<Dish.Type, List<Dish>> vegetarianDishesByType = menu.stream().collect(
      partitioningBy(Dish::isVegetarian, groupingBy(Dish::getType)));
```

```
{false={FISH=[prawns, salmon], MEAT=[pork, beef, chicken]},
true={OTHER=[french fries, rice, season fruit, pizza]}}}
```

`partitioningBy`가 반환한 맵 구현은 코드도 짧아지지만 참과 거짓 두 가지 키만 포함하여, 간결하고 효과적으로 표현할 수 있다. 또한 다수준으로 분할 역시 가능하다.
