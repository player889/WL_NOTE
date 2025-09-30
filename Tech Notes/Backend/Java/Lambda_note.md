#Lambda

> [!info]+
>
> -   Supplier(生產者) :他的泛型一定和方法的返回值類型是一種類型，如果需要獲得一個數據,並且不需要傳入參數,可以使用 Supplier 接口.
> -   Consumer(消費者):如果想要==**處理一個數據,但是不需要返回值**==,可以使用 Consumr 接口e
> -   predicate(判斷):如果想要判斷一個數據,並且需要一 個布爾類型的返回值,可以使用 predicate 接口.
> -   Function(函數):如果想要進行屬性之間的轉換,如 String->Integer,則需要使用 Function 接口,Function 的泛型一般有兩種類型,前面的類型表示傳入的參數類型,後面的類型表示返回值類型.

````ad-note









```ad-info
title: List
collapse: close
~~~java
List<A> aList = getAList();
List<B> bList = new ArrayList<>();
for(A a : aList) { bList.add(a.getB()); }
aList.forEach(a -> bList.add(a.getB()));
//java 8
aList.stream().map(A::getB).forEach(bList::add);
List<B> bList = aList.stream().map(A::getB).collect(Collectors.toList());

List<Select2Vo> bList = e.stream().map(o -> new Select2Vo(o.getAccountId(), o.getLevel())).collect(Collectors.toList());
// equivalent to collectors
List<Select2Vo> cList = e.stream().collect(Collectors.mapping(o -> new Select2Vo(o.getAccountId(), o.getLevel()), Collectors.toList()));

Arrays.stream(opp.getClient()).collect(Collectors.mapping(client -> accountService.findByNameAndCode(client.getName(), client.getNo()), Collectors.toList()));
List<String> associates = new ArrayList<>();
curPage.getUsers().stream().map(User::getPrimaryEmail).collect(Collectors.toCollection(() -> associates));
curPage.getUsers().stream().map(User::getPrimaryEmail).collect(Collectors.toList());

Consumer<String> action = S -> { System.out.println("Value is : " + S.toString()); };
emails.stream().map(String::toString).forEach(action);
~~~

```













