## 2.2 동작 파라미터화
참 혹은 거짓을 반환하는 함수를 프리디케이트라고 한다. 프리디케이트 인터페이스를 정의하면 다음과 같다.
```Java
public interface ApplePredicate {
  boolean test(Apple apple);
}
```
위 인터페이스를 구현하는 클래스에서는 다음과 같이 여러 버전의 `ApplePredicate`를 정의할 수 있다.
```Java
public class AppleWeightPredicate implements ApplePredicate {

    @Override
    public boolean test(Apple apple) {
      return apple.getWeight() > 150;
    }
}
```
```Java
public class AppleColorPredicate implements ApplePredicate {

    @Override
    public boolean test(Apple apple) {
      return apple.getColor() == Color.GREEN;
    }
}
```
2.1절에서 구현했던 메서드를 `ApplePredicate`를 이용한 `filterApples` 메서드로 구현해보자.
```Java
public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (p.test(apple)) {
        result.add(apple);
      }
    }
    return result;
  }
```
`ApplePredicate` 객체에 의해 `filterApples` 메서드의 동작이 결정된다. 이것이 **동작 파라미터화**이다.  
한 가지 아쉬운 점은 `test` 메서드를 구현해야 새로운 동작을 정의하는 것인데 메서드는 객체만 인수로 받으므로 `test` 메서드를 `ApplePredicate` 객체로 감싸서 메서드에 전달해야 한다. 필터링의 기준에 따라 여러 클래스를 구현해서 인스턴스화하는 과정이 번거롭다. 이 점도 개선해보자. 
