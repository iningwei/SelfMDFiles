要在Unity3D里面接入iOS SDK，因此又在新mac上配置了一道环境。机器是Mac mini，芯片是M2，OS为Sonoma 14.2。

若Unity3D工程里面有EDM4U插件，且配置了Dependencies文件（文件内配置了iosPod），那么在导出XCode的工程时，会自动安装CocoaPods，但多半会失败。失败原因多是机器自带的ruby版本过低或者根本就不自带ruby了（2022之后苹果安装的系统不带ruby）。因此考虑手动来安装CocoaPods 。
## 1，CocoaPods安装
### 1.1，先确保XCode安装完毕，笔者安装版本为15.3

### 1.2，安装Homebrew
- 使用命令``/bin/bash -c "$(curl -fsSL https://gitee.com/ineo6/homebrew-install/raw/master/install.sh)"``，这里没有使用网上教程提到的命令``/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"``，是因为github的源经常会失败。参考[这里](https://zhuanlan.zhihu.com/p/90508170)
- 安装完成后执行``brew update``更新下
- 再执行``brew -v``即可看到安装的版本
- ``brew --prefix`` 可查看安装地址,笔者打印出的是/opt/homebrew

### 1.3，安装rvm
rvm是多ruby管理工具(Ruby Version Manager)
- 先安装rvm ``curl -L https://get.rvm.io | bash -s stable``
   - 这一步安装可能会出现异常提示：Failed to connect to raw.githubusercontent.com port 443:Connection refused，即使开着梯子都不行。解决方法详见后文。
- 再载入rvm环境 ``source ~/.rvm/scripts/rvm``
- 查看rvm版本 ``rvm -v``,验证安装完成

### 1.4，安装ruby
- 查看ruby已知版本 ``rvm list known``
- 查看本地ruby已安装版本 ``rvm list``
- 安装一个ruby版本，我选择了2.7.2 ``rvm install 2.7.2`` （注意这里，若unity导出xcode工程时报错的话，那么根据报错提示选择更高版本的ruby安装）
- 安装后，查看ruby版本 ``ruby -v``， 若不是刚下载的版本，还需要使用命令切一下版本``rvm use 2.7.2``
- 查看ruby安装目录：``gem e``

#### 1.4.1，若遇到ruby安装不了的情况可以考虑切换镜像（无梯子可能会遇到）
先检查当前ruby镜像 ``gem sources -l``
如果上述命令后得到的镜像地址为``https://rubygems.org/``
则执行命令``gem sources --remove https://rubygems.org/``删除镜像
然后添加国内镜像``gem sources -a https://gems.ruby-china.com/``，然后使用命令``gem sources -l``确定一下已经切换到国内镜像

### 1.5，安装CocoaPods
执行``sudo gem install -n /usr/local/bin cocoapods``安装cocoapods相关组件，安装好后pod指令便可以使用了。

安装完毕，执行``pod --version``查看cocoapods版本。到这里对于unity打包来说还是不够的，网上说使用``pod setup``即可配置好仓库，都是胡扯。需要严格按照下述流程。

另外使用指令``which pod``可以查看安装目录。

#### 1.5.1，配置Cocoapods仓库
这里必须先执行选择XCode的命令：``sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer``。

- 打开``访达``，使用快捷键 Command+Shift+G,打开 ``前往文件夹``弹窗，输入 ~/.cocoapods ,打开cocoapods文件夹后，删除其下所有内容。
   - 一般来说若之前未配置过Cocapods仓库，或者使用unity导出xcode工程自动安装cocoapods时，未走到这个步骤就中断了的话，那么就没有.cocoapods目录，需要自己新建一个。
   - 另外这是个隐藏目录，一般位于用户的文件夹下。在用户文件夹下使用快捷键``Shift+Command+.``可以切换隐藏文件/文件夹的可见性。
- 新建repos文件夹
- 打开命令行工具依次执行以下3个命令
```
1) cd ~/.cocoapods/repos
2) git clone --depth 1 https://github.com/CocoaPods/Specs.git master
3) pod repo update
```
这样的话，Unity导出的XCode工程就有.xcworkspace文件了(在cocoapods环境有问题的情况下，导出的XCode工程只有.xcodeproj文件，使用该文件进行打包发布会有很多异常)，然后使用XCode打开.xcworkspace文件，即可进行后续编译发布

### 1.6，测试CocoaPods是否安装完毕
可以通过执行``pod search AFNetworking``（需要等候片刻，成功后按q退出）。若出现报错``Unable to find a pod with name,author,summary,or description matching 'AFNetworking'``，则说明Cocoapods仓库配置有错，需要删除重新安装（可以手动删除.cocoapods/repos目录下的所有东西，再执行上述配置Cocoapods仓库的流程）
一般而言.cocoapods目录下只有一个repos目录


## 2，补充
### 2.1，删除CocoaPods流程
- ``sudo gem uninstall cocoapods -n /user/local/bin``,遇到对话的话，输入y，并回车。
- 先查看本地已经安装过的CocoaPods相关依赖组件库，使用命令``gem list``或者带过滤命令``gem list --local | grep cocoapods``，如下图：

![](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/2024/03/cocoapods1.png?raw=true)
- 然后使用命令``sudo gem uninstall cocoapods-core``逐个删除（注意，删除指令指定的CocoaPods相关组件不要带版本号）

![](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/2024/03/cocoapods2.png?raw=true)
- 或者使用遍历删除命令
```c
for i in $( gem list --local --no-version | grep cocoapods );
do
sudo gem uninstall $i;
done
```

### 2.2，解决安装rvm报错：Failed to connect to raw.githubusercontent.com port 443:Connection refused
这里[ip138](https://site.ip138.com/)查询 ``raw.githubusercontent.com`` 的ip，然后把某个可用ip和该url的组合，形如``185.199.110.133 raw.githubusercontent.com``加入到host文件中，重新执行安装命令即可（若不行的话，则再换个ip）


## 3，笔者之前一些总结
- [unity打包ios工程报错iOS framework addition failed due to a Cocoapods installation failure. This will will](https://blog.csdn.net/iningwei/article/details/106769933)。这篇博文中错误原因应该就是之前在配置仓库时，未事先选择XCode导致的。
