Python版本使用的是2022年10月24日刚发布的v3.11.0（今天是10月26日），安装playwright库时遇到问题：

``pip install playwright``报错如下：
```c
pip install playwright
Collecting playwright
  Using cached playwright-1.27.1-py3-none-win_amd64.whl (27.4 MB)
Collecting greenlet==1.1.3
  Using cached greenlet-1.1.3.tar.gz (91 kB)
  Preparing metadata (setup.py) ... done
Requirement already satisfied: pyee==8.1.0 in c:\users\zhouhui\appdata\local\programs\python\python311\lib\site-packages (from playwright) (8.1.0)
Installing collected packages: greenlet, playwright
  DEPRECATION: greenlet is being installed using the legacy 'setup.py install' method, because it does not have a 'pyproject.toml' and the 'wheel' package is not installed. pip 23.1 will enforce this behaviour change. A possible replacement is to enable the '--use-pep517' option. Discussion can be found at https://github.com/pypa/pip/issues/8559
  Running setup.py install for greenlet ... error
  error: subprocess-exited-with-error

  ×  Running setup.py install for greenlet did not run successfully.
  │ exit code: 1
  ╰─> [100 lines of output]
      running install
      C:\Users\ZhouHui\AppData\Local\Programs\Python\Python311\Lib\site-packages\setuptools\command\install.py:34: SetuptoolsDeprecationWarning: setup.py install is deprecated. Use build and pip and other standards-based tools.
```

发现依赖greenlet
于是``pip install greenlet``先，依旧报错：
```c
  Collecting greenlet
  Using cached greenlet-1.1.3.post0.tar.gz (91 kB)
  Preparing metadata (setup.py) ... done
Installing collected packages: greenlet
  DEPRECATION: greenlet is being installed using the legacy 'setup.py install' method, because it does not have a 'pyproject.toml' and the 'wheel' package is not installed. pip 23.1 will enforce this behaviour change. A possible replacement is to enable the '--use-pep517' option. Discussion can be found at https://github.com/pypa/pip/issues/8559
  Running setup.py install for greenlet ... error
  error: subprocess-exited-with-error

  × Running setup.py install for greenlet did not run successfully.
  │ exit code: 1
  ╰─> [75 lines of output]
      running install
      C:\Users\ZhouHui\AppData\Local\Programs\Python\Python311\Lib\site-packages\setuptools\command\install.py:34: SetuptoolsDeprecationWarning: setup.py install is deprecated. Use build and pip and other standards-based tools.
```
按照提示：https://github.com/pypa/pip/issues/8559 使用--use-pep517，依旧不行。


使用pip list看下当前安装的库：
```c
  Package            Version
------------------ -----------
beautifulsoup4     4.11.1
certifi            2022.9.24
charset-normalizer 2.1.1
google             3.0.0
idna               3.4
pip                22.3
protobuf           3.20.1
pyee               8.1.0
requests           2.28.1
setuptools         65.5.0
soupsieve          2.3.2.post1
urllib3            1.26.12
```
按照这篇提示['setup.py install is deprecated' warning shows up every time I open a terminal in VSCode](https://stackoverflow.com/questions/73257839/setup-py-install-is-deprecated-warning-shows-up-every-time-i-open-a-terminal-i)，降低了setuptools的版本至58.2.0依旧不行。


实在没有办法了，于是卸载python，并清空安装目录下的相关库。再下载一个低版本的v3.10.7，顺利安装...哎

## 补充
pip升级：pip install --upgrade pip


 

