# Stream操作方法

**Stream 的關閉功能**

Stream 繼承自 `BaseStream`，而 `BaseStream` 又繼承自 `AutoCloseable`，因此 Stream 類別和 I/O 類別的 Stream 一樣，都具有關閉（`close`）的功能。不過，I/O 類別的 Stream 需要手動關閉，而 Stream 在處理完畢後會自動關閉。當然，你也可以自行關閉，示例如下：

```java
// 承襲前一頁範例，只要使用try-with-resources即可
try (Stream<Human> humanStream = humanList.stream()) {
    //邏輯...
} finally {
    System.out.print("end");
}
```

**Stream 的子類別**

除了 `Stream`，`BaseStream` 還有 `DoubleStream`、`IntStream` 和 `LongStream`。

{% hint style="info" %}
這些類別各自處理不同的數據類型，詳細資訊可以參考官方 API 說明或進一步研究 JDK 內部實作。
{% endhint %}

**建立 Stream**

常見的 Collection 轉換成 Stream 時，得到的是通用型別的 `Stream<E>`。此外，還可以通過以下方式建立 Stream：

* 使用 `Stream.Builder`：

```java
// 使用Stream.Builder進行Stream的建立
Stream.Builder<Integer> streamBuilder = Stream.builder();
Stream<Integer> stream = streamBuilder.add(1).add(2).add(3).add(4).add(5).build();
```

* 使用 `Stream.of`：

```java
// 使用Stream.of進行Stream的建立
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);
```

* 使用 `Stream.empty` 來建立一個空的 Stream：

```java
// 使用Stream.empty建立一個空的Stream
Stream<Integer> stream = Stream.empty();
```

{% hint style="info" %}
Stream.Builder 與 Stream.of 的差異就留待各位自行探索了！
{% endhint %}

**Collection 和 Stream 的轉換**

* 將 Collection 轉換成 Stream（如 `List` 和 `Set`）：

```java
// Collection to Stream (List、Set)
Stream<Human> stream = humanList.stream();
```

* 合併兩個 Stream（必須是相同型別）：

```java
// 兩個Stream合併
Stream<Integer> stream1 = Stream.of(1, 2, 3, 4, 5);
Stream<Integer> stream2 = Stream.of(6, 7, 8, 9, 10);
Stream<Integer> stream = Stream.concat(stream1, stream2);
```

**特殊的生成方式**

* 無限長度的 Stream（不建議使用）：

```java
// 無限長度的Stream
Stream<Integer> stream = Stream.generate(() -> new Random().nextInt())
```

* 無限長度的 Stream，並設置停止條件：

```java
// 無限長度的Stream，可設置停止生長的限制
Stream<Integer> stream = Stream.iterate(1, n -> n < 25, n -> n + 1);
```

特殊的生成方式請謹慎使用，並確保適當設置限制條件以避免無窮迴圈。
