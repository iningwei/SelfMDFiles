配置PyQt5开发环境，参考的是该教程:[PyQt5安装以及使用教程合集(2022)](https://zhuanlan.zhihu.com/p/162866700)。
PyQt5已安装成功，但是安装PyQt5-tools一直报错：
```c
 Installing build dependencies ... done
  Getting requirements to build wheel ... done
  Preparing metadata (pyproject.toml) ... error
  error: subprocess-exited-with-error

  × Preparing metadata (pyproject.toml) did not run successfully.
  │ exit code: 1
  ╰─> [29 lines of output]
      Traceback (most recent call last):
        File "C:\Users\zhouhui\AppData\Local\Programs\Python\Python310\lib\site-packages\pip\_vendor\pep517\in_process\_in_process.py", line 144, in prepare_metadata_for_build_wheel
          hook = backend.prepare_metadata_for_build_wheel
      AttributeError: module 'sipbuild.api' has no attribute 'prepare_metadata_for_build_wheel'

      During handling of the above exception, another exception occurred:
```
找了很久才发现是自己Python版本太高导致的。
这里:[pip install pyqt5-tools](https://pypi.org/project/pyqt5-tools/) 可以查看到当前(2022/12/01)python版本最高才支持到3.9。

![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20221201231307.png)

而自己安装的Python版本是3.10.7。真的是造孽，做个开发真的难。