## 12.1 `LocalDate`, `LocalTime`, `Instant`, `Duration`, `Period` 클래스
### `LocalDate`와 `LocalTime` 사용
`LocalDate`는 시간을 제외한 날짜를 표현하는 불변의 객체이다. 어떤 시간대 정보도 포함하지 않는다.
```Java
LocalDate date = LocalDate.of(2024, 01, 31); 
int year = date.getYear(); //2024
Month month = date.getMonth(); //01
int day = date.getDayOfMonth(); //31
DayOfWeek dow = date.getDayOfWeek(); //WEDNESDAY
LocalDate now = LocalDate.now(); // 현재 날짜 정보
```
`LocalTime`은 시간 정보를 나타낸다.
```Java
LocalTime time = LocalTime.of(13, 45, 20); // 13:45:20
int hour = time.getHour(); // 13
int minute = time.getMinute(); // 45
int second = time.getSecond(); // 20
```
날짜와 시간 문자열로 `LocalDate` 혹은 `LocalTime` 인스턴스를 만들 수 있다.
```Java
LocalDate date = LocalDate.parse("2020-12-22");
LocalTime time = LocalTime.parse("13:45:20");
```
### 날짜와 시간의 조합 `LocalDateTime`
`LocalDate`와 `LocalTime`을 쌓으로 갖는 복합 클래스이다.
```Java
LocalDateTime dateTime = LocalDateTime.of(2023, Month.MAY, 26, 16, 37, 20);
LocalDate date = dateTime.toLocalDate(); // 2023-05-26
LocalTime time = dateTime.toLocalTime(); // 16:37:20
```
### `Instant` 클래스 : 기계의 날짜와 시간
사람은 보통 주, 날짜, 시간, 분으로 날짜와 시간을 계산한다. 하지만 기계에서는 이와 같은 단위로 시간을 표현하지 않고 연속된 시간에서 특정 지점을 하나의 큰 수로 표현한다.

즉, `Instant` 클래스는 유닉스 에포크 시간(1970년 1월 1일 0시 0분 0초 UTC)을 기준으로 특정 지점까지의 시간을 초 단위로 표현한다.
### `Duration`과 `Period` 정의
`Duration`은 초와 나노초로 시간 단위를 표현하여 두 시간 객체 간의 지속시간을 나타내고 `Period`는 년, 월, 일로 표현할 때 사용한다.
```Java
Duration threeMinutes = Duration.ofMinutes(3);
Period tenDats = Periods.ofDays(10);
```
