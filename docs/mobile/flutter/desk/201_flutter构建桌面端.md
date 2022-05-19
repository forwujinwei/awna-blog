# flutter桌面端

flutter默认是关闭桌面端的，可通过下列命令开启桌面端支持

```
flutter config --enable-linux-desktop
flutter config --enable-macos-desktop
flutter config --enable-windows-desktop
```
上面的命令分别对应的Linux，maxos，windows的开关，下面我们分别讲一下各个平台需要的环境，注意目前的桌面版应用开发只支持在宿主环境编译对应的版本，也就说Linux下只能编译出Linux的版本，Mac上只能编译出Mac的版本，Windows同样

## Linux

Linux开发环境除了标准的flutter环境之外还需要cmake和clang++这个包，安装这两个包也比较容易(博主用的ubantu)


```
sudo apt install cmake
sudo apt install clang
```

## Windows

Windows开发环境除了标准的flutter环境之外还需要Visual Studio对，没有错需要安装这个ide，并且还要安装其中的"Desktop development with C++"的workload,，这个如果没有安装的话需要安装，6个多G有点蛋疼。

## Macos

目前没有发现什么需要特殊的配置，能开发IOS理论上就可以开发macos，有一个xcode就可以了

## 创建项目

和创建移动端项目一样

```
flutter create appName
```

## 运行

首先先用

```
flutter devices
```
确认我们的设备是可被识别的，然后使用


```
flutter run
```
可以直接运行在我们的开发机上，效果如下

## 编译

```
flutter build linux
flutter build macos
flutter build windows
```

可编译出对应的版本，存放在build/对应平台/release目录下，注意只能编译和开发机同平台的版本.

- Linux平台编译出的为一个可执行文件
- Windows平台编译出的为.exe文件
- Mac平台编译出的为.pgk文件