# Spring

## 1、Spring

### 1.1、简介

- 2002，首次推出了Spring框架的雏形，interface21 框架
- Spring 框架即以 interface21 框架为基础，经过重新设计，并不断丰富其内涵，于2004年3月24日发布了1.0正式版本。
- Rod Johnson，Spring Framework创始人，著名作者，悉尼大学博士，专业不是计算机而是音乐
- spring理念：使现有的技术更加容易使用，本身是一个大杂烩,整合了现有的技术框架！



- SSH：Strut2 + Spring + Hibernate!
- SSM：SpringMVC + Spring + Mybatis



### 1.2、优点

- Spring是一个开源的免费框架（容器）
- Spring 是一个轻量级的，非入侵式的框架
- 控制反转（IOC），面向切面编程（AOP)!
- 支持事物的处理，对框架整合的支持！

**总结一句话：Spring就是一个轻量级的控制反转（IOC）和面向切面编程（AOP）的框架！**



### 1.3、组成

![image-20201119154125389](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201119154125389.png)

### 1.4、拓展

在Spring 的官网有这个介绍，现代化的Java开发！说白就是基于Spring的开发！

![image-20201119154217874](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201119154217874.png)

- SpringBoot
  - 一个快速发开的脚手架
  - 基于SpringBoot可以快速开发单个微服务。
  - 约束大于配置！
- Spring Cloud
  - SpringCloud是基于SpringBoot实现的



因为现在大多数公司都在使用SpringBoot进行快速开发，学习SpringBoot的前题，需要完全掌握Spring及SpringMVC！承上启下的作用！

弊端：发展了太久之后，违背了原来的理念！配置十分繁琐，人称：”配置地狱！“



## 2、IOC理论推导

1. UserDao 接口
2. UserDaoImpl 实现类
3. UserService 业务接口
4. UserServiceImpl 业务实现类

在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改原代码！如果程序代码量十分大，修改一次的成本代价十分昂贵！

我们使用一个Set接口实现， 已经发生了革命性的变化！

```java
// 利用 set 进行动态实现值得注入！
public void setUserDao(UserDao userDao){
    this.userDao = userDao;
}
```

- 之前程序主动创建对象！控制权在程序猿手上！
- 使用了 set 注入后，程序不再具有主动性，而是编程了被动的接受对象！

这种思想，从本质上解决了问题，我们程序猿不用再去管理对象的创建了。系统的耦合性大大降低~，可以更加专注的在业务的实现上！这是IOC的原型！



## 3、hello spring

建立的 beans.xml 文件

```xml
<!--使用Spring来创建对象，在Spring这些都称为Bean
类型  变量名 = new 类型();
Hello hello = new Hello();
id = 变量名
class = new 的对象
property相当于给对象中的属性
	name: 属性名
	value: 属性值
-->
<bean id="hello" class="com.kuang.pojo.Hello">
    <property name="str" value="Spring"></property>
</bean>
```

**hello 是由谁来创建的？**

hello 对象是由 Spring 创建的

**Hello 对象的属性是怎么设置的？**

hello 对象的属性是由 spring 容器设置的，

这个过程就叫做反转：

**==控制：==** 谁来控制对象的创建，传统应用程序的对象是由程序本身控制创建的，使用 Spring 后，对象是由 Spring 来创建的

反转： 程序本身不创建对象，而变成被动的接收对象

依赖注入：就是利用 set 方法来进行注入的

IOC 是一种编程思想，由主动的编程编程被动的接收

可以通过 new ClassPathXmlApplicationContext 浏览源码

**到目前为止，我们彻底不用再去修改程序了，要实现不同的操作，只需要在 xml 配置文件中进行修改，所谓的 IOC，一句话：对象由 Spring 来创建，管理，装配**



```xml
<bean id="mysqlImpl" class="com.kuang.dao.UserDaoMysql">

</bean>
<bean id="userServiceImpl" class="com.kuang.service.UserServiceImpl">
    <!--
        ref: 引入Spring容器中建好的对象
        value：具体的值，基本数据类型！
    -->
    <property name="userDao" ref="mysqlImpl"></property>
</bean>
```



## 4、IOC创建对象的方式

1. 使用无参构造创建对象，默认！

2. 假设我们要使用有参构造创建对象

   1. 下标赋

   ```xml
   <!--第一种 —— 下标赋值-->
   <bean id="user" class="com.kuang.pojo.User">
        <constructor-arg index="0" value="狂神说java" />
   </bean>
   ```

   2. 类型

   ```xml
   <!--第二种方式-->
   <bean id="user" class="com.kuang.pojo.User">
       <constructor-arg type="java.lang.String" value="qinjiang"></constructor-arg>
   </bean>
   ```

   3. 参数名

   ```xml
   <!--第三种，直接通过参数名来设置-->
   <bean id="user" class="com.kuang.pojo.User">
       <constructor-arg name="name" value="AD钙"></constructor-arg>
   </bean>
   ```

