# terminal operations

## 官方定義

Terminal operations, such as `Stream.forEach` or `IntStream.sum`, may traverse the stream to produce a result or a side-effect. After the terminal operation is performed, the stream pipeline is considered consumed, and can no longer be used; if you need to traverse the same data source again, you must return to the data source to get a new stream. In almost all cases, terminal operations are _eager_, completing their traversal of the data source and processing of the pipeline before returning.&#x20;

## anyMatch

* 如同字義上描述，只要在Stream中任一元素符合所陳述之條件，就視為true，反之全部都不符合就視為false，以下範例確認資料中是否有價格大於500元的發票

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

* 如同字義上描述，必須是Stream中的所有元素都符合條件，就視為true，若只要有一個元素不符合條件則視為false，以下範例確認資料中是否所有發票價格都大於500元

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

* 如同字義上描述，必須是Stream中的所有元素都沒有符合條件，就視為true，若只要有一個元素符合條件則視為false(其實就等同是`anyMatch`的反義詞)

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

* 這是一種收集器，將stream中的物件收集後，轉換成另一個物件，可以是List、Set等常見的Collection，也可以轉換成其他物件，如String、Integer、Double等資料型態

```java
Receipt[] receipts = {
    new Receipt("A0000001", LocalDate.now(), new BigDecimal("100")),
    new Receipt("A0000002", LocalDate.now(), new BigDecimal("359")),
    new Receipt("A0000003", LocalDate.now(), new BigDecimal("782")),
    new Receipt("A0000003", LocalDate.now(), new BigDecimal("782")),
    new Receipt("A0000002", LocalDate.now(), new BigDecimal("359"))
};
Set<Receipt> receiptSet = Stream.of(receipts).collect(Collectors.toSet());
```

```java
List<Integer> list = List.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
Double avg = list.stream().collect(Collectors.averagingInt(i -> i));
System.out.println("avg = " + avg);
//結果 avg = 4.5
```

* 在Collectors中有非常多的函式可以進行操作，詳見[官方文件](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collectors.html)

## count

* stream中的元素總數量，一般來說比較不會直接使用，通常會搭配其他函式進行操作，譬如fillter，先將元素進行過濾後計算數量等等

## findAny

* 任意取出一個元素，請注意，這裡指的是`任意`，而使用findAny、findFirst取出來的會是Optional，這是一個元素保護的物件，避免直接取出時導致null point exception，當然如果直接使用Optional.get()，那就等同忽視保護機制直接取值，除非能確保100%有值，不然不建議直接使用get取值，畢竟Java是一個null point exception無處不在的語言

```java
List<Integer> list = List.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);

Optional<Integer> o1 = list.stream().findAny();
Optional<Integer> o2 = list.parallelStream().findAny();
```

{% hint style="info" %}
請問o1跟o2取出來的元素會是同一個嗎？為什麼？
{% endhint %}

## findFirst

* 取出第一個元素

## forEach

* 在Stream中有兩個API可以進行遍覽操作forEach、forEachOrdered，這兩個差異點在於forEach在執行時可能不按照物件內順序，forEachOrdered則是會強制物件內順序執行。

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

* 當使用Stream時，不論是forEach還是forEachOrdered都會按照物件內順序依序執行，但如果是使用parallelStream時就會產生差異。

## min

* 找出元素中的最小值

## max

* 找出元素中的最大值

{% hint style="info" %}
如果是非數字類型的物件要找出最大值/最小值要怎麼處理？
{% endhint %}

## reduce

* 在Stream中進行聚合，與前面的find查找資料；min、count計算資料等方法是不一樣的，開發人員可以在reduce中進行計算並返回結果(返回的結果同樣是Optional)

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Integer result = stream.reduce((res, i) -> res * i).orElse(0);
System.out.println(result);

//結果 3628800
```

* reduce在使用上可以給予起始值，像上面的案例是沒有起始值的，在起始值的情況下reduce會使用第一個元素作為起始值，下面的案例就賦予一個起始值 `-1`&#x20;

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

Integer result = stream.reduce(-1, (res, i) -> res * i);
System.out.println(result);

//結果 -3628800
```

{% hint style="info" %}
起始值是否賦予的情況下，除了結果不一樣之外，還有什麼差異呢？
{% endhint %}
