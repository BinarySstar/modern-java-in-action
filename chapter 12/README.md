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

## 12.2 날짜 조정, 파싱, 포매팅

`withAttribute` 메소드로 `LocalDate` 를 변경할 수 있다.

이 때 기존의 객체를 바꾸는 것이 아니라 필드를 갱신한 복사본을 만든다.

```Java
// 절대적인 방식 LocalDate 속성 바꾸기
LocalDate date1 = LocalDate.of(2017, 9, 21); // 2017-09-21
LocalDate date2 = date1.withYear(2011); // 2011-09-21
LocalDate date3 = date2.withDayOfMonth(25); // 2011-09-25
LocalDate date4 = date3.with(ChronoField.MONTH_OF_YEAR, 2); // 2011-02-25

// 상대적인 방식 LocalDate 속성 바꾸기
LocalDate date1 = LocalDate.of(2017, 9, 21); // 2017-09-21
LocalDate date2 = date1.plusWeeks(1); // 2017-09-28
LocalDate date3 = date2.minusYears(1); // 2011-09-28
LocalDate date4 = date3.plus(6, ChronoUnit.MONTHS); // 2012-03-28
```

`plus`, `minus` 메소드를 이용하여 상대적인 방식으로 속성을 바꿀 수 있다.

### `TemporalAdjusters` 사용하기

좀 더 복잡한 방식으로 다양한 동작을 수행하려면 `TemporalAdjuster` 를 전달하는 방법으로 문제를 해결할 수 있다.

```Java
import static java.time.temporal.TemporalAdjusters.*;

LocalDate date1 = LocalDate.of(2014, 3, 18); // 2014-03-18 (화)
LocalDate date2 = date1.with(nextOrSame(DayOfWeek.SUNDAY)); // 2014-03-23
LocalDate date3 = date2.with(lastDayOfMonth()); // 2014-03-31
```

필요한 기능이 존재하지 않으면 커스텀 `TemporalAdjuster` 를 구현하여 사용할 수 있다.

```Java
@FunctionalInterface
public interface TemporalAdjuster {
    Temporal adjustInto(Temporal temporal);
}
```

### 날짜와 시간 객체 출력과 파싱

`DateTimeFormatter`를 이용하여 날짜나 시간을 특정 형태의 문자열로 변환할 수 있다.

```Java
LocalDate date = LocalDate.of(2014, 3, 18);
String s1 = date.format(DateTimeFormatter.BASIC_ISO_DATE); // 20140318
String s2 = date.format(DateTimeFormatter.ISO_LOCAL_DATE); // 2014-03-18
```

사용자 지정 패턴으로도 변환할 수 있다.

```Java
DateTimeFormatter formatter = DateTimeFormatter.ofPatterm("dd/MM/yyyy");
LocalDate date = LocalDate.of(2014, 3, 18);
String formattedDate = date1.format(formatter); // 요청 형식의 패턴에 해당하는 문자열 생성
LocalDate date2 = LocalDate.parse(formattedDate, formatter); // 같은 포매터를 적용해서 생성된 문자열을 파싱
```
