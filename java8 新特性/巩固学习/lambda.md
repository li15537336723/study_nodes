# 一、Lambda

## 1. 需求分析

创建一个新的线程，指定线程要执行的任务

```java
public static void main(String[] args) {
    // 开启一个线程
    new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("新线程中执行的代码："+Thread.currentThread().getName());
        }
    }).start();
    System.out.println("主线程中的代码："+Thread.currentThread().getName());
}
```

**代码分析：**

1. Thread 类需要一个 Runnable 接口作为参数，其中的抽象方法 run 方法是来指定线程任务内容的核心
2. 为了指定 run 方法体，不得不需要 Runnable 的实现类
3. 为了省去定义一个 Runnable 的实现类，不得不使用匿名内部类
4. 必须覆盖重写抽象的 run 方法，所有的方法名称，方法参数，方法返回值不得不都再重写一遍，而且不能出错。
5. 而实际上，我们只在乎方法体中的代码



## 2. lambda 初体验

Lambda 表达式是一个匿名函数，可以理解为一段可以传递的代码

```java
new Thread(() -> {
    System.out.println("新线程Lambda表达式》》》》》"+Thread.currentThread().getName());
}).start();
```

Lambda 表达式的优点：简化了匿名内部类的使用，语法更加简单。

匿名内部类语法冗余，体验了 Lambda 表达式后，发现 Lambda 表达式是简化匿名内部类的一种方式。



## 3. Lambda 的语法规则

Lambda 省去了面向对象的条条框框，Lambda 的标准格式由 3 个部门组成

```jav
(参数类型 参数名称) ->{
	代码体;
}
```

**格式说明：**

- (参数类型 参数名称)：参数列表
- {代码体}：方法体
- ->：箭头，分割参数列表和方法体

### 3.1 Lambda 练习1

练习无返回值的 Lambda

**定义一个接口**

```java
public interface UserService {
  void show();
}
```

**然后创建主方法使用**

```java
public class Demo02Lambda {
  public static void main(String[] args) {
    goShow(new UserService() {
      @Override
      public void show() {
        System.out.println("goShow 执行了。。。。。。");
      }
    });
    System.out.println("======================");
    goShow(() -> {
      System.out.println("Lambda Show 执行了。。。。");
    });
  }

  public static void goShow(UserService userService){
    userService.show();
  }
}
```

**输出：**

```java
goShow 执行了。。。。。。
======================
Lambda Show 执行了。。。。
```



### 3.2 Lambda 练习2

练习有返回值的 Lambda

**定义一个实体类**

```java
private String name;
private Integer age;
private Integer height;
```

**然后我们在 List 集合中保存多个 Person 对象，这些对象根据 age 排序操作**

```java
public static void main(String[] args) {
    List<Person> list = new ArrayList<>();
    list.add(new Person("周杰伦",33,182));
    list.add(new Person("刘德华",20,155));
    list.add(new Person("周星驰",35,188));
    list.add(new Person("国富层",23,175));

    Collections.sort(list, new Comparator<Person>() {
      @Override
      public int compare(Person o1, Person o2) {
        return o1.getAge()-o2.getAge();
      }
    });
    for (Person person : list) {
      System.out.println(person);
    }
}
```

发现在 sort 方法的第二个参数是一个 Comparator 接口的匿名内部类，且执行的方法有参数和返回值，将其改写为 Lambda 表达式

```java
System.out.println("=========================");
Collections.sort(list, (Person p1, Person p2) -> {
    return p1.getAge() - p2.getAge();
});
for (Person person : list) {
    System.out.println(person);
}
```

**输出结果**

```java
Person{name='刘德华', age=20, height=155}
Person{name='国富层', age=23, height=175}
Person{name='周杰伦', age=33, height=182}
Person{name='周星驰', age=35, height=188}
```



## 4. @FunctionAlInterface

```java
/**
	这是一个标志注解，被该注解修饰的接口只能声明一个抽象方法
*/
@FunctionAlInterface
public interface UserService {
    void show();
}
```



## 5. Lambda 表达式的省略写法

在 lambda 表达式的标准写法基础上，可以使用省略写法的规则为：

1. 小括号内的参数类型可以省略
2. 如果小括号内有且仅有一个参数，则小括号可以省略
3. 如果大括号内有且仅有一个语句，可以同时省略大括号，return 关键字及语句分号。

