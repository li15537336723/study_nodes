# Lambda 表达式



Lambda 表达式基础语法：Java8 种引入了一个新的操作符 ”->“，该操作符称为箭头或者 Lambda 操作符，箭头操作符将 Lambda 表达式拆分成两部门

左侧：lambda 表达式的参数列表

右侧：lambda 表达式所需执行的功能，即 lambda 体



**语法格式一：无参数，无返回值**
`() -> System.out.println("hello world!!!")`

**语法格式二：有一个参数，并且无返回值**

`(x) -> System.out.println(x)`

**语法格式三：只有一个参数，小括号可以省略不写**

`x -> System.out.println(x)`

**语法格式四：有两个以上参数，有返回值，并且 Lambda 体中有多条语句**

```java
Comparator<Integer> com = (x, y)->{
    System.out.println("函数接口");
    return Integer.compare(x,y);
}
```



## 四大内置核心接口



### Consumer\<T>:消费性接口

`void accept(T t)`



```java
public void happy(double money, Consumer<Double> consumer){
    consumer.accept(money)
}
public void test(){
    happy(1000, (money) ->System.out.println("一共消费" + money))
}
```



### Supplier\<T>: 供给型接口

`T get()`

```java
public List<Integer> sup(int num, Supplier<Integer> sup){
    List<Integer> list = new ArrayList<>();
    for(int i = 0; i < num; i++){
        int n = sup.get();
        list.add(n);
    }
    return list;
}

public void test(){
    List<Integer> list = sup(10, ()->(int)(Math.random()*100));
    for(Integer l : list){
        System.out.println(l)
    }
}
```



### Function\<T,R>:函数型接口

`R apply(T t)`

```java
public String handle(String str, Function<String, String> fun){
    return fun.apply(str)
}
public void test(){
    String s = handle("\t\t", (str) -> str.trim());
    System.out.println(s);
    String s1 = handle("nihaonihao", str -> str.substring(2,4));
    System.out.println(s1);
}
```



### Predicate\<T>:断言型接口

`boolean test(T t)`

```java
public List<String> filter(List<String> list, Predicate<String> pre){
    List<String> strlist = new ArrayList<String>();
    for(String s : list){
        if(pre.test(s)){
            strlist.add(s);
        }
    }
    return strlist;
}
public void test(){
    List<String>() list = new Arrays.asList("hello","world","ni","hao");
    List<String>() newList = filter(list, (str) -> str.length() > 3);
    for(List l : newList){
        System.out.println(l);
    }
}
```















