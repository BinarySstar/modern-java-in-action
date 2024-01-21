## 8.2 리스트와 집합 처리
메서드명|설명|
---|---|
`removeIf`|프리디케이트를 만족하는 요소를 제거한다.
`replaceAll`| 리스트에서 사용할 수 있는 기능으로 요소를 바꾼다.
`sort`|리스트에서 제공하는 기능으로 리스트를 정렬한다.

### `removeIf` 메서드
```Java
for (Iterator<Transaction> iterator = transactions.iterator(); iterator.hasNext(); ) {
    Transaction transaction = iterator.next();
    if (Character.isDigit(transaction.getReferenceCode().charAt(0))) {
        iterator.remove();
    }
}
```
이 코드는 `removeIf` 메서드를 이용하여 더 간결하게 작성할 수 있다.
```Java
transactions.removeIf(transaction -> Character.isDigit(transaction.getReferenceCode().charAt(0)));
```

### `replaceAll` 메서드
때로는 요소를 제거하는게 아니라 바꿔야 하는 상황이 생길수도 있는데 이때 사용하는 메서드이다.
```Java
referenceCode.stream().map(code -> Character.toUpperCase(code.charAt(0)) + code.subString(1)) // [a12, C14, b13]
    .collect(Collectors.toList())
    .forEach(System.out::println); //[A12, C14, B13]
```
이 코드를 `replaceAll`을 이용하여 더 간결하게 작성할 수 있다.
```Java
referenceCodes.replaceAll(code -> Character.toUpperCase(code.charAt(0)) + code.substring(1));
```
