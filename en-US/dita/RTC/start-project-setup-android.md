# Project setup

1. For new projects, in **Android Studio**, create a **Phone and Tablet** [Android project](https://developer.android.com/studio/projects/create-project) with an **Empty Activity**.

   After creating the project, **Android Studio** automatically starts gradle sync. Ensure that the sync succeeds before you continue.

2. Integrate the [sdk-name] into your project.

   a. In `/Gradle Scripts/build.gradle(Project: <projectname>)`, add the following lines to add the JitPack dependency.

   ```java
    allprojects {
        repositories {
            ...
            maven { url 'https://www.jitpack.io' }
        }
    }
   ```

   b. In `/Gradle Scripts/build.gradle(Module: <projectname>.app)`, add the following lines to integrate the Agora Video SDK into your Android project.

   ```java
    ...
    dependencies {
        ...
        // For x.y.z, fill in a specific SDK version number. For example, 3.4.0
        implementation 'com.github.agorabuilder:native-full-sdk:x.y.z'
    }
   ```

   This method applies to v3."start-see-also-android.md#other-approches-to-integrate-the-sdk"roaches to integrating the SDK](see-also-android.md#other-approches-to-integrate-the-sdk).

3. Add permissions for network and device access.

   In `/app/Manifests/AndroidManifest.xml`, add the following permissions after `</application>`:

   ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.BLUETOOTH" />
   ```

4. Prevent code obfuscation.

   In `/Gradle Scripts/proguard-rules.pro`, add the following line to prevent obfuscating the code of the SDK:

   ```
    -keep class io.agora.**{*;}
   ```
