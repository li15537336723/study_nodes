# 01：注释

- 平时我们编写代码，在代码量比较少的时候，我们还可以看懂自己写，但是当项目结构一旦复杂起来我们就需要用到注释了

- 注释并不会被执行，是给我们写代码的人看的
- 书写注释是一个非常好的习惯



Java中的注释有三种：

- 单行注释
- 多行注释
- 文档注释



# 02：标识符 关键字

Java 所有的组成部分都需要名字。类名变量名以及方法名都被成为标识符

![image-20201110234003761](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201110234003761.png)

- 所有的标识符都应该以字母（A-Z或者a-z），美元符（$），或者下划线（_）开始

- 首字符之后可以是字母字母（A-Z或者a-z），美元符（$），或者下划线（_）或数字的任何字符组合
- **不能使用关键字作为变量名或则方法名**
- 标识符是**大小写敏感**的



# 03：数据类型

- **强类型语言**
  - 要求变量的使用要严格符合规定，所有变量都必须先定以后才可以使用

- **Java种的数据类型分为两类**
  - 基本类型：四类八种
  - 引用类型：类，接口，数组



float 有限    离散     舍入误差        大约       接近但不等于



# 04：类型转换

由于Java是强类型语言，所以要进行有些运算的时候的，需要用到类型转换

 低————————————》高

byte, short, int, long, float, double

运算中，不同类型的数据先转化为同一类型然后进行运算



强制类型转换：高到低的转换，可能会存在内存溢出的问题，或精度问题

自动类型转换：



# 05：变量，常量，作用域

- 变量是什么，就是可以变化的量！
- Java是一种强类型语言，每个变量都必须声明其类型
- Java变量是程序最基本的储存单元，其要素包括变量名，变量类型和作用域
- 注意事项：
  - 每个变量都有类型，类型可以是基本类型，也可以是引用类型
  - 变量名必须是合法的标识符
  - 变量声明是一条完整的语句，因此每一个声明都必须以分号结束

**局部变量：**在方法内，必须声明和初始化

**实例变量：**在方法外面，从属于对象，如果不初始化，就会变为默认值，布尔值默认为 false

## 变量名的命名规范

- 所有变量，方法，类型：**见名知意**

- 类成员变量：首字母小写和驼峰原则：monthSalary 除了第一个单词以外，后面的单词首字母大写 lastName

- 局部变量：首字母小写和驼峰原则
- 常量：大写字母和下划线：MAX_VALUE
- 类名：首字母大写和驼峰原则：Man，GoodMan
- 方法名：首字母小写和驼峰原则：run(), runRun()



# 基本运算符

![image-20201111092424350](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201111092424350.png)

二元运算符

**ctrl+D：复制当前行到下一行**



幂运算

```java
double pow = Math.pow(3,3);
System.out.println(pow);
// 结果： 9.0 
```



```java
// 字符串连接符：
System.out.print(""+ 10+20);	// 1020
System.out.print(10+20+""); // 30
```





























