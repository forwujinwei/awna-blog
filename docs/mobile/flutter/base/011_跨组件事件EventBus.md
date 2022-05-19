在组件之间如果有事件需要传递，一方面可以一层层来传递，另一方面我们也可以使用一个EventBus工具来完成。

其实EventBus在Vue、React中都是一种非常常见的跨组件通信的方式：

- EventBus相当于是一种订阅者模式，通过一个全局的对象来管理；
- 这个EventBus我们可以自己实现，也可以使用第三方的EventBus；

这里我们直接选择第三方的EventBus：

```
dependencies:
  event_bus:^1.1.1
```
第一：我们需要定义一个希望在组件之间传递的对象：

- 我们可以称之为一个时间对象，也可以是我们平时开发中用的模型对象（model）

```
class UserInfo {
  String nickname;
  int level;
  
  UserInfo(this.nickname, this.level);
}
```
第二：创建一个全局的EventBus对象

```
final eventBus = EventBus();
```
第三：在某个Widget中，发出事件：

```
class HYButton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return RaisedButton(
      child: Text("HYButton"),
      onPressed: () {
        final info = UserInfo("why", 18);
        eventBus.fire(info);
      },
    );
  }
}
```
第四：在某个Widget中，监听事件

```
class HYText extends StatefulWidget {
  @override
  _HYTextState createState() => _HYTextState();
}

class _HYTextState extends State<HYText> {
  String message = "Hello Coderwhy";

  @override
  void initState() {
    super.initState();

    eventBus.on<UserInfo>().listen((data) {
      setState(() {
        message = "${data.nickname}-${data.level}";
      });
    });
  }

  @override
  Widget build(BuildContext context) {
    return Text(message, style: TextStyle(fontSize: 30),);
  }
}
```
