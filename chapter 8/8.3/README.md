## 8.3 맵 처리
자바 8에서는 `Map` 인터페이스에 몇 가지 디폴트 메서드를 추가하였다.
### `forEach` 메서드
키와 값을 반복하면서 확인하는 기능을 제공한다.
```Java
ageOfFriends.forEach((friend, age) -> System.out.println(friend + " is " + age + " years old"));
```
### 정렬 메서드
맵의 항목을 값 또는 키를 기준으로 정렬할 수 있다.
```Java
Map<String, String> favoriteFruit = Map.ofEntries(
      entry("홍길동", "사과"),
      entry("김계란", "바나나"),
      entry("강호동", "파인애플")
);
favoriteFruit.entrySet()
      .stream()
      .sorted(Map.Entry.comparingByKey())
      .forEachOrdered(System.out::println);
```
```
강호동=파인애플
김계란=바나나
홍길동=사과
```
### `getOrDefault` 메서드
기존에는 찾으려는 키가 존재하지 않으면 `NullPointerException`이 발생하였다. 여기에 기본값을 반환하는 `getOrDefault` 메서드를 이용하면 예외가 발생하지 않는다.

이 메서드의 첫 번째 인수로 키 값을 받고 두 번째 인수로 기본값을 받는다. 맴에 키가 존재하지 않으면 두 번째 인수로 받은 기본값을 반환한다.
```Java
System.out.println(favoriteFruit.getOrDefault("이수근","키위"));// 키위 출력
```
### 계산 패턴
Map에 key가 존재하는지에 따라 어떤 동작을 실행하고 결과를 저장해야 하는지 상황일 때 사용하는 패턴
- `computeIfAbsent` : 제공된 키에 해당하는 값이 없으면 키를 이용해서 새 값을 계산하고 맵에 추가
- `computeIfPresent` : 제공된 키가 존재하면 새 값을 계산하고 맵에 추가
- `compute` : 제공된 키로 새 값을 계산하고 맵에 추가

### 삭제 패턴
자바 8에서는 키가 특정한 값과 연관되었을 때만 항목을 제거하는 오버로드 버전 `remove` 메서드를 제공한다.

### 교체 패턴
맵의 항목을 바꾸는데 사용할 수 있는 두 개의 메서드가 있다.
- `replaceAll`
- `Replace` : 키가 존재하면 맵의 값을 바꾼다.
