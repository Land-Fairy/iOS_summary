## 1. 动态 添加 方法

* 用处

> 用来 动态的 添加 方法
>
> * 如果一个类方法太多，其初始时加载会造成占用太大，因此考虑：用时加载（类似于 懒加载）

* 为DBDPerson类 动态添加 eat 方法

```object-c
DBDPerson *person = [[DBDPerson alloc] init];
// 调用 eat 方法
    
objc_msgSend(person,@selector(eat));
```

> 由于 没有实现 eat 方法，会直接 崩溃报错
>
> 消息 转发 机制
>
> * 如果  一个 消息 在 类中没有 找到 其对应的实现，则会调用
>
> `resolveInstanceMethod`
> 或者
>
> `resolveClassMethod`
>
> 方法，如果没有实现，或者返回NO，则 会去其 父类 进行 查找

> 因此 可以 重写  上述  两个方法

```
+ (BOOL)resolveInstanceMethod:(SEL)aSEL
{
    return [super resolveInstanceMethod:sel];
}
```

> 发现： 在 DBDPerson 类 未找到 eat 方法的实现后，会首先 调用 
>
> ```
> resolveInstanceMethod
> ```
>
> 方法，并且：其中



