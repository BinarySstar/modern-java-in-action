## 스트림 슬라이싱
### 프리디케이트를 이용한 슬라이싱
- **`takewhile`** 활용 : 주어진 조건을 만족하는 동안 요소를 가져올 수 있게 해주는 함수. 조건이 거짓이 되는 순간, 반복이 종료되고 조건에 맞는 요소들이 반환된다.
 ```Java
  List<Dish> slicedMenu1 = menu.stream()
                              .takeWhile(dish -> dish.getCalories() < 320)
                              .collect(toList());
 ```
- **`dropwhile`** 활용 : `takewhile`과 정반대 작업을 수행한다.
```Java
  List<Dish> slicedMenu2 = menu.stream()
                              .dropWhile(dish -> dish.getCalories() < 320)
                              .collect(toList());
```

### 스트림 축소
스트림은 주어진 값 이하의 크기를 갖는 새로운 스트림을 반환하는 `limit(n)` 메서드를 지원한다. 스트림이 정렬되어 있으면 최대 요소 n개를 반환한다.

### 요소 건너뛰기
스트림은 처음 n개의 요소를 제외한 스트림을 반환하는 `skip(n)` 메서드를 지원한다.
