# Stream介紹

## 什麼是Stream(串流)

Stream 是一種用於方便資料處理（如排序、運算、整合等）的工具。它的主要優點在於簡化程式碼，使其更加簡潔易讀。結合 Lambda 表達式使用後，Stream 可以顯著降低程式碼的複雜度（這裡的複雜度指的是閱讀，而非效能）。雖然 Stream 使程式碼更易理解，但在不熟悉其設計原理時，可能會導致效能瓶頸。不過，總體來說，其優勢多於弊端。

首先，以下是使用傳統迴圈和條件語句的範例：

```java
// 篩選出年紀大於18歲且身高超過180公分，且BMI介於15~24之間的人
// 並且依照年紀排序後取出姓名
List<Human> humanList = humanDao.findAll();
List<Human> newHumanList = new ArrayList<>();
List<String> nameList = new ArrayList<>();
for (Human human : humanList) {
    if (human.getAge() >= 18
            && human.getHeight() >= 180f
            && human.getBMI().compareTo(new BigDecimal("15")) >= 0
            && human.getBMI().compareTo(new BigDecimal("24")) <= 0) {
        newHumanList.add(human);
    }
}
newHumanList.sort(new Comparator<Human>() {
    @Override
    public int compare(Human o1, Human o2) {
        return o1.getAge() - o2.getAge();
    }
});
for (Human human : newHumanList) {
    nameList.add(human.getName());
}
```

使用 Stream 來改寫上述邏輯後的程式碼如下：

```java
List<Human> humanList = humanDao.findAll();
List<String> nameList = new ArrayList<>();
nameList = humanList.stream()
        .filter(human -> human.getAge() >= 18
                && human.getHeight() >= 180f
                && human.getBMI().compareTo(new BigDecimal("15")) >= 0
                && human.getBMI().compareTo(new BigDecimal("24")) <= 0)
        .sorted(Comparator.comparingInt(Human::getAge))
        .map(Human::getName)
        .collect(Collectors.toList());
```

從上述範例可以看出，Stream 確實提供了方便性，使程式碼更具可讀性。

## 串流(Stream)與併行流(ParallelStream)

Stream 是單一流向的資料流，操作方式與一般物件操作類似。然而，當資料量較大時，可能會導致執行時間過長，此時可以考慮使用 ParallelStream。ParallelStream 是多流向的資料流，可以類比為使用多線程的 Stream，通過資源換取效能。然而，這也可能引發線程安全問題，因此在使用時需要特別注意。