```java
public static void main(String[] args) {
    // Lambda 省略写法
    goStudent((name, age) -> name + age + "  666 ....");
    goOrder(age -> 35);
}

public static void goStudent(StudentService studentService){
    studentService.show("张三", 23);
}

public static void goOrder(OrderService orderService){
    orderService.show(25);
}
```



## 6. Lambda 使用前提

Lambda 语法非常简洁，但是 Lambda 表达式不是随便使用的，使用时有几个特别要注意的

1. 方法的参数或局部变量必须为接口才能使用 Lambda
2. 接口中有且仅有一个抽象方法



## 7. Lambda 和匿名内部类的对比

Lambda 和匿名内部类的对比

1. 所需类型不一样
   1. 匿名内部类的类型可以是类，抽象类，接口
   2. Lambda 表达式需要的类型必须是接口

2. 抽象方法的数量不一样
   1. 匿名内部类所需的接口中的抽象方法的数量是随意的
   2. Lambda 表达式所需的接口中只能有一个抽象方法
3. 实现原理不一样
   1. 匿名内部类是在编译后形成 class
   2. Lambda 是在程序运行的时候动态生成 class



# 二、静态方法

JDK8 中为了新增静态方法，作用也是为了接口的扩展

## 1. 语法规则

```java
interface 接口名{
    修饰符 static 返回值类型 方法名{
        方法体
    }
}
```

## 2. 默认方法与静态方法的区别

1. 默认方法通过实例调用，静态方法通过接口名调用
2. 默认方法可以被继承，实现类可以直接调用接口默认方法，也可以重写接口默认方法
3. 静态方法不能被继承，实现类不能重写接口的静态方法，只能使用接口名调用



# 三、方法引用

## 1. 为什么要用方法引用

### 1.1 lambda 表达式冗余

在使用 Lambda 表达式的时候，也会出现代码冗余情况，比如：用 Lambda 表达式求一个数组的和

### 1.2 解决方案

因为在 Lambda 表达式中国要执行的代码和我们另一个方法中的代码是一样的，这时就没有必要重写一份逻辑了，这时我们就可以引用重复代码

```java
// :: 方法引用，也是JDK8中的新语法
public static void main(String[] args) {
  printMax(FunctionRefTest02::getTotal);
}

public static void getTotal(int a[]){
  int sum = 0;
  for (int i : a) {
    sum += i;
  }
  System.out.println("数组之和：" + sum);
}

public static void printMax(Consumer<int[]> consumer){
  int[] a = {10, 20, 30, 40};
  consumer.accept(a);
}
```

## 2. 方法引用的格式

符号表示：`::`

符号说明：双冒号为方法引用运算符，而它所在的表达式被称为方法引用

应用场景：如果Lambda表达式所要实现的方案，已经有其他方法存在相同的方案，那么则可以使用方法引用。

常见引用方式：

方法引用在 JDK8 中使用是相当灵活的，有以下几种形式：

1. `对象名::方法名`
2. `类名::静态方法名`
3. `类名::不同方法`
4. `类名::new 调用的构造器`
5. `String[]::new 调用数组的构造器`

### 2.1 对象名: :方法名

这是最常见的一种用法。如果一个类中已经存在一个

```java
public static void main(String[] args) {
  Date now = new Date();
  Supplier<Long> supplier = () -> now.getTime();
  // 通过 方法引用 的方式处理
  Supplier<Long> supplier1 = now::getTime;
  System.out.println(supplier1.get());
}
```

方法引用的注意事项

1. 被引用的方法，参数要和接口中的抽象方法的参数一样
2. 当接口抽象方法有返回值时，被引用的方法也必须有返回值

### 2.2 类名: :静态方法名

也是比较常用的方式

```java
public static void main(String[] args) {
  Supplier<Long> supplier = () -> System.currentTimeMillis();
  System.out.println(supplier.get());
  // 通过 方法引用 来实现
  Supplier<Long> supplier1 = System::currentTimeMillis;
  System.out.println(supplier1.get());
}
```

### 2.3 类名: :引用实例方法

Java 面向对象中，类名只能调用静态方法，类名引用实例方法是用前提的，实际上是拿第一个参数作为方法的调用者

