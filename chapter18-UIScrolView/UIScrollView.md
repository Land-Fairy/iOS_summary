#### 1. contentSize

- 告诉UIScrollView 需要显示内容的大小

  > 如果contetSize的<= 其 bounds.size，则不会有滚动效果

#### 2. UIScrollView  显示内容的小细节

- 滚动时，超出边框内容自动被隐藏

  > 即 clipToBounds = YES

- 拖动时可以查看超出边框的隐藏内容

  > 弹簧效果

#### 3. UIScrollView  的 理解

> 1) UIScrollView 其实 只是 UIView 的升级 版本而已，即增加了一个 滚动查案屏幕的功能而已
>
> 2) UIScrollView 的大小(Bounds) 相当于 一个 可视 范围，用户只能从这么大的窗口 来查看
>
>   其 contentSize  为景色的真实大小
>
> 3） 滚动 的 是  景色 ，UIScrollView  是固定的 
>
> 

#### 4.UIScorllView 的属性

- 能否滚动

  ```
  scrollEnabled
  ```

- 能否 交互

  ```
  userInteractionEnabled
  ```

- 弹簧效果

  ```
  bounces
  ```

- 不管有没有contentSize，总是有弹簧效果

  ```
  alwaysBounceHorizontal
  alwaysBounceVertical
  ```

- 滚动条

  ```
  showHorizontalScrollIndicator
  showVerticalScrollIndicator
  ```

  ![UIScrollView](../assets/UIScrollView.tiff)

- contentOffset 

  > 1) 内容的 偏移量 （水平 和 垂直）  set 方法
  >
  > 2) 得知当前内容滚动的位置    get 方法
  >
  > 3) contentOffset  的  x  =   UIScrollView 窗口的  左侧 - content 的 左侧    （即使左面 有  contentInset 也不影响）
  >
  > ​	右方  是  x 的 正方向
  >
  > 4) contentOffset  的  y =   UIScrollView 窗口的  上侧 - content 的 上侧    （即使左面 有  contentInset 也不影响）
  >
  > ​      下方  是  x  的 正方向

- contentInset

  > 增加一个  额外 滚动距离
  >
  > ​	假设 ：scrollview 的宽 为  40，content 的宽 为 60
  >
  > 则  scrollview  最多 能 向 右侧 滚动  20的范围
  >
  > ​	但是，如果  contentInset.right  = 20
  >
  > 则 scrollview  最多 能 滚动 的 距离 是  40
  >
  > ​       但是 ，contentOffset  的计算  仍然 是  scrollView  和 content 相减(而不是 和  contentInset)

#### 5. UIScrollView  子控件

- 不要通过```索引``` 在```subviews 数组``` 来访问 ```UIScrollView```的子控件

> UIScrollView 的子控件 ，默认会有 水平 和 垂直 两个 滚动条！！

#### 6. UIScrollView  的作用

如果 想要 在 UIScrollView 正在 滚动 或者 滚动到 某个位置的时候，执行一些操作，怎么做呢?

> 监听  UIScrollView 的 滚动 

##### 6.1 代理

> 1）当 UIScrollView 发生 滚动 的时候，自动 通知 其 代理 对象 ，让代理 知道 其滚动情况
>
> 2） 并不是只有控制器 可以成为  其 代理！
>
> ​	任何 OC 对象 都可以 作为 代理（只要 遵守 其协议)
>
> 3)  继承 自  UIView的 ，一般有 delegate  例如 TextField 可以监听 编辑
>
> ​	但是：TextField 也可以 使用 addTarget  方法
>
> ​    继承自 UIControl 的，有 addTarget  例如 UIButton等

- 用户滚动的时候，调用的函数

```
(void)scrollViewDidScroll:(UIScrollView *)scrollView
```

- 用户即将开始 拖拽

```
(void)scrollWillBeginDragging:(UIScrollView *)scrollView
```

- 用户即将停止拖拽

```
(void)scrollWillEndDragging:(UIScrollView *)scrollView ...
```

- 用户已经 停止 拖拽—即 用户 手松开了，但是 有惯性 会继续 减速滚动

```
(void)scrollDidEndDragging:(UIScrollView *)scrollView
```

- scrollview减速完毕时—即 完全 停止 滚动

```
scrollViewDidEndDragging
```





