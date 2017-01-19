## Using NSURLSession

- ```NSURLSession```提供 ```HTTP ```方式下载 的 API
  - 该API 提供 许多代理方法来实现 ```认证```，```后台下载```
  - 使用API时，app 创建一些列的 sessions，每个session包括相关的数据传输任务
  - 异步的
  - 提供 下载状态 和 下载进度
  - 提供 取消，重新开始，暂停 任务

## Understanding URL Session Concepts

- session 中 任务 的行为取决于：
  - the type of session
  - the type of task
  - whether the app was in the foreground when the task was created.

#### Types of session

- Default sessions 
  - 缓存在 硬盘，并在 钥匙链 中存储 ```credentials```
- Ephemeral sessions
  - 所有都存储在 RAM中，并且与session绑定，在session被invalidates时，自动清除
- Background sessions 
  - 与Default sessions类似，但是 有一个单独的进程来处理 数据传输，并有 特殊限制，参考```Background Transfer Considerations```

#### Types of Tasks

- Data tasks
  - 使用 ```NSData```  来 发送和 接收 数据
  - 适用于 短时，交互式的请求
- Download tasks 
  - 使用 文件 接收 数据
  - 支持 后台下载（当app 不在运行时）
- Upload tasks
  - 使用 文件 上传数据
  - 支持 后台上传 （当app 不在运行时）

#### Background Transfer Considerations

- 只有使用 ```background session configuration```之后 才能支持
- 由于 实际的传输 是在一个单独的 进程中，并且重启 app的进程 代价相对高，因此有一些特性不可用，导致下面几个限制
  - session 必须提供 ```event delivery```的代理
  - 仅仅支持```HTTP,HTTPS```,不支持```custom protocols```
  - 仅支持 从文件的上传任务
  - 如果 后台传输 是当 app在后台时初始化的，则configuration对象的 discretionary属性被当做true

#### iOS relaunched

- 当 后台传输 ```完成``` 或者 ```需要 credentials```时，如果app```不在运行```，则iOS会在后台```自动relaunched```你的app，并调用```UIApplicationDelegate``` 中的

```objective-c
// 当 app 已经 有 对应于 identifier的 session 对象，并且此时 app 处于 running 或者 suspended状态，则不需要创建一个新的 session  在此方法中。 此时，suspended apps 会被转到 background，一旦app进入running，identifier 对应的 NSURLSession 正常接收 events 和 正常 处理
// 如果 app 没有 对应 session，则 需要新建 identifier 对应的 session 和 NSURLSession 对象，用来处理 event
- (void)application:(UIApplication *)application handleEventsForBackgroundURLSession:(NSString *)identifier
  completionHandler:(void (^)())completionHandler
{
    /*
     Store the completion handler. The completion handler is invoked by the view controller's checkForAllDownloadsHavingCompleted method (if all the download tasks have been completed).
     */
	self.backgroundSessionCompletionHandler = completionHandler;
}
```

方法. 该方法 用来 保存 ```completionHandler```。

- 当session完成最后一个后台下载任务时，给其代理发送一个

  ```objective-c
  // 告诉 delegate ，session 中的 所有 messages  都被 传送
  - (void)URLSessionDidFinishEventsForBackgroundURLSession:(NSURLSession *)session
  {
      APLAppDelegate *appDelegate = (APLAppDelegate *)[[UIApplication sharedApplication] delegate];
      if (appDelegate.backgroundSessionCompletionHandler) {
          // 拿到 appdelegate 中保存的 completionHandler
          void (^completionHandler)() = appDelegate.backgroundSessionCompletionHandler;
          // 清空
          appDelegate.backgroundSessionCompletionHandler = nil;
          // 执行，注意 该 方法 需要在 主线程  中 执行
          completionHandler();
      }

      NSLog(@"All tasks are finished");
  }
  ```

  消息，在代理的该方法中，调用上述 completion handler，以便于让 OS知道 应当 suspend 你的app

- 当 app 处于 suspended时，如果 任务完成，则代理的```URLSession:downloadTask:didFinishDownloadingToURL:```会被调用

- 当任务需要credentials，代理的```URLSession:task:didReceiveChallenge:completionHandler:```或者

​        ```URLSession:didReceiveChallenge:completionHandler:```被调用

 ## Life Cycle of a URL Session

两种使用 NSURLSession API 的方法

- 使用 系统提供的 delegate
- 使用 自定义 delegate
  - 当 app 没在运行时，使用 background sessions 来 下载或者 上传
  - 自定义 认证
  - 自定义 SSL 证书检查
  - 决定是否应该下载到磁盘或者显示基于服务器返回的MIME类型或者其他的类似标准
  - 从传输流中拿数据上传(而不是一个NSData对象)
  - 以编程方式限制缓存
  - 以编程方式限制HTTP重定向

