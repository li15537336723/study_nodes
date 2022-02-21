# Map



Map 接口和常用方法



1）Map 与 Collection 并列存在。用于保存具有映射关系的数据 Key - Value

2）Map 中的 key 和 value 可以是任何引用类型的数据，会封装到 HashMap$Node 对象中

3）Map 中的 **key 不允许重复**

4）Map 中的 **value 可以重复**

5）Map 的 key 可以为 null ，value 也可以为 null，注意 key 为 null，只能有一个，value 为 null，可以多个

6）常用 String 类作为 Map 的key

7）key 和 value 之间存在单向一对一关系，即通过指定的 key 总能找到对应的 value



## Map 遍历方式

```java
public class MapFor {
  public static void main(String[] args) {
    Map map = new HashMap();
    map.put("邓超", "孙俪");
    map.put("王宝强", "马蓉");
    map.put("宋喆", "马蓉");
    map.put("刘令博", null);
    map.put(null, "刘亦菲");
    map.put("鹿晗", "关晓彤");
    map.put("ss", "ssss");

    // 第一组；先取出所有的 Key，通过 Key 去除对应的 Value
    Set keySet = map.keySet();
    //(1) 增强 for
    System.out.println("------第一种方式--------------");
    for (Object key : keySet) {
      System.out.println(key + "-" + map.get(key));
    }
    System.out.println("222222222222222222");
    // (2) 迭代器
    Iterator iterator = keySet.iterator();
    while (iterator.hasNext()) {
      Object key = iterator.next();
      System.out.println(key + "-" + map.get(key));
    }
    // 第二组：把所有的 values 取出
    Collection values = map.values();
    System.out.println("增强 for 去除所有的 value");
    for (Object value : values) {
      System.out.println(value);
    }

    System.out.println("-------迭代器-------");
    Iterator iterator1 = values.iterator();
    while (iterator1.hasNext()) {
      Object value = iterator1.next();
      System.out.println(value);
    }

    // 第三组：通过 EntrySet 来获取 k-v
    Set entrySet = map.entrySet();
    // (1) 增强 for
    System.out.println("------使用 EntrySet ------");
    for (Object entry : entrySet) {
      // 将entry 转成 Map.Entry
      Map.Entry m = (Map.Entry) entry;
      System.out.println(m.getKey() + "-" + m.getValue());
    }

    // (2) 迭代器
    System.out.println("------使用 EntrySet 的迭代器-----");
    Iterator iterator2 = entrySet.iterator();
    while (iterator2.hasNext()) {
      Object entry = iterator2.next();
      Map.Entry m = (Map.Entry) entry;
      System.out.println(m.getKey() + "-" + m.getValue());
    }

  }
}
```





## Map 遍历并选出来工资大于18000的员工

```java
public class MapT1 {
  public static void main(String[] args) {
    Map map = new HashMap();
    Employee employee = new Employee(1, "张三", 20000);
    Employee employee1 = new Employee(2, "李四", 20000);
    Employee employee2 = new Employee(3, "王五", 2000);
    map.put(employee.getId(), employee);
    map.put(employee1.getId(), employee1);
    map.put(employee2.getId(), employee2);

    Set set = map.keySet();
    for (Object value : set) {
      Employee emp = (Employee) map.get(value);
      if(emp.getSal()> 18000){
        System.out.println(map.get(value));
      }
    }

    Set set1 = map.entrySet();
    for (Object o : set1) {
      Map.Entry m = (Map.Entry) o;
      Employee emp = (Employee) m.getValue();
      if(emp.getSal() > 18000){
        System.out.println(m.getKey() + "-" + m.getValue());
      }
    }

    Iterator iterator = set1.iterator();
    while (iterator.hasNext()) {
      Map.Entry entry = (Map.Entry) iterator.next();
      Employee value = (Employee) entry.getValue();
      if (value.getSal() > 18000) {
        System.out.println(value);
      }
    }
  }
}

class Employee{
  private int id;
  private String name;
  private int sal;

  @Override
  public String toString() {
    return "Employee{" +
            "id=" + id +
            ", name='" + name + '\'' +
            ", sal=" + sal +
            '}';
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
  public int getSal() {
    return sal;
  }
  public void setSal(int sal) {
    this.sal = sal;
  }
  public Employee(int id, String name, int sal) {
    this.id = id;
    this.name = name;
    this.sal = sal;
  }
}
```

























