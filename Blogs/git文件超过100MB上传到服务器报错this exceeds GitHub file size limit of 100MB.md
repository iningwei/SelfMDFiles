github向服务器commit时，若有大于100MB的文件会commit失败，同时报错提示。若要将大文件顺利提交，可以通过以下步骤。
- 如果版本库已经执行了commit、push操作，记录中已经有了大文件，那么需要先把大文件从版本库记录中删除
1，删除错误缓存索引
对于文件：
``git rm --cached path_of_the_file``
对于文件夹:
``git rm --cached -r path_of_the_dir``
2，重新提交
修改提交信息：``git commit --amend``
重新提交：``git push origin master`` 
这样就把所有小文件都提交到远程仓库了

- 使用Git Large File Storage进行大文件提交
1，安装教程：https://github.com/git-lfs/git-lfs/wiki/Installation
2，查找大文件，并把大文件加入大文件track中
查找大文件：``find ./ -size +100M``
把上述查找到的大文件通过``git lfs track "path_of_the_file"`` 加入到track中
执行完所有大文件后，在.git同级目录下会生成.gitattributes文件，先把这个文件提交到远程仓库
3，再通过git add把大文件加入到add列表，然后通过commit,push即可顺利完成大文件的提交

## 疑难处理
1，在push大文件的时候还是有可能遇到报错，诸如：``Remote "origin" does not support the LFS locking API.Consider disabling it with:git config lfs.XXXX.locksverify false``
可以直接按照提示输入git命令，然后就可以push成功了。
