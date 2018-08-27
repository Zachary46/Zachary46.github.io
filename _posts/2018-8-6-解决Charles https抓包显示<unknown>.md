---
layout: post
title:  "解决Charles https抓包显示<unknown>"
category: Charles
date:   2018-8-27 14:01:00
categories: mac

---

用mac电脑开发安卓的都应该知道青花瓷吧~（不知道的都是小菜鸡，邪恶.jpg）
Charles类似Windows版的Fiddler（没用过Fiddler的都是小菜鸡中的战斗机，嘲笑.png），基本用法我就不说了，抓包的时候如果碰到https链接的地址，你会发现都是看不了内容的，都显示<unknown>

![image.png](https://upload-images.jianshu.io/upload_images/6544996-d8b591e88750be0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为https是加密的，Charles需要配置证书才可以抓包。没配置证书之前，你开着Charles，Google也是用不了的，打开都是这鸟样子!

![image.png](https://upload-images.jianshu.io/upload_images/6544996-bbe18c2f6c19de19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 安装证书

##### 电脑端

点击 **Help**  -- **SSL Proxying** -- **Install Charles Root Certificate**

![image.png](https://upload-images.jianshu.io/upload_images/6544996-fa64dbbb388d1aaf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到可怕的不信任红字，系统默认对Charles证书是不信任的，别慌，我们双击Charles证书

![WechatIMG10.jpeg](https://upload-images.jianshu.io/upload_images/6544996-554c3b59deb1df89.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点开左边的**信任**，选择**始终信任**，点关闭，弹出账户密码验证，填一下就改好了

![WX20180806-151319@2x.png](https://upload-images.jianshu.io/upload_images/6544996-8112f640f5f846f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

回到证书列表可以看到Charles证书已经受信任了

![WechatIMG11.jpeg](https://upload-images.jianshu.io/upload_images/6544996-3a4201b8164c4579.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 手机端

点击 **Help**  -- **SSL Proxying** -- **Install Charles Root Certificate on a Mobile Device or Remote Browser**

![image.png](https://upload-images.jianshu.io/upload_images/6544996-e72cb2bad7b62193.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

弹出提示，手机wifi网络配置代理

![WechatIMG13.jpeg](https://upload-images.jianshu.io/upload_images/6544996-aa1ede16f8d5e9ee.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后就可以在手机浏览器输入 **chls.pro/ssl** 下载证书

![image.png](https://upload-images.jianshu.io/upload_images/6544996-bc412e922a2d558d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

证书下载下来是一个 ***.pem** 的文件，我们需要重命名为  ***.crt** 文件，不然无法安装，有的浏览器直接下载下来就是 ***.crt** 文件，看运气~~  然后进入手机设置安装，在此以小米6手机为例，点击  **设置 -- 更多设置 -- 系统安全 -- 加密与凭据 -- 从存储设备安装** 找到刚才浏览器下载证书的路径选择安装，为证书起个名字

![WechatIMG14.png](https://upload-images.jianshu.io/upload_images/6544996-5685b14a8f29cf58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点确定，输入账户密码完成安装，回到Charles

![image.png](https://upload-images.jianshu.io/upload_images/6544996-31162d4dbd79faa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到https链接下终于不再显示<unknown>了，而是感人的数据！

![勾引.png](https://upload-images.jianshu.io/upload_images/6544996-31b25cbd963d286c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 # 随手点个赞，长高十公分！！!



