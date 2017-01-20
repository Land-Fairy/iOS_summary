## 1> UIRefreshControl下拉刷新
### 1.1> 注意
- 必须是在UITableViewController子类使用

### 1.2> 代码

```
//RootTaleViewController.h file
@interface RootTableViewController:UITableViewController
{
}
@end
//RootTableViewController.m file
@interface RootTableViewController()

@end
@implementation RootTableViewController
//省略不相干代码

- (void)viewDidLoad
{
    [super viewDidLoad];
    //初始化UIRefreshControl
    UIRefreshControl *refreshControl = [[UIRefreshControl alloc] init];
    [refreshControl addTarget:self action:@selector(refresh:) forControlEvents:UIControlEventValueChanged];
    [refreshControl setAttributedTitle:[[NSAttributedString alloc] initWithString:@"Refreshing..."]];
    [self setRefreshControl:refreshControl];
}
- (void)refresh:(id)sender
{
    //do something...
    // End Refreshing
    [(UIRefreshControl *)sender endRefreshing];

}
@end
```