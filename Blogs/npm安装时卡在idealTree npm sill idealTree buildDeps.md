安装依赖项时卡在``idealTree:npm:sill idealTree buildDeps``，然后过段时间开始报其它错误。
解决方法：
- 1.删除用户界面下的npmrc文件（注意一定是用户C:\Users\{账户}\下的.npmrc文件下不是nodejs里面）
- 2.清除缓存，注意不要用npm cache clean --force，容易出现npm WARN using --force I sure hope you know what you are doing.要用：npm cache verify
- ３.设置镜像源：npm config set registry https://registry.npmmirror.com
- ４.查看下是否设置成功：npm config get registry
- ５.运行npm 安装东西即可

若1，没有那个文件；则直接从2开始
