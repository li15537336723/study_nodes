# Linux 实操篇



## 1 Xshell 的使用

![image-20210315112951559](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210315112951559.png)



## 2 Vi 和 Vim 编辑器

#### 2.1 Vi 和 Vim 基本介绍

所有的 Linux 系统都会内建 Vi 文本编辑器

Vim 具有程序编辑的能力，可以看作是 Vi 的增强版本，可以主动的以字体颜色辨别语法的正确性，方便程序设计。代码补完，编译及错误跳转等方便编程的功能特别丰富，在程序员中被广泛使用



#### 2.2 vi 和 Vim 的三种常见模式



##### 2.2.1 正常模式

在正常模式下，我们可以使用快捷键

以 vim 打开也给文档就直接进入了一般模式（这是默认模式）。在这个模式中，可以使用【上下左右】按键来移动光标，可以使用【删除字符】或者【删除整行】来处理档案内容，也可以使用【复制，贴上】来处理你的文件数据。

##### 2.2.2 插入模式/编辑模式

在模式下，程序员可以输入任何内容

按下 i，I，o，O，a，A，r，R 等任何一个字母之后才会进入编辑模式，一般来说 i 即可

##### 2.2.3 命令行模式

在这个模式当中，可以提供你相关指令，完成读取、存盘、替换、离开、vim、显示行号等的动作则是在此模式中达成的！

#### 实际操作

![image-20210315152943182](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210315152943182.png)

![image-20210315153144466](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210315153144466.png)

==**:q! 表示打开修改之后还不想保存但是要退出**==



#### 2.3 快捷键的使用案例

1）拷贝当前行 yy，拷贝当前行向下的 5 行，5yy，并粘贴【p】。

2）删除当前行 dd，删除当前行向下的 5 行 5 dd

3）在文件中查询找到某个单词【命令行下 / 关键字，回车 查找，输入 n 就是查找下一个】

4）设置文件的行号，取消文件的行号【命令行下	:set nu 和 :set nonu】

5）编辑 /etc/profile 文件，使用快捷键到底文档的最末行【G】和最首行[gg]，注意：这些都是在正常模式下执行

6）在一个文件中输入 “hello”，然后又撤销这个动作 u

7）编辑 /etc/profile 文件，并将光标移动到 15 行 shift + g

​		第一步：显示行号

​		第二步：输入 15这个数

​		第三步：输入shift + g



## 3 关机 & 重启命令

#### 3.1 基本介绍

`shutdown  -h	now`	立刻进行关机

`shutdown  -h   1` "hello, 1 min 后会关机"

`shutdown  -r  now` 现在重启计算机

`halt` 		 	关机，作用和上面一样

`reboot`			现在重新启动计算机

`sync`   		把内存的数据同步到磁盘

注意细节：

当我们关机或者重启时，都应该先执行一下 sync 指令，把内存的数据写入磁盘，防止数据丢失



## 4 用户登陆和注销



#### 4.1 基本介绍

1）登陆时尽量少用 root 账号登陆，因为它是系统管理员，最大的权限，避免操作失误。可以利用普通用户登陆，登陆后再用“su-用户名”命令来切换成系统管理员身份

2）在提示符下输入 logout 即可注销用户



## 5 用户管理

说明

1）Linux 系统是一个多用户任务的操作系统，任何一个使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。

2）Linux 的用户需要至少要属于一个组



#### 5.1 添加用户

##### 5.1.1基本语法

**useradd  [选项]  用户名**

##### 5.1.2 实际案例

添加一个用户：sanjin

```bash
[root@xiaohong /]# useradd sanjin
```

##### 5.1.3 细节说明

1）当创建用户成功后，会自动的创建和用户同名的家目录

2）也可以通过 `useradd -d 指定目录 新的用户名` , 给新创新的用户指定家目录

```bash
useradd -d /home/ding xiaohong
```



#### 5.2、删除用户

##### 5.2.1 基本语法

`userdel  用户名`

