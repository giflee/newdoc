## FAQ

### 无法正常推送
 * 检查管理后台应用中是否配置过推送证书 p12 文件，此证书是否就是此 app bundle id 关联的推送证书
 * 检查证书的线上、测试环境是否跟管理后台配置的相同
 * 检查初始化时填的 appName 是否和管理后台“添加一个App”时填写的“App名称”一致
 * 检查 provision profile 是否包含了推送证书
 * 检查推送证书中是否有 p12 文件
 * 检查代码调试是否可以获取到 devicetoken
 * 检查第三方推送工具是否可以正常推送,如果不能，说明是证书本身的问题


### 进入访客聊天界面马上crash
 * 检查app工程配置－> Build Phases -> copy Bundle Resources 里面有没有QYResource.bundle；如果没有，必须加上

### 能否同时创建多个sessionVieController
 * 不能，需要保持全局唯一。每次调用 [[QYSDK sharedSDK] sessionViewController]; 会得到一个全新的 QYSessionViewController 对象，开发者需要保证此对象全局唯一。

### 怎么知道sessionVieController被pop了
 * 请参考 UINavigationControllerDelegate 中这个函数：

 ```objc
 -(nullable id <UIViewControllerAnimatedTransitioning>)navigationController:
 					(UINavigationController *)navigationController
 					animationControllerForOperation:(UINavigationControllerOperation)operation
 					fromViewController:(UIViewController *)fromVC
 					toViewController:(UIViewController *)toVC;
```

### sessionVieController的导航栏可以自定义吗
 部分自定义。 sessionVieController 会占用 self.navigationItem.title 和 self.navigationItem.rightBarButtonItem；navigationItem的其它部分，比如leftBarButtonItem等，您可以根据需要做任何自定义。

### 聊天界面可以自定义吗
 部分自定义。 具体可参考 QYCustomUIConfig 类，Demo源码中也有相关样例代码。

### 可以同时使用第三方推送工具吗
 可以。可以同时使用第三方推送工具和 SDK 的消息推送，两者可以共存，不会有任何冲突。