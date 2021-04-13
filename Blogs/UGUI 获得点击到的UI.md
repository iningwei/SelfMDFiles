## 法一：射线检测
UGUI中无法直接使用通用的射线检测函数Physics.Raycast。取而代之的是EventSystem.current.RaycastAll,通过射线检测可以获得当前点中的UI最上层是哪一个，具体代码如下：
```csharp
using UnityEngine.EventSystems;

public GameObject ClickObject()
    {
        PointerEventData eventDataCurrentPosition = new PointerEventData(EventSystem.current);
        eventDataCurrentPosition.position = new Vector2
            (
#if UNITY_EDITOR
            Input.mousePosition.x, Input.mousePosition.y
#elif UNITY_ANDROID || UNITY_IOS
           Input.touchCount > 0 ? Input.GetTouch(0).position.x : 0, Input.touchCount > 0 ? Input.GetTouch(0).position.y : 0
#endif 
            );
        List<RaycastResult> results = new List<RaycastResult>();
        EventSystem.current.RaycastAll(eventDataCurrentPosition, results);
        if (results.Count > 0)
        {
            return results[0].gameObject;
        }
        else
        {
            return null;
        }
    }
```

## 法二：继承IPointerClickHandler
代码如下：
```csharp
using UnityEngine.EventSystems;

public class NewBehaviourScript : MonoBehaviour, IPointerClickHandler
{

    public void OnPointerClick(PointerEventData eventData)
    {
        print("当前点击的UI是："+ eventData.pointerEnter);
    }
}
```