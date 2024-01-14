## 3.1 람다란 무엇인가?

**람다** : 메서드로 전달할 수 있는 익명 함수를 단순화한 형태

코드를 더욱 간결한 방식으로 전달할 수 있다. 물론 람다가 항상 옳은 방식은 아니며 코드가 2줄 이상 복잡해진다면 메서드로 따로 구현한다. 코드의 간결성을 해치지 않는 것이 더 중요하다.

### 람다의 기본문법

```Bash
(parameters) -> expression
(parameters) -> {statements;}
```

|     사용 사례      | 람다 예제                                          |
| :----------------: | :------------------------------------------------- |
|   불리언 표현식    | (List<String> list) -> list.isEmpty()              |
|     객체 생성      | () -> new Apple(10)                                |
|   객체에서 소비    | (Apple a) -> { System.out.println(a.getWeight());} |
| 객체에서 선택/추출 | (String s) -> s.length()                           |
| 두 값에 대한 연산  | (int a, int b) -> a \* b                           |

## 3.2 어디에, 어떻게 람다를 사용할까?

### 함수형 인터페이스

2절에서 만든 `Predicate<T>` 인터페이스로 `filter` 메서드를 파라미터화하였다. `Predicate<T>`가 함수형 인터페이스이다. **오직 하나의 추상 메서드만 지정하기 때문이다.**

인터페이스는 디폴트 메서드를 포함할 수 있다. 많은 디폴트 메서드가 있다 하더라도 추상 메서드가 오직 하나면 함수형 인터페이스이다.

람다 표편식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로 전체 표현식을 함수형 인터페이스의 인스턴스로 취급할 수 있다.

자바 API에서 제공하는 `Runnable` 인터페이스는 함수형 인터페이스 중 하나이다.

```Java
public interface Runnable {
  void run();
}
```

```Java
Runnable r1 = () -> System.out.println("Hello World!");

public static void process(Runnable r) {
  r.run();
}

process(r1); // Hello World! 출력
process(()-> System.out.println("Hello World 2!")); // Hello World 2! 출력
```

### 함수 디스크립터

**함수 디스크립터** : 람다 표현식의 시그니처를 서술하는 메서드

`Runnable` 인터페이스의 추상 메서드 `run`은 인수와 반환값이 없으므로(void 반환) `Runnable` 인터페이스는 인수와 반환값이 없는 시그니처로 생각할 수 있다. 시그니처가 일치해야 유효한 람다식이다.

> **@FunctionalInterface**  
> 새로운 자바 API를 살펴보면 함수형 인터페이스에 @FunctionalInterface 어노테이션이 추가되어있다. @FunctionalInterface로 함수형 인터페이스를 선언하였지만 추상 메서드가 한 개가 아니라면 컴파일러가 에러를 발생시킨다.

## 3.3 람다 활용 : 실행 어라운드 패턴

자원 처리(예를 들면 데이터베이스의 파일 처리)에 사용하는 순환 패턴(recurrent pattern)은 자원을 열고, 처리한 다음에, 자원을 닫는 순서로 이루어 진다. 설정(setup)과 정리(cleanup) 과정은 대부분 비슷하다. 즉, 실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 갖는다.

```Java
public String processFile() throws IOException {
  try ( BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
    return br.readLine();
  }
}
```

### 1단계 : 동작 파라미터화

현재 코드는 파일에서 한 번에 한 줄만 읽을 수 있다. 만약 한 번에 두 줄을 읽거나 가장 자주 사용되는 단어를 반환하려면 어떻게 해야 할까?

바로 `processFile`의 동작을 파라미터화 시키는 것이다.

