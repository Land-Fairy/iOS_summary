# 1> 跳转回根页面
```
[self.navigationController popToRootViewController］
```
---
# 2> 跳转回指定的一个页面
### 法一：通过index定位
- 通过index定位：rootviewcontroller的index为0，以此类推。。最外层的index为  n-1
- 因此，跳转到最外层的上一层也可以用
```
[self.navigationController popToViewController:
    [self.navigationController.viewControllers 
            objectAtIndex:self.navigationController.viewControllers.count - 2] 
            animated:YES];
```
---
### 法二：通过class定位

```

for (UIViewController *controller in self.navigationController.viewControllers) {

 if ([controller isKindOfClass:[你要跳转到的Controller class]]) {

 [self.navigationController popToViewController:controller animated:YES];

 }

}

```
---
# 3> 页面间 反向 传值
### 3.1> NSNotificationCenter传值
---
### 3.2> 使用单例（自定义或系统）实现传数值
---
### 3.3> 代理委托
---
### 3.4> 利用NSUserDefult
---
### 3.5> 写入文件，读取文件
---
### 3.6> KVO ???