```ad-info
title: Group By
collapse: close
~~~java
List<Item> items = Arrays.asList(
    new Item("apple", 10, new BigDecimal("9.99")),
    new Item("banana", 20, new BigDecimal("19.99")),
    new Item("orang", 10, new BigDecimal("29.99")),
    new Item("watermelon", 10, new BigDecimal("29.99")),
    new Item("papaya", 20, new BigDecimal("9.99")),
    new Item("apple", 10, new BigDecimal("9.99")),
    new Item("banana", 10, new BigDecimal("19.99")),
    new Item("apple", 20, new BigDecimal("9.99"))
);

//group by price
Map<BigDecimal, List<Item>> groupByPriceMap = items.stream().collect(Collectors.groupingBy(Item::getPrice));

System.out.println(groupByPriceMap);
/*
{
	19.99=[
			Item{name='banana', qty=20, price=19.99},
			Item{name='banana', qty=10, price=19.99}
		],
	29.99=[
			Item{name='orang', qty=10, price=29.99},
			Item{name='watermelon', qty=10, price=29.99}
		],
	9.99=[
			Item{name='apple', qty=10, price=9.99},
			Item{name='papaya', qty=20, price=9.99},
			Item{name='apple', qty=10, price=9.99},
			Item{name='apple', qty=20, price=9.99}
		]
}
*/

// group by price, uses 'mapping' to convert List<Item> to Set<String>
Map<BigDecimal, Set<String>> result = items.stream().collect(Collectors.groupingBy(Item::getPrice,Collectors.mapping(Item::getName, Collectors.toSet())));
/*
{
	19.99=[banana],
	29.99=[orang, watermelon],
	9.99=[papaya, apple]
}
*/

Map <Gender, Map <Object, String>> byGenderAndBirthDate = persons.stream()
	.collect(Collectors.groupingBy(p -> p.getGender(),
	Collectors.groupingBy(p -> dateFormat.format(p.getBirthDate()),
	Collectors.mapping(p -> p.getName(), Collectors.joining(", "))))
);

public Map<Integer, Long> groupByLikesWithCounts() {
    return fruits.stream().collect(Collectors.groupingBy(Fruit::getLikes, Collectors.counting()));
}

//simplest grouping by an integer
public Map<Integer, List<Fruit>> groupByQuantity() {
    return fruits.stream().collect(Collectors.groupingBy(Fruit::getQuantity));
}

//simplest grouping by an enum with a string
public Map<FruitType, List<Fruit>> groupByType() {
    return fruits.stream().collect(Collectors.groupingBy(Fruit::getType));
}

// simplest grouping by an enum with lambda, toList() is the default
public Map<FruitType, List<Fruit>> groupByType_lambda() {
    return fruits.stream().collect(Collectors.groupingBy(f -> f.getType()));
}

//group by more than two classification with a method
public Map<String, List<Fruit>> groupByTypeAndLike() {
    return fruits.stream().collect(Collectors.groupingBy(Fruit::getTypeLikes));
}

//group by more than two classification with two groupingBy
public Map<FruitType, Map<Integer, List<Fruit>>> groupByTypeAndLike_2() {
    return fruits.stream()
        .collect(Collectors.groupingBy(Fruit::getType, Collectors.groupingBy(Fruit::getLikes)));
}

//concurrent grouping by with enum
public Map<FruitType, List<Fruit>> groupByTypeConcrrently() {
    return fruits.parallelStream().collect(Collectors.groupingByConcurrent(Fruit::getType));
}

//group by more than two classification with a complex type
public Map<TypeQuantity, List<Fruit>> groupByTypeQuantity(){
    return fruits.stream().collect(Collectors.groupingBy( fruit -> new TypeQuantity(fruit.getType(), fruit.getQuantity())));
}

//grouping by an enum with a string, then change the results to set
public Map<FruitType, Set<Fruit>> groupByTypeToSet() {
    return fruits.stream().collect(Collectors.groupingBy(Fruit::getType, Collectors.toSet()));
}

public Map<FruitType, Double> groupByTypeWithAverageSum() {
    return fruits.stream()
        .collect(Collectors.groupingBy(Fruit::getType, Collectors.averagingDouble(Fruit::getTotal)));
}

//grouping by a type and map it to counts
public Map<FruitType, Long> groupByTypeWithCounts() {
    return fruits.stream().collect(Collectors.groupingBy(Fruit::getType, Collectors.counting()));
}

//groupingBy with aggregation functions
public Map<FruitType, Integer> groupByTypeWithQuantitySum() {
    return fruits.stream()
        .collect(Collectors.groupingBy(Fruit::getType, Collectors.summingInt(Fruit::getQuantity)));
}

public Map<FruitType, Double> groupByTypeWithTotalSum() {
    return fruits.stream()
        .collect(Collectors.groupingBy(Fruit::getType, Collectors.summingDouble(Fruit::getTotal)));
}

public Map<FruitType, DoubleSummaryStatistics> groupByType_TotalSummary() {
    return fruits.stream().collect(Collectors.groupingBy(Fruit::getType, Collectors.summarizingDouble(Fruit::getTotal)));
}

Map<Tuple, List<BlogPost>> postsPerTypeAndAuthor = posts.stream().collect(groupingBy(post -> new Tuple(post.getType(), post.getAuthor())));

~~~
```














```ad-info
title: Comparator
collapse: close
- [Java group by sort – multiple comparators example](https://howtodoinjava.com/java/sort/groupby-sort-multiple-comparators/)

~~~java
public static <T, K extends Comparable<K>> Collector<T, ?, TreeMap<K, List<T>>>
   sortedGroupingBy(Function<T, K> function) {
        return Collectors.groupingBy(function,
           TreeMap::new, Collectors.toList());
}

Map<String, List<Book>> authorsToBooks = books
       .stream()
       .collect(sortedGroupingBy(Book::getAuthor));
//Returned the Map of authors to their books in alphabetical order.
~~~
```





```ad-info
title: Sorted
collapse: close
- [Group By and Sorted](https://www.mkyong.com/java8/java-8-how-to-sort-list-with-stream-sorted/)

~~~java
dtoList.stream()
    .sorted(Comparator.comparingInt(ComponentDto::getOrder))
    .collect(Collectors.groupingBy(ComponentDto::getGroup));
~~~
```