##### 5.2.2 应用案例

1）删除用户 xq，但是要保留家目录

```bash
userdel xq
```

2）删除用户 xh 以及用户主目录

```bash
userdel -r xh
```



#### 5.3 查询用户信息

##### 5.3.1 基本语法

id	用户名

##### 5.3.2 应用实例

案例1：请查询 root 信息

![image-20210315172430675](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210315172430675.png)

##### 5.3.3 细解说明

1）当用户不存在时，返回“无此用户”



#### 5.4 切换用户

##### 5.4.1 介绍

在操作 Linux 中，如果当前用户的权限不够，可以通过 su - 指令，切换到高权限用户，比如 root

##### 5.4.2 基本语法

su - 切换用户名

##### 5.4.3 应用实例

1）创建一个用户 snaji ，指定密码，然后切换到 sanjin

![image-20210315174406931](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210315174406931.png)

##### 5.4.4 细解说明

1）从权限高的用户切换到权限低的用户，不需要输入密码，反之需要

2）当需要返回到原来用户时，使用 exit



## 6、用户组

#### 6.1 介绍

类似于角色，系统可以对有共行的多个用户进行统一的管理

#### 6.2 增加组

groupadd 组名

##### 6.2.1 案例演示

![image-20210315195955655](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210315195955655.png)

#### 6.3 删除组

指令（基本语法）

grouped 组名

##### 6.3.1案例演示



#### 6.4 增加用户时直接加上组

##### 6.4.1 指定（基本语法）

`useradd -g 用户组 用户名`

##### 6.4.2 案例演示

增加一个用户 zwj，直接将他指定到 wudang

```bash
[root@xiaohong ~]# groupadd wudang		# 先创建一个组
[root@xiaohong ~]# useradd -g wudang zwj	# 创建 zwj 用户，并指导到 wudang 这个组
```

#### 6.5 修改用户组

##### 6.5.1 指定（基本语法）

`usermod -g 用户组 用户名`

创建也给 shaolin 组，让将 zwj 用户修改到 shaolin

```bash
[root@xiaohong home]# groupadd shaolin
[root@xiaohong home]# usermod -g shaolin zwj
[root@xiaohong home]# id zwj
uid=1001(zwj) gid=1002(shaolin) groups=1002(shaolin)
```



#### 6.6  /etc/passwd 文件

用户（user）的配置文件，记录用户的各种信息

每行的含义：用户名：口令：用户标识号：注释性描述：主目录：登陆 Shell



#### 6.7 /etc/shadow 文件

口令的配置文件

每行的含义：登录名：加密口令：最后一次修改时间：最小时间间隔：最大时间间隔：警告时间：不活动时间；失效时间：标志



#### 6.8 /etc/group 文件

组（group）的配置文件，记录 Linux 包含的组的信息

每行含义：组名：口令：组标识号：组内用户列表



## 7 使用指令

#### 7.1 指定运行级别

运行级别说明：

0：关机

1：单用户【找回丢失密码】

2：多用户状态没有网络服务

3：多用户状态有网络服务

4：系统未使用保留给用户

5：图形界面

6：系统重启

常用运行级别是 3 和 5，要修改默认的运行级别可改文件

/etc/inittab 的 id:5:initdefault:这一行中的数字

**运行级别的示意图**

![image-20210315204220962](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210315204220962.png)



#### 7.2 切换到指定运行级别的指令



#### 7.3 帮助指令

##### 7.3.1 介绍

当我们对某个指令不熟悉时，我们可以使用 Linux 提供的帮助指令来了解这个指令的使用方法

##### 7.3.2 man 获得帮助信息

- 基本语法

  `man [命令或配置文件] (功能描述：获得帮助信息)`

- 应用实例

  案例：查看 ls 命令的帮助信息

![image-20210316200248564](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316200248564.png)

#### 7.3.3 help 指令

- 基本语法

  `help 命令 (功能描述：获得 shell 内置命令的帮助信息)`