```java
public static void main(String[] args) {
  Function<String, Integer> function = s -> s.length();
  System.out.println(function.apply("hello"));

  // 通过方法引用来实现
  Function<String, Integer> function1 = String::length;
  System.out.println(function1.apply("hellohello"));

  BiFunction<String, Integer, String> function2 = String::substring;
  String msg = function2.apply("helloworld", 3);
  System.out.println(msg);
}
```



### 2.4 类名: :构造器

由于构造器的名称和类名完全一致，所以构造器引用使用 `::new` 的格式使用

```java
public static void main(String[] args) {
  Supplier<Person> sup = ()->new Person();
  System.out.println(sup.get());
  // 通过方法引用来实现
  Supplier<Person> sup1 = Person::new;
  System.out.println(sup1.get());
  BiFunction<String, Integer, Person> function = Person::new;
  System.out.println(function.apply("张三", 23));
}
```

### 2.5 数组: :构造器

数组是怎么构造出来的？

```java
public static void main(String[] args) {
  Function<Integer, String[]> fun1 = len -> new String[len];
  String[] a1 = fun1.apply(3);
  System.out.println("数组的长度是："+a1.length);
  // 方法引用的方式调用数组的构造器
  Function<Integer, String[]> fun2 = String[]::new;
  String[] a2 = fun2.apply(5);
  System.out.println(a2.length);
}
```

小结：方法引用是对 Lambda 表达式符合特定情况的一种缩写方式，它使得我们的 Lambda 表达式更加的精简，也可以理解为 lambda 表达式的缩写形式，不过要注意的方法引用只能引用已经存在的方法。



# 四、Stream

当我们在需要对集合中的元素进行操作的时候，除了必需的添加，删除，获取外，最典型的就是遍历数组了

```java
public static void main(String[] args) {
  List<String> list = Arrays.asList("AAA","ABB","As","CC","DD");

  // 获取所有以 A 开头的数据
  List<String> list1 = new ArrayList<>();
  for (String s : list) {
    if(s.startsWith("A")){
      list1.add(s);
    }
  }

  List<String> list2 = new ArrayList<>();
  // 获取长度为 3 的数据
  for (String s : list1) {
    if (s.length() == 3) {
      list2.add(s);
    }
  }
  for (String s : list2) {
    System.out.println(s);
  }
}
```

以上代码可以看出有很多的循环，只要有了不同的需求，就需要进行一遍循环，是很繁琐的，因此，通过 JDK8 新特性的stream流可以解决这个问题。

Stream 更加优雅的代码解决方案

```java
public static void main(String[] args) {
  List<String> list = Arrays.asList("AAA","ABB","As","CC","DD");

  // 获取所有以 A 开头的数据
  // 获取长度为 3 的数据
  list.stream()
          .filter(s -> s.startsWith("A"))
          .filter(s -> s.length() == 3)
          .forEach(System.out::println);
}
```

以上代码含义：获取流，过滤“A”，过滤长度，逐一打印。代码相比于上面的案例更加简介直观



## 1. Stream流式思想概述

Stream流式思想类似于工厂车间的“生产流水线”，Stream流不是一种数据结构，不保存数据，而是对数据进行加工处理。Stream可以看作是流水线上的一个工序。在流水线上，通过多个工序让一个原材料加工成一个商品。

![image-20211011160711052](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211011160711052.png)

![image-20211011160727728](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211011160727728.png)

![image-20211011160738570](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211011160738570.png)

Stream API 可以让我们快速完成许多复制的操作，如筛选，切片，映射，查找，去重复，统计，匹配和归约



## 2. Stream流的获取方式

### 2.1 根据Collection获取

首先， java.util.Collection 接口中加入了 defatult 方法 Stream，也就是说 Collection 接口下的所有的实现都可以通过 stream 方法来获取 Stream 流。

```java
public static void main(String[] args) {
  List<String> list = Arrays.asList("AAA","ABB","As","CC","DD");
  list.stream();

  Set<String> set = new HashSet<>();
  set.stream();

  Vector vector = new Vector();
  vector.stream();
}
```

对于集合 Map 接口没有实现 Collection 接口，那么，我们可以根据 Map 获取对应的 key 和 value 的集合

