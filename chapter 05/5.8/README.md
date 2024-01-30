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
