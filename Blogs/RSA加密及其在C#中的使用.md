RSA算法[简介](https://baike.baidu.com/item/RSA%E7%AE%97%E6%B3%95/263310?fr=aladdin)

## 笔者使用场景
Java作为服务端生成一对公私钥，C#作为客户端拥有公钥。
C#使用公钥对上行byte[]类型数据进行加密，服务端Java使用私钥解密。

## 规范
- 公私钥都是``base64``字符串

## 过程
1，Java服务端开发人员给到客户端这边公、私钥（之所以给了私钥是方便客户端这边自己来测试）

2，C#客户端这边发现Java给到的公私钥，客户端这边并不能直接用。C#支持的公私钥格式有点类似XML格式。

比如Java公钥，其在C#中对应的格式如下：
```csharp
    public string java_publicKey = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCv2NbWPuQwPegAcydw3+xO3GOCKMMyUme9bUuEEmds4CogvasV1f2savwiXA5mKqawoxDjbsjUgdgPHQbmlrhpNBBMa83Us9w5xjgl5tZFyDusnO/r8IQa9pGW7NAJ6xJX5vYbv4rZWon1D8/N7l/ybsR6nZTt+rEcL1V78POXIwIDAQAB";

    public string csharp_publicKey = "<RSAKeyValue><Modulus>r9jW1j7kMD3oAHMncN/sTtxjgijDMlJnvW1LhBJnbOAqIL2rFdX9rGr8IlwOZiqmsKMQ427I1IHYDx0G5pa4aTQQTGvN1LPcOcY4JebWRcg7rJzv6/CEGvaRluzQCesSV+b2G7+K2VqJ9Q/Pze5f8m7Eep2U7fqxHC9Ve/DzlyM=</Modulus><Exponent>AQAB</Exponent></RSAKeyValue>";
```
可见二者不仅仅是加了几个XML标签那么简单，内容也发生了变化，因此需要提供工具把Java公钥转换为C#支持的格式。

