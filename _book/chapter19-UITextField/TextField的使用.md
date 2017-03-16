### 1. 允许和禁止 特定 输入

```
- (BOOL)textField:(UITextField *) shouldChangeCharactersInRange:(NSRange) range replacementString:(NSString :)string 
其中  返回 YES ，表示 允许 用户输入
	 返回 NO ，表示 禁止 用户输入
	string: 表示 用户输入的文字
	可以 判断 string，来决定  返回YES,NO，达到 禁止 和 允许 输入的效果
```

