## 音乐网站总结

### 一、Vue中bus的使用

vue 中的 bus 事件，一般作为中央事件总线来使用

简单例子：比如在A，B组件中兄弟组件，现在 A 要调用 B 中的 C 事件

1. 创建一个 bus.js

内容：

```javascript
import Vue from 'vue'
const Bus = new Vue()
export {Bus}
```

2. 在 A B 组件中引入 bus.js

`import {Bus} from 'bus'`

3. 在 A 组件中定义要调用 B 事件的 bus 事件名

例如：` Bus.$emit('callC')`

4. 在 B 组件中调用 C 方法

`Bus.$on('callC', this.C)`

`Bus.$on` 里有两个参数，第一个是在A组件定义的名字，第二个参数是在 B 组件要调用的方法 

#### 1.1 作品中的应用：

在后台管理中，侧边栏需要控制左右放缩，需要在顶部点击按钮进行控制左右放缩，侧边栏与顶部属于兄弟组件。

因为头部组件需要调用侧边栏组件中的事件，所以在头部组件中写 `bus.$emit('collapse',this.collapse)`，在侧边栏组件中调用头部组件中的方法`bus.$on('collapse',msg)`。通过 bus 进行组件间的通信，来完成则爹侧边栏

---

### 二、vue中data是否使用return的区别

```javascript
// 一般情况下，再写大的项目中，会出现这样的格式
export default {
    data(){
        return{
            age:0,
            name:'zhangsan'
        }
    }
}
// 这样写的作用：不使用 return 包裹的数据会在项目的全局可见，会造成变量污染
// 使用 return 包裹后数据中变量只在当前组件中生效，不会影响其他组件
```

---

### 三、vue中路由的使用

在建立路由时，一般会出现 router 文件夹，然后里面有 `index.js` 跟目录，基本格式如下

```javascript
import Vue from 'vue'
import Router from 'vue-router'
Vue.use(Router)
export default new Router({
    routes:[
        {
            path:'/',
            name:'name',
            component:resolve => require(['xx/xx/xx.vue'], resolve)
        },
        {
        	path:'/Home',
            name:'name',
            component:resolve => require(['xx/xx/xx.vue'], resolve),
    		// 子路由的实现
            children:[
                {
                    path:'child1',
                    name:'name1',
                    component:resolve => require(['xx/xx/x.vue'], resolve)
                }
            ]
        }
    ]
})
```

#### 3.1 路由跳转(`this.$router.push、replace、go的区别`)

1. this.$router.push()

   描述：跳转到不同的 url，但这个方法回向 history 栈添加一个记录，点击后会返回到到上一个页面

用法：

```javascript
// 字符串
router.push('home')
// 对象
router.push({path:'home'})
// 命名的路由
router.push({name:'user', params:{userId:123})
// 带参数查询，   register?plan = private
router.push({path:'register', query:{plan:'private'}})
```

#### 3.2 项目中的用法

```javascript
// 传参
this.$router.push({path:'/ListSong',query:{id}})
// 接收参数
this.songListId = this.$route.query.id
```





#### 3.3 vue-router 传参(query 和 params)

##### 3.3.1 query 方式传参和接收参数

传参：

