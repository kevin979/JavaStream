# terminal operations

## 官方定義

Terminal operations, such as `Stream.forEach` or `IntStream.sum`, may traverse the stream to produce a result or a side-effect. After the terminal operation is performed, the stream pipeline is considered consumed, and can no longer be used; if you need to traverse the same data source again, you must return to the data source to get a new stream. In almost all cases, terminal operations are _eager_, completing their traversal of the data source and processing of the pipeline before returning.&#x20;

## anyMatch

如同字義上描述，只要在Stream中任一元素符合所陳述之條件，就視為true，反之全部都不符合就視為false，以下範例確認資料中是否有價格大於500元的發票

```java
List<Receipt> receiptList = List.of(
        new Receipt("A0000001", LocalDate.now(), new BigDecimal("100")),
        new Receipt("A0000002", LocalDate.now(), new BigDecimal("359")),
        new Receipt("A0000003", LocalDate.now(), new BigDecimal("782"))
);
boolean hasPriceGreater500 = receiptList.stream()
        .anyMatch(receipt -> receipt.getAmount().compareTo(new BigDecimal("500")) > 0);
//結果 true
```

## allMatch

如同字義上描述，必須是Stream中的所有元素都符合條件，就視為true，若只要有一個元素不符合條件則視為false，以下範例確認資料中是否所有發票價格都大於500元

```java
List<Receipt> receiptList = List.of(
        new Receipt("A0000001", LocalDate.now(), new BigDecimal("100")),
        new Receipt("A0000002", LocalDate.now(), new BigDecimal("359")),
        new Receipt("A0000003", LocalDate.now(), new BigDecimal("782"))
);
boolean hasPriceGreater500 = receiptList.stream()
        .allMatch(receipt -> receipt.getAmount().compareTo(new BigDecimal("500")) > 0);
//結果 false
```

## noneMatch

如同字義上描述，必須是Stream中的所有元素都沒有符合條件，就視為true，若只要有一個元素符合條件則視為false(其實就等同是`anyMatch`的反義詞)

```java
List<Receipt> receiptList = List.of(
        new Receipt("A0000001", LocalDate.now(), new BigDecimal("100")),
        new Receipt("A0000002", LocalDate.now(), new BigDecimal("359")),
        new Receipt("A0000003", LocalDate.now(), new BigDecimal("782"))
);
boolean hasPriceGreater500 = receiptList.stream()
        .noneMatch(receipt -> receipt.getAmount().compareTo(new BigDecimal("500")) > 0);
//結果 false
```

## collect

## count

## findAny

## findFirst

## forEach

在Stream中有兩個API可以進行for each的操作

* forEach
* forEachOrdered

這兩個差異點在於forEach在執行時可能不按照物件內順序，forEachOrdered則是會強制物件內順序執行。

```java
// 先產生一個List，內容是從0~1000依序排列的值
List<Integer> list = new ArrayList<>();
for (int i = 0; i < 1000; i ++) {
    list.add(i);
}
```

```java
// 使用Stream的forEach
list.stream().forEach(System.out::println);
//結果 0,1,2,3,4,5,6,7,8...
```

```java
// 使用parallelStream的forEach
list.parallelStream().forEach(System.out::println);
//結果 627,628,629,630,631,327,328...
```

```java
// 使用Stream的forEachOrdered
list.stream().forEachOrdered(System.out::println);
//結果 0,1,2,3,4,5,6,7,8...
```

```java
// 使用parallelStream的forEachOrdered
list.parallelStream().forEachOrdered(System.out::println);
//結果 0,1,2,3,4,5,6,7,8...
```

結論：當使用Stream時，不論是forEach還是forEachOrdered都會按照物件內順序依序執行，但如果是使用parallelStream時就會產生差異。

## min

## max

## reduce

## toArray
