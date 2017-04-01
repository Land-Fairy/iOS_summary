### 1. 特性界面

- 思路

> 1> 每次进入时，取出 版本号 与 已存储的版本号进行比较，若不等，则首先展示 特性界面, 并 存储 当前 版本号

```objective-c
NSString *currVersion = [NSBundle mainBundle].infoDictionary[@"CFBundleShortVersionString"];
```

