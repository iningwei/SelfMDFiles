
笔者版本为：Unity2021.3.8f1

## 设置
### 模版
Player->Resolution and Presentation->WebGL Template
默认是Default，这个模版有加载进度条，且在程序出错会弹出提示框。还有一个Minimal模版，没有进度条加载等流程，且程序出错无提示信息。
我们也可以自己自定义模版，放到``Editor\Data\PlaybackEngines\WebGLSupport\BuildTools\WebGLTemplates``目录下即可使用。

### 压缩
Player->Publishing Settings->Compression Format。可以设置压缩格式，一般的话会设置Gzip压缩。若无法确保服务端是否支持压缩格式，则建议设置为Disabled

### 代码裁切
Strip Engine Code可以大大降低WebGL文件大小，但是要慎用其裁剪等级，需要多次测试选择合适的等级。[官方文档：Managed code stripping](https://docs.unity3d.com/Manual/ManagedCodeStripping.html)

### 色彩空间
默认是Gamma模式。若要使用Linear模式，则图形API只能选择WebGL 2.0

### WebGL 2.0
WebGL 2.0 于 2017 年首次发布。相比上一版本，WebGL 2.0在图形功能方面实现了很大提升，带来了OpenGL ES 3.0 功能集，为 WebGL 1.0 图形管道添加了对变换反馈、实例渲染、多个渲染目标、统一缓冲区对象、遮挡查询和更广泛的纹理支持的访问。
目前几乎所有主流浏览器都支持WebGL 2.0了(Safari浏览器需要iOS15+才支持WebGL 2.0)。目前笔者使用Unity版本WebGL 1.0旁标示了Deprecated。

### 显示效果
默认情况下WebGL设置的品质比较低，锯齿感比较强，可以在性能允许的前提下通过增加Anti Aliasing级别以及设置Quality的级别来增加显示效果。

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


2，使用Build And Run，unity会自己在本地搭建个服务器，且完美支持Gzip压缩。

### nginx发布
若设置了Gzip压缩，则浏览器运行后会报错
```c
Unable to parse Build/web_output.framework.js.gz! 
This can happen if build compression was enabled but web server hosting the content was misconfigured to not serve the file with HTTP Response Header "Content-Encoding: gzip" present. 
Check browser Console and Devtools Network tab to debug.
```

可以禁用Gzip压缩，或者设置Decompression Fallback来规避。若是要求使用Gzip，则可以按照[官方文档：Server configuration code samples](https://docs.unity3d.com/2021.3/Documentation/Manual/webgl-server-configuration-code-samples.html)把以下配置添加到conf/nginx.conf中：
```c
# On-disk Brotli-precompressed data files should be served with compression enabled:
location ~ .+\.(data|symbols\.json)\.br$ {
    # Because this file is already pre-compressed on disk, disable the on-demand compression on it.
    # Otherwise nginx would attempt double compression.
    gzip off;
    add_header Content-Encoding br;
    default_type application/octet-stream;
}

# On-disk Brotli-precompressed JavaScript code files:
location ~ .+\.js\.br$ {
    gzip off; # Do not attempt dynamic gzip compression on an already compressed file
    add_header Content-Encoding br;
    default_type application/javascript;
}

# On-disk Brotli-precompressed WebAssembly files:
location ~ .+\.wasm\.br$ {
    gzip off; # Do not attempt dynamic gzip compression on an already compressed file
    add_header Content-Encoding br;
    # Enable streaming WebAssembly compilation by specifying the correct MIME type for
    # Wasm files.
    default_type application/wasm;
}

# On-disk gzip-precompressed data files should be served with compression enabled:
location ~ .+\.(data|symbols\.json)\.gz$ {
    gzip off; # Do not attempt dynamic gzip compression on an already compressed file
    add_header Content-Encoding gzip;
    default_type application/octet-stream;
}

# On-disk gzip-precompressed JavaScript code files:
location ~ .+\.js\.gz$ {
    gzip off; # Do not attempt dynamic gzip compression on an already compressed file
    add_header Content-Encoding gzip;
    default_type application/javascript;
}

# On-disk gzip-precompressed WebAssembly files:
location ~ .+\.wasm\.gz$ {
    gzip off; # Do not attempt dynamic gzip compression on an already compressed file
    add_header Content-Encoding gzip;
    # Enable streaming WebAssembly compilation by specifying the correct MIME type for
    # Wasm files.
    default_type application/wasm;
}
```

### 手机浏览器运行
由于手机性能问题，WebGL默认是不建议使用手机浏览器的，通过Default模版出的包，一开始会弹出提示框``WebGL builds are not supported on mobile devices.``，当然如果程序没问题，那么依旧是可以继续运行的。
若是想禁用Default模板下的这个提示框，可以在index.html中注释``unityShowBanner('WebGL builds are not supported on mobile devices.');``即可。
但是笔者在iOS手机safari浏览器运行后弹出了报错窗口（需要使用Default模版才会有报错窗口，Minimal模版无报错窗口，因此为了调试方便一开始开发建议使用Default窗口），内容为：
```c
RuntimeError:call_indirect to a null table entry(evaluating 'dynCall_iiii(index,a1,a2,a3)')
...(略)
```
[Fatal error with WebGL running on 15.4？](https://forum.unity.com/threads/fatal-error-with-webgl-running-on-15-4.1244374/)，这里找到了相关缘由，是iOS设备15.4版本的问题。



参考文档：
[Unity官方文档：WebGL](https://docs.unity3d.com/2021.3/Documentation/Manual/webgl.html)