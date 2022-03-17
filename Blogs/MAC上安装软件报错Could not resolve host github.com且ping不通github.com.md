MAC上使用命令行安装软件经常会从git库中下载软件，可能会经常遇到安装失败，报错诸如 Could not resolve host:github.com

这个时候打开终端， 执行 ``ping github.com``, 大概率会提示Time Out

## 解决方法
往MAC机器中的hosts文件中添加如下代码：
```c
# github
192.30.255.112  github.com git 
185.31.16.184 github.global.ssl.fastly.net  
```
### 如何添加
- 法一
SHIFT+CMD+G打开``前往文件夹``弹窗，输入/etc
从打开的文件夹中找到hosts文件，复制到桌面，添加代码后，再粘贴到etc目录下。（无法直接在etc文件夹下编辑hosts文件）

- 法二
1.终端输入 sudo vi /etc/hosts 打开编辑文件
2.按s进入编辑模式
4.添加代码
5.按control+c保存
6.输入: 再输入wq就可以了（wq是write写入和quit退出的意思）