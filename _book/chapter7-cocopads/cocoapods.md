# CocoaPods
## 1.为什么需要CocoaPods
在进行iOS开发的时候，总免不了使用第三方的开源库，比如SBJson、AFNetworking、Reachability等等。使用这些库的时候通常需要：
- 下载开源库的源代码并引入工程
- 向工程中添加开源库使用到的framework
- 解决开源库和开源库以及开源库和工程之间的依赖关系、检查重复添加的framework等问题
- 如果开源库有更新的时候，还需要将工程中使用的开源库删除，重新执行前面的三个步骤

**CocoaPods会为我们做好一切！**
## 2.什么是CocoaPods

**CocoaPods是一个用来帮助我们管理第三方依赖库的工具。**
它可以解决库与库之间的依赖关系，下载库的源代码，同时通过创建一个Xcode的workspace来将这些第三方库和我们的工程连接起来，供我们开发使用。
## 3.安装CocoaPods
### 3.1安装
CocoaPods是用Ruby实现的，要想使用它首先需要有Ruby的环境。幸运的是OS X系统默认的已经可以运行Ruby了，因此我们只需要执行以下命令：


```
sudo gem install cocoapods

```
在安装进程结束的时候，执行命令：
```
pod setup

```
如果没有报错，就说明一切安装就成功了！
## 4.使用
### 4.1创建Podfile文件
**首先进入到工程的根目录下，创建空白的Podfile文件**
### 4.2编辑Podfile
在Podfile文件中写入需要用到的第三方库，以SBJson、AFNetworking、Reachability三个库为例

```
platform :ios
pod 'Reachability', '~> 3.0.0'
pod 'SBJson', '~> 4.0.0'
platform :ios, '7.0'
pod 'AFNetworking', '~> 2.0'

```
### 4.3执行导入命令
首先进入工程根目录，然后执行pod install命令

```
pod install

```
CocoaPods就开始为我们做下载源码、配置依赖关系、引入需要的framework等一些列工作
### 4.4导入成功
导入成功后：工程的根目录下多了三个东西：CocoaPodsTest.xcworkspace、
Podfile.lock文件和Pods目录。
- 第三方库会被编译成静态库供我们正真的工程使用
 - CocoaPods会将所有的第三方库以target的方式组成一个名为Pods的工程，该工程就放在刚才新生成的Pods目录下。整个第三方库工程会生成一个名称为libPods.a的静态库提供给我们自己的CocoaPodsTest工程使用。
- 我们的工程和第三方库所在的工程会由一个新生成的workspace管理
 - 为了方便我们直观的管理工程和第三方库，CocoaPodsTest工程和Pods工程会被以workspace的形式组织和管理，也就是我们刚才看到的CocoaPodsTest.xcworkspace文件。

### 4.5使用CocoaPodsTest.xcworkspace文件来开发
## 5 Podfile.lock文件
执行完pod install之后，会生成一个Podfile.lock文件
- 该文件用于保存已经安装的Pods依赖库的版本

```
PODS:  
  - AFNetworking (2.1.0):  
    - AFNetworking/NSURLConnection  
    - AFNetworking/NSURLSession  
    - AFNetworking/Reachability  
    - AFNetworking/Security  
    - AFNetworking/Serialization  
    - AFNetworking/UIKit  
  - AFNetworking/NSURLConnection (2.1.0):  
    - AFNetworking/Reachability  
    - AFNetworking/Security  
    - AFNetworking/Serialization  
  - AFNetworking/NSURLSession (2.1.0):  
    - AFNetworking/NSURLConnection  
  - AFNetworking/Reachability (2.1.0)  
  - AFNetworking/Security (2.1.0)  
  - AFNetworking/Serialization (2.1.0)  
  - AFNetworking/UIKit (2.1.0):  
    - AFNetworking/NSURLConnection  
  - Reachability (3.0.0)  
  - SBJson (4.0.0)  
  
DEPENDENCIES:  
  - AFNetworking (~> 2.0)  
  - Reachability (~> 3.0.0)  
  - SBJson (~> 4.0.0)  
  
SPEC CHECKSUMS:  
  AFNetworking: c7d7901a83f631414c7eda1737261f696101a5cd  
  Reachability: 500bd76bf6cd8ff2c6fb715fc5f44ef6e4c024f2  
  SBJson: f3c686806e8e36ab89e020189ac582ba26ec4220  
  
COCOAPODS: 0.29.0  
```
### 5.1 Podfile.lock 作用
**为了保证版本一致**
使用 pod install之后，生成的Podfile.lock文件就记录下了当时最新Pods依赖库的版本。

项目中另一个人clone这份代码，在执行 pod install时，如果有lock文件，则其下载，安装的版本和前一个人一样，不会下载最新的版本。

### 常用命令
```
1.先升级Gem

 sudo gem update --system

2.切换cocoapods的数据源

 【先删除，再添加，查看】

 gem sources --remove https://rubygems.org/

 gem sources -a http://ruby.taobao.org/

 gem sources -l

3.安装cocoapods

 sudo gem install cocoapods

4.将Podspec文件托管地址从github切换到国内的oschina

 【先删除，再添加，再更新】

 pod repo remove master

 pod repo add master http://git.oschina.net/akuandev/Specs.git

 pod repo add master https://gitcafe.com/akuandev/Specs.git

 pod repo update

5.设置pod仓库

 pod setup

6.测试

 【如果有版本号，则说明已经安装成功】

 pod --version

7.利用cocoapods来安装第三方框架

 01 进入要安装框架的项目的.xcodeproj同级文件夹

 02 在该文件夹中新建一个文件Podfile

 03 在文件中告诉cocoapods需要安装的框架信息

 a.该框架支持的平台

 b.适用的iOS版本

 c.框架的名称

 d.框架的版本

8.安装

pod install --no-repo-update

pod update --no-repo-update

```
