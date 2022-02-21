```vue
hello	--------->项目名
	-build 	---------->用来使用webpack打包使用build依赖
	-config	----------->用来做整个项目配置目录
	-node_ modules	------>用来管理项目中使用依赖
	-src		----->用来书写vue的源代码[重点]
		+assets	------>用来存放静态资源[重点]
		components	------>用来书写Vue组件[ 重点]
		router	----->用来配置项目中路由[重点]
		App . vue	----->项目中根组件[重点]
		main.js	----->项目中主入口[重点]
	-static
	- . babelrc
	- . editorconfig
	- . gitignore
	- . postcssrc.js
	- index . html
	-package. json
	-package- lock. json
	- README . md

```

Vue  中项目开发方式

注意：一切皆组件	一个组件中	js代码，html代码，css样式







登记部门下拉api



添加		编辑			详情  		3种状态	

信访来源前端写死



### 双向绑定的操作——ref

```html
<input type="text" ref="age" @keyup="getAge">
<input type="text" ref="name" @keyup="getName">
```

```javascript
methods:{
    getAge(){
        this.age = this.$refs.age.value
    },
    getName(){
        this.name =  this.$refs.name.value
    }
}，
// watch
watch{
    name(val, oldVal){
		console.log(val, oldVal)
    },
    age(val, oldVal){
        console.log(val, oldVal)
    }
}
```



### 计算属性——computed

```html
<button @click="a++">Add to  a</button>
<button @click="b++">Add to b</button>
```



```javascript
data(){
    return{
        a:0,
        b:0
    }
},
computed:{
    addToA(){
        console.log("addToA");
        return this.a + this.age;
    },
    addToB(){
        console.log("addToB");
        return this.b + this.age;
    }
}
```



### axios请求

```javascript
// post 请求
axios.post('path', this.xxx).then(res => {
    console.log(res.data)
})
// get请求
axios.get('path').then(res => {
    console.log(res)
})
```



### 子父组件通信

父组件：绑定属性

```html
<Index :users="users"></Index>
```

```javascript
data(){
    return {
        users:[
            {name:"xiaohogn1", wechat:"11111",show:false},
            {name:"xiaohogn2", wechat:"22222",show:false},
            {name:"xiaohogn3", wechat:"33333",show:false},
            {name:"xiaohogn4", wechat:"44444",show:false},
        ]
    }
},
```

子组件，用props接收绑定的属性

```javascript
export default {
  props:["users"],
  name:'index',
  data(){
    return{

    }
  },
  methods:{
    test(){
      console.log(this.users);
    }
  }
}
```



 在页面中显示

```html
<p>{{users}}</p>
```



方式二：子组件中显示

```javascript
props:{
    users:{
        type:Array,		// 传递属性的类型
        required: true	// true 表示在父组件中必须写入 所绑定的属性
    }
},
```





传值分为两种

1，传引会影响到多处

2，传值不会影响到多处





### 子组件修改父组件中的属性——emit

1，子组件自定义事件 `$emit`

```javascript
methods:{
    changeTitle(){
      // this.title = "xioahong change the title"
      // 注册事件 参数1：事件名称 参数2..:值
      this.$emit("titleChange", "xiaohong 修改了 title 值")
    }
  }
```

2，标签点击后发送事件

```html
<div id="Student"  @click="changeTitle">
```

3，父组件进行接收

注意：子组件发出来 `$emit` 的名称要和父组件绑定的相同 **==updateTitle==**

`<Student @titleChange="updateTitle" :title="title"></Student>`

4，定义updateTitle 方法进行title 的更改







### 生命周期函数

beforeCreate

created

beforeMount

mount

beforeUpdate

updated

beforeDestroy

destroyed







this.reactedForm.sign = this.defaultSigns === '' ? 0 : this.defaultSigns

   this.reactedForm.ranks = this.defaultRanks === '' ? 0 : this.defaultRanks

   this.reactedForm.politicsStatus = this.defaultPoliticsStatus === '' ? 0 : this.defaultPoliticsStatus 

   this.reactedForm.regionId = this.defaultRegion === '' ? 0 : this.defaultRegion 

   this.reactedForm.nationId = this.defaultNation === '' ? 0 : this.defaultNation

   this.reactedForm.profession = this.defaultProfession === '' ? 0 : this.defaultProfession 

   this.reactedForm.post = this.defaultPost === '' ? 0 : this.defaultPost

   this.reactedForm.gender = this.defaultGender === '' ? 0 : this.defaultGender 













### 信访登记：

1 被反映人：姓名接口，输入姓**张**，选中某一个回显，**点击添加**按钮添加之后点击某个人再回显

2 重要字段正则判断

3 总**提交**按钮，

### 案件详情：未进行

### 重复案件/受理/驳回：未进行



### 附件管理：

删除文件





```
aa(x){
      this.getAllId.push(x)
      console.log(x);
    },
    getIndex(x){
      this.getAllId.push(x)
    },
    
    getId(){
      var obj = {}
      for(var i = 0; i < this.getAllId.length; i++){
        if(obj[this.getAllId[i]]){
          obj[this.getAllId[i]]++
        }else{
          obj[this.getAllId[i]] = 1
        }
      }
      for(var k in obj){
        if((`${obj[k]}` % 2) !== 0){
          this.returnId.push(`${k}`)
          console.log(`${k}`,"xlianghong");
        }
      }
    },
```

















