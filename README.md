# Stream介紹

## 什麼是Stream(串流)

Stream是為了方便資料處理(排序、運算、整合...等等)，它有許多優點，結合了Lambda的使用，讓程式碼變得更為簡潔易讀，通過不同的API操作，讓程式碼的複雜度降低(這邊的複雜度指的是閱讀，無關效能)，雖然在操作上簡潔易懂，但在不熟悉設計原理與邏輯時，是有可能導致效能瓶頸，但終歸利多於弊。

首先，來看一下以下範例，Stream簡化後的程式碼長怎樣。

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

讓我們使用Stream來改寫上述的邏輯吧

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

從上述範例中可以看到Stream帶來了一些方便性，讓程式碼變得比較好閱讀。

## 串流(Stream)與併行流(ParallelStream)

stream是單一流向的資料流，因此在操作上與一般的物件操作上並無二致，也因此當資料量較大時，有可能會導致執行時間過久，此時就可以考慮使用ParallelStream，ParallelStream是多流向的資料流，可以想像成是使用Thread的Stream，利用資源換取效能，但同時也可能會產生Thread Safe的議題，因此在使用上需格外注意。
