MAC新机系统11.4，安装的XCode版本为13.2.1。
打开一个Unity工程，切换到iOS平台后，后台进行了CocoaPods的安装，但是报错提示安装失败：
```c
ERROR: Error installing cocoapods:
       ERROR: Failed to build gem native extension.
   current directory: /Library/Ruby/Gems/2.6.0/gems/ffi-1.15.5/ext/ffi_c/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/bin/ruby -I /System/Library/Framewoks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0 -r ./siteconf20220311-4106-wxounc.rb extconf.rb
   mkmf.rb can't find header files for ruby at /System/Library/Framewoks/Ruby.framework/Versions/2.6/usr/lib/ruby/include/ruby.h

You might have to install separate package for the ruby development environment,ruby-dev or ruby-devel for example.
```
根据报错提示，并从网络上查询应该是需要安装一个新的ruby版本，不能使用系统自带的ruby。
安装ruby需要使用rvm，安装rvm需要安装Homebrew.

## 安装Homebrew
- 使用命令``/bin/bash -c "$(curl -fsSL https://gitee.com/ineo6/homebrew-install/raw/master/install.sh)"``，这里没有使用网上教程提到的命令``/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"``，是因为github的源经常会失败。参考[这里](https://zhuanlan.zhihu.com/p/90508170)
- 安装完成后执行``brew update``更新下
- 再执行``brew -v``即可看到安装的版本

## 安装rvm
- 先安装rvm ``curl -L https://get.rvm.io | bash -s stable``
- 再载入rvm环境 ``source ~/.rvm/scripts/rvm``
- 查看rvm版本 ``rvm -v``

## 安装ruby
- 查看ruby已知版本 ``rvm list known``
- 查看ruby已安装版本 ``rvm list``
- 安装一个ruby版本，我选择了2.6.5 ``rvm install 2.6.5``
- 安装后，查看ruby版本 ``ruby -v``， 若不是刚下载的版本，还需要使用命令切一下版本

## 安装CocoaPods
上述安装完毕后，执行``sudo gem install -n /usr/local/bin cocoapods``安装cocoapods
安装完毕，执行``pod --version``查看cocoapods版本

到这里对于unity打包来说cocoapods其实并没有完全装好，还需要继续按以下步骤处理。
- Command+Shift+G,打开 ``前往文件夹``弹窗，输入 ~/.cocoapods ,打开cocoapods文件夹后，删除其下所有内容
- 新建repos文件夹
- 打开命令行工具依次执行以下3个命令
```
1) cd ~/.cocoapods/repos
2) git clone --depth 1 https://github.com/CocoaPods/Specs.git master
3) pod repo update
```
这样的话，Unity导出的XCode工程就有.xcworkspace文件了(在cocoapods环境有问题的情况下，导出的XCode工程只有.xcodeproj文件，使用该文件进行打包发布会有很多异常)，然后使用XCode打开该文件，即可进行后续发布

参考：[unity打包ios工程报错iOS framework addition failed due to a Cocoapods installation failure. This will will](https://blog.csdn.net/iningwei/article/details/106769933)


