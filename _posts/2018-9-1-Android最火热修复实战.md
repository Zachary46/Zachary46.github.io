---
layout: post
title:  "Android最火热修复实战"
category: Android
date:   2018-9-1 
categories: Java

---

**Sophix官网文档地址
https://help.aliyun.com/document_detail/53240.html**


## 快速接入
项目build.gradle的repositories节点下添加
```
maven{
      url "http://maven.aliyun.com/nexus/content/repositories/releases"
     }
```
app目录build.gradle的dependencies节点下添加
```
    implementation 'com.aliyun.ams:alicloud-android-hotfix:3.2.4'
```
AndroidManifest.xml里配置如下权限
```
<!-- 网络权限 -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <!-- 外部存储读权限，调试工具加载本地补丁需要 -->
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
```
<application />标签里添加
```
    <meta-data
         android:name="com.taobao.android.hotfix.IDSECRET"
         android:value="App ID" />
    <meta-data
         android:name="com.taobao.android.hotfix.APPSECRET"
         android:value="App Secret" />
    <meta-data
         android:name="com.taobao.android.hotfix.RSASECRET"
         android:value="RSA密钥" />
```
App ID、App Secret和RSA密钥在阿里云控制台获取，[控制台传送门](https://emas.console.aliyun.com/?spm=5176.131995.673114.con1.3cc269fdOvHoc3#/productList)
![](https://upload-images.jianshu.io/upload_images/6544996-43b7184736db652d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击添加产品，然后添加应用
![](https://upload-images.jianshu.io/upload_images/6544996-f5bc048a7ff779a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
应用创建完，点击 **移动热修复-应用信息** 可以看到所需的App ID、App Secret和RSA密钥
![](https://upload-images.jianshu.io/upload_images/6544996-aa5c773e0938b520.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/6544996-f73e31bc2d749121.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
需要注意的是，AppKey不是App ID，AppKey加上"-1"才是App ID，即25053496-1才是需要的App ID，可以下载如下文件查看App ID
![](https://upload-images.jianshu.io/upload_images/6544996-df047265ee3046be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


Application配置如下
```
package com.example.zachary.sophix;

import android.app.Application;
import android.content.Context;
import com.taobao.sophix.PatchStatus;
import com.taobao.sophix.SophixManager;
import com.taobao.sophix.listener.PatchLoadStatusListener;

public class App extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        // queryAndLoadNewPatch不可放在attachBaseContext 中，否则无网络权限，建议放在后面任意时刻，如onCreate中
        SophixManager.getInstance().queryAndLoadNewPatch();
    }

    @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
        // initialize必须放在attachBaseContext最前面，初始化代码直接写在Application类里面，切勿封装到其他类。
        SophixManager.getInstance().setContext(this)
                .setAppVersion("1.0.0")
                .setAesKey(null)
                .setEnableDebug(true)
                .setPatchLoadStatusStub(new PatchLoadStatusListener() {
                    @Override
                    public void onLoad(final int mode, final int code, final String info, final int handlePatchVersion) {
                        // 补丁加载回调通知
                        if (code == PatchStatus.CODE_LOAD_SUCCESS) {
                            // 表明补丁加载成功
                        } else if (code == PatchStatus.CODE_LOAD_RELAUNCH) {
                            // 表明新补丁生效需要重启. 开发者可提示用户或者强制重启;
                            // 建议: 用户可以监听进入后台事件, 然后调用killProcessSafely自杀，以此加快应用补丁，详见1.3.2.3
                        } else {
                            // 其它错误信息, 查看PatchStatus类说明
                        }
                    }
                }).initialize();
    }
}

```

这样就配置完了，接下来测试一下，在activity布局文件随便写几个字![](https://upload-images.jianshu.io/upload_images/6544996-4b7f146c3ce1e4c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)然后打包安装到手机![](https://upload-images.jianshu.io/upload_images/6544996-632bb3bd61ff938c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后改一下布局文件，再次打包
![](https://upload-images.jianshu.io/upload_images/6544996-11a73da6eedd8e88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载补丁生成器，地址：https://help.aliyun.com/document_detail/53247.html?spm=a2c4g.11186623.6.552.76dc4122Tgu7um
![](https://upload-images.jianshu.io/upload_images/6544996-59deb0da6d091dbc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
选择两次打包的apk，点击 **GO!** 生成补丁
![](https://upload-images.jianshu.io/upload_images/6544996-afc5c4824cb0a1ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开控制台，点击 **移动热修复-补丁管理-上传补丁**![](https://upload-images.jianshu.io/upload_images/6544996-fc0fede3c3af31a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/6544996-74e4b4a0df00e586.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上传完补丁，点击详情
![](https://upload-images.jianshu.io/upload_images/6544996-84ae073f04f7b7b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
点击新建发布，选择全量发布,（[灰色发布](https://baike.baidu.com/item/%E7%81%B0%E5%BA%A6%E5%8F%91%E5%B8%83/7100322?fr=aladdin)大概是随机选择几个目标测试，减少风险）
![](https://upload-images.jianshu.io/upload_images/6544996-6f04cbb4ada0c144.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
发布成功后，杀掉刚才安装带有“天王盖地虎”内容的安装包，重新打开，发现内容变成“宝塔镇河妖”，说明热修复成功！
![](https://upload-images.jianshu.io/upload_images/6544996-3960eb5a11824960.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 稳健接入
[稳健接入](https://help.aliyun.com/document_detail/61082.html?spm=a2c4g.11186623.6.549.7f5841227jpSXk)和[快速接入](https://help.aliyun.com/document_detail/53240.html?spm=a2c4g.11186623.6.548.767322bfNBvx7p)的差异主要在Application的配置上，其他配置都一样，新建一个SophixStubApplication类，配置如下
```
package com.example.zachary.sophix;

import android.content.Context;
import android.support.annotation.Keep;
import android.util.Log;
import com.taobao.sophix.PatchStatus;
import com.taobao.sophix.SophixApplication;
import com.taobao.sophix.SophixEntry;
import com.taobao.sophix.SophixManager;
import com.taobao.sophix.listener.PatchLoadStatusListener;
/**
 * Sophix入口类，专门用于初始化Sophix，不应包含任何业务逻辑。
 * 此类必须继承自SophixApplication，onCreate方法不需要实现。
 * 此类不应与项目中的其他类有任何互相调用的逻辑，必须完全做到隔离。
 * AndroidManifest中设置application为此类，而SophixEntry中设为原先Application类。
 * 注意原先Application里不需要再重复初始化Sophix，并且需要避免混淆原先Application类。
 * 如有其它自定义改造，请咨询官方后妥善处理。
 */
public class SophixStubApplication extends SophixApplication {
    private final String TAG = "SophixStubApplication";
    //此处SophixEntry应指定真正的Application，并且保证RealApplicationStub类名不被混淆。
    @Keep
    @SophixEntry(MyRealApplication.class)
    static class RealApplicationStub {}
    @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
        //如果需要使用MultiDex，需要在此处调用。
        //MultiDex.install(this);
        initSophix();
    }
    private void initSophix() {
        String appVersion = "1.0.0";//和build.gradle配置的要一致
        try {
            appVersion = this.getPackageManager()
                    .getPackageInfo(this.getPackageName(), 0)
                    .versionName;
        } catch (Exception e) {
        }
        final SophixManager instance = SophixManager.getInstance();
        instance.setContext(this)
                .setAppVersion(appVersion)
                .setSecretMetaData(null, null, null)
                .setEnableDebug(true)
                .setEnableFullLog()
                .setPatchLoadStatusStub(new PatchLoadStatusListener() {
                    @Override
                    public void onLoad(final int mode, final int code, final String info, final int handlePatchVersion) {
                        if (code == PatchStatus.CODE_LOAD_SUCCESS) {
                            Log.i(TAG, "sophix load patch success!");
                        } else if (code == PatchStatus.CODE_LOAD_RELAUNCH) {
                            //如果需要在后台重启，建议此处用SharePreference保存状态。
                            Log.i(TAG, "sophix preload patch success. restart app to make effect.");
                        }
                    }
                }).initialize();
    }
}
```
MyRealApplication就是我们项目中自己的Application，需要在onCreate里添加如下代码
```
public class MyRealApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        //queryAndLoadNewPatch不可放在attachBaseContext 中，否则无网络权限，建议放在后面任意时刻，如onCreate中
        SophixManager.getInstance().queryAndLoadNewPatch();
    }
}
```
然后在AndroidManifest.xml中声明SophixStubApplication![](https://upload-images.jianshu.io/upload_images/6544996-e02aaf2c26af9034.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)记住是SophixStubApplication，而不是项目本来的Application，这跟微信的热修复框架[Tinker](https://github.com/Tencent/tinker/wiki/Tinker-%E6%8E%A5%E5%85%A5%E6%8C%87%E5%8D%97)有点类似啊哈哈~~

项目已上传Github: [https://github.com/Zachary46/Sophix](https://github.com/Zachary46/Sophix)

![](https://upload-images.jianshu.io/upload_images/6544996-3d3b4bde3de69360.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)






