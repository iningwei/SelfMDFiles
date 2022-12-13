
笔者版本为：Unity2021.3.8f1
Unity提供了WebGL平台来，支持在浏览器中实时对3D图形应用交互。
## 设置
### 模版
Player->Resolution and Presentation->WebGL Template
默认是Default，这个模版有加载进度条，且在程序出错会弹出提示框。还有一个Minimal模版，没有进度条加载等流程，且程序出错无提示信息。
我们也可以自己自定义模版，放到``Editor\Data\PlaybackEngines\WebGLSupport\BuildTools\WebGLTemplates``目录下即可使用。

### 压缩
Player->Publishing Settings->Compression Format。可以设置压缩格式，一般的话会设置Gzip压缩。若无法确保服务端是否支持压缩格式，则建议设置为Disabled

### 代码裁切
Strip Engine Code可以大大降低WebGL文件大小，Unity 会扫描项目中所有从 UnityObject 继承的类型，包括检查它的内部引用以及序列化字段，将会移除没有任何引用的类型，从而减少发布大小，生成的代码也更少、更快、内存占用更少。但是要慎用其裁剪等级，需要多次测试选择合适的等级。[官方文档：Managed code stripping](https://docs.unity3d.com/Manual/ManagedCodeStripping.html)。
对于使用了Assetbundle的项目代码裁剪很容易丢失对类的引用。运行时会看到错误``Could not produce class with ID XXX``,针对这种情况一般可以按如下方式解决：
step1:根据提示 ID，查找裁剪的类型:[ClassIDReference](https://docs.unity3d.com/Manual/ClassIDReference.html)
step2:设置不被裁剪
可以在代码中强制引用一下step1中找到的类型，或者在Assets目录下创建link.xml文件，强制引用，格式如下：
```xml
<linker>
    <assembly fullname="UnityEngine">
        <type fullname="UnityEngine.Collider" preserve="all"/>
    </assembly>
</liner>
```
Strip Engine Code选项只是针对 Unity 的本机代码，对于托管代码来说，总是会进行代码裁剪，IL2CPP 通过托管 DLL，以及代码脚本中的静态引用，来进行代码裁剪，如果代码中使用反射来获取类型，那么同样的，该类型有可能被裁剪掉，因此也需要添加到 linker.xml中。

### 色彩空间
默认是Gamma模式。若要使用Linear模式，则图形API只能选择WebGL 2.0

### WebGL 2.0
WebGL 2.0 于 2017 年首次发布。相比上一版本，WebGL 2.0在图形功能方面实现了很大提升，带来了OpenGL ES 3.0 功能集，为 WebGL 1.0 图形管道添加了对变换反馈、实例渲染、多个渲染目标、统一缓冲区对象、遮挡查询和更广泛的纹理支持的访问。
目前几乎所有主流浏览器都支持WebGL 2.0了(Safari浏览器需要iOS15+才支持WebGL 2.0)。目前笔者使用Unity版本WebGL 1.0旁标示了Deprecated。

### 抗锯齿
默认情况下WebGL设置的品质比较低，锯齿感比较强，可以在性能允许的前提下通过增加Anti Aliasing级别以及设置Quality的级别来增加显示效果。但是在 WebGL 1.0 中存在一些限制：
- 不能在运行时启用/禁用，必须在系统发布时就确定；
- 多重采样（multi sampleing 2x、4x…)没有作用，只有开启、关闭两个状态；
- 如果 Camera 上又任何Post-Processing-Effect，都会引起抗锯齿失效；
- HDR 和抗锯齿是不兼容的，如果开启抗锯齿，就需要关闭 Camera上的 Allow HDR选项。

WebGL2.0 不存在以上限制。

### Enable Exceptions
Player->Publishing Settings/Enable Exceptions提供了异常处理选项
- None 不需要异常支持，性能最好，发布包最小，但是任何错误都会引起系统停止（可以在确认没有bug的情况下再切换到这个设置）；
- Explicitly Thrown Exceptions Only ： 默认设置，能够捕获由代码中 throw 抛出的异常，并且能够正确执行 finally 语句块，这将会引起生成大 JavaScript 代码更长、更慢，但这通常只在代码引起瓶颈时才要考虑；
- Full Without Stacktrace：能够捕捉如下异常：
  - throw 抛出的异常
  - 空引用异常，Null Reference
  - 数组越界
- Full With Stacktrace：和上面比多了一个调用栈信息，通常应该在调试阶段使用，并且在 64 位浏览器中测试。

### Compression Format
Player->Publishing Settings/Enable Exceptions提供了导出文件的压缩处理选项，默认为Gzip
- Brotli
Google 在 2015 年 9 月推出了无损压缩算法 Brotli。Brotli 通过变种的 LZ77 算法、Huffman 编码以及二阶文本建模等方式进行数据压缩，与其他压缩算法相比，它有着更高的压缩效率。
Brotli 压缩算法具有多个特点，最典型的是以下 3 个：
针对常见的 Web 资源内容，Brotli 的性能相比 Gzip 提高了 17-25%；
当 Brotli 压缩级别为 1 时，压缩率比 Gzip 压缩等级为 9（最高）时还要高；
除了 IE 和 Opera Mini 之外，几乎所有的主流浏览器都已支持 Brotli 算法。
[brotli浏览器支持情况查询](https://caniuse.com/?search=brotli).
Brotli压缩速度慢，且只支持https协议。
- Gzip
Gzip 基于 DEFLATE 算法，它是 LZ77 和霍夫曼编码的组合，最早用于 UNIX 系统的文件压缩。HTTP 协议上的 Gzip 编码是一种用来进 Web 应用程序性能的技术，Web 服务器和客户端（浏览器）必须共同支持 Gzip，当下主流的浏览器都是支持 Gzip 压缩。
- Disabled
不压缩，包体会比较大

服务端的配置可以参考如下官方文档：
[Compressed builds and server configuration](https://docs.unity3d.com/Manual/webgl-deploying.html?_ga=2.163659836.2009813986.1571904029-1030292064.1564583003)
[Server configuration code samples](https://docs.unity3d.com/Manual/webgl-server-configuration-code-samples.html)
### Data Caching
默认启用，会使用资源缓存到浏览器的 IndexedDB 数据库，在后面运行的时候不用再次从服务器下载资源，不同浏览器的缓存策略也不尽相同。如果只是想进行空引用检查和数组越界检查，而不想完全支持异常，可以通过如下代码来支持：
```csharp
using UnityEditor;

public class WebGLEditorScript
{
    [MenuItem("WebGL/EnableNullChecks")]
    public static void EnableNullChecks()
    {
        PlayerSettings.SetPropertyString("additionalIl2CppArgs", "--emit-null-checks --enable-array-bounds-check",UnityEditor.BuildTargetGroup.WebGL);
    }
}
```
## AssetBundles
WebGL 在启动之前，需要将所有的资源预加载完成，因此减少启动时间的一个有效方式就是减少系统资源，或者说减少发布资源，可以利用 AssetBundle 将资源从主包中分离出来，这样，只需要加载一个非常小的加载场景即可，另外 AssetBundle 还能帮助资源管理。

在 WebGL 平台使用 AssetBundle 有以下几点需要注意：
- AssetBundle 中包括主包中没有的类型时，会引起资源加载失败，最好是在 AssetBundle 中不要打包新类型；
- WebGL 不支持多线程，而AssetBundle 数据在 Http 下载完成后才可用，因此，AssetBundle 就需要在主线程进行解压缩，这会引起主线阻塞，LZMA 在 WebGL 平台是不可用的，因为它是整包压缩，可以采用 LZ4 压缩，它是单个资源独立压缩，即加载单个资源时，不需要解压整个资源包，如果需要更小的资源包格式，可以采用 gzip 或者 brotli 进行二次压缩，不过这需要 web 服务器进行相应的配置；
- 支持 WWW.LoadFromCacheOrDownload 方法，它采用浏览器的 IndexedDB 来实现缓存；

## 字体
WebGL 支持动态字体，但是由于它不能访问本机文件系统，所以使用到的字体文件必须放到项目当中。（包括 Fallback fonts，以及粗体和斜体字体），另外需要注意的是，默认的Arial字体在浏览器中不支持中文（Arial字体本来就不支持中文，只是在PC或者移动端终端失败后会在设备中寻找合适字体进行替代），需要自行替换字体以支持。

为了提高性能，建议在 WebGL 中使用 TMP 来替代默认的 UGUI 文本组件，使用静态字体来替代动态字体。

## 发布
### 项目文件结构
以Default模版为例，目录结构为
- Build
已翻译为js的源码和相关插件
- StreamingAssets
项目中的 StreamingAssets 文件夹(WebGL平台下 Application.streamingAssetsPath 的值为 http://youer_host_url:port/StreamingAssets)
- TemplateData
包含Default模版需要的logo、进度条等UI。Minimal模版下无此文件夹。
- index.html

### 持久化数据
Unity WebGL将所有持久化数据（PlayerPrefs、缓存）都保存到浏览器的 IndexedDB 中，在开发者工具中，可以通过 Application 标签页查看，这一API 是异步的，所以不知道什么时候能够完成。因此可能会遇到报错：``Problem: Files saved to Application.persistentDataPath do not persist``

### 运行index.html
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
这是服务端未支持Gzip导致的。可以通过禁用压缩，或者设置Decompression Fallback来规避（实质是在发布包内集成了一个对应压缩方式的js解压工具，在浏览器解压失败会使用该工具进行解压，这样会增加加载的时间）。
若是要求使用压缩且发布包不想集成解压工具，则可以按照[官方文档：Server configuration code samples](https://docs.unity3d.com/2021.3/Documentation/Manual/webgl-server-configuration-code-samples.html)把以下配置添加到conf/nginx.conf中：
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



### WebGL稳定性
- 不支持多线程，因为JavaScript不支持多线程，所以System.Threading命名空间下的类无法使用
- 网络协议不支持Socket。若要在WebGL下使用网络功能可以考虑使用HTTP协议，WebSocket或者WebRTC;也可以使用自带的WWW或者UnityWebRequest,它们是基于HTTP协议实现的。
- WebGL 1.0基于OpenGL ES 2.0，WebGL 2.0基于OpenGL ES 3.0，所以存在一定的限制。
- WebGL音频是基于自定义的后台，因此只具备基本的音频功能。
- WebGL是AOT，因此不能使用System.Reflection.Emit下的类型进行代码生成。

浏览器厂商为了稳定性，一般采用黑名单/白名单的方式，针对不同的显卡驱动进行功能限制，在 chrome 中可以通过输入 chrome://gpu 来查看当前 gpu 的状态，或者可以在[BlacklistsAndWhitelists](https://www.khronos.org/webgl/wiki/BlacklistsAndWhitelists)查询；

## 参考文档
[Unity官方文档：WebGL](https://docs.unity3d.com/2021.3/Documentation/Manual/webgl.html)
[Unity WebGL 开发指北（完全篇）](https://zhuanlan.zhihu.com/p/475307249)