## 4.4 스트림 연산

```Java
List<String> threeHighCaloricDishNames =
    menu.stream()
        .filter(d -> d.getCalories() > 300) // 고칼로리 필터링
        .map(Dish::getName) // 요리명 추출
        .limit(3) // 선착순 3개만
        .collect(toList()); // 리스트로 저장
```

위 예제에서 연산을 두 그룹으로 구분할 수 있다.

- `filter`, `map`, `limit`는 서로 연결되어 파이프라인을 형성한다. (중간 연산)
- `collect`로 파이프라인을 실행한 다음에 닫는다. (최종 연산)

연결할 수 있는 스트림 연산을 **중간 연산**, 스트림을 닫는 연산은 **최종 연산**이라 한다.

### 중간 연산

- 다른 스트림을 반환한다.
- 연산 결과를 스트림으로 반환하기 때문에, 연속적으로 여러 번 수행할 수 있다.
- 단말 연산을 스트림 파이프라인에 실행하기 전까지는 아무 연산도 수행하지 않는다. 중간 연산을 합친 다음에 합쳐진 중간 연산을 최종 연산으로 한 번에 처리하기 때문이다.

중간 연산의 게으른 특성 덕분에 얻은 최적화 효과를 얻을 수 있다. 위의 예시에서는 limit 연산이 **쇼트서킷** 연산에 해당된다. 3개의 결과를 얻은 후 앞선 filter와 map연산은 더 이상 수행할 필요가 없어 빠르게 최종 연산을 수행한다.
또한 `filter`와 `map`은 서로 다른 연산이지만 한 과정으로 병합되었다. 이 기법을 **루프 퓨전**이라고 한다.

### 최종 연산

스트림 파이프라인에서 결과를 도출한다. 보통 최종 연산에 의해 List, Integer, void 등 스트림 이외의 결과 반환된다.

스트림 이용 과정을 3줄로 요약하면 다음과 같다.

- 질의를 수행할 데이터 소스
- 스트림 파이프라인을 구성할 중간 연산 연결
- 스트림 파이프라인을 실행하고 결과를 만들 최종 연산
