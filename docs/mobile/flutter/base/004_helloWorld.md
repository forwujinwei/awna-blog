# helloWorld

接下来我们创建自己的第一个Flutter应用程序，并且将其跑在两个模拟器上

## 创建Flutter应用
有两种方式创建Flutter应用：通过终端，通过编辑器。

这里我先选择通过终端（Windows和macOS都是一样的命令）

打开终端 - 执行如下命令

```
flutter create helloflutter
# 注意：后面的名称不能有特殊符号，也不能由大写
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1ghknzh7rysj30u00iq3zv.jpg)

## 项目跑在模拟器
通过一个你喜欢的开发工具打开（我这里暂时选择Android Studio）
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1ghko05mpx0j30u00gvwfl.jpg)
选择你要启动的设备，点击 运行 按钮
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1ghko0myya6j30u006y0sz.jpg)
我把项目运行在了两个模拟器上
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1ghko0ztnd0j30u00gvjsv.jpg)

## 体验Flutter热重载
对于我们开发测试阶段，如果每次修改代码都需要重新编译整个项目再加载的话，那每次可能都需要花费10秒左右甚至是几分钟的时间（电脑太慢的话），这样的开发效率是非常低的。

现在前端开发都支持热重载（Hot Reload），可以大大加快我们的开发效率。

- 热重载可以在无需重新编译代码、重新启动应用程序的情况下，看到修改后界面的效果

Flutter在开发阶段使用JIT编译模式（后面我会讲解什么是JIT模式），所以可以做到热重载来提高我们的开发效率

**下面我们体会一下热重载，后面有时间我们来分析热重载是如何实现的**

- 将下面红框中的内容改成Hello Flutter，保存一下应用程序
- 你会发现在不到1秒钟内，界面直接发生了刷新
- 并且没有应用程序没有进行任何的重新，效率非常高
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1ghko2ne4wkj30u00ifgmn.jpg)

如果热重载不起作用，我们也可以点击右上角的 Hot Restart，并不需要重新运行项目
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1ghko38bxeaj30u003w0sq.jpg)

## 工程目录分析
Flutter工程创建完毕会，会感觉比较复杂，我们来看下图：

![image-20220304094101948](https://gitee.com/Awna/pic/raw/master/image-20220304094101948.png)

目录包含哪些东西呢？

- 其中包含Flutter开发和测试需要的lib、test，在开发过程中，我们主要使用的就是lib目录
- 另外一些是管理项目的配置文件信息等，当然也包括一些Git相关的文件
- 除此之外，还包括一个Android子工程和iOS子工程

为什么包含Android子工程和iOS子工程呢？

- 这是因为作为一个跨平台的开发方案，最终还是要嵌入到Android工程或者iOS工程中来运行的
- 并且在开发过程中也需要调用原生的一些功能


## 代码分析
### runApp和Widget
runApp是Flutter内部提供的一个函数，当我们启动一个Flutter应用程序时就是从调用这个函数开始的

- 我们可以点到runApp的源码，查看到该函数
- 我们暂时不分析具体的源码（因为我发现过多的理论，对于初学者来说并不友好）

```
void runApp(Widget app) {
  ...省略代码
}
```
该函数让我们传入一个东西：Widget？

我们先说Widget的翻译：

- Widget在国内有很多的翻译；
- 做过Android、iOS等开发的人群，喜欢将它翻译成控件；
- 做过Vue、React等开发的人群，喜欢将它翻译成组件；
- 如果我们使用Google，Widget翻译过来应该是小部件；
- 没有说哪种翻译一定是对的，或者一定是错的，但是我个人更倾向于小部件或者组件；

Widget到底什么东西呢？

- 我们学习Flutter，从一开始就可以有一个基本的认识：Flutter中万物皆Widget（万物皆可盘）；
- 在我们iOS或者Android开发中，我们的界面有很多种类的划分：应用（Application）、视图控制器（View Controller）、活动（Activity）、View（视图）、Button（按钮）等等；
- 但是在Flutter中，这些东西都是不同的Widget而已；
- 也就是我们整个应用程序中所看到的内容几乎都是Widget，甚至是内边距的设置，我们也需要使用一个叫Padding的Widget来做；

runApp函数让我们传入的就是一个Widget：

- 但是我们现在没有Widget，怎么办呢？
- 我们可以导入Flutter默认已经给我们提供的Material库，来使用其中的很多内置Widget；

### Material设计风格
**material是什么呢？**

- material是Google公司推行的一套设计风格，或者叫设计语言、设计规范等；
- 里面有非常多的设计规范，比如颜色、文字的排版、响应动画与过度、填充等等；
- 在Flutter中高度集成了Material风格的Widget；
- 在我们的应用中，我们可以直接使用这些Widget来创建我们的应用（后面会用到很多）；

**Text小部件分析：**

- 我们可以使用Text小部件来完成文字的显示；
- 我们发现Text小部件继承自StatelessWidget，StatelessWidget继承自Widget；
- 所以我们可以将Text小部件传入到runApp函数中
- 属性非常多，但是我们已经学习了Dart语法，所以你会发现只有this.data属性是必须传入的。

```
class Text extends StatelessWidget {
  const Text(
    this.data, {
    Key key,
    this.style,
    this.strutStyle,
    this.textAlign,
    this.textDirection,
    this.locale,
    this.softWrap,
    this.overflow,
    this.textScaleFactor,
    this.maxLines,
    this.semanticsLabel,
    this.textWidthBasis,
  });
}
```
**StatelessWidget简单介绍：**

- StatelessWidget继承自Widget；
- 后面我会更加详细的介绍它的用法；

```
abstract class StatelessWidget extends Widget {
	// ...省略代码
}
```

## 代码改进
### 改进界面样式
我们发现现在的代码并不是我们想要的最终结果：

- 我们可能希望文字居中显示，并且可以大一些；
- 居中显示： 需要使用另外一个Widget，Center；
- 文字大一些： 需要给Text文本设置一些样式；

我们修改代码如下：

- 我们在Text小部件外层包装了一个Center部件，让Text作为其child；
- 并且，我们给Text组件设置了一个属性：style，对应的值是TextStyle类型；

```
import 'package:flutter/material.dart';

