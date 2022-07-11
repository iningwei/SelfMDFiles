相信很多Unity开发者都遇到过这个警告：
There are inconsistent line endings in the ‘XXXXXX’ script. Some are Mac OS X (UNIX) and some are Windows.
This might lead to incorrect line numbers in stacktraces and compiler errors. Many text editors can fix this using Convert Line Endings menu commands
这个警告告诉你，有某脚本上的行结束符不一致，即既有有CR LF标识的，又有LF标识的。这样的话会导致堆栈路径中的错误行数，甚至是编译错误。


## 原因分析
这是由Windows和Unix不同的标准引起的，即“回车”和“换行”的问题。“回车”和“换行”是ASCII字符集中两个不可见的控制符。“回车”就是CHAR(13)，即\r；“换行”就是CHAR(10)，即\n。
在Unix中“回车”不换行，“换行”才换行，行尾只需要一个“换行”；而在Windows中，“回 车”和“换行”都换行，“回车”+“换行”才是行尾。
## 行尾
- LF: Unix平台
- CRLF: Windows平台
- CR: Macintosh平台
```
CR回车(\r) LF换行(\n)
Windows/Dos CRLF \r\n
Linux/Unix LF \n
MacOS CR \r
```
## 解决方法
Unity中创建的新文件的默认行尾都是LF，Visual Studio中创建的文件默认行尾是CRLF。统一的方法有两种。
1，统一修改VS中的文件默认行尾
笔者使用的是VS2019，没有找到相关设置项。只能修改单个文件的：编辑->高级->设置行尾序列
单个文件的当前行尾符可以通过VS中打开文件的右下角的``混合``按钮点开查看。
PS：在老版本的VS中``File->Advanced Save Options``可以弹出设置保存文件的行尾符选项。
2，统一修改Unity中文件的默认行尾
方法为修改模版文件的行尾符，在目录：``XXX\Editor\Data\Resources\ScriptTemplates``中使用``Notepad++``打开模版文件：``View->Show Symbol->Show End Of Line``即可查看当前模版文件的行结尾符。
通过：``Edit->EOL Conversion``即可设置模版文件的行尾符。 