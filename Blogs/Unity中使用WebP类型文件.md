## 简介
WebP是一种由Google开发的图像格式，旨在提供更高的压缩效率和更好的图像质量，特别是针对Web上的图像传输。以下是一些关键特点：

压缩效率： WebP通常能够提供比JPEG更好的压缩效率，尤其是对于包含大量颜色的图像。

透明度支持： WebP支持有损和无损压缩，而且能够在有损压缩中保留透明度。这是与JPEG相比的一个优势，因为JPEG不支持透明度。

动态图像： WebP格式还支持动态图像，类似于GIF。这使得它适用于展示简单的动画。

颜色配置： WebP支持多种颜色配置，包括灰度、彩色和透明。

支持有损和无损： WebP可以使用有损和无损两种压缩模式。无损模式保留图像质量，而有损模式提供更高的压缩率但可能会有一些细微的视觉损失。

快速解码： WebP图像可以在相对较短的时间内进行解码，这对于Web页面上的快速加载非常重要。

WebP已经得到了许多现代Web浏览器的支持，包括Google Chrome、Mozilla Firefox、Microsoft Edge等。然而，由于不是所有的图像编辑软件都支持WebP，有时可能需要转换工具来处理这种格式的图像。

## Unity中使用WebP文件
Unity默认并不支持WebP格式，因此需要我们拿到数据后对其进行转换。

github有一些开源的方案，诸如：
- [unity.webp](https://github.com/netpyoung/unity.webp)
- [webp-unity3d](https://github.com/minhhh/webp-unity3d/tree/master)

你也可以从[谷歌开源的libwebp库](https://github.com/webmproject/libwebp)中下载源码，并编译为对应平台的插件。

笔者的需求是：windows环境下获得各个直播平台的玩家头像（tk平台是webp格式），并展示在UI上。因此需要判断头像是否是webp格式，然后再转换为unity支持的texture。

### WebP格式验证
WebP格式文件前12个字节是固定的标识字节序列。前4个字节是 "RIFF"，5-8字节标识文件大小（以 little-endian 表示），最后4个字节标识文件类型，WebP图片是 "WEBP"。
[RIFF](https://zh.wikipedia.org/wiki/%E8%B3%87%E6%BA%90%E4%BA%A4%E6%8F%9B%E6%AA%94%E6%A1%88%E6%A0%BC%E5%BC%8F) 是 Resource Interchange File Format 的缩写，是一种通用的文件格式标识符。

Microsoft在AVI和WAV这两种著名的文件格式中，都使用RIFF的格式当成它们的基础。

RIFF文件由一个简单的表头（header）跟随着多个"chunks"所组。其格式完全跟IFF一样，除整数的存储方式不一样以外。

- 表头（Header）
   - 4位组（bytes）：固定为"RIFF".
   - 4位组：little-endian 32-bit正整数，整个文件的大小，扣掉识别字符和长度，共8个字节。
   - 4位组：这个文件的类型字符，例如："AVI "或"WAVE".
- 接下来是区块（Chunks），每个区块包含：
   - 4位组：此区块的ASCII识别字，例如："fmt "或"data".
   - 4位组：little-endian 32-bit正整数，表示本区块的长度（这个正整数本身和区块识别字的长度不算在内）。
   - 不固定长度字段：此区块的资料，大小等同前一栏之正整数。
   - 假如区块的长度不为偶数，则填入一个byte。



针对我们的项目，我这里只验证了RIFF和WEBP标识，代码如下：
```csharp
    public static bool IsWebP(byte[] imageData)
    {
        if (imageData.Length<12)
        {
            return false;
        }
        // WebP文件头的标识字节序列
        byte[] webpHeader = { 0x52, 0x49, 0x46, 0x46 };//R对应的ASCII码用16进制表示为 0x52 ，当然也可以使用十进制 82，或者二进制 0b1010010 （使用二进制需要使用0b前缀来标识）

        // 检查前4个字节是否匹配WebP头部
        for (int i = 0; i < webpHeader.Length; i++)
        {
            if (imageData.Length <= i || imageData[i] != webpHeader[i])
            {
                return false;
            }
        }

        // 提取文件大小（little-endian）
        int fileSize = BitConverter.ToInt32(imageData, 4);//fileSize+8==imageData.Length

        // 文件类型标识符 "WEBP"
        byte[] webpIdentifier = { 0x57, 0x45, 0x42, 0x50 };

        // 检查文件类型标识符
        for (int i = 8; i < 12; i++)
        {
            if (imageData.Length <= i || imageData[i] != webpIdentifier[i - 8])
            {
                return false;
            }
        }        
        return true;
    }
```

### 转换为texture
笔者使用了上述提到的[webp-unity3d](https://github.com/minhhh/webp-unity3d/tree/master)，在Windows PC环境下实现了需求（其它平台未测）。

需要注意的是，该作者通过CreateTexture2DFromWebP获得到的Texture2D是不可读的。可以在该方法中增加参数，Apply生成ITexture2D时通过参数来供外部自由设置。