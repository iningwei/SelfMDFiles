编辑器下和EXE存储位置是不同的，这也不难理解，是为了避免存储位置相同导致开发和测试冲突。


编辑器下位置：``HKEY_CURRENT_USER\Software\Unity\UnityEditor\ExampleCompanyName\ExampleProductName``
EXE位置：``HKEY_CURRENT_USER\Software\ExampleCompanyName\ExampleProductName``

因此在编辑器下写的快捷工具,调用以下方法：
```csharp
PlayerPrefs.DeleteAll();
PlayerPrefs.Save();
```
只会清理掉编辑器下的储存信息，无法清除EXE对应的位置。

因此针对清除EXE的需求，笔者这里使用bat命令传参删除目标注册表(``要注意bat中if else的写法，括号的位置、空格，否则都会一闪而过``)：
```bat
@echo off
set CompanyName=%1
set ProductName=%2
echo CompanyName:%CompanyName%
echo ProductName:%ProductName%
if "%CompanyName%"=="" (
	echo CompanyName is null
) else (
	if "%ProductName%"=="" (
		echo ProductName is null
	) else (
		reg delete "HKEY_CURRENT_USER\SOFTWARE\%CompanyName%\%ProductName%" /f

		echo delete %CompanyName% %ProductName% 's all PlayerPrefs Data!
	)
)

pause
```

再在Unity编辑器内写个快捷键：
```csharp
    [MenuItem("工具/PlayerPrefs/删除本项目 PC包对应的所有PlayerPrefs信息")]
    public static void DoReset2()
    {
        string path = Application.dataPath + "/../PlayerPrefs-Tool/PC-Tool.bat";
        string companyName = Application.companyName;
        string productName = Application.productName;
        Process.Start(path, $"{companyName} {productName}");
    }
```


注意：在unity中还有个[EditorPrefs](https://docs.unity3d.com/ScriptReference/EditorPrefs.html)，一定要慎用其``EditorPrefs.DeleteAll()``方法，会删除编辑器的一些设置！！！
## 参考
[PlayerPrefs官方文档](https://docs.unity3d.com/ScriptReference/PlayerPrefs.html)