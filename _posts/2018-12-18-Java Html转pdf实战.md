---
layout: post
title:  "Java Html转pdf实战"
category: utils
date:   2017-11-26 14:01:00
categories: Java

---

年尾手头没啥事，干起了打杂工作，最近帮忙解决后端项目里一个html批量转pdf速度慢的问题，项目里用到的转换工具是 [wkhtmltopdf](https://wkhtmltopdf.org) ，这货转单个html还好，批量转速度就慢了。几经摸索（各种baidu、google......）各种测试后，终于找到个性能不错的工具 —— [phantomjs](http://phantomjs.org/)

## Phantomjs安装
官网地址：<http://phantomjs.org>
下载地址：<http://phantomjs.org/download.html>  

![](https://upload-images.jianshu.io/upload_images/6544996-21089421ef215c27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择合适自己系统的版本，解压就行，目录结构如下图（mac为例）

![](https://upload-images.jianshu.io/upload_images/6544996-a34da6f4701f51eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新建 **html2pdf.js** 文件，把下面代码拷进去，文件最好放在phantomjs的bin目录下  

```
var page = require('webpage').create();
var system = require('system');
 
////读取命令行参数，也就是js文件路径。
if (system.args.length === 1) {
  console.log('Usage: loadspeed.js <some URL>');
//这行代码很重要。凡是结束必须调用。否则phantomjs不会停止
  phantom.exit();
}
page.settings.loadImages = true;  //加载图片
page.settings.resourceTimeout = 30000;//超过10秒放弃加载
//截图设置，
//page.viewportSize = {
//  width: 1000,
//  height: 3000
//};
var address = system.args[1];
page.open(address, function(status) {
	
	function checkReadyState() {//等待加载完成将页面生成pdf
        setTimeout(function () {
            var readyState = page.evaluate(function () {
                return document.readyState;
            });
 
            if ("complete" === readyState) {
                
				page.paperSize = { width:'1500px',height:'2000px',orientation: 'portrait',border: '1cm' };
				var timestamp = Date.parse(new Date());
				var pdfname = 'HT_'+timestamp + Math.floor(Math.random()*1000000);
				var outpathstr = "/Users/zachary/Downloads/phantomjs2/bin/"+pdfname+".pdf";
				page.render(outpathstr);
				//console.log就是传输回去的内容。
				console.log("生成成功");
				console.log("$"+outpathstr+"$");
				phantom.exit(); 
				
            } else {
                checkReadyState();
            }
        },1000);
    }
    checkReadyState();
});

```
## Phantomjs使用
打开终端，进入phantomjs的bin目录，执行命令`phantomjs html2pdf.js "网址或html文件路径"`，注意空格

![](https://upload-images.jianshu.io/upload_images/6544996-fef56cbc19dd3194.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

生成路径在 **html2pdf.js** 文件里设置，可以看到bin目录下生成pdf成功

![](https://upload-images.jianshu.io/upload_images/6544996-0788729c74985914.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开看下效果，相当Nice ! ! ! 

![](https://upload-images.jianshu.io/upload_images/6544996-637c2bcb80ba8a1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Java调用Phantomjs
新建工具类 **Html2pdfUtil.java**,代码如下

```
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;

/**
 * 转换html为pdf
 * @author Zachary46
 */
public class Html2pdfUtil {

    public static String parseHtml2Pdf(String url) throws IOException {
        System.out.println(url);
        Runtime rt = Runtime.getRuntime();
        Process p = rt.exec("/Users/zachary/Downloads/phantomjs2/bin/phantomjs /Users/zachary/Downloads/phantomjs2/bin/html2pdf.js "+url);
        InputStream is = p.getInputStream();
        BufferedReader br = new BufferedReader(new InputStreamReader(is));
        StringBuffer sbf = new StringBuffer();
        String tmp = "";
        while ((tmp = br.readLine()) != null) {
            sbf.append(tmp);
        }
        String resultstr = sbf.toString();
        System.out.println("resultstr:"+resultstr);
        String[] arr = resultstr.split("\\$");
        String result = "";
        for(String s : arr){
            if(s.endsWith("pdf"))result = s;
        }
        return result;
    }
}

```

写个 测试类 **Converter.java**，代码如下  

```
public class Converter {
    public static void main(String[] args) throws Exception {
        long start = System.currentTimeMillis();
        String result = Html2pdfUtil.parseHtml2Pdf("https://www.baidu.com");
        long all = System.currentTimeMillis()- start;
        System.out.println("pdf生成地址:"+result+",用时:"+all/1000+"秒");
    }
}
```

运行一下Converter.java ，完美生成pdf 。  

![](https://upload-images.jianshu.io/upload_images/6544996-0977618bb41586e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/6544996-b89adc0e92b8b476.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



