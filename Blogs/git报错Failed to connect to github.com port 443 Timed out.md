环境：WinPC+梯子
问题描述：git在pull或者push的时候经常性失败，报错提示为：Failed to connect to github.com port 443: Timed out.
虽然可以通过多次尝试实现顺利提交或者拉取，但是很耽误时间。

## 解决方法
step1: 执行以下命令
```c
git config --global http.proxy http://127.0.0.1:1080

git config --global https.proxy http://127.0.0.1:1080
```
设置全局代理。其中1080是我梯子的代理端口

step2：执行相关git操作

step3：为了防止全局代理造成其它影响，可在不使用git命令后，取消全局代理
```c
git config --global --unset http.proxy

git config --global --unset https.proxy
```
