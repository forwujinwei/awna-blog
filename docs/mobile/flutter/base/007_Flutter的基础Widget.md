# 文本Widget
在Android中，我们使用TextView，iOS中我们使用UILabel来显示文本；

Flutter中，我们使用Text组件控制文本如何展示；

## 普通文本展示
在Flutter中，我们可以将文本的控制显示分成两类：

- 控制文本布局的参数： 如文本对齐方式 textAlign、文本排版方向 textDirection，文本显示最大行数 maxLines、文本截断规则 overflow 等等，这些都是构造函数中的参数；
- 控制文本样式的参数： 如字体名称 fontFamily、字体大小 fontSize、文本颜色 color、文本阴影 shadows 等等，这些参数被统一封装到了构造函数中的参数 style 中；

下面我们来看一下其中一些属性的使用

```
class MyHomeBody extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text(
      "《定风波》 苏轼 \n莫听穿林打叶声，何妨吟啸且徐行。\n竹杖芒鞋轻胜马，谁怕？一蓑烟雨任平生。",
      style: TextStyle(
        fontSize: 20,
        color: Colors.purple
      ),
    );
  }
}
```
展示效果如下：
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8mw0qrxvj30u00gs0th.jpg)

我们可以通过一些属性来改变Text的布局：

- textAlign：文本对齐方式，比如TextAlign.center
- maxLines：最大显示行数，比如1
- overflow：超出部分显示方式，比如TextOverflow.ellipsis
- textScaleFactor：控制文本缩放，比如1.24

代码如下

```
class MyHomeBody extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text(
      "《定风波》 苏轼 \n莫听穿林打叶声，何妨吟啸且徐行。\n竹杖芒鞋轻胜马，谁怕？一蓑烟雨任平生。",
      textAlign: TextAlign.center, // 所有内容都居中对齐
      maxLines: 3, // 显然 "生。" 被删除了
      overflow: TextOverflow.ellipsis, // 超出部分显示...
//      textScaleFactor: 1.25,
      style: TextStyle(
        fontSize: 20,
        color: Colors.purple
      ),
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8mwr48p3j30u00gzgmg.jpg)

## 富文本展示
前面展示的文本，我们都应用了相同的样式，如果我们希望给他们不同的样式呢？

- 比如《定风波》我希望字体更大一点，并且是黑色字体，并且有加粗效果；
- 比如 苏轼 我希望是红色字体；

如果希望展示这种混合样式，那么我们可以利用分片来进行操作（在Android中，我们可以使用SpannableString，在iOS中，我们可以使用NSAttributedString完成，了解即可）

代码如下：

```
class MyHomeBody extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Text.rich(
      TextSpan(
        children: [
          TextSpan(text: "《定风波》", style: TextStyle(fontSize: 25, fontWeight: FontWeight.bold, color: Colors.black)),
          TextSpan(text: "苏轼", style: TextStyle(fontSize: 18, color: Colors.redAccent)),
          TextSpan(text: "\n莫听穿林打叶声，何妨吟啸且徐行。\n竹杖芒鞋轻胜马，谁怕？一蓑烟雨任平生。")
        ],
      ),
      style: TextStyle(fontSize: 20, color: Colors.purple),
      textAlign: TextAlign.center,
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8mybnzxtj30u00gyq3v.jpg)

# 按钮Widget

## 按钮的基础
Material widget库中提供了多种按钮Widget如FloatingActionButton、RaisedButton、FlatButton、OutlineButton等

我们直接来对他们进行一个展示：


```
class MyHomeBody extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: <Widget>[
        FloatingActionButton(
          child: Text("FloatingActionButton"),
          onPressed: () {
            print("FloatingActionButton Click");
          },
        ),
        RaisedButton(
          child: Text("RaisedButton"),
          onPressed: () {
            print("RaisedButton Click");
          },
        ),
        FlatButton(
          child: Text("FlatButton"),
          onPressed: () {
            print("FlatButton Click");
          },
        ),
        OutlineButton(
          child: Text("OutlineButton"),
          onPressed: () {
            print("OutlineButton Click");
          },
        )
      ],
    );
  }
}
```

![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8mzdslqgj30u00gyt9k.jpg)

## 自定义样式
前面的按钮我们使用的都是默认样式，我们可以通过一些属性来改变按钮的样式

