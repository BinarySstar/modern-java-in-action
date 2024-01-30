## 람다 표현식을 조합할 수 있는 유용한 메서드
`Comparator`, `Function`, `Predicate` 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있도록 유틸리티 메서드를 제공한다. 이 메서드가 바로 **디폴트 메서드**이다.

### Comparator 조합
`Comparator` 인터페이스의 디폴트 메서드로 역정렬을 지원하는 `reversed`가 있다.
```Java
inventory.sort(comparing(Apple::getWeight).reversed());
```
사과의 무게를 내림차순으로 정렬한다.

만약 무게가 같은 사과가 존재한다면 어떻게 해야 할까? 무게가 같은경우 원산지 국가별로 정렬할 수 있다. `thenComparing` 함수를 이용한다.
```Java
inventory.sort(comapring(Apple::getWeight)
  .reversed()
  .thenComparing(Apple::getCountry));
```

### Predicate 조합
Predicate 인터페이슨느 복잡한 프레디케이트를 만들 수 있도록 `negate`, `and`, `or` 세 가지 메서드를 제공한다.
- negate
```Java
Predicate<Apple> notRedApple = redApple.negate(); // 빨간색이 아닌 사과
```
- and
```Java
Predicate<Apple> redAndHeavyApple = redApple.and(apple -> apple.getWeight() > 150); // 빨간색 그리고 무게가 150 이상인 사과
```
- or
```Java
Predicate<Apple> readAndHeavyAppleOrGreen = redApple.and(apple -> apple.getWeight() > 150)
  .or(apple -> GREEN.equals(a.getColor())); // 빨간색이면서 무게가 150 이상인 사과 또는 그냥 초록사과
```

### Function 조합
Function 인터페이스는 `andThen`과 `compose` 두 가지 디폴트 메서드를 제공한다.

예를 들어 숫자를 증가 시키는 x -> x + 1 이라는 f 함수가 있고, 숫자에 2를 곱하는 g 함수가 있다고 가정하자.

- andThen
```Java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.andThen(g); // g(f(x))
int result = h.apply(1); // 4를 반환
```
- compose
```Java
Function<Integer, Integer> h = f.compose(g); // f(g(x))
int result = h.apply(1); // 3을 반환
```
