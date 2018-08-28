---
layout: post
title:  "2017-5-16-Android Studio修改apk打包生成名称"
category: Android Studio
date:   2017-5-16
categories: Android

---

在app的build.gradle文件的android{}括号里添加

```
android.applicationVariants.all { variant ->
        variant.outputs.each { output ->
            def outputFile = output.outputFile
            if (outputFile != null && outputFile.name.endsWith('.apk')) {
                //在这里修改apk文件名
                def fileName = "想要的文件名.apk"
                output.outputFile = new File(outputFile.parent, fileName)
            }
        }
}
```

gradle 3.0以上版本改用以下代码添加

```
android.applicationVariants.all {
    variant ->
        variant.outputs.all {
            //在这里修改apk文件名
            outputFileName = "${variant.name}-v${variant.versionName}.apk"
        }
}
```

variant.name软件名称，variant.versionName软件版本号，比如你的软件叫jianshu，版本号为1.0.0，则输出的软件名称就是 jianshu-v1.0.0.apk

**可通过以下途径关注本人：**

[个人网站](https://link.juejin.im/?target=http%3A%2F%2Fzachary46.xyz)

[Github](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2FZachary46)

[CSDN博客](https://blog.csdn.net/Zachary_46)

[博客园](https://link.juejin.im/?target=https%3A%2F%2Fwww.cnblogs.com%2FZachary46)

[掘金](https://juejin.im/user/5b57e9a16fb9a04f8e1432ef)