总结：在配置文件加载的时候，容器中管理的对象就已经初始化了！



## 5、Spring配置

### 5.1、别名

```xml
<!--别名，如果添加了别名，我们也可以使用别名获取这个对象-->
<alias name="user" alias="userNew"></alias>
```

### 5.2、配置

```xml
<!--
    id: bean 的唯一标识符，也就是相当于我们学的对象名
    clss：bean 对象所对应的全限定名：包名 + 类型
    name: 也是别名, 而且那么可以同时去多个别名
-->
<bean id="userT" class="com.kuang.pojo.UserT" name="user2, u2" scope="">
    <property name="name" value="请开始"></property>
</bean>
```

### 5.3、import

这个 import ，一般用于团队开发使用，他可以将多个配置文件，导入合并为一个

假设，现在项目中有多个人开发，这三个复制不同类的开发，不同的类需要注册不同的bean中，我们可以利用import 将所有人的 beans.xml 合并为一个总的！

- 张三
- 李四
- 王五
- applicationContext.xml

使用的时候，直接使用总的配置就可以了

## 6、依赖注入==【重点】==



### 6.1、构造器注入



```xml
<!--第三种，直接通过参数名来设置-->
<bean id="user" class="com.kuang.pojo.User">
    <constructor-arg name="name" value="AD钙"></constructor-arg>
</bean>
```



### 6.2、Set方式注入



依赖：bean 对象的创建依赖于容器



【环境搭建】

1. 复杂类型

```java
public class Address {
    String address;
    public String getAddress(){
        return address;
    }
    public void setAddress(String address){
        this.address = address;
    }
}
```

2. 真实测试对象

```java
public class Student {
    private String name;
    private Address address;
    private String books;
    private List<String> hobby;
    private Map<String, String> card;
    private Set<String> games;
    private String wife;
    private Properties Info;
}
```

3. beans.xml

```xml
<bean id="student" class="com.kuang.pojo.Student">
    <!--第一种，普通值注入-->
    <property name="name" value="AD钙"></property>
</bean>
```

4. 测试类

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        Student student = (Student) context.getBean("student");
        System.out.println(student.getName());
    }
}
```

完善注入信息

```xml
<bean id="address" class="com.kuang.pojo.Address">
    <property name="address" value="西安"></property>
</bean>

<bean id="student" class="com.kuang.pojo.Student">
    <!--第一种，普通值注入-->
    <property name="name" value="AD钙"></property>
    <!--第二种，bean注入，ref-->
    <property name="address" ref="address"></property>
    <!--数组注入，ref-->
    <property name="books">
        <array>
            <value>红楼梦</value>
            <value>西游记</value>
            <value>水浒传</value>
            <value>三国演义</value>
        </array>
    </property>

    <!--List-->
    <property name="hobby">
        <list>
            <value>听歌</value>
            <value>吃饭</value>
            <value>看电影</value>
        </list>
    </property>
    <!--Map-->
    <property name="card">
        <map>
            <entry key="身份证" value="111111111111" />
            <entry key="学生卡" value="222222222222"></entry>
        </map>
    </property>
    <!--set-->
    <property name="games">
        <set>
            <value>LOL</value>
            <value>COC</value>
            <value>BOB</value>
        </set>
    </property>
    <!--null-->
    <property name="wife">
        <null></null>
    </property>
    <!--properties-->
    <property name="info">
        <props>
            <prop key="学号">202202313</prop>
            <prop key="性别">男</prop>
            <prop key="姓名">李四</prop>
        </props>
    </property>

</bean>
```



### 6.3、拓展方式注入

我么可以使用p命名空间和c命名空间进行注入

官方解释：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd ">

    <!--p命名空间注入，可以直接注入属性的值 property-->
    <bean id="user" class="com.kuang.pojo.User" p:name="AD钙" p:age="18"></bean>

    <!--c命名空间，通过构造器注入：construct-args-->
    <bean id="user2" class="com.kuang.pojo.User" c:age="18" c:name="哈哈哈"></bean>

</beans>
```

测试：

```java
@Test
public void test2(){
    ApplicationContext context = new ClassPathXmlApplicationContext("userBeans.xml");
    User user = context.getBean("user2", User.class);
    System.out.println(user);
}
```

注意点：p命名和c命名空间不能直接使用，需要导入xml约束！

```xml
xmlns:p="http://www.springframework.org/schema/p"
xmlns:c="http://www.springframework.org/schema/c"
```



