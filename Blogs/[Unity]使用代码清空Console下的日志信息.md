只可用于编辑器下。
public static void ClearLog()
{
    var assembly = Assembly.GetAssembly(typeof(UnityEditor.ActiveEditorTracker));//注意Assembly需要使用using System.Reflection
    var type = assembly.GetType(“UnityEditorInternal.LogEntries”);
    var method = type.GetMethod(“Clear”);
    method.Invoke(new object(), null);
}