3，C#控制台应用实现把Java公私钥转换为C#支持的格式
笔者使用BouncyCastle.Cryptography库，可以使用NuGet安装。实现了C#和Java的公私钥格式互转。
核心代码
```csharp
using Org.BouncyCastle.Crypto.Parameters;
using Org.BouncyCastle.Math;
using System;
using System.Xml;
using System.Security.Cryptography;
using Org.BouncyCastle.Asn1.Pkcs;
using Org.BouncyCastle.Asn1.X509;
using Org.BouncyCastle.X509;
using Org.BouncyCastle.Security;
using Org.BouncyCastle.Pkcs;

namespace RSA_Java2CSharp
{
    /// <summary>  
    /// RSA密钥格式转换  
    /// </summary>  
    public class RSAKeyConvert
    {
        /// <summary>  
        /// RSA私钥格式转换，java->.net  
        /// </summary>  
        /// <param name="privateKey">java生成的RSA私钥</param>  
        /// <returns></returns>  
        public static string RSAPrivateKeyJava2DotNet(string privateKey)
        {
            var data = Convert.FromBase64String(privateKey);
            RsaPrivateCrtKeyParameters privateKeyParam = (RsaPrivateCrtKeyParameters)PrivateKeyFactory.CreateKey(Convert.FromBase64String(privateKey));

            return string.Format("<RSAKeyValue><Modulus>{0}</Modulus><Exponent>{1}</Exponent><P>{2}</P><Q>{3}</Q><DP>{4}</DP><DQ>{5}</DQ><InverseQ>{6}</InverseQ><D>{7}</D></RSAKeyValue>",
                Convert.ToBase64String(privateKeyParam.Modulus.ToByteArrayUnsigned()),
                Convert.ToBase64String(privateKeyParam.PublicExponent.ToByteArrayUnsigned()),
                Convert.ToBase64String(privateKeyParam.P.ToByteArrayUnsigned()),
                Convert.ToBase64String(privateKeyParam.Q.ToByteArrayUnsigned()),
                Convert.ToBase64String(privateKeyParam.DP.ToByteArrayUnsigned()),
                Convert.ToBase64String(privateKeyParam.DQ.ToByteArrayUnsigned()),
                Convert.ToBase64String(privateKeyParam.QInv.ToByteArrayUnsigned()),
                Convert.ToBase64String(privateKeyParam.Exponent.ToByteArrayUnsigned()));
        }

        /// <summary>  
        /// RSA私钥格式转换，.net->java  
        /// </summary>  
        /// <param name="privateKey">.net生成的私钥</param>  
        /// <returns></returns>  
        public static string RSAPrivateKeyDotNet2Java(string privateKey)
        {
            XmlDocument doc = new XmlDocument();
            doc.LoadXml(privateKey);
            BigInteger m = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("Modulus")[0].InnerText));
            BigInteger exp = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("Exponent")[0].InnerText));
            BigInteger d = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("D")[0].InnerText));
            BigInteger p = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("P")[0].InnerText));
            BigInteger q = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("Q")[0].InnerText));
            BigInteger dp = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("DP")[0].InnerText));
            BigInteger dq = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("DQ")[0].InnerText));
            BigInteger qinv = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("InverseQ")[0].InnerText));

            RsaPrivateCrtKeyParameters privateKeyParam = new RsaPrivateCrtKeyParameters(m, exp, d, p, q, dp, dq, qinv);

            PrivateKeyInfo privateKeyInfo = PrivateKeyInfoFactory.CreatePrivateKeyInfo(privateKeyParam);
            byte[] serializedPrivateBytes = privateKeyInfo.ToAsn1Object().GetEncoded();
            return Convert.ToBase64String(serializedPrivateBytes);
        }

        /// <summary>  
        /// RSA公钥格式转换，java->.net  
        /// </summary>  
        /// <param name="publicKey">java生成的公钥</param>  
        /// <returns></returns>  
        public static string RSAPublicKeyJava2DotNet(string publicKey)
        {
            RsaKeyParameters publicKeyParam = (RsaKeyParameters)PublicKeyFactory.CreateKey(Convert.FromBase64String(publicKey));
            return string.Format("<RSAKeyValue><Modulus>{0}</Modulus><Exponent>{1}</Exponent></RSAKeyValue>",
                Convert.ToBase64String(publicKeyParam.Modulus.ToByteArrayUnsigned()),
                Convert.ToBase64String(publicKeyParam.Exponent.ToByteArrayUnsigned()));
        }

        /// <summary>  
        /// RSA公钥格式转换，.net->java  
        /// </summary>  
        /// <param name="publicKey">.net生成的公钥</param>  
        /// <returns></returns>  
        public static string RSAPublicKeyDotNet2Java(string publicKey)
        {
            XmlDocument doc = new XmlDocument();
            doc.LoadXml(publicKey);
            BigInteger m = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("Modulus")[0].InnerText));
            BigInteger p = new BigInteger(1, Convert.FromBase64String(doc.DocumentElement.GetElementsByTagName("Exponent")[0].InnerText));
            RsaKeyParameters pub = new RsaKeyParameters(false, m, p);

            SubjectPublicKeyInfo publicKeyInfo = SubjectPublicKeyInfoFactory.CreateSubjectPublicKeyInfo(pub);
            byte[] serializedPublicBytes = publicKeyInfo.ToAsn1Object().GetDerEncoded();
            return Convert.ToBase64String(serializedPublicBytes);
        }

    }
}
```

4，测试

CSharp加解密接口
```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Security.Cryptography;


namespace RSA_Java2CSharp
{
    public class UEncrypt
    {
        /// <summary>
        /// 生成RSA私钥 公钥
        /// </summary>
        /// <param name="privateKey"></param>
        /// <param name="publicKey"></param>
        public static void RSAGenerateKey(ref string privateKey, ref string publicKey)
        {
            RSACryptoServiceProvider rsa = new RSACryptoServiceProvider();
            privateKey = rsa.ToXmlString(true);
            publicKey = rsa.ToXmlString(false);
        }

        /// <summary>
        /// 用RSA公钥 加密
        /// </summary>
        /// <param name="data"></param>
        /// <param name="publicKey"></param>
        /// <returns></returns>
        public static byte[] RSAEncrypt(byte[] data, string publicKey)
        {
            RSACryptoServiceProvider rsa = new RSACryptoServiceProvider();
            rsa.FromXmlString(publicKey);
            byte[] encryptData = rsa.Encrypt(data, false);
            return encryptData;
        }

        /// <summary>
        /// 用RSA私钥 解密
        /// </summary>
        /// <param name="data"></param>
        /// <param name="privateKey"></param>
        /// <returns></returns>
        public static byte[] RSADecrypt(byte[] data, string privateKey)
        {
            RSACryptoServiceProvider rsa = new RSACryptoServiceProvider();
            rsa.FromXmlString(privateKey);
            byte[] decryptData = rsa.Decrypt(data, false);
            return decryptData;
        }
    }

}

```

