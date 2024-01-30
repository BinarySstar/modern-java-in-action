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
