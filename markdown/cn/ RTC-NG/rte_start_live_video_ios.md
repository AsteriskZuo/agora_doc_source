本文介绍如何使用 Agora SDK 快速实现视频直播。

视频直播和视频通话的区别就在于，直播频道的用户有角色之分。你可以将角色设置为主播或者观众，主播可以发送和接收音视频流，观众只能接收流。

## 示例项目

Agora 在 GitHub 上提供开源的[互动直播示例项目](https://github.com/AgoraIO/API-Examples/tree/dev/3.4.200/iOS/APIExample/Examples/Basic/JoinChannelVideo)。在实现相关功能前，你可以下载并查看源代码。

## 前提条件

- Xcode 9.0 或以上版本
- 支持 iOS 8.0 或以上版本的 iOS 设备
- 有效的 [Agora 账户](https://docs.agora.io/cn/Agora%20Platform/sign_in_and_sign_up) 和 [App ID](https://docs.agora.io/cn/Agora%20Platform/token?platform=iOS#getappid)

> 如果你的网络环境部署了防火墙，请根据[应用企业防火墙限制](https://docs.agora.io/cn/Agora%20Platform/firewall?platform=iOS")打开相关端口。

## 准备开发环境

本节介绍如何创建项目，并将 Agora SDK 集成至你的项目中。

### 创建 iOS 项目

参考以下步骤创建一个 iOS 项目。如果已有 iOS 项目，可以直接查看[集成 SDK](#IntegrateSDK)。

<details>
	<summary><font color="#3ab7f8">创建 iOS 项目</font></summary>

1. 打开 **Xcode** 并点击 **Create a new Xcode project**。
2. 选择项目类型为 **Single View App**，并点击 **Next**。
3. 输入项目信息，如项目名称、开发团队信息、组织名称和语言，并点击 **Next**。

	**Note**：如果你没有添加过开发团队信息，会看到 **Add account…** 按钮。点击该按钮并按照屏幕提示登入 Apple ID，完成后即可选择你的账户作为开发团队。
4. 选择项目存储路径，并点击 **Create**。
5. 将你的 iOS 设备连接至电脑。
6. 进入 **TARGETS > Project Name > General > Signing** 菜单，选择 **Automatically manage signing**，并在弹出菜单中点击 **Enable Automatic**。
	
	![](https://web-cdn.agora.io/docs-files/1568803609379)
</details>

<a name="IntegrateSDK"></a>
### 集成 SDK

参照以下步骤将 Agora SDK 集成到你的项目中。

1. 获取最新版的 Agora SDK，然后解压。SDK 包中有两种 `AgoraRtcKit.framework` 和 `AgoraRtcCryptoLoader.framework`，区别如下：

   - `libs` 文件夹内的 `AgoraRtcKit.framework` 和 `AgoraRtcCryptoLoader.framework` 包含 armv7 和 arm64 架构，不支持模拟器。集成该库后，app 可以直接上架 App Store。

   - `ALL_ARCHITECTURE` 文件夹内的 `AgoraRtcKit.framework` 和 `AgoraRtcCryptoLoader.framework` 包含 armv7、arm64 和 x86_64 架构，支持模拟器。集成该库后，app 不能直接上架 App Store，你需要在上架前手动移除库中的 x86_64 架构。

     ![iOS SDK 包内容](https://web-cdn.agora.io/docs-files/1591775772856)

2. 将 `AgoraRtcKit.framework` 复制到项目文件夹下。

3. 打开 Xcode（以 Xcode 11.0 为例），进入 **TARGETS > Project Name > General > Frameworks, Libraries, and Embedded Content** 菜单，点击 **+**，再点击 **Add Other…** 添加 `AgoraRtcKit.framework`。添加完成后，项目会自动链接其他系统库。为保证动态库的签名和 app 的签名一致，你需要将动态库的 **Embed** 属性设置为 **Embed & Sign**。

   > - 根据 Apple 官方要求，app 的 Extension 不允许包含动态库。如果工程中的 Extension 需要集成 SDK，则集成动态库时需将文件状态改为 Do Not Embed。
   > - 如需使用媒体流加密功能，需添加 `AgoraRtcCryptoLoader.framework`。添加后 app 体积会增大。

### 添加媒体设备权限

根据场景需要，在 `info.plist` 文件中，点击 **+** 图标开始添加如下内容，获取相应的设备权限：

| Key                                    | Type   | Value                                                        |
| :------------------------------------- | :----- | :----------------------------------------------------------- |
| Privacy - Microphone Usage Description | String | 使用麦克风的目的，例如：for a call or live interactive streaming。 |
| Privacy - Camera Usage Description     | String | 使用摄像头的目的，例如：for a call or live interactive streaming。 |

## 实现互动直播

本节介绍如何实现互动直播。互动直播的 API 调用时序见下图：

![iOS 视频直播 API 时序图](https://web-cdn.agora.io/docs-files/1619681788599)

### 1. 创建用户界面

根据场景需要，为你的项目创建互动直播的用户界面。若已有用户界面，可以直接查看[导入类](#ImportClass)。

如果你想实现一个视频直播，我们推荐你添加如下 UI 元素：

- 主播视频窗口
- 退出频道按钮

当你使用示例项目中的 UI 设计时，你将会看到如下界面：

![iOS 示例项目 UI](https://web-cdn.agora.io/docs-files/1568802379788)

<a name="ImportClass"></a>
### 2. 导入类

在项目中导入 `AgoraRtcKit` 类：

```objective-c
// Objective-C
#import <AgoraRtcKit/AgoraRtcEngineKit.h>
```
```swift
// Swift
import AgoraRtcKit
```

> Agora Native SDK 默认使用 libc++ (LLVM)，如需使用 libstdc++ (GNU)，请联系 sales@agora.io。SDK 提供的库是 Fat Image，包含 32/64 位模拟器、32/64 位真机版本。

### 3. 初始化 AgoraRtcEngineKit

在调用其他 Agora API 前，需要创建并初始化 `AgoraRtcEngineKit` 对象。

调用 `sharedEngineWithConfig` 方法，传入获取到的 App ID，将频道场景设置为直播，即可初始化 `AgoraRtcEngineKit`。

你还可以根据场景需要，在初始化时注册想要监听的回调事件。

```objective-c
// Objective-C
- (void)initializeAgoraEngine {
     AgoraRtcEngineConfig *config = [[AgoraRtcEngineConfig alloc] init];
     // 传入 App ID。
     config.appId = appId;
     // 设置频道场景为直播。
     config.channelProfile = AgoraChannelProfileLiveBroadcasting;
     // 创建并初始化 AgoraRtcEngineKit 实例。
     self.agoraKit = [AgoraRtcEngineKit sharedEngineWithConfig:config delegate:self];
}
```
```swift
// Swift
func initializeAgoraEngine() {
     let config = AgoraRtcEngineConfig()
     // 传入 App ID。
     config.appId = KeyCenter.AppId
     // 设置频道场景为直播。
     config.channelProfile = .liveBroadcasting
     // 创建并初始化 AgoraRtcEngineKit 实例。
     agoraKit = AgoraRtcEngineKit.sharedEngine(with: config, delegate: self)
}
```

### 4. 设置用户角色

直播频道有两种用户角色：主播和观众，默认的角色为观众。设置频道场景为直播后，你可以在 app 中参考如下步骤设置用户角色：

1. 让用户选择自己的角色是主播还是观众
2. 调用 `setClientRole` 方法，然后使用用户选择的角色进行传参

注意，直播频道内的用户，只能看到主播的画面、听到主播的声音。加入频道后，如果你想切换用户角色，也可以调用 `setClientRole` 方法。

```objective-c
// Objective-C
 if (self.isBroadcaster) {
        self.clientRole = AgoraClientRoleAudience;
        if (self.fullSession.uid == 0) {
            self.fullSession = nil;
        }
    } else {
        self.clientRole = AgoraClientRoleBroadcaster;
    }
    // 设置用户角色。
    [self.rtcEngine setClientRole:self.clientRole];
```
```swift
// Swift
// 设置用户角色。
agoraKit.setClientRole(.audience)
agoraKit.setClientRole(.broadcaster)
```

### 5. 设置本地视图

成功初始化 `AgoraRtcEngineKit` 对象后，需要在加入频道前设置本地视图，以便在通话中看到本地图像。参考以下步骤设置本地视图：

- 调用 `enableVideo` 方法启用视频模块。
- 调用 `startPreview` 开启本地视频预览。
- 调用 `setupLocalVideo` 方法设置本地视图。

```objective-c
// Objective-C
// 启用视频模块。
[self.agoraKit enableVideo];
[self.agoraKit startPreview];
- (void)setupLocalVideo {
    AgoraRtcVideoCanvas *videoCanvas = [[AgoraRtcVideoCanvas alloc] init];
    videoCanvas.uid = 0;
    videoCanvas.view = self.localVideo;
    videoCanvas.renderMode = AgoraVideoRenderModeHidden;
    // 设置本地视图。
    [self.agoraKit setupLocalVideo:videoCanvas];
}
```
```swift
// Swift
// 启用视频模块。
agoraKit.enableVideo()
// 开启本地视频预览。
agoraKit.startPreview()
 
// 设置本地视图。
let videoCanvas = AgoraRtcVideoCanvas()
videoCanvas.uid = 0
videoCanvas.view = localVideo.videoView
videoCanvas.renderMode = .hidden
agoraKit.setupLocalVideo(videoCanvas)
```

### 6. 加入频道

完成初始化和设置本地视图后，你就可以调用 `joinChannelByToken` 方法加入频道。你需要在该方法中传入如下参数：

- `channelId`: 传入能标识频道的频道 ID。输入频道 ID 相同的用户会进入同一个频道。
- `token`: 传入能标识用户角色和权限的 Token。你可以设置如下值：
  - `nil`。
  - 控制台中生成的临时 Token。一个临时 Token 的有效期为 24 小时，详情见[获取临时 Token](https://docs.agora.io/cn/Agora%20Platform/token?platform=iOS#get-a-temporary-token)。
  - 你的服务器端生成的正式 Token。适用于对安全要求较高的生产环境，详情见[生成 Token](https://docs.agora.io/cn/Interactive%20Broadcast/token_server?platform=iOS)。
  > 若项目已启用 App 证书，请使用 Token。

- `uid`: 本地用户的 ID。数据类型为整型，且频道内每个用户的 `uid` 必须是唯一的。若将 `uid` 设为 0，则 SDK 会自动分配一个 `uid`，并在 `joinSuccessBlock` 回调中报告。
- `joinSuccessBlock`：成功加入频道回调。`joinSuccessBlock` 优先级高于 `didJoinChannel`，2 个同时存在时，`didJoinChannel` 会被忽略。 需要有 `didJoinChannel` 回调时，请将 `joinSuccessBlock` 设置为 `nil`。

更多的参数设置注意事项请参考 `joinChannelByToken` API 文档。

```objective-c
// Objective-C
- (void)joinChannel {
    // 加入频道。
    [self.agoraKit joinChannelByToken:token channelId:@"demoChannel1" info:nil uid:0 joinSuccess:^(NSString *channel, NSUInteger uid, NSInteger elapsed) {
    }];
}
```
```swift
// Swift
// 加入频道。
agoraKit.joinChannel(byToken: KeyCenter.Token, channelId: channelId, info: nil, uid: 0, joinSuccess: nil)
```

### 7. 设置远端视图

视频直播中，不论你是主播还是观众，都应该看到频道中的所有主播。在加入频道后，可通过调用 `setupRemoteVideo` 方法设置远端主播的视图。

远端主播成功加入频道后，SDK 会触发 `didJoinedOfUid` 回调，该回调中会包含这个主播的 `uid` 信息。在该回调中调用 `setupRemoteVideo` 方法，传入获取到的 `uid`，设置主播的视图。

```objective-c
// Objective-C
// 监听 didJoinedOfUid 回调。
// 远端主播加入频道时，会触发该回调。
// 可以在该回调中调用 setupRemoteVideo 方法设置远端视图。
- (void)rtcEngine:(AgoraRtcEngineKit *)engine didJoinedOfUid:(NSUInteger)uid elapsed:(NSInteger)elapsed {
    AgoraRtcVideoCanvas *videoCanvas = [[AgoraRtcVideoCanvas alloc] init];
    videoCanvas.uid = uid;
    videoCanvas.renderMode = AgoraVideoRenderModeHidden;
    videoCanvas.view = self.remoteView;
    // 设置远端视图。
    [self.agoraKit setupRemoteVideo:videoCanvas];
}
```
```swift
// Swift
// 监听 didJoinedOfUid 回调。
// 当远端主播成功加入频道时，会触发该回调。
// 可以在该回调中调用 setupRemoteVideo 方法设置远端视图。
func rtcEngine(_ engine: AgoraRtcEngineKit, didJoinedOfUid uid: UInt, elapsed: Int) {
    LogUtils.log(message: "remote user join: \(uid) \(elapsed)ms", level: .info)
     
    // Only one remote video view is available for this
    // tutorial. Here we check if there exists a surface
    // view tagged as this uid.
    let videoCanvas = AgoraRtcVideoCanvas()
    videoCanvas.uid = uid
    // the view to be binded
    videoCanvas.view = remoteVideo.videoView
    videoCanvas.renderMode = .hidden
    agoraKit.setupRemoteVideo(videoCanvas)
}
```

### 8. 离开频道

根据场景需要，如结束通话、关闭 app 或 app 切换至后台时，调用 `leaveChannel` 离开当前通话频道。

```objective-c
// Objective-C
[self.rtcEngine setupLocalVideo:nil];
    // 离开频道。
    [self.rtcEngine leaveChannel:nil];
    if (self.isBroadcaster) {
        [self.rtcEngine stopPreview];
    }
```
```swift
// Swift
func leaveChannel() {
    setIdleTimerActive(true)
    agoraKit.setupLocalVideo(nil)
    // 离开频道。
    agoraKit.leaveChannel(nil)
    if settings.role == .broadcaster {
       agoraKit.stopPreview()
    }
    navigationController?.popViewController(animated: true)
    }
```

## 运行项目

你可以在 iOS 设备上运行此项目。当成功开始视频直播时，主播可以看到自己的画面；观众可以看到主播的画面。