测试
```csharp
using RSA_Java2CSharp;
//java侧生成的RSA公钥 base64编码
string java_pubkey = "MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCv2NbWPuQwPegAcydw3+xO3GOCKMMyUme9bUuEEmds4CogvasV1f2savwiXA5mKqawoxDjbsjUgdgPHQbmlrhpNBBMa83Us9w5xjgl5tZFyDusnO/r8IQa9pGW7NAJ6xJX5vYbv4rZWon1D8/N7l/ybsR6nZTt+rEcL1V78POXIwIDAQAB";

//java侧生成的RSA私钥 base64编码
string java_prikey = "MIICeAIBADANBgkqhkiG9w0BAQEFAASCAmIwggJeAgEAAoGBAK/Y1tY+5DA96ABzJ3Df7E7cY4IowzJSZ71tS4QSZ2zgKiC9qxXV/axq/CJcDmYqprCjEONuyNSB2A8dBuaWuGk0EExrzdSz3DnGOCXm1kXIO6yc7+vwhBr2kZbs0AnrElfm9hu/itlaifUPz83uX/JuxHqdlO36sRwvVXvw85cjAgMBAAECgYAL/8fpJrF7eT3ziegPE7iUSQWhRBbNwEHD80lL4dWc4E7/nPGns8pzkXlS/uUco5xsQTRN0Klzlsiff2ORKIe8yLxPlq8CjK2+LmS08lUwFa3EFEXaZ2EC3yj/YS28I+MosqVk/TjnVJGOXURt8JWGNpnuG4KpsdpSBv6eXQcPQQJBAPdnMCh/fiq2jwZOYYKnIAQ1TTfrFP62wyhs4dPdBIoIO8RtPsXYV/DCAAfYp+QmwxRvfNWdXBM7tAqEaUZxuEcCQQC19R0B5do3nQfv1Wz50KtTfVd+GJuRb1UMNFyWI0KnJch0KSuYRbVOte3FZrUYOMu9PkgeBQLDDMUI2Bfz7bRFAkEAq2l8LWMAKFnqSIU//EUM4r2Hbcnb9wrrtnOSF7dXcMd5mYPTbUKS04Wmfck3sdTFbsA77skjRVQlTvAk/KDH+wJBAKzdX1+MNRKctwudIdj766h37gU376Ptt0jO/h8NBKezd4sUCfyyTRuoL2pYtzd5zeXOI8mRTRxtJ0vNsIPnklkCQQCs+JyzCxH6fFDAw6dg3oKLrBYS9tgsBzedzQDEzkISy5eXOIqcMaZOcfDERkoWX5rQfvQcY7LoluUmSi8xTrhP";


string csharp_pubKey = RSAKeyConvert.RSAPublicKeyJava2DotNet(java_pubkey);
Console.WriteLine("csharp_pubkey:" + csharp_pubKey);
string csharp_prikey = RSAKeyConvert.RSAPrivateKeyJava2DotNet(java_prikey);
Console.WriteLine("csharp_prikey:" + csharp_prikey);


string msg = "123666666哪里(=@__@=)hehe"; ;
byte[] msgCrypted = UEncrypt.RSAEncrypt(System.Text.Encoding.Default.GetBytes(msg), csharp_pubKey);

byte[] msgDecrypted = UEncrypt.RSADecrypt(msgCrypted, csharp_prikey);
string result = System.Text.Encoding.Default.GetString(msgDecrypted);

Console.WriteLine(result); 
Console.ReadLine();
```

## 扩展阅读
[C#实现RSA加密解密](https://www.cnblogs.com/laggage/p/11028614.html)
[The data to be decrypted exceeds the maximum for this modulus of 128 bytes. ](https://www.cnblogs.com/Mainz/p/2767646.html)
[RSA非对称加密算法实现：C#](https://www.cnblogs.com/shanfeng1000/p/14840055.html)
[密码学基础1：RSA算法原理全面解析](https://www.jianshu.com/p/6aa7b59be872)
[密码学基础3：密钥文件格式完全解析](https://www.jianshu.com/p/ce7ab5f3f33a)