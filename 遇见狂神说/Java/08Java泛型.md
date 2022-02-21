# 泛型

把元素的类型设计成一个参数，这个类型参数叫做泛型

```java
ArrayList<Integer> list = new ArrayList<Integer>();
list.add(88);
list.add(458);
list.add(45);
list.add(32);
for(Integer score : list){
    System.out.println(score);
}
```

- 在实例化集合类是，可以指明具体的泛型类型
- 如果实例化时，没有泛型的类型。默认类型为java.lang.Object类型