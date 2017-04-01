## 导航栏

## 1.系统默认导航栏 返回按钮 及 样式

* 其文本默认为上一个ViewController的标题，如果上一个ViewController没有标题，则为Back（中文环境下为“返回”）
* 当上一页面标题过长时？
* 返回样式：
  * 可以右滑屏返回

## 2. 自定义左上角的 返回 按钮

* 自定义一个按钮，包装成 UIBarButtonItem即可

```objective-c
UIButton * leftBtn = [UIButton buttonWithType:UIButtonTypeSystem];
//位置不需要设置，但是 按钮的  大小 需要设置
leftBtn.frame = CGRectMake(0, 0, 25,25);
//只有一个图片，没有文字
[leftBtn setBackgroundImage:[UIImage imageNamed:@"nav_back"] 
        forState:UIControlStateNormal];
[leftBtn addTarget:self action:@selector(leftBarBtnClicked:)
        forControlEvents:UIControlEventTouchUpInside];
self.navigationItem.leftBarButtonItem = 
[[UIBarButtonItem alloc]initWithCustomView:leftBtn];
```

#### 2.1 效果问题：按钮偏右

###### 如何调整按钮的位置

* 方法一：设置 按钮中图片左侧 内边距为 负值（即向左侧移动）

  * 问题：虽然能够显示其 靠左，但是 图片超出 按钮这个父控件范围，超出部分应当不可点击
  * 解决：由于UINavigationBar的特殊性，即使点击按钮周围区域，也是可以出发返回效果，因此，不影响使用

* 方法二：利用UIBarButtonSystemItemFixedSpace的控件

  * 原理：由于UINavigationBar可以放置多个按钮，因此使用一个固定大小的空白按钮来占左侧位置，即可调整 返回按钮 的位置

  ```objective-c
  //创建返回按钮，代码与上类似
  。。。。
  UIBarButtonItem * leftBarBtn = [[UIBarButtonItem alloc]initWithCustomView:leftBtn];;
  //创建UIBarButtonSystemItemFixedSpace

  UIBarButtonItem * spaceItem = 
  [[UIBarButtonItem alloc]initWithBarButtonSystemItem:UIBarButtonSystemItemFixedSpace target:nil action:nil];
  //将宽度设为负值
  spaceItem.width = -15;
  //将两个BarButtonItem都返回给NavigationItem
  self.navigationItem.leftBarButtonItems = @[spaceItem,leftBarBtn];
  ```

#### 2.2 效果问题：右滑返回手势 无反应

###### 解决：重新添加导航栏的interactivePopGestureRecognizer的delegate即可

* 首先对   ViewController 添加 `UIGestureRecognizerDelegate` 协议

* 设置代理

  ```objective-c
  self.navigationController.interactivePopGestureRecognizer.delegate = self;
  ```

#### 2.3 全屏滑动返回 需求

* 在导航栏给导航栏添加`UIGestureRecognizerDelegate`协议

* 在ViewDidLoad中写入如下代码

  ```objective-c
  // 获取系统自带滑动手势的target对象
  id target = self.interactivePopGestureRecognizer.delegate;

  // 创建全屏滑动手势，调用系统自带滑动手势的target的action方法
  UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:target action:@selector(handleNavigationTransition:)];

  // 设置手势代理，拦截手势触发
  pan.delegate = self;

  // 给导航控制器的view添加全屏滑动手势
  [self.view addGestureRecognizer:pan];

  // 禁止使用系统自带的滑动手势
  self.interactivePopGestureRecognizer.enabled = NO;
  ```

  ​

## 3. 导航栏设置颜色

### 3.1 为什么导航栏颜色感觉浅一点？

> 由于 导航栏 默认 是 半透明的，因此 颜色 设置 会感觉 浅点

### 3.2 怎么修改？

* 使用 背景图片

> 给 导航栏设置 背景 图片的时候，默认会变为 不透明的，并且 背景图片的 默认区域为 0-69，即包括状态栏

* 设置 透明度 为 不透明

```
    self.navigationController.navigationBar.barTintColor = MainColor;
    self.navigationController.navigationBar.translucent = NO;
```

### 3.3 上述设置 之后，发现 控件位置 均下移了64？

> 透明状态下：
>
> * 默认 view 的 起点 是 屏幕 顶部，view 的大小 为 屏幕的大小
>
> 不透明状态下：
>
> * 默认 view 的起点是 \(0,64\)即 导航栏底部（放置遮盖）,view 的 大小为 \(ScreenW ， ScreenH - 64\)

* 但是 ，一般我们 控件 位置 都是 按照 view的起点为 \(0,0\)设置的，如何解决？？

> ```
> self.extendedLayoutIncludesOpaqueBars = YES;
> 这样，view的起点 与 大小 又会 与 透明状态下 一致
> ```

## 4. 如何 去除 导航栏底部的 横线

* 底部横线？？

> 导航如果不设置背景图片，那么 会自动 生成一个 背景图片 放在下面，并设置 一个 底部阴影 view
>
> * 底部横线 就是 默认的 那个 底部阴影 view

* 如何 去除

> 如果想要修改 底部阴影 view，那么 首先 需要 自己设置一个 背景 图片
>
> 然后，将 底部阴影 view 的image 设置为一个\[\[UIImage alloc\] init\]即可