- 应用实例

  案例：查看 cd 命令的帮助信息

  ![image-20210316200634145](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316200634145.png)



#### 7.4 文件目录类

##### 7.4.1 pwd 指令

- 基本语法

  `pwd (功能描述：显示当前工作目录的绝对路径)`

- 应用实例

  案例：显示当前工作目录的绝对路径

  ![image-20210316201924149](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316201924149.png)

##### 7.4.2 ls 指令

- 基本语法

  `ls [选项] [目录或是文件]`

- 常用选项

  -al：显示当前目录所有的文件和目录，包括隐藏的

  -l：以列表的方式显示信息

- 应用实例

  案例：查看当前目录的所有内容信息

  

##### 7.4.3 mkdir 指令

mkdir 指令用于创建目录 (make directory)

- 基本语法 

  `mkdir [选项] 要创建的目录`

- 常用选项

  **-p**：创建多级目录

- 应用实例

  案例一：创建一个目录 /home/dog

![image-20210316204152275](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316204152275.png)

​	案例二：创建多级目录 /home/animal/tigger

![image-20210316204319740](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316204319740.png)





##### 7.4.4 rmdir 指令

- 介绍

  rmdir 指令删除的空目录

- 基本语法

  rmdir [选项] 要删除的空目录

- 应用实例

​	案例1：删除一个目录 /home/dog

![image-20210316204655966](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316204655966.png)

- 使用细解

  `rmdir` 删除的是非空，如果目录下有内容时无法删除的。

  提示：如果需要删除非空目录，需要使用 `rm -rf` 删除的目录



##### 7.4.5 touch 指令

touch 指令创建空文件

- 基本语法

  touch 文件名称

- 应用实例

  案例1：创建一个空文件 hello.txt

  ![image-20210316205921339](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316205921339.png)

  案例2：一次性创建多个文件

  ![image-20210316210053164](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316210053164.png)



##### 7.4.6 cp 指令

cp 指令拷贝文件到指定目录

- 基本语法

  cp [选项] source dest

- 常用选项

  -r ：递归复制整个文件夹

- 应用实例

  案例1：将 /home/aaa.txt 拷贝到 /home/bbb 目录下

  ![image-20210316210510028](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316210510028.png)

  案例2：递归复制整个文件夹，**举例将 test 文件夹全部拷贝到 zwj 文件夹下**

![image-20210316210836638](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316210836638.png)

- 使用细解

  再次拷贝 会出现覆盖 ：强制覆盖不提示的方法：\cp

![image-20210316211244378](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316211244378.png)



##### 7.4.7 rm指令

`rm` 指令移除【删除】**文件或目录**

- 基本语法

  rm [选项] 要删除的文件或目录

- 常用选项

  -r：递归删除整个文件夹

  -f：强制删除不提示

- 应用实例

  案例1：将 /home/aaa.txt 删除

  案例2：递归删除整个文件夹 /home/bbb

- 使用细解

  强制删除不提示的方法：带上 -f 参数即可

  ![image-20210316212251300](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316212251300.png)

![image-20210316212310653](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316212310653.png)



##### 7.4.8 mv 指令

mv 移动文件与目录或重命名

- 基本语法

  `mv oldNameFile newNameFile` (功能描述：重命名)

  `mv /temp/movefile /targetFolder` (功能描述：移动文件)

- 应用实例

  案例1：将 /home/aaa.txt 文件 重新命名为 pig.txt

  ![image-20210316212751999](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316212751999.png)

  案例2：将 /home/pig.txt 文件移动到 /root 目录下

![image-20210316213218970](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210316213218970.png)



##### 7.4.9 cat 指令

cat 查看文件内容

- 基本语法

  `cat [选项] 要查看的文件`

- 常用选项

  -n：显示行号

- 应用实例

  案例1：/etc/profile 	文件内容，并显示行号，more 代表分页显示

![image-20210317090524892](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317090524892.png)

