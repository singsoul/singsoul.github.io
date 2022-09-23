### 1. 如何获取sdk

在product的build.gradle中加入

```java
maven { url 'http://nexus.rhinox.cn:880/repository/maven-public/'}
```

在app的builde.gradle中加入

```java
implementation 'com.boniu.baseinfo:infoSdk:{version_code}@aar'//version_code从白夜处获取
  

   //网络请求
    implementation 'com.squareup.okhttp3:okhttp:4.9.0'
    implementation 'com.squareup.retrofit2:retrofit:2.5.0'
    implementation 'com.squareup.retrofit2:adapter-rxjava:2.5.0'
  //解析库
    implementation 'com.google.code.gson:gson:2.8.6'

    //通知
    implementation 'org.greenrobot:eventbus:3.0.0'
```

在proguard-rules.pro中加入混淆配置

```java
-keep class com.boniu.baseinfo.bean.**{ *;}
```



### 2.sdk初始化

```java
/*
    public String appName;//app唯一标识
    public String channel;//渠道
    public String versionName;//版本号
    public String uuid;//用户唯一标识
    public String baseUrl;//域名 
    public String headerKey;//header加密key
    public String bodyKey;// body加密key
    public String language;//语言，默认zh
    */
BnConfigBean bnConfigBean = new BnConfigBean("appName", "channel", "versionName", "uuid",
        "httpurl",
        "headerKey", "bodyKey","language");
    /**
     *
     * @param context
     * @param bnConfigBean  基础信息配置
     * @param token   如旧项目使用该sdk，则有token传token，无则传null
     * @param accountid 如旧项目使用该sdk，则有accountid传accountid，无则传null
     * 初始化后记得清空本地的token，accountid
     */
ApiConfig.getInstance().init(this,bnConfigBean,"token","accountid");
```

### 3.内部文件介绍

1.ApiConfig 配置类（包含 AccountInfo，是否登陆，是否会员，清空个人信息等功能）

2.AccountInfo 个人信息数据（所有个人信息，登陆信息都由sdk保存，可以通过该对象获取）

```java
public String accountId = "";//登陆信息
public String token = "";//登陆信息
public String mNickName = "";//用户昵称
public String mMobile = "";//手机号
public String mAvatar = "";//头像
public String mVipExpireTime = "";//会员有效期
public int vipExpireDays;//会员天数

public int times;//功能可消耗次数
public String vipType = "NORMAL";//会员类型
public String dataid = "";//用户id
public int remainTimeSeconds = 0;//可消耗的功能时长，单位秒
public int wordage = 0;//可消耗的功能字数
```

3.EventBean 通知类 通过eventbus接收

```java
//支付成功
public static class PaySuccessBean{
    public PaySuccessBean() {
    }
}
//登陆失效，重新登陆
public static class ReLoginBean{
    public ReLoginBean() {
    }
}
//退出登陆
public static class OutLoginBean{
    public OutLoginBean() {
    }
}

//刷新accountid
public static class RefreshAccountIdBean{
    public RefreshAccountIdBean() {
    }
}

//刷新个人信息
public static class RefreshUserInfoBean{
    public RefreshUserInfoBean() {
    }
}
```

4.XResult 请求返回数据

```java
public String errorCode;
public String errorMsg;
public String errorStack;
public String returnCode;
public boolean success;//是否成功
public boolean timeOut;
public String result;//result对象对应string类型数据
private Gson gson = new Gson();

public boolean isSuccess() {
    return success;
}
//result转对象
public <T> T convertObj(Class<T> clazz) {

    return gson.fromJson(result,clazz);
}
//result转数组
public <T> List<T> converList(java.lang.reflect.Type type){

    return gson.fromJson(result,type);
}
```

### 5.请求封装

```java
		ApiHelper.getAppBase()//获取app基础信息
  	ApiHelper.login()//登陆 
   
    ApiHelper.quicklogin()//一键登录  
    ApiHelper.sendSmsCode()//发送验证码 
       /**
     * 登陆状态下  获取用户信息
     * 个人信息从ApiConfig.getInstance().accountInfo中获取
    	* 不返回个人信息数据
     */
    ApiHelper.getUserInfo() 
    
    ApiHelper.getCancleInfo()//获取注销信息  
    ApiHelper.applyUserCancle()//申请注销  
    ApiHelper.cancleApple()//取消注销  
        /**
     * 意见反馈
      * @param content 反馈内容
     * @param contact 联系方式
     * @param requestCallback
     */
    ApiHelper.addFeedbackInfo()
    ApiHelper.getProductList()//获取产品列表  
    ApiHelper.getPayChannel()//获取支付渠道  
    ApiHelper.createOrder()//创建订单  
    ApiHelper.paySubmit()//统一下单  
    ApiHelper.queryOrder()//会员支付结果查询  
    ApiHelper.baiduCustomHttp()//百度图像处理  
    ApiHelper.aliImageEnhancement()//ali 图像增强  
    ApiHelper.ali_GenerateHumanAnimeStyle()//ali 人像动漫画  
    ApiHelper.aLI_MergeImageFace()// ali 人脸融合  
       /**
     * 自定义接口
     * @param url  链接地址
     * @param bodyJson 接口请求的body数据
     * @param requestCallback
     * @param headerKey 不同域名key有可能不一致，如需必要请传递  可不传
     */
    ApiHelper.customHttp() 

        /**
     * 文件同步相关接口
     */
		ApiHelper.getCloudCompare()//比对文件信息
     
   	ApiHelper.getCloudPull()//根据文件配置类型拉取文件信息
    ApiHelper.getCloudStorage()//获取云空间使用存储容量
    ApiHelper.cloudSync()//同步文件信息
  
 

```