### 2.2 通过 Stream 的 of 方法

在实际开发中我们不可避免的还是会操作到数组中的数据，由于数组对象不可能添加默认方法，所有Stream接口中提供了静态方法 of

```java
public static void main(String[] args) {
  Stream<String> a1 = Stream.of("a1", "a2", "a3");
  String[] arr1 = {"aa","bb","cc"};
  Stream<String> arr11 = Stream.of(arr1);
  Integer[] arr2 = {1,2,3,4};
  Stream<Integer> arr21 = Stream.of(arr2);
  arr21.forEach(System.out::println);

  // 注意：基本数据类型的数组是不会将其中的元素一个一个给打印出来的
  int[] arr3 = {1,2,3,4};
  Stream.of(arr3).forEach(System.out::println);
}
```

## 3. Stream 常用方法

### 3.1 forEach —— 终结方法

forEach 用来遍历流中的数据的

```java
void forEach(Consumer<? super T> action);
```

该方法接受一个 Consumer 接口，会将每一个流元素交给函数处理

```java
public static void main(String[] args) {
  Stream.of("aa","bb","cc","dd").forEach(System.out::println);
}
```

### 3.2 count —— 终结方法

Stream流中的 count 方法用来统计其中的元素个数的

```java
long count();
```

该方法返回一个 long 值，代表元素的个数

```java
long count = Stream.of("aa", "bb", "cc", "dd").count();
System.out.println(count);
```

### 3.3 filter

filter 方法的作用是用来过滤数据数据的。返回符合条件的数据。

![image-20211011171340994](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211011171340994.png)

可以通过filter方法将一个流转换成另一个子集流

```java
Stream<T> filter(Predicate<? super T> predicate);
```

该接口接收一个 Predicate 函数式接口参数作为筛选条件

```java
Stream.of("a1","a2","a3","s","sa","d","f","cc","xx")
        .filter(s -> s.contains("a"))
        .forEach(System.out::println);
```

输出：

a1
a2
a3
sa

### 3.4 limit

![image-20211011171927940](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211011171927940.png)

limit 方法可以对流进行截取处理，只取前 n 个数据

```java
Stream<T> limit(long maxSize);
```

参数是一个 long 类型的数值，如果集合当前长度大于参数就进行截取，否则不进行操作

```java
Stream.of("a1","a2","a3","s","sa","d","f","cc","xx")
        .limit(3)
        .forEach(System.out::println);
```

输出

```java
a1
a2
a3
```

### 3.5 skip

![image-20211011172252849](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211011172252849.png)

如果希望跳过前面几个元素，可以使用 skip 方法获取一个截取之后的新流

```java
Stream<T> skip(long n);
```

操作：

```java
Stream.of("a1","a2","a3","s","sa","d","f","cc","xx")
        .skip(3)
        .forEach(System.out::println);
```

输出：

```java
s
sa
d
f
cc
xx
```

### 3.6 map

如果我们需要将流中的元素映射到另一个流中，可以使用 map 方法

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

![image-20211011173934032](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211011173934032.png)

该接口需要一个Function函数式接口参数，可以将当前流中的 T 类型数据转换成另一种 R 类型的数据

```java
Stream.of("1","2","3","4","5","6","7")
        .map(Integer::parseInt)
        .forEach(System.out::println);
```

### 3.7 sorted

如果需要将数据排序，可以使用 sorted 这个方法

```java
Stream<T> sorted();
```

在使用的时候可以根据自然规则排序，也可以通过比较来指定对应的排序规则

```java
Stream.of("1","3","2","0","9","4","7")
        .map(Integer::parseInt)
        .sorted() // 根据数据日然顺序排序
        .sorted((o1,o2) -> 02 - o1)
        .forEach(System.out::println);
```

### 3.8 distinct

如果要去掉重复的数据，可以使用该方法

```java
Stream<T> distinct();
```

![image-20211011174430756](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211011174430756.png)

使用

```java
Stream.of("1","3","2","2","7","4","7")
        .map(Integer::parseInt)
        .sorted() // 根据数据日然顺序排序
        .sorted((o1,o2) -> 02 - o1)
        .distinct()
        .forEach(System.out::println);

Stream.of(
        new Person("张三",18),
        new Person("李四",22),
        new Person("张三",18)
).distinct()
        .forEach(System.out::println);
```