#### Life Cycle of a URL Session with System-Provided Delegates

前提：没有提供 delegate 对象

- 1. 创建一个 session configuration。对应background sessions，必须包含一个unique identifier
  2. 创建一个 session，并制定 上述的 configuration object，设置 delegate 为 nil
  3. 创建 task 对象，每一个 task 对象 都表示一个 资源request
     - 每个 task 初始 状态 均为 susended state，需要调用 resume 来开始
     - task  均为 NSURLSessionTask 的子类，分为 NSURLSessionDataTask,NSURLSessionUploadTask,NSURLSessionDownloadTask
     - 需要在创建 task时使用带有 completionHandler参数的方法，否则无法获取到数据
  4. task 完成时，NSURLSession 调用 task 的 completion handler
     - error 参数 代表的是 client-side errors。Server-side errors 则需要从HTTP status code 来辨别
  5. 对于不需要的 session ，需要 invalidate it，通过```invalidateAndCancel``` 或者 ```finishTasksAndInvalidate```.前者 直接 取消任务，后者 等待任务完成

#### Life Cycle of a URL Session with Custom Delegates

- 1. 创建一个 session configuration。对应background sessions，必须包含一个unique identifier

  2. 创建一个 session，并制定 上述的 configuration object，设置 delegate

  3. 创建 task 对象，每一个 task 对象 都表示一个 资源request

  4. 如果 server 返回 status code 表示 需要 authentication ，并且authentication需要 connection-level challenge，则

     - 对于 Session-level challenges 

       代理的```URLSession:didReceiveChallenge:completionHandler:```方法被调用

     - 对于 non-session-level challenges 

       代理的 ```URLSession:task:didReceiveChallenge:completionHandler:```被调用

  5. 如果接收到 HTTP 重定向

  6.  

  7.  

  8.  

  9.  

  10. 传输进度

      - download task

        ```URLSession:downloadTask:didWriteData:totalBytesWritten:totalBytesExpectedToWrite:``` 

        如果传输期间，用户通过```cancelByProducingResumeData:```  暂停或取消 task 

        然后，用户 resume 下载，需要 生成新的 task，使用```downloadTaskWithResumeData:``` 或者 ```downloadTaskWithResumeData:completionHandler:```方法，其中 resumeData 为 取消或者 暂停 task 时，保存的

        ​

      - data task

        ``` URLSession:dataTask:didReceiveData:``` 

  11.  

  12. 当 download task 成功完成时，

      ```objective-c
      - (void)URLSession:(NSURLSession *)session downloadTask:(NSURLSessionDownloadTask *)downloadTask didFinishDownloadingToURL:(NSURL *)downloadURL
      {
          /*
           The download completed, you need to copy the file at targetPath before the end of this block.
           As an example, copy the file to the Documents directory of your app.
          */
          NSFileManager *fileManager = [NSFileManager defaultManager];

          NSArray *URLs = [fileManager URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask];
          NSURL *documentsDirectory = [URLs objectAtIndex:0];

          NSURL *originalURL = [[downloadTask originalRequest] URL];
          NSURL *destinationURL = [documentsDirectory URLByAppendingPathComponent:[originalURL lastPathComponent]];
          NSError *errorCopy;
        
          BOOL success = [fileManager copyItemAtURL:downloadURL toURL:destinationURL error:&errorCopy];
          
          if (success)
          {
              dispatch_async(dispatch_get_main_queue(), ^{
                  UIImage *image = [UIImage imageWithContentsOfFile:[destinationURL path]];
                  self.imageView.image = image;
                  self.imageView.hidden = NO;
                  self.progressView.hidden = YES;
              });
          }
          else
          {
              /*
               In the general case, what you might do in the event of failure depends on the error and the specifics of your application.
               */
              BLog(@"Error during the copy: %@", [errorCopy localizedDescription]);
          }
      }
      ```

      方法被调用.其中 ``` downloadURL``` 为下载的临时 文件路径，需要在该方法return之前，将文件移动到所需的地方

  13. 当有任何 task 完成时，

      ``` objective-c
      - (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
      ```

      方法被调用，如果成功，则 error 为 nil

## Creating and Configuring a Session



## Fetching Resources Using System-Provided Delegates

使用 system-provided delegate ，主要是方便，简单，但有很大局限性

- 创建 configuration 对象 和 session 对象
- completion handler 

