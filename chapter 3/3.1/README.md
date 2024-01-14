## 3.1 람다란 무엇인가?

**람다** : 메서드로 전달할 수 있는 익명 함수를 단순화한 형태

코드를 더욱 간결한 방식으로 전달할 수 있다. 물론 람다가 항상 옳은 방식은 아니며 코드가 2줄 이상 복잡해진다면 메서드로 따로 구현한다. 코드의 간결성을 해치지 않는 것이 더 중요하다.

### 람다의 기본문법
```Bash
(parameters) -> expression  
(parameters) -> {statements;}
```

| 사용 사례 | 람다 예제 |
|:---:|:---|
|불리언 표현식| (List<String> list) -> list.isEmpty()|
|객체 생성| () -> new Apple(10)|
|객체에서 소비| (Apple a) -> { System.out.println(a.getWeight());}|
|객체에서 선택/추출| (String s) -> s.length()|
|두 값에 대한 연산| (int a, int b) -> a * b|
