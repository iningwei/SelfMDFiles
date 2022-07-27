## LZMA

在U3D中，AssetBundle的标准压缩格式便是LZMA（LZMA是一种序列化流文件），因此在默认情况下，打出的AssetBundle包处于LZMA格式的压缩状态，在使用AssetBundle前需要先解压缩。

使用LZMA格式压缩的AssetBundle的包体积最小（高压缩比），但是相应的会增加解压缩时的时间。



## LZ4

Unity 5.3之后的版本增加了LZ4格式压缩，由于LZ4的压缩比一般，因此经过压缩后的AssetBundle包体的体积较大（该算法基于chunk）。但是，使用LZ4格式的好处在于解压缩的时间相对要短。

若要使用LZ4格式压缩，只需要在打包的时候开启 BuildAssetBundleOptions.ChunkBasedCompression即可。