main(List<String> args) {
  runApp(
    Center(
      child: Text(
        "Hello World",
        textDirection: TextDirection.ltr,
        style: TextStyle(fontSize: 36),
      ),
    )
  );
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8lxza5x1j30u00gx74s.jpg)

### 改进界面结构
目前我们虽然可以显示HelloWorld，但是我们发现最底部的背景是黑色，并且我们的页面并不够结构化。

- 正常的App页面应该有一定的结构，比如通常都会有导航栏，会有一些背景颜色等

在开发当中，我们并不需要从零去搭建这种结构化的界面，我们可以使用Material库，直接使用其中的一些封装好的组件来完成一些结构的搭建。

我们通过下面的代码来实现：

```
import 'package:flutter/material.dart';

main(List<String> args) {
  runApp(
    MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text("CODERWHY"),
        ),
        body: Center(
          child: Text(
            "Hello World",
            textDirection: TextDirection.ltr,
            style: TextStyle(fontSize: 36),
          ),
        ),
      ),
    )
  );
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8lysj1iuj30u00gw3z4.jpg)

在最外层包裹一个MaterialApp

- 这意味着整个应用我们都会采用MaterialApp风格的一些东西，方便我们对应用的设计，并且目前我们使用了其中两个属性；
- title：这个是定义在Android系统中打开多任务切换窗口时显示的标题；（暂时可以不写）
- home：是该应用启动时显示的页面，我们传入了一个Scaffold；

Scaffold是什么呢？

- 翻译过来是脚手架，脚手架的作用就是搭建页面的基本结构；
- 所以我们给MaterialApp的home属性传入了一个Scaffold对象，作为启动显示的Widget；
- Scaffold也有一些属性，比如appBar和body；
- appBar是用于设计导航栏的，我们传入了一个title属性；
- body是页面的内容部分，我们传入了之前已经创建好的Center中包裹的一个Text的Widget；

### 进阶案例实现
我们可以让界面中存在更多的元素：

- 写到这里的时候，你可能已经发现嵌套太多了，不要着急，我们后面会对代码重构的

```
import 'package:flutter/material.dart';

main(List<String> args) {
  runApp(
    MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text("CODERWHY"),
        ),
        body: Center(
          child: Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Checkbox(
                value: true,
                onChanged: (value) => print("Hello World")),
              Text(
                "同意协议",
                textDirection: TextDirection.ltr,
                style: TextStyle(fontSize: 20),
              )
            ],
          ),
        ),
      ),
    )
  );
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8m0imu6oj30u00gwaar.jpg)

## 代码重构
### 创建自己的Widget
很多学习Flutter的人，都会被Flutter的嵌套劝退，当代码嵌套过多时，结构很容易看不清晰。

