# Implement a client for [product-name]

This section shows how to use the [sdk-name] to implement [product-name] in your app step by step.

<note>The code samples in this section are written in Swift. If you prefer programming with Objective-C, see <a href="see-also-ios.md#objective-c-code-sample">Objective-C code sample</a>.</note>

## Create the UI

<p props="video live">In the interface, you have one frame for local video and another for remote video. </p>

 ```swift
 import UIKit
"start-see-also-ios.md#objective-c-code-sample"
     var localView: UIView!
     var remoteView: UIView!
     
     override func viewDidLoad() {
         super.viewDidLoad()
         initView()
      }
   
     override func viewDidLayoutSubviews() {
         super.viewDidLayoutSubviews()
         remoteView.frame = self.view.bounds
         localView.frame = CGRect(x: self.view.bounds.width - 90, y: 0, width: 90, height: 160)
     }
   
     func initView() {
         remoteView = UIView()
         self.view.addSubview(remoteView)
         localView = UIView()
         self.view.addSubview(localView)
     }
 }
 ```

 ## Implement the [product-name] logic

 When your app opens, you create an [IRtcEngine] instance, enable the video, join a channel, and publish the local video to the lower frame layout in the UI. When another user joins the channel, your app catches the join event and adds the remote video to the top frame layout in the UI.

 The following figure shows the API call sequence of implementing Video Call. 

 ![start-api-sequence-ios]

 To implement this logic, take the following steps:

 1. Import the Agora kit.

    In `ViewController.swift`, add the following line after `import UIKit`:

    ```swift
     import AgoraRtcKit
    ```

    And add the `agoraKit` variable in the `ViewController` class:

    ```swift
    class ViewController: UIViewController {
        var localView: UIView!
        var remoteView: UIView!
        // Add agoraKit here
        var agoraKit: AgoraRtcEngineKit?
    }
    ```

 2. Initialize the app and join the channel.

    Call the core methods for initializing the app and joining a channel. In the following sample code, we use an `initializeAndJoinChannel` function to encapsulate these core methods.

    In `ViewController.swift`, add the following lines after the `initView` function:
    <p conref = "conref/get-started-sample-code.dita#get-started-sample-code/init-ios"/>

 3. Add the remote interface when a remote [user] joins the channel.

    In `ViewController.swift`, add the following lines after the `ViewController` class:

     ```swift
     extension ViewController: AgoraRtcEngineDelegate {
         // This callback is triggered when a remote user joins the channel
         func rtcEngine(_ engine: AgoraRtcEngineKit, didJoinedOfUid uid: UInt, elapsed: Int) {
             let videoCanvas = AgoraRtcVideoCanvas()
             videoCanvas.uid = uid
             videoCanvas.renderMode = .hidden
             videoCanvas.view = remoteView
             agoraKit?.setupRemoteVideo(videoCanvas)
         }
     }
     ```

## Start and stop your app

Now you have created the [product-name] functionality, start and stop the app. In this implementation, a [feature] starts when the user opens your app. The call ends when the user closes your app.

 1. When the view is loaded, call `initializeAndJoinChannel` to join a [feature] channel.

    In `ViewController.swift`, add the `initializeAndJoinChannel` function inside the `viewDidLoad` function:.

     ```swift
    override func viewDidLoad() {
            super.viewDidLoad()
            initView()
            // Add this line
            initializeAndJoinChannel()
         }
     ```

 2. When the user closes this app, clean up all the resources used by your app.

    In `ViewController.swift`, add `applicationWillTerminate` after the `initializeAndJoinChannel` function.

     ```swift
    func applicationWillTerminate(notification: NSNotification) {
            agoraKit?.leaveChannel(nil)
            AgoraRtcEngineKit.destroy()
        }
     ```