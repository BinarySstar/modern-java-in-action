## 2.1 변화하는 요구사항에 대응하기

- **동작 파라미터화(Behavior parameterization)** : 변화하는 요구사항에 효과적으로 대응할 수 있는 코드 블록

예제를 선정한 다음에 변화하는 요구사항에 쉽게 대응할 수 있는 유연한 코드를 작성해본다.

### 사과 농장 재고 어플리케이션에 리스트에서 녹색 사과만 필터링하는 기능을 추가한다.

```Java
enum Color {RED, GREEN}
```

```Java
public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (apple.getColor() == Color.GREEN) {
        result.add(apple);
      }
    }
    return result;
  }
```

여기서 녹색뿐만 아니라 빨간색, 옅은 녹색, 어두운 빨간색, 노란색 등 다양한 색으로 필터링하는 요구사항이 제시될 수도 있다. 색깔로 필터링하는 코드는 다음과 같다.

```Java
public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (apple.getColor().equals(color)) {
        result.add(apple);
      }
    }
    return result;
  }
```

더 나아가 색깔 뿐만 아니라 무게로 필터링하는 기능을 추가한다면 코드를 다음과 같이 작성할 수 있다.

```Java
public static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
      if (apple.getWeight() > weight) {
        result.add(apple);
      }
    }
    return result;
  }
```

필터링의 기준만 다를뿐 코드의 구조는 동일하다. 필터링의 기준에 상관없이 코드를 유연하게 작성하는 **동작 파라미터화**를 이용할 것이다.

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

## 2.3 복잡한 과정 간소화

### 익명 클래스

익명 클래스를 이용하면 클래스 선언과 인스턴스화를 동시에 할 수 있다. 즉석에서 필요한 구현을 만들어서 사용할 수 있다.

```Java
public static List<Apple> filterApples(List<Apple> inventory, new ApplePredicate(){
  public boolean test(Apple apple){
    return apple.getColor() == RED;
  }
});
```

인터페이스를 구현하는 여러 클래스를 선언하는 과정을 조금 줄일 수 있지만 근본적인 문제는 해결되지 않았다. `ApplePredicate` 객체를 만들고 `test` 메서드를 새로 구현해야 한다는 점은 여전히 동일하다.

### 람다

자바 8의 람다를 이용하면 코드를 간단하게 구현할 수 있다.

```Java
List<Apple> result = filterApples(inventory, (Apple apple) -> apple.getColor() == RED);
```

동작 파라미터화를 이용하는 방법으로 인터페이스를 구현하는 클래스, 익명 클래스, 람다가 있는데 람다가 이 중에서는 가장 간결하고 유연하다.

사과뿐만 아니라 여러 객체에서도 필터링 메서드를 구현하려면 다음과 같이 추상화를 진행하면 된다.

```Java
public interface Predicate<T>{
  boolean test(T t);
}
```

```Java
public static <T> List<T> filter(List<T> inventory, Predicate<T> p) {
    List<T> result = new ArrayList<>();
    for (T e : inventory) {
      if (p.test(e)) {
        result.add(e);
      }
    }
    return result;
}
```

빨간 사과를 필터링하거나, 짝수 정수만 필터링하려면 다음과 같이 코드를 작성할 수 있다.

```Java
List<Apple> redApples = filter(inventory, (Apple apple) -> apple.getColor() == RED);
List<Integer> evenNumbers = filter(inventory, (Integer i) -> i % 2 == 0);
```
