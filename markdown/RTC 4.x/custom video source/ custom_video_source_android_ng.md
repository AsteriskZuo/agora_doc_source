# 自定义视频采集 (Android)

自定义视频采集是指通过自定义的视频采集源实现对视频流的采集。

与 SDK 默认的视频采集方式不同，自定义视频采集支持用户自行控制采集源，实现更加精细的视频属性调整。例如，支持通过高清摄像头、无人机摄像头或其他类型的摄像头实现视频采集，同时支持动态调整视频质量、分辨率和帧率等参数，以适应不同的应用场景和需求。

声网推荐优先使用更为稳定、可靠、集成维护难度低的 SDK 视频采集，如果你有特定的视频采集需求或无法使用 SDK 采集，那么自定义视频采集为你提供灵活、可定制的方案。


## 适用场景

你可以在多种行业的多种场景下使用到自定义视频采集：

### 视频特殊处理和增强

在某些游戏或虚拟现实应用中，需要对视频流进行实时的特效处理、滤镜处理或其他增强效果。在这种情况下，使用自定义视频采集可以直接获取原始视频流，并进行实时处理，从而实现更加逼真的游戏或虚拟现实效果。

### 高精度视频采集

在视频监控领域，需要对场景中的细节进行精细的观察和分析，此时使用自定义视频采集可以获得更高的图像质量和更精细的采集控制。

### 特定视频源采集

在 IoT、直播等行业需要使用特定的摄像头、监控设备或其他非摄像头设备视频源，例如视频捕捉卡、录屏数据等。在这种情况下，使用 SDK 内部采集可能无法满足需求，必须使用自定义视频采集来实现对特定视频源的采集。

### 与特定设备或第三方应用无缝对接

在智能家居或物联网领域，需要将设备中的视频传输到用户的手机或电脑上进行监控和控制，此时可能需要使用特定的设备或应用程序进行视频采集。在这种情况下，使用自定义视频采集可以方便地将特定设备或应用程序与 RTC SDK 进行对接。

### 特定的视频编码格式

在特定直播场景中，可能需要使用特定的视频编码格式来满足业务需求，此时使用 SDK 内部采集可能无法满足需求，必须使用自定义视频采集来实现对特定编码格式视频的采集和自定义编码。


## 优势介绍

使用自定义视频采集，你可以体验到：

### 更多类型的视频流

自定义视频采集功能可以使用更高质量、更多类型的采集设备和摄像头，从而获得更清晰、更流畅的视频流。这有助于提高用户的观看体验，并使产品更具竞争力。

### 更灵活的视频特效

自定义视频采集功能可以帮助用户实现更丰富、更个性化的视频特效和过滤器，从而提高用户的体验和应用程序的吸引力。用户可以通过自定义视频采集功能实现各种特效，如美颜、滤镜、动态贴纸等。

### 更适应各种场景的需求

自定义视频采集功能可以帮助应用程序更好地适应各种场景的需求，如直播、视频会议、在线教育等。用户可以根据不同的场景需求，定制不同的视频采集方案，从而提供适应性更强的应用程序。


## 技术原理

声网 SDK 提供自定义视频轨道方式实现视频自采集。你可以创建一个或多个自定义视频轨道，加入频道并在每个频道中发布已创建的视频轨道。你需要使用自采集模块驱动采集设备对视频进行采集，并将采集的视频帧通过视频轨道发送给 SDK。

下图展示在单频道和多频道中实现自定义视频采集时，视频数据的传输过程：

### 单频道

仅在一个频道内发布自采集视频流：

