## 3.4 함수형 인터페이스 사용
### Predicate
java.util.function.Predicate<T> 인터페이스는 `test` 추상 메서드를 정의하며 `test`는 제네릭 형식 T의 객체를 인수로 받아 불리언으로 반환한다.
```Java
@FunctionalInterface
public interface Predicate<T>{
  boolean test(T t);
}
public static <T> List<T> filter(List<T> inventory, Predicate<T> p) {
    List<T> result = new ArrayList<>();
    for (T e : inventory) {
      if (p.test(e)) {
        result.add(e);
      }
    }
    return result;
}
Predicate<String> noEmptyStringPredicate = (String s) -> !s.isEmpty();
List<String> noEmpty = filter(listOfStrings, noEmptyStringPredicate);
```

### Consumer
java.util.function.Consumer<T> 인터페이스는 제네릭 형식 T 객체를 받아서 void를 반환하는 `accept` 추상 메서드를 정의한다. T 형식의 객체를 인수로 받아서 어떤 동작을 수행하고 싶을 때 `Consumer` 인터페이스를 사용한다.
```Java
@FunctionalInterface
public interface Consumer<T>{
  void accept(T t);
}
public static <T> void forEach(List<T> list, Consumer<T> c) {
    for(T t: list){
      c.appect(t);
    }
}
forEach(Arrays.asList(1,2,3,4,5), (Integer i) -> System.out.println(i)); // 모든 정수를 출력
```

### Function
java.util.function.Function<T, R> 인터페이스는 제네릭 형식 T를 인수로 받아서 제네릭 형식 R 객체를 반환하는 추상 메서드 `apply`를 정의한다. 입력을 출력으로 매핑하는 람다를 정의할 때 `Function` 인터페이스를 활용할 수 있다.
```Java
@FunctionalInterface
public interface Function<T, R>{
  R apply(T t);
}
public static <T, R> List<R> map(List<T> list, Function<T, R> f) {
  List<R> result = new ArrayList<>();
  for(T t: list){
    result.add(f.apply(t));
  }
  return result; 
}
List<Integer> l = map(Arrays.asList("lamdas", "in", "action"), (String s) -> s.length()); // [7, 2, 6]
```