```objective-c
NSURLSession *sessionWithoutADelegate = [NSURLSession sessionWithConfiguration:defaultConfiguration];
NSURL *url = [NSURL URLWithString:@"https://www.example.com/"];
 
[[sessionWithoutADelegate dataTaskWithURL:url completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
    NSLog(@"Got response %@ with error %@.\n", response, error);
    NSLog(@"DATA:\n%@\nEND DATA\n", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
}] resume];
```



## Fetching Data Using a Custom Delegate



## Downloading Files

#### Download task example

```objective-c
NSURL *url = [NSURL URLWithString:@"https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/ObjC_classic/FoundationObjC.pdf"];
NSURLSessionDownloadTask *downloadTask = [backgroundSession downloadTaskWithURL:url];
[downloadTask resume];
```

#### Delegate methods for download tasks

```objective-c
- (void)URLSession:(NSURLSession *)session
      downloadTask:(NSURLSessionDownloadTask *)downloadTask
      didWriteData:(int64_t)bytesWritten
 totalBytesWritten:(int64_t)totalBytesWritten
totalBytesExpectedToWrite:(int64_t)totalBytesExpectedToWrite
{
    NSLog(@"Session %@ download task %@ wrote an additional %lld bytes (total %lld bytes) out of an expected %lld bytes.\n", session, downloadTask, bytesWritten, totalBytesWritten, totalBytesExpectedToWrite);
}
 
- (void)URLSession:(NSURLSession *)session
      downloadTask:(NSURLSessionDownloadTask *)downloadTask
 didResumeAtOffset:(int64_t)fileOffset
expectedTotalBytes:(int64_t)expectedTotalBytes
{
    NSLog(@"Session %@ download task %@ resumed at offset %lld bytes out of an expected %lld bytes.\n", session, downloadTask, fileOffset, expectedTotalBytes);
}
 
- (void)URLSession:(NSURLSession *)session
      downloadTask:(NSURLSessionDownloadTask *)downloadTask
didFinishDownloadingToURL:(NSURL *)location
{
    NSLog(@"Session %@ download task %@ finished downloading to URL %@\n", session, downloadTask, location);
 
    // Perform the completion handler for the current session
    self.completionHandlers[session.configuration.identifier]();
 
   // Open the downloaded file for reading
    NSError *readError = nil;
    NSFileHandle *fileHandle = [NSFileHandle fileHandleForReadingFromURL:location error:readError];
    // ...
 
   // Move the file to a new URL
    NSFileManager *fileManager = [NSFileManager defaultManager];
    NSURL *cacheDirectory = [[fileManager URLsForDirectory:NSCachesDirectory inDomains:NSUserDomainMask] firstObject];
    NSError *moveError = nil;
    if ([fileManager moveItemAtURL:location toURL:cacheDirectory error:moveError]) {
        // ...
    }
}
```



## Uploading Body Content

HTTP POST request 的 body 内容可以使用3中方式:

- NSData 对象
  - 如果 app 内存中已经有 存在的 数据，则最好使用 NSData方式
- file
  - 需要上传的 是 硬盘 上的 一个文件
- stream

不管选用的那种方式，都需要 实现

```objective-c
URLSession:task:didSendBodyData:totalBytesSent:totalBytesExpectedToSend: 
```

方法来获取 上传进度

#### Uploading Body Content Using an NSData Object

```objective-c
NSURL *textFileURL = [NSURL fileURLWithPath:@"/path/to/file.txt"];
NSData *data = [NSData dataWithContentsOfURL:textFileURL];
 
NSURL *url = [NSURL URLWithString:@"https://www.example.com/"];
NSMutableURLRequest *mutableRequest = [NSMutableURLRequest requestWithURL:url];
mutableRequest.HTTPMethod = @"POST";
[mutableRequest setValue:[NSString stringWithFormat:@"%lld", data.length] forHTTPHeaderField:@"Content-Type"];
[mutableRequest setValue:@"text/plain" forHTTPHeaderField:@"Content-Type"];
 
NSURLSessionUploadTask *uploadTask = [defaultSession uploadTaskWithRequest:mutableRequest fromData:data];
[uploadTask resume];
```

#### Uploading Body Content Using a File

```objective-c
NSURL *textFileURL = [NSURL fileURLWithPath:@"/path/to/file.txt"];
 
NSURL *url = [NSURL URLWithString:@"https://www.example.com/"];
NSMutableURLRequest *mutableRequest = [NSMutableURLRequest requestWithURL:url];
mutableRequest.HTTPMethod = @"POST";
 
NSURLSessionUploadTask *uploadTask = [defaultSession uploadTaskWithRequest:mutableRequest fromFile:textFileURL];
[uploadTask resume];
```









