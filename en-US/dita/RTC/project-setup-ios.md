# Project setup

In Xcode, follow the steps to create the environment necessary to add [feature] into your app.

 1. [Create a new project](https://help.apple.com/xcode/mac/current/#/dev07db0e578) for an iOS app using the **Single View App** template. Make sure you select **Storyboard** as the user interface.

    <note type="attention">If you have not added any team information, you can see an **Add account...** button. Click it, input your Apple ID, and click **Next** to add your team.</note>

 2. Integrate the Video SDK into your project.

    Go to **File** > **Swift Packages** > **Add Package Dependencies...**, and paste the following link:

    `https://github.com/AgoraIO/AgoraRtcEngine_iOS`

    In the next sheet, [specify version requirements](https://help.apple.com/xcode/mac/current/#/devb83d64851) according to your needs.
    <note type="attention">
    <ul>
    <li>Each SDK version has a corresponding Swift Package with the same version number. For the Video SDK, Agora provides Swift Packages for 3.4.3 and later versions.</li>
    <li>If you have issues installing this Swift Package, try going to <b>File</b> > <b>Swift Packages</b> > <b>Reset Package Caches</b>.</li>
    <li>For more integration methods, see <a href="see-also-ios.md#other-approaches-to-integrate-the-sdk">Other approaches to integrating the SDK</a>.</li>
    </ul>
    </note>

 3. [Enable automatic signing](https://help.apple.com/xcode/mac/current/#/dev23aab79b4) for your project.
 4. [Set the target devices](https://help.apple.com/xcode/mac/current/#/deve69552ee5) to deploy your iOS app.
 5. Add permissions for microphone and camera usage.
    Open the `info.plist` file in the project navigation panel, and [edit the property list](https://help.apple.com/xcode/mac/current/#/dev3f399a2a6) to add the following properties:
    - Privacy - Microphone Usage Description
    - Privacy - Camera Usage Description
