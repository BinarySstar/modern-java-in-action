## 람다, 메서드 참조 활용하기
사과 리스트 정렬 코드를 리팩토링하면서 람다에 대한 이해를 한다.

### 1단계 : 코드 전달
자바 8의 List API에서 `sort` 메서드를 제공하므로 정렬 메서드에 대한 직접적인 구현은 필요없고 정렬 기준을 전달해야하는 방법만 고안하면 된다. 먼저 `sort` 메서드를 살펴보자.
```Java
void sort(Comparator<? super E> c)
```
Comparator 객체를 인수로 받아 두 사과를 비교한다. 객체 안에 동작을 포함시키는 방식으로 다양한 정렬 기준을 전달할 수 있다.(동작 파라미터화)

초기 코드는 다음과 같이 완성할 수 있다.
```Java
public class AppleComparator implements Comparator<Apple> {
    @Override
    public int compare(Apple a1, Apple a2) {
      return a1.getWeight() - a2.getWeight();
    }
}
inventory.sort(new AppleComparator());
```

### 2단계 : 익명 클래스 사용
한 번만 사용할 `Comparator`를 익명 클래스를 사용하여 더 간단하게 구현할 수 있다.
```Java
inventory.sort(new Comparator<Apple>(){
  public int compare(Apple a1, Apple a2) {
      return a1.getWeight() - a2.getWeight();
    }
});
```

### 3단계 : 람다 표현식 사용
```Java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight() - a2.getWeight());
inventory.sort((a1, a2) -> a1.getWeight() - a2.getWeight()); // 형식 추론으로 다음과 같이 줄일 수 있음
```
`Comparator`는 `Comparable` 키를 추출해서 `Comparator` 객체로 만드는 `Function` 함수를 인수로 받는 정적 메서드 `comparing`이 있다. 다음처럼 `comparing` 메서드를 사용할 수 있다.
```Java
Comparator<Apple> c = Comparator.comparing((Apple a) -> a.getWeight());
```
코드를 다음처럼 간소화할 수 있다.
```Java
inventory.sort(comparing(apple -> apple.getWeight());
```

### 4단계 : 메서드 참조 사용
```Java
inventory.sort(comparing(Apple::getWeight));
```
메서드 참조를 사용하여 코드가 짧아진 것 뿐만 아니라 코드 자체로 'Apple을 weight별로 비교해서 inventory에 sort하라`는 명확한 의미를 전달할 수 있다.
