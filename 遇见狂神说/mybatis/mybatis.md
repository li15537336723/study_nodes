# mybatis

环境：

- JDK1.8
- Mysql 5.7
- maven 3.6.1
- IDEA

回顾：

- JDBC
- Mysql
- Java基础
- Maven
- Junit



SSM框架：配置文件的。最好的方式：看官网



## 1. 简介

### 1.1、什么是mybatis

![image-20201114114612634](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201114114612634.png)

- MyBatis 是一款优秀的**持久层框架**，
- 它支持自定义 SQL、存储过程以及高级映射。
- MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
- MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。



如何获取mybtis？

- maven仓库：

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.6</version>
</dependency>
```

### 1.2、持久层

数据持久化

- 持久化就是讲程序的数据在持久状态和瞬时状态化的过程
- 内存：**断电即失**
- 数据库（Jdbc），io文件持久化
- 生活：冷藏，罐头

**为什么需要持久化？**

- 有一些对象不能让他丢掉
- 内存太贵了



### 1.3、持久层

Dao层，Service层，Controller层...

- 完成持久化工作的代码块
- 层界限十分明显



### 1.4 问什么需要Mybatis？

- 帮助程序员将数据存入到数据库中
- 方便
- 传统的JDBC代码太复杂。简化，框架，自动化
- 不用Mybatis也可以，更容易上手。**技术没有高低之分**

- 优点：
  - 简单易学
  - 灵活
  - sql和代码的分离，提高了可维护性
  - 提供映射标签，支持对象与数据库的orm字段关系映射
  - 提供对象关系映射，支持对象关系组件维护
  - 提供xml标签，支持编写动态sql

**最重要的一点：使用的人多**

Spring  SpringMVC  SpringBoot



## 2、第一个Mybatis程序

思路：搭建环境 --> 导入Mybatis --> 编写代码 --> 测试！

### 2.1 搭建环境

搭建数据库

```mysql
USE `mybatis`