Stream 流中的 `distinct` 基本数据类型是可以直接去重的，但是自定义数据类型，我们需要重写 `hashCode` 和 `equals` 方法来进行去重



### 3.9 match

#### anyMatch、allMatch、noneMatch

anyMatch 表示判断的条件里，任意一个元素成功，返回 true

allMatch 表示判断里的元素，所有的都是，返回 true

noneMatch 跟 allMatch 相反，判断条件里的元素，所有的都不是，返回 true

```java
List<String> strs = Arrays.asList("a", "a", "a", "a", "b");
boolean aa = strs.stream().anyMatch(str -> str.equals("a"));
boolean bb = strs.stream().allMatch(str -> str.equals("a"));
boolean cc = strs.stream().noneMatch(str -> str.equals("a"));
long count = strs.stream().filter(str -> str.equals("a")).count();
System.out.println(aa);// TRUE
System.out.println(bb);// FALSE
System.out.println(cc);// FALSE
System.out.println(count);// 4
```



### 3.10 find

#### findFirst、findAny

findFirst：返回列表中的第一个元素。

findAny：返回的元素是不确定的，对于同一个列表多次调用 findAny() 有可能会返回不同的值。使用 findAny() 是为了更高效的性能。如果是数据较少，串行的情况下，一般会返回第一个结果，如果是并行的情况，那就不能确保是第一个。

```java
List<String> lst1 = Arrays.asList("Jhonny", "David", "Jack", "Duke", "Jill","Dany","Julia","Jenish","Divya");
List<String> lst2 = Arrays.asList("Jhonny", "David", "Jack", "Duke", "Jill","Dany","Julia","Jenish","Divya");
 
Optional<String> findFirst = lst1.parallelStream().filter(s -> s.startsWith("D")).findFirst();
Optional<String> fidnAny = lst2.parallelStream().filter(s -> s.startsWith("J")).findAny();
 
System.out.println(findFirst.get()); //总是打印出David
System.out.println(fidnAny.get()); //会随机地打印出Jack/Jill/Julia
```



### 3.11 max 和 min

#### max

```java
Optional<Person> max = Stream.of(
        new Person("aa", 77),
        new Person("bb", 66),
        new Person("cc", 55),
        new Person("dd", 44),
        new Person("eef", 33),
        new Person("ff", 22)
).max(Comparator.comparingInt(Person::getAge));
System.out.println(max);
max.ifPresent(s -> System.out.println(s.getAge()));

// 结果
Optional[Person{name='aa', age=77}]
77
```

#### min

```java
Optional<Person> max = Stream.of(
        new Person("aa", 77),
        new Person("bb", 66),
        new Person("cc", 55),
        new Person("dd", 44),
        new Person("eef", 33),
        new Person("ff", 22)
).min(Comparator.comparingInt(Person::getAge));
System.out.println(max);
max.ifPresent(s -> System.out.println(s.getAge()));

// 结果
Optional[Person{name='ff', age=22}]
22
```



### 3.12 reduce 方法

如果需要将所有数据归纳得到又给数据，可以使用 reduce 方法

```java
T reduce(T identity, BinaryOperator)
```

使用：

```java
public static void main(String[] args) {
  Integer sum = Stream.of(3,4,5,9)
          // identity 默认值
          // 第一次的时候会将默认值赋值给 x
          // 之后每次会将 上一次的操作结果赋值给x  y就是每次从数据中获取的元素
          .reduce(0, (x, y) -> {
            return x + y;
          });
  System.out.println(sum);

  // 获取最大值
  Integer max = Stream.of(4,5,3,9)
          .reduce(0, (x, y) -> {
            return x > y? x : y;
          });
  System.out.println(max);
}
```

### 3.13 map 和 reduce 的组合

