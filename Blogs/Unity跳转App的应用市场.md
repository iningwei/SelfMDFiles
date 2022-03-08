APP应用市场提供了专门的格式URL用于应用市场的跳转。
安卓中的格式为：market://details?id=``安卓应用包名``
IOS中的格式为：itms-apps://itunes.apple.com/app/id``苹果应用APPID``。

一般情况下Unity中可以通过Application.OpenURL(url)的方式，直接调用上述专用格式URL即可打开。
## 安卓平台
但是对于安卓平台，由于碎片化，机器中可能会安装有多个应用市场，这样的话直接使用Unity的API会随机打开一个应用市场。
可以通过下述方法打开目标市场：
```csharp
    /// <summary>
    /// 打开安卓商店
    /// </summary>
    /// <param name="appPackage">应用市场中目标app包名</param>
    /// <param name="marketPackage">应用市场包名</param>
    public static void JumpAndroidMarketShop(string appPackage, string marketPackage)
    {
        if (!Application.isEditor)
        {
            AndroidJavaClass intentClass = new AndroidJavaClass("android.content.Intent");
            AndroidJavaObject intentObject = new AndroidJavaObject("android.content.Intent");
            intentObject.Call<AndroidJavaObject>("setAction", intentClass.GetStatic<string>("ACTION_VIEW"));
            AndroidJavaClass uriClass = new AndroidJavaClass("android.net.Uri");
            AndroidJavaObject uriObject = uriClass.CallStatic<AndroidJavaObject>("parse", "market://details?id=" + appPackage);
            intentObject.Call<AndroidJavaObject>("setData", uriObject);
            intentObject.Call<AndroidJavaObject>("setPackage", marketPackage);
            AndroidJavaClass unity = new AndroidJavaClass("com.unity3d.player.UnityPlayer");
            AndroidJavaObject currentActivity = unity.GetStatic<AndroidJavaObject>("currentActivity");
            currentActivity.Call("startActivity", intentObject);
        }
    }
```
appPackage即市场中目标app的包名，marketPackage为应用市场的包名，比如要打开GoogleStore，则marketPackage需要传入：``com.android.vending``。

补充资料：
[App内跳转应用市场详情](https://www.jianshu.com/p/064db6556c27)
[Android应用市场和应用包名大全](https://www.jianshu.com/p/cfb7f212a5a2)
[Android打开第三方应用方法总结](https://www.jianshu.com/p/2a5dfbdaf833)
