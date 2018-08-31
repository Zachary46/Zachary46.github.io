---
layout: post
title:  "Android使用Jenkins自动化构建测试打包apk"
category: Android
date:   2016-9-18 
categories: Android

---

Jenkins这东西搭建起来真是一点也不省心啊，看着别人的教程摸着石头过河，配置的东西有点多啊，稍有不慎，就构建不成功啦！即使步骤跟别人一样也会报各种乱七八糟的错误啊哈哈~~这东西只能佛系搭建~~在经历几十次的随缘构建之后，终于构建成功啦~~好感人！眼泪掉下来。。。。。。在此做下记录，造福广大群众！阿弥陀佛~~废话少说，且看如下操作：

## 下载Jenkins并安装

官网下载地址：[https://jenkins.io/download](https://jenkins.io/download)

![](https://upload-images.jianshu.io/upload_images/6544996-1ec1ec646f7f596f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

我们下载如图所示Window版本，下载下来是个.zip的压缩包，我们解压一下得到如下图所示文件：

![](https://upload-images.jianshu.io/upload_images/6544996-4d6541b03010fbe7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/690/format/webp)

双击即可开始安装，一直Next，即可安装完成。

## 环境变量配置

- Java环境配置  

- Android SDK环境配置


**右键此电脑-属性-高级系统设置-环境变量**

在系统变量中添加 ：

变量名 JAVA_HOME 

变量值 E:\Java\jdk8 （路径根据自己实际的java安装目录选择） 

变量名 ANDROID_HOME

变量值 E:\Android\SDK （路径根据自己实际的java安装目录选择）

![](https://upload-images.jianshu.io/upload_images/6544996-6f6ae97c9222b8fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/614/format/webp)

然后在系统的Path变量中添加 ：%Java_Home%\bin  

![](https://upload-images.jianshu.io/upload_images/6544996-15dc2b6988657752.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/525/format/webp)

以上是Java环境的基本配置，作为一名Android开发攻城狮应该都知道基本操作。接下来就是重头戏啦！！！

Jenkins配置

安装完jenkins一般会自动在浏览器中打开，没有的话在浏览器输入 localhost:8080 或者 127.0.0.1:8080页面出来后会提示选择安装插件，第一个是默认推荐的插件，第二个是自己选择要安装的插件

![](https://upload-images.jianshu.io/upload_images/6544996-a21505247659628f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700/format/webp)

我们直接选默认的就好，点击第一个坐等jenkins自动安装插件

![](https://upload-images.jianshu.io/upload_images/6544996-92d59bece358c8da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/827/format/webp)

插件安装后需要创建你的账号

![](https://upload-images.jianshu.io/upload_images/6544996-06b39052f4d5590c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/814/format/webp)

创建完就可以登录Jenkins控制台啦！

![](https://upload-images.jianshu.io/upload_images/6544996-12d21e57cc24cbc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

点击左侧系统管理-系统设置 

![](https://upload-images.jianshu.io/upload_images/6544996-f4d9ddc1d40f8730.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

来到如下设置页面：

![](https://upload-images.jianshu.io/upload_images/6544996-a56465538d6a5151.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

点开主目录右边的高级设置，修改一下工作空间根目录(路径任意，自己知道就行)，若不改，aapt编译时会报错，因为windows版本默认的根目录在C盘底下路径过长

![](https://upload-images.jianshu.io/upload_images/6544996-9f8d6e9732bd3300.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

往下拉配置全局属性，勾选环境变量，添加如下键值对

![](https://upload-images.jianshu.io/upload_images/6544996-2923f2ff5fb2a48c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

为什么说GRADLE_USER_HOME字段要固定写呢，因为要和Gradle的配置字段一致，否则编译会报错，如果这里不进行配置Gradle会使用默认的目录，会因为默认目录路径太长导致编译报错，是不是很坑！！！

![](https://upload-images.jianshu.io/upload_images/6544996-1177f4c144b9e9db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

配置完点击左下角保存按钮保存配置。

回到主页面点击左侧 新建任务

![](https://upload-images.jianshu.io/upload_images/6544996-26933c1488c0e784.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

输入一个任务名称，选择构建一个自由风格的软件项目，点击确定

![](https://upload-images.jianshu.io/upload_images/6544996-d77202f4d035ea8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

这样在 我的视图 里就能看到创建的任务啦，点击任务，进入任务详细页面

![](https://upload-images.jianshu.io/upload_images/6544996-16e672e0ee340c7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/892/format/webp)

点击配置，进行构建前的详细配置，先配置普通项General，勾选Github prooject，设置远程仓库地址，以Github为例：

![](https://upload-images.jianshu.io/upload_images/6544996-ed4311d94a0743ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

然后设置 源码管理 勾选Git，添加Github项目地址，你的Github账号密码

![](https://upload-images.jianshu.io/upload_images/6544996-5d87a50f14a7228a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/946/format/webp)

然后设置 构建触发器（非必需设置）设置了只是多了一种构建方式，可以勾选 触发远程构建 ，然后设置一个身份验证令牌，即可通过浏览器访问链接触发构建

![](https://upload-images.jianshu.io/upload_images/6544996-9fd97236a95e66f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/941/format/webp)

如红色方框所示，有两种拼接链接的方式触发构建，你可以在浏览器中输入localhost:8080/me/my-views/view/all/job/Test/build?token=123456 或者/buildWithParameters?token=123456 来触发构建。

![](https://upload-images.jianshu.io/upload_images/6544996-32ee64670daca341.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/934/format/webp)

也可以勾选Poll SCM，设置每隔一段时间自动构建，我设置了每小时构建一次，如下图所示

![](https://upload-images.jianshu.io/upload_images/6544996-157cd5e9f09837f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/582/format/webp)

语法参考如下：


接下来设置 构建 点击增加构建步骤，选择Invoke Gradle script

![](https://upload-images.jianshu.io/upload_images/6544996-69e0f1df8d497c67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/333/format/webp)

勾选Invoke Gradle，选择gradle

![](https://upload-images.jianshu.io/upload_images/6544996-c041af921e8222e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/942/format/webp)

Tasks 填写 clean build 命令

![](https://upload-images.jianshu.io/upload_images/6544996-a280bc5819311655.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/941/format/webp)

最后设置 构建后操作 点击增加构建后操作步骤，选择Archive the artifacts

![](https://upload-images.jianshu.io/upload_images/6544996-f1b8e52ff3d843b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/517/format/webp)

填入正则表达式路径，匹配用于存档的apk

![](https://upload-images.jianshu.io/upload_images/6544996-02dd132c212443b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/944/format/webp)

app/build/outputs/release/*.apk的意思是匹配app/build/outputs/release路径下所有拓展名为.apk的安装包，看这路径是不是很熟悉，其实就是项目路径下apk的输出路径

![](https://upload-images.jianshu.io/upload_images/6544996-9ca6c360138f04c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/517/format/webp)

存档后Jenkins-release-v1.0.0.apk就会显示在Jenkins项目最后一次成功的构建结果

![](https://upload-images.jianshu.io/upload_images/6544996-a77a8f655c95beb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

到此配置基本完成，是时候构建一波了，回到任务主页，点击立即构建

![](https://upload-images.jianshu.io/upload_images/6544996-0468ad1bd47e4519.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/795/format/webp)

左下角会出现构建进度，蓝色圆点表示成功，红色圆点表示失败，点击构建序号，下拉选择控制台输出可以查看构建日志

![](https://upload-images.jianshu.io/upload_images/6544996-1b4c527ddea8194b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/375/format/webp)
![](https://upload-images.jianshu.io/upload_images/6544996-a7641b4ea57ced93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)
![](https://upload-images.jianshu.io/upload_images/6544996-8656482efe08b9bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)


# 至此，构建就大功告成啦！

在本地的JenkinsWork工作空间目录下可以看到从Github上拉取的项目源码

![](https://upload-images.jianshu.io/upload_images/6544996-3247910c08d4ad12.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/744/format/webp)

构建成功打包的apk在此目录下

![](https://upload-images.jianshu.io/upload_images/6544996-92e8f3a16b15abb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/683/format/webp)

接下来我们可以设置让构建打包好的apk自动发布到fir，详细教程请移步至 [http://blog.fir.im/jenkins](http://blog.fir.im/jenkins) 文章写得很详细，我就不多说了。发布到fir后，平常测试人员找你要测试包，你可以对他说：“自己去fir下载。”一脸帅气.jpg

好了，今天就分享到这，祝大家Jenkins构建之路愉快！有什么问题可以联系我 QQ:**337047207**

可通过以下途径关注本人

- 个人网站 https://zachary46.xyz

- Github https://github.com/Zachary46

- CSDN博客 https://blog.csdn.net/Zachary_46

- 博客园 https://www.cnblogs.com/Zachary46

- 掘金 https://juejin.im/user/5b57e9a16fb9a04f8e1432ef