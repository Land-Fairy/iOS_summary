## 1. 动态 添加 方法

* 用处

> 用来 动态的 添加 方法
>
> * 如果一个类方法太多，其初始时加载会造成占用太大，因此考虑：用时加载（类似于 懒加载）

* 为DBDPerson类 添加 eat 方法

```
DBDPerson *person = [[DBDPerson alloc] init];

objc_msgSend(person,@selector(eat));
```

> 类 里面 有一个 dispatch table ，用来 存储 指向 方法的指针
>
> 当 给 person 对象，发送 eat 消息的时候：
>
> * 首先 根据 person 对象 中的  isa，找到 其 类对象 （DBDPerson）
> * 然后在 DBDPerson 类对象 的 dispatch table 中，找 eat这个 SEL 对应的  imp 方法名
> * 如果 找到了 IMP 方法名，则 通过IMP（实质就是 对应实现的地址） 可以找到 对应的实现；然后调用该实现即可
> * 如果 没有 找到 IMP 方法名，则 会调用
>
> `+ (BOOL)resolveInstanceMethod:(SEL)sel`  
> 如果  该 方法 返回的 为 NO（即没有自行处理）,则 会 通过 类对象的 isa 去其 父对象 中进行 继续查找（消息转发）
>
> 因此，可以在上述 方法 中 ，动态 添加方法即可

```
/**
 *  需要动态添加的 eat 方法的 实现
 *
 *  @param self 自带的参数
 *  @param _cmd 自带参数
 */
void myeatMethodIMP(id self, SEL _cmd){
    NSLog(@"My added eat func");
}

+ (BOOL)resolveInstanceMethod:(SEL)sel{
    /**
     *  如果是 eat 方法
     */
    if(sel == @selector(eat)){
        /**
         *  给 类 动态 添加 一个 方法
         *
         *  @param class] 要给 哪个  类 添加 方法
         *  @param sel    添加 方法  sel 名称
         *  @param IMP    添加 方法 的 实现 的 指针
         *
         *  @return 编码过的 参数列表
         */
        class_addMethod([self class], sel, (IMP)myeatMethodIMP, "v@:");
        return YES;
    }
    return [super resolveInstanceMethod:sel];
}
```

## 2. 交换方法实现

* 用处

> 比如：一个项目已经开发很久了，忽然需要 替换一个 方法，如果 直接 替换，则需要 修改的 地方太多，不太实际
>
> * 可以通过 重新写一个 满足需求的方法，让 需要替换的方法 和 此方法 进行 交换实现
> * 这样 在使用 老方法的时候，实际上 调用的 是 新的方法

* 替换 原来的 imageNmaged 方法

> 比如：原来的 需要 在 imageNamed 得到的图片 进行一些处理，则 一种 办法是 给UIImage 添加一个分类，
>
> 在分类中 重写一个 方法，然后 使用  重写的方法 即可

* 原来 方法 结构

![](/assets/QQ20170401-211855.png)

* 交换后的 方法结构

![](/assets/QQ20170401-212532.png)

> 即：此时 如果 调用 imageNamed：方法，则 会直接 找到  dbd_imageNamed：方法的实现；相当于 原来调用 dbd\_imageNamed方法_



