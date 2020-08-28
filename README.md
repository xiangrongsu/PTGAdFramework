# PTGAdFramework
@[toc]
### 入门指南
本指南适用于希望借助 PTGAdSDK 通过 iOS 应用获利。
要展示广告和赚取收入，第一步是将移动广告 SDK 集成到应用中。集成 SDK 后，您可以选择一种广告样式，
#### 前提条件
*   使用 Xcode 11.0 或更高版本
*   定位到 iOS 9.0 或更高版本
*   创建PTGAdSDK 帐号并注册应用'
#### 导入 SDK
##### 手动导入 SDK
直接下载并解压缩 SDK 框架，然后将以下框架导入您的 Xcode 项目中：

*   PTGAdSDK.framework
导入完成后，请确保 Build Phases > Copy Bundle Resources 中有 


###### 需要添加其它 Framework

点击 Build Phases > Link Binary With Libraries，添加下面所需的 Framework:

*   libz.tbd
*   libbz2.tbd
*   libxml2.tbd
*   AdSupport.framework
*   CoreMedia.framework
*   CoreTelephony.framework
*   CoreLocation.framework
*   MediaPlay.framework
*   MobileCoreServices.framework
*   CoreMotion.framework
*   SystemConfiguration.framework
*   WebKit.framework
###### 头条还需要添加依赖库：

*  libresolv.9.tbd
*  MobileCoreServices.framework
*  MediaPlayer.framework
*  CoreMotion.framework
*  Accelerate.framework
*  libc++.tbd
*  libsqlite3.tbd
*  ImageIO.framework
#####  cocopods命令(推荐)
```
pod  'PTGAdFramework', '1.0.0'//暂时未上线 必选 可先选framework 包方式
pod  'Bytedance-UnionAD', '3.1.0.9'
pod  'GDTMobSDK', '4.11.10'
```
#### 全局配置
##### 应用传输安全

应用传输安全(ATS) 是 iOS 9 中引入的隐私设置功能。默认情况下，系统会为新应用启用该功能，并强制实施安全连接。为确保您的广告不受 ATS 影响，请执行以下操作：

在您的应用的 Info.plist 文件中，添加 NSAllowsArbitraryLoads 以停用 ATS 限制。

![image.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8zMDU2MDkwLTRmMzAyZjAwM2FiYTMxYWY?x-oss-process=image/format,png)

```
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>

```
##### 关于 iOS 14 AppTrackingTransparency

在 iOS 14 设备上，建议您在应用启动时调用 apple 提供的 AppTrackingTransparency 方案，获取用户的 IDFA 授权，以便PTGAdSDK提供更精准的广告投放和收入优化

