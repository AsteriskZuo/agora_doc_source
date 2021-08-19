# See also

This section provides additional information for your reference.

## Sample project

Agora provides an open source sample project [start-sample-project](https://github.com/AgoraIO/Basic-Video-Call) on GitHub that implements one-to-one video call and group video call for your reference.

## Other approches to integrate the SDK

In addition to integrating the Agora Video SDK for iOS through Swift Package, you can also import the SDK into your project through CocoaPods or by manually copying the SDK files.

**Automatically integrate the SDK with CocoaPods**

 1. Install CocoaPods if you have not. See [Getting Started with CocoaPods](https://guides.cocoapods.org/using/getting-started.html#getting-started).
 2. In Terminal, navigate to the root of your project folder, and run the `pod init` command to create a `Podfile` in the project folder.
 3. Open the `Podfile`, and replace all contents with the following code. Remember to replace `Your App` with the target name of your project.
 4. In Terminal, run the `pod install` command to install the Agora Video SDK for iOS. When the SDK is installed successfully, you can see  `Pod installation complete!` in Terminal and an `xcworkspace` file in the project folder.
 5. Open the `xcworkspace` file for any further steps.

**Manually copy the SDK files**

  1. Go to [SDK Downloads](https://docs.agora.io/en/Video/downloads?platform=iOS), download the latest version of the Agora Video SDK, and extract the files from the downloaded SDK package.
  2. From the `libs` folder of the downloaded SDK package, copy the files or subfolders you need to the root of your project folder.
     <note>Certain files and subfolders under the <code>libs</code> folder are optional. See <a href="https://docs.agora.io/en/Video/faq/reduce_app_size_rtc?platform=iOS#extension_libraries">extension libraries</a> for details.</note>
  3. In Xcode, [link your target to the frameworks or libraries](https://help.apple.com/xcode/mac/current/#/dev51a648b07) you have copied. Be sure to choose **Embed & Sign** from the pop-up menu in the Embed column.

     <note type="attention"><ul><li>Apple does not allow an app extension to contain any dynamic library. If you are integrating the Agora SDK to an app extension, choose <b>Do Not Embed</b> in the Embed column.</li><li>The Agora SDK uses libc++ (LLVM) by default. Contact support@agora.io if you want to use libstdc++ (GNU). The SDK provides FAT image libraries with multi-architecture support for both 32/64-bit audio emulators and 32/64-bit audio/video real devices.</li></ul></note>


### Objective-C code sample

To implement Video Call in your app using Objective-C:

  1. Replace the contents in the  `ViewController.h` file with the following:
     ```objective-c
      #import <UIKit/UIKit.h>
    
      #import <AgoraRtcKit/AgoraRtcEngineKit.h>
    
      @interface ViewController : UIViewController <AgoraRtcEngineDelegate>
      @property (strong, nonatomic) AgoraRtcEngineKit *agoraKit;
    
      @end
      ```

  2. Replace the contents in the `ViewController.m` file with the following:
     ```objective-c
     #import "ViewController.h"
     #import <UIKit/UIKit.h>
    
     @interface ViewController ()
     @property (nonatomic, strong) UIView *localView;
     @property (nonatomic, strong) UIView *remoteView;
     @end
    
     @implementation ViewController
     - (void)viewDidLoad {
        [super viewDidLoad];
        [self initViews];
        [self initializeAndJoinChannel];
     }
    
     - (void)viewDidLayoutSubviews {
        [super viewDidLayoutSubviews];
        self.remoteView.frame = self.view.bounds;
        self.localView.frame = CGRectMake(self.view.bounds.size.width - 90, 0, 90, 160);
     }
    
     - (void)initViews {
        self.remoteView = [[UIView alloc] init];
        [self.view addSubview:self.remoteView];
        self.localView = [[UIView alloc] init];
        [self.view addSubview:self.localView];
     }
    
     - (void)initializeAndJoinChannel {
        // Pass in your App ID here
        self.agoraKit = [AgoraRtcEngineKit sharedEngineWithAppId:@"Your App ID" delegate:self];
        [self.agoraKit enableVideo];
        AgoraRtcVideoCanvas *videoCanvas = [[AgoraRtcVideoCanvas alloc] init];
        videoCanvas.uid = 0;
        videoCanvas.renderMode = AgoraVideoRenderModeHidden;
        videoCanvas.view = self.localView;
        [self.agoraKit setupLocalVideo:videoCanvas];
        // Pass in your token and channel name here
        [self.agoraKit joinChannelByToken:@"Your App ID" channelId:@"Channel name" info:nil uid:0 joinSuccess:^(NSString * _Nonnull channel, NSUInteger uid, NSInteger elapsed) {
        }];
     }
    
     - (void)rtcEngine:(AgoraRtcEngineKit *)engine didJoinedOfUid:(NSUInteger)uid elapsed:(NSInteger)elapsed {
        AgoraRtcVideoCanvas *videoCanvas = [[AgoraRtcVideoCanvas alloc] init];
        videoCanvas.uid = uid;
        videoCanvas.renderMode = AgoraVideoRenderModeHidden;
        videoCanvas.view = self.remoteView;
        [self.agoraKit setupRemoteVideo:videoCanvas];
     }
    
     - (void)applicationWillTerminate:(NSNotification *)notification{
        [self.agoraKit leaveChannel:nil];
        [AgoraRtcEngineKit destroy];
     }
     @end
     ```
