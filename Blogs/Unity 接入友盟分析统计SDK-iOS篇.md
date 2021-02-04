## 说明
友盟SDK也是分了类的，包括游戏统计SDK、消息推送SDK、社会化分享SDK。笔者这里接入的是游戏统计SDK（笔者接入时SDK更新的日期截止到2019年1月2日）



## 接入过程
[下载U3D版本SDK](https://developer.umeng.com/sdk/u3d)

SDK分为common和analytics两个unitypackage,都导入到工程中

由于友盟SDK版本发布时间较久，BuildPostProcessor.cs中的部分接口在较新版本Unity中已经有问题，需要调整，调整后的代码如下：
```csharp
using System.IO;
using UnityEngine;
using UnityEditor;
using UnityEditor.Callbacks;
#if UNITY_IOS
using UnityEditor.iOS.Xcode;
#endif

public class BuildPostProcessor
{

#if UNITY_IOS
    [PostProcessBuildAttribute(1)]
    public static void OnPostProcessBuild(BuildTarget target, string path)
    {
        if (target == BuildTarget.iOS)
        {
            // Read.
            string projectPath = PBXProject.GetPBXProjectPath(path);
            PBXProject project = new PBXProject();
            project.ReadFromString(File.ReadAllText(projectPath));
#if UNITY_2019_3_OR_NEWER
            string targetGUID = project.GetUnityFrameworkTargetGuid();
            AddFrameworks(project, targetGUID);
#else
			string targetName = PBXProject.GetUnityTargetName();
			string targetGUID = project.TargetGuidByName(targetName);
			AddFrameworks(project, targetGUID);
#endif


            // Write.
            File.WriteAllText(projectPath, project.WriteToString());
        }
    }

    static void AddFrameworks(PBXProject project, string targetGUID)
    {
        // Frameworks 

        project.AddFrameworkToProject(targetGUID, "libz.dylib", false);
        project.AddFrameworkToProject(targetGUID, "libsqlite3.tbd", false);
        project.AddFrameworkToProject(targetGUID, "CoreTelephony.framework", false);


    }
#endif
}
```

- 初始化

从运营那拿到appKey
```csharp
public class UMengSdkManager : Singleton<UMengSdkManager>
{
    string appKey = "xxxxxxxxxxxxx";

    public void Init()
    {
        GA.StartWithAppKeyAndChannelId(appKey, "UMeng");

        GA.SetLogEnabled(false);
    }
}
```

## 相关文档
[开发文档](https://developer.umeng.com/docs/66632/detail/67588)