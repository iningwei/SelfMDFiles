笔者遇到使用VisualStudioCode打开文件后乱码的问题。
原因是VisualStudioCode默认是UTF-8编码格式，而你打开的文件原始编码格式可能是ANSI编码，因此导致乱码。
## 解决方式
File->Prefrences->Settings
搜索Auto Guess关键字，勾选Files:Auto Guess Encoding。这样打开文件的时候会根据文件原始的编码格式来设置其编码方式。

设置好后，再打开由ANSI编码的文件，在界面右下会显示GB2312，代表ANSI编码。