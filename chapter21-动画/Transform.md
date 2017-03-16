## 1.Transform

### 1.1 平移

> 1> make 形式，平移相对于 第一次的位置,只会平移一次（相当于一次性）
>
> 2> 即使 将其位置 移动到 初始位置，也不会 在平移
>
> 3> 初始位置->旋转 ->平移
>
> - 发现 平移后 是 相对于  初始位置 平移的

```objective-c
self.contentView.transform = 
  					CGAffineTransformMakeTranslation(10, 10);
```

- 怎么 实现 只相对于 最 初始 位置的？ 发现 平移之后。更改 当前位置，之后，还是不会动？

> 2> form形式，每次平移 是相对于 上一次 的位置

```objective-c
self.contentView.transform = 		
		CGAffineTransformTranslate(self.contentView.transform, 10, 10);
```

### 1.2 旋转（与上类似，只展示 form）

> 1> make 形式  
>
> 2> form 形式

```objective-c
self.contentView.transform = CGAffineTransformRotate(self.contentView.transform, M_PI_4);
```

### 1.3 缩放(与上一致)

> 1> make 形式
>
> 2> form 形式

```objective-c
self.contentView.transform = CGAffineTransformScale(self.contentView.transform, 0.5, 0.5);
```

