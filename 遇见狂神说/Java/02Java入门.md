

# 01：Java帝国的诞生

- 语法有点像C语言
- 没有指针
- 没有内存管理
- 真正的可移植性，编写一次，到处运行
- 面向对象
- 类型安全
- 高质量类库

Java 2 标准版（J2SE）:去占领桌面

Java 2 移动版（J2ME）:去占领手机

Java 2 企业版（J2EE）:去占领服务器



- 构建工具：Ant，Maven，Jekins
- 应用服务器：Tomcat，，，，
- Web开发：Struts，Spring，mybatis
- 开发工具：Eclipse，idea

# 02：Java的优势和特性

- 简单性
- 面向对象
- 可移植性
- 高性能
- 分布式
- 动态性
- 多线程
- 安全性
- 健壮性



# 03：Java三大版本

JavaSE：标准版（桌面程序，控制台开发）

JavaME：嵌入式开发（手机，小家电）

JavaEE：E企业级开发（web端，服务器开发）



# 04：JDK、JRE、JVM

JDK：Java Development Kit

JRE：Java Runtime Enviroment

JVM：JAVA Virtual Machine

![image-20201110172129485](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201110172129485.png)



# 05：hello world详解

1. 随便新建一个文件夹，存放代码
2. 新建一个Java文件
   1. 文件后缀名为  .java 
   2. Hello.java
   3. 【注意点】系统可能没有显示文件后缀名，我们需要手动打开

```java
public class Hello{
    public static void main(String[] args){
        System.out.println("hello world!");
    }
}
```

4. 编译 javac java 文件，会生成一个class文件
5. 运行class文件，java class文件

![image-20201110173633321](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201110173633321.png)



## 可能遇到的情况

1. 每个单词大小不能出现问题，**Java是大小写敏感的**

2. 尽量使用英文
3. 文件名和类名必须保证一致，并且首字母大写
4. 符号使用了中文











































