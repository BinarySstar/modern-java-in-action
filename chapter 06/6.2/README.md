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
