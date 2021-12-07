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
经过多次测试是App首次运行FCM在尝试主动获取Token时因为网络原因（无网络、或者因为墙无法连通Google）导致的崩溃，[笔者在在官方GitHub上提了Issues，在一些朋友的帮助下也得到了解决](https://github.com/firebase/firebase-unity-sdk/issues/73)

### 注意事项
FirebaseApp.androidlib目录是打包过程中自动生成的必需目录。


 