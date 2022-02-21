# springSecurity 微服务权限方案

内容：

1、什么是微服务 ?

2、微服务认证和授权实现过程

3、完成 SpringSecurity 认证授权案例



### 一、什么是微服务



#### 1、微服务的由来

微服务最早由Martin Fowler 与 James Lewis 于 2014 年共同提出，微服务架构风格是一种使用一套小服务来开发单个应用的方式途径，每个服务运行在自己的进程中，并使用轻量级机制通信，通常是 HTTP API，这些服务基于业务能力构建，并能够通过自动化部署机制来独立部署，这些服务使用的不同编程语言实现，以及不同数据存储技术，并保持最低限度的集中式管理。



#### 2、微服务优势

1. 微服务每个模块就相当于一个单独的项目，代码量明显减少，遇到问题也相对来说比较好解决。
2. 微服务每个模块都可以使用不同的存储方式（比如有的用 redis，有的用 mysql 等），数据库也是单个模块对应自己的数据库。
3. 微服务每个模块都可以使用不同的开发技术，开发模块更灵活。



### 二、微服务认证

1、如果是基于 Session，那么 Spring-security 会对 cookie 里的 sessionid 进行解析，找到服务器存储的 session 信息，然后判断当前用户是否符合请求的要求。

2、如果是 token，则是解析出 token，然后将当前请求加入到 Spring-security 管理的权限信息中去。

![image-20210220200945101](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210220200945101.png)



### 三、微服务权限管理案例主要功能：

1、登陆（认证）

2、添加角色

3、为角色分配菜单

4、添加用户

5、为用户分配角色



#### 1、权限管理数据模型

#### 2、案例设计技术说明

菜单表：1. 讲师管理  		2. 课程管理

角色表：11. 管理员		    12. 销售人员

用户表：21. lucy				 22. mary

角色菜单关系表：cid	rid

用户角色关系表：uid	rid



**主要功能**

1. 添加角色
2. 为角色分配菜单
3. 添加用户
4. 为用户分配菜单

![image-20210220203450629](C:\Users\李祥鸿\AppData\Roaming\Typora\typora-user-images\image-20210220203450629.png)





### 四、案例代码编写

#### 4.1、密码处理工具类

```java
@Component
public class DefaultPasswordEncoder implements PasswordEncoder {

    public DefaultPasswordEncoder() {
        this(-1);
    }
    public DefaultPasswordEncoder(int strength) {
    }
    //进行MD5加密
    @Override
    public String encode(CharSequence charSequence) {
        return MD5.encrypt(charSequence.toString());
    }
    //进行密码比对
    @Override
    public boolean matches(CharSequence charSequence, String encodedPassword) {
        return encodedPassword.equals(MD5.encrypt(charSequence.toString()));
    }
}
```

#### 4.2、 token操作工具类

使用 jwt 生成 token

```java
// 1. 使用 jwt 根据用户名生成 token
public String createToken(String username){
    String token = Jwts.builder().setSubject(username)
            .setExpiration(new Date(System.currentTimeMillis()+tokenEcpiration))
.signWith(SignatureAlgorithm.HS512,tokenSignKey).compressWith(CompressionCodecs.GZIP).compact();
    return token;
}

// 2. 根据 token 字符串得到用户信息
public String getUserInfoFromToken(String token){
    String userinfo = Jwts.parser().setSigningKey(tokenSignKey).parseClaimsJws(token).getBody().getSubject();
    return userinfo;
}
```



#### 4.3、退出处理器

```java
@Override
public void logout(HttpServletRequest request, HttpServletResponse response, Authentication authentication) {
    // 1. 从header里面获取 token
    // 2. token 不为空，移除 token，从rediss 删除token
    String token = request.getHeader("token");
    if (token != null) {
        // 移除
        tokenManager.removeToken(token);
        // 从token获取用户名
        String username = tokenManager.getUserInfoFromToken(token);
        redisTemplate.delete(username);
    }
    ResponseUtil.out(response, R.ok());
}
```



#### 认证过滤器操作

##### 1、获取表单提交用户名和密码

```java
@Override
public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {
    // 获取表单提交数据
    try {
        User user = new ObjectMapper().readValue(request.getInputStream(), User.class);
        return authenticationManager.authenticate(new UsernamePasswordAuthenticationToken(user.getUsername(),user.getPassword(),
                new ArrayList<>()));
    }catch (Exception e){
        e.printStackTrace();
        throw new RuntimeException();
    }
}
```

##### 2、认证成功调用的方法

```java
// 2.认证成功调用的方法
@Override
protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {
    // 认证成功，得到认证成功之后用户信息
    SecurityUser user = (SecurityUser)authResult.getPrincipal();
    // 根据用户名生成 token
    String token = tokenManager.createToken(user.getCurrentUserInfo().getUsername());
    // 把用户名称和用户权限列表放到 redis
    redisTemplate.opsForValue().set(user.getUsername(),user.getPermissionValueList());
    // 返回 token
    ResponseUtil.out(response, R.ok().data("token",token));
}
```

3、认证失败调用的方法

```java
// 3. 认证失败调用的方法
@Override
protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response, AuthenticationException failed) throws IOException, ServletException {
    ResponseUtil.out(response, R.error());
}
```



#### 授权过滤器操作

```java
private UsernamePasswordAuthenticationToken getAuthentication(HttpServletRequest request){
    // 从 header 获取 token
    String token = request.getHeader("token");
    if(token != null){
        // 从 token 获取用户名
        String username = tokenManager.getUserInfoFromToken(token);
        // 从 redis 获取对用权限列表
        List<String> permissionValueList = (List<String>) redisTemplate.opsForValue().get(username);
        Collection<GrantedAuthority> authority = new ArrayList<>();
        for(String permissionValue : permissionValueList) {
            SimpleGrantedAuthority auth = new SimpleGrantedAuthority(permissionValue);
            authority.add(auth);
        }
        return new UsernamePasswordAuthenticationToken(username, token, authority);
    }
    return null;
}
```









































