```
RaisedButton(
  child: Text("同意协议", style: TextStyle(color: Colors.white)),
  color: Colors.orange, // 按钮的颜色
  highlightColor: Colors.orange[700], // 按下去高亮颜色
  shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(20)), // 圆角的实现
  onPressed: () {
    print("同意协议");
  },
)
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8n01305zj30u00h0754.jpg)

事实上这里还有一个比较常见的属性：elevation，用于控制阴影的大小，很多地方都会有这个属性，大家可以自行演示一下

# 图片Widget

图片可以让我们的应用更加丰富多彩，Flutter中使用Image组件

Image组件有很多的构造函数，我们这里主要学习两个：

- Image.assets：加载本地资源图片；
- Image.network：加载网络中的图片；

## 加载网络图片
相对来讲，Flutter中加载网络图片会更加简单，直接传入URL并不需要什么配置，所以我们先来看一下Flutter中如何加载网络图片。

我们先来看看Image有哪些属性可以设置：

```
const Image({
  ...
  this.width, //图片的宽
  this.height, //图片高度
  this.color, //图片的混合色值
  this.colorBlendMode, //混合模式
  this.fit,//缩放模式
  this.alignment = Alignment.center, //对齐方式
  this.repeat = ImageRepeat.noRepeat, //重复方式
  ...
})
```
- width、height：用于设置图片的宽、高，当不指定宽高时，图片会根据当前父容器的限制，尽可能的显示其原始大小，如果只设置width、height的其中一个，那么另一个属性默认会按比例缩放，但可以通过下面介绍的fit属性来指定适应规则。
- fit：该属性用于在图片的显示空间和图片本身大小不同时指定图片的适应模式。适应模式是在BoxFit中定义，它是一个枚举类型，有如下值：
  + fill：会拉伸填充满显示空间，图片本身长宽比会发生变化，图片会变形。
  + cover：会按图片的长宽比放大后居中填满显示空间，图片不会变形，超出显示空间部分会被剪裁。
  + contain：这是图片的默认适应规则，图片会在保证图片本身长宽比不变的情况下缩放以适应当前显示空间，图片不会变形。
  + fitWidth：图片的宽度会缩放到显示空间的宽度，高度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁。
  + fitHeight：图片的高度会缩放到显示空间的高度，宽度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁。
  + none：图片没有适应策略，会在显示空间内显示图片，如果图片比显示空间大，则显示空间只会显示图片中间部分。
- color和 colorBlendMode：在图片绘制时可以对每一个像素进行颜色混合处理，color指定混合色，而colorBlendMode指定混合模式；
- repeat：当图片本身大小小于显示空间时，指定图片的重复规则。

我们对其中某些属性做一个演练：

- 注意，这里我用了一个Container，大家可以把它理解成一个UIView或者View，就是一个容器；
- 后面我会专门讲到这个组件的使用；

```
class MyHomeBody extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        child: Image.network(
          "http://img0.dili360.com/ga/M01/48/3C/wKgBy1kj49qAMVd7ADKmuZ9jug8377.tub.jpg",
          alignment: Alignment.topCenter,
          repeat: ImageRepeat.repeatY,
          color: Colors.red,
          colorBlendMode: BlendMode.colorDodge,
        ),
        width: 300,
        height: 300,
        color: Colors.yellow,
      ),
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8n4ofwn1j30u00gy758.jpg)

## 加载本地图片
加载本地图片稍微麻烦一点，需要将图片引入，并且进行配置

```
class MyHomeBody extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        width: 300,
        height: 300,
        color: Colors.yellow,
        child: Image.asset("images/test.jpeg"),
      ),
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8n5f9lv4j30u00gzmy7.jpg)

## 实现圆角图像
### 实现圆角头像
方式一：CircleAvatar

CircleAvatar可以实现圆角头像，也可以添加一个子Widget：

```
const CircleAvatar({
  Key key,
  this.child, // 子Widget
  this.backgroundColor, // 背景颜色
  this.backgroundImage, // 背景图像
  this.foregroundColor, // 前景颜色
  this.radius, // 半径
  this.minRadius, // 最小半径
  this.maxRadius, // 最大半径
})
```
我们来实现一个圆形头像：

- 注意一：这里我们使用的是NetworkImage，因为backgroundImage要求我们传入一个ImageProvider；
  + ImageProvider是一个抽象类，事实上所有我们前面创建的Image对象都有包含image属性，该属性就是一个ImageProvider

- 注意二：这里我还在里面添加了一个文字，但是我在文字外层包裹了一个Container；
  + 这里Container的作用是为了可以控制文字在其中的位置调整；
```
class HomeContent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: CircleAvatar(
        radius: 100,
        backgroundImage: NetworkImage("https://tva1.sinaimg.cn/large/006y8mN6gy1g7aa03bmfpj3069069mx8.jpg"),
        child: Container(
          alignment: Alignment(0, .5),
          width: 200,
          height: 200,
          child: Text("兵长利威尔")
        ),
      ),
    );
  }
}

```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8n7nii2uj30u00gw753.jpg)

**方式二：ClipOval**

ClipOval也可以实现圆角头像，而且通常是在只有头像时使用

```
class HomeContent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: ClipOval(
        child: Image.network(
          "https://tva1.sinaimg.cn/large/006y8mN6gy1g7aa03bmfpj3069069mx8.jpg",
          width: 200,
          height: 200,
        ),
      ),
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8n8ngw1wj30u00gyq3s.jpg)

