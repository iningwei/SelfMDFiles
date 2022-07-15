## 简介
adb全称为Android Debug Bridge,起调试桥的作用。

## 安装
1：将Android SDK中的platform-tools路径加到系统环境变量中
2：也可以不安装，每次都全目录来执行cmd命令

## 常用命令
adb help 查看adb相关信息，包括版本号，常用命令等等
adb version 查看adb版本
adb install abc.apk 安装apk，一般虚拟机安装的时候使用该命令
adb devices 列出当前连接到主机的设备，可以用该命令查看手机是否和电脑通过usb连接了，比如用于Unity Remote联调判断连接情况
adb logcat 捕获设备的日志信息
adb logcat -s Unity 专门获得Unity相关的日志（注意U要大写）
adb shell 获取设备的shell环境
adb shell dumpsys meminfo com.test 查看正在运行的的安卓设备的程序的内存占用情况

## 查看CPU架构
adb shell getprop ro.product.cpu.abi
示例输出：armeabi-v7a