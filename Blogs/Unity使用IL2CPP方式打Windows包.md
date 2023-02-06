笔者Unity版本2021.3.8f1

1,确保安装了对应版本的扩展包：UnitySetup-Windows-IL2CPP-Support-for-Editor

2,打包报错：
```
error: Could not set up a toolchain for Architecture x64. Make sure you have the right build tools installed for il2cpp builds. Details:
UnityEngine.GUIUtility:ProcessEvent (int,intptr,bool&)
```
和
```
Internal build system error. BuildProgram exited with code -2146233088.
error: Could not set up a toolchain for Architecture x64. Make sure you have the right build tools installed for il2cpp builds. Details:
IL2CPP C++ code builder is unable to build C++ code. In order to build C++ code for Windows Desktop, you must have one of these installed:
 * Visual Studio 2022 or newer with C++ compilers and Windows 10 SDK (recommended)
 * Visual Studio 2019 with C++ compilers and Windows 10 SDK
 * Visual Studio 2017 with C++ compilers and Windows 10 SDK
 * Visual Studio 2015 with C++ compilers and Windows 10 SDK

Visual Studio 2017 (or newer) is detected using `vswhere.exe` as well as VSCOMNTOOLS environment variables.
Visual Studio 2015 is detected by looking at "SOFTWARE\Microsoft\VisualStudio\14.0_Config\InstallDir" in the registry as well as VSCOMNTOOLS environment variables.
Windows 10 SDK is detected by looking at "SOFTWARE\Wow6432Node\Microsoft\Microsoft SDKs\Windows\v10.0\InstallationFolder" in the registry.

Unable to detect any compatible Visual Studio installation!
 * Found Visual Studio 2022 installation without C++ tool components

Windows 10 SDK is not installed. You can install from here: https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk/


Unity.IL2CPP.Bee.BuildLogic.ToolchainNotFoundException: IL2CPP C++ code builder is unable to build C++ code. In order to build C++ code for Windows Desktop, you must have one of these installed:
 * Visual Studio 2022 or newer with C++ compilers and Windows 10 SDK (recommended)
 * Visual Studio 2019 with C++ compilers and Windows 10 SDK
 * Visual Studio 2017 with C++ compilers and Windows 10 SDK
 * Visual Studio 2015 with C++ compilers and Windows 10 SDK

Visual Studio 2017 (or newer) is detected using `vswhere.exe` as well as VSCOMNTOOLS environment variables.
Visual Studio 2015 is detected by looking at "SOFTWARE\Microsoft\VisualStudio\14.0_Config\InstallDir" in the registry as well as VSCOMNTOOLS environment variables.
Windows 10 SDK is detected by looking at "SOFTWARE\Wow6432Node\Microsoft\Microsoft SDKs\Windows\v10.0\InstallationFolder" in the registry.

```
[这里：unable-to-build-il2cpp](https://forum.unity.com/threads/unable-to-build-il2cpp-in-2021-2.1189441/) 建议使用Visual Studio Installer补充安装一些C++相关的工具包。
![](https://github.com/iningwei/SelfPictureHost/blob/master/Blog/il2cpp%E6%8A%A5%E9%94%99vs%E5%A4%84%E7%90%86.png?raw=true)

3,若依旧报错类似``Building Library\Bee\artifacts\WinPlayerBuildProgram\ei6vj\ejx6_b_vm6.lump.obj failed with output:
i3ib_vm6.lump.cpp``
可以考虑把如下脚本放到Assets目录下(来自[这里](https://forum.unity.com/threads/unity-2022-1-22f1-with-il2cpp-not-working.1359580/))
```csharp
#if UNITY_EDITOR
using System;
using UnityEditor.Build;
using UnityEditor.Build.Reporting;
public class MsvcStdextWorkaround : IPreprocessBuildWithReport
{
    const string kWorkaroundFlag = "/D_SILENCE_STDEXT_HASH_DEPRECATION_WARNINGS";
    public int callbackOrder => 0;
    public void OnPreprocessBuild(BuildReport report)
    {
        var clEnv = Environment.GetEnvironmentVariable("_CL_");
        if (string.IsNullOrEmpty(clEnv))
        {
            Environment.SetEnvironmentVariable("_CL_", kWorkaroundFlag);
        }
        else if (!clEnv.Contains(kWorkaroundFlag))
        {
            clEnv += " " + kWorkaroundFlag;
            Environment.SetEnvironmentVariable("_CL_", clEnv);
        }
    }
}
#endif // UNITY_EDITOR
```
