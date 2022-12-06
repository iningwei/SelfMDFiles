
笔者版本为：Unity2021.3.8f1

## 设置
### 模版
Player->Resolution and Presentation->WebGL Template
默认是Default。还提供有Minimal，是一种极简模式，没有进度条加载等流程。

### 压缩
Player->Publishing Settings->Compression Format。可以设置压缩格式，一般的话会设置Gzip压缩。若无法确保服务端是否支持压缩格式，则建议设置为Disabled

### 代码裁切
Strip Engine Code可以大大降低WebGL文件大小，但是要慎用其裁剪等级，需要多次测试选择合适的等级。[官方文档：Managed code stripping](https://docs.unity3d.com/Manual/ManagedCodeStripping.html)

## 发布
### 报错处理
Build Settings页面Build后生成的文件index.html,双击后一般来说是无法顺利运行的。
若选择的模版是Default则会弹出报错提示界面，内容为：
```c
Failed to download file Build/web_output.data.gz. 
Loading web pages via a file:// URL without a web server is not supported by this browser. 
Please use a local development web server to host Unity content, or use the Unity Build and Run option.
```

若模版是Minimal则无提示信息，界面空白，只能从浏览器Console查看报错日志：
```c
Access to fetch at 'file:///C:/MetaProject/webAvatarShow/web_output/Build/web_output.data.gz' from origin 'null' has been blocked by CORS policy: 
Cross origin requests are only supported for protocol schemes: http, mnt, https, nb, data, chrome, tx, chrome-untrusted, chrome-extension.
```
针对这个报错，若不想通过搭建服务器的方式来解决的话，目前有两种处理方式来解决。
1，依旧使用Build，且需要使用Firefox浏览器来查看（Chrome,Edge等其它浏览器都不行），而且Firefox还需要进行如下设置：
```
在地址栏输入 about:config,出现Firefox配置信息，在Search输入webgl

双击webgl.forece-enabled设置为true;强制开启webgl支持

双击webgl.forece-disabled设置为false (如果没有该项，则忽略)

搜索security.fileuri.strict_origin_policy设置为false;允许从本地加载资源

重启firefox
```

若是有如下报错，则是因为设置了Gzip压缩格式，可以通过设置压缩格式为Disabled来规避，或者勾选Publishing Settings->Decompression Fallback也可以规避该问题。
```c
Unable to parse Build/web_output.framework.js.gz! Loading pre-compressed (brotli or gzip) content via a file:// URL without a web server is not supported by this browser. 
Please use a local development web server to host compressed Unity content, or use the Unity Build and Run option.
```


2，使用Build And Run，unity会自己在本地搭建个服务器，且完美支持Gzip压缩的问题。

### nginx发布
若设置了Gzip压缩，则浏览器运行后会报错
```
Unable to parse Build/web_output.framework.js.gz! 
This can happen if build compression was enabled but web server hosting the content was misconfigured to not serve the file with HTTP Response Header "Content-Encoding: gzip" present. 
Check browser Console and Devtools Network tab to debug.
```