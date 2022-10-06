在处理抖音弹幕信息时，报错如下：
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



## 解决方法
将protobuf 4.21.7版本降级到3.20.x，通常即可解决问题。
```
pip3 install --upgrade protobuf==3.20.1
```