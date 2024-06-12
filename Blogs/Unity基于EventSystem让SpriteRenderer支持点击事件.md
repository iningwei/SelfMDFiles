若2D场景中使用了UGUI的UI系统，则默认是有EventSystem的。否则可以创建个UGUI的物体，然后保留EventSystem，删除其它不需要的。

场景主相机添加``Physics2DRaycaster``组件，并妥善设置其EventMask属性。


为SpriteRenderer物体添加``BoxCollider2D``碰撞体组件，否则其接受不到射线检测。

下面扩展一个SpriteRendererButton组件，并添加到SpriteRenderer物体上：
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Events;
using UnityEngine.EventSystems;


public class SpriteRendererButtonClickEvent : UnityEvent
{
}

public class SpriteRendererButton : MonoBehaviour, IPointerClickHandler
{
    public bool enable = true;
    public SpriteRendererButtonClickEvent onClick = new SpriteRendererButtonClickEvent();

    public void OnPointerClick(PointerEventData eventData)
    {
        if (enable)
        {
            onClick?.Invoke();
        }
    }
}

```
后面的用法就和传统Button一样了！

同理3D项目中物体也可以使用类似的方式,使得3D物体可以使用我们熟悉的类似UGUI的点击事件。

- 扩展阅读
[Unity | 射线检测及EventSystem总结](https://blog.csdn.net/weixin_39766005/article/details/137076373)