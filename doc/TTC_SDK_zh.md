# TTC SDK教程
## 准备工作

下载 [SDK](https://github.com/TTCECO/TTCSDK_Android)

## 快速集成
将sdk放入libs目录下，在build.gradle中添加依赖，sdk的名称请根据相应的版本号填写。

```
android {
	repositories {
    	flatDir {
      	  dirs 'libs'
    	}
	}
}

dependencies {
  implementation 'org.web3j:core:3.3.1-android'
  implementation 'com.google.protobuf.nano:protobuf-javanano:3.0.0-alpha-5'
  implementation(name: 'ttc_sdk_xxx', ext: 'aar')
  implementation(name: 'ttc_sdk_biz_xxx', ext: 'aar')
}

```
在AndroidManifest中加入下面的配置， 将“应用包名”替换为您应用的包名，appID和secretKey替换为您之前申请的值，其它的请勿修改。

```
<activity-alias
    android:name="bind"
    android:targetActivity="com.ttc.sdk.ui.BindActivity">

    <intent-filter>
        <action android:name="com.ttc.wallet.BINDACTION" />
        <category android:name="android.intent.category.DEFAULT"/>
        <data
            android:scheme="应用包名" />
    </intent-filter>

</activity-alias>

<meta-data
    android:name="TTC_APP_ID"
    android:value="appID"/>
<meta-data
    android:name="TTC_APP_SECRET_KEY"
    android:value="secretKey"/>
```

## 初始化接口
在Application中初始化;  

```
@Override
public void onCreate() {
    super.onCreate();
    int errCode = TTCAgent.init(this);
    if (errCode > 0) {
       String msg = TTCError.getMessage(errCode);
    }
}
```
## 注册用户接口

注册用户， 传入要注册的userId， 注册成功后如果有返回用户信息；  

```
TTCAgent.register(String userId, IManager.UserInfoCallback callback)
```  
例如：

```
TTCAgent.register("123", new IManager.UserInfoCallback() {
           @Override
           public void success(Map<String, String> map) {
               //常用属性在UserAttr中已定义
           }

           @Override
           public void error(String msg) {
           }
       });

```
## 注销用户接口
用户退出时务必调用， 清空在本地保留的用户信息    

```
TTCAgent.unregister()
```
## 更新用户信息接口
参数info为用户要更新的信息，常用属性在UserAttr中已定义，只需上传需要更新的即可。更新成功后在回调中返回用户已上传的所有信息。    
 
```
TTCAgent.updateUserInfo(Map<String, String> info,IManager.UserInfoCallback callback)
```
## 解绑应用接口
解绑后，无法将TTC转入钱包。    

```
TTCAgent.unbindApp()
```
## 待提取的TTC接口
在此app中，还未转入钱包的所有TTC；

```
TTCAgent.getAppBalance(IManager.BalanceCallback callback)
```
例如：

```
TTCAgent.getAppBalance(new IManager.BalanceCallback() {
    @Override
    public void success(BigDecimal balance) {
    }

    @Override
    public void error(String msg) {
    }
});
```

## 钱包的TTC余额接口
查询与此app绑定的钱包的余额；    

```
TTCAgent.getWalletBalance(IManager.BalanceCallback callback)
```
例如：

```
TTCAgent.getWalletBalance(new IManager.BalanceCallback() {
    @Override
        public void success(BigDecimal balance) {
        }

    @Override
    public void error(String msg) {
    }
});
```
## 用户行为接口
behaviorType为行为类型，behaviorType要大于100；extra 必须为json字符串。    

```
TTCAgent.onEvent(int behaviorType, String extra)
```
## sdk设置
设置日志开关、 sdk功能开关（通过serverEnabled设置）

```
TTCAgent.configure(TTCConfigure configure)
```

例如：

```
TTCConfigure.Builder builder = new TTCConfigure.Builder();
builder.logEnabled(false);
builder.serverEnabled(true);
TTCAgent.configure(builder.build());
```
## sdk版本信息
获取sdk的版本名称和版本号：

```
TTCAgent.getVersionName()
TTCAgent.getVersionCode()
```

## 混淆配置
如果您的应用使用了混淆，请添加如下配置：

```
-keep class org.web3j.** {*;}
-keep class com.fasterxml.**{*;}
-keep class okhttp3.**{*;}

-dontwarn  com.fasterxml.**
-dontwarn  org.w3c.dom.**
-dontwarn  okio.**
-dontwarn  rx.**
-dontwarn  javax.**
-dontwarn  org.slf4j.**
-dontwarn sun.misc.**
```
## 错误码文档
错误码文档[详情](SDK_error_code_zh.md)