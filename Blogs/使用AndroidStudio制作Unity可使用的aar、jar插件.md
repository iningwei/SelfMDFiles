## 环境说明
AndroidStudio:4.1.3
Unity:2020.3.0f1

## 需求
创建一个新的Activity作为Unity的主Activity
## 步骤
### 创建AS工程
新建AS工程，模版选择Empty，设置好项目名、包名、最低支持到的SDK版本

### Project面板内修改视图为Project
形如：![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210927114859.png)

### 引入classes.jar
由于需要使用新Activity取代默认的UnityPlayerActivity，因此需要创建一个继承自UnityPlayerActivity的Activity。故需要引入Unity中的classes.jar
笔者项目使用的是il2cpp，因此选择目录``\Editor\Data\PlaybackEngines\AndroidPlayer\Variations\il2cpp\Release\Classes``下classes.jar。（这里补充一下使用最新版本的classes.jar报错，笔者暂时使用了一个老版本unity的classes.jar）

复制该classes.jar文件，鼠标右键点击上图视图中app/libs节点目录，在弹出菜单中选择Paste。文件粘贴到libs目录后，再右键clases.jar文件，选择Add As Library

### 修改build.gradle文件
点开app/build.gradle文件，内容如下：
```c
plugins {
    id 'com.android.application'
}

android {
    compileSdkVersion 30
    buildToolsVersion "30.0.3"

    defaultConfig {
        applicationId "com.trillionjoy.sgame"
        minSdkVersion 21
        targetSdkVersion 30
        versionCode 1
        versionName "1.0"

        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {

    implementation 'androidx.appcompat:appcompat:1.1.0'
    implementation 'com.google.android.material:material:1.1.0'
    implementation 'androidx.constraintlayout:constraintlayout:1.1.3'
    implementation files('libs\\classes.jar')
    testImplementation 'junit:junit:4.+'
    androidTestImplementation 'androidx.test.ext:junit:1.1.1'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'
}
```
可以看到这一行：implementation files('libs\\classes.jar')，即上面提到的对unity中classes.jar的引用。

- 修改plugins{id 'com.android.application'}为plugins{id 'com.android.library'}。然后按照提示sync Gradle。
- 删除 defaultConfig下面的applicationId "com.trillionjoy.sgame"项（Library工程要求必须删除applicationId配置）


## 修改MainActivity
由于笔者创建的是Empty模版，因此在app/src下有一些安卓默认的资源。在app/src/main目录下为各种资源。其中java目录下为代码，res目录下为资源（drawable、values、layout），AndroidManifest.xml为安卓配置文件。其中res可以根据具体项目需求，决定是否删除，但是在导出aar时AndroidManifest.xml文件是要求必须存在的。
打开MainActivity.java文件，修改其为下：

```java
package com.xxx.xxxx;//包名

import androidx.appcompat.app.AppCompatActivity;
import android.os.Bundle;
import com.unity3d.player.UnityPlayerActivity;

public class MainActivity extends UnityPlayerActivity  {
    @Override
    protected void onCreate(Bundle bundle) {
        super.onCreate(bundle);
    }
}
```

## 导出aar
点击Build->Make Project即可生成aar，默认输出目录为app/build/outputs/aar下。

## 删除unity的classes.jar引用
上面提到添加了对unity中classes.jar的引用。在生成的aar文件中也有对其的引用。若直接使用该aar，由于Unity在打包的时候会再次拷贝安装目录下的classes.jar到项目中，存在两份classes.jar导致冲突。因此需要在aar中删除unity的classes.jar。
把aar后缀改成rar，解压后可以看到里面有两个clases.jar：一个位于根目录下、一个位于libs目录下，还有之前提到过的AndroidManifest.xml和res等文件。其中unity的classes.jar位于libs目录下，删除该classes.jar即可。然后重新打压缩包，把后缀改成aar即可完成对unity的classes.jar的剔除。
若是用不到aar文件，只需要使用jar文件中的相关方法函数，则可以把aar中的classes.jar（非unity的那个classes.jar）提取出来，然后改个容易识别的名字直接使用亦可。




## 补充
### 其它导出aar的方法
![](https://raw.githubusercontent.com/iningwei/SelfPictureHost/master/Blog/20210927125310.png)
通过Gradle/../app/Tasks/build/assemble按钮可以快速导出debug和release两个版本的aar。生成目录也是app/build/outputs/aar。

### 快速导出jar的方法
在上文中app/build.grade文件下部增加：
```c
//删除
task deleteOldJar(type: Delete) {
    delete 'build/libs/AndroidPlugin.jar'
}

//task to export contents as jar
task exportJar(type: Copy) {
    //起始文件夹（即生成的jar文件classes.jar所在的目录。各个不同as版本不一样，可以通过在build/imtermediates目录下搜索classes.jar来确定具体位置。若是在编译的时候报错 exportJar: NO-SOURCE,就说明此路径错误）
    from('build/intermediates/compile_library_classes_jar/release/')
    //目标文件夹(没有的话会创建)
    into('build/libs')
    //指定只把起始文件夹中的classes.jar移动到目标文件夹中
    include('classes.jar')
    //重命名下，起个个性的名字，不要与别人混淆
    rename('classes.jar', 'AndroidPlugin.jar')
}
//每次编译，先删除老的jar
exportJar.dependsOn(deleteOldJar, build)
```

然后通过Gradle/../app/Tasks/other/exportJar，即可快速获得jar