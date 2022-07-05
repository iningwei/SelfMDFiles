使用 UnityWebRequest 下载https协议下图片时报错``Curl error 60: Cert verify failed: UNITYTLS_X509VERIFY_FLAG_USER_ERROR1``

## 解决方法
出现上述报错是证书验证的问题，在不影响安全的情况下可以跳过证书验证。
创建一个返回true的证书认证脚本：
```csharp
  public class CertificateWhore : CertificateHandler
     {
         protected override bool ValidateCertificate(byte[] certificateData)
         {
             return true;
         }
     }
```
在每一个request中，设置其certificateHandler：
```csharp
    var request = UnityWebRequest.Delete(API + aCommand);
    request.certificateHandler = new CertificateWhore();
```

## 补充
笔者遇到的情况是服务端使用了https自签名证书，后面服务端人员处理了一下自签名证书，这个报错也不见了，无需客户端额外处理。