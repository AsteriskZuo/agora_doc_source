# Understand the tech

The following figure shows the workflow of a [feature] implemented by the Agora SDK.

![start-basic-workflow]

To start a [feature], you implement the following steps in your app:

<ol>
<li props="live">Set the client role</li>
<li>Retrieve a token</li>
<li>Join a channel</li>
<li>Publish and subscribe to <ph keyref="media-type"/> in the channel</li>
</ol>

For an app client to join a channel, you need the following information:

- The App ID: A randomly generated string provided by Agora for identifying your app. You can get the App ID from [Agora Console](https://console.agora.io/).
- The user ID: The unique identifier of a user. You need to specify the user ID yourself, and ensure that it is unique in the channel.
- A token: In a test or production environment, your app client retrieves tokens from your server. For rapid testing, you can use a temporary token with a validity period of 24 hours.
- The channel name: A string that identifies the channel for the video call. 

