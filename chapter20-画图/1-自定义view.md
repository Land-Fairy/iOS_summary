## 1. Quartz2D简介

> 1> 用来绘制各种各样 好看的  效果
>
> 2> 是 一些 绘图 库 的 集合而已

### 1.1 自定义 view

- ```UIImageView``` 为什么能 显示 图片，怎么做到的？？

> 相当于一个自定义的view，接受图片之后，将其 绘画 到 view 上面

=> 自己也可以 封装 一个 UIImageview，实现 同样的效果

### 1.2 如何 ```自定义 view ```呢?

- 1> 集成自 ```UIView```
- 2> 重写 ``` drawRect``` 方法
- 3> 在```drawRect```方法中进行绘制

### 1.3 为什么要重写```drawRect```方法？？

> 对于绘画，需要 画板 和 画笔
>
> 类似，Quartz2D提供的画板有哪些？

- layer
- pdf
- bitmap
- printer
- Window

> 绘画的步骤一般就是 ：
>
> 找到画板->绘画 而已
>
> 而计算机 上绘画则是：
>
> 找到画板->  画笔 绘画 -> 绘画保存到 画板 ->画板内容渲染到 需要的地方

- 问题在于：画板内容输出到哪？比如view上绘画，画板如何找到这个view？

> 因此 便需要 将  画板 和 view  建立 关联

- 如何建立联系？

> drawRect: 方法中，系统自定义了 一个 与 该view 相连的 画板！
>
> 因此，需要重写 drawRect方法