### 实现圆角图片
**方式一：ClipRRect**

ClipRRect用于实现圆角效果，可以设置圆角的大小。

实现代码如下，非常简单：


```
class HomeContent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Center(
      child: ClipRRect(
        borderRadius: BorderRadius.circular(10),
        child: Image.network(
          "https://tva1.sinaimg.cn/large/006y8mN6gy1g7aa03bmfpj3069069mx8.jpg",
          width: 200,
          height: 200,
        ),
      ),
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8n9lzr5jj30u00gwt9k.jpg)

# 表单Widget
## TextField的使用
### TextField的介绍
TextField用于接收用户的文本输入，它提供了非常多的属性，我们来看一下源码：
- 但是我们没必要一个个去学习，很多时候用到某个功能时去查看是否包含某个属性即可

```
const TextField({
    Key key,
    this.controller,                    // 控制正在编辑文本
    this.focusNode,                     // 获取键盘焦点
    this.decoration = const InputDecoration(),              // 边框装饰
    TextInputType keyboardType,         // 键盘类型
    this.textInputAction,               // 键盘的操作按钮类型
    this.textCapitalization = TextCapitalization.none,      // 配置大小写键盘
    this.style,                         // 输入文本样式
    this.textAlign = TextAlign.start,   // 对齐方式
    this.textDirection,                 // 文本方向
    this.autofocus = false,             // 是否自动对焦
    this.obscureText = false,           // 是否隐藏内容，例如密码格式
    this.autocorrect = true,            // 是否自动校正
    this.maxLines = 1,                  // 最大行数
    this.maxLength,                     // 允许输入的最大长度
    this.maxLengthEnforced = true,      // 是否允许超过输入最大长度
    this.onChanged,                     // 文本内容变更时回调
    this.onEditingComplete,             // 提交内容时回调
    this.onSubmitted,                   // 用户提示完成时回调
    this.inputFormatters,               // 验证及格式
    this.enabled,                       // 是否不可点击
    this.cursorWidth = 2.0,             // 光标宽度
    this.cursorRadius,                  // 光标圆角弧度
    this.cursorColor,                   // 光标颜色
    this.keyboardAppearance,            // 键盘亮度
    this.scrollPadding = const EdgeInsets.all(20.0),        // 滚动到视图中时，填充边距
    this.enableInteractiveSelection,    // 长按是否展示【剪切/复制/粘贴菜单LengthLimitingTextInputFormatter】
    this.onTap,                         // 点击时回调
    this.buildCounter,
    this.scrollController,
    this.scrollPhysics,
})
```
我们来学习几个比较常见的属性：

- 一些属性比较简单：keyboardType键盘的类型，style设置样式，textAlign文本对齐方式，maxLength最大显示行数等等；
- decoration：用于设置输入框相关的样式
  + icon：设置左边显示的图标
  + labelText：在输入框上面显示一个提示的文本
  + hintText：显示提示的占位文字
  + border：输入框的边框，默认底部有一个边框，可以通过InputBorder.none删除掉
  + filled：是否填充输入框，默认为false
  + fillColor：输入框填充的颜色
- controller：
- onChanged：监听输入框内容的改变，传入一个回调函数
- onSubmitted：点击键盘中右下角的down时，会回调的一个函数

### TextField的样式以及监听
我们来演示一下TextField的decoration属性以及监听：

```
class HomeContent extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(20),
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          TextFieldDemo()
        ],
      ),
    );
  }
}

class TextFieldDemo extends StatefulWidget {
  @override
  _TextFieldDemoState createState() => _TextFieldDemoState();
}

