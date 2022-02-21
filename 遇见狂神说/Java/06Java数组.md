# 01：数组的声明和创建

- 首先必须声明数组变量，才能在程序中使用数组。下面是声明数组变量的语法

  ```java
  dataType[] arrayRefvar;
  或
  dataType arrayRefvar[];
  ```

- Java语言使用new操作符来创建数组，语法如下：

  ```java
  dataType[] arrayRefvar = new dataType[arraySize];
  ```

- 数组的元素是通过索引访问的，数组索引从 0 开始

- 获取数组长度     **arrays.length**



# 02：内存分析

### Java内存

- 堆
  - 存放 new 的对象和数组
  - 可以被所有的线程共享，不会存放别的对象引用
- 栈
  - 存放基本变量类型（会包含这个基本类型的具体数值）
  - 引用对象的变量（会存放这个引用在堆里面的具体地址）

- 方法区
  - 可以被所有的线程共享
  - 包含了所有的class和static变量

![image-20201112162441145](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201112162441145.png)

# 03：数组三种初始化的方式

```java
// 静态初始化：创建 + 赋值
int[] a = {1, 2, 3, 4, 5, 6, 7, 8};
System.out.println(a[0])
// 动态初始化
int[] b = new int[10];
b[0] = 10;
b[1] = 10;
```



# 04:冒泡排序

```java
/*
	冒泡排序
	1. 比较数组中，两个相邻的元素，如果第一个数比第二个数大，我们就交换它们的位置
	2. 每一次比较，都会产生出一个最大，或者最小的数
	3. 下一轮则可以少一次排序！
	4. 依次循环，直到结束！
*/
public static int[] sort(int[] arr){
    int temp = 0;
    // 外层循环，判断我们这个要走多少次
    for(int i = 0; i < arr.length - 1; i++){
        boolean flag=  false; // 通过flag标识位减少没有意义的比较
        // 内层循环，比较判断两个数，如果第一个数，比第二个数大，则交换位置
        for(int j = 0; j < arr.length-1-i; j++){
            for(arr[j + 1] > arr[j]){
                temp = arr[j+1];
                arr[j+1] = arr[j];
                arr[j] = temp;		// 每轮循环都把最大的数字放在最后面
                flag = true;
            }
        }
        if(flag == false){
            break;
        }
    }
    return arr;
}


```



# 05：稀疏数组

- 当一个数组中大部分元素为 0，或者为同一值时，可以使用稀疏数组来保存该数组。
- 稀疏数组的处理方式是：
  - 记录数组一共有几行几列，有多少个不同值
  - 把具有不同值得元素和行列及值记录在一个小规模得数组中，从而缩小程序的规模
- 如下图：左边是原始数组，右边是

![image-20201112170409491](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20201112170409491.png)



















































