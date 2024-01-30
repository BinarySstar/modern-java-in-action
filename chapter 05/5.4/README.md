## 검색과 매칭
특정 속성이 데이터 집합에 있는지 여부를 검색하는 데이터 처리도 자주 사용된다.
스트림 API에서 `allMatch`, `anyMatch`, `noneMatch`, `findFirst`, `findAny` 메서드를 제공한다.

### `anyMatch` 메서드
프리디케이트가 적어도 한 요소와 일치하는지 확인한다.
```Java
if( menu.stream().anyMatch(Dish::isVegetarian) ) {
    System.out.println( "채식 요리" );
}
```
### `allMatch` 메서드
스트림의 모든 요소가 주어진 프리디케이트와 일치하는지 검사한다.
```Java
boolean isHealthy = menu.stream()
                        .allMatch(dish -> dish.getCalories() < 1000);
```
`noneMatch`는 `allMatch`와 반대 연산을 수행한다. 즉, `noneMatch`는 주어진 프리디케이트와 일치하는 요소가 없으면 true를 반환한다. 이전 예제는 다음과 같이 구현할 수 있다.
```Java
boolean isHealthy = menu.stream()
                        .noneMatch(dish -> dish.getCalories() >= 1000);
```
`anyMatch`, `allMatch`, `noneMatch` 세 메서드는 스트림의 **쇼트서킷** 기법을 활용한다.
> **쇼트서킷**
>   
> 전체 스트림을 처리하지 않았더라도 결과를 반환할 수 있다. 예를 들어 여러 and 연산으로 연결된 불리언 표현식을 평가한다고 가정하자. 표현식에서 하나라도 거짓이라면
> 나머지 표현식의 결과와 상관없이 전체 결과가 거짓이 된다. 이를 쇼트서킷이라 부른다. `anyMatch`, `allMatch`, `noneMatch`, 'findFirst', 'findAny' 등의 연산은 모든
> 스트림의 요소를 처리하지 않고도 결과를 반환할 수 있다. 원하는 요소를 찾았으면 즉시 결과를 반환한다. 특히 무한한 요소를 가진 스트림을 유한한 크기로 줄일 수 있는 유용한 연산이다.

### `findAny` 메서드
현재 스트림에서 임의의 요소를 반환한다.
```Java
Optional<Dish> dish = menu.stream()
                          .filter(Dish::isVegetarian)
                          .findAny();
```
이 예제는 임의로 채식주의자를 반환한다.

### `findFirst` 메서드
스트림의 첫 번째 요소를 반환한다.
```Java
List<Integer> someNumbers = Arrays.asList(1, 2, 3, 4, 5);
Optional<Integer> firstSquareDivisibleByThree = someNumbers.stream()
                                                           .map(n -> n * n)
                                                           .filter(n -> n % 3 == 0)
                                                           .findFirst();  //9
```

`findFirst`와 `findAny`는 언제 사용할까? 그 차이는 병렬성에 있다. 병렬 실행에서는 첫 번째 요소를 찾기 어렵다. 따라서 요소의 반환 순서가 상관없다면 병렬 스트림에서는 제약이 적은 `findAny`를 사용한다.
