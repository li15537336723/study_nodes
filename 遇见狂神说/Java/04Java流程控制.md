# 01：用户交互Scanner

**Scanner对象**

- 之前我们学的基本语法中我们没有实现程序和人的交互，但是Java给我们提供了一个工具类，我们可以获取用户的输入。Java.util.Scanner是Java5的新特性，**我们可以通过Scanner 类来获取用户的输入。**

- 基本语法：

  ```java
  Scanner s = new Scanner(System.in);
  ```

- 通过Scanner 类的next() 与 nextLine() 方法获取输入的字符串，在读取前我们一班需要使用hasNext() 与 hasNextLine() 判断是否还有输入的数据。



# 02：if选择结构

```java
public static void main(String[] args){
    Scanner scanner = new Scanner(System.in);
    String s = scanner.nextLine();
    if(s.equals("hello")){
        System.out.println(s);
    }
    System.out.println("end");
    scanner.close();
}
```

# 03：switch多选择结构

多选择结构还有一一个实现方式就是 **switch case** 语句 

**switch case** 语句判断一个变量与一系列值中摸个值是否相等，每个值成为分支

### 结构

![image-20201111105730300](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201111105730300.png)

switch 在JDK7之后支持了字符串的比较

```java
String name = "hello";
switch(name){			// 括号中的为表达式
    case "AD钙":
        System.out.print("AD钙");
        break;
    case "hello":
        System.out.print("hello");
        break;
    default:
        System.out.print("弄啥类");
}
```

# 04：循环结构

### while 循环

```java
while(布尔表达式){
    // 循环内容
}
```

- 只要布尔表达式为 true，循环就会一致执行下去
- **我们大多数情况是会让循环停止下来，我们需要一个让表达式失效的方式结束循环。**
- 循环条件一直为true就会造成无限循环【死循环】



### do...while

- 对于 while 语言而言，如果不满足条件，则不能进入循环，但有时候我们需要即时不满足条件，也至少执行一次
- do...while 循环和 while 循环相似，不同的是，do...while 循环至少会执行一次

```java
do{
    // 代码体
}while(布尔表达式);
```

### while 和 do...while 的区别

- while 先判断在执行，do... while是先执行后判断！
- do...while 总是保证循环体会白至少执行一次！这就是它们的主要差别



### for 循环

```java
for(初始化;布尔表达式;更新){
    // 代码语句
}
```

- for 循环语句是支持迭代的一种通用结构，**是最有效，最灵活的循环结构**



### 增强 for 循环

- 这里我们只是先见一面，了解，之后数组重点使用

- Java5 引入了一种主要用于数组或集合的增强型 for 循环

- Java 增强 for 循环语格式如下

  ```java
  for(声明语句 : 表达式){
      // 代码句子
  }
  ```

































