```java
Integer sum = Stream.of(3,4,5,9)
        // identity 默认值
        // 第一次的时候会将默认值赋值给 x
        // 之后每次会将 上一次的操作结果赋值给x  y就是每次从数据中获取的元素
        .reduce(0, (x, y) -> x + y);
System.out.println(sum);

// 获取最大值
Integer max = Stream.of(4,5,3,9)
        .reduce(0, (x, y) -> x > y? x : y);
System.out.println(max);


Integer sumAge = Stream.of(
        new Person("aa", 22),
        new Person("bb", 32),
        new Person("cc", 12),
        new Person("dd", 56),
        new Person("ee", 43)
).map(Person::getAge)
        .reduce(0, Integer::sum);
System.out.println(sumAge);

// 求年龄的最大值
Integer maxAge = Stream.of(
        new Person("aa", 22),
        new Person("bb", 32),
        new Person("cc", 12),
        new Person("dd", 56),
        new Person("ee", 43)
).map(Person::getAge)
        .reduce(0, Math::max);
System.out.println(maxAge);

Integer count = Stream.of("a", "b", "c", "d").map(ch -> "a".equals(ch) ? 1 : 0)
        .reduce(0, Integer::sum);
System.out.println(count);
```

### 3.14 mapToInt

如果需要将 Stream 中的 Integer 类型转换成 int 类型，可以使用 mapToInt 方法来实现

![image-20211012090712356](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20211012090712356.png)

使用

```java
// Integer 占用的内存比 int 多很多，在 Stream 流操作中会自动装修和拆箱操作
Integer arr[] = {1,2,3,5,6,8};
Stream.of(arr)
        .filter(i -> i > 0)
        .forEach(System.out::println);
System.out.println("----------------");
// 为了提高程序代码的效率，我们可以先将流中的 Integer 数据转换为 int 数据，然后再操作
IntStream intStream = Stream.of(arr)
        .mapToInt(Integer::intValue);
intStream.filter(i -> i > 3)
        .forEach(System.out::println);
```



### 3.15 concat

如果有两个流希望合并成一个流，那么可以使用 Stream 接口的静态方法 concat

使用：

```java
Stream<String> stream1 = Stream.of("a","b","c");
Stream<String> stream2 = Stream.of("x","y","z");
// 通过 concat 方法将两个流合并为一个新的流
Stream.concat(stream1, stream2).forEach(System.out::println);
```



# 五、Stream 结果收集

## 5.1 收集到集合中

```java
public static void main(String[] args) {
  // 收集到 List 集合中去
  List<String> list = Stream.of("aa","bb","cc","aa")
          .collect(Collectors.toList());
  System.out.println(list);
  // 收集到 Set 集合中去
  Set<String> set = Stream.of("aa","bb","cc","aa")
          .collect(Collectors.toSet());
  System.out.println(set);
  ArrayList<String> arrayList = Stream.of("aa","bb","cc","aa")
          .collect(Collectors.toCollection(ArrayList::new));
  System.out.println(arrayList);
  HashSet<String> hashSet = Stream.of("aa","bb","cc","aa")
          .collect(Collectors.toCollection(HashSet::new));
  System.out.println(hashSet);
}
```

结果输出

```java
[aa, bb, cc, aa]
[aa, bb, cc]
[aa, bb, cc, aa]
[aa, bb, cc]
```



## 5.2 收集到数组中

Stream 中提供了 `toArray` 方法来讲结果放到一个数组中，返回值类型是 `Object[]`，如果我们要指定返回的类型，那么可以使用另一个重载的 `toArray(IntFunctioin f)` 方法

```java
Object[] objects = Stream.of("aa", "bb", "cc", "aa")
        .toArray();
System.out.println(Arrays.toString(objects));
String[] strings = Stream.of("aa", "bb", "cc", "aa")
        .toArray(String[]::new);
System.out.println(Arrays.toString(strings));
```

结果输出

```java
[aa, bb, cc, aa]
[aa, bb, cc, aa]
```



## 5.3 对流中的数据做聚合计算

当我们使用 Stream 流处理数据后，可以像数据库中共的聚合函数一样对某个字段进行操作，比如获得最大值，最小值，求和，平均值，统计数量

