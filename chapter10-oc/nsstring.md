## 疑问：关于NSString 可以再次赋值的问题？

- 描述：NSString是不可变的，但是可以重新赋值
- 原因：NSString不可变是说，生成的一个NSString该对象是不可变的（相当于const），但是变量存储的只是该对象的地址，重新赋值即穿了另一个对象的地址给他。（与指向const对象的指针类似）。

## NSArray

- NSArray只能存储 Objective-C的对象，而不能存储原始的C语言基础数据类型。

- NSArray 不能够存储 nil

 - 一个原因是因为 arrayWithObjects:创建新的NSArray时，需要在列表的结尾添加nil,代表列表的结束。但使用字面量语法时，不需要补上Nil

## 枚举(NSEnumerator)

###### 请求枚举器(并非数组中的第一个对象)

```
- (NSEnumerator *)objectEnumerator;
```

###### 从后往前浏览

```
- (NSEnumeraotr *)reverseObjectEnumerator;
```

###### 获取数组中下一个对象

```
- (id)nextObject;
```

##### 使用

```
NSEnumerator *enumerator = [array objectEnumerator];
while(id thingie = [enumerator nextObject])
{
//处理
}
```

## 快速枚举
#### for in
#### enumerateObjectsUsingBlock

```
- (void)enumerateObjectsUsingBlock:
        (void(^)(id object,NSUInteger idx,BOOL *stop))block
```

##### 代码块的好处？

## 能否创建 NSString NSArray 或 NSDictionary的子类？
#### 类簇(class clusters)的概念

## 基本数据类型的封装
#### NSNumber
- 可以用来封装基本的数据类型

```
+ (NSNumber *) numberWithChar:(char)value;
...
//或者使用字面量语法
NSNumber *number = @123;
```
---
#### NSValue
- 可以封装任何类型的值(例：结构体)

```
+(NSValue *)valueWithBytes:(const void *)value  objCType:(const char *)byte;
```
######　例如

```
NSRect rect = NSMakeRect(1,2,30,40);
NSValue *value = [NSValue valueWithBytes: &rect objCType:@encode(NSRect)];
//对该对象的操作
```
###### 提取数值

```
- (void)getValue:(void *)buffer;
```

###### 提供常用的struct数据转为NSValue的便捷方法

```
+ (NSValue *)valueWithPoint:(NSPoint)aPoint;
- (NSPoint)pointValue;
```
---
## NSNull
如果确实需要存储一个表示"空"的值，就可以使用NSNull
- 其只有一个方法

```
+ (NSNull *)null;
```

#### NSNull 与 nil区别
---