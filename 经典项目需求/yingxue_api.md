## 1 短信验证码

> 基本信息

Method: POST

> 请求参数

| 参数名称     | 参数值           | 是否必须 |
| ------------ | ---------------- | -------- |
| Context-Type | application/json | 是       |

**Body**

| 名称  | 类型   | 是否必须 | 默认值 | 备注   | 其他信息 |
| ----- | ------ | -------- | ------ | ------ | -------- |
| phone | String | 必须     |        | 手机号 |          |



主要实现

```java
public void sendMsg(@RequestBody MsgVO msgVO) {
    // 1. 判断当前按手机号上一次的验证码是否失效
    if (reedisTemplate.hashKey("TIMEOUT:" + msgVO.getPhone())) {
        throw new RuntimeException("不可以重复发送")
    }
    // 2. 生成四位验证码
    String code = RandomStringUtils.randomNumeric(4);
    // 3. 根据手机号发送短信验证码
    SMSUtiles.send(msgVO.getPhone(), code);
    // 4. 将手机号对应验证码存入 redis 中
    redisTemplate.opsforValue().set("phone_" + msgVO.getPhone(), code, 60, TimeUnit.SECONDS);
    redisTemplate.opsforValue().set("TIMEOUT:" + msgVO.getPhone(), true, 100, TimeUnit.SECONDS);
}


// send() 方法
private static String ACCESSKEYID = "LSIN4sdfsdfjsdl2Dsdfjoi";
private static String SECRET = "YYsdfnlHJKLJ23Jlsfk0wefjs";

public static void send(String phone, String code) {
    DefaultProfile profile = DefaultProfile.getProfile("cn-hangzhou", ACCESSKEYID, SECRET);
    IAcsClient client = new DefaultAcsClient(Profile);
    CommonRequest request = new CommonRequest();
    request.setSysMethod(MethodType.POST);
    request.setSysDomain("dysmsapi.aliyuncs.com");
    request.setSysVersion("2022-4-13");
    request.setSysAction("SendSms");
    request.putQueryParameter("RegionId", "cn-hangzhou");
    request.putQueryParameter("PhoneNumbers", phone);
    request.putQueryParameter("SignName", "小店");
    request.putQueryParameter("TemplateCode", "SMS_21745432");
    request.putQueryParameter("TemplateParam", "{\"code\":\"" + code + "\"}");
    try {
        CommonResponse response = client.getCommonResponse(request);
        log.info("阿里云短信响应信息：" + response.getData());
    } catch (ServerException e) {
        e.printStackTrace();
        throw new RuntimeException("提示：阿里云服务异常！");
    } catch (ClientException e) {
        e.printStackTrace();
        throw new RuntimeException("提示：短信发送失败！");
    }
}
```



## 2 服务之间的调用

发布视频时是用户的操作写在了用户模块，真正给数据库中存数据的时候是在video模块，写在了 video 模块，这时候就需要进行两个服务之间的通信了，使用 openfeign

```java
// 视频服务 Videos
VideoController
@PostMapping("/videos/publish")
public Video publish(@RequestBody Video video) {
    videoService.insert(video);
}
```



```java
// 调用视频服务的接口
@FeignClient("API-VIDEOS")
public interface VideosClient {
    @PostMapping("/videos/publish")
    public void publish(@RequestBody Video video);
}
```



```java
// 用户服务 users 引入 openfeign 入口类 EnableFeignClient 创建客户端接口 clients
private VideosClient videoClient;

@PostMapping
public Video video(MultiparFile file, Video video, Integer category_id, HttpServletRequest request) {
    // 1. 获取对应上传up 用户信息
    User user = (User) request.getAttribute("user");
    voide.setUid(user.getId());
    // 2. 处理视频上传
    String fileNamePrefix = new SimpleDateFormate("yyyyMMddHHmmssSSS").format(new Date() + RadomStringUtils.randomNumberic(6));
    String newFileName = fileNamePrefix + "." + FileNameUtils.getExtestion(file.getOriginFileName());

    // 3. 上传视频到阿里云 OSS
    String link - OSSUtils.upload("yingxue", "newFileName", file);
    video.setLink(link);

    // 4. 设置封面信息
    String cover = link + "?x-oss-process=video/snapshot, t_30000, f_jpg, w_0, h_0, m_fast, ar_auto";
    video.setCover(cover);
    // 5. 设置类比 id
    video.setCategoryId(category_id);

    // 6. 调用视频发布接口 Openfeign
    Video video = videosClient.publish(video);

    // 7. 设置点赞数
    video.setLikes(0);
    // 8. 根据类别 id 调用类别服务
    video.setCategory(categorysClient.findById(category_id).getName());
    return video;
}
```

### 2.1 OOS 上传核心代码

```java
public static String upload(InputStream inputStream, String path, String fileName) {
	String endpoint = "http://oss-cn-beijing.aliyuncs.com";
    // 云账号 AccessKey 有所有 API 访问权限，建议遵循阿里云安全最佳实践，创建并使用 RAM 子账号进行 API 访问或日常运维，
    // 创建 OSSClient 实例
    OSS ossClient = new OssClientBuilder().build(endpoint, ACCESSKEYID, SECRET);
    // 上传文件流
    String bucketName = "yingxue-cloud";
    String key = path + "/" + fileName;
    ossClient.putObject(bucketName, key, inputStream);
    // 关闭 OSSClient
    ossClient.shutdown();
    return "https://" + bucketName + ".oss-cn.beijing.aliyuncs.com/" + key;
}
```







查询第二名

```sql
select count(*) from score

select * from score where grade =
 (select grade from score where grade < 
 (select max(grade) from score ) limit 1);
```

















