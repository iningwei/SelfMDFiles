## 前言
android5.0（API Level21）后（不包括5.0）,为了统一系统风格，状态栏（手机顶部）icon不能再使用色彩丰富的图片了，只能够有白色和透明两个颜色出现。通知栏图标会自动把icon的透明通道填充为白色，非透明通道默认情况下会被设置为系统默认的颜色（比如铅灰色）。
虽然可以通过降低targetSdkVersion的方式来实现显示彩色icon的功能，但是考虑到5.0系统已经很古老，不推荐这样设置了。
因此在设计推送图标的时候就不可能像APP图标那样有丰富的色彩，一般而言需要遵循：只有白色和透明，不要出现渐变、半透明，图片格式为png。

## Unity接入FCM的设置
1，图标
png格式的图标放到Plugins/Android/res/drawable目录下，名字随意（不要使用汉字和特殊字符），比如：icon_notification.png。
AndroidManifest.xml配置:``<meta-data android:name="com.google.firebase.messaging.default_notification_icon" android:resource="@drawable/icon_notification" />``
2，通知栏中icon非透明通道颜色设置
上面也提到在通知栏中，系统会自动填充透明通道为白色，非透明通道默认会被设置为一个系统默认的颜色。在FCM SDK中可以通过配置meta-data来设置非透明通道的颜色。如下：
```xml
<meta-data android:name="com.google.firebase.messaging.default_notification_color"
        android:resource="@color/colorNotifyBg" />
```
上面使用到了color/colorNotifyBg资源，可以通过在Plugins/Android/res/values目录下创建colors.xml文件来配置colorNotifyBg,colors.xml内容形如：
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
	<color name="colorNotifyBg">#ff8400</color>
    <color name="colorPrimary">#039BE5</color>
    <color name="colorPrimaryDark">#0288D1</color>
    <color name="colorAccent">#FFA000</color>
	
    <color name="blue_grey_500">#607D8B</color>
    <color name="blue_grey_600">#546E7A</color>
    <color name="blue_grey_700">#455A64</color>
    <color name="blue_grey_800">#37474F</color>
    <color name="blue_grey_900">#263238</color>
</resources>
```

上述两个设置在[官方demo的AndroidManifest.xml](https://github.com/firebase/quickstart-android/blob/master/messaging/app/src/main/AndroidManifest.xml)中都有提到。
同时需要注意，官方demo中创建了多个带后缀(mdpi、hdpi、xhdpi、xxhdpi等)的drawable目录：[这里](https://github.com/firebase/quickstart-android/tree/master/messaging/app/src/main/res)，用以存放不同分辨率的icon图片资源，用来更好适配不同分辨率机型。由于低分辨率机型已经很少了，因此我们就只创建了一个drawable目录，使用64*64分辨率的icon图片。


