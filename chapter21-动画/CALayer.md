## 1. CALayer

> 1> UIView 能显示，原因 在于 其 内部 有一个 layer
>
> 2> 创建 UIView时，内部自动创建了一个 CALayer对象，可以通过UIView的layer 属性访问
>
> 3> UIView 显示到屏幕 过程
>
> - drawRect方法 将内容绘制到 layer上
> - 系统将 layer 拷贝到屏幕
>
> 4> UIView 本身 不具有 显示 的功能，由于其 内部的 layer才能 显示
>
>  

> 理解：感觉是 UIView 的属性(颜色，frame)等等，只是 对 其内部 CALayer 属性的 封装 而已
>
> - CALayer 不能处理 事件
> - UIView 可以进行 事件处理

### 1.1 CALayer 的基本属性

> 添加圆角

```objective-c
self.layer.cornerRadius = 10;
```

> 但是，对于 UIImageView的Layer设置圆角，发现没有效果?

- UIImageView 的图片 放在 layer 的 contents 属性 中去了，因此，直接设置 layer的 圆角，不起效果，因此可以 将 超过 根层 以外的东西 进行 裁减掉 才可行

> 其里面有一个mask层，初始时，mask层 与 根层 同样的大小

```
self.imgV.layer.maskToBouns = YES;
```



### 1.2 CALayer 的层次

> 1> 可以像 添加 View 一样，对layer添加 layer

> 2> layer 展示 与 UIImageVie 同样的效果
>
> - 与view 一样 添加
> - 用 contents 属性 来 存放 CGImageRef 类型 的图片 即可

```
CALayer *layer = ...;
layer.frame = ..;
layer.contents = [UIImage imageNamed:].CGImage;
[self.layer  addSubLayer:layer];
```



### 1.3 CATransForm3D

> 与 作用于 View 上的 transform 类似

> 1> 作用于 view上
>
> 2> x,y,z 用来 表明 ，绕着 哪个轴进行旋转

```objective-c
self.contentView.layer.transform = CATransform3DRotate(self.contentView.layer.transform, M_PI_4, 0, 0, 1);
```

或者

> KVC 方式

```
NSValue *value = [NSValue valueWithCATransform3D...];
self.contentView.layer setValue:value forKeyPath:@"transform"；
```

