## 매핑
데이터를 처리하는 방법으로 스트림의 매핑이 있다.

### 스트림의 각 요소에 함수 적용하기
스트림은 함수를 인수로 받는 `map` 메서드를 지원한다. 인수로 제공된 함수는 각 요소에 적용되며 함수를 적용한 결과가 새로운 요소로 매핑된다.

아래의 예제는 `Dish::getName`을 `map` 메서드로 전달해서 스트림의 요리명을 추출하는 코드다.
```Java
List<String> dishNames = menu.stream()
                              .map(Dish::getName)
                              .collect(toList());

```
`getName`은 문자열을 반환하므로 `map` 메서드의 출력 스트림은 `Stream<String>` 형식을 갖고 있다.

각 요리명의 길이를 알고 싶으면 `map` 메서드를 연결할 수 있다.
```Java
List<Integer> dishNames = menu.stream()
                              .map(Dish::getName)
                              .map(String::length)
                              .collect(toList());

```
### 스트림 평면화
단어들 사이에 사용된 고유 문자를 반환하고자 할 때에도 스트림을 활용할 수 있다.

예를 들어 ["Hello", "World"] 리스트가 있다면 결과로 ["H", "e", "l", "o", "W", "r", "d"] 리스트가 반환되어야 한다.

```Java
words.stream()
      .map(word -> word.split(""))
      .distinct()
      .collect(toList());
```
위 코드의 문제점은 람다가 각 단어의 `String[]`을 반환한다. 따라서 `map` 메서드가 반환한 스트림의 형식은 `Stream<String[]>`이다. 우리가 원하는 것은 문자열의 스트림을 표현하는 `Stream<String>`이다.

`flatMap` 메서드를 이용하면 이 문제를 해결할 수 있다.
```Java
List<String> uniqueCharacters = words.stream()
                                     .map(word -> word.split(""))   // 각 단어를 개별 문자를 포함하는 배열로 변환
                                     .flafMap(Arrays::stream)       // 생성된 스트림을 하나의 스트림으로 평면화
                                     .distinct()                    
                                     .collect(toList());            
```
