## 6.4 분할
분할 함수(partitioning function)라고 불리는 프레디케이트를 분류 함수로 사용하는 특수한 그룹화 기능이다.
분함 함수는 불리언을 반환하므로 맵의 키 형식은 `Boolean`이다. 결과적으로 그룹화 맵은 최대 두개의 그룹으로 나뉜다.

요리를 채식요리와 채식요리가 아닌 것으로 분류하는 코드는 다음과 같다.
```Java
Map<Boolean, List<Dish>> partitionedMenu =
    menu.stream().collect(partitioningBy(Dish::isVegetarian));
```
```
{false=[pork, beef, chicken, prawns, salmon], true = [french fries, rice, season fruit, pizza]}
```

### 분할의 장점
분할 함수가 반환하는 참, 거짓 두 가지 요소의 스트림 리스트를 모두 유지한다는 점이 분할의 장점이다. 
분할를 사용한다면 스트림 작업을 한 번만 수행하여 결과를 두 개의 그룹으로 분류할 수 있어 가독성 면에서 더 뛰어나다. 
또한 두번째 인수를 전달할 수 있는 `partitioningBy` 메서드를 사용한다면 더욱 세부적으로 리스트를 생성할 수 있다.
```Java
Map<Boolean, Map<Dish.Type, List<Dish>> vegetarianDishesByType = menu.stream().collect(
      partitioningBy(Dish::isVegetarian, groupingBy(Dish::getType)));
```
```
{false={FISH=[prawns, salmon], MEAT=[pork, beef, chicken]},
true={OTHER=[french fries, rice, season fruit, pizza]}}}
```
`partitioningBy`가 반환한 맵 구현은 코드도 짧아지지만 참과 거짓 두 가지 키만 포함하여, 간결하고 효과적으로 표현할 수 있다. 또한 다수준으로 분할 역시 가능하다.