class _TextFieldDemoState extends State<TextFieldDemo> {
  @override
  Widget build(BuildContext context) {
    return TextField(
      decoration: InputDecoration(
        icon: Icon(Icons.people),
        labelText: "username",
        hintText: "请输入用户名",
        border: InputBorder.none,
        filled: true,
        fillColor: Colors.lightGreen
      ),
      onChanged: (value) {
        print("onChanged:$value");
      },
      onSubmitted: (value) {
        print("onSubmitted:$value");
      },
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8nd57vg0j30u00gwq3s.jpg)

### TextField的controller
我们可以给TextField添加一个控制器（Controller），可以使用它设置文本的初始值，也可以使用它来监听文本的改变；

事实上，如果我们没有为TextField提供一个Controller，那么会Flutter会默认创建一个TextEditingController的，这个结论可以通过阅读源码得到：

```
@override
  void initState() {
    super.initState();
    // ...其他代码
    if (widget.controller == null)
      _controller = TextEditingController();
  }
```

我们也可以自己来创建一个Controller控制一些内容：

```
class _TextFieldDemoState extends State<TextFieldDemo> {
  final textEditingController = TextEditingController();

  @override
  void initState() {
    super.initState();

    // 1.设置默认值
    textEditingController.text = "Hello World";

    // 2.监听文本框
    textEditingController.addListener(() {
      print("textEditingController:${textEditingController.text}");
    });
  }
	
  // ...省略build方法
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8ne1t7g0j30u00gwdgt.jpg)

## Form表单的使用
### Form表单的基本使用
Form表单也是一个Widget，可以在里面放入我们的输入框。

但是Form表单中输入框必须是FormField类型的
- 我们查看刚刚学过的TextField是继承自StatefulWidget，并不是一个FormField类型；
- 我们可以使用TextFormField，它的使用类似于TextField，并且是继承自FormField的；

我们通过Form的包裹，来实现一个注册的页面：

```
class FormDemo extends StatefulWidget {
  @override
  _FormDemoState createState() => _FormDemoState();
}

class _FormDemoState extends State<FormDemo> {
  @override
  Widget build(BuildContext context) {
    return Form(
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          TextFormField(
            decoration: InputDecoration(
              icon: Icon(Icons.people),
              labelText: "用户名或手机号"
            ),
          ),
          TextFormField(
            obscureText: true,
            decoration: InputDecoration(
              icon: Icon(Icons.lock),
              labelText: "密码"
            ),
          ),
          SizedBox(height: 16,),
          Container(
            width: double.infinity,
            height: 44,
            child: RaisedButton(
              color: Colors.lightGreen,
              child: Text("注 册", style: TextStyle(fontSize: 20, color: Colors.white),),
              onPressed: () {
                print("点击了注册按钮");
              },
            ),
          )
        ],
      ),
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8nf27yjcj30u00gz3zi.jpg)

### 保存和获取表单数据
有了表单后，我们需要在点击注册时，可以同时获取和保存表单中的数据，怎么可以做到呢？
- 1、需要监听注册按钮的点击，在之前我们已经监听的onPressed传入的回调中来做即可。（当然，如果嵌套太多，我们待会儿可以将它抽取到一个单独的方法中）
- 2、监听到按钮点击时，同时获取用户名和密码的表单信息。

如何同时获取用户名和密码的表单信息？
- 如果我们调用Form的State对象的save方法，就会调用Form中放入的TextFormField的onSave回调：

```
TextFormField(
  decoration: InputDecoration(
    icon: Icon(Icons.people),
    labelText: "用户名或手机号"
  ),
  onSaved: (value) {
    print("用户名：$value");
  },
),
```
- 但是，我们有没有办法可以在点击按钮时，拿到 Form对象 来调用它的save方法呢？
知识点：在Flutter如何可以获取一个通过一个引用获取一个StatefulWidget的State对象呢？

答案：通过绑定一个GlobalKey即可。
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8ng3pme9j30u00g3dgo.jpg)

案例代码演练：

```
class FormDemo extends StatefulWidget {
  @override
  _FormDemoState createState() => _FormDemoState();
}

class _FormDemoState extends State<FormDemo> {
  final registerFormKey = GlobalKey<FormState>();
  String username, password;

  void registerForm() {
    registerFormKey.currentState.save();

    print("username:$username password:$password");
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      key: registerFormKey,
      child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: <Widget>[
          TextFormField(
            decoration: InputDecoration(
              icon: Icon(Icons.people),
              labelText: "用户名或手机号"
            ),
            onSaved: (value) {
              this.username = value;
            },
          ),
          TextFormField(
            obscureText: true,
            decoration: InputDecoration(
              icon: Icon(Icons.lock),
              labelText: "密码"
            ),
            onSaved: (value) {
              this.password = value;
            },
          ),
          SizedBox(height: 16,),
          Container(
            width: double.infinity,
            height: 44,
            child: RaisedButton(
              color: Colors.lightGreen,
              child: Text("注 册", style: TextStyle(fontSize: 20, color: Colors.white),),
              onPressed: registerForm,
            ),
          )
        ],
      ),
    );
  }
}
```
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8ngmw4hlj30u00gvjsf.jpg)

### 验证填写的表单数据
在表单中，我们可以添加验证器，如果不符合某些特定的规则，那么给用户一定的提示信息

比如我们需要账号和密码有这样的规则：账号和密码都不能为空。

按照如下步骤就可以完成整个验证过程：

- 1、为TextFormField添加validator的回调函数；
- 2、调用Form的State对象的validate方法，就会回调validator传入的函数；
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8nh8smnrj30u00h3758.jpg)

也可以为TextFormField添加一个属性：autovalidate
- 不需要调用validate方法，会自动验证是否符合要求；
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi8nhuaiuvj30u007i0ss.jpg)
