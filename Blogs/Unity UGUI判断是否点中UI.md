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