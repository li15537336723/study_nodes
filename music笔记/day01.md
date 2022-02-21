# 第一天学习

### 解决跨域问题

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry){
        registry.addMapping("/**")
        // 设置允许跨域请求的域名
        .allowedOriginPatterns("*")
        // 是否允许证书（cookies）
        .allowCredentials(true)
        // 设置允许的方法
        .allowedMethods("*")
        // 跨域允许时间
        .maxAge(3600)
        .allowedMethods("GET","HEAD","POST","PUT","DELETE","OPTIONS")
        .allowedHeaders("*");
    }
}
```

### select count(*) from 与 select * from 的区别

**语句一**：`select count(*) from T`

作用： 查询表中数据的行

**语句二**：`select * from T`

区别：语句一 查询结果为一个数值，就是表中数据条数，语句二查询结果是表中所有数据，就是包括字段名字，字段里面的内容的详细信息



### 前端接口访问

首先在 api 文件夹中添加一个 `http.js` 文件

`axios.defaults.timeout = 5000`  超时时间是 5 秒

`axios.default.withCredentials=true`   允许跨域

### 用户名登陆

在用户登录这一块，首先需要拿到用户输入的信息，然后传到后端进行和数据库中的用户和密码比对，如果比对成功进行登陆，否则出现登陆失败界面

前端代码——使用 `v-model` 双向绑定进行操作

```html
<!-- 用户名 -->
<el-form-item prop="username">
	<el-input v-model="ruleForm.username" placeholder="用户名"></el-input>
</el-form-item>
<!-- 密码 -->
<el-form-item prop="password">
	<el-input type="password" v-model="ruleForm.password" placeholder="密码"></el-input>
</el-form-item>
```

JS 代码

```javascript
let params = new URLSearchParams();		// 用来创建 web 接口
params.append("name", this.ruleForm.username)   	// 将用户输入的用户名传到后端
params.append("password", this.ruleForm.password)	// 将用户输入的密码传到后端
```

前端将所有接口全部写到一个 JS 文件中，需要用到那个接口直接 import 导入对应的 JS 文件即可使用

```javascript
// 判断管理员是否登陆
export const getLoginStatus = (params) => post(`admin/login/status`, params)
```

在登陆页面中导入

```javascript
import {getLoginStatus} from "../api/index"
```

再通过接口进行判断用户登陆是否成功

```javascript
getLoginStatus(params).then(res => {
	if(res.code == 1){
		this.notify("登陆成功","success")
	}else{
		this.notify("登陆失败","error")
	}
})
```

#### 登陆成功后路由跳转

首先在路由的 index.js 中设置路由跳转的页面

```javascript
path:'/Info'
component: resolve => require(['../page/InfoPae.vue'], resolve)
```

如果登陆成功之后进行跳转

```javascript
if(res.code == 1){
	this.$router.push("/Info")		// 直接在路由中 push 跳转转页面
	this.notify("登陆成功","success")
}else{
	this.notify("登陆失败","error")
}
```



### Vue中bus的使用

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



### element ui

写前端页面用到 element ui 相当于是一个bootStrap，页面看着更光滑，柔软写，用到那个直接去里面找就可以达到一点效果





















































