``this.$router.push({path:`'/ListSong',query:{id}})`

接收参数：

`this.$route.query.id`

==注意：==：传参是 **==this.$router==**，接收参数是 ==**this.$route**== ，这里千万看清楚！！！

##### 3.3.2 this.$router、 this.$route 有何区别？

![image-20210217101826764](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210217101826764.png)

- $router 为 VueRouter 实例，想到导航到不同的 URl，则使用 $router.push 方法
- $route 为当前 router 跳转对象，里面可以获取 name，path，query，params等

##### 3.3.3 params 方式传参和接收参数

传参：

```javascript
this.$router.push({name:'xxx',params:{id:id}})
```

接收参数

```javascript
this.$route.params.id
```

==**注意：**==params传参，push里面只能式 `name:'xxx'`，不能是path: '/xxx'，因为 params 只能用 name 来引入路由，如果这里写成了 path，接收参数页面将会是 undefined ！！

另外，二者却别，query相当于get请求，页面跳转的时候，可以在地址栏看到请求参数，而 params 相当于 post 请求，参数不会在地址栏中显示



### 3.4 this.$router.replace()

描述：同样是跳转到指定的url，但是这个方法不会像history里面添加新记录，点击返回，会跳转到上上一个页面，上一个记录是不存在的

### 3.5 this.$router.go(n)

相当于当前页面向前或向后跳转多少个页面，类似 `window.history.go(n)` 。n 可为正数可为负数。正数返回上一个页面

```javascript
// 在浏览器记录中前进一步，等同于 history.forward()
router.go(1)
// 后退一步记录，等同于 history.back()
router.go(-1)

```

---

### 四、vue 生命周期函数

#### 4.1 beforeCreate

第一个生命周期函数，便是实例完全被创建出来前，会执行它
注意：在 beforeCreate 中生命周期函数执行的时候， data 和 methods 中的属性与方法定义都还没有初始化

#### 4.2 created

第二个生命周期函数，在 created 中，data 和 methods 都已经被初始化好了！如果要调用 methods 中的方法，或者操作data 中的数据，最早，只能在 created 中操作

#### 4.3 beforeMount

第三个生命周期函数，表示模板已经在内存中编辑完成了，但是尚未把模板渲染到页面中。在 beforeMount 执行的时候，页面中的元素，还没有真正替换过来

#### 4.4 mounted

第四个生命周期函数，表示，内存中的模板，已经真是挂载在页面中，用户已经可以看到渲染好的页面了
注意：mounted 是实例创建期间的最后一个生命周期函数，当执行完成 mounted 就表示，实例已经被完全创建好了，此时，如果没有其他操作的话，这个实例，就静静的躺在我们的内存中，一动不动
此时，表示 vue 实例已经初始化完毕了，组件已经脱离创建阶段，进入运行阶段了，接下来的是运行中的两个事件

#### 4.5 beforeUpdate

这时候，表示我们的界面还没有被更新

#### 4.6 updated

updated 事件执行的时候，页面和打他数据已经保持同步了，都是最新的

#### 4.7 beforeDestory

销毁之前执行，当 beforeDestory 函数执行时，表示 Vue 实例已经从运行阶段进入销毁阶段，vue 实例身上所有的方法与数据都处于可用状态

#### 4.8 destoryed

当 destory 函数执行是，组件中所有的方法与数据已经被完全销毁，不可用

#### 4.9 activated

页面出现的时候执行 actived 声明周期函数，跟监听 watch 有类似作用
activated 声明周期函数，是配合 keep-alive 进行使用。进入页面时，mounted 与 activated 生命周期函数都会执行，当前 keep-alive 时，进行了缓存，这是返回上一页，mounted 生命周期函数不会执行，而 activated 会执行

#### 4.10 deactivated

页面消失时执行，deactivated 生命周期函数，是配合 keep-alive 使用

---

### 五、vue 中 mixins 的使用方法和注意点

#### 5.1 mixins 基础概念

混入(mixins)：是一种分发 Vue 组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。

#### 5.2 怎么用 ？

定义一个混入对象

```javascript
export const myMixin = {
    data(){
        return{
            num:1
        }
    },
    created(){
        this.hello()
    },
    methods:{
        hello(){
            console.log('hello from mixin')
        }
    }
}
```

把混入对象混入到当前的组件中

```html
<template>
    <div class='tempate1'>
        组件1
    </div>
</template>
<script>
	import {myMixin} from '@assets/mixin.js'
    export default{
        mixins:[myMixin]
    }
</script>
```

#### 5.3 mixins 的特点

##### 5.3.1 ==方法和参数在各组件中不共享==

混合对象中的参数 num

```javascript
export const myMixin = {
    data(){
        return{
            num:1
        }
    }
}
```

组件 1 中的参数 num 进行 +1 操作

```html
<template>
	<div>
    	template1里的num:{{num}}
    </div>
</template>

<script>
	import {myMixin} from '@/assets/mixin.js'
    export default{
        mixins:[myMixin],
        created(){
            this.num++
        }
    }
</script>
```

组件2中的参数 num 未进行操作

```html
<template>
	<div>
        template2里的num：{{num}}
    </div>
</template>

<script>
	import {myMixin} from '@/assets/mixin.js'
    export default{
        mixins:[myMixin]
    }
</script>
```

看两组间中分别输出的 num 值

![image-20210217152508587](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210217152508587.png)

可以看到的是，我在组件 1 里改变了 num 里面的值，组件 2 中的 num 值还是混入对象里面的初始值

##### 5.3.2 ==值为对象的选项，如 methods，components 等，选项会被合并，键冲突的组件会覆盖混入对象的混入对象中的方法==

```javascript
export const myMixin = {
    data(){
        return {
            num:1
        }
    },
    created(){
        this.hello();
    },
    methods:{
        func_one(){
            console.log('func_one from mixin')
        },
        func_two(){
            console.log('func_two from mixin')
        }
    }
}
```

组件中的方法

```html
<template>
	<div>
        template1里的:{{num}}
    </div>
</template>
<script>
	import {myMixin} from '@/assets/mixin.js'
    export default{
        mixins:[myMixin],
        created(){
            this.func_self();
            this.func_one();
            this.func_two();
        },
        methods:{
            func_self(){
                console.log('func_self from template1')
            },
            func_two(){
                console.log('func_two from template1')
            }
        }
    }
</script>
```

打印台的输出

![img](https://img2018.cnblogs.com/blog/1141519/201811/1141519-20181109211303534-1030669997.png)

##### 5.3.3 ==值为函数的选项，如created，mounted等，就会被合并调用，混合对象里的钩子函数组件里的钩子函数之前调用混入对象函数中的console==

```javascript
export const myMixin = {
	data(){
        return{
            num:1
        }
    },
    created(){
        console.log('hello from mixin')
    }
}
```

组件函数中的 console

```html
<template>
	<div>
        template1里的num：{{num}}
    </div>
</template>
<script>
	imnport {myMixin} from '@/assets/mixin.js'
    export default{
        mixins:[myMixin],
        created(){
            console.log('hello from self')
        }
    }
</script>
```

打印台的打印

![image-20210217154134163](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210217154134163.png)

#### 5.4 与 Vuex 的区别

经过上面的例子之后，他们之间的区别如下

- vuex：用来做状态管理的，里面定义的变量在每个组件中均可以使用和修改，在任一组件中修改此变量的值之后，其他组件中此变量的值也会随之修改
- Mixins：可以定义共用的变量，在每个组件中使用，引入组件中之后，各个变量是相互独立的，值的修改在组件中不会相互影响

#### 5.5 与公共组件的区别

- 组件：在父组件中引入组件，相当于在父组件中给出一片独立的空间供子组件使用，让后根据 props 来传值，但本质上两者是相对立的。

- Mixins：则是在引入组件之后与组件中的对象和方法进行合并，相当于扩展了父组件的对象与方法，可以理解为形成了一个新的组件。

  

  ---

### 六、监听器 watch

#### 6.1  用法1：常见用法

```html
<div id="root">
    <h3>Watch 用法1：常见用法</h3>
    <input v-model="message">
    <span>{{copyMessage}}</span>
</div>
<script>
	export default{
        data(){
            return{
                message:'hello Vue',
                copyMessage:''
            }
        }
        watch:{
            message(value){
                this.copyMessage = value
            }
        }
    }
</script>
```

键值一体，键为 `message`，值为 `message()` 方法

#### 6.2 用法2：绑定方法

```html
<div id="root2">
    <h3>Watch 用法2：绑定方法</h3>
    <input v-model="message">
    <span>{{copyMessage}}</span>
</div>
<script>
	export default{
        data(){
            return{
                message:'Hello Vue',
                copyMessage:''
            }
        },
        watch:{
            message:'handleMessage'
        },
        methods:{
            handleMessage(value){
                this.copyMessage = value
            }
        }
    }
</script>
```

键为 `message`，值为 `handleMessage()` 方法，每次监听到 `message` 变化，`handleMessage()` 方法就会执行一次

#### 6.3 用法3：deep+handler

```html
<div id="root3">
    <h3>Watch 用法3：deep + handler</h3>
    <input v-model="deepMessage.a.b">
    <span>{{copyMessage}}</span>
</div>
<script>
	export default{
        data(){
            return {
                deepMessage:{
                    a:{
                        b:'Deep Message'
                    }
                },
                copyMessage:''
            }
        },
        watch:{
            deepMessae:{
                handler:'handleDeepMessage',
                deep:true
            }
        },
        methods:{
            handleDeepMessage(vaule){
                this.copyMessage = value.a.b
            }
        }
    }
</script>
```

默认情况下 `watch` 方法只监听 `data` 中的对象，而无法监听到对象内部属性的改变，此时就需要 deep 属性对对象进行深度监听

![image-20210217162628839](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210217162628839.png)

这个案例可以看出：双向绑定的值(`v-model="deepMessage.a.b"` 和 `deepMessage:{a:{b:'DeepMessage'}}`)保持一致；`watch` 不能直接监听 `deepMessage.a.b`，而是需要通过 `handler` 和 `deep` 属性来完成监听

### 6.4  用法4：immediate

```html
<div id="root4">
    <h3>Watch 用法4：immediate</h3>
    <input v-model="message">
    <span>{{copyMessage}}</span>
</div>
<script>
	export default{
        data(){
            return{
                message:'hello vue',
                copyMessage:''
            }
        },
        watch:{
            handler:'handleMessage',
            immediate:true
        },
        methods:{
            handleMessage(value){
                this.copyMessage = value
            }
        }
    }
</script>
```

`watch` 默认情况下在页面首次渲染时，即使监听的值有初始值，也不会直接执行，这种情况下想要第一次渲染后直接监听就需要添加属性：`immediate:true`

---

### 七、vue中的 slot-scope 及 scope.row

写后台系统在写到修改的地方，vue中提供了 `scope 以及 scope.row` 可以使操作更加方便

`slot-scope=scope` 作用域插槽中定义一个对象(这里对象被定义为 scope)来存储插槽上绑定的数据用法

scope.row 使用 ElementUI表格模板渲染数据时使用

当前行数据的获取也会用到插槽，`scope`相当于一行数据，`scope.row` 相当于当前的数据对象

```html
<el-table-column label="简介">
    <template slot-scope="scope">
    	<p style="height:100px;overflow:scroll">{{scope.row.introduction}}</p>
    </template>
</el-table-column>
```

`slot-scope="scope"` 这是作用域插槽中定义一个对象(这里对象被定义为 scope)来存储插槽上绑定的数据的用法

当前数据对象 `scope.row`

在这里使用 ElementUI 表格模板渲染数据时，"当前行数据的获取也会用到插槽，scope 相当于一行的数据，scope.row 相当于当前行的数据对象还是比较方便的

---

### 八、状态管理 Vuex

vuex 是使用 vue 中必不可少的一部分，基于父子，兄弟组件，我们传值可能会很方便，但是如果是没有关联的组件之间要使用同一数据，就显得很无力，那么 vuex 就很好的解决了我们这种问题，它相当于一个公共仓库，保存这所有组件都能共用的数据。

特点：集中式数据管理，一处修改，多处使用

安装：`npm install vuex --save`
`import vuex from 'vuex'`
Vuex 实例对象属性主要有以下5个核心属性
state：全局访问的 state 对象，存放设置的初始状态名及值（==必须要有==）
mutation：里面可以存放改变 state 的初始值的方法（==同步操作，必须要有==）
getters：实时监听 state 值的变化可对状态进行处理，返回一个新的状态，相当于 store 的计算属性
actions：里面可以存放用来一步出发 mutations 里面的方法的方法
modules：存放模块化的数据

在 src 目录下创建 store 文件夹，并在里面创建 index.js 文件，然后在 index.js 中配置

![img](C:\Users\李祥鸿\AppData\Local\YNote\data\qq5638461A6A941A5CB202FDFDBD2C2F22\ca0e05cab78e4af1b6b25a3ebf21239a\clipboard.png)

在项目中的 main.js 中将Vuex 注册到全局实例中

![img](C:\Users\李祥鸿\AppData\Local\YNote\data\qq5638461A6A941A5CB202FDFDBD2C2F22\d624703e30904f1cbf6cb4c7aad0ae3b\clipboard.png)

#### 8.1 state 属性

在组件中获取状态的值：this.$store.state.xxx

```javascript
const store = new Vuex.Store({
	state
})
```

#### 8.2 getter属性

getter 不是必须的，在什么时候会用到呢？有时候我们需要从 store 中的state中派生出一些状态（对state进行计算过滤等操作），比如在getter 中从state中的分数派生出两个状态：90分以上的学生及其数量
在组件中获取 getters 派生两个状态的值：**==this.$store.getters.XXX==**

#### 8.3 mutation 属性

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation，所以它是必须的

```javascript
const mutations={
	ChangeStuScore(state, obj){
		state.StudNum = obj.length
		state.StuScore = obj
	}
}
```

#### 8.4 Actions 属性

暴露给用户使用，借此出发 mutation 中的方法，保存数据
Actions 类似于 mutations，不同在于：

1. Actions 提交的是 mutation，而不是直接变更状态
2. Actions 可以包含任意异步操作

#### 8.5 modules 属性

由于单一状态树，应用的所有状态会集中到一个较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。
为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**每个模块拥有自己的 state，mutation，action，getter，甚至是嵌套子模块——从上至下进行同样方式的分割

![img](C:\Users\李祥鸿\AppData\Local\YNote\data\qq5638461A6A941A5CB202FDFDBD2C2F22\c0e63ec9707f4cffba722c8070a1ee4d\clipboard.png)

---

### 九、 mapGetters

```javascript
// vue 组件中使用到了 ...mapGetters
computed: {
    ...mapGetters([
        'id',
        'url',
        'isPlay'
    ])
},
```

通过引入 mapGetter 我们就可以轻松的取到 vuex 中存储的数据，从代码中可以看出，getters 就类似于 vue 组件的中 computed（计算属性），在组件中引入 mapGetters 就是将 vuex 中的数据映射到组件的计算属性当中，在组件不多，组件的数据通信不是很多的时候这样写看似将简单的东西复杂化了，但是在稍复杂点的项目中这样会极大减少工作量，及组件间数据传递的复杂程度

### 十、play() 和 pause() 方法

**==play():  播放==**
==**pause(): 暂停**==

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>视频播放</title>
</head>
<body>
	<script>
    	window.onload = function(){
            var ov = document.getElementById("a1")
            ov.onmouseover = function(){
                this.play()
            }
            ov.onmouseout = function(){
                this.pause()
            }
        }
    </script>
    <video id="a1" src="XX.mp4" controls loop></video>
</body>
```

























































