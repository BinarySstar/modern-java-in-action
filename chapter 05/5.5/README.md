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
