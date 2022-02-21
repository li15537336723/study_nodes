## Swagger

学习目标：

- 了解 Swagger 的作用和概念
- 了解前后端分离
- 在 SpringBoot 中集成 Swagger



### Swagger 简介

**前后端分离**

Vue + SpringBoot

- 后端：后端控制层，服务层，数据访问层【后端团队】
- 前端：前端控制层，视图层【前端团队】
  - 伪造后端数据，json。已经存在了，不需要后端，前端工程依旧能够跑起来

- 前后端如何交互？===> API
- 前后端相对独立，松耦合
- 前后端甚至可以部署在不同的服务器上



产生一个问题：

- 前后端集成联调，前端人员和后端人员无法做到”即使协商，尽早解决“，崩盘

解决方案：

- 前端测试后端接口：postman
- 后端提供接口，需要实时更新新的消息及改动！



### Swagger

- 号称世界上最流行的 API 框架

- RestFul Api 文档在线自动生成工具 => Api 文档与 API 

- 直接运行，可以在线测试 API 接口
- 支持多种语言：（Java， PHP.....）



官网：https://swagger.io/



在项目中使用 Swagger 需要 springbox

- swagger2
- ui



### SpringBoot 集成 Swagger

1、新建 SpringBoot = web 项目

2、导入相关依赖

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

3、编写一个hello工程

4、配置 Swagger => config

```java
@Configuration      // 加载到配置里面
@EnableSwagger2     // 开启 swagger
public class SwaggerConfig {
}
```

5、测试运行 

![image-20210224091710014](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210224091710014.png)



### 配置 Swagger

Swagger 的 bean 实例 Docket

```java
// 配置 swaggeri西南西 -apiInfo
private ApiInfo apiInfo(){
    Contact contact = new Contact("小红", "https://blog.kuangstudy.com/", "2420279320@qq.com");
    return new ApiInfo("小红的 SwaggerAPI 文档",
            "即使再小的帆也能远航",
            "v1.0",
            "https://blog.kuangstudy.com/",
            contact,
            "Apache 2.0",
            "http://www.apache.org/licenses/LICENSE-2.0",
            new ArrayList());
}
```



### Swagger 配置扫描接口

Docket.select()

```java
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())
            .select()
            // RequestHandlerSelectors，配置要扫描接口的方式
            // basePackage：指定要扫描的包
            // any()：全部扫描
            // none(): 不扫描
            // withClassAnnotation：扫描类上的注解,参数是一个注解的反射对象
            // withMethodAnnotation: 扫描方法上的注解
            .apis(RequestHandlerSelectors.basePackage("com.kuang.controller"))
            .paths(PathSelectors.ant("/kuang/**"))
            .build();
}
```



**配置是否启动 Swagger**

```java
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
            .apiInfo(apiInfo())
            .enable(false)  // enable 是否启动 Swagger，如果为 false，则 Swagger 不能在浏览器中访问
            .select()
            .apis(RequestHandlerSelectors.basePackage("com.kuang.controller"))
            .paths(PathSelectors.ant("/kuang/**"))
            .build();
}
```



我们只希望自己的 swagger 在生产环境中使用，在发布的时候不使用！

- 判断是不是生成环境 flag = false
- 注入 enable（flag）

```java
@Bean
public Docket docket(Environment environment){

    // 设置要显示的 swagger 环境
    Profiles profiles = Profiles.of("dev","test");

    // 通过 environment.acceptsProfiles 判断是否储在自己设定的环境当中
    boolean flag = environment.acceptsProfiles(profiles);

    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .enable(flag)  // enable 是否启动 Swagger，如果为 false，则 Swagger 不能在浏览器中访问
        .select()
        // RequestHandlerSelectors，配置要扫描接口的方式
        // basePackage：指定要扫描的包
        // any()：全部扫描
        // none(): 不扫描
        // withClassAnnotation：扫描类上的注解,参数是一个注解的反射对象
        // withMethodAnnotation: 扫描方法上的注解
        .apis(RequestHandlerSelectors.basePackage("com.kuang.controller"))
        //                .paths(PathSelectors.ant("/kuang/**"))
        .build();
}
```



配置 API 文档的分组

```java
.groupName("小红")
```

如果配置多个分组；多个 Docket 实例即可

```java
@Bean
public Docket docket1(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("A");
}
@Bean
public Docket docket2(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("B");
}
@Bean
public Docket docket3(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("C");
}
```



实体类配置

```java
//  @Api (注释)
@ApiOperation("hello控制类")
@ApiModel("用户实体类")
public class User {
    @ApiModelProperty("用户名")
    public String username;
    @ApiModelProperty("密码")
    public String password;
}
```



controller

```java
@RestController
public class HelloController {

    //      /error
    @RequestMapping("hello")
    public String hello(){
        return "hello";
    }

    // 只要接种，返回值存在实体类，它就会被扫描到 swagger 中
    @PostMapping("user")
    public User user(){
        return new User();
    }

    // Operation 接口
    @ApiOperation("hello控制类")
    @GetMapping("hello2")
    public String hello(@ApiParam("用户名") String username){
        return "hello" + username;
    }

    // Operation 接口
    @ApiOperation("hello控制类")
    @PostMapping("postt")
    public User postt(@ApiParam("用户名") User user){
        int i = 5/0;
        return user;
    }
}
```



总结：

1、我们可以通过Swagger给一些比较难理解的属性或者接口，增加注释信息

2、接口文档实时更新

3、可以在线测试

【注意点】在正式发布的时候，关闭 Swagger！！！处于安全考虑，而且节省运行的内存