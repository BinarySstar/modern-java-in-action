## 11.1 값이 없는 상황을 어떻게 처리할까?
### 보수적인 자세로 NPE 줄이기
예기치 않은 `NullPointerException`을 피하려면 어떻게 해야할까? `if`문을 사용하여 모든 상황에 대한 `null` 대체를 할 수 있다.
```Java
public String getCarInsuranceName(Person person) {
  if (person != null) {
      Car car = person.getCar();
      if(car != null) {
          Insurance insurance = car.getInsurance();
          if(insurance != null) {
              return insurance.getName();
          }
      }
  }
  return "Unknown";
}
```
모든 변수가 `null`인지 의심하므로 변수를 접근할 때 마다 `if`문이 중첩되어 들여쓰기 수준이 증가한다. 반복되면 코드의 구조가 엉망이 되고 가독성도 떨어진다. 다른 방법도 알아보자.
```Java
public String getCarInsuranceName(Person person) {
  if (person == null)
      return "Unknown";
  Car car = person.getCar();
  if (car == null)
      return "Unknown";
  Insurance insurance = car.getInsurance();
  if (insurance == null)
      return "Unknown";
  return insurance.getName();
}
```
전보다 중첩 `if`문을 줄여 가독성은 높아졌다. 그러나 메서드에 4개의 출구가 생겨 유지보수가 어려워진다.
### null로 발생하는 문제
- **에러의 근원이다.** : 가장 흔히 발생하는 에러는 NPE이다.
- **코드를 어지럽힌다.** : `null` 확인 코드를 추가해야 하므로 가독성이 떨어진다.
- **아무 의미가 없다.** : `null`은 아무 의미도 표현하지 않는다.
- **자바 철학에 위배된다.** : 자바는 개발자로부터 모든 포인터를 숨겼다. 하지만 예외가 있는데 그것이 바로 `null` 포인터이다.
- **형식 시스템에 구멍을 만든다.** : `null`은 형식이 없고 정보를 포함하고 있지 않으므로 모든 참조 형식에 `null`을 할당할 수 있다. 즉, `null`이 어떤 의미로 사용되었는지 알 수 없다.
