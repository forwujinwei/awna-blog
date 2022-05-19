# 调用原生功能
## Camera
某些应用程序可能需要使用移动设备进行拍照或者选择相册中的照片，Flutter官方提供了插件：image_picker

### 添加依赖
添加对image_picker的依赖：
- https://pub.dev/packages/image_picker

```
dependencies:
  image_picker: ^0.6.5
```
### 平台配置
对iOS平台，想要访问相册或者相机，需要获取用户的允许：

- 依然是修改info.plist文件：/ios/Runner/Info.plist
- 添加对相册的访问权限：Privacy - Photo Library Usage Description
- 添加对相机的访问权限：Privacy - Camera Usage Description
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8slqamy7j30gx027dfs.jpg)
之后选择相册或者访问相机时，会弹出如下的提示框：
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8sm9vvvhj306v04amwz.jpg)

### 代码实现
image_picker的核心代码是pickImage方法：

- 可以传入数据源、图片的大小、质量、前置后置摄像头等
- 数据源是必传参数：ImageSource枚举类型
  + camera：相机
  + gallery：相册

```
static Future<File> pickImage(
      {@required ImageSource source,
      double maxWidth,
      double maxHeight,
      int imageQuality,
      CameraDevice preferredCameraDevice = CameraDevice.rear}) async
```

案例演练

```
import 'package:flutter/material.dart';
import 'dart:io';
import 'package:image_picker/image_picker.dart';

class HYCameraScreen extends StatefulWidget {
  static const String routeName = "/camera";

  @override
  _HYCameraScreenState createState() => _HYCameraScreenState();
}

class _HYCameraScreenState extends State<HYCameraScreen> {
  File _image;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Camera"),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            _image == null ? Text("未选择图片"): Image.file(_image),
            RaisedButton(
              child: Text("选择照片"),
              onPressed: _pickImage,
            )
          ],
        ),
      ),
    );
  }

  void _pickImage() async {
    File image = await ImagePicker.pickImage(source: ImageSource.gallery);
    setState(() {
      _image = image;
    });
  }
}
```
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8snhnmmfj30u00gydgt.jpg)

## 电池信息
某些原生的信息，如果没有很好的插件，我们可以通过platform channels（平台通道）来获取信息。

### 平台通过介绍
平台通过是如何工作的呢？

- 消息使用platform channels（平台通道）在客户端（UI）和宿主（平台）之间传递；
- 消息和响应以异步的形式进行传递，以确保用户界面能够保持响应；
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8sodb4shj30g40hzaar.jpg)
调用过程大致如下：

- 1.客户端（Flutter端）发送与方法调用相对应的消息
- 2.平台端（iOS、Android端）接收方法，并返回结果；
  + iOS端通过FlutterMethodChannel做出响应；
  + Android端通过MethodChannel做出响应；

Flutter、iOS、Android端数据类型的对应关系：
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8sp4pn1nj30pt0dlab7.jpg)

### 创建测试项目
我们这里创建一个获取电池电量信息的项目，分别通过iOS和Android原生代码来获取对应的信息：

创建方式一：默认创建方式
- 目前默认创建的Flutter项目，对应iOS的编程语言是Swift，对应Android的编程语言是kotlin

```
flutter create batterylevel
```
创建方式二：指定编程语言
- 如果我们希望指定编程语言，比如iOS编程语言为Objective-C，Android的编程语言为Java

```
flutter create -i objc -a java batterylevel2
```
### 编写Dart代码
在Dart代码中，我们需要创建一个MethodChannel对象：

- 创建该对象时，需要传入一个name，该name是区分多个通信的名称
- 可以通过调用该对象的invokeMethod来给对应的平台发送消息进行通信
  + 该调用是异步操作，需要通过await获取then回调来获取结果


```
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() => runApp(MyApp());


class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
          primarySwatch: Colors.blue, splashColor: Colors.transparent),
      home: HYBatteryScreen(),
    );
  }
}

class HYBatteryScreen extends StatefulWidget {
  static const String routeName = "/battery";

  @override
  _HYBatteryScreenState createState() => _HYBatteryScreenState();
}

class _HYBatteryScreenState extends State<HYBatteryScreen> {
  // 核心代码一：
  static const platform = const MethodChannel("coderwhy.com/battery");
  int _result = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Battery"),
      ),
      body: Center(
        child: Column(
          children: <Widget>[
            Text("当前电池信息: $_result"),
            RaisedButton(
              child: Text("获取电池信息"),
              onPressed: getBatteryInfo,
            )
          ],
        ),
      ),
    );
  }

  void getBatteryInfo() async {
    // 核心代码二
    final int result = await platform.invokeMethod("getBatteryInfo");
    setState(() {
      _result = result;
    });
  }
}
```
当我们通过 platform.invokeMethod 调用对应平台方法时，需要在对应的平台实现其操作：

