## Swagger

学习目标：

- 了解 Swagger 的作用和概念
- 了解前后端分离
- 在 SpringBoot 中集成 Swagger

### Swagger 简介

**前后端分析**

Vue + SpringBoot

后端时代：前端只用挂历静态页面；html ==> 后端。模板引擎 JSP ==》 后端是主力



前后端分离时代：

- 后端：后端控制层，服务层，数据访问层【后端团队】
- 前端：前端控制层，视图层【前端团队】
  - 伪造后端数据：json，已经存在了，不需要后端，前端工程依旧能够跑起来
- 前后端如何交互？ ===>  API
- 前后端相对独立，松耦合
- 前后但甚至可以部署在不同的服务器上

产生一个问题：

- 前后端集成联调，前端人员和后端人员无法做到“即使协商，尽早解决”，最终导致问题集爆发

解决方案：

- 首先制定一个 schema [计划的提纲]，实时更新最新 API，降低集成的风险
- 早些年：制定 word 计划文档
- 前后端分离：
  - 前端测试后端接口：postman
  - 后端提供接口，需要实时更新最新的消息及改动

### Swagger

- 号称世界上最流行的 API 框架
- RestFul API 文档在线自动生成工具 ==》**API 文档与API 定义同步更新**
- 直接运行，可以在线测试 API 接口
- 支持多种语言：（Java，php，，，，）

官网：https://swagger.io/

在项目中使用 Swagger 需要 Springbox；

- swagger2
- ui

### SpringBoot 集成 Swagger

1. 新建一个 spring boot    Web 项目
2. 导入相关依赖



```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>3.0.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>3.0.0</version>
</dependency>

```

3. 编写一个 Hello 工程
4. 配置 Swagger ==》 Config

```java
@Configuration
@EnableSwagger2
public class SwaggerConfig {

}
```























