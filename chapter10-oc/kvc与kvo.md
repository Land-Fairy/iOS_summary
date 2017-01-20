http://www.jianshu.com/p/cfd553f250f9

## 1> KVC (Key-Value Coding)
### 1.1> 给私有(公有)的成员变量赋值

```
//Person类有一个私有成员变量_age
Person *person = [Person new];
[person setValue:@(18) forKey: @"age"];

```
or
```
//Person类有一个私有成员变量_age
Person *person = [Person new];
NSNumber *age = [person valueForKey:@"age"];
```

###### 注意：
- value的值一定是对象
- 使用KVC的方式，会首先寻找age这个没有下划线的成员变量，如果查找不到，会继续查找_age这个有下划线的成员变量，所以使用KVC的时候无论加不加下划线都可以
- valueForKeyPath：以访问对象中的对象属性的对象属性......就像一个path一样可以一直访问下去

---

### 1.2> 字典转模型
- setValuesForKeysWithDictionary:方法
###### 注意：
- 求字典的key和模型类的属性的名字要相同，并且key的数量不能多于类的属性

#### 1.2.1> 字典中有字典

```
//在Person类中有一个dog属性：
@property (nonatomic, strong) Dog *dog;
//Dog类中有一个name属性，有一个weight属性。
---
//需要转模型的字典为
NSDictionary *dict = { 
        @"name": @"joyann", 
        @"age": @(18), 
        @"dog": @{
                  @"name":@"A", 
                  @"weiget": @(12.0)
                 } 
        };

```

则需要两次调用 ```setValuesForKeysWithDictionary:```方法

```
//第一次结束后，它的dog属性的指针指向了一个字典，而不是Dog类的对象
[person setValusForKeysWithDicatonary: personDict];

person.dog = [[Dog alloc] init]; 
// 让指向字典的dog重新指向Dog对象
[person.dog setValuesForKeysWithDictonary: personDict[@"dog"]];
```

---
#### 1.2.2> 字典中有数组

```
//Person 有一个 dogs 数组，数组中为Dog对象
@property (nonatomic, strong) NSArray< Dog *> *dogs;
//需要被转换的字典
NSDictionary *dict = { @"name": @"joyann", 
                        @"age": @(18),
                        @"dog": @{    @"name": @"A",
                                      @"weiget": @(12.0)}
                       @"dogs": @[ @{@"name": @"B",
                                       "weight": @(13.0)},
                                   @{@"name": @"C",
                                       "weight": @(14.0)}
                                    ]};
```

此时，需要遍历数组，然后将其中的字典元素转换成对应的模型数据

```
[person setValuesForKeysWithDictionary: personDict];

NSMutableArray *tempDogs = [NSMutableArray array];
for (NSDictionary *dogDict in person.dogs) {
    Dog *dog = [[Dog alloc] init];
      [dog setValuesForKeysWithDictionary: dogDict];

      [tempDogs addObject: dog];
}

person.dogs = tempDogs;
```
###### 注意：
- 给一个对象发送valueForKeyPath:也会提到这个key对应的value的值
- 如果给一个对象的数组属性发送这个消息，那么会得到这个数组中的对象的value组成的数组

###### 总结：
如果字典中有字典等，直接使用``` setValuesForKeysWithDictionary:``` 方法，对于属性获得的为字典，需要对其中的属性，继续 使用 ```  setValuesForKeysWithDictionary:``` ,
直到 字典 全部 被转为 模型

---

## 2> KVO（Key-Value Observing）

#### 2.1> 监听对象的属性变化

```
[self.person addObserver:self 
             forKeyPath:@"name" 
             options: NSKeyValueObserveringOptionOld | NSKeyValueObserveringOptionNew 
             context:@"other"
];
```

当name属性发生变化时，会给self发送下面这个消息：

```
- (void)observeValueForKeyPath:(NSString *)keyPath //监听的属性
                      ofObject:(id)object  //监听的对象
                      change:(NSDictionary *)change  //字典：key取决于监听时的 options
                      context:(void *)context;  //额外的数据(void *类型)，context传过来的
```

不使用时，需要remove操作
```
[self.person removeObserver:self forKeyPath:@"name"];
```
