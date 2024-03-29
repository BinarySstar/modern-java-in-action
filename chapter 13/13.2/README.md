## 13.2 디폴트 메서드란 무엇인가?
자바 8에서는 호환성을 유지하면서 API를 바꿀 수 있도록 새로운 기능인 디폴트 메서드를 제공한다.
인터페이스를 구현하는 클래스에서 메서드를 구현하지 않아도 되는 새로운 메서드 시그너처이다.
인터페이스를 구현하는 클래스에서 구현하지 않은 메서드는 인터페이스 자체에서 기본으로 제공한다.
```Java
public interface Sized {
    int size();
    default boolean isEmpty() {
        return size() == 0;
    }
}
```
위 코드에서 `Sized` 인터페이스는 추상 메서드 `size`와, 디폴트 메서드 `isEmpty`를 포함한다.
`Sized` 인터페이스를 구현하는 모든 클래스는 `isEmpty`의 구현까지 상속받는다.
