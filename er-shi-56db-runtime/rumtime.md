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





