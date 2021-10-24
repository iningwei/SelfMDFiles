### Step1:git仓库创建个人access token

Settings->Developer settings->Personal access tokens->Generate new token

然后为Token取个名字，可以为所有项目使用一个token，也可以一个项目仓库一个token。

设置Token过期时间，若是个人仓库，为避免麻烦，完全可以设置永久时限。

设置Token拥有的权限，一般勾选repo就够了，当然为了最大兼容也可以勾选所有权限。

最后点击Generate token生成token，需要记住该token，因为界面关闭后就无法再访问了。

### Step2:使用命令
在某个具体项目仓库下的git面板内输入命令：
```c
// <your_token>：包括<>在内的全部字符替换成你的token
// <USERNAME>：包括<>在内的全部字符替换成你的username
// <REPO>：包括<>在内的全部字符替换成你要访问的仓库名称,注意是仓库名称，不是仓库url
git remote set-url origin  https://<your_token>@github.com/<USERNAME>/<REPO>.git
```