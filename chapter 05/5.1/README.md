## 필터링
스트림의 요소를 선택하는 방법으로 프리디케이트 필터링 방법과 고유 요소만을 필터링하는 방법에 대해 알아본다.

### 프리디케이트로 필터링
불리언을 반환하는 프리디케이트를 인수로 받아 스트림을 반환할 것이다.
```Java
List<Dish> vegetarianMenu = menu.stream()
                                .filter(Dish::isVegetarian)
                                .collect(toList());
```
![image](https://github.com/BinarySstar/modern-java-in-action/assets/117090689/23e5e03d-2a39-4198-9a58-98dc2062e1e2)

### 고유 요소 필터링
스트림은 고유 요소로 이루어진 스트림을 반환하는 `distinct` 메서드를 지원한다. 중복을 제거한다.
```Java
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4)
        .stream()
        .filter(number -> number % 2==0)
        .distinct()
        .forEach(System.out::println);
```
![image](https://github.com/BinarySstar/modern-java-in-action/assets/117090689/e79e0f80-9257-40f8-8e1d-4864628b3536)

