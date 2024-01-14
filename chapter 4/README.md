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

## 4.2 스트림 시작하기

스트림이란 '데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소'로 정의할 수 있다.

컬렉션의 주제는 데이터고, 스트림의 주제는 계산이다. 스트림은 `filter`, `map`, `reduce`, `find`, `match`, `sort` 등으로 데이터를 조작할 수 있다. 그리고 순차적으로 또는 병렬로 실행할 수 있다.

스트림은 컬렉션, 배열, I/O 자원 등의 데이터 제공 소스로부터 데이터를 소비한다. 정렬된 컬렉션으로 스트림을 생성하면 정렬이 그대로 유지된다. 즉, 리스트로 스트림을 만들면 스트림의 요소는 리스트의 요소와 같은 순서를 유지한다.

### 스트림의 특징

- **파이프라이닝** : 대부분의 스트림 연산은 스트림 연산끼리 연결해서 커다란 파이프 라인을 구성할 수 있도록 스트림 자신을 반환한다. 그 덕분에 게으름(laziness), 쇼트서킷(short-cricuiting) 같은 최적화도 얻을 수 있다. 연산 파이프라인은 데이터 솟에 적용하는 데이터베이스 질의와 비슷하다.
- **내부 반복** : 컬렉션은 반복자를 이용해서 명시적으로 반복하지만, 스트림은 내부 반복을 지원한다.

```Java
List<String> threeHighCaloricDishNames =
    menu.stream()
        .filter(d -> d.getCalories() > 300) // 고칼로리 필터링
        .map(Dish::getName) // 요리명 추출
        .limit(3) // 선착순 3개만
        .collect(toList()); // 리스트로 저장
```

마지막 `collect(toList())`를 호출하기 전까지는 `menu`에서 무엇도 선택되지 않으며 출력 결과도 없다. 즉 collect가 호출되기 전까지 메서드 호출이 저장되는 효과가 있다.

## 4.3 스트림과 컬렉션

스트림과 컬렉션 모두 연속된 요소 형식의 값을 저장하는 자료구조의 인터페이스를 제공한다.

이 둘의 차이점은 계산이 되는 시점에서 차이가 있다.

- 컬렉션 : 현재 자료구조가 포함하는 모든 값을 메모리에 저장하는 자료구조이다. 즉, 컬렉션의 모든 요소는 컬렉션에 추가되기 전에 계산되어야 한다.
- 스트림 : 요청할 때만 요소를 계산하는 고정된 자료구조이다.

### 딱 한 번만 탐색할 수 있다.

스트림은 한 번만 탐색할 수 있다. 한 번 탐색한 요소를 다시 탐색하려면 초기 데이터 소스에서 새로운 스트림을 만들어야 한다.

```Java
List<String> title = Arrays.asList("JAVA8","In","Action");
Stream<String> s = title.stream();
s.forEach(System.out::println);
s.forEach(System.out::println);  //안됨
```

### 외부 반복과 내부 반복

- **외부 반복** : `for-each` 사용 등 컬렉션 인터페이스를 사용하려면 사용자가 직접 요소를 반복한다. 명시적으로 컬렉션 항목을 하나씩 가져와서 처리한다.

```Java
List<String> names = new ArrayList<>();
for(Dish dish : menu){
  names.add(dish.getName());
}
```

- **내부 반복** : 스트림 라이브러리는 반복을 알아서 처리하고 결과 스트림값을 저장해준다.

```Java
List<String> names = menu.stream().map(Dish::getName).collect(toList());
```

외부 반복에 비해 내부 반복이 더 좋은 이유는 작업을 투명하게 병렬로 처리하거나 더 최적화된 다양한 순서로 처리할 수 있다.

`for-each`를 이용하는 외부 반복에서는 병렬성을 **스스로 관리**해야 한다. 이 말은 프로그래머가 병렬성을 포기하든지 아니면 `synchronized`를 사용하는 매우 힘든 길을 걷는다는 뜻이다. 반면 스트림 라이브러리의 내부 반복은 데이터 표현과 하드웨어를 활요한 병렬성 구현을 자동으로 선택한다.

## 4.4 스트림 연산

```Java
List<String> threeHighCaloricDishNames =
    menu.stream()
        .filter(d -> d.getCalories() > 300) // 고칼로리 필터링
        .map(Dish::getName) // 요리명 추출
        .limit(3) // 선착순 3개만
        .collect(toList()); // 리스트로 저장
```

위 예제에서 연산을 두 그룹으로 구분할 수 있다.

- `filter`, `map`, `limit`는 서로 연결되어 파이프라인을 형성한다. (중간 연산)
- `collect`로 파이프라인을 실행한 다음에 닫는다. (최종 연산)

연결할 수 있는 스트림 연산을 **중간 연산**, 스트림을 닫는 연산은 **최종 연산**이라 한다.

### 중간 연산

- 다른 스트림을 반환한다.
- 연산 결과를 스트림으로 반환하기 때문에, 연속적으로 여러 번 수행할 수 있다.
- 단말 연산을 스트림 파이프라인에 실행하기 전까지는 아무 연산도 수행하지 않는다. 중간 연산을 합친 다음에 합쳐진 중간 연산을 최종 연산으로 한 번에 처리하기 때문이다.

중간 연산의 게으른 특성 덕분에 얻은 최적화 효과를 얻을 수 있다. 위의 예시에서는 limit 연산이 **쇼트서킷** 연산에 해당된다. 3개의 결과를 얻은 후 앞선 filter와 map연산은 더 이상 수행할 필요가 없어 빠르게 최종 연산을 수행한다.
또한 `filter`와 `map`은 서로 다른 연산이지만 한 과정으로 병합되었다. 이 기법을 **루프 퓨전**이라고 한다.

### 최종 연산

스트림 파이프라인에서 결과를 도출한다. 보통 최종 연산에 의해 List, Integer, void 등 스트림 이외의 결과 반환된다.

스트림 이용 과정을 3줄로 요약하면 다음과 같다.

- 질의를 수행할 데이터 소스
- 스트림 파이프라인을 구성할 중간 연산 연결
- 스트림 파이프라인을 실행하고 결과를 만들 최종 연산