##### 7.4.10 more 指令

more 指令是一个基于 Vi 编辑器的文本过滤器，它以全屏幕的方式按页显示文本的内容。more 指令中内置了若干快捷键

- 基本语法

  more 要查看的文件

- 操作说明

- 应用实例

  案例：采用 more 查看文件

  /etc/profile

![image-20210317090935496](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317090935496.png)

快捷键一栏

![image-20210317090959698](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317090959698.png)



##### 7.4.11 less 指令

less 指令用来分屏查看文件内容，它的功能与 more 指令类似，但是比 more 强大很多，支持各种显示终端。less 指令在显示文件内容时，并不是一次性将整个文件加载之后才显示，而是根据显示需要加载内容，**对于显示大型文件具有较高的效率**

- 基本语法

  less 要查看的文件

- 操作说明

- 应用实例

  案例：采用 less 查看一个大文件

  

##### 7.4.12 echo 指令

echo 输出内容到控制台

- 基本语法

  echo	[选项]	[输出内容]

- 应用实例

  案例：使用 echo 指令输出环境变量，输出当前环境的路径

  ![image-20210317093746496](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317093746496.png)

  案例：使用 echo 指令输出 hello，world！



##### 7.4.13 head指令

head 用于显示文件的开头部分内容，默认情况下 head 指令显示文件的前 10 行内容

- 基本语法

  head 文件(功能描述：查看文件头 10 行内容)

  head -n 5 文件(功能描述：查看文件头5行内容，5可以是任意行数)

- 应用实例

  案例：查看 /etc/profile 的前面 5 行代码

  ![image-20210317094133024](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317094133024.png)



##### 7.4.14 ln 指令

软链接也叫符号连接，类似于 windows 里的快捷方式，主要存放了链接其他文件的路径

- 基本语法

  ln -s[源文件或目录] [软连接名]

- 应用实例

  案例1：在/home 目录下创建一个软件链接(功能描述：给源文件建一个软链接)

![image-20210317103224297](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317103224297.png)

​	案例2：删除软链接 linkToRoot

![image-20210317103403262](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317103403262.png)



##### 7.4.15 history 指令

查看已经执行过历史命令，也可以执行历史命令

- 基本语法

  history （功能描述，查看已经执行过历史命令）

- 应用实例

  案例1：显示所有的历史命令

  ![image-20210317103900835](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317103900835.png)

  案例2：显示最近使用过的 10 个指令

  ![image-20210317103915272](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317103915272.png)

  案例3：执行历史编号为 n 的指令

![image-20210317104011636](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317104011636.png)

#### 7.5 时间日期类

##### 7.5.1 date 指令-显示当前日期

- 基本语法

  1）date		(功能描述：显示当前时间)

  2）date + %Y(功能描述：显示当前年份)

  3）date + %m(功能描述：显示当前月份)

  4）date + %d(功能描述：显示当前是哪一天)

  5）date "+%Y-%m-%d %H:%M:%S"(功能描述：显示当前具体时间)

- 应用实例

  案例1：显示当前时间信息

  ![image-20210317105201295](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317105201295.png)

  案例2：显示当前时间年月日

  ![image-20210317105207585](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317105207585.png)

  案例3：显示当前时间年月日时分秒

![image-20210317105332330](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317105332330.png)

##### 7.5.2 date 指令-设置日期

- 基本语法

  date -s 字符串时间

- 应用实例

  案例1：设置系统当前时间，比如设置成 2020-10-10 11:11:11

![image-20210317105915597](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317105915597.png)

还原跟计算机时间一致 `hwclock -s`

##### 7.5.3 cal 指令

查看日历指令

- 基本语法

  cal[选项] （功能描述：不加选项，显示本月日历）

- 应用实例

  案例1：显示当前日历

  ![image-20210317110536119](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317110536119.png)

  案例2：显示 2020 年日历

![image-20210317110556740](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317110556740.png)



#### 7.6 搜索查找类

