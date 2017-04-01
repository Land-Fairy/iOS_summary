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

> 1> 添加圆角

```objective-c
self.layer.cornerRadius = 10;
```

> 2> contents 属性
>
> - 用来设置图片  CGImageRef
>
> 对于 UIImageView的Layer设置圆角，发现没有效果?

- UIImageView 的图片 放在 layer 的 contents 属性 中去了，因此，直接设置 layer的 圆角，不起效果，因此可以 将 超过 根层 以外的东西 进行 裁减掉 才可行

> 其里面有一个mask层，初始时，mask层 与 根层 同样的大小

```
self.imgV.layer.maskToBouns = YES;
```

> 2> position 与 anchorPoint
>
> - 决定 layer 的位置，与 frame 的作用一致

> 3> transform
>
> - 形变属性

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

#### 1.4 UIView 与 CALayer 的选择

- 相同

> 1> UIView 和 CALayer 可以做出相同的效果

- 不同

> 1> UIView 多了一个 事件处理的功能(由于继承自UIResponder)

- 因此

> 1> 如果需要 和 用户 交互，选择 UIView；否则，选择UIView和CALayer都可以
>
> 2> CALayer的性能高一点，由于少了事件处理的功能

#### 1.5 实例

```objective-c
2.1.设置阴影
//默认图层是有阴影的, 只不过,是透明的 
_RedView.layer.shadowOpacity = 1;
//设置阴影的圆⾓角
_RedView.layer.shadowRadius =10; 
//设置阴影的颜⾊色,把UIKit转换成CoreGraphics框架,⽤用.CG开头
_RedView.layer.shadowColor = [UIColor blueColor].CGColor;
//2.2.设置边框 设置图层边框,在图层中使⽤用CoreGraphics的CGColorRef
_RedView.layer.borderColor = [UIColor whiteColor].CGColor; _RedView.layer.borderWidth = 2;
//2.3.设置圆⾓角 图层的圆⾓角半径,圆⾓角半径为宽度的⼀一半, 就是⼀一个圆 
_RedView.layer.cornerRadius = 50;
3.操作layer改变UIImageView的外观.
//设置图形边框
_imageView.layer.borderWidth = 2; _imageView.layer.borderColor = [UIColor whiteColor].CGColor;
//设置图⽚片的圆⾓角半径
_imageView.layer.cornerRadius = 50;
//裁剪,超出裁剪区域的部分全部裁剪掉
_imageView.layer.masksToBounds = YES;
```

