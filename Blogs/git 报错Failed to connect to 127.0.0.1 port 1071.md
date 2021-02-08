使用git命令checkout时，报错如题。

这是由于设置了动态代理导致的报错。

解决方法如下（依次执行，因为有可能在多个地方设置了动态代理）：
- git命令方式
使用git命令查询动态代理

```
git config --global http.proxy

git config --global https.proxy
```

若是有返回值，则使用下面命令取消代理
```
git config --global --unset http.proxy
git config --global --unset http.proxy
```

- git配置文件

打开``c:\Users\当前用户``目录下的``.gitconfig``文件（该文件默认是隐藏的）。将里面关于proxy的行删除

- 环境变量

打开环境变量,查看用户变量和系统变量,将里面的http_proxy(也可能不是这个名字,但是看值是127.0.0.1:1071这种的就是了,或者有proxy),把这一行删掉就行