CREATE TABLE `user`(
	`id` INT(20) NOT NULL PRIMARY KEY,
	`name` VARCHAR (30) DEFAULT NULL,
	`pwd` VARCHAR (30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `user` (`id`,`name`,`pwd`) VALUES
(1, '狂神','132'),
(2, '李四','456'),
(3, '张三','879')
```

新建项目

1. 新建一个普通的maven项目
2. 删除src目录

3. 导入maven依赖

```xml
<dependencies>
    <!--mysql驱动-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.47</version>
    </dependency>
    <!--mybatis-->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.4.6</version>
    </dependency>
    <!--junit-->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
    </dependency>
</dependencies>
```

### 2.2 创建一个模块

- 编写mybatis核心配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<!--核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=&amp;characterEncoding=UFT-8"/>
                <property name="username" value="root"/>
                <property name="password" value="li12345"/>
            </dataSource>
        </environment>
    </environments>

</configuration>
```

- 编写mybatis工具类

```java
public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    static{
        try {
            // 使用mybatis获取sqlSesionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```

### 2.3 编写代码

- 实体类

```java
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }
}
```

- Dao接口

  ```java
  public interface UserDao {
      List<User> getUserList();
  }
  ```

- 实现类，由原来的UserDaoImpl转变为一个Mapper配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.kuang.dao.UserDao">
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from mybatis.user
    </select>
</mapper>
```

### 2.4 测试

org.apache.ibatis.binding.BindingException: Type interface com.kuang.dao.UserDao is not known to the MapperRegistry.

**MapperRegistry是什么？**

核心配置文件中注册 mappers

- junit测试

```java
@Test
public void test(){
    // 获取SqlSession对象
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserDao userDao = sqlSession.getMapper(UserDao.class);
    List<User> userList = userDao.getUserList();
    for (User user : userList) {
        System.out.println();
    }
    sqlSession.close();
}
```



可能会遇到的问题：

1. 配置文件没有注册
2. 绑定接口错误。
3. 方法名不对
4. 返回类型不对
5. Maven到处资源问题



## 3 CRUD

### 1、namespace

namespace中的包名要和 Dao/mapper 接口的包名一致

### 2、select，delete，update，add

选择、查询语句：

- id：就是对应的namespace中的方法
- resultType：Sql语句执行的返回值！
- parameterType：参数类型

```java
public interface UserMapper {
    // 查询全部用户
    List<User> getUserList();
    // 根据id查询用户
    User getUserById(int id);
    // insert 语句
    int addUser(User user);
    // 修改用户
    int updateUser(User user);
    // 删除一个用户
    int deleteUser(int id);
}
```

```xml
<mapper namespace="com.kuang.dao.UserMapper">
    <select id="getUserList" resultType="com.kuang.pojo.User">
        select * from mybatis.user
    </select>
    
    <select id="getUserById" parameterType="int" resultType="com.kuang.pojo.User">
        select * from mybatis.user where id = #{id}
    </select>

    <insert id="addUser" parameterType="com.kuang.pojo.User">
        insert into mybatis.user (id, name, pwd) values (#{id}, #{name}, #{pwd});
    </insert>

    <update id="updateUser" parameterType="com.kuang.pojo.User">
        update mybatis.user set name = #{name},pwd = #{pwd} where id = #{id}
    </update>

    <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id = #{id}
    </delete>
</mapper>
```

注意点：

- 增删改需要提交事务！

### 3、分析错误

- 标签不要匹配错误
- resource 绑定 mapper，需要使用路径
- 程序配置文件必须符合规范
- NullPointerException，没有注册到资源
- 输出的XML文件中存在中文乱码问题！
- maven资源没有导出问题

### 4 模糊查询

```xml
<select id="getUserLike" resultType="com.kuang.pojo.User">
    select * from mybatis.user where name like #{value}
</select>
```

```java
@Test
public void getUserLike(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();

    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    List<User> userList = mapper.getUserLike("%李%");
    for (User user : userList) {
        System.out.println(user);
    }

    sqlSession.commit();
    sqlSession.close();
}
```



## 4 配置解析

### 1 核心配置文件

- mybatis-config.xml
- Mybatis 的配置文件包含了会深深影响 Mybatis 行为的设置和属性信息

```
configuration（配置）
properties（属性）
settings（设置）
typeAliases（类型别名）
typeHandlers（类型处理器）
objectFactory（对象工厂）
plugins（插件）
environments（环境配置）
environment（环境变量）
transactionManager（事务管理器）
dataSource（数据源）
databaseIdProvider（数据库厂商标识）
mappers（映射器）
```

### 2 配置化境

MyBatis 可以配置成适应多种环境

**不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory 实例只能选择一种环境**

学会使用配置多套运行环境！

Mybatis 默认的事务管理器就是JDBC，连接池POOLED

### 3 属性（properties）

我们可以通过properties 属性类实现引用配置文件

这些属性都是可以外部配置且可以动态替换的，既可以在典型的Java属性文件中配置，也可以通过 properties 元素的子元素来传递



db.properties

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybatis?serverTimezone=UTC&userSSL=true&userUnicode=true&characterEncoding=UTF-8
username=root
password=li12345
```

在核心配置文件中引入

```xml
<property name="driver" value="${driver}"/>
<property name="url" value="${url}"/>
<property name="username" value="${username}"/>
<property name="password" value="${password}"/>
```

- 可以直接引入外部文件
- 可以在其中增加一些属性配置

- 如果两个文件有同一个字段，优先使用外部配置文件的！



### 4、类型别名

- 类型别名是为了 Java 类型设置一个短的名字
- 存在的意义仅仅在于减少类完全限定名的冗余

```xml
<typeAliases>
    <typeAlias type="com.kuang.pojo.User" alias="User" />
</typeAliases>
```

也可以指定一个包名，Mybatis会在报名下面搜索需要的 Java Bean，比如扫描实体类的包，它的默认别名就是这个类的 类名，首字母小写！

```xml
<typeAliases>
    <package name="com.kaung.pojo"/>
</typeAliases>
```

在实体类比较少的时候，使用第一种

如果实体类十分多，建议使用第二种

第一种可以自定义别名，第二种则不行，如果非要改，需要在实体类上增加注解

```java
@Alias("user")
public class User {}
```

### 5 生命周期和作用域

声明周期和作用域，是至关重要的，因为错误的使用会导致非常严重的**并发问题**

**SqlSessionFactoryBuilder:**

- 一旦创建了 SqlSessionFactory，就不再需要它了

- 局部变量

**SqlSessionFactory:**

- 说白了就是可以想象为数据库连接池

- SqlSessionFactory 一旦被创建就应该再应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**

- 因此 SqlSessionFactory 的最佳作用域是应用作用域
- 最简单的就是使用单例模式或者静态单例模式

**SqlSession**

- 连接到连接池的一个请求！
- SqlSession 的实例不是线程安全的，因此是不能别共享的，所以它的最佳的作用语是请求或方法作用域
- 用完之后需要赶紧关闭，否则资源被占用！

![image-20201116153609691](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201116153609691.png)

这里面的每一个Mapper，就代表一个具体的业务！



## 5 解决属性名和字段名不一致的问题

### 1 问题

数据库中的字段

![image-20201116155300134](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201116155300134.png)

新建一个项目，拷贝之前的，测试实体类字段不一致的情况

```java
public class User {
    private int id;
    private String name;
    private String password;
```

测试出息那问题

![image-20201116155656258](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201116155656258.png)

解决方法：

- 起别名

  ```mysql
  select id, name, pwd as password from user where id = #{id}
  
  ```

### 2 resultMap

结果集映射

```
id 		name		pwd
id		name 		password
```

```xml
<resultMap id="UserMap" type="User">
    <!--column 数据库中的字段， property实体类中的属性-->
    <result column="id" property="id"></result>
    <result column="name" property="name"></result>
    <result column="pwd" property="password"></result>
</resultMap>

<select id="getUserById" resultMap="UserMap">
    select * from mybatis.user where id = #{id}
</select>
```

- resultMap 元素是 Mybatis 中最重要最强大的元素
- ResultMap 的设计思想是，对于简单的语句根本不需要显示的结果映射，而对于复杂一点的语句只需要描述它们的关系就行了
- ResultMap 最优秀的地方在于，虽然你已经对它相当了解了，但是根据不需要显示地用到它们



## 6 日志

### 6.1 日志工厂

如果一个数据库操作，出现了异常，我们需要排错。日志就是最好的助手！

曾经：sout，debug

现在：日志工厂

![image-20201116162258744](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201116162258744.png)

- LOG4J2
- STDOUT_LOGGING

在 Mybatis 中具体使用那个日志实现，在设置中设定！

**STDOUT_LOGGING 标准日志输出**

![image-20201116171932377](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201116171932377.png)

### 6.2 LOG4J

什么事 log4j ?

- Log4j 是 Apache 的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台，文件，GUI组件
- 我们也可以控制每一条日志的输出格式
- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志生成地过程
- 通过一个配置文件来临过地进行配置，而不需要修改应用地代码



1. 先导入Log4j包

```xml
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```



**简单使用**

1. 在要使用 Log4j 的类中，导入包 import org.apache.log4j.Logger;

2. 日志对象，参数为当前类的class

   ```java
   static Logger logger = Logger.getLogger(UserDaoTest.class);
   ```



## 7 分页

**思考：问什么要分页？**

- 减少数据的处理量

**使用limit分页**

```sql
语法：SELECT * FROM USER limit startIndex, pageSize;
SELECT * FROM USER limit 3;  #[0,n]
```

使用 Mybatis 实现分页，核心SQL

1. 接口

   ```java
   // 分页
   List<User> getUserByLimit(Map<String, Integer> map);
   ```

2. Mapper.xml

```xml
<select id="getUserByLimit" parameterType="map" resultMap="UserMap">
    select * from mybatis.user limit #{startIndex},#{pageSize}
</select>
```

3. 测试

```java
@Test
public void getUserByLimit(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    HashMap<String, Integer> map = new HashMap<>();
    map.put("startIndex", 0);
    map.put("pageSize", 2);

    List<User> userList = mapper.getUserByLimit(map);
    for (User user : userList) {
        System.out.println(user);

    }
    sqlSession.close();
}
```

## 8 使用注解开发

### 8.1 面向接口编程

**根本原因：解耦，可拓展，提高复用，分层开发，上层不用管具体的实现，大家都遵循共同的标准，使得开发变得容易，规范性更好**

### 8.2 使用注解开发

1. 注解就直接在接口上实现

```java
@Select("select * from user")
List<User> getUsers();
```

2. 需要在核心配置文件中绑定接口！

```xml
<!--绑定接口-->
<mappers>
    <mapper class="com.kuang.dao.UserMapper"></mapper>
</mappers>
```

3. 测试

本质：反射机制实现

底层：动态代理模式！



**Mybatis详细的执行流程！**



### 8.3 CRUD

我们可以在工具类创建的时候自动提交事务

```java
public static SqlSession getSqlSession(){
    return sqlSessionFactory.openSession(true);
}
```

编写接口，增加注解

```java
@Select("select * from user")
List<User> getUsers();

@Select("select * from user where id = #{id}")
User getUserById(@Param("id") int id);  /*@Param("id")   ==   #{id}*/

@Insert("insert into user(name, id, pwd) values (#{name},#{id},#{password})")
int addUser(User user);

@Update("update user set name = #{name}, pwd = #{password} where id = #{id}")
int updateUser(User user);

@Delete("delete from user where id = #{id}")
int deleteUser(@Param("id") int id);
```

测试类

【注意：我们必须要将接口注册绑定到我们的核心配置文件中！】



**关于@Param()注解**

- 基本类型的参数或者String类型，需要加上
- 引用类型不需要加
- 如果只有一个基本类型可以忽略，但是建议加上
- 我们在SQL中引用的就是我们这里的@Param()中设定的属性名！



## 9 Lombok

作用：

- Java library
- plugs
- build tools
- with one annotation your class



使用步骤：

1. 在 IDEA 中安装 Lombok
2. 在项目中导入 lombok 的 jar 包



## 10 多对一处理

多对一：

![image-20201118085201962](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201118085201962.png)

- 多个学生对应一个老师

- 对于学生这边，关联多个学生关联一个老师【多对一】

- 对于老师而言，集合，一个老师有很多学生【一对多】



建立数据库

```sql
USE `mybatis`
CREATE TABLE `teacher`(
	`id` INT(10) NOT NULL PRIMARY KEY,
	`name` VARCHAR(30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO teacher(`id`, `name`) VALUES (1, "AD钙")

CREATE TABLE `student`(
	`id` INT(10) NOT NULL PRIMARY KEY,
	`name` VARCHAR(30) DEFAULT NULL,
	`tid` INT(10) DEFAULT NULL,
	KEY `fktid` (`tid`),
	CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO `student` (`id`, `name`, `tid`) VALUES (1,'小明','1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (2,'小红','1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (3,'小张','1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (4,'小李','1');
INSERT INTO `student` (`id`, `name`, `tid`) VALUES (5,'小王','1');
```



### 10.1 测试环境搭建

1. 导入lombok
2. 新建实体类Teacher，Student
3. jianliMapper接口
4. 建立Mapper.XML文件
5. 在核心配置文件中绑定注册沃恩的Mapper接口或文件

6. 测试查询是否能够成功

### 10.2 按照查询嵌套处理

```xml
<!--/*
    思路：
    1. 查询所有的学生信息
    2. 根据查询出来的学生的id，寻找对应的老师
    */-->
<select id="getStudent" resultMap="StudentTeacher">
    select * from student;
    --         select s.id, s.name, t.name from student s, teacher t where s.tid = t.id;
</select>
<resultMap id="StudentTeacher" type="Student">
    <result property="id" column="id"></result>
    <result property="name" column="name"></result>
    <!--
            复杂的属性，我们需要单独处理
            对象：association
            集合：collection
        -->
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"></association>
</resultMap>

<select id="getTeacher" resultType="Teacher">
    select * from teacher where id = #{id}
</select>
```

### 10.3 按照结果嵌套处理

```xml
<!--按照结果嵌套处理-->
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id, s.name, t.name
    from student s, teacher t
    where s.tid = t.id
</select>
    <resultMap id="StudentTeacher2" type="Student">
    <result property="id" column="id"></result>
    <result property="name" column="sname"></result>
    <association property="teacher" javaType="Teacher">
    	<result property="name" column="tname"></result>
	</association>
</resultMap>
```



回顾mysql连表查询

- 子查询

- 联表查询

## 11 一对多处理

比如：一个老师拥有多个学生

对于老师而言，就是一对多的关系！

### 11.1 环境搭建，和上面一对多相似

**实体类**

```java
@Data   // 作用是省去了写 set()/get()方法
public class Student {
    private int id;
    private String name;
    private int tid;
}
```

```java
@Data   // 作用是省去了写 set()/get()方法
public class Teacher {
    private int id;
    private String name;
    // 一个老师拥有多个学生
    private List<Student> students;
}
```

### 11.2 按照结果嵌套处理

```xml
<!--按照结果嵌套处理-->
<select id="getTeacher" resultMap="TeacherStudent">
    select s.id sid, s.name sname, t.name tname, t.id tid
    from student s, teacher t
    where s.tid = t.id and t.id = #{tid}
</select>
<resultMap id="TeacherStudent" type="Teacher">
    <result property="id" column="tid"></result>
    <result property="name" column="tname"></result>
    <!--
        复杂的属性，我们需要单独处理，对象：association 集合：collection
        javaType=""指定属性的类型！
        集合中泛型信息，我们使用ofType获取
    -->
    <collection property="students" ofType="Student">
        <result property="id" column="sid"></result>
        <result property="name" column="sname"></result>
        <result property="tid" column="tid"></result>
    </collection>
</resultMap>
```

### 11.3 按照查询嵌套处理

```xml
<select id="getTeacher2" resultMap="TeacherStudent2">
    select  * from mybatis.teacher where id = #{tid}
</select>
<resultMap id="TeacherStudent2" type="Teacher">
    <collection property="students" javaType="ArrayList" ofType="Student" select="getStudentByTeacherId" column="id"></collection>
</resultMap>

<select id="getStudentByTeacherId" resultType="Student">
    select * from mybatis.student where tid = #{tid}
</select>
```



小节：

1. 关联 - association【多对一】

2. 集合 - collection【一对多】
3. javaType & ofType
   1. JavaType 用来指定实体类中属性的类型
   2. ofType 用来指定映射到List或者集合中的pojo类型，泛型中的约束类型！

注意点：

- 保证SQL的可读性，尽量保证通俗易懂
- 注意一对多和多对一中，属性名和字段的问题
- 如果问题不好排查，可使用日志



面试高频

- Mysql 引擎
- InnoDB底层原理
- 索引
- 索引优化



## 12动态SQL

**什么是动态SQL：动态SQL就是根据不同的条件生成不同的SQL语句**

利用动态SQL这一特性可以彻底摆脱这种痛苦

```
如果你之前用过 JSTL 或任何基于类 XML 语言的文本处理器，你对动态 SQL 元素可能会感觉似曾相识。在 MyBatis 之前的版本中，需要花时间了解大量的元素。借助功能强大的基于 OGNL 的表达式，MyBatis 3 替换了之前的大部分元素，大大精简了元素种类，现在要学习的元素种类比原来的一半还要少。

if
choose (when, otherwise)
trim (where, set)
foreach
```

### 12.1 搭建环境

```sql
CREATE TABLE `blog`(
	`id` VARCHAR(30) NOT NULL COMMENT '博客id',
	`title` VARCHAR(50) NOT NULL COMMENT '博客标题',
	`author` VARCHAR(30) NOT NULL COMMENT '博客作者',
	`create_time` DATETIME NOT NULL COMMENT '创建时间',
	`views` INT(30) NOT NULL COMMENT '浏览量'
)ENGINE=INNODB DEFAULT CHARSET=utf8
```

创建一个基础工程

1. 导包
2. 编写配置文件
3. 编写实体类

```java
@Data
public class Blog {
    private String id;
    private String title;
    private String author;
    private Date create_time;        // 属性名和字段名不一致
    private int view;
}
```

4. 编写实体类对应的Mapper接口和Maper.XML文件

### **12.2 IF**

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog where 1=1
    <if test="title != null">
        and  title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</select>
```



### **12.3 choose(when)**

```xml
select * from mybatis.blog
<where>
    <choose>
        <when test="title != null">
            title = #{title}
        </when>
        <when test="author != null">
            and author = #{author}
        </when>
        <otherwise>
            and views = #{views}
        </otherwise>
    </choose>
</where>
```



### **12.4 trim(where, set)**

```xml
select * from mybatis.blog
<where>
    <if test="title != null">
        title = #{title}
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</where>
```

```xml
<update id="updateBlog" parameterType="map">
    update mybatis.blog
    <set>
        <if test="title != null">
            title = #{title},
        </if>
        <if test="author != null">
            author = #{author}
        </if>
    </set>
    where id = #{id}
</update>
```

**所谓的动态sql，本质还是sql语句，只是我们可以在sql层面去执行一个逻辑代码**



### SQL片段

有的时候，我们可能会将公共的片段抽取出来，方便复用！

1. 使用SQL标签抽取公共的部分

```xml
<sql id="if-title-author">
    <if test="title != null">
        title = #{title},
    </if>
    <if test="author != null">
        and author = #{author}
    </if>
</sql>
```

2. 在需要使用的地方使用 Include 标签引用即可

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <include refid="if-title-author"></include>
    </where>
</select>
```

注意事项：

- 最好基于单表来定义SQL片段！
- 不要存在where标签





### foreach

![image-20201118155540929](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201118155540929.png)

```xml
<!--
    我们现在传递一个完成的map，这map中可以存在一个集合
-->
<select id="queryBlogForeach" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <foreach collection="ids" item="id" open="and (" close=")" separator="or">
            id = #{id}
        </foreach>
    </where>
</select>
```

动态 SQL 就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就可以了

建议：

- 先在Mysql中写出完整的 SQL 在对应的去修改我们的动态 SQL 实现通用即可！



## 13 缓存

### 13.1 简介

```
查询：链接数据库，耗资源
	一次查询的结果，给他暂存一个可以直接取到的地方 --> 内存：缓存
	
我们再次查询相同数据的时候，直接走缓存，就不用走数据库了
```



1. 什么是缓存
   1. 存在内存中的临时数据
   2. 从缓存中查询，从而提高查询效率，解决了高并发系统的性能问题
2. 为什么使用缓存
   1. 减少和数据的交互次数，减少系统的开销，提高系统效率

3. 什么样的数据能使用缓存？
   1. 经常查询并且不经常改变的数据【可以使用缓存】

### 13.2 Mybatis缓存

- Mybatis包含一个非常强大的缓存特性，它可以非常方便地定制配置缓存。缓存可以极大地提升查询效率
- Mybatis系统默认定义了两级缓存：**一级缓存**和**二级缓存**
  - 默认情况下，只有一级缓存开启（SqlSession级别地缓存，也成为本地缓存）
  - 二级缓存需要手动开启和配置，他是基于namespace级别地缓存。
  - 为了提高扩展性，Mybatis定义了缓存接口Cache。我们可以通过Cache接口来定义二级缓存

### 13.3 一级缓存

- 一级缓存也叫本地缓存: SqlSession
  - 与数据库同一次会话期间查询到数据会放在本地缓存中
  - 以后如果需要获取相同地数据，直接从缓存中拿，没必要再去查询数据库

测试步骤：

1. 开启日志！
2. 测试在一个Session中查询两次相同地记录
3. 查看日志输出![image-20201118170312634](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201118170312634.png)

缓存失效的情况

1. 查询不同的东西

2. 增删改操作，可能会改变原来的数据，所以必定会刷新缓存

   ![image-20201118170430563](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201118170430563.png)

3. 查询不同的Mapper.xml
4. 手动清理缓存

![image-20201118170634612](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201118170634612.png)

小节：

一级缓存默认是开启的，只在一次SqlSession中有效，也就是拿到链接到关闭连接这个区间段！

一级缓存就是一个 Map



### 13.4 二级缓存

- 二级缓存也叫全局缓存，一级缓存作用域太低了，所以诞生了二级缓存
- 基namespace级别的缓存，一个名称空间，对应一个二级缓存
- 工作机制
  - 一个会话查询一条书，这个数据就会被放在的那概念会话的一级缓存中
  - 如果当前会话关闭了，这个会话对应的记忆缓存就没有了，但是我们想要的是，会话关闭了，一级缓存中的数据会被包存到二级缓存中、
  - 新的会话查询信息，就可以从二级缓存中获取内容
  - 不同的mapper查出的数据会在自己对应的缓存（map）中

步骤：

1. 开启全局缓存

```xml
<!--开启默认缓存-->
<setting name="cacheEnabled" value="true"/>
```

2. 在要使用二级缓存的Mapper中开启

```xml
<!--在当前Mapper.xml中开启二级缓存-->
<cache></cache>
```

也可以以定义参数

```xml
<!--在当前Mapper.xml中开启二级缓存-->
<cache eviction="FIFO" flushInterval="60000" size="512" readOnly="true"></cache>
```

3. 测试



小节：

- 只要开启了二级缓存，在同一个Mapper下就有效
- 所有的数都会先放在一级缓存中
- 只有当会话提交，或者关闭的时候，才会提交到二级缓存中

### 13.5 缓存的原理

![image-20201119110350679](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201119110350679.png)







































