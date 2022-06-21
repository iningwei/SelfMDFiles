## 官方文档
官方文档:[Facebook Login For Gaming](https://developers.facebook.com/docs/games/acquire/login-for-gaming)

总结如下：
1，你的FB账户内还有一个游戏账户，在Unity中使用FB的官方SDK提供的接口进行登录时，比如
```csharp
var perms = new List<string>() { "gaming_profile", "email" };
FB.LogInWithReadPermissions(perms, authCallback);
```
当使用gaming_profile权限登录，拉起的登录界面只有游戏账户选项；当使用gaming_user_picture权限登录，拉起的登录界面既有FB账户选项又有游戏账户选项。

2，不同的登录选项会获得不同的头像和玩家名
比如，当玩家通过游戏账户登录，那么后续通过FB.API获得的头像是游戏账户的头像，获得的name是游戏账户设置的昵称
因此要想获得FB的用户头像，需要玩家自己选择使用FB帐号登录，即若玩家使用的是游戏账户登录，那么是无法获得其FB帐号的头像、昵称信息的

3，FB后台可以设置当使用gaming_user_picture权限时，拉起的登录界面的默认选项

## 获取头像
查阅了一些资料，笔者尝试了几种方式去获取用户头像
法一：通过构建形如 http://graph.facebook.com/user_id/picture?width=240&height=240&type=square&access_token=xxx 的URL（URL中设置的宽高并未生效），在网页中可以直接下载，但是通过UnityWebRequest无法下载，因此不可取


法二：通过构建形如：https://graph.facebook.com/user_id/?fields=picture&width=240&height=240&type=square&access_token=XXX 的URL(URL中设置的宽高并未生效)，通过UnityWebRequest获得一个JSON字符串，形如：
```json
{
	"picture": {
		"data": {
			"height": 600,
			"is_silhouette": false,
			"url": "https://platform-lookaside.fbsbx.com/platform/profilepic/?asid=102473365568759&gaming_photo_type=unified_picture&ext=1640317531&hash=AeSOOQjV1NjyT5HUu8A",
			"width": 600
		}
	},
	"id": "102473365568759"
}
```
通过json中的url再通过UnityWebRequest下载图片。

法三：使用FB for Unity的SDK提供的FB.API接口获得图片的URL
在上述authCallback回调中，调用诸如`` FB.API("/me?fields=first_name,last_name,gender,email,picture", HttpMethod.GET, graphCallback);``获得昵称、邮件、头像信息。
头像信息是string类型的URL，形如法二中JSON数据中的url。
获得后用户可以把该URL告之服务端，后续涉及头像的地方只需要服务端下发是系统头像还是URL头像，是URL头像的话把该URL带给你，然后你通过UnityWebRequest即可实现下载。
当然为了避免重复下载，做个本地的缓存机制还是需要的~