##### 7.6.1 find 指令

find 指令将从目录向下递归的遍历其各个子目录，将满足条件的文件或者目录显示在终端。

- 基本语法

  find [搜索范围] [选项]

- 选项说明

![image-20210317111153322](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317111153322.png)

- 应用实例

  案例1：按文件名：根据名称查找 /home 目录下的 mydata.txt 文件

![image-20210317111323221](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317111323221.png)

​		案例2：按拥有者：查找 /opt 目录下，用户名称为 root 的文件

​	![image-20210317111705610](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317111705610.png)

​		案例3：查找整个 linux 系统下大于 20m 的文件 （+n 大于	-n 小于	n 等于）

![image-20210317111908340](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317111908340.png)

![image-20210317111925844](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317111925844.png)



##### 7.6.2 grep 指令和 管道符号 |

grep 过滤查找，管道符，”|“，表示前一个命令的处理结果输出传递给后面的命令处理

- 基本语法

  grep [选项] 查找内容 源文件

- 常用选项

![image-20210317112725119](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317112725119.png)

- 应用实例

  案例1：请在 hello.txt 文件中，查找 ”yes“ 所在行，并且显示行号，大小写说明

![image-20210317113127806](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317113127806.png)



#### 7.7 压缩和解压缩

##### 7.7.1 gzip/gunzip 指令

gzip 用于压缩文件，gunzip 用于解压缩

- 基本语法

  gzip 文件	（功能描述：压缩文件，只能将文件压缩为 *.gz 文件）

  gunzip 文件.gz	（功能描述：解压缩文件命令）

- 实例应用

  - 案例1：gzip 压缩，将 /home 下的 hello.txt 文件进行压缩

    ![image-20210317113633305](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317113633305.png)

  - 案例2：gunzip 压缩，将/home 下的 hello.txt.gz文件进行 **解压缩**

![image-20210317113738393](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317113738393.png)

细解说明

当我们使用 gzip 对文件进行压缩后，不会保留原来的文件



##### 7.7.2 zip/unzip 指令

zip 用于压缩文件，unzip 用于解压的，这个在项目打包发布中有用

- 基本语法

  zip [选项] XXX.zip 将要压缩的内容(功能米哦啊是：压缩文件和目录的命令)

  unzip [选项] XXX.zip （功能描述：解压缩文件）

- zip 常用选项

  -r ：递归压缩，即压缩目录

- unzip 的常用选项

  -d<目录>：指定解压后文件的存放目录

- 应用实例

  - 案例1：将 /home 下的 所有文件进行压缩成 mypackage.zip

  ![image-20210317114522535](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317114522535.png)

  ![image-20210317114544403](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317114544403.png)

  - 案例2：将 mypackge.zip 解压到 /opt/tmp 目录下

![image-20210317114731448](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317114731448.png)



##### 7.7.3 tar 指令

tar 指令是**打包指令**，最后打包的文件是 .tar.gz 的文件

- 基本语法

  tar [选项] XXX.tar.gz 打包的内容 （功能描述，打包目录，压缩后的文件格式.tar.gz）

- 选项说明

  

  **选项											功能**

  **-c											产生 .tar 打包文件**

  **-v											显示详细信息**

  **-f											指定压缩后的文件名**

  **-z											打包同时压缩**

  **-x											解包 .tar 文件**

- 应用实例

  案例1：压缩多个文件，将 /home/a1.txt 和 /home/a2.txt 压缩成 a.tar.gz

  ![image-20210317115609852](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317115609852.png)

  案例2：将 /home 的文件夹 压缩成 myhome.tar.gz

  ![image-20210317115843418](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317115843418.png)

  案例3：将 a.tar.gz 解压到当前目录

  ![image-20210317120049359](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317120049359.png)

  案例4：将 myhome.tar.gz 解压到 /opt/tmp2 目录下

  ![image-20210317120323119](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210317120323119.png)

==**指定解压到的目录首先要存在，否则报错**==













