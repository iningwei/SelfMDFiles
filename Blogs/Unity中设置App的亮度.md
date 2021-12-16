现在的移动设备大多加入了光感、省电模式，这些都会动态的影响屏幕的亮度，我们的需求是改变App的亮度，而不是从系统级上控制设备的亮度。
在安卓上可以通过以下步骤来实现：
1，封装jar
AndroidTools.java:
```java
    public class AndroidTools {
    //设置app的屏幕亮度（只影响对应App，不影响其它App）
    //注意：unity中直接调用会报错，可以开启runOnUiThread进行调用
    //brightnessValue范围[0,255]
    public static void SetAppScreenBrightness(Activity activity, int brightnessValue)
    {
        Window window=activity.getWindow();
        WindowManager.LayoutParams lp=window.getAttributes();
        lp.screenBrightness=brightnessValue/255.0f;
        window.setAttributes(lp);
    }
}
```
2，Unity中调用
```csharp
            var androidTools = new AndroidJavaClass("XX.XX.XX.AndroidTools");

            using (AndroidJavaClass jc = new AndroidJavaClass("com.unity3d.player.UnityPlayer"))
            {
                var jo = jc.GetStatic<AndroidJavaObject>("currentActivity");

                //SetAppScreenBrightness函数直接调用会报错---->：
                //c# exception:UnityEngine.AndroidJavaException: android.view.ViewRootImpl$CalledFromWrongThreadException: 
                //Only the original thread that created a view hierarchy can touch its views.
                jo.Call("runOnUiThread", new AndroidJavaRunnable(()=> {
                    androidTools.CallStatic("SetAppScreenBrightness", jo, value);
                }));
            }
```

经笔者测试可以实现需求，且不需要任何安卓权限上的设置，切换到桌面或者其它App不影响系统级的亮度设置。该设置是不存档的，当重启App后，使用的是系统级的亮度。


## 搬运一个老外的博文（包含Android和iOS上获取和设置亮度的代码）
- iOS native plugin
BrightnessPlugin.m, iOS中不需要权限申请
```objc
#import <Foundation/Foundation.h>
#import <UIKit/UIKit.h>
 
#ifdef __cplusplus
extern "C" {
#endif
 
void setBrightness(float val) { [UIScreen mainScreen].brightness = val; }

float getBrightness() { return [UIScreen mainScreen].brightness; }

#ifdef __cplusplus
}
#endif
```

- Android代码以及iOS的调用代码
使用了在C#侧直接调用安卓代码的方式
```csharp
namespace ZGame.SDK
{
    public class AppBrightness
    {
#if !UNITY_EDITOR && UNITY_IOS
        [DllImport("__Internal")]
        private static extern void setBrightness(float brightness);

        [DllImport("__Internal")]
        private static extern float getBrightness();
#endif





        /// <summary>
        /// 
        /// </summary>
        /// <param name="brightness"></param>
        public static void Set(float brightness)
        {

#if UNITY_EDITOR

#elif UNITY_IOS
                        setBrightness(brightness.Value);
#elif UNITY_ANDROID
//这里设置的是当前运行App的亮度，并不会影响到其它App和系统的亮度，切到其它界面后，会恢复到系统的亮度
            var unityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
            var activity = unityPlayer.GetStatic<AndroidJavaObject>("currentActivity");

            activity.Call("runOnUiThread", new AndroidJavaRunnable(() =>
            {
                var window = activity.Call<AndroidJavaObject>("getWindow");
                var lp = window.Call<AndroidJavaObject>("getAttributes");
                lp.Set("screenBrightness", brightness);
                window.Call("setAttributes", lp);
            }));

#endif

        }


        /// <summary>
        /// 
        /// </summary>
        /// <returns></returns>
        public static float Get()
        {
            float brightness = 1f;
#if UNITY_EDITOR
#elif UNITY_IOS
            brightness = getBrightness();
#elif UNITY_ANDROID
//需要注意的是这里获得的是屏幕亮度，即系统级的亮度。安卓中这部分操作不需要权限
            var unityPlayer = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
            var activity = unityPlayer.GetStatic<AndroidJavaObject>("currentActivity");
            var system = new AndroidJavaClass("android.provider.Settings.System");
            var contentResolver = activity.Call<AndroidJavaObject>("getContentResolver");
            var b = system.CallStatic<int>("getInt", contentResolver, system.CallStatic<string>("SCREEN_BRIGHTNESS"));
            brightness = b / 255f;
#endif
            return brightness;
        }
    }
}

```

