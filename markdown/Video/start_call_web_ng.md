---
title: 实现视频通话
platform: Web
updatedAt: 2020-12-30 09:08:12
---
Agora Video SDK 可以让你在自己的 app 里轻松实现实时音视频通话。你可以利用 Agora Video SDK 简便、快捷地在你的各类社交、工作、教育和物联网 app 中实现面对面互动。

本文介绍如何通过最简单的代码利用 Agora Video SDK， 在你的 Web 应用中实现基本的实时视频通话功能。

## 工作原理

下图展示 Agora Video SDK 实现视频通话的工作流程。

![video-call](https://web-cdn.agora.io/docs-files/1620472908634)

参考以下步骤在你的 app 客户端实现视频通话：

1. **获取 Token**

   你的 app 客户端会向你的服务端发送获取 Token 的请求，当用户在你的客户端加入频道时，使用该 Token 对用户进行鉴权。

2. **加入 Agora RTC 频道**

   你的客户端可以通过调用 Agora 提供的 API 来加入 RTC 频道，这时，Agora 会自动创建一个 RTC 频道，传入相同频道名的客户端会加入同一频道。

3. **在频道中发送并接收音视频流**

   通过 `LocalTrack` 和 `RemoteTrack` 对象在频道内发布、接收音视频轨道。

加入频道前，你还需获取以下信息：

- App ID：Agora 针对你的 app 所随机生成的一串字符，作为你的项目的唯一标识。你可以在 Agora 控制台获取你的 App ID。
- 用户 ID：用户身份的独特标识。你需要自定义用户 ID，并确保该用户 ID 不会与频道内其他用户 ID 重复。
- Token：一种身份证书，当你的客户端加入 RTC 频道时，使用 Token 对用户身份进行校验。
- 频道名称：视频通话的频道名称。

## 前提条件

在操作前，你必须拥有：

- 一个有效的 Agora 账号
- 一个 Agora 项目及其 App ID 和临时 Token
- 一台满足以下要求的 Windows 或 macOS 计算机：
  - 可连接到互联网。如果你的网络环境部署了防火墙，请参考[应用企业防火墙限制](https://docs.agora.io/cn/Agora%20Platform/firewall?platform=Web)以正常使用  Agora 服务。
  - 使用 Agora Web SDK 所[支持的浏览器](https://docs.agora.io/en/Video/faq/browser_support)。Agora 强烈推荐使用[最新版本](https://www.google.com/chrome/)的 Google Chrome 浏览器。
  - 具备物理音视频采集设备
  - 计算机搭载 2.2 GHz Intel 第二代 i3/i5/i7 处理器或同等性能的其他处理器。

- [Node.js 及 npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)

## 创建一个 Web 项目

创建一个名为`agora_web_quickstart`的新目录。以一个最简单的 Web 客户端为例，在目录中为该客户端创建以下文件：

- `index.html`：与用户的视觉界面。
- `basicVideoCall.js`：与 `AgoraRTCClient` 的可编程界面，实现客户端逻辑。
- `package.json`：安装并管理你的项目的依赖项。你可以通过命令行进入 `agora_web_quickstart` 目录并运行 `npm init` 命令来创建 `package.json` 文件。

## 在客户端实现视频通话

本节展示利用 Agora Video SDK 在你的 Web 应用中增加视频通话功能的详细步骤。

### 1. 集成 SDK

通过 npm 将 Agora Web SDK 集成到你的项目中，如下文所示：

1. 在 `package.json` 文件中，在  `dependencies` 字段中添加  `agora-rtc-sdk-ng`  及其版本号： 

   ```json
   {
     "name": "agora_web_quickstart",
     "version": "1.0.0",
     "description": "",
     "main": "basicVideoCall.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1"
     },
     "dependencies": {
       "agora-rtc-sdk-ng": "latest",
     },
     "author": "",
     "license": "ISC"
   }
   ```

2. 复制以下代码到 `basicVideoCall.js` 文件中，以把  `AgoraRTC` 模块导入到你的项目中。

   ```javascript
   import AgoraRTC from "agora-rtc-sdk-ng"
   ```

### 2. 实现用户界面

将以下代码复制到 `index.html` 来实现用户界面：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Agora Video Web SDK Quickstart</title>
    <!-- 
      This line is used to refer to the bundle.js file packaged by webpack. A sample webpack configuration is shown in the later step of running your app.
    -->
    <script src="./dist/bundle.js"></script>
</head>
<body>
    <h2 class="left-align">Agora Video Web SDK Quickstart</h2>
        <div class="row">
            <div>
                <button type="button" id="join">JOIN</button>
                <button type="button" id="leave">LEAVE</button>
            </div>
        </div>
</body>
</html>
```

### 3. 实现客户端逻辑

通过以下步骤来实现客户端视频通话逻辑：

1. 调用  [`createClient`](./API%20Reference/web_ng/interfaces/iagorartc.html#createclient)  方法来创建 `AgoraRTCClient` 对象。
2. 调用  [`join`](./API%20Reference/web_ng/interfaces/iagorartcclient.html#join)  方法来加入一个 RTC 频道，你需要在该方法中传入 App ID 、用户 ID、Token、频道名称。
3. 调用  [`createMicrophoneAudioTrack`](./API%20Reference/web_ng/interfaces/iagorartc.html#createmicrophoneaudiotrack)  方法，从麦克风采集的音频中创建一个本地音频轨道，调用  [`createCameraVideoTrack`](./API%20Reference/web_ng/interfaces/iagorartc.html#createcameravideotrack)  方法，从摄像头采集的视频 中创建一个本地视频轨道。调用  `publish`  方法，在频道中发布本地音视频轨道。
4. 当一个远端用户加入频道并发布音视频轨道时：
   1. 监听 [`client.on("user-published")`](./API%20Reference/web_ng/interfaces/iagorartcclient.html#event_user_published)  事件。当SDK 触发该事件后，在这个事件回调函数的参数中你可以获取远端用户 `AgoraRTCRemoteUser`  对象  。
   2. 调用  [`subscribe`](./API%20Reference/web_ng/interfaces/iagorartcclient.html#subscribe)  方法来订阅远端用户 `AgoraRTCRemoteUser`  对象，获取远端用户的 远端音频轨道  `RemoteAudioTrack`  和远端视频轨道  `RemoteVideoTrack`  对象。
   3. 调用  [`play`](./API%20Reference/web_ng/interfaces/itrack.html#play)  方法来播放远端音视频轨道。

下图为基本的一对一视频通话的 API 调用时序图。

![](https://web-cdn.agora.io/docs-files/1617877136130)

将以下代码复制到  `basicVideoCall.js`  文件中，将  `appID`  和  `token`  替换为[你的Agora项目的App ID 和 Token](https://docs.agora.io/cn/Agora%20Platform/get_appid_token?platform=Web)。

```js
import AgoraRTC from "agora-rtc-sdk-ng"

let rtc = {
    localAudioTrack: null,
    localVideoTrack: null,
    client: null
};

let options = {
    // 填入项目 APP ID。
    appId: "Your App ID",
    // 设置频道名称。
    channel: "test",
    // 填入你的临时 Token。
    token: "Your temp token",
    // 设置用户 ID。
    uid: 123456
};

async function startBasicCall() {
    // 创建一个远端用户 AgoraRTCClient 对象。
    rtc.client = AgoraRTC.createClient({ mode: "rtc", codec: "vp8" });
    
    // 监听 "user-published" 事件，从该事件中你可以获取远端用户 AgoraRTCRemoteUser 对象。
    rtc.client.on("user-published", async (user, mediaType) => {
        // 当 SDK 触发 "user-published" 事件时，订阅远端用户。
        await rtc.client.subscribe(user, mediaType);
        console.log("subscribe success");

        // 远端用户是否发布视频轨道。
        if (mediaType === "video") {
            // 在远端用户 AgoraRTCRemoteUser 对象中获取远端视频轨道 RemoteVideoTrack 对象。
            const remoteVideoTrack = user.videoTrack;
            // 动态插入一个 DIV 节点作为播放远端视频轨道的容器。
            const remotePlayerContainer = document.createElement("div");
            // 给这个 DIV 节点指定一个 ID，你可以指定为远端用户的 ID。
            remotePlayerContainer.id = user.uid.toString();
            remotePlayerContainer.textContent = "Remote user " + user.uid.toString();
            remotePlayerContainer.style.width = "640px";
            remotePlayerContainer.style.height = "480px";
            document.body.append(remotePlayerContainer);

            // 播放远端视频音轨。
            // 传入 DIV 节点，让 SDK 在这个节点下创建相应的播放器播放远端视频。
            remoteVideoTrack.play(remotePlayerContainer);

            // 也可只传入该 DIV 节点的 ID。
            // remoteVideoTrack.play(playerContainer.id);
        }

        // 远端用户是否发布音频轨道
        if (mediaType === "audio") {
            // 在远端用户 AgoraRTCRemoteUser 对象中获取远端音频轨道 RemoteAudioTrack 对象。
            const remoteAudioTrack = user.audioTrack;
            // 播放远端音频轨道，不需要提供 DOM 元素的信息。
            remoteAudioTrack.play();
        }

        // 监听 "user-unpublished" 事件。
        rtc.client.on("user-unpublished", user => {
            // 获取刚刚动态创建的 DIV 节点。
            const remotePlayerContainer = document.getElementById(user.uid);
            // 销毁这个节点。
            remotePlayerContainer.remove();
        });

    });

    window.onload = function () {

        document.getElementById("join").onclick = async function () {
            // 加入一个 RTC 频道。
            await rtc.client.join(options.appId, options.channel, options.token, options.uid);
            // 从麦克风采集的音频中创建一个本地音频轨道。
            rtc.localAudioTrack = await AgoraRTC.createMicrophoneAudioTrack();
            // 从摄像头采集的视频中创建一个本地视频轨道。
            rtc.localVideoTrack = await AgoraRTC.createCameraVideoTrack();
            // 在频道中发布音视频轨道。
            await rtc.client.publish([rtc.localAudioTrack, rtc.localVideoTrack]);
            // 动态插入一个 DIV 节点作为播放本地视频轨道的容器。
            const localPlayerContainer = document.createElement("div");
            // 给这个 DIV 节点指定一个 ID，你可以指定为本地用户的 ID。
            localPlayerContainer.id = options.uid;
            localPlayerContainer.textContent = "Local user " + options.uid;
            localPlayerContainer.style.width = "640px";
            localPlayerContainer.style.height = "480px";
            document.body.append(localPlayerContainer);

            // 播放本地视频轨道。
            // 传入 DIV 节点，让 SDK 在这个节点下创建相应的播放器播放本地视频。
            rtc.localVideoTrack.play(localPlayerContainer);
            console.log("publish success!");
        }

        document.getElementById("leave").onclick = async function () {
            // 销毁本地音视频轨道。
            rtc.localAudioTrack.close();
            rtc.localVideoTrack.close();

            // 遍历远端用户。
            rtc.client.remoteUsers.forEach(user => {
                // 销毁动态创建的 DIV 节点。
                const playerContainer = document.getElementById(user.uid);
                playerContainer && playerContainer.remove();
            });

            // 离开频道。
            await rtc.client.leave();
        }
    }
}

startBasicCall()
```

### 测试你的 app

此快速开始中使用  [webpack](https://webpack.js.org/)  来包裹你的项目，使用  `webpack-dev-server`  来运行你的项目。

1. 在  `package.json`  文件中，将  `webpack-cli` 和    `webpack-dev-server`  添加到  `dependencies`  字段中，将  `build`  和  `start:dev`  命令添加到  `scripts`  字段中。

   ```json
   {
     "name": "agora_web_quickstart",
     "version": "1.0.0",
     "description": "",
     "main": "basicVideoCall.js",
     "scripts": {
       "test": "echo \"Error: no test specified\" && exit 1",
       "build": "webpack --config webpack.config.js",
       "start:dev": "webpack serve --open --config webpack.config.js"
     },
     "dependencies": {
       "agora-rtc-sdk-ng": "latest",
       "webpack": "5.28.0",
       "webpack-dev-server": "3.11.2",
       "webpack-cli": "4.5.0"
     },
     "author": "",
     "license": "ISC"
   }
   ```

2. 在项目目录中创建一个名为 `webpack.config.js` 的文件并用以下代码来配置 webpack ：

   ```javascript
   const path = require('path');
   
    module.exports = {
    entry: './basicVideoCall.js',
    output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, './dist'),
    },
    devServer: {
    compress: true,
    port: 9000
    }
    };
   ```

3. 运行下列命令来安装依赖项：

   ```shell
   npm install
   ```

4. 运行下列命令来利用 webpack 创建并运行项目：

   ```shell
   # Use webpack to package the project
   npm run build
   ```

5. 利用 webpack-dev-server 来运行项目：

   ```shell
   npm run start:dev
   ```

本地网络服务器自动在你的浏览器中打开，你会看见以下页面：

![](https://web-cdn.agora.io/docs-files/1619428543085)

点击 **JOIN** 开始视频通话，你还可以邀请朋友一起在[示例 app](https://webdemo.agora.io/agora-websdk-api-example-4.x/basicVideoCall/index.html) 上和你加入同一频道，进行视频通话。

- 在本地服务器（localhost）来运行 web 应用仅为测试用途。部署生产环境时，请确保使用 HTTPS 协议。
- 由于浏览器的安全策略对除 127.0.0.1 以外的 HTTP 地址作了限制，Agora Web SDK 仅支持 HTTPS 协议或者  `http://localhost`（`http://127.0.0.1`）。如果如果你通过HTTP部署你的项目，你只能在 `http://localhost`（`http://127.0.0.1`）访问你的项目。

### 更多步骤

在生产环境中，手动操作生成 Token 并不是最佳选择。[使用 Token 鉴权](https://docs.agora.io/cn/Video/token_server?platform=Web)介绍了如何利用从你的服务器获取的 Token 来开始视频通话。

### 相关参考

- Agora 还在 GitHub 上提供一个开源的[示例项目](https://github.com/AgoraIO/API-Examples-Web)供你参考。

- 除了通过 npm 来将 Agora Web SDK 集成到你的项目中，你还可以选择通过以下方式来集成 Agora Web SDK：

  - 使用CDN方法：在你的项目中的  `index.html`  文件中，在  `<style>`  标记前面一行添加以下代码。

    ```html
    <script src="https://download.agora.io/sdk/release/AgoraRTC_N-4.5.0.js"></script>
    ```

  - 手动下载最新的 [Agora Web SDK 4.x](https://docs.agora.io/cn/Video/downloads?platform=Web)，将其中的 `.js` 文件复制到你项目文件的目录中，在  `<style>`  标记前面一行添加以下代码。

    ```html
    <script src="./AgoraRTC_N-4.5.0.js"></script>
    ```

    <div class="alert info">如果你使用上述方法集成 SDK，SDK 都会在全局导出一个 <code>AgoraRTC</code> 对象，直接访问这个对象即可操作 SDK。</div>       

- 本文仅适用于 Agora Web SDK 4.x，如果你使用的是  Web SDK 3.x 或更早版本，请参考以下快速开始：

  - [跑通示例项目](https://docs.agora.io/cn/Video/run_demo_video_call_web?platform=Web)
  - [开始视频通话](https://docs.agora.io/cn/Video/start_call_web?platform=Web)