### Introduction

Infobip RTC is a Flutter plugin package which enables you to take advantage of the Infobip platform, giving you the ability to enrich your mobile applications with real-time communications in minimum time, while you focus on your application's user experience and business logic. We currently support audio and video calls between two web or app users, and phone calls between a web or an app user and actual phone device.

Here you will find an overview, and a quick guide on how to connect to the Infobip platform. There is also an in-depth reference documentation available.

### First-time setup

In order to use Infobip RTC, you need to have Web and In-app Calls enabled on your account and that's it! You are ready to make Web and In-app calls. To learn how to enable them see [the documentation](https://www.infobip.com/docs/voice-and-video/web-and-in-app-calls#set-up-web-and-in-app-calls).

### Minimum requirements

This plugin package supports:

* iOS 10.0 and higher
* Android API level 21 (Lollipop) and higher

### Getting SDK

You can get our SDK by running the following command:

```bash
flutter pub add infobip_rtc
```

This will add a line like this to your package's pubspec.yaml (and run the `flutter pub get` command)

```yaml
dependencies:
  infobip_rtc: ^0.1.7
```

Now you can add the following line in your Dart files to use our plugin package:

```dart
import 'package:infobip_rtc/infobip_rtc.dart';
```

### Authentication

Since Infobip RTC is a plugin package, it means you develop your own application, and you only use Infobip RTC as a dependency. Your application has your own users, which we will call subscribers throughout this guide. So, in order to use Infobip RTC, you need to register your subscribers on our platform. The credentials your subscribers use to connect to your application are irrelevant to Infobip. We only need the identity they will use to present themselves. When we have the subscriber's identity, we can generate a token assigned to that specific subscriber. With that token, your subscribers can connect to our platform (using Infobip RTC plugin package).

