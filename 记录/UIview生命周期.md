
在 iOS 中，`UIView` 的生命周期是指它从创建到销毁的整个过程，主要包括以下几个阶段：

1. **初始化阶段**：
   - `init` 方法：在对象被创建时调用，用于初始化对象的基本属性和状态。
   - `initWithFrame:` 方法：是 `UIView` 的指定初始化方法之一，用于初始化并设置视图的初始位置和大小。
   - `awakeFromNib` 方法：如果视图是从 xib 或 storyboard 加载的，则在对象加载完成后调用，可以在这里执行一些与界面相关的初始化操作。
2. **布局阶段**：
   - `layoutSubviews` 方法：在视图的布局发生变化时调用，可以在这里更新视图的布局和子视图的位置。
   - `setNeedsLayout` 方法：用于标记视图需要重新布局，系统会在下一个运行循环周期内调用 `layoutSubviews` 方法来更新布局。
3. **显示阶段**：
   - `willMoveToWindow:` 方法：在视图即将被添加到窗口上时调用。
   - `didMoveToWindow` 方法：在视图被添加到窗口上后调用。
   - `willMoveToSuperview:` 方法：在视图即将被添加到父视图上时调用。
   - `didMoveToSuperview` 方法：在视图被添加到父视图上后调用。
4. **渲染阶段**：
   - `drawRect:` 方法：在视图需要绘制内容时调用，你可以在这里自定义绘制视图的内容。
5. **事件响应阶段**：
   - 用户与视图进行交互时，系统会根据用户的操作触发相应的事件，如 `touchesBegan:`, `touchesMoved:`, `touchesEnded:`, `touchesCancelled:` 等。
6. **销毁阶段**：
   - 当视图被销毁时，会调用 `dealloc` 方法来释放对象占用的内存。

这些方法和阶段组成了 `UIView` 的生命周期，在应用中你可以根据需要重写其中的某些方法来实现自定义的行为，例如自定义绘制、布局、动画等。









1. **UIViewContentModeScaleToFill：**视图内容将被拉伸以填充视图的边界框。这可能会导致内容的拉伸失真。
2. **UIViewContentModeScaleAspectFit：**视图内容将被缩放以适应视图的边界框，同时保持内容的宽高比。这样可以确保内容完整地显示在视图内，但可能会留有空白区域。
3. **UIViewContentModeScaleAspectFill：**视图内容将被缩放以填充视图的边界框，同时保持内容的宽高比。这样可以填充视图，但可能会裁剪内容的一部分。