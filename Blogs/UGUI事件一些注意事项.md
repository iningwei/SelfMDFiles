1，Pointer相关接口在ScrollView内部使用时，当OnPointerMove触发后，鼠标（或者手指）未松开，都会立马触发OnPointerUp

``这个是Unity UGUI底层做的处理，在做拖动之前，取消任何向下的指针状态和选择!``

相关逻辑在PointerInputModule.cs:https://blog.csdn.net/qq_35272477/article/details/106586877

所以建议有相关需求的可以选择实现Drag接口。但是Drag接口的实现，会导致上层ScrollView的Drag事件被屏蔽。如果需求是不屏蔽ScrollView的Drag事件，那么在Drag接口实现的脚本中调用一下上层ScrollView的Drag相关方法。