## 메서드 참조

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
