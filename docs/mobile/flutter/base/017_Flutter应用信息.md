> 真正开发一个完成的跨平台App需要针对不同的平台设置不同的应用信息  
比如应用标识、应用名称、应用图标、应用启动图等等

# 应用标识
##  Android应用标识
Android应用标识在对应的Android目录下：Android/app/build.gradle
- applicationId：是打包时的应用标识

```
defaultConfig {
    // TODO: Specify your own unique Application ID (https://developer.android.com/studio/build/application-id.html).
    applicationId "com.coderwhy.catefavor"
    minSdkVersion 16
    targetSdkVersion 28
    versionCode flutterVersionCode.toInteger()
    versionName flutterVersionName
    testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
}
```
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8scmz3yaj30u007n0t2.jpg)

## iOS应用标识
使用xcode导入flutter项目下的ios目录

iOS应用标识在对应的iOS目录下：ios/Runner/Info.plist（可以通过Xcode打开来进行修改）
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8sdkrg5cj30u0083t8x.jpg)

# 应用名称
## Android应用名称
Android应用名称在对应的Android目录下：android/app/src/main/AndroidMainifest.xml
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8se5mfl9j30m4097t97.jpg)

## iOS应用名称
iOS应用名称在对应的iOS目录下：ios/Runner/Info.plist（可以通过Xcode打开来进行修改）
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8semepktj30p80bjjsa.jpg)

#  应用图标
## Android应用图标
官方建议将图标（icon）根据不同的dpi放置在res/mipmap文件夹下。
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8sf9octfj307109eq3a.jpg)
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8sfh8v2aj30k8095mx8.jpg)

## iOS应用图标
iOS的应用图标在ios/Runner/Assets.xcassets/AppIcon.appiconset中管理（可以直接打开Xcode将对应的图标拖入）
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8sfvrk7aj30u00h0aas.jpg)

# 应用启动图
##  Android应用启动图
Android中默认的启动图是一片空白的，这是Flutter的默认设置效果。
- 在哪里设置呢？android/app/src/main/res/drawable/launch_background.xml

```
<?xml version="1.0" encoding="utf-8"?>
<!-- Modify this file to customize your launch splash screen -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@android:color/white" />

    <!-- You can insert your own image assets here -->
    <!--<item>
        <bitmap
            android:gravity="center"
            android:src="@mipmap/launcher_image"/>
    </item>-->
</layer-list>
```
我们可以进行如下修改：
第一步：将对应的启动图片，添加到对应的minimap文件夹中
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8sgq772ej30m902v746.jpg)

第二步：修改launch_background.xml文件如下：
- 注意：我这里启动图命名为launcher_image，需要修改为你的名称


```
<?xml version="1.0" encoding="utf-8"?>
<!-- Modify this file to customize your launch splash screen -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
<!--    <item android:drawable="@android:color/white" />-->

    <!-- You can insert your own image assets here -->
    <item>
        <bitmap
            android:gravity="center"
            android:src="@mipmap/launcher_image"/>
    </item>
</layer-list>
```
## iOS应用启动图
**iOS需要两步来完成**
第一步：将启动图片添加到资源依赖中
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8shp1g3aj30u00h0jro.jpg)

第二步：在LaunchScreen.storyboard中，添加一个ImageView，并且添加约束
![undefined](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8shyortyj30u00h0wfc.jpg)