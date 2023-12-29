## 什么是Protobuf协议
1，Google出品

2，他是一种与语言无关、与平台无关，是一种可扩展的用于序列化和结构化数据的方法，常用于用于通信协议，数据存储等。

他是一种灵活，高效，自动化的机制，用于序列化结构化数据，结合 JSON 和 XML 来理解，你可以暂时将他们仨理解成同一种类型的事物，但是呢，Protobuf对比于他们两个，拥有着体量更小，解析速度更快的优势。

XML、JSON 更注重 数据结构化，关注人类可读性和语义表达能力。Protobuf 更注重 数据序列化，关注效率、空间、速度，人类可读性差，语义表达能力不足。

Protobuf选择二进制编码的形式，将业务数据编码到其中，不会有无关数据，甚至连字段名都不会编码进去，这让带宽压力减小，此外Google自己设计了编码与解码算法，以保证资源占用合理且尽量快速的特点。

此外，Protobuf还有一个副作用：人类对编码后的数据比较难读。这个副作用从一定程度实现数据保护的效果，一些网站利用这个特性实现了反爬。 

3，Protobuf 有两个大版本，proto2 和 proto3，两个版本并不兼容，如果是新接触的话，建议直接入手 proto3 版本。所以下文的描述都是基于 proto3 的。

proto3 相对 proto2而言，简言之就是支持更多的语言（Ruby、C#等）、删除了一些复杂的语法和特性、引入了更多的约定等。

## proto文件
使用Protobuf通信的第一步，便是定义出proto文件，我们定义一个简单的proto文件，名为demo.proto，内容如下：：
```
syntax = "proto3";

message Person{
    required string name = 1;
    message Info{
        required int32 id = 1;
        repeated string phonenumber = 2; 
    }
    repeated Info info = 2;
}
```


先思考一下，为啥需要proto文件？为何不能像JSON那样直接使用呢？

回顾一下Protobuf其中一个优点：只编码业务相关的数据到待传输的二进制流中，以节省带宽。

Protobuf实现这个特点的原理是：通信双方手里都拿着定义好的proto文件。

发送方通过这个proto文件定义发送的内容，这些内容不会需要像JSON那样，有字段名、有括号这些，接收方收到数据后，再利用手里的proto文件，按算法直接解析里面的数据。

我一直强调，JSON传输时，会有字段名、有括号，可能有人会比较懵逼，还是举个例子，如下JSON：
```
{"name": "ayuliao", "age": 30}
```
这段JSON在传输时，name和age这两个字段名也会被传输，但这两个字段名没啥业务意义，主要就是用来获取数据的，假设这段数据使用Protobuf传输，Protobuf就不会将name和age编码到数据流里，只会将ayuliao与30编码进去，接收方获得数据后，手里有一份与发送方一样的proto文件，然后按proto文件中的格式进行解码。

思索一下，要在没有字段名的情况下，合理的解码Protobuf数据，就必须要求传输数据是按一定规则组织的，比如ayuliao必须在30前，这样我会先解码ayuliao，再解码30，至此，我们引入proto文件的一个语法要求，message结构里的分配标识号不可重复。

看到上面的proto文件，有两个messge结构，外部的message结构是Person，Person中有有name与info两个属性，其中name的分配标识号为1，info的分配标识号为2，在同一个message中，分配标识号不可重复，否则protobuf无法正常使用。

此外，从上面proto文件中也可以发现，message中不同属性可以有不同的限定修饰符，有3种：
- required：发送方发送的数据中必须包含这个字段的值，接收方接收的数据也必须要能识别该字段，大白话，加上required修饰符，这个字段双方必须使用，否则报错。
- optional：可选字段，发送方可选择性地发送该字段，接收方如果能够识别该字段就进行相应解码处理，如果不能识别，则直接忽略。
- repeated：可重复字段，发送方每次发送都可以包含多个值，类似于传递一个数组。 

在日常使用protobuf时，有两个常见的tips:

1.分配标识号一般会按业务划分，不同业务间字段不按大小顺序紧密排序，如：

message data {
  optional string name = 10001;
  optional int32 age = 10002;
  
  optional string job = 20001;
  optional string hobby = 20002;
}
上述proto，基础信息（name、age）以1000开头，其他信息（job、hobby）以2000开头，这样后续要添加时，更加清晰，比如要添加性别这个基本信息 ：optional string sex = 10003;。

2.很多使用protobuf通信的系统会将字段的限定修饰符设置为optional，这样系统在升级时，旧版程序无需升级也可以与新程序进行通信，只是对于新字段无法识别而已，这样可以做到平滑升级。

### 标识号
在消息定义中，每个字段都有唯一的一个标识符。这些标识符是用来在消息的二进制格式中识别各个字段的，一旦开始使用就不能够再改 变。注：[1,15]之内的标识号在编码的时候会占用一个字节。[16,2047]之内的标识号则占用2个字节。所以应该为那些频繁出现的消息元素保留 [1,15]之内的标识号。切记：要为将来有可能添加的、频繁出现的标识号预留一些标识号。

最小的标识号可以从1开始，最大到229 - 1, or 536,870,911。不可以使用其中的[19000－19999]的标识号， Protobuf协议实现中对这些进行了预留。如果非要在.proto文件中使用这些预留标识号，编译时就会报警。

### 注释
向.proto文件添加注释，可以使用C/C++/java风格的双斜杠（//）

