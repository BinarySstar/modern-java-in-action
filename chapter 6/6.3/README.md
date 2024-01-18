### 6.3 그룹화
`Collectors.groupingBy` 정적 팩토리 메서드를 이용하면 쉽게 그룹화할 수 있다.
```Java
/** 
  * 그룹화 groupingBy
  * 생선, 고기 그 밖의 것들로 그룹화 
  */
Map<Type, List<Dish>> dishesByType = menu.stream().collect(groupingBy(Dish::getType));
```
```Java
// 칼로리로 구분
Map<CaloricLevel, List<Dish>> dishesByCaloricLevel = menu.stream().collect(
  groupingBy(dish -> {
    if(dish.getCalories() <= 400) return CaloricLevel.DIET;
    else if(dish.getCalories() <= 700) return CaloricLevel.NORMAL;
    else return CaloricLevel.FAT;
  }));
```
```Java
// 500칼로리가 넘는 요리만 타입과 종류로 그룹화
Map<Type, List<Dish>> caloricDishesByType = menu.stream().filter(dish -> dish.getCalroies() > 500)
  .collect(groupingBy(Dish::getType));
/**
  * 결과
  * {OTHER=[french fries, pizza], MEAT=[pork, beef]}
  * 위 코드의 단점은 위 filter 프레디케이트를 만족하는 값이 없을 경우 키값 자체가 제외되서 맵에 담지 못한다.
  * 해결책 : Collectors 클래스의 정적 팩터리 메서드인 filtering 사용
  */
  
// 해결
Map<Type, List<Dish>> caloricDishesByType = menu.stream().collect(groupingBy
  (Dish::getType, filtering(dish -> dish.getCalories() > 500, toList())));
// 결과 : {OTHER=[french fries, pizza], MEAT=[pork, beef], FISH=[]}
```

### 다수준 그룹화
`Collectors.groupingBy` 정적 팩토리 메서드를 이용하면 항목을 다수준으로 그룹화할 수 있다.
```Java
Map<Dish.Type, Map<CaloricLevel, List<Dish>>> groupDishedByTypeAndCaloricLevel = 
menu.stream().collect(
    groupingBy(Dish::getType,
        groupingBy((Dish dish) -> {
          if (dish.getCalories() <= 400) {
            return CaloricLevel.DIET;
          }
          else if (dish.getCalories() <= 700) {
            return CaloricLevel.NORMAL;
          }
          else {
            return CaloricLevel.FAT;
          }
        })
    )
);
```

### 서브그룹으로 데이터 수집
`Collectors.collectingAndThen` 팩토리 메서드는 적용할 컬렉터와 변환 함수를 인수로 받아 다른 컬렉터를 반환한다.
```Java
Map<Type, Dish> mostCaloricByType = 
  menu.stream()
    .collect(groupingBy(Dish::getType, collectingAndThen(maxBy(comparingInt(Dish::getCaloreis)), Optioanl::get));
```
`Optional::get`으로 반환된 `Optional`에 포함된 값을 추출한다.
