### 1> 获取对象所有属性名
- class_copyPropertyList方法

```
typedef struct objc_property *objc_property_t;

unsigned int count;

    // 获取类的所有属性
    // 如果没有属性，则count为0，properties为nil
    objc_property_t *properties = class_copyPropertyList([self class], &count);
    NSMutableArray *propertiesArray = [NSMutableArray arrayWithCapacity:count];

    for (NSUInteger i = 0; i < count; i++) {
        // 获取属性名称
        const char *propertyName = property_getName(properties[i]);
        NSString *name = [NSString stringWithUTF8String:propertyName];

        [propertiesArray addObject:name];
    }

    // 注意，这里properties是一个数组指针，是C的语法，
    // 我们需要使用free函数来释放内存，否则会造成内存泄露
    free(properties);

```
---
### 2> 获取对象的所有属性名和属性值

- 上面拿到的属性名，使用 valueForKey 方法即可得到属性值

---
### 3> 获取对象的成员变量名称
- class_copyIvarList方法

```
    unsigned int count = 0;
    ///Ivar 变量
    ///获取成员变量的数组 （指针）
    Ivar *ivars = class_copyIvarList([self class], &count);

    NSMutableArray *results = [[NSMutableArray alloc] init];
    for (NSUInteger i = 0; i < count; ++i) {
        ///获取每个成员变量
        Ivar variable = ivars[i];
        ///获取成员变量的字符名称
        const char *name = ivar_getName(variable);
        ///将名称转为NSString
        NSString *varName = [NSString stringWithUTF8String:name];

        [results addObject:varName];
    }
    free(ivars);
```
---
### 4> 获取对象的所有方法名
- class_copyMethodList方法

```
    unsigned int outCount = 0;
    Method *methods = class_copyMethodList([self class], &outCount);

    for (int i = 0; i < outCount; ++i) {
        Method method = methods[i];

        // 获取方法名称，但是类型是一个SEL选择器类型
        SEL methodSEL = method_getName(method);
        // 需要获取C字符串
        const char *name = sel_getName(methodSEL);
        // 将方法名转换成OC字符串
        NSString *methodName = [NSString stringWithUTF8String:name];

        // 获取方法的参数列表
        int arguments = method_getNumberOfArguments(method);
        NSLog(@"方法名：%@, 参数个数：%d", methodName, arguments);
    }

    // 记得释放
    free(methods);
```
---
### 5> 运行时发消息
- objc_msgSend(class,selector);

#####　5.1> 需要关闭严格检查
###### 错误
> Too many arguments to function call ,expected 0,have2

###### 解决

Build Setting-->搜索 Enable Strict Checking of objc_msgSend Calls 改为 NO

---
###　6> Category扩展”属性”

```
const void *s_LSCallbackKey = "s_LSCallbackKey";

- (void)setCallback:(LSCallBack)callback {
// 第一个参数：给哪个对象添加关联
// 第二个参数：关联的key，通过这个key获取
// 第三个参数：关联的value
// 第四个参数:关联的策略
  objc_setAssociatedObject(self, s_LSCallbackKey, callback, OBJC_ASSOCIATION_COPY_NONATOMIC);
}

- (HYBCallBack)callback {
  return objc_getAssociatedObject(self, s_LSCallbackKey);
}
```

---
### 7> 交换方法

```
@implementation UIImage (image)
// 加载分类到内存的时候调用

+(void)load
{
    // 交换方法

    // 获取imageWithName方法地址
    Method imageWithName = class_getClassMethod(self, @selector(imageWithName:));

    // 获取imageWithName方法地址
    Method imageName = class_getClassMethod(self, @selector(imageNamed:));

    // 交换方法地址，相当于交换实现方式
    method_exchangeImplementations(imageWithName, imageName);

}

// 不能在分类中重写系统方法imageNamed，因为会把系统的功能给覆盖掉，而且分类中不能调用super.

// 既能加载图片又能打印

+(instancetype)imageWithName:(NSString *)name
{

    // 这里调用imageWithName，相当于调用imageName
    UIImage *image = [self imageWithName:name];

    if (image == nil) {

        NSLog(@"加载空的图片");
    }

    return image;
}
```