### Optional的字段和默认值
如上所述，消息描述中的一个元素可以被标记为“可选的”（optional）。一个格式良好的消息可以包含0个或一个optional的元素。当解 析消息时，如果它不包含optional的元素值，那么解析出来的对象中的对应字段就被置为默认值。默认值可以在消息描述文件中指定。例如，要为 SearchRequest消息的result_per_page字段指定默认值10，在定义消息格式时如下所示：
```
optional int32 result_per_page = 3 [default = 10];
```

如果没有为optional的元素指定默认值，就会使用与特定类型相关的默认值：对string来说，默认值是空字符串。对bool来说，默认值是false。对数值类型来说，默认值是0。对枚举来说，默认值是枚举类型定义中的第一个值。

### proto文件转换为各个语言支持的数据访问类
protoc 是 protobuf 自带的编译器，可以将 .proto 文件编译成 java、python、go、C# 等多种语言的代码文件。这些代码可以操作在.proto文件中定义的消息类型，包括获取、设置字段值，将消息序列化到一个输出流中，以及从一个输入流中解析消息。

下载[protoc](https://github.com/protocolbuffers/protobuf/releases)编译工具。如果你跟我一样，使用的windows 64位的系统，那么下载win64版本的protoc则可。protoc.exe是支持生成多语言类型，这对于跨语言的混合编程比较方便。

以上述提到的demo.proto文件为例,假设要把demo.proto输出python使用的py文件，最简单的做法就是把demo.proto文件拷贝到protoc/bin文件夹下（和protoc.exe一个目录下），命令行下CD到protoc/bin目录下，使用如下命令：
```
protoc --python_out=. demo.proto
```
即可在protoc/bin文件夹下生成demo_pb2.py文件。（也可以把protoc/bin目录加入到系统环境变量中，方便随时使用protoc命令）

```
--python_out:用于指定要生成python文件同时设置其输出路径，上例中的.即表示输出路径和protoc.exe同级。
```
```
随后空格并紧接着demo.proto，表示输入文件。（一定要注意输入和输出之间有个空格符）
```

### 常用的protoc命令行参数
- --proto_path:指定.proto文件查找路径
- --cpp_out:指定生成C++代码文件目录
- --java_out:指定生成Java代码文件目录
- --python_out:指定生成Python代码文件目录
- --go_out::指定生成Go代码文件目录
- --csharp_out::指定生成C#代码文件目录
- --objc_out::指定生成Objective-C代码文件目录
- --ruby_out::指定生成Ruby代码文件目录
- --js_out::指定生成JavaScript代码文件目录

使用示例：
``protoc --proto_path=./ --cpp_out=./output ./message.proto``
上述命令会在当前目录下查找message.proto文件，生成C++代码并存放在output目录下。

在命令行使用``protoc -h``可以查看更多的命令。


## Unity接入Protobuf协议
### Google.Protobuf 和 Protobuf.net
protobuf是一个语言无关、平台无关的序列化协议，由谷歌开源提供。再加上其高性能、存储占用更小等特点，在云原生的应用中越来越广泛。
在C#中主要有两种方法来使用protobuf协议，分别为Google.Protobuf和protobuf-net，其中Google.Protobuf由谷歌官方提供。
github主页：https://github.com/protocolbuffers/protobuf/

### 接入Google.Protobuf
在[这里](https://github.com/protocolbuffers/protobuf/releases)下载Protobuf的Zip包（注意确保protoc工具和protobuf版本保持一致），解压后，在csharp/src/Google.Protobuf文件夹下的即是Unity可用的源码，拷贝到Unity项目中。
笔者使用22.4这个版本，发现必须开启unity的Unsafe，同时还需要把``C:\Program Files\Microsoft Visual Studio\2022\Enterprise\MSBuild\Current\Bin``目录下的System.Runtime.CompilerServices.Unsafe.dll拷贝到Plugins下。具体可以参考这里：
[error CS0122: 'Unsafe' is inaccessible due to its protection level](https://github.com/protocolbuffers/protobuf/issues/10085)

#### 序列化
```
public static byte[] Serialize(IMessage msg)
        {
            using (MemoryStream output = new MemoryStream())
            {
                msg.WriteTo(output);
                return output.ToArray();
            }
        }
```

#### 反序列化
```
byte[] data;
ClassName msg = ClassName.Parser.ParseFrom(data);
```

### 接入Protobuf.net
github主页：https://github.com/mgravell/protobuf-net；

序列化：
```
public static byte[] Serialize(ErrorLog log)
        {
            using (MemoryStream memoryStream = new MemoryStream())
            {
                ProtoBuf.Serializer.Serialize(memoryStream, log);
                return memoryStream.ToArray();
            }
        }
```

反序列化
```
public static ErrorLog DeSerialize(byte[] data)
        {
            using (MemoryStream ms = new MemoryStream(data))
            {
                return ProtoBuf.Serializer.Deserialize<ErrorLog>(ms);
            }
        }
```

//TODO:
https://www.jb51.net/article/230234.htm

https://www.jb51.net/article/230186.htm

https://blog.csdn.net/csdn1987/article/details/128418647

https://www.baidu.com/baidu?ie=UTF-8&word=protobuf+net+google+protobuf&tn=maxco3_dg&ch=1

https://www.jb51.net/article/82795.htm

https://blog.csdn.net/l_jinxiong/article/details/50709745

https://blog.csdn.net/linshuhe1/article/details/51781749
## Lua接入Protobuf协议


参考文档：
[Protobuf 使用指南](https://www.jianshu.com/p/cae40f8faf1e)
[protobuf.dev官方文档](https://protobuf.dev/getting-started/)