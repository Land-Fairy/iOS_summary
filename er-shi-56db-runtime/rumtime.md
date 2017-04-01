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

```
- (instancetype)dbd_imageNamed:(NSString *)imgStr{
    UIImage *img = [UIImage imageNamed:imgStr];
    /**
     *  做 其他 处理
     */
    return img;
}
```

> 但是，如果 很多地方 都用到了 imageNamed: 方法，则需要每个地方都修改，每个地方都导入 分类 头文件，很麻烦
>
> * 可以 交换 两个方法的实现即可

```
#import "UIImage+dbdImageNamed.h"
#import <objc/message.h>
@implementation UIImage (dbdImageNamed)
/**
 *  load 方法 在类 第一次加载的时候 调用
 *  在整个文件被加载到运行时，在 main 函数调用之前被 ObjC 运行时调用的钩子方法
 */
+ (void)load{
    /**
     *  随翻 load 方法 只会被 调用 一次， 但是仍然要确保 方法 交换 只会执行一次
     */
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        /**
         *  获取 类 的 类 方法
         *  应该 获取 类的  类方法 还是 实例方法，取决于 需要交换的类是  类方法 还是  实例方法
         */
        Method imagIMP = class_getClassMethod([self class], @selector(imageNamed:));

        Method dbd_imagIMP = class_getClassMethod([self class], @selector(dbd_imageNamed:));
        /**
         *  交换 两个 方法
         */
        method_exchangeImplementations(imagIMP, dbd_imagIMP);
    });
}


+ (instancetype)dbd_imageNamed:(NSString *)imgStr{
    /**
     *  注意： 此时 调用 dbd_imageNamed,实际 上 调用的 是 imageNamed 的实现
     */
    UIImage *img = [UIImage dbd_imageNamed:imgStr];
    /**
     *  做 其他 处理
     */
    NSLog(@"dgdgg");
    return img;
}

@end
```

> 此时：在原来地方 调用 ImageNamed: 方法，发现实际上执行的 确实 我们 新的方法

* 原来 方法 结构

![](/assets/QQ20170401-211855.png)

* 交换后的 方法结构

![](/assets/QQ20170401-212532.png)

> 即：此时 如果 调用 imageNamed：方法，则 会直接 找到  dbd_imageNamed：方法的实现；相当于 原来调用 dbd\_imageNamed方法_
>
> * ImageNamed 消息 仍然 会去 类的 dispatch table里面去寻找 imageNamed 这个IMP
> * 变得只是 这个IMP 指向的地址而已！！

## 3. 给分类添加属性

### 3.1 分类为什么不能直接添加属性

- @property 在 类 中做了什么？
> 我们为 DBDPerson类 添加了一些 成员变量 和 属性，然后 打印一下 该类 的 成员变量，属性和方法



```
{
    NSString *string11;
    NSInteger va12;
}
@property (nonatomic, strong) NSString *st1;
@property (nonatomic, strong) NSString *st2;
@property (nonatomic, strong) NSString *st3;
@property (nonatomic, strong) NSString *st4;
@property (nonatomic, assign) NSInteger va1;

```
> 然后 写一个 方法，来打印：使用的是RunTime



```
+ (void)getPropertyUsingRunTime{
    /**
     *  获取 该类 中 所有 属性
     */
    {
        unsigned int outCount = 0;
        objc_property_t *properties = class_copyPropertyList([self class], &outCount);
        for (int i=0; i<outCount; i++) {
            objc_property_t property = properties[i];
            NSLog(@"property---%s  %s",property_getName(property),property_getAttributes(property));
        }
    }
    
    /**
     *  获取 该 类中 的 所有 成员变量
     */
    {
        unsigned int outCount = 0;
        /// Ivar 代表的就是 成员变量
        Ivar *ivaArray = class_copyIvarList([self class], &outCount);
        for (int i=0; i<outCount; i++) {
            Ivar iva = ivaArray[i];
            NSLog(@"chengyaun---%s",ivar_getName(iva));
        }
    }
    /**
     *  获取 该类中的 所有方法
     */
    {
        unsigned int outCount = 0;
        Method * methodArray = class_copyMethodList([self class], &outCount);
        for (int i=0; i<outCount; i++) {
            Method method = methodArray[i];
            NSLog(@"fangfa----%@",NSStringFromSelector(method_getName(method)));
        }
    }
    
}


```
> 打印结果如下：
![](/assets/QQ20170401-224432.png)

> 通过 分析 上面 打印结果发现
> * 使用property 在 类里面 会自动生成 _的成员变量
> * 使用property 在 类里面 还会自动生成 对应的 setter 和 getter 方法

- @property 在 分类中 又做了什么呢？
> 我们为 DBDPerson 创建一个分类，并在分类中使用property添加一个属性


```
#import "DBDPerson.h"

@interface DBDPerson (cate)
@property (nonatomic, strong) NSString *cateStrTest;
@end

```
> 使用上面的方法 重新打印
![](/assets/QQ20170401-225802.png)
> 分析上图发现：
> * 分类中 使用 property 不会自动生成 _成员变量
> * 不会自动生成 get 和 set 方法
> * 因此：在外面直接调用.语法便会造成错误

- 总结：
> 其实分类中是可以为一个类添加属性的，但是一定做不到添加成员变量，不要混淆了成员变量和属性的概念
> 成员变量是一个类的东西，分类本身就不是一个类，分类本来就是OC里面通过运行时动态的为一个类添加的一些方法和属性等，不是一个真正的类
> 类最开始生成了很多基本属性，比如IvarList，MethodList，分类只会将自己的method attach到主类，并不会影响到主类的IvarList

- 那么，如何给 分类 添加属性呢？
- 取巧方式
> 可以通过 重写 setter 和 getter方法，并且 用一个静态变量来 充当 ”_成员变量“



```
#import "DBDPerson+cate.h"

@implementation DBDPerson (cate)
/**
 *  定义一个全局变量 来充当 _成员变量的作用
 */
static NSString *_castStrTest;

- (NSString *)cateStrTest{
    return _castStrTest;
}
- (void)setCateStrTest:(NSString *)cateStrTest{
    _castStrTest = cateStrTest;
}
@end

```
> 发现在外边直接使用 . 语法也不会造成错误