这里有两点我先说明一下：

- 1、Flutter整个开发过程中就是形成一个Widget树，所以形成嵌套是很正常的。
- 2、关于Flutter的代码缩进，更多开发中我们使用的是2个空格（前端开发2个空格居多，你喜欢4个也没问题）

但是，我们开发一个这么简单的程序就出现如此多的嵌套，如果应用程序更复杂呢？

- 我们可以对我们的代码进行封装，将它们封装到自己的Widget中，创建自己的Widget；

如何创建自己的Widget呢？

- 在Flutter开发中，我们可以继承自StatelessWidget或者StatefulWidget来创建自己的Widget类；
- StatelessWidget： 没有状态改变的Widget，通常这种Widget仅仅是做一些展示工作而已；
- StatefulWidget： 需要保存状态，并且可能出现状态改变的Widget；

在上面的案例中对代码的重构，我们使用StatelessWidget即可，所以我们接下来学习一下如果利用StatelessWidget来对我们的代码进行重构；

StatefulWidget我们放到后面的一个案例中来学习；

### StatelessWidget
StatelessWidget通常是一些没有状态（State，也可以理解成data）需要维护的Widget：

- 它们的数据通常是直接写死（放在Widget中的数据，必须被定义为final，为什么呢？我在下一个章节讲解StatefulWidget会讲到）；
- 从parent widget中传入的而且一旦传入就不可以修改；
- 从InheritedWidget获取来使用的数据（这个放到后面会讲解）；

我们来看一下创建一个StatelessWidget的格式：

- 1、让自己创建的Widget继承自StatelessWidget；
- 2、StatelessWidget包含一个必须重写的方法：build方法

```
class MyStatelessWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return <返回我们的Widget要渲染的Widget，比如一个Text Widget>;
  }
}
```

build方法的解析：

- Flutter在拿到我们自己创建的StatelessWidget时，就会执行它的build方法；
- 我们需要在build方法中告诉Flutter，我们的Widget希望渲染什么元素，比如一个Text Widget；
- StatelessWidget没办法主动去执行build方法，当我们使用的数据发生改变时，build方法会被重新执行；

build方法什么情况下被执行呢？：

- 1、当我们的StatelessWidget第一次被插入到Widget树中时（也就是第一次被创建时）；
- 2、当我们的父Widget（parent widget）发生改变时，子Widget会被重新构建；
- 3、如果我们的Widget依赖InheritedWidget的一些数据，InheritedWidget数据发生改变时；

### 重构案例代码
现在我们就可以通过StatelessWidget来对我们的代码进行重构了

- 因为我们的整个代码都是一些数据展示，没有数据的改变，使用StatelessWidget即可；
- 另外，为了体现更好的封装性，我对代码进行了两层的拆分，让代码结构看起来更加清晰；（具体的拆分方式，我会在后面的案例中不断的体现出来，目前我们先拆分两层）

重构后的代码如下：

```
import 'package:flutter/material.dart';

main(List<String> args) {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text("CODERWHY"),
        ),
        body: HomeContent(),
      ),
    )
  }
}

class HomeContent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Row(
        mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          Checkbox(
              value: true,
              onChanged: (value) => print("Hello World")),
          Text(
            "同意协议",
            textDirection: TextDirection.ltr,
            style: TextStyle(fontSize: 20),
          )
        ],
      ),
    );
  }
}
```

![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8m4eyjnij30u00gw3za.jpg)

# 案例练习
## 案例最终效果
我们先来看一下案例的最终展示效果：

- 这个效果中我们会使用很多没有接触的Widget；
- 没有关系，后面这些常用的Widget我会一个个讲解；
- 这个案例最主要的目的还是让大家更加熟悉Flutter的开发模式以及自定义Widget的封装过程；
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8m59xq7tj30u00gwq44.jpg)

##  自定义Widget
在我们的案例中，很明显一个产品的展示就是一个大的Widget，这个Widget包含如下Widget：

- 标题的Widget：使用一个Text Widget完成；
- 描述的Widget：使用一个Text Widget完成；
- 图片的Widget：使用一个Image Widget完成；
- 上面三个Widget要垂直排列，我们可以使用一个Column的Widget（上一个章节中我们使用了一次Row是水平排列的）

另外，三个展示的标题、描述、图片都是不一样的，所以我们可以让Parent Widget来决定内容：

- 创建三个成员变量保存父Widget传入的数据