```ad-info
title: Join
collapse: close

~~~java
List<String> strings = List.of("a", "bb", "cc", "ddd");
TreeMap<Integer, List<String>> result = strings.stream().collect(groupingBy(String::length, TreeMap::new, toList()));
// {1=[a], 2=[bb, cc], 3=[ddd]}

Map<Integer, TreeSet<String>> result = strings.stream().collect(groupingBy(String::length, toCollection(TreeSet::new)));
//{1=[a], 2=[bb, cc], 3=[ddd]}

Map<Integer, Long> result = strings.stream().collect(groupingBy(String::length, counting()));
// {1=1, 2=2, 3=1}

Map<Integer, String> result = strings.stream().collect(groupingBy(String::length, joining(",", "[", "]")));
// {1=[a], 2=[bb,cc], 3=[ddd]}

Map<Integer, List<String>> result = strings.stream().collect(groupingBy(String::length, filtering(s -> !s.contains("c"), toList())));
// {1=[a], 2=[bb], 3=[ddd]}

Map<BlogPostType, Set<BlogPost>> postsPerType = posts.stream().collect(groupingBy(BlogPost::getType, toSet()));

Map<String, Map<BlogPostType, List>> map = posts.stream().collect(groupingBy(BlogPost::getAuthor, groupingBy(BlogPost::getType)));

Map<BlogPostType, Double> averageLikesPerType = posts.stream().collect(groupingBy(BlogPost::getType, averagingInt(BlogPost::getLikes)));

Map<BlogPostType, String> postsPerType = posts.stream().collect(groupingBy(BlogPost::getType,mapping(BlogPost::getTitle, joining(", ", "Post titles: [", "]"))));

EnumMap<BlogPostType, List<BlogPost>> postsPerType = posts.stream().collect(groupingBy(BlogPost::getType, () -> new EnumMap<>(BlogPostType.class), toList()));

Arrays.asList("a", "b", "c").stream().map(Object::toString).collect(Collectors.joining(", "));
~~~
```












```ad-info
title: Count/Group By
collapse: close

~~~java
public Map<String, Long> getWordCounts(){
    return Arrays.stream(words)
        .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
}

public Map<Integer, List<String>> getWordLength(){
    return Arrays.stream(words)
        .collect(Collectors.groupingBy(String::length));
}

public Map<Integer, Collection<String>> getWordLength_Set(){
    return Arrays.stream(words)
        .collect(Collectors.groupingBy(String::length, Collectors.toCollection(TreeSet::new)));
}

public Map<Integer, List<String>> getWordLength_List(){
    return Arrays.stream(words)
        .collect(Collectors.groupingBy(String::length, TreeMap::new, Collectors.toList()));
}

public Map<Integer, String> getWordLength_String(){
    return Arrays.stream(words)
        .collect(Collectors.groupingBy(String::length, Collectors.joining("|","[","]")));
}

public Map<Integer, IntSummaryStatistics> getWordLength_summarizingInt(){
    return Arrays.stream(words)
        .collect(Collectors.groupingBy(String::length, Collectors.summarizingInt(String::hashCode)));
}

ec.getValue().stream().map(SFLeaveBean::getDisplayQuantity).mapToDouble(Double::parseDouble).sum();

Function<SFLeaveBean, BigDecimal> totalFn = total -> new BigDecimal(total.getDisplayQuantity());
ec.getValue().stream().map(totalFn).reduce(BigDecimal.ZERO, BigDecimal::add);

double result = list.stream().collect(Collectors.summingDouble(Double::parseDouble));
~~~
```





```ad-info
title: Flatmap
collapse: close

- [FlatMap vs Map](https://www.cnblogs.com/lijingran/p/8727507.html)
- Use flatMap. If a value is present, flatMap returns a sequential Stream containing only that value, otherwise returns an empty Stream. So there is no need to use `ifPresent()`
~~~java
List<DrivePermission> permissions = new ArrayList<>();
for (DriveBean driveBean : beans) {
    permissions.addAll(driveBean.getPermissions());
}
List<DrivePermission> temp2 = beans.stream().map(DriveBean::getPermissions).flatMap(Collection::stream).collect(Collectors.toList());

list.stream().map(data -> data.getSomeValue).map(this::getOptinalValue).flatMap(Optional::stream).collect(Collectors.toList());
~~~
~~~java
//求名字中s出现的次数
Integer sCount = Stream.of(pers).map(per -> per.getName()).map(str -> str.split(""))
        .flatMap(str -> Arrays.stream(str))
        //先将每个字母映射数字0或1（如果是s，则映射为1；否则为0），再累加全部的数字（只有s才是有效的累加；因为其他字母是0，对0进行累加是没有效果的）
        .map(a -> a.equals("s") ? 1 : 0).reduce(0, (x, y) -> x + y);
System.out.println("s出现的次数是：" + sCount);
~~~


```