To generate these tokens for your subscribers, you need to call our [`/webrtc/1/token`](https://dev.infobip.com/webrtc/generate-token) HTTP API method using proper parameters. There you authenticate yourself against the Infobip platform, so we can relate the subscriber's token to you. Typically, generating a token occurs after your subscribers are authenticated inside your application.
You will receive the token in a response that you will use to instantiate [`InfobipRTC`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTC) client in your application.

### Infobip RTC Client

After you have received a token via HTTP API, you can use the [`InfobipRTC`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTC) class to call various static methods in our plugin package.

### Making a call

You can call another subscriber if you know their identity. It is done via the [`call`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTC#call) method:

```dart
let callRequest = CallRequest(token, destination, callEventListener);
let outgoingCall = await InfobipRTC.call(callRequest);
```

Or if you want to initiate a video call:

```dart
let callRequest = CallRequest(token, destination, callEventListener);
let callOptions = CallOptions(video: true);
let outgoingCall = await InfobipRTC.call(callRequest, callOptions);
```

As you can see, the [`call`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTC#call) method returns a [`Future`](https://api.dart.dev/stable/2.15.1/dart-async/Future-class.html) which we need to handle with the await keyword. When the method returns a value, it will be an instance of [`OutgoingCall`](https://github.com/infobip/infobip-rtc-flutter/wiki/OutgoingCall).

You can set up event handlers, so you can do something when the called subscriber answers the call, rejects it, the call is ended, etc. You set up event handlers by implementing the [`CallEventListener`](https://github.com/infobip/infobip-rtc-flutter/wiki/CallEventListener) on the page where you want to handle them (most probably a page representing your active call).

When the call is hung up, you will receive a [`CallHangupEvent`](https://github.com/infobip/infobip-rtc-flutter/wiki/OutgoingCall) which you can handle in the [`onHangup`](https://github.com/infobip/infobip-rtc-flutter/wiki/CallEventListener#onHangup) method. The following example shows a toast notification with the text "onHangup".

```dart
@override
void onHangup(CallHangupEvent callHangupEvent) {
  Fluttertoast.showToast(
        msg: "onHangup",
        toastLength: Toast.LENGTH_SHORT,
        gravity: ToastGravity.CENTER,
        timeInSecForIosWeb: 1,
        backgroundColor: Colors.red,
        textColor: Colors.white,
        fontSize: 16.0
    );
}
```

During the call, you can also mute (and unmute) your audio using the following:

```dart
outgoingCall.mute(true);
```

Also, you can check the [`call status`](https://github.com/infobip/infobip-rtc-flutter/wiki/CallStatus):

```dart
CallStatus status = outgoingCall.status;
```

You can check information such as [`duration`](https://github.com/infobip/infobip-rtc-flutter/wiki/Call#duration), [`start time`](https://github.com/infobip/infobip-rtc-flutter/wiki/Call#startTime), [`establish time`](https://github.com/infobip/infobip-rtc-flutter/wiki/Call#establishTime) and [`end time`](https://github.com/infobip/infobip-rtc-flutter/wiki/Call#endTime) by accessing these attributes of the call:

```dart
let duration = outgoingCall.duration;
let startTime = outgoingCall.startTime;
let establishTime = outgoingCall.establishTime;
let endTime = outgoingCall.endTime;
```

### Calling a phone number

It is similar to calling a regular WebRTC user, you just use the callPhoneNumber method instead of call. As the second parameter, you send the phone number you are trying to call. The result of the [`callPhoneNumber`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTC#callPhoneNumber) is an instance of [`OutgoingCall`](https://github.com/infobip/infobip-rtc-flutter/wiki/OutgoingCall) with which you can do everything as when using the call method and the same events are implemented:

```dart
let callRequest = CallRequest(token, destination, callEventListener);
let outgoingCall = await InfobipRTC.callPhoneNumber(callRequest);
```

### Calling an agent on Conversations

When starting a call towards the Conversations platform, you don't need to specify an identity you want to call. This will be handled on the Conversations platform by the queues you have set up. The result of the [`callConversations`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTC#callConversations) method, which is used in this case, is also an instance of [`OutgoingCall`](https://github.com/infobip/infobip-rtc-flutter/wiki/OutgoingCall) with which you can do everything as when using the call method in previous cases and the same events are implemented:

```dart
let callConversationsRequest = CallConversationsRequest(token, callEventListener);
let outgoingCall = await InfobipRTC.callConversations(callConversationsRequest);
```

As with the first example of making a call, you can also send a [`CallOptions`](https://github.com/infobip/infobip-rtc-flutter/wiki/CallOptions) object as the second parameter to [`callConversations`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTC#callConversations). With this parameter you can set if you want to start the call with video enabled or with the audio and video recordings enabled. If your subscriber (end user) is on a mobile device, you can also set which camera they will be using during the video call, front or back.

```dart
let callConversationsRequest = CallConversationsRequest(token, callEventListener);
let callOptions = CallOptions(
                video: true,
                recordingOptions:
                    RecordingOptions(audio: true, video: true),
                videoOptions: VideoOptions(
                    cameraOrientation: CameraOrientation.front)))));
let outgoingCall = await InfobipRTC.callConversations(callConversationsRequest);
```

### How to setup the video view

To help you setup the video view we provide the [`InfobipRTCVideoView`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTCVideoView) stateful widget. Depending on if you have a local video stream or a remote video stream, you can initialize the [`InfobipRTCVideoView`](https://github.com/infobip/infobip-rtc-flutter/wiki/InfobipRTCVideoView) with the "local" or "remote" stream IDs, respectively:

```dart
@override
Widget build(BuildContext context) {
  SizeConfig().init(context);
  return Scaffold(
    ...
    body: GridView.builder(
      ...
      itemBuilder: (context, index) => index == 0
          ? (call.hasLocalVideo
              ? const InfobipRTCVideoView("local")
              : const ParticipantPlaceholder())
          : (call.hasRemoteVideo
              ? const InfobipRTCVideoView("remote")
              : const ParticipantPlaceholder()),
    ),
    ...
  );
}
```