### 6.4、bean的作用域

1. 单例模式（Spring 默认机制）

```xml
<bean id="user2" class="com.kuang.pojo.User" c:age="18" c:name="哈哈哈" scope="singleton"></bean>
```

2. 原型模式：每次从容其中get的时候，都会产生一个新对象！

```xml
<bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"></bean>
```

3. 其余的request，session，aplication，这些个只能在web开发中使用到！



## 7、bean的自动装配

- 自动装配是Spring满足bean依赖一种方式
- Spring会在上下文中自动寻找，并自动给bean装配属性！

在 Spring 中有三种装配的方式

1. 在xml中显示配置
2. 在Java中显示配置
3. 隐式的自动装配bean【重要】

### 7.1、测试

1. 环境搭建
   1. 一个人有两个宠物

### 7.2、ByName自动装配

```xml
<!--
byName:会自动在容器上下文中查找，和自己对象set方法后面的值的对应的 beanid
byType: 会自动在容器上下文中查找，和自己对象属性累心相同的bean！
-->
<bean id="people" class="com.kuang.pojo.People" autowire="byName">
	<property name="name" value="AD钙"></property>
</bean>
```

### 7.3、ByType自动装配

```xml
<!--
    byName:会自动在容器上下文中查找，和自己对象set方法后面的值的对应的 beanid
    byType: 会自动在容器上下文中查找，和自己对象属性类型相同的bean！
-->
<bean id="people" class="com.kuang.pojo.People" autowire="byType">
	<property name="name" value="AD钙"></property>
</bean>
```

小节：

- byname的时候，需要保证bean的id唯一，并且bean需要和自动注入的属性的set方法的值一致
- bytype的时候，需要保证所有的bean的calss唯一，并且这个bean和自动注入的属性类型一样

### 7.4、使用注解实现自动装配

jdk1.5支持的注解，Spring就支持注解了！

要使用注解须知：

1. 导入约束。context约束
2. 配置注解的支持：**<context:annotation-config/>**【重要】

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

@Autowird

直接在属性上使用即可！也可以在set方式上使用！

使用Autowired我们可以不用编写set方法了，前提是你这个自动装配的属性在 （Spring） 容器中存在，且符合名字byName！



@Autowired 和 @Resource 区别：

都是用来自动装配，都可以放在属性字段上

@Autowired 通过 byType 的方式实现

@Resource 默认通过 byname 的方式实现，如果找不到名字，则通过 byType 实现！如果两个都找不到的情况下，报错！





## 8、使用注解开发

在Spring4之后使用注解开发，必须要保证AOP的包导入了

![image-20201120111346614](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201120111346614.png)

使用注解需要导入context约束，增加注解的支持！

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
</beans>
```



1. bean
2. 属性如何注解

```java
// 等价于<bean id="user" class="com.kuang.pojo.User"/>
//@Component 组件
@Component
public class User {
    // 相当于 <property name="name" value="ADDDDD">
    @Value("ADDDDD")
    public String name;
}
```

3. 衍生的注解

   @Component 有几个衍生注解，我们在web开发中，会按照三层架构分层！

   - dao【@Repository】
   - service【@Service】
   - controller 【@Controller】

   这四个注解功能都是一样的，都是代表将某个类注册到Spring中，装配Bean

4. 自动装配置

```
- @Autowired: 自动装配通过类型。名字
  - 如果Autowired不能唯一自动装配属性，则需要@Qualifier(value="xxx")
- @Nullable: 字段标记了这个注解，说明这个字段可以为null
- @Resource: 自动装配通过名字。类型
```

5. 作用域

```java
// 等价于<bean id="user" class="com.kuang.pojo.User"/>
//@Component 组件
@Component
public class User {
    // 相当于 <property name="name" value="ADDDDD">
    @Value("ADDDDD")
    public String name;
}
```

6. 小结

xml 于注解：

- xml 更加万能，适用于任何场合！维护简单方便
- 注解不是自己类使用不了，维护相对复杂

xml 与 注解最佳实践：

- xml 用来管理bean；
- 注解只负责完成属性的注入
- 我们在使用的过程中，只需要注意一个问题，必须让注解生效，就必须开启注解的支持

```xml
<context:component-scan base-package="com.kuang.pojo"></context:component-scan>
<context:annotation-config/>
```



## 9、使用Java的方式配置Spring

我们现在要完全不使用Spring的xml配置了，全权交给java来做！

javaConfig是Spring的一个子项目，在Spring4之后，它成为了一个核心功能！



## 10、代理模式

为什么要学习代理模式？因为这就是SpringAOP的底层！【SpringAOP和SpringMVC】

代理模式的分类：

- 静态代理
- 动态代理

![image-20201120170350578](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201120170350578.png)

### 10.1、静态代理

角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决
- 真是角色：被代理的角色
- 代理角色：代理真是角色，代理真是角色后，我们一般会做一些附属操作
- 客户：访问代理对象的人！



代码步骤：

1. 接口

```java
public interface Rent {
    public void rent();
}
```

2. 真是角色

```java
//房东
public class Host implements Rent {
    public void rent() {
        System.out.println("房东要出租房子");
    }
}
```

3. 代理角色

```java
public class Proxy {
    private Host host;
    public Proxy(){}
    public Proxy(Host host){
        this.host = host;
    }

