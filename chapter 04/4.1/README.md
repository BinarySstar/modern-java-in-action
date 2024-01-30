## 4.1 스트림이란 무엇인가?
스트림은 자바 8 API에 새로 추가된 기능이다. 스트림을 이용하면 선언형(즉, 데이터를 처리하는 임시 구현 코드 대신 질의로 표현 할 수 있다.)으로 컬렉션 데이터를 처리할 수 있다.
또한 스트림을 이용하면 멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬 처리가 가능하다.

다음 예제는 저칼로리의 요리명을 반환하고, 칼로리를 기준으로 요리를 정렬하는 자바 코드이다. 자바 7 이전 버전과 자바 8 코드를 비교해보자.

### 자바 7
```Java
List<Dish< lowCaloricDishes = new ArrayList<>();
for(Dish dish : menu) {
  if(dish.getCalories() < 400) {
    lowCaloricishes.add(dish);
  }
}

Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
  public int compare(Dish dish1, Dish dish2) {
    reutnr Integer.compare(dish1.getCalories(), dish2.getCalories());
});

List<Strin lowCaloricDishesName = new ArrayList<>();
for(Dish dish : lowCaloricDishes) {
  lowCaloricDishesName.add(dish.getName());
}
```
### 자바 8 (스트림 이용)
```Java
import static java.util.Comparator.comparing;
import static java.util.stream.Collectors.toList;

List<String> lowCaloricDishesName = menu.stream()
  .filter(d -> d.getCalories() < 400) // 400 칼로리 이하의 요리 선택
  .sorted(comparing(Dish::getCalories)) // 칼로리로 요리 정렬
  .map(Dish::getName) // 요리명 추출
  .collect(toList()); // 모든 요리명을 리스트에 저장
```
자바 8의 경우 `stream()`을 `parallelStream()`으로 선언하면 멀티코어 아키텍처에서 병렬로 실행할 수 있다.

스트림은 소프트웨어공학적으로 다음의 다양한 이득을 가져다 준다.
- **선언형으로 코드를 구현할 수 있다.** 루프나 if 조건문 등의 제어 블록을 사용하지 않아도 되어 변화하는 요구사항에 쉽게 대응할 수 있다.
- **`filter`, `sorted`, `map`, `collect` 같은 여러 빌딩 블록 연산을 연결하여 코드의 가독성과 명확성이 유지되는 데이터 처리 파이프라인을 만들 수 있다.**

스트림 API의 특징을 요약하자면 다음과 같다.
- **선언형** : 코드가 더 간결하고 가독성이 좋아진다.
- **조립할 수 있음** : 유연성이 좋아진다.
- **병렬화** : 성능이 좋아진다.