- iOS中可以通过Objective-C或Swift来实现
- Android中可以通过Java或者Kotlin来实现

### 编写iOS代码
#### Swift代码实现
代码相关的操作步骤如下：

- 1.获取FlutterViewController(是应用程序的默认Controller)
- 2.获取MethodChannel(方法通道)
注意：这里需要根据我们创建时的名称来获取
- 3.监听方法调用(会调用传入的回调函数)
  + iOS中获取信息的方式
  + 如果没有获取到,那么返回给Flutter端一个异常
  + 通过result将结果回调给Flutter端
  + 3.1.判断是否是getBatteryInfo的调用,告知Flutter端没有实现对应的方法
  + 3.2.如果调用的是getBatteryInfo的方法, 那么通过封装的另外一个方法实现回调


```
import UIKit
import Flutter

@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
    
    // 1.获取FlutterViewController(是应用程序的默认Controller)
    let controller : FlutterViewController = window?.rootViewController as! FlutterViewController
    
    // 2.获取MethodChannel(方法通道)
    let batteryChannel = FlutterMethodChannel(name: "coderwhy.com/battery",
                                              binaryMessenger: controller.binaryMessenger)
    
    // 3.监听方法调用(会调用传入的回调函数)
    batteryChannel.setMethodCallHandler({
      [weak self] (call: FlutterMethodCall, result: FlutterResult) -> Void in
      // 3.1.判断是否是getBatteryInfo的调用,告知Flutter端没有实现对应的方法
      guard call.method == "getBatteryInfo" else {
        result(FlutterMethodNotImplemented)
        return
      }
      // 3.2.如果调用的是getBatteryInfo的方法, 那么通过封装的另外一个方法实现回调
      self?.receiveBatteryLevel(result: result)
    })
    
    GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
    
  private func receiveBatteryLevel(result: FlutterResult) {
    // 1.iOS中获取信息的方式
    let device = UIDevice.current
    device.isBatteryMonitoringEnabled = true
    
    // 2.如果没有获取到,那么返回给Flutter端一个异常
    if device.batteryState == UIDevice.BatteryState.unknown {
      result(FlutterError(code: "UNAVAILABLE",
                          message: "Battery info unavailable",
                          details: nil))
    } else {
      // 3.通过result将结果回调给Flutter端
      result(Int(device.batteryLevel * 100))
    }
  }
}
```
#### Objective-C代码实现
实现思路和上面是一致的，只是使用了Objective-C来实现：

- 可以参考注释内容

```
#import <Flutter/Flutter.h>
#import "AppDelegate.h"
#import "GeneratedPluginRegistrant.h"

@implementation AppDelegate
- (BOOL)application:(UIApplication*)application didFinishLaunchingWithOptions:(NSDictionary*)launchOptions {
  // 1.获取FlutterViewController(是应用程序的默认Controller)
  FlutterViewController* controller = (FlutterViewController*)self.window.rootViewController;

  // 2.获取MethodChannel(方法通道)
  FlutterMethodChannel* batteryChannel = [FlutterMethodChannel
                                          methodChannelWithName:@"coderwhy.com/battery"
                                          binaryMessenger:controller.binaryMessenger];
  
  // 3.监听方法调用(会调用传入的回调函数)
  __weak typeof(self) weakSelf = self;
  [batteryChannel setMethodCallHandler:^(FlutterMethodCall* call, FlutterResult result) {
    // 3.1.判断是否是getBatteryInfo的调用
    if ([@"getBatteryInfo" isEqualToString:call.method]) {
      // 1.iOS中获取信息的方式
      int batteryLevel = [weakSelf getBatteryLevel];
      // 2.如果没有获取到,那么返回给Flutter端一个异常
      if (batteryLevel == -1) {
        result([FlutterError errorWithCode:@"UNAVAILABLE"
                                   message:@"Battery info unavailable"
                                   details:nil]);
      } else {
        // 3.通过result将结果回调给Flutter端
        result(@(batteryLevel));
      }
    } else {
      // 3.2.如果调用的是getBatteryInfo的方法, 那么通过封装的另外一个方法实现回调
      result(FlutterMethodNotImplemented);
    }
  }];

  [GeneratedPluginRegistrant registerWithRegistry:self];
  return [super application:application didFinishLaunchingWithOptions:launchOptions];
}

- (int)getBatteryLevel {
  // 获取信息的方法
  UIDevice* device = UIDevice.currentDevice;
  device.batteryMonitoringEnabled = YES;
  if (device.batteryState == UIDeviceBatteryStateUnknown) {
    return -1;
  } else {
    return (int)(device.batteryLevel * 100);
  }
}

@end
```
### 编写Android代码
#### Kotlin代码实现
实现思路和上面是一致的，只是使用了Kotlin来实现：

