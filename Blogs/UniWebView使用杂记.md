UniWebView是OneVCat大佬开发的适用于anroid、iOS和Mac上的Unity内置浏览器插件，目前笔者用下来觉得挺好。

iOS使用的是[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview)，安卓使用的[WebView](https://developer.android.com/reference/android/webkit/WebView.html)

- iOS设备上html页面未自适应，安卓没问题

html页面加上如下标签：
``<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">``