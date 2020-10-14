[TOC]

## 创建Github仓库
七牛云虽然有每个月10G的流量，但是需要绑定域名、域名备案，不然就要忍受其1个月的试用期，以及每个月进行资源转移的痛苦。因此选择使用Github免费图床。
- Step1:创建github仓库（repository）,用来充当图床的角色
- Step2:获得Token

点击头像下拉菜单中的``Settings``，选择``Developer settings``，再选择``Personal access tokens``。点击``Generate new token``按钮，输入描述文字，并勾选repo。
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201014181613.png)

```
创建成功后会生成一串token，一定要第一时间给它保存了（后续配置PicGo上传Github图床的时候会用到），因为它只会显示一次，之后便不再显示了。
```
## 配置PicGo
PicGo是开源软件，Github上搜索下载个稳定版即可。
### 配置Github图床
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201014182158.png)
- 设定仓库名，按照``用户名/仓库名``的格式
- 设定分支名，一般来说填写``master``
- 设定Token，即上面取到的token串
- 指定存储路径，设置图片上传到github中的目录
- 设定自定义域名。PicGo在上传图片成功后，会将“自定义域名+上传的图片名”生成访问链接，并放到系统的剪切板上。自定义域名的格式为：``https://raw.githubusercontent.com/用户名/仓库名/分支名``

### 快捷键设置
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20201014182954.png)

可以按照上图进行配置，另外建议把快捷键设置为``ctrl+shift+c``，即快捷上传键。会自动把剪切板中的图片上传到github中，并把生成的图片链接替换到剪切板中。

## Markdown编辑工具
推荐使用Typora和VSCode。

Typora不支持双边预览，而是一种更高级的所见即所得，用户在离开正在编辑的有格式的文本段后，Typora会自动隐藏Markdown标记，这种技术称之为Hybrid View。

VSCode可以通过配置一些MD的语法插件、渲染插件，实现强大的功能。


