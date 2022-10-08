### Step1:git仓库创建个人access token

Settings->Developer settings->Personal access tokens->Generate new token

然后为Token取个名字，可以为所有项目使用一个token，也可以一个项目仓库一个token。

设置Token过期时间，若是个人仓库，为避免麻烦，完全可以设置永久时限。

设置Token拥有的权限，一般勾选repo,gist,user,workflow权限就够了，当然为了最大兼容也可以勾选所有权限。

最后点击Generate token生成token，需要记住该token，因为界面关闭后就无法再访问了。

### Step2:
再次push，遇到输入用户名密码的界面，输入github帐号的用户名和密码；再次弹出一个输入密码的对话框，输入上述获得到的token即可提交成功。

20221008 PC的access token又改成：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