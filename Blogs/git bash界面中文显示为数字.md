如图，使用git bash管理项目时，中文显示为数字。
![](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/github-chinesechar-number.png?raw=true)

## 解决方案
git bash下执行命令：
```c
git config --global core.quotepath false
```

作用是路径不以八进制显示（即是允许中文显示路径），此选项默认在 gitbash 是默认开启的。
详细参见 [git doc](https://git-scm.com/docs/git-config):
- core.quotePath
>Commands that output paths (e.g. ls-files, diff), will quote "unusual" characters in the pathname by enclosing the pathname in double-quotes and escaping those characters with backslashes in the same way C escapes control characters (e.g. <font color="#dd0000">\t</font> for TAB, <font color="#dd0000">\n</font> for LF, <font color="#dd0000">\\\\</font> for backslash) or bytes with values larger than 0x80 (e.g. octal <font color="#dd0000">\302\265</font> for "micro" in UTF-8). If this variable is set to false, bytes higher than 0x80 are not considered "unusual" any more. Double-quotes, backslash and control characters are always escaped regardless of the setting of this variable. A simple space character is not considered "unusual". Many commands can output pathnames completely verbatim using the <font color="#dd0000">-z</font> option. The default value is true.


## 火星字中文乱码
若出现其火星字类型的中文乱码，则一般是文件的编码格式导致的，使用UTF-8编码即可解决。