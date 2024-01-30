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
