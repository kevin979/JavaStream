# 終端操作

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
