## v4.2.2

v4.2.2 was released on July xx, 2023.

#### New features

1. **Wildcard token**

   This release introduces wildcard tokens. Agora supports setting the channel name used for generating a token as a wildcard character. The token generated can be used to join any channel if you use the same user id. In scenarios involving multiple channels, such as switching between different channels, using a wildcard token can avoid repeated application of tokens every time users joining a new channel, which reduces the pressure on your token server. See [Wildcard Tokens](https://docportal.shengwang.cn/cn/live-streaming-premium-4.x/wildcard_token?platform=All%20Platforms).

   <div class="alert info">All 4.x SDKs support using wildcard tokens.</div>

2. **Preloading channels**

   This release adds `preloadChannelByToken [1/2]` and `preloadChannelByToken [2/2]` methods, which allows a user whose role is set as audience to preload channels before joining one. Calling the method can help shortening the time of joining a channel, thus reducing the time it takes for audience members to hear and see the host.

   When preloading more than one channels, Agora recommends that you use a wildcard token for preloading to avoid repeated application of tokens every time you joining a new channel, thus saving the time for switching between channels. See [Wildcard Token](https://docportal.shengwang.cn/cn/live-streaming-premium-4.x/wildcard_token?platform=All%20Platforms).

3. **Customized background color of video canvas**

   In this release, the `backgroundColor` member has been added to `AgoraRtcVideoCanvas`, which allows you to customize the background color of the video canvas when setting the properties of local or remote video display.

4. **Video source types for local preview**

   To allow users in selecting different types of video sources for local video preview, this release introduces `startPreview [2/2]` and `stopPreview [2/2]`. You can call `startPreview [2/2]` and specify the type of video source to be previewed by setting the `sourceType` parameter, and call `stopPreview [2/2]` to stop the video preview.

   <div class="alert info"> The video source type specified in this method must match the video source type set in the `AgoraRtcVideoCanvas` of the `setupLocalVideo` method.</div>

#### Improvements

1. **Improved camera capture effect**

   This release has improved camera capture effect in the following aspects:

   1. Support for camera exposure adjustment

      This release adds `isCameraExposureSupported` to query whether the device supports exposure adjustment and `setCameraExposureFactor` to set the exposure ratio of the camera.

   2. Optimization of default camera selection

      Since this release, the default camera selection behavior of the SDK is aligned with that of the iOS system camera. If the device has multiple rear cameras, better shooting perspectives, zooming capabilities, low-light performance, and depth sensing can be achieved during video capture, thereby improving the quality of video capture.

2. **Virtual Background Algorithm Upgrade**

   This version has upgraded the portrait segmentation algorithm of the virtual background, which comprehensively improves the accuracy of portrait segmentation, the smoothness of the portrait edge with the virtual background, and the fit of the edge when the person moves. In addition, it optimizes the precision of the person's edge in scenarios such as meetings, offices, homes, and under backlight or weak light conditions.

3. **Channel media relay**

   The number of target channels for media relay has been increased to 6. When calling `startOrUpdateChannelMediaRelay` and `startOrUpdateChannelMediaRelayEx`, you can specify up to 6 target channels.

4. **Enhancement in video codec query capability**

   To improve the video codec query capability, this release adds the `codecLevels` member in `CodecCapAgoraVideoCodecCapInfoInfo`. After successfully calling `queryCodecCapability`, you can obtain the hardware and software decoding capability levels of the device for H.264 and H.265 video formats through `codecLevels`.

This release includes the following additional improvements:

1. The SDK automacially adjusts the frame rate of the sending end based on the screen sharing scenario. Especially in document sharing scenarios, this feature avoids exceeding the expected video bitrate on the sending end to improve transmission efficiency and reduce network burden.
2. To help users understand the reasons for more types of remote video state changes, the `AgoraVideoRemoteReasonCodecNotSupport` enumeration has been added to the `remoteVideoStateChangedOfUid` callback, indicating that the local video decoder does not support decoding the received remote video stream.

#### Issues fixed

This release fixed the following issues:

- Slow channel reconnection after the connection was interrupted due to network reasons.
- In screen sharing scenarios, the delay of seeing the shared screen was occasionally higher than expected on some devices.
- In custom video capturing scenarios, `setBeautyEffectOptions`, `setLowlightEnhanceOptions`, `setVideoDenoiserOptions`, and `setColorEnhanceOptions` could not load extensions automatically.

#### API changes

**Added**

- `startPreview [2/2]`
- `stopPreview [2/2]`
- `setCameraExposureFactor`
- `isCameraExposureSupported`
- `preloadChannelByToken [1/2]`
- `preloadChannelByToken [2/2]`
- `updatePreloadChannelToken`
- The following members in `AgoraRtcChannelMediaOptions`:
  - `publishThirdCameraTrack`
  - `publishFourthCameraTrack`
  - `publishThirdScreenTrack`
  - `publishFourthScreenTrack`
- `AgoraVideoCodecCapLevels`
- `AgoraVideoCodecCapabilityLevel`
- `backgroundColor` in `AgoraRtcVideoCanvas`
- `codecLevels` in `CodecCapAgoraVideoCodecCapInfoInfo`
- `AgoraVideoRemoteReasonCodecNotSupport` in `AgoraVideoRemoteReason`

## v4.2.1

This version was released on June 21, 2023.

#### Improvements

This version improves the network transmission strategy, enhancing the smoothness of audio and video interactions.

#### Fixed Issues

This version fixed the following issues:

- Inability to join channels caused by SDK's incompatibility with some older versions of AccessToken.
- After the sending end called `setAINSMode` to activate AI noise reduction, occasional echo was observed by the receiving end.
- Brief noise occurred while playing media files using the media player.
- Occasional crash after calling the `destroyMediaPlayer` method.


## v4.2.0

v4.2.0 was released on May 23, 2023.

#### Compatibility changes

If you use the features mentioned in this section, ensure that you modify the implementation of the relevant features after upgrading the SDK.

**1. Video capture**

This release optimizes the APIs for camera and screen capture function. As of v4.2.0, ensure you use the alternative methods listed in the table below and specify the video source by setting the `sourceType` parameter.

| Deleted Methods                                          | Alternative Methods       |
| :------------------------------------------------------- | :----------------------- |
| `startSecondaryCameraCapture` | `startCameraCapture`     |
| `stopSecondaryCameraCapture`   | `stopCameraCapture`      |

**2. Video data acquisition**

- The `onCaptureVideoFrame` and `onPreEncodeVideoFrame` callbacks are added with a new parameter called `sourceType`, which is used to indicate the specific video source type.
- The following callbacks are deleted. Get the video source type through the `sourceType` parameter in the `onPreEncodeVideoFrame` and `onCaptureVideoFrame` callbacks. 
  - `onScreenCaptureVideoFrame`
  - `onPreEncodeScreenVideoFrame`

**3. Channel media options**

- `publishCustomAudioTrackEnableAec` in `AgoraRtcChannelMediaOptions` is deleted. Use `publishCustomAudioTrack` instead.
- `publishTrancodedVideoTrack` in `AgoraRtcChannelMediaOptions` is renamed to `publishTranscodedVideoTrack`.
- `publishCustomAudioSourceId` in `AgoraRtcChannelMediaOptions` is renamed to `publishCustomAudioTrackId`.

**4. Miscellaneous**

- `didApiCallExecute` is deleted. Agora recommends getting the results of the API implementation through relevant channels and media callbacks.
- `enableDualStreamMode`[1/2] and `enableDualStreamMode`[2/2] are depredated. Use `setDualStreamMode`[1/2] and `setDualStreamMode`[2/2] instead.
- `startChannelMediaRelay`, `updateChannelMediaRelay`, `startChannelMediaRelayEx` and `updateChannelMediaRelayEx` are deprecated. Use `startOrUpdateChannelMediaRelay` and `startOrUpdateChannelMediaRelayEx` instead.

#### New features

**1. AI noise suppression**

This release introduces the AI noise suppression function. Once enabled, the SDK automatically detects and reduces background noises. Whether in bustling public venues or real-time competitive arenas that demand lightning-fast responsiveness, this function guarantees optimal audio clarity, providing users with an elevated audio experience. You can enable this function through the newly-introduced `setAINSMode` method and set the noise suppression mode as balance, aggressive or low latency according to your scenarios.

**2. Enhanced virtual background**

To increase the fun of real-time video calls and protect user privacy, this version has enhanced the virtual background feature. You can now set custom backgrounds of various types by calling the `enableVirtualBackground` method, including:

- Process the background as alpha information without replacement, only separating the portrait and the background. This can be combined with the local video mixing feature to achieve a portrait-in-picture effect.
- Replace the background with various formats of local videos.

**3. Video scenario settings**

This release introduces `setVideoScenario` for setting the video application scene. The SDK will automatically enable the best practice strategy based on different scenes, adjusting key performance indicators to optimize video quality and improve user experience. Whether it is a formal business meeting or a casual online gathering, this feature ensures that the video quality meets the requirements.

Currently, this feature provides targeted optimizations for real-time video conferencing scenarios, including:

- Automatically activate multiple anti-weak network technologies to enhance the capability and performance of low-quality video streams in meeting scenarios where high bitrates are required, ensuring smoothness when multiple streams are subscribed by the receiving end.
- Monitor the number of subscribers for the high-quality and low-quality video streams in real time, dynamically adjusting the configuration of the high-quality stream and dynamically enabling or disabling the low-quality stream, to save uplink bandwidth and consumption.

**4. Local video mixing**

This release adds the local video mixing feature. You can use the `startLocalVideoTranscoder` method to mix and render multiple video streams locally, such as camera-captured video, screen sharing streams, video files, images, etc. This allows you to achieve custom layouts and effects, making it easy to create personalized video display effects to meet various scenario requirements, such as remote meetings, live streaming, online education, while also supporting features like portrait-in-picture effect.

Additionally, the SDK provides the `updateLocalTranscoderConfiguration` method and the `didLocalVideoTranscoderErrorWithStream` callback. After enabling local video mixing, you can use the `updateLocalTranscoderConfiguration` method to update the video mixing configuration. Where an error occurs in starting the local video mixing or updating the configuration, you can get the reason for the failure through the `didLocalVideoTranscoderErrorWithStream` callback.

<div class="alert note">Local video mixing requires more CPU resources. Therefore, Agora recommends enabling this function on devices with higher performance.</div>

**5. Cross-device synchronization**

In real-time collaborative singing scenarios, network issues can cause inconsistencies in the downlinks of different client devices. To address this, this release introduces `getNtpWallTimeInMs` for obtaining the current Network Time Protocol (NTP) time. By using this method to synchronize lyrics and music across multiple client devices, users can achieve synchronized singing and lyrics progression, resulting in a better collaborative experience.
#### Improvements

**1. Voice changer**

This release introduces the `setLocalVoiceFormant` method that allows you to adjust the formant ratio to change the timbre of the voice. This method can be used together with the `setLocalVoicePitch` method to adjust the pitch and timbre of voice at the same time, enabling a wider range of voice transformation effects.

**2. Enhanced screen share**

This release adds the `queryScreenCaptureCapability` method, which is used to query the screen capture capabilities of the current device. To ensure optimal screen sharing performance, particularly in enabling high frame rates like 60 fps, Agora recommends you to query the device's maximum supported frame rate using this method beforehand.

This release also adds the `setScreenCaptureScenario` method, which is used to set the scenario type for screen sharing. The SDK automatically adjusts the smoothness and clarity of the shared screen based on the scenario type you set.

**3. Audio and video synchronization**

For custom video and audio capture scenarios, this release introduces `getCurrentMonotonicTimeInMs` for obtaining the current Monotonic Time. By passing this value into the timestamps of audio and video frames, developers can accurately control the timing of their audio and video streams, ensuring proper synchronization.

**4. Multi-camera capture**

This release introduces `startCameraCapture`. By calling this method multiple times and specifying the `sourceType` parameter, developers can start capturing video streams from multiple cameras for local video mixing or multi-channel publishing. This is particularly useful for scenarios such as remote medical care and online education, where multiple cameras need to be connected.

**5. Channel media relay**

This release introduces `startOrUpdateChannelMediaRelay` and `startOrUpdateChannelMediaRelayEx`, allowing for a simpler and smoother way to start and update media relay across channels. With these methods, developers can easily start the media relay across channels and update the target channels for media relay with a single method. Additionally, the internal interaction frequency has been optimized, effectively reducing latency in function calls.

**6. Custom audio tracks**

To better meet the needs of custom audio capture scenarios, this release adds `createCustomAudioTrack` and `destroyCustomAudioTrack` for creating and destroying custom audio tracks. Two types of audio tracks are also provided for users to choose from, further improving the flexibility of capturing external audio source:

- Mixable audio track: Supports mixing multiple external audio sources and publishing them to the same channel, suitable for multi-channel audio capture scenarios.
- Direct audio track: Only supports publishing one external audio source to a single channel, suitable for low-latency audio capture scenarios.
#### Issues fixed

This release fixed the following issues:

- When the host frequently switching the user role between broadcaster and audience in a short period of time, the audience members cannot hear the audio of the host.
- Occasional loss of the `firstRemoteVideoFrameOfUid` callback during channel media relay.
- The receiver actively subscribed to the high-quality stream but unexpectedly received a low-quality stream.
- Abnormal client status cased by an exception in the `onRemoteAudioStateChanged` callback.
#### API changes

**Added**

- `startCameraCapture`
- `stopCameraCapture`
- `startOrUpdateChannelMediaRelay`
- `startOrUpdateChannelMediaRelayEx`
- `getNtpWallTimeInMs`
- `setVideoScenario`
- `getCurrentMonotonicTimeInMs`
- `didLocalVideoTranscoderErrorWithStream`
- `startLocalVideoTranscoder`
- `updateLocalTranscoderConfiguration`
- `queryScreenCaptureCapability`
- `setScreenCaptureScenario`
- `setAINSMode`
- `createCustomAudioTrack`
- `destroyCustomAudioTrack`
- `AudioTrackConfig`
- `AgoraAudioTrackType`
- `AgoraApplicationScenarioType`
- `AgoraScreenCaptureFrameRateCapability`
- The `domainLimit` and `autoRegisterAgoraExtensions` members in `AgoraRtcEngineConfig`
- The `channelId` and `uid` parameters in `stateDidChanged` and `informationDidUpdated` callbacks
- The `sourceType` parameter in `onCaptureVideoFrame` and `onPreEncodeVideoFrame` callbacks
- The `AgoraVirtualBackgroundNone` and `AgoraVirtualBackgroundVideo` enumerators in `AgoraVirtualBackgroundSourceType`

**Deprecated**

- `enableDualStreamMode`[1/2]
- `enableDualStreamMode`[2/2]
- `startChannelMediaRelay`
- `startChannelMediaRelayEx`
- `updateChannelMediaRelay`
- `updateChannelMediaRelayEx`
- `didReceiveChannelMediaRelayEvent`
- `AgoraChannelMediaRelayEvent`

**Deleted**

- `startSecondaryCameraCapture`
- `stopSecondaryCameraCapture`
- `didApiCallExecute`
- `publishCustomAudioTrackEnableAec` in `AgoraRtcChannelMediaOptions` in `AgoraRtcChannelMediaOptions`
- `onScreenCaptureVideoFrame`
- `onPreEncodeScreenVideoFrame`

## v4.1.1

v4.1.1 was released on January xx, 2023.


#### Compatibility changes
**1. Default video encoding resolution**

As of this release, the SDK optimizes the video encoder algorithm and upgrades the default video encoding resolution from 640 × 360 to 960 × 540 to accommodate improvements in device performance and network bandwidth, providing users with a full-link HD experience in various audio and video interaction scenarios.

You can call the `setVideoEncoderConfiguration` method to set the expected video encoding resolution in the video encoding parameters configuration.

<div class="alert note">The increase in the default resolution affects the aggregate resolution and thus the billing rate. See <a href="./billing_rtc_ng">Pricing</a>.</div>

**2. Options for subscribing to remote video streams**
This release changes the name of `setRemoteVideo` to `setRemoteVideoSubscriptionOptions`, and the name of `setRemoteVideoEx` to `setRemoteVideoSubscriptionOptionsEx`. If you upgrade the SDK to this version or later, to avoid affecting your service, ensure that you modify the names of these two methods.

#### New features

**1. Instant frame rendering**

This release adds the `enableInstantMediaRendering` method to enable instant rendering mode for audio and video frames, which can speed up the first video or audio frame rendering after the user joins the channel.

**2. Video rendering tracing**

This release adds the `startMediaRenderingTracing` and `startMediaRenderingTracingEx` methods. The SDK starts tracing the rendering status of the video frames in the channel from the moment this method is called and reports information about the event through the `videoRenderingTracingResultOfUid` callback.

Agora recommends that you use this method in conjunction with the UI settings (such as buttons and sliders) in your app. For example, call this method at the moment when the user clicks the "Join Channel" button, and then get the indicators in the video frame rendering process through the `videoRenderingTracingResultOfUid` callback. This enables developers to facilitate developers to optimize the indicators to improve the user experience.



#### Improvements

**1. Video frame observer**

As of this release, the SDK optimizes the `onRenderVideoFrame` callback, and the meaning of the return value is different depending on the video processing mode:

- When the video processing mode is `AgoraVideoFrameProcessModeReadOnly`, the return value is reserved for future use.
- When the video processing mode is `AgoraVideoFrameProcessModeReadWrite`, the SDK receives the video frame when the return value is `YES`; the video frame is discarded when the return value is `NO`.

**2. Super resolution**

This release improves the performance of super resolution. To optimize the usability of super resolution, this release removes `enableRemoteSuperResolution`. Super resolution is now included in the online strategies of video quality enhancement which does not require extra configuration.



#### Issues fixed

This release fixed the following issues:

- Playing audio files with a sample rate of 48 kHz failed.
- When there were multiple video streams in a channel, calling some video enhancement APIs occasionally failed.
- At the moment when a user left a channel, a request for leaving was not sent to the server and the leaving behavior was incorrectly determined by the server as timed out.



#### API changes

**Added**

- `enableInstantMediaRendering`
- `startMediaRenderingTracing`
- `startMediaRenderingTracingEx`
- `videoRenderingTracingResultOfUid`
- `AgoraMediaRenderTraceEvent`
- `VideoRenderingTracingInfo`

**Modified**
- `setRemoteVideo` to `setRemoteVideoSubscriptionOptions`
- `setRemoteVideoEx` to `setRemoteVideoSubscriptionOptionsEx`

**Deleted**

- `enableRemoteSuperResolution`
- Deleted `superResolutionType` in `AgoraRtcRemoteVideoStats`

## v4.1.0

v4.1.0 was released on November xx, 2022.

#### New features

#### **1. Multiple cameras for video capture**

This release supports multi-camera video capture. You can call `enableMultiCamera` to enable multi-camera capture mode, call `startSecondaryCameraCapture` to start capturing video from the second camera, and then publish the captured video to the second channel.

To stop using multi-camera capture, you need to call `stopSecondaryCameraCapture` to stop the second camera capture, then call `enableMultiCamera` and set `enabled` to `NO`.

**2. Headphone equalization effect**

This release adds the `setHeadphoneEQParameters` method, which is used to adjust the low- and high-frequency parameters of the headphone EQ. This is mainly useful in spatial audio scenarios. If you cannot achieve the expected headphone EQ effect after calling `setHeadphoneEQPreset`, you can call setHeadphoneEQParameters to adjust the EQ.

**3. Encoded video frame observer**

This release adds the `setRemoteVideo` method in the `AgoraRtcEngineKit` and `AgoraRtcEngineKitEx` classes. When you call the `setEncodedVideoFrameDelegate` method to register a video frame observer for the encoded video frames, the SDK subscribes to the encoded video frames by default. If you want to change the subscription options, you can call these new methods to set them.

For more information about registering video observers and subscription options, see the API reference.

**4. MPUDP (MultiPath UDP) (Beta)**

As of this release, the SDK supports MPUDP protocol, which enables you to connect and use multiple paths to maximize the use of channel resources based on the UDP protocol. You can use different physical NICs on both mobile and desktop and aggregate them to effectively combat network jitter and improve transmission quality.

> To enable this feature, contact [sales-us@agora.io](https://docs.agora.io/cn/video-call-4.x/sales-us@agora.io).

**5. Camera capture options**

This release adds the `followEncodeDimensionRatio` member in `AgoraCameraCapturerConfiguration`, which enables you to set whether to follow the video aspect ratio already set in `setVideoEncoderConfiguration` when capturing video with the camera.

**6. Multi-channel management**

This release adds a series of multi-channel related methods that you can call to manage audio and video streams in multi-channel scenarios.

- The `muteLocalAudioStreamEx` and `muteLocalVideoStreamEx` methods are used to cancel or resume publishing a local audio or video stream, respectively.
- The `muteAllRemoteAudioStreamsEx` and `muteAllRemoteVideoStreamsEx` are used to cancel or resume the subscription of all remote users to audio or video streams, respectively.
- The `startRtmpStreamWithoutTranscodingEx`, `startRtmpStreamWithTranscodingEx`, `updateRtmpTranscodingEx`, and `stopRtmpStreamEx` methods are used to implement Media Push in multi-channel scenarios.
- The `startChannelMediaRelayEx`, `updateChannelMediaRelayEx`, `pauseAllChannelMediaRelayEx`, `resumeAllChannelMediaRelayEx`, and `stopChannelMediaRelayEx` methods are used to relay media streams across channels in multi-channel scenarios.
- Adds the `leaveChannelEx` [2/2] method. Compared with the `leaveChannelEx` [1/2] method, a new `options` parameter is added, which is used to choose whether to stop recording with the microphone when leaving a channel in a multi-channel scenario.

**7. Video encoding preferences**

In general scenarios, the default video encoding configuration meets most requirements. For certain specific scenarios, this release adds the `advancedVideoOptions` member in `VideoEncoderConfiguration` for advanced settings of video encoding properties:

- `compressionPreference`: The compression preferences for video encoding, which is used to select low-latency or high-quality video preferences.
- `encodingPreference`: The video encoder preference, which is used to select adaptive preference, software encoder preference, or hardware encoder video preferences.

**8. Client role switching**

In order to enable users to know whether the switched user role is low-latency or ultra-low-latency, this release adds the `newRoleOptions` parameter to the `didClientRoleChanged` callback. The value of this parameter is as follows:

- `AgoraAudienceLatencyLevelLowLatency` (1): Low latency.
- `AgoraAudienceLatencyLevelUltraLowLatency` (2): Ultra-low latency.

#### Improvements

**1. Relaying media streams across channels**

This release optimizes the `updateChannelMediaRelay` method as follows:

- Before v4.1.0: If the target channel update fails due to internal reasons in the server, the SDK returns the error code `AgoraChannelMediaRelayEventUpdateDestinationChannelRefused`(8), and you need to call the `updateChannelMediaRelay` method again.
- v4.1.0 and later: If the target channel update fails due to internal server reasons, the SDK retries the update until the target channel update is successful.

**2. Reconstructed AIAEC algorithm**

This release reconstructs the AEC algorithm based on the AI method. Compared with the traditional AEC algorithm, the new algorithm can preserve the complete, clear, and smooth near-end vocals under poor echo-to-signal conditions, significantly improving the system's echo cancellation and dual-talk performance. This gives users a more comfortable call and live-broadcast experience. AIAEC is suitable for conference calls, chats, karaoke, and other scenarios.

**3. Virtual background**

This release optimizes the virtual background algorithm. Improvements include the following:

- The boundaries of virtual backgrounds are handled in a more nuanced way and image matting is is now extremely thin.
- The stability of the virtual background is improved whether the portrait is still or moving, effectively eliminating the problem of background flickering and exceeding the range of the picture.
- More application scenarios are now supported, and a user obtains a good virtual background effect day or night, indoors or out.
- A larger variety of postures are now recognized, when half the body is motionless, the body is shaking, the hands are swinging, or there is fine finger movement. This helps to achieve a good virtual background effect in conjunction with many different gestures.

#### **Other improvements**

This release includes the following additional improvements:

- Reduces the latency when pushing external audio sources.
- Improves the performance of echo cancellation when using the `AgoraAudioScenarioMeeting` scenario.
- Improves the smoothness of SDK video rendering.
- Enhances the ability to identify different network protocol stacks and improves the SDK's access capabilities in multiple-operator network scenarios.

#### **Issues fixed**

This release fixed the following issues:

- Calling `startAudioMixing` to play music files in the `ipod-library://item` path failed.
- Audience members heard buzzing noises when the host switched between speakers and earphones during live streaming.
- Different timestamps for audio and video data were obtained simultaneously and separately via `onRecordAudioFrame` and `onCaptureVideoFrame` callbacks.
- The call `getExtensionProperty` failed and returned an empty string.
- When entering a live streaming room that has been played for a long time as an audience, the time for the first frame to be rendered was shortened.

#### **API changes**

**Added**

- `enableMultiCamera`
- `startSecondaryCameraCapture`
- `stopSecondaryCameraCapture`
- `setHeadphoneEQParameters`
- `setRemoteVideo`
- `AgoraVideoSubscriptionOptions`
- `muteLocalAudioStreamEx`
- `muteLocalVideoStreamEx`
- `muteAllRemoteAudioStreamsEx`
- `muteAllRemoteVideoStreamsEx`
- `startRtmpStreamWithoutTranscodingEx`
- `startRtmpStreamWithTranscodingEx`
- `updateRtmpTranscodingEx`
- `stopRtmpStreamEx`
- `startChannelMediaRelayEx`
- `updateChannelMediaRelayEx`
- `pauseAllChannelMediaRelayEx`
- `resumeAllChannelMediaRelayEx`
- `stopChannelMediaRelayEx`
- `followEncodeDimensionRatio` in `AgoraCameraCapturerConfiguration`
- `hwEncoderAccelerating` in `AgoraRtcLocalVideoStats`
- `leaveChannelEx` [2/2]
- `advancedVideoOptions` in `AgoraVideoEncoderConfiguration`
- `newRoleOptions` in `didClientRoleChanged`
- `adjustUserPlaybackSignalVolumeEx`
- `enableAudioVolumeIndicationEx`

**Deprecated**

- `didApiCallExecute`. Use the callbacks triggered by specific methods instead.

**Deleted**

- Removes deprecated member parameters `backgroundImage` and `watermark` in `AgoraLiveTranscoding` class.
- Removes `AgoraChannelMediaRelayEventUpdateDestinationChannelRefused`(8) in `didReceiveChannelMediaRelayEvent`.


## v4.0.0

v4.0.0 was released on September 15, 2022.

#### New features


**2. Full HD and Ultra HD resolution**

In order to improve the interactive video experience, the SDK optimizes the whole process of video capturing, encoding, decoding and rendering. Starting from this version, it supports Full HD (FHD) and Ultra HD (UHD) video resolutions. You can set the `dimensions` parameter to 1920 × 1080 or higher resolution when calling the `setVideoEncoderConfiguration` method. If your device does not support high resolutions, the SDK will automatically fall back to an appropriate resolution.

<div class="alert info"><li>The UHD resolution (4K, 60 fps) is currently in beta and requires certain device performance and network bandwidth. If you want to experience this feature, contact <a href="mailto:support@agora.io">technical support</a>.
<li>High resolution typically means higher performance consumption. To avoid a decrease in experience due to insufficient device performance, Agora recommends that you enable FHD and UHD video resolutions on devices with better performance.
<li>The increase in the default resolution affects the aggregate resolution and thus the billing rate. See <a href="./billing_rtc_ng">Pricing</a>.</div>