```Java
String result = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

### 2단계 : 함수형 인터페이스를 이용해서 동작 전달

함수형 인터페이스 자리에 람다를 사용할 수 있다. 따라서 `BufferedReader -> String` 과 `IOException`을 던질(throw) 수 있는 시그니처와 일치하는 함수형 인터페이스를 만들어야 한다.
이 인터페이스를 `BufferedReaderProcessor`라고 정의하자.

```Java
@FunctionalInterface
public interface BufferedReaderProcessor {
  String process(BufferedReader b) throws IOException;
}
```

```Java
public String processFile(BufferedReaderProcessor p) throws IOException {
  // ...
}
```

### 3단계 : 동작 실행

이제 `BufferedReaderProcessor`에 정의된 `process` 메서드의 시그니처(BufferedReader -> String)와 일치하는 람다를 전달할 수 있다.

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으며, 전달된 코드는 함수형 인터페이스의 인스턴스로 전달된 코드와 같은 방식으로 처리한다.

```Java
public String processFile(BufferedReaderProcessor p) throws IOException {
  try(BufferedREader br = new BufferedReader(new FileReader("data.txt"))) {
    return p.process(br);
  }
}
```

### 4단계 : 람다 전달

이제 람다를 이용해서 다양한 동작을 `processFile` 메서드로 전달할 수 있다.

```Java
String oneLine = processFile((BufferedReaer br) -> br.readLine());
String twoLines = processFile((BufferedReaer br) -> br.readLine() + br.readLine());
```

## 3.5 형식 검사, 형식 추론, 제약

람다 표현식 자체에는 람다가 어떤 함수형 인터페이스를 구현하는지의 정보가 포함되어 있지 않다. 따라서 람다 표현식을 더 제대로 이해하려면 람다의 실제 형식을 파악해야 한다.

### 형식 검사

람다가 사용되는 context를 이용해서 람다의 형식을 추론할 수 있다. 어떤 context에서 기대되는 람다 표현식의 형식을 **대상 형식**이라고 한다. 다음 코드를 통해 람다의 확인 과정을 알아보자.

```Java
List<Apple> heavierThan150g = filter(inventory, (Apple apple) -> apple.getWeight() > 150);
```

1. `filter` 메서드의 선언을 확인한다.
2. `filter` 메서드는 두 번째 파라미터로 `Predicate<Apple>` 형식(대상 형식)을 기대한다.
3. `Predicate<Apple>`은 `test`라는 한 개의 추상 메서드를 정의하는 함수형 인터페이스이다.
4. `test` 메서드는 `Apple`을 받아 boolean을 반환하는 함수 디스크립터이다.
5. `filter` 메서드로 전달된 인수는 이와 같은 요구사항을 만족해야 한다.

![image](https://github.com/BinarySstar/modern-java-in-action/assets/117090689/6db997ee-b478-4eac-8c61-5e68badbae57)

위 람다 표현식은 `Apple`을 인수로 받아 boolean을 반환하므로 유효한 코드이다.

### 형식 추론

코드를 조금 더 단순화할 수 있는 방법으로 형식 추론이 있다. 자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 이용해서 람다 표현식과 관련된 함수형 인터페이스를 추론한다. 즉, 대상 형식을 이용하여 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처도 추론할 수 있다. 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 이를 생략할 수 있다.

```Java
List<Apple> greenApples = filter(inventory, apple -> GREEN.equals(apple.getColor())); // 파라미터 apple의 형식을 지정하지 않아도 유효한 코드이다.
```

### 지역 변수 사용 및 제약

람다 표현식에서는 익명 함수가 하는 것 처럼 자유 변수(파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수)를 활용할 수 있다. 이를 람다 캡처링(lamda capturing)이라 부른다. 하지만 그러려면 지역 변수는 명시적으로 final로 선언되어 있어야 하거나 실질적으로 final로 선언된 변수와 똑같이 사용되어야 한다(이후 재 할당 불가).

```Java
int portNumber = 1337;
Runnable r = () -> System.out.println(portNumber);
portNumber = 60000; // Error!
```

인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 위치한다. 람다에서 지역 변수에 바로 접근할 수 있다는 가정하에 람다가 스레드에서 실행된다면 지역 변수를 할당한 스레드가 사라져서 변수 할당이 해제되었는데도 람다를 실행하는 스레드에서는 해당 변수에 접근하려 할 수 있다. 따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공한다. 따라서 복사본의 값이 바뀌지 않아야 하므로 지역 변수에는 한 번만 값을 할당해야 한다는 제약이 생긴것이다.

## 3.6 메서드 참조

메서드 참조를 이용하면 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있다. 때로는 람다 표현식보다 메서드 참조를 사용하는 것이 가독성에서 더 좋고 자연스러울 수 있다.

메서드 참조는 특정 메서드만을 호출하는 람다의 축약형이라고 생각할 수 있다. 메서드 명을 참조하기 때문에 가독성을 높일 수 있다. 메서드를 참조하는 방법은 메서드명 앞에 구분자(::)를 붙이면 된다.

```Java
// (Apple apple) -> apple.getWeight()
Apple::getWeight

// () -> Thread.currentThread().dumpStack()
Thread.currentThread()::dumpStack()

// (str, i) -> str.substring(i)
String::substring

// (String s) -> System.out.println(s)
System.out::println