```ad-info
title: Filter
collapse: close

~~~java
public static <T> Predicate<T> distinctByKey2(Function<? super T, Object> keyExtractor) {
    Map<Object, Boolean> seen = new ConcurrentHashMap<>();
    return t -> seen.putIfAbsent(keyExtractor.apply(t), Boolean.TRUE) == null;
    //or return t -> seen.add(keyExtractor.apply(t));
}

//filter combind mutiple keys
contactVo.stream().filter(distinctByKey2(c -> c.getAccountId() + c.getId())).collect(Collectors.toList());
------------------------------------------------------
List<Person> list = new ArrayList<>();
list.add(new Person(100, "Mohan"));
list.add(new Person(200, "Sohan"));
list.add(new Person(300, "Mahesh"));
Map<Integer, String> map = list.stream().collect(Collectors.toMap(Person::getId, Person::getName));

Map<Integer, String> map = list.stream().collect(Collectors.toMap(Person::getId, Person::getName, (x, y) -> x+", "+ y));
//Key: 100, value: Mohan, Sohan
//Key: 300, value: Mahesh

LinkedHashMap<Integer, String> map = list.stream().collect(Collectors.toMap(Person::getId, Person::getName, (x, y) -> x+", "+ y, LinkedHashMap::new));





//Mutiple predicate
Predicate<TriggerTable> tgrIdnNbr311 = triggerData -> triggerData.getTgrIdnNbr().equals(TriggerConstants.TRIGGER_NUMBER_CREDIT_ADJUST_REVERT_SYNC_IBO_ACCOUNT);
Predicate<TriggerTable> tgrIdnNbr321 = triggerData -> triggerData.getTgrIdnNbr().equals(TriggerConstants.TRIGGER_NUMBER_CREDIT_ADJUST_REVERT_SYNC_IBO_CONSUMER);
List<TriggerTable> a = triggerTables.stream().filter(tgrIdnNbr311.or(tgrIdnNbr321)).collect(Collectors.toList());




~~~
```







```ad-info
title: ToMap
collapse: close

~~~java
Stream<String[]> str = Stream .of(new String[][] { { "GFG", "GeeksForGeeks" }, { "g", "geeks" }, { "G", "Geeks" } });
 Map<String, String> map = str.collect(Collectors.toMap(p -> p[0], p -> p[1]));
//Map:{G=Geeks, g=geeks, GFG=GeeksForGeeks}

Stream<String[]> str = Stream.of(new String[][] { { "GFG", "GeeksForGeeks" }, { "g", "geeks" }, { "GFG", "geeksforgeeks" } });
Map<String, String> map = str.collect(Collectors .toMap(p -> p[0], p -> p[1], (s, a) -> s + ", " + a));
//Map:{g=geeks, GFG=GeeksForGeeks, geeksforgeeks}

Stream<String[]> Ss1 = Stream .of(new String[][] { { "GFG", "GeeksForGeeks" }, { "g", "geeks" }, { "GFG", "Geeks" } });
map2 = Ss1.collect(Collectors.toMap(p -> p[0], p -> p[1], (s, a) -> s + ", " + a, LinkedHashMap::new));
//Map:{GFG=GeeksForGeeks, Geeks, g=geeks}

repository.findByEmailIn(mails).stream().collect(Collectors.toMap(GsuiteEntity::getEmail, GsuiteEntity::getName));
~~~
```





```ad-info
title: Convert
collapse: close

~~~java
List<Object[]> list = departmentService.getAllDepartments();
~~~

~~~java
List<Customer> customer = myObjects.stream() .filter(Customer.class::isInstance) .map(Customer.class::cast) .collect(toList());
~~~

~~~java
list.stream().forEach(x->cusList.add((Customer)x));
~~~

~~~java
(List<Customer>) (List) getList();
~~~

~~~java
scheduleIntervalContainers.stream()
    .filter(ScheduleIntervalContainer.class::isInstance)
    .map (ScheduleIntervalContainer.class::cast)
    .filter(sic -> sic.getStartTime() != sic.getEndTime())
    .collect(Collectors.toList());
~~~
```
















````

````ad-note
title: Consumer

```ad-seealso
title:Consumer - 執行 squareConsumer and then printConsumer
collapse: true
~~~java
List<Integer> numList = Arrays.asList(3, 4, 5, 6);
Consumer<List<Integer>> squareConsumer = list -> {
  for (int i = 0; i < list.size(); i++) {
	list.set(i, list.get(i) * list.get(i));
  }
};
Consumer<List<Integer>> printConsumer = list -> list.forEach(n -> System.out.println(n));
squareConsumer.andThen(printConsumer).accept(numList);
~~~
```

```ad-summary
title:Consumer advance
collapse: true
~~~java
IAccountBo accountBo = (IAccountBo) context.requireAttribute(IasContextKeys.ACCOUNT_BO);
accountBo.forSelfAndParentAccountBo(acc -> {
}

default <T extends IAccountBo> void forSelfAndParentAccountBo(Consumer<T> consumer) {
	consumer.accept((T) this);

	for (IAccountBo parentBo : this.getParentAccountBos()) {
		consumer.accept((T) parentBo);
	}
}
~~~
```


````
