## 11.2 `Optional` 클래스 소개
`Optional`은 선택형값을 캡슐화하는 클래스이다. 객체에 값이 있으면 `Optional` 클래스는 값을 감싼다. 값이 없다면 `Optional`을 반환한다. 기존에는 `null`을 참조하면 NPE가 발생하였지만 값이 없는 `Optional` 객체도 결국은 객체이므로 예외가 발생하지 않는다.

`Optional` 사용한 코드는 다음과 같다.
```Java
public class Person {
	private Optional<Car> car; // 사람이 차를 소유했을 수도 소유하지 않았을 수도 있으므로 Optional 정의
	public Optional<Car> getCar() {
		return car;
	}
}

public class Car {
	private Optional<Insurance> insurance; // 자동차가 보험에 가입되어 있을 수도, 가입되어 있지 않았을 수도 있으므로 Optional 정의 
	public Optional<Insurance> getInsurance() {
		return insurance;
	}
}

public class Insurance {
	private String name; // 보험회사는 밥ㄴ드시 이름이 있다
	public String getName() {
		return name;
	}
}
```
