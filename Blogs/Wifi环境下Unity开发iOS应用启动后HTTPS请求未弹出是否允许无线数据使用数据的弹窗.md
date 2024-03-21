## 情况说明
笔者项目在首次启动，登录界面点击登录按钮会先HTTPS请求创建帐号，但是在WIFI网络下，请求后一直提示网络连接失败。但是切换到流量包后，则会弹出``"无线数据"使用数据``的弹窗，选择允许后则可顺利进入。


## 分析
错误原因就是在WIFI环境下HTTPS请求未能弹出权限界面，导致当前``无线数据``的权限是关闭的，而使用流量时又可以内部触发弹出权限选择界面。

具体的原因网上说法也很多，比如：
- WIFI设置了代理，流量未设置代理。导致WIFI环境下的HTTPS请求被拦截，未能弹出选择窗口。
- Info.list下未配置NSAllowsArbitraryLoads权限为true

上述笔者这边都排除了，但是有个可能是笔者这边HTTPS请求是使用的是一个比较老的稳定性挺好，且用于多个线上项目的Best HTTP插件，有可能是Unity升级后插件适配的问题？

## 解决方案
一般而言App首次启动，网络请求时都会弹出``是否允许使用数据``的弹窗。但是这种坑就遇到了，又不想换Best HTTP插件。

遂写了个iOS插件，在进入登录界面时模拟一次网络请求。代码如下：
```c
#import <Foundation/Foundation.h>

@interface IWebRequestPermission : NSObject
 
#ifdef __cplusplus
extern "C" {
#endif
    BOOL IsConnectedToInternet();
    void TryRequestPermissionIfNeeded();
#ifdef __cplusplus
}
#endif
@end

```
```c
#import "WebRequestPermission.h"
// 引入 SystemConfiguration.framework
#import <SystemConfiguration/SystemConfiguration.h>

@implementation IWebRequestPermission
// 检查当前网络连接状态
BOOL IsConnectedToInternet() {
    BOOL isConnected = NO;
    SCNetworkReachabilityRef reachability = SCNetworkReachabilityCreateWithName(NULL, "www.baidu.com");
    SCNetworkReachabilityFlags flags;
    if (SCNetworkReachabilityGetFlags(reachability, &flags)) {
        isConnected = ((flags & kSCNetworkReachabilityFlagsReachable) != 0);
    }
    CFRelease(reachability);
    return isConnected;
}

// 在应用启动时尝试发起一个简单的网络请求
void TryRequestPermissionIfNeeded() {
    if (IsConnectedToInternet()) {
        NSURL *url = [NSURL URLWithString:@"https://www.baidu.com"];
        NSURLSessionDataTask *dataTask = [[NSURLSession sharedSession] dataTaskWithURL:url completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
            // 处理网络请求的结果
        }];
        [dataTask resume];
    }
}
@end

```

在进入登录界面时Unity侧调用TryRequestPermissionIfNeeded()即可