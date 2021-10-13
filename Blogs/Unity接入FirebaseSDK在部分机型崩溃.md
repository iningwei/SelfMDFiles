## 情况说明
接入FirebaseAnalytics和FirebaseMessaging后一直在一款红米机器上顺利测试，并没有在大量安卓机上进行测试。出包后在多款机器上出现闪退现象。
报错日志如下：
```c
2021-10-08 16:40:29.983 26454-26494/? E/AndroidRuntime: FATAL EXCEPTION: UnityMain
    Process: com.tj.sgame, PID: 26454
    java.lang.Error: *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ***
    Version '2020.3.0f1 (c7b5465681fb)', Build type 'Release', Scripting Backend 'il2cpp', CPU 'arm64-v8a'
    Build fingerprint: 'HUAWEI/EVA-AL10/HWEVA:8.0.0/HUAWEIEVA-AL10/550(C00):user/release-keys'
    Revision: '0'
    ABI: 'arm64'
    Timestamp: 2021-10-08 16:40:29+0800
    pid: 26454, tid: 26590, name: Thread-10  >>> com.tj.sgame <<<
    uid: 10172
    signal 11 (SIGSEGV), code 1 (SEGV_MAPERR), fault addr 0x8
    Cause: null pointer dereference
        x0  0000000000000008  x1  0000007d880fcd90  x2  0000000000000004  x3  0000007d880fce09
        x4  0000007d880fcd48  x5  0000000000000000  x6  455e5e6472606164  x7  7f7f7f7f7f7f7f7f
        x8  0000000000000000  x9  0000007d880fce09  x10 0000007db6243040  x11 0000000000000004
        x12 0000007d88695e08  x13 0000000000002f34  x14 0000007dc0909320  x15 0000000000000000
        x16 0000007da13af3a8  x17 0000007dc49ce21c  x18 0000007d880fd160  x19 0000000000000000
        x20 0000007d880fcd90  x21 0000007d880fcd48  x22 0000000000000000  x23 0000007d880fce09
        x24 0000000000000004  x25 0000000000000008  x26 0000007d880fce08  x27 0000000000000001
        x28 0000000000000009  x29 0000007d880fcf78
        sp  0000007d880fccf0  lr  0000007da12823ac  pc  0000007dc49ce21c
    


backtrace:
      #00 pc 000000000006821c  /system/lib64/libc.so (pthread_mutex_lock) (BuildId: dddfcdc7a39b763aede758813967236d)
      #01 pc 00000000001fe3a8  /data/app/com.tj.sgame-F_nYXx3cy_Bo3IODB5fmeg==/lib/arm64/libFirebaseCppApp-8_3_0.so (firebase::Mutex::Acquire()+4) (BuildId: 8209be713e18b8cc0f0f9cdc20b6d1f4)
      #02 pc 0000000000203c8c  /data/app/com.tj.sgame-F_nYXx3cy_Bo3IODB5fmeg==/lib/arm64/libFirebaseCppApp-8_3_0.so (void firebase::ReferenceCountedFutureImpl::CompleteInternal<std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> >, void firebase::ReferenceCountedFutureImpl::CompleteWithResultInternal<std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> > >(firebase::FutureHandle const&, int, char const*, std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> > const&)::'lambda'(std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> >*)>(firebase::FutureHandle const&, int, char const*, void firebase::ReferenceCountedFutureImpl::CompleteWithResultInternal<std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> > >(firebase::FutureHandle const&, int, char const*, std::__ndk1::basic_string<char, std::__ndk1::char_trai...
      #03 pc 0000000000203c40  /data/app/com.tj.sgame-F_nYXx3cy_Bo3IODB5fmeg==/lib/arm64/libFirebaseCppApp-8_3_0.so (void firebase::ReferenceCountedFutureImpl::CompleteWithResultInternal<std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> > >(firebase::FutureHandle const&, int, char const*, std::__ndk1::basic_string<char, std::__ndk1::char_traits<char>, std::__ndk1::allocator<char> > const&)+64) (BuildId: 8209be713e18b8cc0f0f9cdc20b6d1f4)
      #04 pc 0000000000270820  /data/app/com.tj.sgame-F_nYXx3cy_Bo3IODB5fmeg==/lib/arm64/libFirebaseCppApp-8_3_0.so (firebase::messaging::CompleteStringCallback(_JNIEnv*, _jobject*, firebase::util::FutureResult, char const*, void*)+136) (BuildId: 8209be713e18b8cc0f0f9cdc20b6d1f4)
      #05 pc 0000000000286c18  /data/app/com.tj.sgame-F_nYXx3cy_Bo3IODB5fmeg==/lib/arm64/libFirebaseCppApp-8_3_0.so (firebase::util::JniResultCallback_nativeOnResult(_JNIEnv*, _jobject*, _jobject*, unsigned char, unsigned char, _jstring*, long, long)+172) (BuildId: 8209be713e18b8cc0f0f9cdc20b6d1f4)
      #06 pc 00000000000023d4  /data/data/com.tj.sgame/cache/oat/arm64/app_resources_lib.odex

    at libc.pthread_mutex_lock(pthread_mutex_lock:0)
    at libFirebaseCppApp-8_3_0.firebase::Mutex::Acquire()(Acquire:4)
```
根据报错日志关键词，在Firebase的github上也有很多相关雷同的bug讨论，但是都没有给出妥帖的解决方法，最后都是因为问题超时被管理员关闭。

## 分析
笔者手上有3款测试机，都是国产机器，情况如下：
机器1，CPU架构armeabi-v7a，  安装了GP服务，    开梯子，    不崩溃且能获得FCM Token
机器2，CPU架构armeabi-v7a，  未安装GP服务，    不开梯子，  不崩溃
机器3，CPU架构arm64-v8a，    未安装GP服务，    不开字体，  崩溃报错如上所示
因此初步分析在arm64架构上有问题。

## 解决办法
笔者一时半会儿找不到解决方向，于是新建了个空工程，导入分析和云推送这两个SDK。同样使用继承了MessagingUnityPlayerActivity的Activity作为启动Activity（项目中是这样使用的）。竟然在机器3上没有崩溃，但是会报错，错误信息为（假设该错误信息为X）：``Failed to read Firebase options from the app's resources. Either make sure google-services.json is included in your build or specify options explicitly.``。尝试在机器1上运行，报一样的错，且无法获得FCM Token。

于是对两个工程进行比较，发现项目工程的Plugins/Android目录下有个``FirebaseApp.androidlib``目录，里面有AndroidManifest.xml文件、google-services.xml等文件。对项目工程手动删除该目录后打包，发现在机器3上APP已经不崩溃了，但是会报错，错误信息X。但是在机器1上也会报错信息X，且无法获得FCM Token，因此可以知道FirebaseApp.androidlib是必须的目录。

### 注意事项
笔者发现在删除FirebaseApp.androidlib目录后，后续打包的时候有概率会自动生成该目录，当打包完成后工程中生成了该目录，那么这个包就会在机器3上崩溃，需要删除该目录再打包。



 