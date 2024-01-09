## 1.3 자바 함수

프로그래밍 언어에서 **함수(Function)**라는 용어는 정적 메서드를 의미한다. 메서드는 클래스 내부에 종속되어 있는 동작의 체계를 의미한다. 자바희 함수는 이에 더해 **수학적인 함수**처럼 사용되며 부작용을 일으키지 않는 함수를 의미한다.

함수가 필요한 이유를 알아보기 전에 일급 값 또는 일급 시민이라고 불리는 용어의 의미를 알아보자.
전통적으로 미국 시민 권리에서 유래한 해당 용어는 바꿀 수 있는 값을 일급 시민이라고 불러왔다. 예를 들어 int나 double 형식의 기본값 및 String 형식의 객체가 일급 시민에 해당한다. 하지만 클래스나 메서드와 같은 경우 값의 구조를 표현하는 데 도움이 될 수는 있지만, 그 자체로 값이 될 수는 없기 때문에 이급 시민에 해당한다.

이렇게 이급 시민인 메서드를 런타임에 전달할 수 있는 일급 시민으로 만든다면 프로그래밍에 유용하게 활용될 수 있다.

### 메서드와 람다를 일급 시민으로

스칼라와 그루비 같은 언어에서 메서드를 일급값으로 사용하며 프로그래머가 활용할 수 있는 도구가 다양해지면서 프로그래밍이 수월해진다는 사실을 확인하였다. 이에 자바 8 또한 메서드를 값으로 취급할 수 있게 하기 위하여 새로운 기능을 제공한다.

#### 메서드 참조

자바 8 이전에는 new 연산자를 이용하여 객체를 생성하고 객체 참조를 이용하여 객체를 이리저리 주고받았다. 하지만 자바 8에서는 메서드 참조를 이용할 수 있다.

다음은 디렉토리에서 모든 파일을 찾는 코드이다.

```Java
//자바 8 이전
File[] hiddenFiles = new File(".").listFiles(new FileFilter() {
    public boolean accept(File file) {
        return file.isHidden();
    }
})
```

```Java
//자바 8 : 메서드 참조 적용
File[] hiddenFiles = new File(".").listFiles(File::isHidden);
```

메서드 참조를 이용하면 한줄로 코드 작성이 가능하고 가독성 면에서도 훨씬 더 좋다.  
File 클래스에서는 isHidden() 메서드가 존재하는데 굳이 FileFilter()로 isHidden을 감쌀 필요가 없이 자바 8의 `::`를 이용해서 listFiles에 직접 전달할 수 있다.

#### 람다 : 익명 함수

자바 8에서는 기명 메서드 뿐만 아니라 **람다**(또는 익명 함수)도 일급값으로 취급한다.  
예를 들어 `(int x) -> x + 1`, 즉 'x라는 인수를 호출하면 x + 1을 반환'하는 동작을 수행하도록 코드를 구현할 수 있다.  
직접 메서드를 정의할 수 있지만, 이용할 수 있는 편리한 클래스나 메서드가 없을 때 람다 문법을 이용하면 더 간결하게 코드를 작성할 수 있다.

### 코드 넘겨주기 : 예제

Apple 클래스와 getColor 메서드가 있고, Apples 리스트를 포함하는 변수 inventory가 있다고 가정할 때, 원하는 조건에 맞게 리스트를 반환하는 프로그램을 구현해 보자. 이때 누군가 150그램 이상인 사과만 필터링하고 싶다면 다음과 같이 코드를 작성할 수 있을 것이다.

```Java
public static List<Apple> filterHeavyApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (apple.getWeight() > 150) {
            result.add(apple);
        }
    }
    return result;
}
```

만약 무게를 기준으로 하는 것이 아닌 색깔을 기준으로 한다면, if문 내부의 코드만 달라지고 완전히 똑같은 구조의 코드가 된다. 이는 복사&붙여넣기 식의 코드 작성이며 버그가 발생할 경우 모든 코드를 고쳐야 하는 문제점이 발생한다.

하지만 자바 8에서는 코드를 인수로 넘겨줄 수 있으므로 필터 메서드를 중복으로 구현할 필요가 없어 아래와 같이 구현할 수가 있다.

```Java
public static boolean isHeavyApple(Apple apple) {
    return apple.getWeight() > 150;
}

static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) { //조건 판별 함수가 Predicate 파라미터로 전달
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (p.test(apple)) { //조건 부합 여부 확인
            result.add(apple);
        }
    }
    return result;
}
```

위에서 쓰인 Predicate는 인수로 값을 받아 참 또는 거짓을 반환하는 함수를 의미하는 용어이다. 이제 여러 가지 조건을 검증하기 위해서 똑같은 코드를 복사&붙여넣기 하는 식으로 작성할 필요 없이 검증 메서드만 추가적으로 작성하면 된다. `filterApples()`메서드는 다음과 같이 호출하여 조건을 판별할 수 있다.

```Java
filterApples(inventory, Apple::isHeavyApple);
```

### 메서드 전달에서 람다로

메서드를 값으로 전달하는 것은 유용한 기능이지만 `isHeavyApple`처럼 한두 번만 사용할 메서드를 매번 정의하는 것은 비효율적이다. 자바 8에서는 람다(익명 함수)를 이용하여 이 문제를 해결할 수 있다.

```Java
filterApples(inventory, (Apple a) -> a.getWeight() > 150);
```

하지만 람다가 몇 줄 이상으로 길어진다면 이름을 가진 기명 메서드로 정의하고 메서드 참조를 활용하는 것이 더 바람직하다. 코드의 명확성이 우선시되어야 한다.
