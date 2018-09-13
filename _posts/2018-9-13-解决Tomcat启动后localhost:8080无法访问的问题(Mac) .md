---
layout: post
title:  "解决Tomcat启动后localhost:8080无法访问的问题(Mac)"
category: Server
date:   2018-9-13
categories: IT

---

#### Mac终端安装Tomcat方法

1、安装Tomcat，终端输入
`brew install tomcat`
![](https://upload-images.jianshu.io/upload_images/6544996-0f82db55db6c9c6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2、启动Tomcat，终端输入
`sudo sh startup.sh`
![](https://upload-images.jianshu.io/upload_images/6544996-9905a9abe21969a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3、浏览器输入localhost:8080，正常可以看到的内容应该是![](https://upload-images.jianshu.io/upload_images/6544996-a632dd5f1bc26289.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)可是本人看到的却是![](https://upload-images.jianshu.io/upload_images/6544996-b3df9d3ba9f2c834.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
排查了很久发现是startup.sh的权限问题，于是终端进入/usr/local/Cellar/tomcat/9.0.10/libexec/bin路径下，执行命令`chmod 777 *.sh`
再次输入命令`sudo sh startup.sh`启动tomcat，localhost:8080就可以正常访问！

另外，Tomcat的关闭命令为`sh ./shutdown.sh`