# Stream

## Stream 三个步骤



1. 创建 Stream

   一个数据源（如：集合、数组）获取一个流

2. 中间操作

   一个中间操作链，对数据源的数据进行处理

3. 终止操作（终端操作）

   一个终止操作，执行中间操作链，并产生结果

![image-20210804142227798](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210804142227798.png)



## Stream 的中间操作



多个<font color="blue">中间操作</font>可以连续起来形成一个<font color="blue">流水线</font>，除非流水线上出发终止操作，否则<font color="blue">中间操作不会执行任何的处理</font>！而在<font color="blue">终止操作时一次性全部处理，称为”惰性求值“</font>

![image-20210804145010990](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210804145010990.png)





### filter

接收 Lambda ，从流中排序某些元素

```java
  // 内部迭代，迭代有 stream API 完成
  @Test
  public void test(){
    Stream<Employee> stream = emp.stream()
            .filter(e ->{
              System.out.println("中间迭代");
              return e.getAge() > 30;
            });
    stream.forEach(System.out::println);
  }
```



### limit

截断流，使其元素不超过数量

```java
public void test(){
    emp.stream()
        .filter(e -> {
            System.out.println("短路");
            return e.getSal() > 5000;
        })
        .limit(2)
        .forEach(System.out::println);
}
```



### skip(n)

跳过元素，返回一个扔掉了前 n 个元素的流。若流中元素不足 n 个，则返回一个空流，与 limit(n) 互补

```java
public void test(){
    emp.stream()
        .filter(e -> {
            System.out.println("哈哈");
            return e.getSal() > 5000;
        })
        .skip(2)
        .forEach(System.out::println);
}
```



### distinct

筛选，通过流所生成元素的 hasCode() 和 equals() 去除重复元素

```java
public void test(){
    emp.stream()
        .filter(e -> e.getSal() > 5000)
        .skip(2)
        .distinct()
        .forEach(System.out::println);
}
```



## Map & flatMap



### 映射

**map** —— 接收 Lambda，将元素转换成其他形式或提取信息。接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素

 **flatMap** —— 接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。

```java
@Test
public void test6(){
    List<String> list = Arrays.asList("aa","bb","cc","dd");
    list.stream()
        .map(str -> str.toUpperCase())
        .forEach(System.out::println);
    System.out.println("---------------------------");
    emp.stream()
        .map(Employee::getName)
        .forEach(System.out::println);

    Stream<Character> stream = list.stream()
        .flatMap(TestStreamAPI2::filterCharacter);
    stream.forEach(System.out::println);
}

public static Stream<Character> filterCharacter(String str){
    List<Character> list = new ArrayList<>();
    // 将字符串转成单个并形成数组
    for (Character ch : str.toCharArray()) {
        list.add(ch);
    }
    return list.stream();
}
```

![image-20210804163709046](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210804163709046.png)



## Stream 查找与匹配



```java
/**
  查找与匹配
  allMatch —— 检查是否匹配所有元素
  anyMatch —— 检查是否至少匹配一个元素
  noneMatch —— 检查是否没有匹配所有元素
  findFirst —— 返回第一个元素
  findAny —— 返回当前流中的任意元素
  count —— 返回流中元素的总个数
  max —— 返回流中最大值
  min —— 返回流中最小值
*/
```

### allMatch

```java
boolean b = emp.stream()
        .allMatch(e -> e.getStatus().equals(Employee.Status.BUSY));
System.out.println(b);
```

### anyMatch

```java
boolean b1 = emp.stream()
        .anyMatch(e -> e.getStatus().equals(Employee.Status.BUSY));
System.out.println(b1);
```

### noneMatch

```java
boolean b2 = emp.stream()
        .noneMatch(e -> e.getStatus().equals(Employee.Status.BUSY));
System.out.println(b2);
```

### findFirst

```java
Optional<Employee> op = emp.stream()
        .sorted((e1, e2) -> Double.compare(e1.getSal(), e2.getSal()))
        .findFirst();
System.out.println(op.get());
```

### findAny

```java
Optional<Employee> op2 = emp.parallelStream()
        .filter(e -> e.getStatus().equals(Employee.Status.BUSY))
        .findAny();
System.out.println(op2.get());
```

### count

```java
long count = emp.stream()
        .count();
System.out.println(count);
```

### max

```java
Optional<Employee> op = emp.stream().max((e1, e2) -> Double.compare(e1.getSal(), e2.getSal()));
System.out.println(op.get());
```

### min

```java
Optional<Integer> op2 = emp.stream()
        // 先将工资提取出来
        .map(Employee::getSal)
        // 再冲里面挑选一个最低工资
        .min(Double::compare);
System.out.println(op2.get());
```



## 归约：reduce



