git config --global user.name "userName"
git config --global user.email "email@xx.xx"
打印当前目录：pwd
回到上一级目录：cd ..
进入xx目录：cd xx
创建文件：touch abc.txt
列出目录下的所有文件或文件夹：ls
初始化git:进入目标目录后，git init
打印文件内容：cat 文件名.后缀

增加某个文件到暂存区：git add xxx.xx
把暂存区的所有文件提交到分支：git commit -m "注释"
仓库状态:git status
工作区和暂存区的区别：git diff
暂存区和仓库分支（上次commith后的内容）的区别：git diff --cached
工作区和仓库分支区别：git diff HEAD <file>
**git diff后，要退出按 q 即可**
-----------------------版本库---------------------------------------
··························|································|
················git diff --cached······················|
··························|································|
----------------------暂存区---------------------- git diff HEAD
··························|································|
······················git diff·····························|
··························|································|
---------------------工作区-------------------------------------------




查看提交历史 日志：git log
查看提交历史 日志（清晰版） git log --pretty=oneline
查看日志，带分支信息： git log --graph --pretty=oneline --abbrev-commit
详细的git log可以参考：https://blog.csdn.net/wh_19910525/article/details/7468549


回到过去：
	git reset --hard HEAD^
	git reset --hard HEAD^^
	git reset --hard HEAD~100
	git reset --hard commit_id(commit_id为具体提交版本号)
	
查看所有执行过的命令：git reflog



把暂存区文件移除：git reset HEAD <file>
撤销文件修改:git checkout -- <file>
	(注意一定要带上 -- ，否则就变成切换分支的命令了)
	若文件还没有添加到暂存区，那么会回到跟版本库一样的状态。若文件已经添加到暂存区，了么会回到暂存区状态。
	该命令实质上是从版本库中更新一个文件，替换本地工作区的文件
	
### 删除文件
``rm 文件名.后缀``
	上述删除只是从文件夹内删除文件，但是版本库并没有删除，还需要执行``git rm 文件名.后缀``；最后还要commit提交修改``git commit -m "delete a file"``

若是手动删除一堆物理文件，git add的时候并不能把版本删除的信息添加到版本库，按照提示需要一个个git rm。其实可以使用``git add -u``,该命令会将所有已修改和已删除的文件添加到git索引中，之后我们可以运行git commit命令提交这些更改。



## 远程库相关
1：首先需要把自己的电脑的公钥添加到GitHub ssh keys中（Settings->SSH and GPG keys）
(公钥获得方式，参见廖雪峰博客：https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001374385852170d9c7adf13c30429b9660d0eb689dd43a000)

本地库和远程库关联：
git remote add origin https://github.com/iningwei/ExcelTool.git
若远程库为空，第一次推送需要加上-u参数，即：git push -u origin master
后面再提交使用：git push origin master

注意：
要是本地是个空目录，没有任何任何文件，则需要先git init
然后创建一个文件（一般是README.md文件）。
add并commit后，才可以使用git push -u origin master命令，否则报错src refspec master does not match any


git pull origin master
git pull的作用是将远程库中的更改代码合并到当前分支中，默认为：git fetch + git merge


## 分支
查看分支：git branch
创建分支：git branch <name>
切换分支：git checkout <name>
创建+切换分支：git checkout -b <name>
合并某分支到当前分支：git merge <name>
合并分支禁用Fast Forward：git merge --no-ff -m "描述文字" <name>
删除分支：git branch -d <name>