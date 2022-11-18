默认情况下SVN对文件名是不区分大小写的。比如你提交了个文件A.txt，你本地给文件名改成a.txt，再提交是没有反应的。它会给你当成同一个文件。

## 解决方法
Settings->Advanced->FixCaseRenames
默认该值为true，改成false即可。