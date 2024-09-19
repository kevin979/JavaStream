# intermediate operation

## 官方定義

Intermediate operations return a new stream. They are always _lazy_; executing an intermediate operation such as `filter()` does not actually perform any filtering, but instead creates a new stream that, when traversed, contains the elements of the initial stream that match the given predicate. Traversal of the pipeline source does not begin until the terminal operation of the pipeline is executed.

## filter

* Filter 是用來根據條件過濾 Stream 的內容。

```java
// 在filter中撰寫條件
humanList.stream()
        .filter(human -> human.getAge() >= 18
                && human.getHeight() >= 180f
                && human.getBMI().compareTo(new BigDecimal("15")) >= 0
                && human.getBMI().compareTo(new BigDecimal("24")) <= 0);
// 也可以分在在不同的filter中撰寫條件
humanList.stream()
        .filter(human -> human.getAge() >= 18)
        .filter(human -> human.getHeight() >= 180f)
        .filter(human -> human.getBMI().compareTo(new BigDecimal("15")) >= 0)
        .filter(human -> human.getBMI().compareTo(new BigDecimal("24")) <= 0);
```

{% hint style="info" %}
以上兩種寫法效果一樣，但真的沒有差異或影響嗎？
{% endhint %}

## map

* map是用來取出映射的值，在操作上可分為map、mapToDouble、mapToInt、mapToLong，可使用通用型別或指定型別，常用為map，其他類型則較為少用。

```java
// 利用mapToInt取出年紀的資料
IntStream intStream = humanList.stream().mapToInt(Human::getAge);
```

* map功能不僅僅只有取值，也可以利用map進行數值調整

```java
// 將身高大於180的人，年紀調整為18
Stream<Human> stream = humanList.stream().map(human -> {
    if (human.getHeight() >= 180f) {
        human.setAge(18);
    }
    return human;
});
```

## flatMap

* flatMap是將二維陣列平壓重整成一維串流，在操作上可分為flatMap、flatMapToDouble、flatMapToInt、flatMapToLong，可使用通用型別或指定型別，常用為flatMap，其他類型則較為少用。

```java
List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
List<Integer> list2 = Arrays.asList(100, 101, 102, 103, 104, 105, 106, 107, 108, 109);
List<Integer> list3 = Arrays.asList(200, 201, 202, 203, 204, 205, 206, 207, 208, 209);
List<List<Integer>> list = Arrays.asList(list1, list2, list3);
list.stream().flatMap(Collection::stream).forEach(e -> System.out.print(e + " "));
// 執行結果如下
// 1 2 3 4 5 6 7 8 9 100 101 102 103 104 105 106 107 108 109 200 201 202 203 204 205 206 207 208 209
```

* 因為flatMap是將二維陣列進行平壓重整，因此在重整過程中是可以針對資料進行加工

```java
List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
List<Integer> list2 = Arrays.asList(100, 101, 102, 103, 104, 105, 106, 107, 108, 109);
List<Integer> list3 = Arrays.asList(200, 201, 202, 203, 204, 205, 206, 207, 208, 209);
List<List<Integer>> list = Arrays.asList(list1, list2, list3);
list.stream().flatMap(ll -> ll.stream().map(i -> {
    i++;
    return i;
})).forEach(e -> System.out.print(e + " "));
// 執行結果如下
// 2 3 4 5 6 7 8 9 10 101 102 103 104 105 106 107 108 109 110 201 202 203 204 205 206 207 208 209 210
```

## distinct

* 將重複資料過濾後，保留一筆

<pre class="language-java"><code class="lang-java"><strong>// 先產生一個Stream，裡面有多筆資料，然後有重複資料
</strong>Stream&#x3C;Integer> stream = Stream.of(0, 1, 2, 3, 4, 5, 4, 3, 2, 1, 0);
// 使用distinct後
stream.distinct().forEach(e -> System.out.print(e + " "));
// 結果0 1 2 3 4 5
</code></pre>

{% hint style="info" %}
distinct是利用hashCode() & equals()進行物件比對，如果Stream內的物件你沒有實作hashCode() & equals()會發生什麼事？抑或是hashCode() & equals()你的實作內容沒有納入所有屬性會發生什麼事？
{% endhint %}

## sorted

* 排序時會需要指定欄位、排序方式(升冪、降冪)，因此Stream在sorted設計上是使用Comparator來實現(當然List的sort也是使用Comparator進行實現)

```java
// 依據姓名進行排序，預設是升冪
Stream<Human> stream = humanList.stream().sorted(Comparator.comparing(Human::getName));
// 依據姓名進行排序，降冪
Stream<Human> stream = humanList.stream().sorted(Comparator.comparing(Human::getName).reversed());
```

* 如果要排序的條件有多個的情況，那一樣是可以使用Comparator實現

```java
Stream<Human> stream = humanList.stream()
        .sorted(Comparator.comparing(Human::getAge)
                .thenComparing(Human::getHeight).reversed()
                .thenComparing(Human::getName));
```

## peek

* 這是一個非常特別的API，依據官方文件所述，它的存在是為了debug，而通過此API的元素其實不會被異動

```java
Stream<String> stream = Stream.of("a", "b", "c");
stream.peek(s -> s.toLowerCase()).forEach(s -> System.out.print(s + " "));
// 執行結果 a b c
```

* 如果要用來進行debug，那使用方式可參考如下

```java
List<String> list = stream.peek(s -> System.out.println("element = " + s))
        .map(String::toUpperCase)
        .peek(s -> System.out.println("new element = " + s))
        .collect(Collectors.toList());
/** 執行結果如下
element = a
new element = A
element = b
new element = B
element = c
new element = C
**/
```

* peek真的無法改變元素內容嗎？

```java
List<Receipt> receiptList = List.of(
    new Receipt("A0000001", LocalDate.now(), new BigDecimal("100")),
    new Receipt("A0000002", LocalDate.now(), new BigDecimal("359")),
    new Receipt("A0000003", LocalDate.now(), new BigDecimal("782"))
);
List<Receipt> newReceiptList = receiptList.stream()
    .peek(receipt -> receipt.setReceiptID(receipt.getReceiptID().concat("-C")))
    .peek(receipt -> receipt.setAmount(receipt.getAmount().add(BigDecimal.TEN)))
    .collect(Collectors.toList());
newReceiptList.forEach(System.out::println);
/** 結果
Receipt{receiptID='A0000001-C', receiptDate=2024-09-08, amount=110}
Receipt{receiptID='A0000002-C', receiptDate=2024-09-08, amount=369}
Receipt{receiptID='A0000003-C', receiptDate=2024-09-08, amount=792}
**/
```

{% hint style="info" %}
為什麼上面字串轉大寫沒效，但使用物件改資料卻可以生效？？？
{% endhint %}

## limit

* 指定資料筆數

```java
List<Integer> list = List.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
list.stream().limit(3).forEach(i -> System.out.print(i + " "));
//結果 0 1 2
```

{% hint style="info" %}
如果limit指定的數字大於Stream的元素數量會發生什麼事？
{% endhint %}

## skip

* 跳過指定資料筆數

```java
List<Integer> list = List.of(0, 1, 2, 3, 4, 5, 6, 7, 8, 9);
list.stream().skip(3).forEach(i -> System.out.print(i + " "));
//結果 3 4 5 6 7 8 9
```

{% hint style="info" %}
limit 是取得指定筆數，而skip是跳過指定筆數，如果兩個合用會發生什麼變化呢？
{% endhint %}
