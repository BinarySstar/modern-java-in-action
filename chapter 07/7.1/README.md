## 7.1 병렬 스트림
컬렉션에서 `parallelStream`을 호출하면 병렬 스트림이 생성된다. 
병렬 스트림이란 각각의 스레드에서 처리할 수 있도록 스트림 요소를 여러 청크로 분할한 스트림이다.
따라서 병렬 스트림을 이용하면 모든 멀티코어 프로세서가 각각의 청크를 처리하도록 할당할 수 있다.
### 순차 스트림을 병렬 스트림으로 변환하기
숫자 n을 인수로 받아서 1부터 n까지의 모든 숫자를 합하는 메서드를 구현해보자.
```Java
public long sequentialSum(long n) {
  return Stream.iterate(1L, i -> i + 1)
                .limit(n)
                .reduce(0L, Long::sum);
  }
```
이 메서드를 병렬 스트림으로 변환하면 다음과 같다.
```Java
public long parallelSum(long n) {
  return Stream.iterate(1L, i -> i + 1)
                .limit(n)
                .parallel()
                .reduce(0L, Long::sum);
  }
```
반대로 `sequential`로 병렬 스트림을 순차 스트림으로 바꿀 수 있다.

### 병렬 스트림의 올바른 사용법
```Java
public long sideEffectSum(long n) {
	Accumulator accmulator = new Accumulator();
	LongStream.rangeClosed(1, n).forEach(accumulator::add);
	return accumulator.total;
}

public class Accumulator {
	public long total = 0;
	public void add(long  value) { 
		total += value;
	}
}
```
병렬 실행시 공유된 변수 사용 피해야한다.

### 병렬 스트림 효과적으로 사용하기
- 병렬화를 이용한다 하여 순차나 반복 형식에 비해 성능이 항상 더 좋은 것은 아니다. 자바 마이크로벤치마크 하니스(JMH)를 이용하여 벤치마크를 구현할 수 있다. 순차 스트림과 병렬 스트림 중 어떤 것이 좋을 지 모르겠다면 적절한 벤치마크로 성능을 직접 측정하는 것이 바람직하다.
- 박싱을 주의해야 한다. 자동 박싱과 언박싱은 성능을 저하시킬 수 있는 요인 중 하나이다. 기본형 특화 스트림 `IntStream`, `DoubleStream`, `LongStream`을 사용하는 것이 좋다.
- 순차 보다 병렬에서 성능이 떨어지는 연산을 피해라. `limit`, `findFirst` 같은 요소의 순서에 의존하는 연산을 병렬 스트림에서 하려면 비싼 비용을 치러야한다. 예를 들어 `findAny`는 요소의 순서와 상관없이 연산해서 `findFirst`보다 성능이 좋다.
- 소량의 데이터는 도움이 되지 않는다. 병렬화 과정에서 생기는 부가 비용을 상쇄할 만큼 이득을 얻지 못한다.
- 스트림에서 수행하는 전체 파이프라인의 연산 비용을 고려하라. 처리해야 할 요소 수가 N이고 하나의 요소를 처리하는데 드는 비용을 Q라고 하면 전체 스트림 파이프라인 처리 비용은 N * Q 이다. Q가 높다는 것은 병렬 스트림으로 성능을 개선할 수 있다는 가능성이 크다.
- 스트림을 구성하는 자료구조가 적절한지 확인하라.
