## 1. Core Animation

> 1> 是一组非常强大的动画处理 API，很少代码就可以做出 很炫酷的效果
>
> 2> 作用于 layer层，而非 view
>
> 3> 自动在后台线程处理，而不会阻塞 主线程

### 1.1 继承结构

### 1.2 使用

> 1> CAAnimation  相当于 创建一个 动画对象，然后 告诉这个 动画 对象 怎么修改 CALayer 的属性，从而使其知道 怎样 达到 动画的效果

```objective-c
/**
     *  1.创建一个 动画对象
     */
    CABasicAnimation *anim = [CABasicAnimation animation];
    /**
     *  2. 设置 CALayer 的属性
     */
    anim.keyPath = @"position.x";
    anim.toValue = @200;
    
    /**
     *  由于 动画 结束之后，会自动 移除，并回到原位置（最后一个状态）
     *  因此：需要 设置 自动移除 属性 为 NO，并且设置 最后状态为 前一个状态
     */
    anim.removedOnCompletion = NO;
    anim.fillMode = kCAFillModeForwards;
    
    /**
     *  3. 添加到 layer
     */
    [self.redView.layer addAnimation:anim forKey:nil];
```

