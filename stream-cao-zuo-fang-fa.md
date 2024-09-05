# Stream操作方法

Stream繼承至BaseStream，而BaseStream又繼承至AutoCloseable，因此可以得知Stream與一般的I/O類別的Stream一樣都有個關閉(close)的功能，只是I/O類別的Stream需要手動關閉(close)，而Stream是自動關閉的，當然，要自行進行關閉也可以，範例如下：

```java
// 承襲前一頁範例，只要使用try-with-resources即可
try (Stream<Human> humanStream = humanList.stream()) {
    //邏輯...
} finally {
    System.out.print("end");
}
```

繼承至BaseStream不只是Stream而已，還有DoubleStream、IntStream、LongStream。

{% hint style="info" %}
DoubleStream、IntStream、LongStream，有興趣者可以自行查閱[官方API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)說明或自行拆解JDK進入底層去研究這些Stream的作用與實作方式。
{% endhint %}

而常見的Collection to Stream轉換出來的Stream是通用型別的Stream\<E>，首先就先來自己建立一個Stream吧！

```java
// 使用Stream.Builder進行Stream的建立
Stream.Builder<Integer> streamBuilder = Stream.builder();
Stream<Integer> stream = streamBuilder.add(1).add(2).add(3).add(4).add(5).build();

// 使用Stream.of進行Stream的建立
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);

// 使用Stream.empty建立一個空的Stream
Stream<Integer> stream = Stream.empty();
```

{% hint style="info" %}
Stream.Builder 與 Stream.of 的差異就留待各位自行探索了！
{% endhint %}

