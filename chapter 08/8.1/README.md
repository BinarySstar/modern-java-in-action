## 8.1 컬렉션 팩토리
자바 9에서는 컬렉션 객체를 쉽게 만들수 있는 몇 가지 방법을 제공한다. 이들의 크기는 모두 고정되어 있으므로 컬렉션 생성 후, `add` 등의 메서드로 요소를 추가할 수 없다.

### 리스트 팩토리
```Java
List<String> friends = List.of("LEE", "SANG", "WON");
```
### 집합 팩토리
```Java
Set<String> friends = Set.of("LEE", "SANG", "WON");
```
### 맵 팩토리
```Java
Map<String, Integer> friendsMap = Map.of("LEE", 1, "SANG", 2, "WON", 3);
```
```Java
Map<String, Integer> ageOfFrieds = Map.ofEntries(
  entry("LEE", 1),
  entry("SANG", 2),
  entry("WON", 3));
```
