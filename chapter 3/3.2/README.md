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