- 可以参考注释内容

```
import androidx.annotation.NonNull
import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugin.common.MethodChannel

import android.content.Context
import android.content.ContextWrapper
import android.content.Intent
import android.content.IntentFilter
import android.os.BatteryManager
import android.os.Build.VERSION
import android.os.Build.VERSION_CODES

class MainActivity: FlutterActivity() {
    private val CHANNEL = "coderwhy.com/battery"

    override fun configureFlutterEngine(@NonNull flutterEngine: FlutterEngine) {
        // 1.创建MethodChannel对象
        val methodChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, CHANNEL) 
        
        // 2.添加调用方法的回调
        methodChannel.setMethodCallHandler {
            // Note: this method is invoked on the main thread.
            call, result ->
            // 2.1.如果调用的方法是getBatteryInfo,那么正常执行
            if (call.method == "getBatteryInfo") {
                // 2.1.1.调用另外一个自定义方法回去电量信息
                val batteryLevel = getBatteryLevel()
                
                // 2.1.2. 判断是否正常获取到
                if (batteryLevel != -1) {
                    // 获取到返回结果
                    result.success(batteryLevel)
                } else {
                    // 获取不到抛出异常
                    result.error("UNAVAILABLE", "Battery level not available.", null)
                }
            } else {
                // 2.2.如果调用的方法是getBatteryInfo,那么正常执行
                result.notImplemented()
            }
        }
    }

    private fun getBatteryLevel(): Int {
        val batteryLevel: Int
        if (VERSION.SDK_INT >= VERSION_CODES.LOLLIPOP) {
            val batteryManager = getSystemService(Context.BATTERY_SERVICE) as BatteryManager
            batteryLevel = batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY)
        } else {
            val intent = ContextWrapper(applicationContext).registerReceiver(null, IntentFilter(Intent.ACTION_BATTERY_CHANGED))
            batteryLevel = intent!!.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) * 100 / intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1)
        }

        return batteryLevel
    }
}
```
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8su893eyj30u00gydgv.jpg)

#### Java代码实现
实现思路和上面是一致的，只是使用了Java来实现：

- 可以参考注释内容

```
package com.example.batterylevel2;

import androidx.annotation.NonNull;
import io.flutter.embedding.android.FlutterActivity;
import io.flutter.embedding.engine.FlutterEngine;
import io.flutter.plugins.GeneratedPluginRegistrant;
import io.flutter.plugin.common.MethodChannel;
import android.content.ContextWrapper;
import android.content.Intent;
import android.content.IntentFilter;
import android.os.BatteryManager;
import android.os.Build.VERSION;
import android.os.Build.VERSION_CODES;
import android.os.Bundle;

public class MainActivity extends FlutterActivity {
  private static final String CHANNEL = "coderwhy.com/battery";

  @Override
  public void configureFlutterEngine(@NonNull FlutterEngine flutterEngine) {
    // 1.创建MethodChannel对象
    MethodChannel methodChannel = new MethodChannel(flutterEngine.getDartExecutor().getBinaryMessenger(), CHANNEL);

    // 2.添加调用方法的回调
    methodChannel.setMethodCallHandler(
        (call, result) -> {
          // 2.1.如果调用的方法是getBatteryInfo,那么正常执行
          if (call.method.equals("getBatteryInfo")) {

            // 2.1.1.调用另外一个自定义方法回去电量信息
            int batteryLevel = getBatteryLevel();

            // 2.1.2. 判断是否正常获取到
            if (batteryLevel != -1) {
              // 获取到返回结果
              result.success(batteryLevel);
            } else {
              // 获取不到抛出异常
              result.error("UNAVAILABLE", "Battery level not available.", null);
            }
          } else {
            // 2.2.如果调用的方法是getBatteryInfo,那么正常执行
            result.notImplemented();
          }
        }
      );
  }

  private int getBatteryLevel() {
    int batteryLevel = -1;
    if (VERSION.SDK_INT >= VERSION_CODES.LOLLIPOP) {
      BatteryManager batteryManager = (BatteryManager) getSystemService(BATTERY_SERVICE);
      batteryLevel = batteryManager.getIntProperty(BatteryManager.BATTERY_PROPERTY_CAPACITY);
    } else {
      Intent intent = new ContextWrapper(getApplicationContext()).
              registerReceiver(null, new IntentFilter(Intent.ACTION_BATTERY_CHANGED));
      batteryLevel = (intent.getIntExtra(BatteryManager.EXTRA_LEVEL, -1) * 100) /
              intent.getIntExtra(BatteryManager.EXTRA_SCALE, -1);
    }

    return batteryLevel;
  }
}
```