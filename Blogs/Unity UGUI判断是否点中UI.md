## 法一
```csharp
#if UNITY_EDITOR
        pointerOverUI = EventSystem.current.IsPointerOverGameObject();
#elif UNITY_ANDROID || UNITY_IOS
        if (Input.touchCount > 0)
        {
            pointerOverUI = EventSystem.current.IsPointerOverGameObject(Input.GetTouch(0).fingerId);
        }
#endif
```
该方法不能用于在Input.GetMouseButtonUp(0)或TouchPhase.Ended的条件下来判断是否点中UI，在真机上手指松开时，EventSystem.current.IsPointerOverGameObject(Input.GetTouch(0).fingerId);返回的为false

## 法二
UI事件发出射线检测
```csharp
    public static bool IsPointerOverGameObject()
    {
        PointerEventData eventData = new PointerEventData(UnityEngine.EventSystems.EventSystem.current);
        eventData.pressPosition = Input.mousePosition;
        eventData.position = Input.mousePosition;

        List<RaycastResult> list = new List<RaycastResult>();
        UnityEngine.EventSystems.EventSystem.current.RaycastAll(eventData, list);

        return list.Count > 0;
    }

```

## 法三
Canvas的GraphicRaycaster发出射线检测
```csharp
public bool IsPointerOverGameObject(Canvas canvas)
    {
        PointerEventData eventData = new PointerEventData(EventSystem.current);
        eventData.pressPosition = Input.mousePosition;
        eventData.position = Input.mousePosition;

        GraphicRaycaster uiRaycaster = canvas.gameObject.GetComponent<GraphicRaycaster>();

        List<RaycastResult> results = new List<RaycastResult>();
        uiRaycaster.Raycast(eventData, results);

        return results.Count > 0;
    }
```