```
<key>NSUserTrackingUsageDescription</key>
<string>需要获取您设备的广告标识符，以为您提供更好的广告体验</string>
```
*   [具体设置详见文章](https://www.jianshu.com/p/3002aeef5e8f）

权限请求窗口调用方法：requestTrackingAuthorization(completionHandler:)
##### 添加 -ObjC

将 -ObjC 链接器标记添加到项目的 Build Settings 下的 Other Linker Flags 中：

![image](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8zMDU2MDkwLTkyYmQzZDdmOTQxY2E4OWU?x-oss-process=image/format,png)


### 集合SDK的初始化
加载广告之前，请先使用 PTGAdSDK 应用 ID 进行初始化，此操作仅需执行一次，最好是在应用启动时执行。

##### 初始化
```
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

  [PTGAdSDKManager setAppKey:@"45227" appSecret:@"1r8hOksXStGASHrp" success:^(BOOL result) {
        //初始化成功以后进行开屏页面的加载
    }];
    return YES;
}

@end

```
##### 设置日志是否开启
```
[PTGAdSDKManager setLogEnable:false];
```
##### 获取PTGAdSDK版本号
```
NSString *sdkVersion = [PTGAdSDKManager getSDKVersion];
```

现已导入移动广告 SDK 并完成了初始化，您可以实现广告了。PTGAdSDK 提供了多种广告样式，您可以从中选择最适合您应用的样式。

###  开屏广告

开屏广告用于当用户进入您的应用展示，一般会展示 5 秒（部分可以跳过），倒计时结束会自动关闭。与插屏广告不同，开屏广告可以轻松地展示应用品牌（Logo 和名称），以便用户了解他们看到广告的环境。

本指南向您介绍如何将 PTGAdSDK 开屏广告集成到 iOS 应用中。

##### 前提条件

[导入 PTGAdSDK.framework]
##### 创建开屏广告

开屏广告由 PTGAdSDK 对象来请求和展示。要使用此对象，第一步是对其实例化并设置其广告单元 ID。例如，以下示例演示了如何在 AppDelegate 的 application:didFinishLaunchingWithOptions: 方法中创建：

```

@interface AppDelegate () <PTGSplashAdDelegate>
@property (nonatomic, strong) PTGSplashA *splashAd;
@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Init TaurusXAds
    ...

    // Init SplashAd
    UIView *bottomView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, [[UIScreen mainScreen] bounds].size.width, 80)];
    
    UIImageView *logo = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"SplashLogo"]];
    logo.accessibilityIdentifier = @"splash_logo";
    logo.frame = CGRectMake(0, 0, 311, 47);
    logo.center = bottomView.center;
    [bottomView addSubview:logo];
    self.nativeExpressAd = [[PTGSplashA alloc] initWithPlacementId:@"989" bottomView:bottomView];
    self.nativeExpressAd.delegate = self;
    self.nativeExpressAd.hideSkipButton = false;
    [self.nativeExpressAd loadAd];

    return YES;
}
@end

```

##### 底部区域设置
部分 Network 可以设置开屏广告底部展示的内容。
```
// 设置开屏底部显示的内容
UILabel *bottomView = [[UILabel alloc] initWithFrame:CGRectMake(0,0,[UIScreen mainScreen].bounds.size.width, 50)];
bottomView.backgroundColor = UIColor.whiteColor;
bottomView.text = @"App Name";
self.splashAd.bottomView = bottomView;

```

##### 隐藏设置
如果你想控制开屏中控制跳过按钮的隐藏，可以在 SplashAd 对象上调用 hideSkipButton: 方法。如果不调用，默认显示。
##### 展示广告
开屏广告加载完毕后会自动展示。
##### 广告事件
通过使用 PTGSplashAdDelegate，您可以监听广告生命周期事件，例如，广告何时关闭、用户何时离开应用等。
##### 注册开屏广告事件
要注册开屏广告事件，请将 PTGSplashA 上的 delegate 属性设置为实现 PTGSplashAdDelegate 协议的对象。一般情况下，实现开屏广告的类也充当代理类，在这种情况下，可将 delegate 属性设为 self，如下所示：
```
@interface AppDelegate () <PTGSplashAdDelegate>

@property (nonatomic, strong) PTGSplashA *splashAd;

@end

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    // Init 注册
    ...
    // Init SplashAd
    ...
    self.splashAd.delegate = self;

    return YES;
}

@end

```
##### 实现开屏广告事件
PTGSplashAdDelegate 中的每个方法都是可选方法，因此您只需实现所需的方法即可。以下示例实现了每个方法并将消息记录到控制台：
```
- (void)splashAdSuccessPresentScreen:(NSObject *)splashAd {
    // 加载成功
    // 获取加载成功的 LineItem 信息
}

- (void)splashAdFailToPresent:(NSObject *)splashAd withError:(NSError *)error {
    // 加载失败，error 为错误信息
}


- (void)splashAdClicked:(NSObject *)splashAd{
    // 广告点击
}

- (void)splashAdClosed:(NSObject *)splashAd {
    // 广告关闭
    NSLog(@"txAdSplashDidDismissScreen");
}
```
### 信息流广告
信息流广告是通过平台原本就有的界面组件向用户呈现的广告素材资源。这种广告使用您在构建布局时已经采用的同类视图进行展示，而且能以和周围视觉设计相称的形式呈现，让用户有浑然一体的使用体验。从代码编写的角度来说，这意味着当信息流广告加载时，由应用负责展示它们了。
本指南向您介绍如何将信息流广告集成到 iOS 应用中。
##### 创建广告
信息流广告通过 PTGNativeExpressAd 类加载，要使用此对象，第一步是对其实例化并设置其广告单元 ID。例如，以下示例演示了如何在 UIViewController 的 viewDidLoad 方法中创建 PTGNativeExpressAd：

```
@import PTGNativeExpressAd;
@import UIKit;

@interface ViewController ()

@property(nonatomic, strong) PTGNativeExpressAd *nativeExpressAd;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
 self.nativeExpressAd = [[PTGNativeExpressAd alloc] initWithPlacementId:placementId adSize:CGSizeMake(self.view.frame.size.width , 0)];

}

@end

```
##### 注意事项

建议高度给0 ,保证自适应布局

##### 广告事件
通过使用 PTGNativeExpressAdDelegete，您可以监听广告生命周期事件，例如，广告何时加载、用户何时离开应用等。

##### 实现广告事件
PTGNativeExpressAdDelegete 中的每个方法都是可选方法，因此您只需实现所需的方法即可。以下示例实现了每个方法，并将消息记录到控制台：

```
- (void)nativeExpressAdSuccessToLoad:(NSObject *_Nullable)nativeExpressAd views:(NSArray<__kindof UIView *> *_Nonnull)views{
    // 加载成功
    // 获取加载成功的 LineItem 信息
  //这里做渲染操作
 NSLog(@"%s",__FUNCTION__);
    self.expressAdViews = [NSMutableArray arrayWithArray:views];
    if (self.expressAdViews.count) {
        [self.expressAdViews enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
            [self.nativeExpressAd render:obj controller:self];
           
        }];
    }
    [self.tableView reloadData];
}

- (void)nativeExpressAdFailToLoad:(NSObject *_Nonnull)nativeExpressAd error:(NSError *_Nullable)error {
    // 加载失败，error 为错误信息
}

- (void)nativeExpressAdViewRenderSuccess:(UIView *_Nonnull)nativeExpressAdView{
  //这里做渲染成功
//进行刷新

}
- (void)nativeExpressAdViewClicked:(UIView *_Nonnull)nativeExpressAdView{
    // 广告点击
    NSLog(@"nativeExpressAdViewClicked");
}

- (void)nativeExpressAdViewClosed:(UIView *_Nonnull)nativeExpressAdView {
    // 广告关闭
}
```
##### 展示广告
加载广告后，剩下的工作就是将其展示给您的用户。
