## AOP ????
## 1>情景
- 对于一个大项目，在在众多界面难以找到对应的viewController

### 1.1> 思路
- 1> 在每个页面即将出现时，打印对应的ViewController.
- 2> 即重写　viewWillAppear,并在里面添加自定义输出
- 3> 利用runtime特性，为UIViewController添加一个分类，并 进行 方法交换！！

### 1.2> 链接
[Github：UIViewController-Swizzled](https://github.com/RuiAAPeres/UIViewController-Swizzled)


### 1.3> Requirements

**Include the library:**

- libobjc.dylib

**import the category where you want to use it:**

- #######import "UIViewController+Swizzled.h"


### 1.4> 使用

**在AppDelegate中添加，可以获得所有的输出**

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    SWIZZ_IT;
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    [self.window makeKeyAndVisible];

    // Your code...

    return YES;
}
```
### 1.5> 代码

**UIViewController+Swizzled.m**


```
//
//  UIViewController+Swizzled.m
//
//  Created by Rui Peres on 12/08/2013.
//  Copyright (c) 2013 Rui Peres. All rights reserved.
//

#import "UIViewController+Swizzled.h"
#import <objc/runtime.h>

@implementation UIViewController (Swizzled)

// Poor's man flag. Used to know if the methods are already Swizzed
static BOOL isSwizzed;

static NSString *logTag = @"";

+(void)load
{
    isSwizzed = NO;
}

#pragma mark - Util methods

static void swizzInstance(Class class, SEL originalSelector, SEL swizzledSelector)
{
    Method originalMethod = class_getInstanceMethod(class, originalSelector);
    Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);
    
    /**
     *  Adds a new method to a class with a given name and implementation.
     *  method_getTypeEncoding：Returns a string describing a method's parameter and return types.
     *  返回：由于该类中已经包含 originalSelector，因此会返回    NO
     *  * 该方法 添加originalSelector 不会覆盖原来的
     *  下面代码的执行逻辑？？？
     *  * 不知下面代码逻辑是否为：1> viewWillAppear的实现 用 swizzviewDidAppear 替换
     *                       2> 如果 1>成功，则swizzledSelector 的实现 用viewWillAppear替换(如果这样，现在viewWillAppear不已经是被替换过的吗？？？)
     *                       3> 如果 2>未执行，则直接执行 3>
     */
    BOOL didAddMethod =class_addMethod(
                                    class,// The class to which to add a method
                                    originalSelector,//A selector that specifies the name of the method being added.
                                    method_getImplementation(swizzledMethod),//A function which is the implementation of the new method
                                    method_getTypeEncoding(swizzledMethod)//types： 一个定义该函数返回值和参数的字符串
                                       );
    
    if (didAddMethod)
    {
       /**
         *  Replaces the implementation of a method for a given class.
         *  *swizzledSelector:  A selector that identifies the method whose implementation you want to replace.
         */
        class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod),method_getTypeEncoding(originalMethod));
    }
    else
    {
        /**
         *  Exchanges the implementations of two methods.
         *  相当于两个 函数指针没变，但是其 指向的函数实现  调换了下
         */
        method_exchangeImplementations(originalMethod, swizzledMethod);
    }
}

- (void)logWithLevel:(NSUInteger)level
{
    NSString *paddingItems = @"";
    for (NSUInteger i = 0; i<=level; i++)
    {
        paddingItems = [paddingItems stringByAppendingFormat:@"--"];
    }
    
    NSLog(@"%@%@-> %@", logTag, paddingItems, [self.class description]);
}


#pragma mark - SwizzMethods

- (void)printPath
{
    if ([self parentViewController] == nil)
    {
        [self logWithLevel:0];
    }
    else if([[self parentViewController] isMemberOfClass:[UINavigationController class]])
    {
        UINavigationController *nav = (UINavigationController *)[self parentViewController];
        NSInteger integer = [[nav viewControllers] indexOfObject:self];
        [self logWithLevel:integer];
    }
    else if ([[self parentViewController] isMemberOfClass:[UITabBarController class]])
    {
        [self logWithLevel:1];
    }
}
/**
 *  这一块代码被加载进内存中，由viewWillAppear指向，因此在【self viewWillAppear】时，该块代码被执行！！
 */
-(void)swizzviewDidAppear:(BOOL)animated
{
    [self printPath];
    
    // Call the original method (viewWillAppear)
    [self swizzviewDidAppear:animated];
}

#pragma mark - Public methods

+ (void)swizzIt
{
    // We won't do anything if it's already swizzed
    if (isSwizzed)
    {
        return;
    }
    
    swizzInstance([self class],@selector(viewDidAppear:),@selector(swizzviewDidAppear:));
    
    isSwizzed = YES;
}

+ (void)swizzItWithTag:(NSString *)tag
{
    logTag = tag;
    [self swizzIt];
}
/**
 *  undo 即 将其 实现调换回来
 */
+ (void)undoSwizz
{
    // We won't do anything if it has not been Swizzed
    if (!isSwizzed)
    {
        return;
    }
    
    isSwizzed = NO;
    swizzInstance([self class],@selector(swizzviewDidAppear:),@selector(viewDidAppear:));
}

@end

```
---
**UIViewController+Swizzled.h**

```
//
//  UIViewController+Swizzled.h
//
//  Created by Rui Peres on 12/08/2013.
//  Copyright (c) 2013 Rui Peres. All rights reserved.
//

#import <UIKit/UIKit.h>

#define SWIZZ_IT [UIViewController swizzIt];
#define SWIZZ_IT_WITH_TAG(tag) [UIViewController swizzItWithTag:tag];

#define UN_SWIZZ_IT [UIViewController undoSwizz];

/**
 Used to map your way inside your application, by logging the name
 of your UIViewController + a representation of how deep you are
 */
@interface UIViewController (Swizzled)

/**
 It will swizz the methods:
 viewDidLoad
 @return void
 */
+ (void)swizzIt;

/**
 It will swizz the methods:
 viewDidLoad
 and prepend "tag" to each line of log
 @return void
 */
+ (void)swizzItWithTag:(NSString *)tag;

/**
 It will undo what was done with the swizzIt
 @return void
 */
+ (void)undoSwizz;

@end

```
---