```
class ProductItem extends StatelessWidget {
  final String title;
  final String desc;
  final String imageURL;

  ProductItem(this.title, this.desc, this.imageURL);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: <Widget>[
        Text(title, style: TextStyle(fontSize: 24)),
        Text(desc, style: TextStyle(fontSize: 18)),
        Image.network(imageURL)
      ],
    );
  }
}
```

## 列表数据展示
现在我们就可以创建三个ProductItem来让他们展示了：

- MyApp和上一个章节是一致的，没有任何改变；
- HomeContent中，我们使用了一个Column，因为我们创建的三个ProductItem是垂直排列的

```
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData(
        primaryColor: Colors.blueAccent
      ),
      home: Scaffold(
        appBar: AppBar(
          title: Text("CODERWHY"),
        ),
        body: HomeContent(),
      ),
    );
  }
}

class HomeContent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: <Widget>[
        ProductItem("Apple1", "Macbook Product1", "https://tva1.sinaimg.cn/large/006y8mN6gy1g72j6nk1d4j30u00k0n0j.jpg"),
        ProductItem("Apple2", "Macbook Product2", "https://tva1.sinaimg.cn/large/006y8mN6gy1g72imm9u5zj30u00k0adf.jpg"),
        ProductItem("Apple3", "Macbook Product3", "https://tva1.sinaimg.cn/large/006y8mN6gy1g72imqlouhj30u00k00v0.jpg"),
      ],
    );
  }
}
```
运行效果如下：

- 错误信息：下面出现了黄色的斑马线；
- 这是因为在Flutter的布局中，内容是不能超出屏幕范围的，当超出时不会自动变成滚动效果，而是会报下面的错误；
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8m7jfaq7j30u00gvdh6.jpg)
如何可以解决这个问题呢？

- 我们将Column换成ListView即可；
- ListView可以让自己的子Widget变成滚动的效果
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8m85xdb2j30u00gvmyd.jpg)

## 案例细节调整
### 界面整体边距
如果我们希望整个内容距离屏幕的边缘有一定的间距，怎么做呢？

- 我们需要使用另外一个Widget：Padding，它有一个padding属性用于设置边距大小；
- 没错，设置内边距也是使用Widget，这个Widget就是Padding；
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8m8vfjroj30u00gywfq.jpg)

### 商品内边距和边框
我们现在希望给所有的商品也添加一个内边距，并且还有边框，怎么做呢？

- 我们可以使用一个Container的Widget，它里面有padding属性，并且可以通过decoration来设置边框；
- Container我们也会在后面详细来讲，我们先用起来
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8m9kp9d1j30u00gwdgx.jpg)

### 文字图片的间距
我们希望给图片和文字之间添加一些间距，怎么做呢？

- 方式一：给图片或者文字添加一个向上的内边距或者向下的内边距；
- 方式二：使用SizedBox的Widget，设置一个height属性，可以增加一些距离；
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8ma4wmgaj30u00gyt9t.jpg)

## 最终实现代码
最后，我给出最终实现代码：

```
import 'package:flutter/material.dart';

main(List<String> args) {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData(
        primaryColor: Colors.blueAccent
      ),
      home: Scaffold(
        appBar: AppBar(
          title: Text("CODERWHY"),
        ),
        body: HomeContent(),
      ),
    );
  }
}

class HomeContent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Padding(
      padding: const EdgeInsets.all(8.0),
      child: ListView(
        children: <Widget>[
          ProductItem("Apple1", "Macbook Product1", "https://tva1.sinaimg.cn/large/006y8mN6gy1g72j6nk1d4j30u00k0n0j.jpg"),
          ProductItem("Apple2", "Macbook Product2", "https://tva1.sinaimg.cn/large/006y8mN6gy1g72imm9u5zj30u00k0adf.jpg"),
          ProductItem("Apple3", "Macbook Product3", "https://tva1.sinaimg.cn/large/006y8mN6gy1g72imqlouhj30u00k00v0.jpg"),
        ],
      ),
    );
  }
}

class ProductItem extends StatelessWidget {
  final String title;
  final String desc;
  final String imageURL;

  ProductItem(this.title, this.desc, this.imageURL);

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(20),
      decoration: BoxDecoration(
        border: Border.all()
      ),
      child: Column(
        children: <Widget>[
          Text(title, style: TextStyle(fontSize: 24)),
          Text(desc, style: TextStyle(fontSize: 18)),
          SizedBox(height: 10,),
          Image.network(imageURL)
        ],
      ),
    );
  }
}
```