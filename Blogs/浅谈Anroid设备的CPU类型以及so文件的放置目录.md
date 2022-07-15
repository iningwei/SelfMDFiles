早期的Android系统几乎只支持ARMv5的CPU架构。现在发展为了七种：ARMv5，ARMv7 (从2010年起)，x86 (从2011年起)，MIPS (从2012年起)，ARMv8，MIPS64和x86_64 (从2014年起)，每一种CPU架构都关联一种ABI。

ABI即``Application Binary Interface``，定义了二进制接口（比如so文件）交互规则，以适用于不同CPU。在Android系统上，每一个CPU架构对应一个ABI。

## Android ABI
Android目前支持以下七种ABI：armeabi、armeabi-v7a、arm64-v8a、x86、x86_64、mips、mips64。主流的ABI包括：
- armeabiv-v7a: 第7代及以上的 ARM 处理器。2011年15月以后的生产的大部分Android设备都使用它
- arm64-v8a: 第8代,64位ARM处理器，很少设备，三星 Galaxy S6是其中之一
- armeabi: 第5代、第6代的ARM处理器，早期的手机用的比较多
- x86: 平板、模拟器用得比较多 （2011年起）
- x86_64: 64位的平板 （2014年起）


mips和mips64很少用，可以忽略

## NDK
NDK是啥？当你的Android工程中用到 C/C++ 代码时就会使用 NDK 这个开发工具包进行开发。

NDK即Native Development Kit，属于Android，与Java无直接联系，是一组可以让您在Android应用中快速开发 C/C++ 的动态库，并自动将 so 和应用一起打包成 apk。NDK是Android中实现JNI的手段，JNI即Java Native Inteface使得Java与本地其他类型语言（如C/C++）进行交互。

NDK以前支持mips，mips64，armeabi，但在NDK r17 移除了对这些ABI的支持。

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/v2-d4104b649147df48a11da7f91be47569_720w.png)

因为NDK向前兼容（前，forward，未来），推荐使用app的minsdkVersion对应的编译平台，而不是最新。

## ABI与CPU
大多数CPU都支持多种ABI，但是为了获得最佳性能，最好使用CPU的主要ABI。如同时存在多个ABI（比如so文件），会安装最优ABI，其他的不会安装。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/v2-947da210f0ee54880431b58761616e3e_720w.png)
64位设备（arm64-v8a, x86_64, mips64）能够运行32位的so库。但是以32位模式运行时，会丢失专为64位优化过的性能特征（ART, WebView, Media, etc.）。

很多设备都支持多于一种的ABI，例如ARM64和x86设备也可以同时运行armeabi-v7a和armeabi的二进制包。但最好是针对特定平台提供相应平台的二进制包，这种情况下运行时就少了一个模拟层（例如x86设备上模拟arm的虚拟层），从而得到更好的性能（归功于最近的架构更新，例如硬件fpu，更多的寄存器，更好的向量化等）。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20220715182916.png)

### x86设备上，选择ABI的优先级
1，libs/x86目录中若是存在.so文件的话，会被安装。
2，若是不存在，则会选择armeabi-v7a中的.so文件。
3，若是也不存在，则选择armeabi目录中的.so文件。

### arm设备上CPU 是arm64-v8a，ABI的优先级svg

1，若是手机CPU 是arm64-v8a，优先选择arm64-v8a中的.so文件。
2，若是不存在，则会选择armeabi-v7a中的.so文件。
3，若是也不存在，则选择armeabi目录中的.so文件。
### arm设备上CPU 是armeabi-v7a，ABI的优先级工具

1，若是手机CPU 是armeabi-v7a，优先选择armeabi-v7a中的.so文件。
2，若是不存在，则会选择armeabi中的.so文件。 

## so文件应该放在什么地方

## 配置so的建议
### 针对armeabi和armeabi-v7a两种ABI
方法1：由于armeabi-v7a指令集兼容armeabi指令集，所以如果损失一些应用的性能是可以接受的，同时不希望保留库的两份拷贝，可以移除armeabi-v7a目录和其下的库文件，只保留armeabi目录；比如Apk使用第三方的so只有armeabi这一种ABI时，可以考虑去掉Apk中lib目录下armeabi-v7a目录。

方法2：在armeabi和armeabi-v7a目录下各放入一份so。

### 针对x86
目前市面上的x86机型，为了兼容arm指令，基本都内置libhoudini模块，即二进制转码支持，该模块负责把ARM指令转换为x86指令，所以如果是出于Apk包大小的考虑，并且可以接受一些性能损失，可以选择删掉x86库目录，x86下配置的armeabi目录的so库一样可以正常加载使用。

### 针对64位ABI
如果App开发者打算支持64位，那么64位的so要放全，否则可以选择不单独编译64位的so，全部使用32位的so，64位机型默认支持32位so的加载。比如Apk使用第三方的so只有32位ABI的so，可以考虑去掉Apk中lib目录下的64位ABI子目录，保证Apk安装后正常使用。

### 目前通用做法
由于mips市场占有率很低，x86又兼容了armeabi，因此只须要支持arm 架构便可,而arm中arm64-v8a的cpu架构又向下兼容armeabi-v7a与armeabi，arm-v7a的架构也兼容 armeabi 。所以看起来只需要使用armeabi即可覆盖市面上几乎全部机型，但是只使用armeabi的话不止有性能问题，在一些老旧设备不一定保证不crash。
目前推荐的做法是只支持armeabi和armeabi-v7a，保证两个文件夹下的.so文件数量一致，这样既能保证兼容性又能最大化的降低apk的体积。对于只提供了armeabi版本的第三方.so，原样复制一份到armeabi-v7a文件夹。


## 其它
Native Libs Monitor这个应用可以帮助我们理解手机上安装的APK用到了哪些.so文件，以及.so文件来源于哪些函数库或者框架。