在WIN PC中使用python(笔者python版本为3.10.7)处理抖音弹幕信息时，报错如下：
```c
 _descriptor.FieldDescriptor(
  File "C:\Users\xxx\AppData\Local\Programs\Python\Python310\lib\site-packages\google\protobuf\descriptor.py", line 560, in __new__
    _message.Message._CheckCalledFromGeneratedFile()
TypeError: Descriptors cannot not be created directly.
If this call came from a _pb2.py file, your generated code is out of date and must be regenerated with protoc >= 3.19.0.
If you cannot immediately regenerate your protos, some other possible workarounds are:
 1. Downgrade the protobuf package to 3.20.x or lower.
 2. Set PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=python (but this will use pure-Python parsing and will be much slower).

More information: https://developers.google.com/protocol-buffers/docs/news/2022-05-06#python-updates
```

经分析，代码中确实使用了python的protobuf库，根据提示可知需要降低protobuf的版本。


## 解决方法
在cmd中使用pip命令：``pip3 show protobuf`` （也可以用``pip show protobuf``，或者使用``pip list`` or ``pip3 list``查看当前安装的所有库，里面有protobuf库的信息）。可以获悉，当前安装的版本是4.21.7。
将protobuf 4.21.7版本降级到3.20.x，即可解决问题。使用如下命令：

```
pip3 install --upgrade protobuf==3.20.1
```

补充说明：pip3是python3.x的命令，python3.x依旧兼容之前版本的pip指令。