`reduce(T identity, BinaryOperator) / reduce(BinaryOperator)` 可以将流反复结合起来，得到一个值



```java
public void test(){
    List<Integer> list = Arrays.asList(1,2,3,4,5,6,7,8,9,10);
    Integer sum = list.stream()
        .reduce(0,(x,y) -> x + y);
    System.out.println(sum);
	Optional<Integer> reduce = emp.stream()
        .map(Employee::getSal)
        .reduce(Integer::sum);
    System.out.println(reduce.get());
}
```



## 收集：collect



`collect` —— 将流转换为其他形式。接受一个 Collector 接口的实现，用于给 Stream 中元素做汇总的方法



```java
public void test(){
	/*
    	toList 方式
    */
    List<String> list = emp.stream()
        .map(Employee::getName)
        .collect(Collectors.toList());
    list.forEach(System.out::println);
    /*
    	toSet 方式
    */
    Set<String> set = emp.stream()
        .map(Employee::getName)
        .collect(Collectors.toSet());
    list.forEach(System.out::println);
    /*
    	HashSet 方式
    */
    HashSet<String> hashSet = emp.stream()
        .map(Employee::getName)
        .collect(Collectors.toCollection(HashSet::new));
    hashSet.forEach(System.out::println);
}
```



```java
// 求 总数量，平均值，求和，最大值，获取最小工资，输出一个数值
public void test(){
    // 总数量
    Long count = emp.stream()
        .collect(Collectors.counting());
    System.out.println(count);
    // 平均值
    Double average = emp.stream()
        .collect(Collectors.averagingInt(Employee::getSal));
    System.out.println(average);
    // 求和
    int sum = emp.stream()
        .collect(Colectors.summingInt(Employee::getSal));
    System.out.println(sum);
    // 最大值
    Optional<Employee> max = emp.stream()
        .collect(Collectors.maxBy((e1, e2) -> Integer.compare(e1.getSal(), e2.getSal())));
    System.out.println(max.get());
    // 获取工资最小值
    Optional<Integer> min = emp.stream()
        .map(Employee::getSal)
        .collect(Collectors.minBy(Integer::compare));
    System.out.println(min.get());
}
```



```java
// 分组
public void test(){
    Map<Employee.Status, List<Employee>> map = emp.stream()
        .collect(Collectors.groupingBy(Employee::getStatus));
    System.out.println(map);
}

```



```java
// 分区
public void test(){
	Map<Boolean, List<Employee>> collect = emp.stream()
        .collect(Collectors.partitioningBy(e -> e.getSal > 8000));
    System.out.println(collect);
}
```



## Stream API 练习



```java
// 1. 给定一个数字列表，返回一个由每个数的平方构成的列表
public void test(){
    Integer nums = new Integer[]{1,2,3,4,5};
    Arrays.stream(nums)
        .map(x -> x * x)
        .forEach(System.out::println);
}

// 使用 map 和 reduce 方法数一数流中一共有多少个 Employee。
public void test1(){
    Optional<Integer> count = emp.stream()
        .map(e -> 1)
        .reduce(Integer::sum);
    System.out.println(count.get());
}

// 1. 找出 2011 年发生的所有交易，并按交易额排序（低到高）
public void test(){
    emp.stream()
        .filter(e -> e.getYear() == 2011)
        .sorted((t1, t2) -> Integer.compare(t1.getValue(), t2.getValue()))
        .forEach(System.out::println)
}
// 2. 交易员都在那些不同的城市工作
public void test(){
    emp.stream()
        .map(Employee::getCity())
        .distinct()
        .forEach(System.out::println);
}
// 3. 查询所有来自剑桥的交易员，并按姓名排序
public void test(){
    emp.stream()
        .filter(e -> e.getName.equals("jianqiao"))
        .map(Employee::getName())
        .sorted((t1, t2) -> t1.getName().compareTo(t2.getName()))
        .forEach(System.out::println)
}
// 4. 返回所有交易员的姓名字符串，按字母顺序排序
public void test(){
    emp.stream()
        .map(t -> t.getName())
        .sorted()
        .forEach(System.out::println);
    
    String str = emp.stream()
        .map(e -> e.getName)
        .sorted()
        .reduce("", String::concat);
    
}

public Stream<String> filterCharacter(String str){
    List<String> list = new ArrayList()<>;
    for(Character ch : str.toCharArray()){
        list.add(ch.toString());
    }
    return list.stream();
}

//5. 取出来最高的交易额
public void test(){
    Optional<Integer> max = emp.stream()
        .map(t -> t.getValue())
        .max(Integer::compare);
    System.out.println(max);
}

// 6. 找出最小的交易
public void test(){
    emp.stream()
        .min((t1, t2) -> Integer.compare(t1.getValue(), t2.getValue()));
}
```