![](https://web-cdn.agora.io/docs-files/1683598621022)

### 多频道

在多个频道内发布不同的自采集视频流：

![](https://web-cdn.agora.io/docs-files/1683598671853)


## 前提条件

在进行操作之前，请确保你已经在项目中实现了基本的实时音视频功能，详见[实现视频通话](https://docs.agora.io/cn/video-call-4.x/start_call_android_ng?platform=Android)或[实现视频直播](https://docs.agora.io/cn/live-streaming-premium-4.x/start_live_android_ng?platform=Android)。


## 实现自定义视频采集

参考如下内容，在你的 app 中实现自定义视频采集功能。

### API 调用时序

参考下图调用时序，在你的 app 中实现自定义视频采集：

![](https://web-cdn.agora.io/docs-files/1683598705647)

### 实现步骤 

参考如下步骤，在你的 app 中实现自定义视频采集功能：

1. 初始化 `RtcEngine` 后，调用 `createCustomVideoTrack` 创建自定义视频轨道并获得视频轨道 ID。根据场景需要，你可以创建多个自定义视频轨道。

```java
// 如需创建多个自定义视频轨道，可以多次调用 createCustomVideoTrack
int videoTrackId = RtcEngine.createCustomVideoTrack();
```

2. 调用 `joinChannel` 加入频道，或调用 `joinChannelEx` 加入多频道， 在每个频道的 `ChannelMediaOptions` 中，将 `customVideoTrackId` 参数设置为步骤 1 中获得的视频轨道 ID，并将 `publishCustomVideoTrack` 设置为 `true`，`publishCameraTrack` 设置为 `false`，即可在多个频道中发布指定的自定义视频轨道。

```java
// 如需在多个频道发布自定义视频轨道，则需要多次设置 ChannelMediaOptions 并多次调用 joinChannelEx
ChannelMediaOptions option = new ChannelMediaOptions();
option.clientRoleType = Constants.CLIENT_ROLE_BROADCASTER;
option.autoSubscribeAudio = true;
option.autoSubscribeVideo = true;
// 取消发布摄像头流
option.publishCameraTrack = false;
// 发布自采集视频流
option.publishCustomVideoTrack = true;
option.customVideoTrackId = videoTrackId;
// 加入主频道
int res = RtcEngine.joinChannel(accessToken, option, new IRtcEngineEventHandler(){});
// 或加入多频道
int res = RtcEngine.joinChannelEx(accessToken, connection, option, new IRtcEngineEventHandler(){});
```

3. 实现视频采集。声网提供 [VideoFileReader.java](https://github.com/AgoraIO/API-Examples/blob/main/Android/APIExample/app/src/main/java/io/agora/api/example/utils/VideoFileReader.java) 演示从本地文件读取 YUV 格式的视频数据。在实际的生产环境中，声网 SDK 不提供自定义视频处理 API，你需要结合业务需求使用 Android SDK 为你的设备创建自定义视频模块。

4. 将采集到的视频帧发送至 SDK 之前，通过设置 `VideoFrame` 集成你的视频模块。你可以参考以下代码，将采集到的 YUV 视频数据转换为不同类型的 `VideoFrame`。为确保音视频同步，声网建议你调用 `getCurrentMonotonicTimeInMs` 获取 SDK 当前的 Monotonic Time 后，将该值传入采集的 `VideoFrame` 的时间戳参数。

   ```java
   // 创建不同类型的 VideoFrame
   VideoFrame.Buffer frameBuffer;
   // 将 YUV 视频数据转换为 NV21 格式
   if ("NV21".equals(selectedItem)) {
      int srcStrideY = width;
      int srcHeightY = height;
      int srcSizeY = srcStrideY * srcHeightY;
      ByteBuffer srcY = ByteBuffer.allocateDirect(srcSizeY);
      srcY.put(yuv, 0, srcSizeY);
   
      int srcStrideU = width / 2;
      int srcHeightU = height / 2;
      int srcSizeU = srcStrideU * srcHeightU;
      ByteBuffer srcU = ByteBuffer.allocateDirect(srcSizeU);
      srcU.put(yuv, srcSizeY, srcSizeU);
   
      int srcStrideV = width / 2;
      int srcHeightV = height / 2;
      int srcSizeV = srcStrideV * srcHeightV;
      ByteBuffer srcV = ByteBuffer.allocateDirect(srcSizeV);
      srcV.put(yuv, srcSizeY + srcSizeU, srcSizeV);
   
      int desSize = srcSizeY + srcSizeU + srcSizeV;
      ByteBuffer des = ByteBuffer.allocateDirect(desSize);
      YuvHelper.I420ToNV12(srcY, srcStrideY, srcV, srcStrideV, srcU, srcStrideU, des, width, height);
   
      byte[] nv21 = new byte[desSize];
      des.position(0);
      des.get(nv21);
   
      frameBuffer = new NV21Buffer(nv21, width, height, null);
   }
      // 将 YUV 视频数据转换为 NV12 格式
      else if ("NV12".equals(selectedItem)) {
         int srcStrideY = width;
         int srcHeightY = height;
         int srcSizeY = srcStrideY * srcHeightY;
         ByteBuffer srcY = ByteBuffer.allocateDirect(srcSizeY);
         srcY.put(yuv, 0, srcSizeY);
   
         int srcStrideU = width / 2;
         int srcHeightU = height / 2;
         int srcSizeU = srcStrideU * srcHeightU;
         ByteBuffer srcU = ByteBuffer.allocateDirect(srcSizeU);
         srcU.put(yuv, srcSizeY, srcSizeU);
   
         int srcStrideV = width / 2;
         int srcHeightV = height / 2;
         int srcSizeV = srcStrideV * srcHeightV;
         ByteBuffer srcV = ByteBuffer.allocateDirect(srcSizeV);
         srcV.put(yuv, srcSizeY + srcSizeU, srcSizeV);
   
         int desSize = srcSizeY + srcSizeU + srcSizeV;
         ByteBuffer des = ByteBuffer.allocateDirect(desSize);
         YuvHelper.I420ToNV12(srcY, srcStrideY, srcU, srcStrideU, srcV, srcStrideV, des, width, height);
   
         frameBuffer = new NV12Buffer(width, height, width, height, des, null);
      }
      // 将 YUV 视频数据转换为 Texture 格式
      else if ("Texture2D".equals(selectedItem)) {
         if (textureBufferHelper == null) {
               textureBufferHelper = TextureBufferHelper.create("PushExternalVideoYUV", EglBaseProvider.instance().getRootEglBase().getEglBaseContext());
         }
         if (yuvFboProgram == null) {
               textureBufferHelper.invoke((Callable<Void>) () -> {
                  yuvFboProgram = new YuvFboProgram();
                  return null;
         });
         }
         Integer textureId = textureBufferHelper.invoke(() -> yuvFboProgram.drawYuv(yuv, width, height));
         frameBuffer = textureBufferHelper.wrapTextureBuffer(width, height, VideoFrame.TextureBuffer.Type.RGB, textureId, new Matrix());
      }
      // 将 YUV 视频数据转换为 I420 格式
      else if("I420".equals(selectedItem))
      {
         JavaI420Buffer i420Buffer = JavaI420Buffer.allocate(width, height);
         i420Buffer.getDataY().put(yuv, 0, i420Buffer.getDataY().limit());
         i420Buffer.getDataU().put(yuv, i420Buffer.getDataY().limit(), i420Buffer.getDataU().limit());
         i420Buffer.getDataV().put(yuv, i420Buffer.getDataY().limit() + i420Buffer.getDataU().limit(), i420Buffer.getDataV().limit());
         frameBuffer = i420Buffer;
      }
   
   // 获取 SDK 当前的 Monotonic Time
   long currentMonotonicTimeInMs = engine.getCurrentMonotonicTimeInMs();
   // 创建 VideoFrame，并将 SDK 当前的 Monotonic Time 赋值到 VideoFrame 的时间戳参数
   VideoFrame videoFrame = new VideoFrame(frameBuffer, 0, currentMonotonicTimeInMs);
   
   // 通过视频轨道推送视频帧到 SDK
   int ret = engine.pushExternalVideoFrameEx(videoFrame, videoTrack);
   if (ret < 0) {
      Log.w(TAG, "pushExternalVideoFrameEx error code=" + ret);
   }
   ```

5. 调用 `pushExternalVideoFrameEx` 并将 `videoTrackId` 指定为步骤 2 中指定的视频轨道 ID，将视频帧通过视频轨道发送给 SDK。

```java
// 通过视频轨道推送视频帧到 SDK
int ret = engine.pushExternalVideoFrameEx(videoFrame, videoTrack);
if (ret < 0) {
    Log.w(TAG, "pushExternalVideoFrameEx error code=" + ret);
}
```

6. 如需停止自定义视频采集，调用 `destroyCustomVideoTrack` 来销毁视频轨道。如需销毁多个视频轨道，可多次调用 `destroyCustomVideoTrack`。

```java
engine.destroyCustomVideoTrack(videoTrack);
```


## 参考信息

本节介绍本文中使用方法的更多信息以及相关页面的链接。

### 注意事项

如果采集到的自定义视频格式为 Texture，并且远端用户在本地自定义采集视频中看到闪烁、失真等异常情况时，建议先复制视频数据，再将原始视频数据和复制的视频数据发回至 SDK，从而消除内部数据编码过程中出现的异常情况。

### 示例项目

声网在 GitHub 上提供了开源的示例项目 [MultiVideoSourceTracks](https://github.com/AgoraIO/API-Examples/blob/main/Android/APIExample/app/src/main/java/io/agora/api/example/examples/advanced/MultiVideoSourceTracks.java) 供你参考。

### API 参考

- [`createCustomVideoTrack`](https://docs.agora.io/cn/extension_customer/API%20Reference/java_ng/API/toc_video_process.html#api_irtcengine_createcustomvideotrack)
- [`destroyCustomVideoTrack`](https://docs.agora.io/cn/extension_customer/API%20Reference/java_ng/API/toc_video_process.html#api_irtcengine_destroycustomvideotrack)
- [`pushExternalVideoFrameEx` [2/2]](https://docs.agora.io/cn/extension_customer/API%20Reference/java_ng/API/toc_multi_channel.html#api_irtcengineex_pushvideoframeex2)