```java
// 求年龄最大的数据
Optional<Person> maxAge = Stream.of(
        new Person("aa", 77),
        new Person("bb", 66),
        new Person("cc", 55),
        new Person("dd", 44),
        new Person("eef", 33),
        new Person("ff", 22)
).max(Comparator.comparingInt(Person::getAge));
System.out.println(maxAge);

// 求年龄最小的数据
Optional<Person> minAge = Stream.of(
        new Person("aa", 77),
        new Person("bb", 66),
        new Person("cc", 55),
        new Person("dd", 44),
        new Person("eef", 33),
        new Person("ff", 22)
).min(Comparator.comparingInt(Person::getAge));
System.out.println(minAge);

// 统计处所有年龄的和
Integer sumAge = Stream.of(
        new Person("aa", 77),
        new Person("bb", 66),
        new Person("cc", 55),
        new Person("dd", 44),
        new Person("eef", 33),
        new Person("ff", 22)
).mapToInt(Person::getAge).sum();
System.out.println(sumAge);

// 统计出年龄的数量
long countAge = Stream.of(
        new Person("aa", 77),
        new Person("bb", 66),
        new Person("cc", 55),
        new Person("dd", 44),
        new Person("eef", 33),
        new Person("ff", 22)
).mapToInt(Person::getAge).count();
System.out.println(countAge);
// 统计出年龄大于50岁的人的数量
long countAgeUp50 = Stream.of(
        new Person("aa", 77),
        new Person("bb", 66),
        new Person("cc", 55),
        new Person("dd", 44),
        new Person("eef", 33),
        new Person("ff", 22)
).filter(p -> p.getAge() > 50)
        .mapToInt(Person::getAge).count();
System.out.println(countAgeUp50);

// 求出年龄的平均值、
OptionalDouble averageAge = Stream.of(
        new Person("aa", 77),
        new Person("bb", 66),
        new Person("cc", 55),
        new Person("dd", 44),
        new Person("eef", 33),
        new Person("ff", 22)
).mapToInt(Person::getAge).average();
System.out.println(averageAge);
```



## 5.4 对流中的数据做分组操作

当我们使用 Stream 流处理数据后，可以根据某个属性将数据分组

```java
// 根据姓名分组
Map<String, List<Person>> map1 = Stream.of(
        new Person("aa", 77),
        new Person("bb", 12),
        new Person("aa", 55),
        new Person("bb", 15),
        new Person("bb", 33),
        new Person("cc", 22)
).collect(Collectors.groupingBy(Person::getName));
map1.forEach((k, v)-> System.out.println("k = "+k+"\t"+"v = "+v));

// 根据年龄分组，超过 18 为成年，不满 18 岁是未成年人
Map<String, List<Person>> map2 = Stream.of(
        new Person("aa", 77),
        new Person("bb", 12),
        new Person("aa", 55),
        new Person("bb", 15),
        new Person("bb", 33),
        new Person("cc", 22)
).collect(Collectors.groupingBy(p -> p.getAge() > 18 ? "成年" : "未成年"));
map2.forEach((k, v) -> System.out.println("k = "+k+"\t"+"v = "+v));
```

**输出结果：**

```java
k = cc	v = [Person{name='cc', age=22}]
k = bb	v = [Person{name='bb', age=12}, Person{name='bb', age=15}, Person{name='bb', age=33}]
k = aa	v = [Person{name='aa', age=77}, Person{name='aa', age=55}]
k = 未成年	v = [Person{name='bb', age=12}, Person{name='bb', age=15}]
k = 成年	v = [Person{name='aa', age=77}, Person{name='aa', age=55}, Person{name='bb', age=33}, Person{name='cc', age=22}]
```



## 5.5 多级分组

现根据姓名分组，然后再根据年龄分成年和未成年

```java
// 根据姓名分组
Map<String, Map<String, List<Person>>> map = Stream.of(
        new Person("aa", 77),
        new Person("bb", 12),
        new Person("aa", 55),
        new Person("bb", 15),
        new Person("bb", 33),
        new Person("cc", 22)
).collect(Collectors.groupingBy(Person::getName, Collectors.groupingBy(p -> p.getAge() > 18 ? "成年" : "未成年")));

map.forEach((k, v) ->{
  System.out.println(k);
  v.forEach((k1,v1) -> {
    System.out.println("\t"+k1 + "="+v1);
  });
});
```

**输出结果：**

```java
cc
	成年=[Person{name='cc', age=22}]
bb
	未成年=[Person{name='bb', age=12}, Person{name='bb', age=15}]
	成年=[Person{name='bb', age=33}]
aa
	成年=[Person{name='aa', age=77}, Person{name='aa', age=55}]
```



