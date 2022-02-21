## SpringMVC

SSM：mybatis + Spring + SpringMVC **MVC三层架构**

JavaSE：认真学习，老师带，入门快

JavaWeb：认真学习，老师带，入门快

SSM框架：研究官方文档，锻炼自学能力，锻炼笔记能力，锻炼项目能力



SpringMVC + Vue + SpringBoot + SpringCloud + linux	还需要再学习



SSM：IOC 和 AOP

SpringMVC：SpringMVC的执行流程！

SpringMVC：SSM框架整合！



Spring 



MVC: 模型（dao，service）  视图（jsp）  控制器（Servlet）



dao

service

servlet：转发，重定向

jsp/html



前端   数据传输   实体类

实体类：用户名，密码，生日，爱好，.....

前端：用户名    密码

pojo：User

vo：UserVo

dto：



JSP：本质就是一个Servlet



假设：

你的项目的架构，是设计好的，还是演进的？

- ALibaba    PHP
- 随着用户量越来越大，Java
- 王坚    IOE



## SpringMVC 01

新建SpringMVC项目删除掉src目录

导入依赖

```xml
<!--导入依赖-->
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.1.9.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>servlet-api</artifactId>
        <version>2.5</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>jsp-api</artifactId>
        <version>2.2</version>
    </dependency>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>
</dependencies>
```



### 01 添加一个模块 springmvc-01-servlet

将当前的模块变成web形式，

右键：

![image-20201123153237499](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201123153237499.png)

选中Web  Application

![](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201123153336065.png)



## 什么是SpringMVC

Spring MVC是基于Java实现MVC的轻量级Web框架。

### 为什么始学习SpringMVC？

Spring MVC特点：

1. 轻量级，简单易学
2. 高校，基于请求响应的MVC框架
3. 与Spring兼容性好，无缝结合
4. 约定优于配置
5. 功能强大
6. 简介灵活



### SpringMVC 开发的流程图

![image-20201123164554522](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201123164554522.png)



**虚线是需要自己手写，其他的springmvc自动给出**





##  Controller

### 控制器 Controller

- 控制器复杂提供访问应用程序的行为，通常通过接口定义活注解定义两种方法实现。
- 控制器负责解析用户的请求并将其转换为一个模型
- 在Spring MVC中一个控制器类可以包含多个方法
- 在Spring MVC中，对于Controller的配置方式有很多种

**推荐使用注解方式进行操作**

- @Controller注解类型用于声明Spring类的实例使一个控制器
- Spring可以使用扫描机制来找到程序中所有基于注解的控制器类，为了保证Spring能找到你的控制器，需要在配置文件中声明组件扫描

```
@Component	组件
@Service	service
@Controller	controller
@Respository
```



## RestFul 风格

```java
@RequestMapping("/add/{a}/{b}")
public String test1(@PathVariable int a, @PathVariable int b, Model model){
    int res = a + b;
    model.addAttribute("mas", "结果为"+res);
    return "test";
}
```



## 解决乱码问题 web.xml配置

![image-20201124093748460](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201124093748460.png)





































































































