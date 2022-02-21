# 原理初探

## 自动配置：

### pom.xml

- spring-boot-dependencies: 核心依赖在父工程中！
- 我们在写或者引入一些Springboot依赖的时候，不需要指定版本，就因为有这些版本仓库

### 启动器

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
```

- 启动器：说白了就是springboot的启动场景
- 比如 spring-boot-starter-web，他就会帮我们自动导入 web 环境所有的依赖
- springboot会将所有的功能场景，都变成一个个的启动器
- 我们要使用什么功能，就只需要找到对应的启动器就可以了   **starter**



### 主程序

```java
@SpringBootApplication      // 标注这个类是一个springboot的应用
public class Springboot01Application {

    public static void main(String[] args) {
        // 将springboot应用启动
        SpringApplication.run(Springboot01Application.class, args);
    }
}
```

- 注解：

  - ```java
    @SpringBootConfiguration: springboot的配置
    	@Configuration:	spring配置类
    	@Component:		说明这也是一个spring的组件
    	
    @EnableAutoConfiguration:	自动装配
    	@AutoConfigurationPackage:	自动配置包
    		@Import(AutoConfigurationPackages.Registrar.class):		自动配置'包注册'
    	@Import(AutoConfigurationImiportSelector.class)
            
    // 获取所有的配置
    List<String> configurations = getCandidateConfigurations(annotatio nMetadata, attributes);
    ```



结论：springboot 所有自动装配都是在启动的时候扫描并加载：spring.factories 所有的自动配置类都在这里面，但是不一定生效，要判断条件是否成立，只要导入了对应的start，就有对应的启动器了，有了启动器，我们自动装配就会生效，然后就配置成功！

1. springboot在启动的时候，从类路径下 /META-INF/spring.factories获取指定的值
2. 将这些自动配置的类导入容器，自动配置就会生效，帮我们进行自动配置！
3. 以前我们需要自动配置的东西，现在springboot帮我们做了
4. 整合JavaEE，解决方案和自动配置的东西都在 spring-boot-autoconfigure-2.4.1.RELEASE.jar 这个包下
5. 它会把所有需要导入的组件，以类名的方式返回，这些组件就会被添加要容器
6. 容器中也会存在非常多的xxxAutoConfiguration的文件，就是这些类给容器中导入了这个场景所需要的所有组件；并自动配置，@Configuration，JavaConfig！
7. 有了自动配置类，免去了我们手动配置的文件



### 配置文件

SpringBoot使用一个全局的配置文件，配置文件名称是固定的

- application.properties
  - 语法结构：key = value
- application.yml
  - 语法结构：key: 空格 value

#### YAML

**标记语言**

以前的配置文件，大多数都是使用xml来配置；比如一个简单的端口配置，我们来对比一下 yml 和 xml 

yml 配置：

```yaml
server:
	prot: 8080
```

xml 配置：

```xml
<server>
	<port>8081<port>
<server>
```

yml有好几种写法：**==对空格的要求十分高==**

```yaml
server:
  port: 8081

# 普通的 key-value
name: qinjiang

# 对象
student:
  name: qinjiang
  age: 3

# 行内写法
student1: {name:qinjaing , age: 1 }

# 数组
pets:
  - cat
  - dog
  - pig

pets1: [cat, dog, pig]
```



### SpringBoot 配置

yaml 可以直接给实体类赋值



### SpringBoot 自动装配的原理

1. SpringBoot 启动会加载大量的自动配置类
2. 我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中
3. 我们再来看这个自动配置类中到底配置了那些组件；（我们只需用的组件存在其中，我们就不需要再动手配置了）
4. 给容器中自动配置类添加组件的时候，会从 properties 类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可
5. xxxAutoConfiguration：自动配置类：给容器中添加组件
6. xxxProperties: 封装配置文件中相关属性



SpringBoot Web 开发

jar:webApp!

#### 自动装配

springBoot 到底帮助我们配置了什么？我们能不能进行修改？能修改那些东西？能不能扩展？

- xxxAutoConfiguration.. 想容器中自动配置组件
- xxxProperties: 自动配置类，装配文件中字定义的一些内容

#### 要解决的问题：

- 导入静态资源  ....
- 首页
- 模板引擎
- 装配扩展SpringMVC
- 增删改查
- 拦截器
- 国际化！

#### 静态资源

1. 在springboot，我们可以使用以下方式处理静态资源

- webjars	localhost:8080/webjars/
- public，static，/**，resources       localhost:8080/

2. 优先级：resources>static(默认)>public 



### 模板引擎

结论：只要需要使用 thymeleaf， 只需要导入对应的依赖就可以了！我们将 html 放在我们的 templates 目录下即可

使用 thymeleaf 访问元素

第一步：引入thymeleaf 资源



第二步：在controller 层中写入 Model

![image-20201216101704890](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201216101704890.png)

第三步：在 html 文件中获取

![](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201216101848638.png)

####  使用 thymeleaf 语法遍历

```java
model.addAttribute("users", Arrays.asList("DADG","sfd"));
```

```html
<h3 th:each="user:${users}" th:text="${user}"></h3>
```



### 整合 mybatis

整合包

mybatis-spring-boot-starter

1. 导入包
2. 配置文件
3. mybatis 配置
4. 编写 sql
5. 业务层调用 dao 层
6. controller 调用 service 层

### SpringSecurity（安全）

在 web 开发中！安全第一位！过滤器，拦截器

功能性需求：否

做网站：安全应该在什么时候考虑？设计之初！

- 漏洞，隐私泄露
- 架构一旦确定 

























