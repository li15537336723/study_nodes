# 常用注解及其说明

- @Autowired: 自动装配通过类型。名字
  - 如果Autowired不能唯一自动装配属性，则需要@Qualifier(value="xxx")
- @Nullable: 字段标记了这个注解，说明这个字段可以为null
- @Resource: 自动装配通过名字。类型
- @Component: 组件，放在类上，说明这个类被Spring管理了，就是bean
- @Value：给数据类型赋值

```java
@Value("ADDDDD")
public String name;	// 此时 name = "ADDDDD"
```

