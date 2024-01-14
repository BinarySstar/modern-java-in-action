## 람다 활용 : 실행 어라운드 패턴
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
