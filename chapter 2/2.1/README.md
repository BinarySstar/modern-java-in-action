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
