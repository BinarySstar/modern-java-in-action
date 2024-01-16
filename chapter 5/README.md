## 5.1 필터링

스트림의 요소를 선택하는 방법으로 프리디케이트 필터링 방법과 고유 요소만을 필터링하는 방법에 대해 알아본다.

### 프리디케이트로 필터링

불리언을 반환하는 프리디케이트를 인수로 받아 스트림을 반환할 것이다.

```Java
List<Dish> vegetarianMenu = menu.stream()
                                .filter(Dish::isVegetarian)
                                .collect(toList());
```

![image](https://github.com/BinarySstar/modern-java-in-action/assets/117090689/23e5e03d-2a39-4198-9a58-98dc2062e1e2)

### 고유 요소 필터링

스트림은 고유 요소로 이루어진 스트림을 반환하는 `distinct` 메서드를 지원한다. 중복을 제거한다.

```Java
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4)
        .stream()
        .filter(number -> number % 2==0)
        .distinct()
        .forEach(System.out::println);
```

![image](https://github.com/BinarySstar/modern-java-in-action/assets/117090689/e79e0f80-9257-40f8-8e1d-4864628b3536)

## 5.2 스트림 슬라이싱

### 프리디케이트를 이용한 슬라이싱

- **`takewhile`** 활용 : 주어진 조건을 만족하는 동안 요소를 가져올 수 있게 해주는 함수. 조건이 거짓이 되는 순간, 반복이 종료되고 조건에 맞는 요소들이 반환된다.

```Java
 List<Dish> slicedMenu1 = menu.stream()
                             .takeWhile(dish -> dish.getCalories() < 320)
                             .collect(toList());
```

- **`dropwhile`** 활용 : `takewhile`과 정반대 작업을 수행한다.

```Java
  List<Dish> slicedMenu2 = menu.stream()
                              .dropWhile(dish -> dish.getCalories() < 320)
                              .collect(toList());
```

### 스트림 축소

스트림은 주어진 값 이하의 크기를 갖는 새로운 스트림을 반환하는 `limit(n)` 메서드를 지원한다. 스트림이 정렬되어 있으면 최대 요소 n개를 반환한다.

### 요소 건너뛰기

스트림은 처음 n개의 요소를 제외한 스트림을 반환하는 `skip(n)` 메서드를 지원한다.

## 5.3 매핑

데이터를 처리하는 방법으로 스트림의 매핑이 있다.

### 스트림의 각 요소에 함수 적용하기

스트림은 함수를 인수로 받는 `map` 메서드를 지원한다. 인수로 제공된 함수는 각 요소에 적용되며 함수를 적용한 결과가 새로운 요소로 매핑된다.

아래의 예제는 `Dish::getName`을 `map` 메서드로 전달해서 스트림의 요리명을 추출하는 코드다.

```Java
List<String> dishNames = menu.stream()
                              .map(Dish::getName)
                              .collect(toList());

```

`getName`은 문자열을 반환하므로 `map` 메서드의 출력 스트림은 `Stream<String>` 형식을 갖고 있다.

각 요리명의 길이를 알고 싶으면 `map` 메서드를 연결할 수 있다.

```Java
List<Integer> dishNames = menu.stream()
                              .map(Dish::getName)
                              .map(String::length)
                              .collect(toList());

```

### 스트림 평면화

단어들 사이에 사용된 고유 문자를 반환하고자 할 때에도 스트림을 활용할 수 있다.

예를 들어 ["Hello", "World"] 리스트가 있다면 결과로 ["H", "e", "l", "o", "W", "r", "d"] 리스트가 반환되어야 한다.

```Java
words.stream()
      .map(word -> word.split(""))
      .distinct()
      .collect(toList());
```

위 코드의 문제점은 람다가 각 단어의 `String[]`을 반환한다. 따라서 `map` 메서드가 반환한 스트림의 형식은 `Stream<String[]>`이다. 우리가 원하는 것은 문자열의 스트림을 표현하는 `Stream<String>`이다.

`flatMap` 메서드를 이용하면 이 문제를 해결할 수 있다.

```Java
List<String> uniqueCharacters = words.stream()
                                     .map(word -> word.split(""))   // 각 단어를 개별 문자를 포함하는 배열로 변환
                                     .flafMap(Arrays::stream)       // 생성된 스트림을 하나의 스트림으로 평면화
                                     .distinct()
                                     .collect(toList());
```

## 5.4 검색과 매칭

특정 속성이 데이터 집합에 있는지 여부를 검색하는 데이터 처리도 자주 사용된다.
스트림 API에서 `allMatch`, `anyMatch`, `noneMatch`, `findFirst`, `findAny` 메서드를 제공한다.

### `anyMatch` 메서드

프리디케이트가 적어도 한 요소와 일치하는지 확인한다.

```Java
if( menu.stream().anyMatch(Dish::isVegetarian) ) {
    System.out.println( "채식 요리" );
}
```

### `allMatch` 메서드

스트림의 모든 요소가 주어진 프리디케이트와 일치하는지 검사한다.

```Java
boolean isHealthy = menu.stream()
                        .allMatch(dish -> dish.getCalories() < 1000);
```

`noneMatch`는 `allMatch`와 반대 연산을 수행한다. 즉, `noneMatch`는 주어진 프리디케이트와 일치하는 요소가 없으면 true를 반환한다. 이전 예제는 다음과 같이 구현할 수 있다.

```Java
boolean isHealthy = menu.stream()
                        .noneMatch(dish -> dish.getCalories() >= 1000);
```

`anyMatch`, `allMatch`, `noneMatch` 세 메서드는 스트림의 **쇼트서킷** 기법을 활용한다.

> **쇼트서킷**
>
> 전체 스트림을 처리하지 않았더라도 결과를 반환할 수 있다. 예를 들어 여러 and 연산으로 연결된 불리언 표현식을 평가한다고 가정하자. 표현식에서 하나라도 거짓이라면
> 나머지 표현식의 결과와 상관없이 전체 결과가 거짓이 된다. 이를 쇼트서킷이라 부른다. `anyMatch`, `allMatch`, `noneMatch`, 'findFirst', 'findAny' 등의 연산은 모든
> 스트림의 요소를 처리하지 않고도 결과를 반환할 수 있다. 원하는 요소를 찾았으면 즉시 결과를 반환한다. 특히 무한한 요소를 가진 스트림을 유한한 크기로 줄일 수 있는 유용한 연산이다.

### `findAny` 메서드

현재 스트림에서 임의의 요소를 반환한다.

```Java
Optional<Dish> dish = menu.stream()
                          .filter(Dish::isVegetarian)
                          .findAny();
```

이 예제는 임의로 채식주의자를 반환한다.

### `findFirst` 메서드

스트림의 첫 번째 요소를 반환한다.

```Java
List<Integer> someNumbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> firstSquareDivisibleByThree = someNumbers.stream()
                                                           .map(n -> n * n)
                                                           .filter(n -> n % 3 == 0)
                                                           .findFirst();  //9
```

`findFirst`와 `findAny`는 언제 사용할까? 그 차이는 병렬성에 있다. 병렬 실행에서는 첫 번째 요소를 찾기 어렵다. 따라서 요소의 반환 순서가 상관없다면 병렬 스트림에서는 제약이 적은 `findAny`를 사용한다.

## 5.5 리듀싱

스트림에서 리듀싱은 스트림의 모든 요소를 하나의 값으로 줄이는(reduce) 작업을 수행한다. 리듀싱은 대개 스트림의 모든 요소를 하나의 값으로 합치기 위해 사용된다.

### 요소의 합

`reduce` 메서드를 살펴보기 전에 `for-each` 루프를 이용해서 리스트의 숫자 요소를 더하는 코드를 확인하자.

```Java
int sum = 0;
for (int x : numbers) {
	sum += x;
}
```

for-loop 에서 사용된 파라미터는 변수 초깃값을 나타내는 `sum`과 리스트의 모든 요소를 조합하는 연산(+)이다.

`reduce`로 간단하게 반복되는 패턴을 추상화할 수 있다.

```Java
int sum = numbers.stream().reduce(0, (a, b) -> a + b);
```

메서드 참조를 이용해서 이 코드를 조금 더 간결하게 만들 수 있다. 자바 8에서는 `Integer` 클래스에 두 숫자를 더하는 정적 `sum` 메서드를 제공한다.

```Java
int sum = numbers.stream().reduce(0, Integer::sum);
```

초깃값을 받지 않도록 오버로드된 `reduce`도 있다. 이 `reduce`는 `Optional` 객체를 반환한다.

```Java
Optional<Integer> sum = numbers.stream().reduce(Integer::sum);
```

### 최댓값과 최솟값

마찬가지로 `Integer` 클래스의 `max`와 `min` 메서드를 이용하면 쉽게 구할 수 있다.

```Java
Optional<Integer> max = numbers.stream().reduce(Integer::max);
Optional<Integer> min = numbers.stream().reduce(Integer::min);
```

## 5.7 숫자형 스트림

아래와 같은 코드를 통해 메뉴의 칼로리 합계를 구할 수 있다.

```Java
int calories = menu.stream()
                   .map(Dish::getCalories)
                   .reduce(0, Integer::sum);
```

하지만 이 코드는 합계를 계산하기 전에 `Integer`를 기본형으로 언박싱하는 과정을 거치기 때문에 박싱 비용이 추가된다. 내부적으로 합계를 계산하기 전에 기본형으로 언박싱해야한다.
스트림 API 숫자 스트림을 효율적으로 처리할 수 있도록 하는 **기본형 특화 스트림**을 제공한다.

### 기본형 특화 스트림

스트림 API는 박싱 비용을 피할 수 있도록 int 요소에 특화된 `IntStream`, double 요소에 특화된 `DoubleStream`, long 요소에 특화된 `LongStream`을 제공한다.
각각의 인터페이스에는 숫자 스트림의 합계를 계산하는 `sum`, 최댓값을 구하는 `max`, 필요할 때 객체 스트림으로 복원하는 기능도 제공한다.

스트림을 특화 스트림으로 변환할 때는 `mapToInt`, `mapToDouble`, `mapToLong`과 같은 메서드를 사용하며, 반환 값으로 특화된 스트림을 반환한다.

```Java
int calories = menu.stream() //Stream<Dish> 반환
                   .mapToInt(Dish::getCalories) //IntStream 반환
                   .sum();
```

숫자 스트림을 만든 후에 원상태로 복구하려면 `boxed` 메서드를 이용하면 된다.

```Java
IntStream intStream = menu.stream().mapToInt(Dish::getCalories);
Stream<Integer> stream = intStream.boxed(); // 숫자 스트림을 스트림으로 변환
```

스트림에 요소가 없는 상황과 실제 최댓값이 0인 상황을 구별하기 위해서 컨테이너 클래스인 Optional을 이용할 수 있는데,
`Optional`을 `Integer`, `String` 등의 참조 형식으로 파리미터화할 수 있으며 `OptionalInt`와 같이 기본형 특화 스트림 버전도 제공한다.

```Java
OptionalInt maxCalories = menu.stream()
                              .mapToInt(Dish::getCalories)
                              .max();

maxCalories.orElse(1); //값이 없을 때 기본 최댓값을 명시적으로 설정
```

### 숫자 범위

자바 8의 `IntStream`과 `LongStream`에서는 `range`와 `rangeClosed`라는 두 가지 정적 메서드를 제공하는데 공통적으로 첫 번째 인수로 시작 값을, 두 번째 인수로 종료 값을 갖는다.
차이점이 있다면 `range`는 시작 값과 종료 값이 결과에 포함되지 않는 반면 `rangeClosed`는 결과에 포함된다는 점이다

```Java
IntStream evenNumbers = IntStream.rangeClosed(1, 100) // 1부터 100까지
                                 .filter(n -> n % 2 == 0)
System.out.println(evenNumbers.count()); //50개의 짝수 출력
```

## 5.8 스트림 만들기

### 값으로 스트림 만들기

임의의 수를 인수로 받는 정적 메서드 `Stream.of`를 이용해서 스트림을 만들 수 있다.

```Java
Stream<String> stream = Stream.of("Modern", "Java", "In", "Action");
stream.map(String::toUpperCase).forEach(System.out::println);
```

`empty` 메서드를 이용해서 스트림을 비울 수 있다.

```Java
Stream<String> emptyStream = Stream.empty();
```

### null이 될 수 있는 객체로 스트림 만들기

자바 9부터 지원되며 `Stream.ofNullable` 메서드를 이용하여 null이 될 수 있는 객체를 지원하는 스트림을 만들 수 있다.

### 배열로 스트림 만들기

`Arrays.stream`을 이용하여 배열을 스트림으로 만들 수 있다.

```Java
int[] numbers = {0, 1, 2, 3, 4};
int sum = Arrays.straem(numbers).sum(); //IntStream으로 변환 후, IntStream 메서드 sum 진행
```

### 함수로 무한 스트림 만들기

`Stream.iterate`와 `Stream.generate`를 통해 함수를 이용하여 **무한 스트림**을 만들 수 있다. `iterate`와 `generate`에서 만든 스트림은 요청할 때마다 주어진 함수를 이용해서 값을 만든다.
따라서 무제한으로 값을 계산할 수 있지만, 보통 무한한 값을 출력하지 않도록 `limit(n)` 함수를 함께 연결해서 사용한다.

#### `iterate` 메서드

```Java
Stream.iterate(0, n -> n + 2)
      .limit(10)
      .forEach(System.out::println);
```

`iterate` 메서드는 초깃값(예제에서는 0)과 람다를 인수로 받아서 새로운 값을 무한히 생산할 수 있다. 예제에서는 이전 결과에 2를 더한 값을 반환한다.
`limit(10)`을 이용하여 처음 10개의 짝수만 스트림에 추가한다. 일반적으로 연속된 일련의 값을 만들 때 사용한다.

자바 9의 `iterate` 메서드는 프리디케이드를 지원한다. 예를 들어 0에서 시작해서 100보다 크면 숫자 생성을 중단하는 코드는 다음과 같다.

```Java
IntStream.iterate(0, n -> n < 100, n -> n + 2)
      .forEach(System.out::println);
```

`iterate` 메서드 두 번쨰 인수로 프리디케이트를 받아 작업의 범위를 제시한다.

#### `generate` 메서드

`iterate`와 마찬가지로 값을 계산하는 무한 스트림을 만들 수 있다. 하지만 `iterate`와 달리 `generate`는 생상된 각 값을 연속적으로 계산하지 않는다.