    public void rent(){
        host.rent();
        seeHouse();
        heTong();
        fare();
    }

    //看房
    public void seeHouse(){
        System.out.println("中介带你看房子");
    }

    // 合同
    public void heTong(){
        System.out.println("签合同");
    }

    // 收中介费
    public void fare(){
        System.out.println("收中介费");
    }
}
```

4. 客户端访问角色

```java
public class Client {
    public static void main(String[] args) {
        // 房东要租房子
        Host host = new Host();
        // 代理，中介帮你组房子，但是呢？代理角色一般会有一些附属操作

        Proxy proxy = new Proxy(host);
        // 你不用面对房东，直接找中介即可
        proxy.rent();
    }
}
```



代理模式的好处：

- 可以使真实角色的操作更加存粹！不用去关注一些公共的业务
- 公共也就是交给代理角色！实现了业务的分工
- 公共业务发生扩展的时候，方面集中管理！

缺点：

- 一个真实角色就会产生一个代理角色；代码量会翻倍，发发效率会变低

### 10.2、加深理解

代码：对应 08-demo02;

聊聊AOP

![image-20201120172944048](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201120172944048.png)



### 10.3、动态代理

- 动态代理和静态代理角色一样
- 动态代理的代理是动态生成的，不是我们直接写好的！
- 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  - 基于接口 --- JDK动态代理【我们在这里使用】
  - 基于类：cglib
  - Java子节码实现：javasist

需要了解两个类：proxy：代理，InvocationHandler：调用处理程序



动态代理的好处：

- 可以使真实角色的操作更加存粹！不用去关注一些公共的业务
- 公共也就是交给代理角色！实现了业务的分工
- 公共业务发生扩展的时候，方面集中管理！
- 一个动态代理类代理的是一个接口，一般就是对应的一类业务
- 一个动态代理类可以代理多个类，只要是实现了同一个接口即可！



## 11、AOP

### 11.3、什么是AOP

AOP(Aspect Oriented Pargraming)意为：面向切面编程，通过预编译方式和运行期动态代理实现功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架的一个重要内容，是函数式编程的一种衍生泛型，利用AOP可以对有我逻辑的各个部分进行隔离，从而使得业务逻辑部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

![image-20201122085102272](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201122085102272.png)

### 11.2、AOP在spring中的作用

提供声明式事务；允许用户自定义切面

- 横切关注点：跨越应用程序多个模块的方法或功能。即是，与我们业务逻辑无关的，但是我们需要关注的部分，就是横切关注点。如日志，安全，缓存。，，，，
- 切面（ASPECT）：横切关注点被模块化的特殊对象，即，它是一个类。
- 通知（Advice）：切面必须要完成的工作，即，它是类中的一个方法。
- 目标（Target）：被通知对象。
- 代理（Proxy）：向目标对象应用通知之后创建的对象
- 切入点（PointCut）：切面通知执行的“地点”的定义
- 连接点（JointPoint）：与切入点匹配的执行点

![image-20201122085534268](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201122085534268.png)

### 11.3、使用Spring实现Aop

【重点】使用AOP植入，需要导入一个依赖包！

```xml
<dependencies>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.9.4</version>
    </dependency>
</dependencies>
```



方式一：使用Spring的API接口【主要SpringAPI接口实现】

方式二：自定义来实现AOP【主要是切面定义】

方式三：使用注解实现！



## 12、整合Mybatis

步骤：

1. 导入相关jar包
   1. junit
   2. mybatis
   3. mysql数据库
   4. spring相关的
   5. aop植入
   6. mybatis-spring【new】
2. 编写配置文件
3. 测试

### 12.1、回忆mybatis



## 13、声明式事务

### 1、回顾事务

- 要么都成功，要么都失败
- 事务在项目开发中，十分的重要，涉及到数据的一致性问题，不能马虎！

- 确保完整性和一致性；

事务ACID原则：

- 原子性
- 一致性
- 隔离性
  - 多个业务可能操作同一个资源，防止数据损坏
- 持久性
  - 事务一旦





















































































