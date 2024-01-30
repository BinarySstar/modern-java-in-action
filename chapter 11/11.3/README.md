## 11.3 `Optional` 적용 패턴
`Optional` 활용 방법에 대해 알아보자.

### `Optional` 객체 만들기
#### 빈 `Optional`
정적 팩토리 메서드 `Optional.empty`로 빈 `Optional` 객체를 얻을 수 있다.
```Java
Optional<Car> optCar = Optional.empty();
```
#### `null`이 아닌 값으로 `Optional` 만들기
정적 팩토리 메서드 `Optional.of`로 `null`이 아닌 값을 가지는 `Optional`을 만들 수 있다.
```Java
Optional<Car> optCar = Optional.of(car);
```
이제 `car`가 `null`이라면 즉시 NPE가 발생한다. `Optional`을 사용하지 않았다면 `car`에 접근하려고 할 때 예외가 발생했을 것이다.

#### `null`값으로 `Optional` 만들기
정적 팩토리 메서드 `Optional.ofNullable`로 `null`값을 저장할 수 있는 `Optional`을 만들 수 있다.
```Java
Optional<Car> optCar = Optional.ofNullabe(car);
```
`car`가 `null`이라면 빈 `Optional` 객체가 반환된다.

### 맵으로 `Optional` 값 추출하고 변환하기
```Java
Optional<Insurance> optInsurance = Optional.ofNullable(insurance);
Optional<String> name = optInsurance.map(Insurance::getName);
```
### `flatMap`으로 `Optional` 객체 연결
다음과 같이 작성한 코드는 컴파일되지 않는다.
```Java
Optional<Person> optPerson = Optional.of(person);
Optional<String> name = optPerson.map(Person::getCar)
                                .map(Car::getInsurance)
                                .map(Insurance::getName);
```
`optPerson`의 형식은 `Optional`이므로 `map` 메서드를 호출할 수 있지만, `map`의 연산 결과는 중첩 `Optional` 형태이므로 `getInsurance` 메서드를 지원하지 않는다.

이 문제를 해결하기 위해서 `flatMap`을 이용할 수 있다. 보통 인수로 받은 함수를 스트림의 각 요소에 적용하면 스트림의 스트림이 만들어지는데, `flatMap`은 인수로 받은 함수를 적용해서 생성된 각각의 스트림에서 콘텐츠만 남긴다.

아래 코드는 `Optional`로 자동차의 보험회사 이름을 짓는 코드이다.
```Java
public String getCarInsuranceName(Optional<Person> person) {
    return person.flatMap(Person::getCar)
                 .flatMap(Car::getInsurance)
                 .map(Insurance::getName)
                 .orElse("Unknown");
}
```
### `Optional` 스트림 조작
자바 9에서는 `Optional`을 포함하는 스트림을 쉽게 처리할 수 있도록 `Optional`에 `stream()` 메서드를 추가했다.
```Java
public Set<String> getCarInsuranceNames(List<Person> persons) {
    return persons.stream()
                  .map(Person::getCar) //사람 목록을 각 사람이 보유한 자동차의 Optional<Car> 스트림으로 변환
                  .map(optCar -> optCar.flatMap(Car::getInsurance)) //flatmap 연산을 이용해 Optional<Insurance>로 변환
                  .map(optIns -> optIns.map(Insurance::getName)) //해당 이름의 Optional<String>으로 매핑
                  .flatMap(Optional::stream) //Stream<Optional<String>>을 현재 이름을 포함하는 Stream<String>으로 변환
                  .collect(toSet()); //결과 문자열을 중복되지 않은 집합으로 수
}
```
### 디폴트 액션과 `Optional` 언랩
`Optional` 클래스는 인스턴스에 포함된 값을 읽는 다양한 방법을 제공하고 있다.
- `get()` : 값을 읽는 가장 간단한 메서드이면서 동시에 가장 안전하지 않은 메서드이다. 래핑된 값이 있으면 해당 값을 반환하고, 값이 없으면 NoSuchElementException을 발생시킨다.
- `orElse()` : `Optional`이 값을 포함하지 않을 때 기본 값을 제공할 수 있다.
- `orElseGet()` : `orElse`에 대응하는 게으른 버전의 메서드로, `Optional`에 값이 없을 때만 인수로 받는 `Supplier`가 실행된다.
- `orElseThrow()` : `Optional`이 비어있을 때 예외를 발생시킨다는 점에서 `get`과 비슷하지만, 해당 메서드는 발생시킬 예외의 종류를 선택할 수 있다.
- `ifPresent()` : 값이 존재할 때 인수로 넘겨준 동작을 실행할 수 있고, 없으면 아무 일도 일어나지 않는다.
- `ifPresentOrElse()` : `Optional`이 비었을 때 실행할 수 있는 `Runnable`을 인수로 받는다는 점 이외에는 `ifPresent`와 동일한 기능을 한다.