// (String s) -> this.isValidName(s)
this::isValidName
```

메서드 참조는 3가지로 구분할 수 있다.

1. **정적 메서드 참조** : Integer의 `parseInt` 메서드는 `Integer::parseInt`로 표현할 수 있다.
2. **다양한 형식의 인스턴스 메서드 참조** : String의 `lenght` 메서드는 `String::length`로 표현할 수 있다.
3. **기존 객체의 인스턴스 메서드 참조** : `Transaction` 객체를 할당받은 `expensiveTransaction` 지역 변수가 있고, `Transaction` 에는 `getValue` 메서드가 있다면, 이를 `expensiveTransaction::getValue`라고 표현할 수 있다.

### 생성자 참조

`ClassName::new` 처럼 클래스명과 new 키워드를 이용해서 기존 생성자의 참조를 만들 수 있다.

```Java
// Supplier<Apple> c1 = () -> new Apple();
Supplier<Apple> c1 = Apple::new;
Apple a1 = c1.get();
```

## 3.7 람다, 메서드 참조 활용하기

사과 리스트 정렬 코드를 리팩토링하면서 람다에 대한 이해를 한다.

### 1단계 : 코드 전달

자바 8의 List API에서 `sort` 메서드를 제공하므로 정렬 메서드에 대한 직접적인 구현은 필요없고 정렬 기준을 전달해야하는 방법만 고안하면 된다. 먼저 `sort` 메서드를 살펴보자.

```Java
void sort(Comparator<? super E> c)
```

Comparator 객체를 인수로 받아 두 사과를 비교한다. 객체 안에 동작을 포함시키는 방식으로 다양한 정렬 기준을 전달할 수 있다.(동작 파라미터화)

초기 코드는 다음과 같이 완성할 수 있다.

```Java
public class AppleComparator implements Comparator<Apple> {
    @Override
    public int compare(Apple a1, Apple a2) {
      return a1.getWeight() - a2.getWeight();
    }
}
inventory.sort(new AppleComparator());
```

### 2단계 : 익명 클래스 사용

한 번만 사용할 `Comparator`를 익명 클래스를 사용하여 더 간단하게 구현할 수 있다.

```Java
inventory.sort(new Comparator<Apple>(){
  public int compare(Apple a1, Apple a2) {
      return a1.getWeight() - a2.getWeight();
    }
});
```

### 3단계 : 람다 표현식 사용

```Java
inventory.sort((Apple a1, Apple a2) -> a1.getWeight() - a2.getWeight());
inventory.sort((a1, a2) -> a1.getWeight() - a2.getWeight()); // 형식 추론으로 다음과 같이 줄일 수 있음
```

`Comparator`는 `Comparable` 키를 추출해서 `Comparator` 객체로 만드는 `Function` 함수를 인수로 받는 정적 메서드 `comparing`이 있다. 다음처럼 `comparing` 메서드를 사용할 수 있다.

```Java
Comparator<Apple> c = Comparator.comparing((Apple a) -> a.getWeight());
```

코드를 다음처럼 간소화할 수 있다.

```Java
inventory.sort(comparing(apple -> apple.getWeight());
```

### 4단계 : 메서드 참조 사용

```Java
inventory.sort(comparing(Apple::getWeight));
```

메서드 참조를 사용하여 코드가 짧아진 것 뿐만 아니라 코드 자체로 'Apple을 weight별로 비교해서 inventory에 sort하라`는 명확한 의미를 전달할 수 있다.

## 3.8 람다 표현식을 조합할 수 있는 유용한 메서드

`Comparator`, `Function`, `Predicate` 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있도록 유틸리티 메서드를 제공한다. 이 메서드가 바로 **디폴트 메서드**이다.

### Comparator 조합

`Comparator` 인터페이스의 디폴트 메서드로 역정렬을 지원하는 `reversed`가 있다.

```Java
inventory.sort(comparing(Apple::getWeight).reversed());
```

사과의 무게를 내림차순으로 정렬한다.

만약 무게가 같은 사과가 존재한다면 어떻게 해야 할까? 무게가 같은경우 원산지 국가별로 정렬할 수 있다. `thenComparing` 함수를 이용한다.

```Java
inventory.sort(comapring(Apple::getWeight)
  .reversed()
  .thenComparing(Apple::getCountry));
```

### Predicate 조합

Predicate 인터페이슨느 복잡한 프레디케이트를 만들 수 있도록 `negate`, `and`, `or` 세 가지 메서드를 제공한다.

- negate

```Java
Predicate<Apple> notRedApple = redApple.negate(); // 빨간색이 아닌 사과
```

- and

```Java
Predicate<Apple> redAndHeavyApple = redApple.and(apple -> apple.getWeight() > 150); // 빨간색 그리고 무게가 150 이상인 사과
```

- or

```Java
Predicate<Apple> readAndHeavyAppleOrGreen = redApple.and(apple -> apple.getWeight() > 150)
  .or(apple -> GREEN.equals(a.getColor())); // 빨간색이면서 무게가 150 이상인 사과 또는 그냥 초록사과
```

### Function 조합

Function 인터페이스는 `andThen`과 `compose` 두 가지 디폴트 메서드를 제공한다.

예를 들어 숫자를 증가 시키는 x -> x + 1 이라는 f 함수가 있고, 숫자에 2를 곱하는 g 함수가 있다고 가정하자.

- andThen

```Java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.andThen(g); // g(f(x))
int result = h.apply(1); // 4를 반환
```

- compose

```Java
Function<Integer, Integer> h = f.compose(g); // f(g(x))
int result = h.apply(1); // 3을 반환
```
