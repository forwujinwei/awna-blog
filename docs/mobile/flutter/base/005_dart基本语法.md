## Dart介绍和安装
Google为Flutter选择了Dart就已经是既定的事实，无论你多么想用你熟悉的语言，比如JavaScript、Java、Swift、C++等来开发Flutter，至少目前都是不可以的。

在讲解Dart的过程中，我会假定你已经有一定的编程语言基础，比如JavaScript、Java、Python、C++等。

其实如果你对编程语言足够的自信，Dart的学习过程甚至可以直接忽略：

因为你学过N种编程语言之后，你会发现他们的差异是并不大；

无非就是语法上的差异+某些语言有某些特性，而某些语言没有某些特性而已；

在我初次接触Flutter的时候，并没有专门去看Dart的语法，而是对于某些语法不太熟练的时候回头去了解而已；

所以，如果你对编程语言已经足够了解，可以跳过我们接下来的Dart学习：

我也并不会所有特性都一一罗列，我会挑出比较重要的语言特性来专门讲解；

某些特性可能会等到后面讲解Flutter的一些知识的时候单独拿出来讲解；

下面，我们就从安装Dart开始吧！

##  安装Dart
下载Dart SDK

到Dart的官方，根据不同的操作系统下载对应的Dart

官方网站：https://dart.dev/get-dart

无论是什么操作系统，安装方式都是有两种：通过工具安装和直接下载SDK，配置环境变量

1.通过工具安装

- Windows可以通过Chocolatey
- macOS可以通过homebrew
- 具体安装操作官网网站有详细的解释

2.直接下载SDK，配置环境变量

下载地址：https://dart.dev/tools/sdk/archive

我采用了这个安装方式。

下载完成后，根据路径配置环境变量即可。

## VSCode配置
学习Dart过程中，我使用VSCode作为编辑器

- 一方面编写代码非常方便，而且界面风格我也很喜欢
- 另一方面我可以快速在终端看到我编写代码的效果

使用VSCode编写Dart需要安装Dart插件：我目前给这个VSCode装了四个插件

- Dart和Flutter插件是为Flutter开发准备的
- Atom One Dark Theme是我个人比较喜欢的一个主题
- Code Runner可以点击右上角的按钮让我快速运行代码

## Hello Dart
### Hello World
接下来，就可以步入正题了。学习编程语言，从祖传的Hello World开始。

在VSCode中新建一个helloWorld.dart文件，添加下面的内容：

```
main(List<String> args) {
  print('Hello World');
}
```
然后在终端执行dart helloWorld.dart，就能看到Hello World的结果了。

完成了这个执行过程之后，以你之前学习的编程语言来看，你能得到多少信息呢？

### 程序的分析
接下来，就是我自己的总结：

- 一、Dart语言的入口也是main函数，并且必须显示的进行定义；
- 二、Dart的入口函数main是没有返回值的；
- 三、传递给main的命令行参数，是通过List<String>完成的。
- 从字面值就可以理解List是Dart中的集合类型。
- 其中的每一个String都表示传递给main的一个参数；
- 四、定义字符串的时候，可以使用单引号或双引号；
- 五、每行语句必须使用分号结尾，很多语言并不需要分号，比如Swift、JavaScript；

## 定义变量
### 明确声明(Explicit)
明确声明变量的方式, 格式如下:

```
变量类型 变量名称 = 赋值;
```
示例代码:

```

String name = 'coderwhy';
int age = 18;
double height = 1.88;
print('${name}, ${age}, ${height}'); // 拼接方式后续会讲解
```
注意事项: 定义的变量可以修改值, 但是不能赋值其他类型

```
String content = 'Hello Dart';
content = 'Hello World'; // 正确的
content = 111; // 错误的, 将一个int值赋值给一个String变量
```

### 类型推导(Type Inference)
类型推导声明变量的方式, 格式如下:

```
var/dynamic/const/final 变量名称 = 赋值;
```
#### var的使用
var的使用示例:
- runtimeType用于获取变量当前的类型

```
var name = 'coderwhy';
name = 'kobe';
print(name.runtimeType); // String
```
var的错误用法:

```
var age = 18;
age = 'why'; // 不可以将String赋值给一个int类型
```

dynamic的使用
如果确实希望这样做,可以使用dynamic来声明变量:

- 但是在开发中, 通常情况下不使用dynamic, 因为类型的变量会带来潜在的危险

```
dynamic name = 'coderwhy';
print(name.runtimeType); // String
name = 18;
print(name.runtimeType); // int
```

#### final&const的使用
final和const都是用于定义常量的, 也就是定义之后值都不可以修改

```
final name = 'coderwhy';
name = 'kobe'; // 错误做法

const age = 18;
age = 20; // 错误做法
```

final和const有什么区别呢?

- const在赋值时, 赋值的内容必须是在编译期间就确定下来的
- final在赋值时, 可以动态获取, 比如赋值一个函数

```
String getName() {
  return 'coderwhy';
}

main(List<String> args) {
  const name = getName(); // 错误的做法, 因为要执行函数才能获取到值
  final name = getName(); // 正确的做法
}
```

final和const小案例:

- 首先, const是不可以赋值为DateTime.now()
- 其次, final一旦被赋值后就有确定的结果, 不会再次赋值

```
// const time = DateTime.now(); // 错误的赋值方式
final time = DateTime.now();
print(time); // 2019-04-05 09:02:54.052626

sleep(Duration(seconds: 2));
print(time); // 2019-04-05 09:02:54.052626
```
const放在赋值语句的右边，可以共享对象，提高性能:

- 这里可以暂时先做了解，后面讲解类的常量构造函数时，我会再次提到这个概念

```
class Person {
  const Person();
}

main(List<String> args) {
  final a = const Person();
  final b = const Person();
  print(identical(a, b)); // true

  final m = Person();
  final n = Person();
  print(identical(m, n)); // false
}
```
### 数据类型
#### 数字类型
对于数值来说，我们也不用关心它是否有符号，以及数据的宽度和精度等问题。只要记着整数用int，浮点数用double就行了。

不过，要说明一下的是Dart中的int和double可表示的范围并不是固定的，它取决于运行Dart的平台
```
// 1.整数类型int
int age = 18;
int hexAge = 0x12;
print(age);
print(hexAge);

// 2.浮点类型double
double height = 1.88;
print(height);
```
字符串和数字之间的转化:

```

// 字符串和数字转化
// 1.字符串转数字
var one = int.parse('111');
var two = double.parse('12.22');
print('${one} ${one.runtimeType}'); // 111 int
print('${two} ${two.runtimeType}'); // 12.22 double

// 2.数字转字符串
var num1 = 123;
var num2 = 123.456;
var num1Str = num1.toString();
var num2Str = num2.toString();
var num2StrD = num2.toStringAsFixed(2); // 保留两位小数
print('${num1Str} ${num1Str.runtimeType}'); // 123 String
print('${num2Str} ${num2Str.runtimeType}'); // 123.456 String
print('${num2StrD} ${num2StrD.runtimeType}'); // 123.46 String
```

#### 布尔类型
布尔类型中,Dart提供了一个bool的类型, 取值为true和false


```
// 布尔类型
var isFlag = true;
print('$isFlag ${isFlag.runtimeType}');
```
注意: Dart中不能判断非0即真, 或者非空即真
Dart的类型安全性意味着您不能使用if(非booleanvalue)或assert(非booleanvalue)之类的代码。

```
var message = 'Hello Dart';
  // 错误的写法
  if (message) {
    print(message)
  }
```
#### 字符串类型
Dart字符串是UTF-16编码单元的序列。您可以使用单引号或双引号创建一个字符串:

```
// 1.定义字符串的方式
var s1 = 'Hello World';
var s2 = "Hello Dart";
var s3 = 'Hello\'Fullter';
var s4 = "Hello'Fullter";
```
可以使用三个单引号或者双引号表示多行字符串:

```
// 2.表示多行字符串的方式
var message1 = '''
哈哈哈
呵呵呵
嘿嘿嘿''';
```
字符串和其他变量或表达式拼接: 使用${expression}, 如果表达式是一个标识符, 那么{}可以省略

```
// 3.拼接其他变量
var name = 'coderwhy';
var age = 18;
var height = 1.88;
print('my name is ${name}, age is $age, height is $height');
```

### 集合类型
#### 集合类型的定义
对于集合类型，Dart则内置了最常用的三种：List / Set / Map。

其中，List可以这样来定义：

```
// List定义
// 1.使用类型推导定义
var letters = ['a', 'b', 'c', 'd'];
print('$letters ${letters.runtimeType}');

// 2.明确指定类型
List<int> numbers = [1, 2, 3, 4];
print('$numbers ${numbers.runtimeType}');
```
其中，set可以这样来定义：

- 其实，也就是把[]换成{}就好了。
- Set和List最大的两个不同就是：Set是无序的，并且元素是不重复的。

```
// Set的定义
// 1.使用类型推导定义
var lettersSet = {'a', 'b', 'c', 'd'};
print('$lettersSet ${lettersSet.runtimeType}');

// 2.明确指定类型
Set<int> numbersSet = {1, 2, 3, 4};
print('$numbersSet ${numbersSet.runtimeType}');
```


最后，**Map**是我们常说的字典类型，它的定义是这样的：

```
// Map的定义
// 1.使用类型推导定义
var infoMap1 = {'name': 'why', 'age': 18};
print('$infoMap1 ${infoMap1.runtimeType}');

// 2.明确指定类型
Map<String, Object> infoMap2 = {'height': 1.88, 'address': '北京市'};
print('$infoMap2 ${infoMap2.runtimeType}');
```
#### 集合的常见操作
了解了这三个集合的定义方式之后，我们来看一些最基础的公共操作

第一类，是所有集合都支持的获取长度的属性length：
```
// 获取集合的长度
print(letters.length);
print(lettersSet.length);
print(infoMap1.length);
```

第二类, 是添加/删除/包含操作

并且，对List来说，由于元素是有序的，它还提供了一个删除指定索引位置上元素的方法

```
// 添加/删除/包含元素
numbers.add(5);
numbersSet.add(5);
print('$numbers $numbersSet');

numbers.remove(1);
numbersSet.remove(1);
print('$numbers $numbersSet');

print(numbers.contains(2));
print(numbersSet.contains(2));

// List根据index删除元素
numbers.removeAt(3);
print('$numbers');
```

第三类，是Map的操作

由于它有key和value，因此无论是读取值，还是操作，都要明确是基于key的，还是基于value的，或者是基于key/value对的。


```
// Map的操作
// 1.根据key获取value
print(infoMap1['name']); // why

// 2.获取所有的entries
print('${infoMap1.entries} ${infoMap1.entries.runtimeType}'); // (MapEntry(name: why), MapEntry(age: 18)) MappedIterable<String, MapEntry<String, Object>>

// 3.获取所有的keys
print('${infoMap1.keys} ${infoMap1.keys.runtimeType}'); // (name, age) _CompactIterable<String>

// 4.获取所有的values
print('${infoMap1.values} ${infoMap1.values.runtimeType}'); // (why, 18) _CompactIterable<Object>

// 5.判断是否包含某个key或者value
print('${infoMap1.containsKey('age')} ${infoMap1.containsValue(18)}'); // true true

// 6.根据key删除元素
infoMap1.remove('age');
print('${infoMap1}'); // {name: why}
```

### 函数
#### 函数的基本定义
Dart是一种真正的面向对象语言，所以即使函数也是对象，所有也有类型, 类型就是Function。

这也就意味着函数可以作为变量定义或者作为其他函数的参数或者返回值.

函数的定义方式:

```
返回值 函数的名称(参数列表) {
  函数体
  return 返回值
}
```

按照上面的定义方式, 我们定义一个完整的函数:


```
int sum(num num1, num num2) {
  return num1 + num2;
}
```

Effective Dart建议对公共的API, 使用类型注解, 但是如果我们省略掉了类型, 依然是可以正常工作的


```
sum(num1, num2) {
  return num1 + num2;
}
```

另外, 如果函数中只有一个表达式, 那么可以使用箭头语法(arrow syntax)

注意, 这里面只能是一个表达式, 不能是一个语句


```
sum(num1, num2) => num1 + num2;
```
#### 函数的参数问题
##### 可选参数
可选参数可以分为 命名可选参数 和 位置可选参数

定义方式:


```
命名可选参数: {param1, param2, ...}
位置可选参数: [param1, param2, ...]
```

命名可选参数的演示:


```
// 命名可选参数
printInfo1(String name, {int age, double height}) {
  print('name=$name age=$age height=$height');
}

// 调用printInfo1函数
printInfo1('why'); // name=why age=null height=null
printInfo1('why', age: 18); // name=why age=18 height=null
printInfo1('why', age: 18, height: 1.88); // name=why age=18 height=1.88
printInfo1('why', height: 1.88); // name=why age=null height=1.88
```

位置可选参数的演示:


```
// 定义位置可选参数
printInfo2(String name, [int age, double height]) {
  print('name=$name age=$age height=$height');
}

// 调用printInfo2函数
printInfo2('why'); // name=why age=null height=null
printInfo2('why', 18); // name=why age=18 height=null
printInfo2('why', 18, 1.88); // name=why age=18 height=1.88
命名可选参数, 可以指定某个参数是必传的(使用@required, 有问题)

// 命名可选参数的必须
printInfo3(String name, {int age, double height, @required String address}) {
  print('name=$name age=$age height=$height address=$address');
}
```

##### 参数默认值

参数可以有默认值, 在不传入的情况下, 使用默认值

注意, 只有可选参数才可以有默认值, 必须参数不能有默认值


```
// 参数的默认值
printInfo4(String name, {int age = 18, double height=1.88}) {
  print('name=$name age=$age height=$height');
}
```

Dart中的main函数就是一个接受可选的列表参数作为参数的, 所以在使用main函数时, 我们可以传入参数, 也可以不传入

#### 函数是一等公民
在很多语言中, 函数并不能作为一等公民来使用, 比如Java/OC. 这种限制让编程不够灵活, 所以现代的编程语言基本都支持函数作为一等公民来使用, Dart也支持.

这就意味着你可以将函数赋值给一个变量, 也可以将函数作为另外一个函数的参数或者返回值来使用.


```
main(List<String> args) {
  // 1.将函数赋值给一个变量
  var bar = foo;
  print(bar);

  // 2.将函数作为另一个函数的参数
  test(foo);

  // 3.将函数作为另一个函数的返回值
  var func =getFunc();
  func('kobe');
}

// 1.定义一个函数
foo(String name) {
  print('传入的name:$name');
}

// 2.将函数作为另外一个函数的参数
test(Function func) {
  func('coderwhy');
}

// 3.将函数作为另一个函数的返回值
getFunc() {
  return foo;
}
```
#### 匿名函数的使用
大部分我们定义的函数都会有自己的名字， 比如前面定义的foo、test函数等等。

但是某些情况下，给函数命名太麻烦了，我们可以使用没有名字的函数，这种函数可以被称之为匿名函数( anonymous function)，也可以叫lambda或者closure。

```
main(List<String> args) {
  // 1.定义数组
  var movies = ['盗梦空间', '星际穿越', '少年派', '大话西游'];

  // 2.使用forEach遍历: 有名字的函数
  printElement(item) {
    print(item);
  }
  movies.forEach(printElement);

  // 3.使用forEach遍历: 匿名函数
  movies.forEach((item) {
    print(item);
  });
  movies.forEach((item) => print(item));
}
```

#### 词法作用域
dart中的词法有自己明确的作用域范围，它是根据代码的结构({})来决定作用域范围的

优先使用自己作用域中的变量，如果没有找到，则一层层向外查找。

```
var name = 'global';
main(List<String> args) {
  // var name = 'main';
  void foo() {
    // var name = 'foo';
    print(name);
  }

  foo();
}
```
#### 词法闭包
闭包可以访问其词法范围内的变量，即使函数在其他地方被使用，也可以正常的访问。

```
main(List<String> args) {
  makeAdder(num addBy) {
    return (num i) {
      return i + addBy;
    };
  }

  var adder2 = makeAdder(2);
  print(adder2(10)); // 12
  print(adder2(6)); // 8

  var adder5 = makeAdder(5);
  print(adder5(10)); // 15
  print(adder5(6)); // 11
}
```
#### 返回值问题
所有函数都返回一个值。如果没有指定返回值，则语句返回null;隐式附加到函数体。


```
main(List<String> args) {
  print(foo()); // null
}

foo() {
  print('foo function');
}
```

# 运算符

这里，我只列出来相对其他语言比较特殊的运算符，因为某些运算符太简单了，不浪费时间，比如+、-、+=、==。

你可能会疑惑，Dart为什么要搞出这么多特殊的运算符呢？

你要坚信一点：所有这些特殊的运算符都是为了让我们在开发中可以更加方便的操作，而不是让我们的编码变得更加复杂。

## 除法、整除、取模运算
我们来看一下除法、整除、取模运算

```
var num = 7;
print(num / 3); // 除法操作, 结果2.3333..
print(num ~/ 3); // 整除操作, 结果2;
print(num % 3); // 取模操作, 结果1;
```
## ??=赋值操作
dart有一个很多语言都不具备的赋值运算符：

- 当变量为null时，使用后面的内容进行赋值。
- 当变量有值时，使用自己原来的值。

```
main(List<String> args) {
  var name1 = 'coderwhy';
  print(name1);
  // var name2 = 'kobe';
  var name2 = null;
  name2 ??= 'james'; 
  print(name2); // 当name2初始化为kobe时，结果为kobe，当初始化为null时，赋值了james
}
```
## 条件运算符
Dart中包含一直比较特殊的条件运算符：expr1 ?? expr2

- 如果expr1是null，则返回expr2的结果;
- 如果expr1不是null，直接使用expr1的结果。

```
var temp = 'why';
var temp = null;
var name = temp ?? 'kobe';
print(name);
```
## 级联语法：..
- 某些时候，我们希望对一个对象进行连续的操作，这个时候可以使用级联语法

```
class Person {
  String name;

  void run() {
    print("${name} is running");
  }

  void eat() {
    print("${name} is eating");
  }

  void swim() {
    print("${name} is swimming");
  }
}

main(List<String> args) {
  final p1 = Person();
  p1.name = 'why';
  p1.run();
  p1.eat();
  p1.swim();

  final p2 = Person()
              ..name = "why"
              ..run()
              ..eat()
              ..swim();
}
```

# 流程控制
## if和else
和其他语言用法一样

这里有一个注意点：不支持非空即真或者非0即真，必须有明确的bool类型

- 我们来看下面name为null的判断
![image](http://ww1.sinaimg.cn/large/006BtgH3ly1ghyegzpz7kj30u005nmx7.jpg)

## 循环操作
基本的for循环

```
for (var i = 0; i < 5; i++) {
  print(i);
}
```
for in遍历List和Set类型

```
var names = ['why', 'kobe', 'curry'];
for (var name in names) {
  print(name);
}
```
while和do-while和其他语言一致

break和continue用法也是一致

## switch-case
普通的switch使用
- 注意：每一个case语句，默认情况下必须以一个break结尾

```

main(List<String> args) {
  var direction = 'east';
  switch (direction) {
    case 'east':
      print('东面');
      break;
    case 'south':
      print('南面');
      break;
    case 'west':
      print('西面');
      break;
    case 'north':
      print('北面');
      break;
    default:
      print('其他方向');
  }
}
```

# 类和对象
## 类的定义
在Dart中，定义类用class关键字。

类通常有两部分组成：成员（member）和方法（method）。

定义类的伪代码如下

```

class 类名 {
  类型 成员名;
  返回值类型 方法名(参数列表) {
    方法体
  }
}
```
编写一个简单的Person类：
- 这里有一个注意点: 我们在方法中使用属性(成员/实例变量)时，并没有加this；
- Dart的开发风格中，在方法中通常使用属性时，会省略this，但是有命名冲突时，this不能省略；

```
class Person {
  String name;

  eat() {
    print('$name在吃东西');
  }
}
```
我们来使用这个类，创建对应的对象：

- 注意：从Dart2开始，new关键字可以省略。

```

main(List<String> args) {
  // 1.创建类的对象
  var p = new Person(); // 直接使用Person()也可以创建

  // 2.给对象的属性赋值
  p.name = 'why';

  // 3.调用对象的方法
  p.eat();
}
```
## 构造方法
### 普通构造方法
我们知道, 当通过类创建一个对象时，会调用这个类的构造方法。

- 当类中没有明确指定构造方法时，将默认拥有一个无参的构造方法。
- 前面的Person中我们就是在调用这个构造方法.

我们也可以根据自己的需求，定义自己的构造方法:

**注意一**:当有了自己的构造方法时，默认的构造方法将会失效，不能使用

当然，你可能希望明确的写一个默认的构造方法，但是会和我们自定义的构造方法冲突；

这是因为Dart本身不支持函数的重载（名称相同, 参数不同的方式）。

**注意二**:这里我还实现了toString方法

```
class Person {
  String name;
  int age;

  Person(String name, int age) {
    this.name = name;
    this.age = age;
  }

  @override
  String toString() {
    return 'name=$name age=$age';
  }
}
```
另外，在实现构造方法时，通常做的事情就是通过**参数给属性**赋值

为了简化这一过程, Dart提供了一种更加简洁的语法糖形式.

上面的构造方法可以优化成下面的写法：

```
Person(String name, int age) {
    this.name = name;
    this.age = age;
  }
  // 等同于
  Person(this.name, this.age);
```
### 命名构造方法
但是在开发中, 我们确实希望实现更多的构造方法，怎么办呢？

- 因为不支持方法（函数）的重载，所以我们没办法创建相同名称的构造方法。

我们需要使用命名构造方法:

```
class Person {
  String name;
  int age;

  Person() {
    name = '';
    age = 0;
  }
	// 命名构造方法
  Person.withArgments(String name, int age) {
    this.name = name;
    this.age = age;
  }

  @override
  String toString() {
    return 'name=$name age=$age';
  }
}

// 创建对象
var p1 = new Person();
print(p1);
var p2 = new Person.withArgments('why', 18);
print(p2);
```
在之后的开发中, 我们也可以利用命名构造方法，提供更加便捷的创建对象方式:

- 比如开发中，我们需要经常将一个Map转成对象，可以提供如下的构造方法

```
// 新的构造方法
	Person.fromMap(Map<String, Object> map) {
    this.name = map['name'];
    this.age = map['age'];
  }

	// 通过上面的构造方法创建对象
  var p3 = new Person.fromMap({'name': 'kobe', 'age': 30});
  print(p3);
```
### 初始化列表
我们来重新定义一个类Point, 传入x/y，可以得到它们的距离distance:

```
class Point {
  final num x;
  final num y;
  final num distance;

  // 错误写法
  // Point(this.x, this.y) {
  //   distance = sqrt(x * x + y * y);
  // }

  // 正确的写法
  Point(this.x, this.y) : distance = sqrt(x * x + y * y);
}
```
上面这种初始化变量的方法, 我们称之为**初始化列表(Initializer list)**

### 重定向构造方法
在某些情况下, 我们希望在一个构造方法中去调用另外一个构造方法, 这个时候可以使用重定向构造方法：

- 在一个构造函数中，去调用另外一个构造函数（注意：是在冒号后面使用this调用）

```
class Person {
  String name;
  int age;

  Person(this.name, this.age);
  Person.fromName(String name) : this(name, 0);
}
```
### 常量构造方法
在某些情况下，传入相同值时，我们希望返回同一个对象，这个时候，可以使用常量构造方法.

默认情况下，创建对象时，即使传入相同的参数，创建出来的也不是同一个对象，看下面代码:

- 这里我们使用identical(对象1, 对象2)函数来判断两个对象是否是同一个对象:

```
main(List<String> args) {
  var p1 = Person('why');
  var p2 = Person('why');
  print(identical(p1, p2)); // false
}

class Person {
  String name;

  Person(this.name);
}
```
但是, 如果将构造方法前加const进行修饰，那么可以保证同一个参数，创建出来的对象是相同的

- 这样的构造方法就称之为常量构造方法。

```
main(List<String> args) {
  var p1 = const Person('why');
  var p2 = const Person('why');
  print(identical(p1, p2)); // true
}

class Person {
  final String name;

  const Person(this.name);
}
```
常量构造方法有一些注意点:

- 注意一：拥有常量构造方法的类中，所有的成员变量必须是final修饰的.
- 注意二: 为了可以通过常量构造方法，创建出相同的对象，不再使用 new关键字，而是使用const关键字
- 如果是将结果赋值给const修饰的标识符时，const可以省略.

### 工厂构造方法
Dart提供了factory关键字, 用于通过工厂去获取对象

```
main(List<String> args) {
  var p1 = Person('why');
  var p2 = Person('why');
  print(identical(p1, p2)); // true
}

class Person {
  String name;

  static final Map<String, Person> _cache = <String, Person>{};

  factory Person(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final p = Person._internal(name);
      _cache[name] = p;
      return p;
    }
  }

  Person._internal(this.name);
}
```

## setter和getter
默认情况下，Dart中类定义的属性是可以直接被外界访问的。

但是某些情况下，我们希望监控这个类的属性被访问的过程，这个时候就可以使用setter和getter了

```
main(List<String> args) {
  final d = Dog("黄色");
  d.setColor = "黑色";
  print(d.getColor);
}

class Dog {
  String color;

  String get getColor {
    return color;
  }
  set setColor(String color) {
    this.color = color;
  }

  Dog(this.color);
}
```
## 类的继承
面向对象的其中一大特性就是继承，继承不仅仅可以减少我们的代码量，也是多态的使用前提。

Dart中的继承使用extends关键字，子类中使用super来访问父类。

父类中的所有成员变量和方法都会被继承,，但是构造方法除外。

```
main(List<String> args) {
  var p = new Person();
  p.age = 18;
  p.run();
  print(p.age);
}

class Animal {
  int age;

  run() {
    print('在奔跑ing');
  }
}

class Person extends Animal {

}
```
子类可以拥有自己的成员变量, 并且可以对父类的方法进行重写：

```
class Person extends Animal {
  String name;

  @override
  run() {
    print('$name在奔跑ing');
  }
}
```

子类中可以调用父类的构造方法，对某些属性进行初始化：

- 子类的构造方法在执行前，将隐含调用父类的无参默认构造方法（没有参数且与类同名的构造方法）。
- 如果父类没有无参默认构造方法，则子类的构造方法必须在初始化列表中通过super显式调用父类的某个构造方法。

```
class Animal {
  int age;

  Animal(this.age);

  run() {
    print('在奔跑ing');
  }
}

class Person extends Animal {
  String name;

  Person(String name, int age) : name=name, super(age);

  @override
  run() {
    print('$name在奔跑ing');
  }

  @override
  String toString() {
    return 'name=$name, age=$age';
  }
}
```
## 抽象类
我们知道，继承是多态使用的前提。

所以在定义很多通用的**调用接口**时, 我们通常会让调用者传入父类，通过多态来实现更加灵活的调用方式。

但是，父类本身可能并不需要对某些方法进行具体的实现，所以父类中定义的方法,，我们可以定义为抽象方法。

什么是 抽象方法? 在Dart中没有具体实现的方法(没有方法体)，就是抽象方法。

- 抽象方法，必须存在于抽象类中。

- 抽象类是使用abstract声明的类。

下面的代码中, Shape类就是一个抽象类, 其中包含一个抽象方法.

```
abstract class Shape {
  getArea();
}

class Circle extends Shape {
  double r;

  Circle(this.r);

  @override
  getArea() {
    return r * r * 3.14;
  }
}

class Reactangle extends Shape {
  double w;
  double h;

  Reactangle(this.w, this.h);

  @override
  getArea() {
    return w * h;
  }
}
```
注意事项:

**注意一**:抽象类不能实例化.

**注意二**:抽象类中的抽象方法必须被子类实现, 抽象类中的已经被实现方法, 可以不被子类重写.

## 隐式接口
Dart中的接口比较特殊, 没有一个专门的关键字来声明接口.

默认情况下，定义的每个类都相当于默认也声明了一个接口，可以由其他的类来实现(因为Dart不支持多继承)

在开发中，我们通常将用于给别人实现的类声明为抽象类:

```
abstract class Runner {
  run();
}

abstract class Flyer {
  fly();
}

class SuperMan implements Runner, Flyer {
  @override
  run() {
    print('超人在奔跑');
  }

  @override
  fly() {
    print('超人在飞');
  }
}
```
## Mixin混入
在通过implements实现某个类时，类中所有的方法都必须被重新实现(无论这个类原来是否已经实现过该方法)。

但是某些情况下，一个类可能希望直接复用之前类的原有实现方案，怎么做呢?

- 使用继承吗？但是Dart只支持单继承，那么意味着你只能复用一个类的实现。

Dart提供了另外一种方案: Mixin混入的方式

- 除了可以通过class定义类之外，也可以通过mixin关键字来定义一个类。
- 只是通过mixin定义的类用于被其他类混入使用，通过with关键字来进行混入。

```

main(List<String> args) {
  var superMan = SuperMain();
  superMan.run();
  superMan.fly();
}

mixin Runner {
  run() {
    print('在奔跑');
  }
}

mixin Flyer {
  fly() {
    print('在飞翔');
  }
}

// implements的方式要求必须对其中的方法进行重新实现
// class SuperMan implements Runner, Flyer {}

class SuperMain with Runner, Flyer {

}
```
## 类成员和方法
前面我们在类中定义的成员和方法都属于对象级别的, 在开发中, 我们有时候也需要定义类级别的成员和方法

在Dart中我们使用static关键字来定义:

```
main(List<String> args) {
  var stu = Student();
  stu.name = 'why';
  stu.sno = 110;
  stu.study();

  Student.time = '早上8点';
  // stu.time = '早上9点'; 错误做法, 实例对象不能访问类成员  
 ‍‍‍Student.attendClass();  // stu.attendClass(); 错误做法, 实现对象不能‍访问类方法
}

class Student {
  String name;
  int sno;

  static String time;

  study() {
    print('$name在学习');
  }

  static attendClass() {
    print('去上课');
  }
}
```

## 枚举类型
枚举在开发中也非常常见, 枚举也是一种特殊的类, 通常用于表示固定数量的常量值。
### 枚举的定义
枚举使用enum关键字来进行定义:

```
main(List<String> args) {
  print(Colors.red);
}

enum Colors {
  red,
  green,
  blue
}
```
### 枚举的属性
枚举类型中有两个比较常见的属性:

- index: 用于表示每个枚举常量的索引, 从0开始.
- values: 包含每个枚举值的List.

```
main(List<String> args) {
  print(Colors.red.index);
  print(Colors.green.index);
  print(Colors.blue.index);

  print(Colors.values);
}

enum Colors {
  red,
  green,
  blue
}
```
枚举类型的注意事项:

- 注意一: 您不能子类化、混合或实现枚举。
- 注意二: 不能显式实例化一个枚举

# 泛型
## 为什么使用泛型?
对于有基础的同学, 这部分不再解释
## List和Map的泛型
List使用时的泛型写法:

```
 // 创建List的方式
  var names1 = ['why', 'kobe', 'james', 111];
  print(names1.runtimeType); // List<Object>

  // 限制类型
  var names2 = <String>['why', 'kobe', 'james', 111]; // 最后一个报错
  List<String> names3 = ['why', 'kobe', 'james', 111]; // 最后一个报错
```
Map使用时的泛型写法:

```
 // 创建Map的方式
  var infos1 = {1: 'one', 'name': 'why', 'age': 18}; 
  print(infos1.runtimeType); // _InternalLinkedHashMap<Object, Object>

  // 对类型进行显示
  Map<String, String> infos2 = {'name': 'why', 'age': 18}; // 18不能放在value中
  var infos3 = <String, String>{'name': 'why', 'age': 18}; // 18不能放在value中
```
## 类定义的泛型
如果我们需要定义一个类, 用于存储位置信息Location, 但是并不确定使用者希望使用的是int类型,还是double类型,  甚至是一个字符串, 这个时候如何定义呢?
- 一种方案是使用Object类型, 但是在之后使用时, 非常不方便
- 另一种方案就是使用泛型.

Location类的定义: Object方式

```
main(List<String> args) {
  Location l1 = Location(10, 20);
  print(l1.x.runtimeType); // Object
}

class Location {
  Object x;
  Object y;

  Location(this.x, this.y);
}
```
Location类的定义: 泛型方式

```

main(List<String> args) {
  Location l2 = Location<int>(10, 20);
  print(l2.x.runtimeType); // int 

  Location l3 = Location<String>('aaa', 'bbb');
  print(l3.x.runtimeType); // String
}
}

class Location<T> {
  T x;
  T y;
  Location(this.x, this.y);
}
```
如果我们希望类型只能是num类型, 怎么做呢?

```

main(List<String> args) {
  Location l2 = Location<int>(10, 20);
  print(l2.x.runtimeType);
	
  // 错误的写法, 类型必须继承自num
  Location l3 = Location<String>('aaa', 'bbb');
  print(l3.x.runtimeType);
}

class Location<T extends num> {
  T x;
  T y;
  Location(this.x, this.y);
}
```
## 泛型方法的定义
最初，Dart仅仅在类中支持泛型。后来一种称为泛型方法的新语法允许在方法和函数中使用类型参数。

```

main(List<String> args) {
  var names = ['why', 'kobe'];
  var first = getFirst(names);
  print('$first ${first.runtimeType}'); // why String
}

T getFirst<T>(List<T> ts) {
  return ts[0];
}
```

# dart库使用
在Dart中，你可以导入一个库来使用它所提供的功能。

库的使用可以使代码的重用性得到提高，并且可以更好的组合代码。

Dart中任何一个dart文件都是一个库，即使你没有用关键字library声明

## 库的导入
import语句用来导入一个库，后面跟一个字符串形式的Uri来指定表示要引用的库，语法如下：

```
import '库所在的uri';
```
常见的库URI有三种不同的形式
- 来自dart标准版，比如dart:io、dart:html、dart:math、dart:core(但是这个可以省略)
```
//dart:前缀表示Dart的标准库，如dart:io、dart:html、dart:math
import 'dart:io';
```
- 使用相对路径导入的库，通常指自己项目中定义的其他dart文件
```
//当然，你也可以用相对路径或绝对路径的dart文件来引用
import 'lib/student/student.dart';
```
Pub包管理工具管理的一些库，包括自己的配置以及一些第三方的库，通常使用前缀package
```
//Pub包管理系统中有很多功能强大、实用的库，可以使用前缀 package:
import 'package:flutter/material.dart';
```

**库文件中内容的显示和隐藏**

如果希望只导入库中某些内容，或者刻意隐藏库里面某些内容，可以使用show和hide关键字

- **show关键字**:可以显示某个成员（屏蔽其他）
- **hide关键字**:可以隐藏某个成员（显示其他）
```
import 'lib/student/student.dart' show Student, Person;

import 'lib/student/student.dart' hide Person;
库中内容和当前文件中的名字冲突
```
**当各个库有命名冲突的时候，可以使用as关键字来使用命名空间**

```
import 'lib/student/student.dart' as Stu;

Stu.Student s = new Stu.Student();
```

## 库的定义
**library关键字**

通常在定义库时，我们可以使用library关键字给库起一个名字。

但目前我发现，库的名字并不影响导入，因为import语句用的是字符串URI

```
library math;
```
**part关键字**
在之前我们使用student.dart作为演练的时候，只是将该文件作为一个库。

在开发中，如果一个库文件太大，将所有内容保存到一个文件夹是不太合理的，我们有可能希望将这个库进行拆分，这个时候就可以使用part关键字了

不过官方已经不建议使用这种方式了：

https://dart.dev/guides/libraries/create-library-packages

>mathUtils.dart文件

```
part of "utils.dart";

int sum(int num1, int num2) {
  return num1 + num2;
}
```

> dateUtils.dart文件

```
part of "utils.dart";

String dateFormat(DateTime date) {
  return "2020-12-12";
}
```

> utils.dart文件

```
part "mathUtils.dart";
part "dateUtils.dart";
```
> test_libary.dart文件

```
import "lib/utils.dart";

main(List<String> args) {
  print(sum(10, 20));
  print(dateFormat(DateTime.now()));
}
```
**export关键字**

官方不推荐使用part关键字，那如果库非常大，如何进行管理呢？

将每一个dart文件作为库文件，使用export关键字在某个库文件中单独导入

> mathUtils.dart文件


```
int sum(int num1, int num2) {
  return num1 + num2;
}
```
> dateUtils.dart文件
```
String dateFormat(DateTime date) {
  return "2020-12-12";
}
```

> utils.dart文件

```
library utils;
export "mathUtils.dart";
export "dateUtils.dart";
```
> test_libary.dart文件

```
import "lib/utils.dart";

main(List<String> args) {
  print(sum(10, 20));
  print(dateFormat(DateTime.now()));
}
```
最后，也可以通过Pub管理自己的库自己的库，在项目开发中个人觉得不是非常有必要，所以暂时不讲解这种方式。


在写这篇文章之前，我一直在犹豫，要不要在这里讲解Dart的异步相关话题，因为这部分内容很容易让初学者望而却步：

1、关于单线程和异步之间的关系，比较容易让人迷惑，不过我一定会用自己的方式尽可能让你听懂。

2、大量的异步操作方式（Future、await、async等），目前你看不到具体的应用场景。（比如你学习过前端中的Promise、await、async可能会比较简单，但是我会假设你没有这样的基础）。

不过，听我说：如果这一个章节你学完之后还有很多疑惑，没有关系，在后面用到相关知识时，回头来看，你会豁然开朗。

# Dart的异步模型

## Dart是单线程的
### 程序中的耗时操作
**开发中的耗时操作：**
- 在开发中，我们经常会遇到一些耗时的操作需要完成，比如网络请求、文件读取等等；
- 如果我们的主线程一直在等待这些耗时的操作完成，那么就会进行阻塞，无法响应其它事件，比如用户的点击；
- 显然，我们不能这么干！！

**如何处理耗时的操作呢？**
- 针对如何处理耗时的操作，不同的语言有不同的处理方式。
- 处理方式一： 多线程，比如Java、C++，我们普遍的做法是开启一个新的线程（Thread），在新的线程中完成这些异步的操作，再通过线程间通信的方式，将拿到的数据传递给主线程。
- 处理方式二： 单线程+事件循环，比如JavaScript、Dart都是基于单线程加事件循环来完成耗时操作的处理。不过单线程如何能进行耗时的操作呢？！

### 单线程的异步操作
我之前碰到很多开发者都对单线程的异步操作充满了问号？？？
其实它们并不冲突：
- 因为我们的一个应用程序大部分时间都是处于空闲的状态的，并不是无限制的在和用户进行交互。
- 比如等待用户点击、网络请求数据的返回、文件读写的IO操作，这些等待的行为并不会阻塞我们的线程；
- 这是因为类似于网络请求、文件读写的IO，我们都可以基于非阻塞调用；

**阻塞式调用和非阻塞式调用**

- 如果想搞懂这个点，我们需要知道操作系统中的阻塞式调用和非阻塞式调用的概念。
- 阻塞和非阻塞关注的是程序在等待调用结果（消息，返回值）时的状态。
- 阻塞式调用： 调用结果返回之前，当前线程会被挂起，调用线程只有在得到调用结果之后才会继续执行。
- 非阻塞式调用： 调用执行之后，当前线程不会停止执行，只需要过一段时间来检查一下有没有结果返回即可。

我们用一个生活中的例子来模拟：

- 你中午饿了，需要点一份外卖，点外卖的动作就是我们的调用，拿到最后点的外卖就是我们要等待的结果。
- 阻塞式调用： 点了外卖，不再做任何事情，就是在傻傻的等待，你的线程停止了任何其他的工作。
- 非阻塞式调用： 点了外卖，继续做其他事情：继续工作、打把游戏，你的线程没有继续执行其他事情，只需要偶尔去看一下有没有人敲门，外卖有没有送到即可。

而我们开发中的很多耗时操作，都可以基于这样的 非阻塞式调用：

- 比如网络请求本身使用了Socket通信，而Socket本身提供了select模型，可以进行非阻塞方式的工作；
- 比如文件读写的IO操作，我们可以使用操作系统提供的基于事件的回调机制；
- 这些操作都不会阻塞我们单线程的继续执行，我们的线程在等待的过程中可以继续去做别的事情：喝杯咖啡、打把游戏，等真正有了响应，再去进行对应的处理即可。

这时，我们可能有两个问题：

- 问题一： 如果在多核CPU中，单线程是不是就没有充分利用CPU呢？这个问题，我会放在后面来讲解。
- 问题二： 单线程是如何来处理网络通信、IO操作它们返回的结果呢？答案就是事件循环（Event Loop

###  什么是事件循环
单线程模型中主要就是在维护着一个事件循环（Event Loop）。

事件循环是什么呢？

- 事实上事件循环并不复杂，它就是将需要处理的一系列事件（包括点击事件、IO事件、网络事件）放在一个事件队列（Event Queue）中。
- 不断的从事件队列（Event Queue）中取出事件，并执行其对应需要执行的代码块，直到事件队列清空位置。

我们来写一个事件循环的伪代码：

```
// 这里我使用数组模拟队列, 先进先出的原则
List eventQueue = []; 
var event;

// 事件循环从启动的一刻，永远在执行
while (true) {
  if (eventQueue.length > 0) {
    // 取出一个事件
    event = eventQueue.removeAt(0);
    // 执行该事件
    event();
  }
}

```
当我们有一些事件时，比如点击事件、IO事件、网络事件时，它们就会被加入到eventLoop中，当发现事件队列不为空时发现，就会取出事件，并且执行。
- 齿轮就是我们的事件循环，它会从队列中一次取出事件来执行。

## 事件循环代码模拟
这里我们来看一段伪代码，理解点击事件和网络请求的事件是如何被执行的：

- 这是一段Flutter代码，很多东西大家可能不是特别理解，但是耐心阅读你会读懂我们在做什么。
- 一个按钮RaisedButton，当发生点击时执行onPressed函数。
- onPressed函数中，我们发送了一个网络请求，请求成功后会执行then中的回调函数。

```
RaisedButton(
  child: Text('Click me'),
  onPressed: () {
    final myFuture = http.get('https://example.com');
    myFuture.then((response) {
      if (response.statusCode == 200) {
        print('Success!');
      }
    });
  },
)
```
这些代码是如何放在事件循环中执行呢？

1. 当用户发生点击的时候，onPressed回调函数被放入事件循环中执行，执行的过程中发送了一个网络请求。
2. 网络请求发出去后，该事件循环不会被阻塞，而是发现要执行的onPressed函数已经结束，会将它丢弃掉。
3. 网络请求成功后，会执行then中传入的回调函数，这也是一个事件，该事件被放入到事件循环中执行，执行完毕后，事件循环将其丢弃。

尽管onPressed和then中的回调有一些差异，但是它们对于事件循环来说，都是告诉它：我有一段代码需要执行，快点帮我完成。

# Dart的异步操作

>Dart中的异步操作主要使用Future以及async、await。
如果你之前有过前端的ES6、ES7编程经验，那么完全可以将Future理解成Promise，async、await和ES7中基本一致。
但是如果没有前端开发经验，Future以及async、await如何理解呢？

## 认识Future
> 我思考了很久，这个Future到底应该如何讲解
### 同步的网络请求
我们先来看一个例子吧：

- 在这个例子中，我使用getNetworkData来模拟了一个网络请求；
- 该网络请求需要3秒钟的时间，之后返回数据；

```
import "dart:io";

main(List<String> args) {
  print("main function start");
  print(getNetworkData());
  print("main function end");
}

String getNetworkData() {
  sleep(Duration(seconds: 3));
  return "network data";
}
```
这段代码会运行怎么的结果呢？

- getNetworkData会阻塞main函数的执行

```
main function start
// 等待3秒
network data
main function end
```
显然，上面的代码不是我们想要的执行效果，因为网络请求阻塞了main函数，那么意味着其后所有的代码都无法正常的继续执行。

## 异步的网络请求
我们来对我们上面的代码进行改进，代码如下：

- 和刚才的代码唯一的区别在于我使用了Future对象来将耗时的操作放在了其中传入的函数中；
- 稍后，我们会讲解它具体的一些API，我们就暂时知道我创建了一个Future实例即可；

```
import "dart:io";

main(List<String> args) {
  print("main function start");
  print(getNetworkData());
  print("main function end");
}

Future<String> getNetworkData() {
  return Future<String>(() {
    sleep(Duration(seconds: 3));
    return "network data";
  });
}
```

我们来看一下代码的运行结果：

- 1、这一次的代码顺序执行，没有出现任何的阻塞现象；
- 2、和之前直接打印结果不同，这次我们打印了一个Future实例；
- 结论：我们将一个耗时的操作隔离了起来，这个操作不会再影响我们的主线程执行了。
- 问题：我们如何去拿到最终的结果呢？

```
main function start
Instance of 'Future<String>'
main function end
```
获取Future得到的结果
有了Future之后，如何去获取请求到的结果：通过.then的回调：

```
main(List<String> args) {
  print("main function start");
  // 使用变量接收getNetworkData返回的future
  var future = getNetworkData();
  // 当future实例有返回结果时，会自动回调then中传入的函数
  // 该函数会被放入到事件循环中，被执行
  future.then((value) {
    print(value);
  });
  print(future);
  print("main function end");
}
```
上面代码的执行结果：

```
main function start
Instance of 'Future<String>'
main function end
// 3s后执行下面的代码
network data
```
执行中出现异常

如果调用过程中出现了异常，拿不到结果，如何获取到异常的信息呢？

```
import "dart:io";

main(List<String> args) {
  print("main function start");
  var future = getNetworkData();
  future.then((value) {
    print(value);
  }).catchError((error) { // 捕获出现异常时的情况
    print(error);
  });
  print(future);
  print("main function end");
}

Future<String> getNetworkData() {
  return Future<String>(() {
    sleep(Duration(seconds: 3));
    // 不再返回结果，而是出现异常
    // return "network data";
    throw Exception("网络请求出现错误");
  });
}
```
上面代码的执行结果：

```
main function start
Instance of 'Future<String>'
main function end
// 3s后没有拿到结果，但是我们捕获到了异常
Exception: 网络请求出现错误
```
###  Future使用补充
#### 补充一：上面案例的小结

我们通过一个案例来学习了一些Future的使用过程：

1、创建一个Future（可能是我们创建的，也可能是调用内部API或者第三方API获取到的一个Future，总之你需要获取到一个Future实例，Future通常会对一些异步的操作进行封装）；

2、通过.then(成功回调函数)的方式来监听Future内部执行完成时获取到的结果；

3、通过.catchError(失败或异常回调函数)的方式来监听Future内部执行失败或者出现异常时的错误信息；

#### 补充二：Future的两种状态

事实上Future在执行的整个过程中，我们通常把它划分成了两种状态：

状态一：未完成状态（uncompleted）

执行Future内部的操作时（在上面的案例中就是具体的网络请求过程，我们使用了延迟来模拟），我们称这个过程为未完成状态

状态二：完成状态（completed）

当Future内部的操作执行完成，通常会返回一个值，或者抛出一个异常。

这两种情况，我们都称Future为完成状态。

Dart官网有对这两种状态解析，之所以贴出来是区别于Promise的三种状态

#### 补充三：Future的链式调用

上面代码我们可以进行如下的改进：

我们可以在then中继续返回值，会在下一个链式的then调用回调函数中拿到返回的结果

```

import "dart:io";

main(List<String> args) {
  print("main function start");

  getNetworkData().then((value1) {
    print(value1);
    return "content data2";
  }).then((value2) {
    print(value2);
    return "message data3";
  }).then((value3) {
    print(value3);
  });

  print("main function end");
}

Future<String> getNetworkData() {
  return Future<String>(() {
    sleep(Duration(seconds: 3));
    // 不再返回结果，而是出现异常
     return "network data1";
  });
}
```
打印结果如下：

```
main function start
main function end
// 3s后拿到结果
network data1
content data2
message data3
```
#### 补充四：Future其他API
Future.value(value)

- 直接获取一个完成的Future，该Future会直接调用then的回调函数

```
main(List<String> args) {
  print("main function start");

  Future.value("哈哈哈").then((value) {
    print(value);
  });

  print("main function end");
}
```
打印结果如下：

```
main function start
main function end
哈哈哈
```
疑惑：为什么立即执行，但是哈哈哈是在最后打印的呢？

- 这是因为Future中的then会作为新的任务会加入到事件队列中（Event Queue），加入之后你肯定需要排队执行了

Future.error(object)

- 直接获取一个完成的Future，但是是一个发生异常的Future，该Future会直接调用catchError的回调函数

```
main(List<String> args) {
  print("main function start");

  Future.error(Exception("错误信息")).catchError((error) {
    print(error);
  });

  print("main function end");
}
```
打印结果如下：

```

main function start
main function end
Exception: 错误信息
```
Future.delayed(时间, 回调函数)

- 在延迟一定时间时执行回调函数，执行完回调函数后会执行then的回调；
- 之前的案例，我们也可以使用它来模拟，但是直接学习这个API会让大家更加疑惑；

```
main(List<String> args) {
  print("main function start");

  Future.delayed(Duration(seconds: 3), () {
    return "3秒后的信息";
  }).then((value) {
    print(value);
  });

  print("main function end");
}
```
## await、async
### 理论概念理解
如果你已经完全搞懂了Future，那么学习await、async应该没有什么难度。

await、async是什么呢？

- 它们是Dart中的关键字（你这不是废话吗？废话也还是要强调的，万一你用它做变量名呢，无辜脸。）
- 它们可以让我们用同步的代码格式，去实现异步的调用过程。
- 并且，通常一个async的函数会返回一个Future（别着急，马上就看到代码了）。

我们已经知道，Future可以做到不阻塞我们的线程，让线程继续执行，并且在完成某个操作时改变自己的状态，并且回调then或者errorCatch回调。

如何生成一个Future呢？

- 1、通过我们前面学习的Future构造函数，或者后面学习的Future其他API都可以。

- 2、还有一种就是通过async的函数。

### 案例代码演练
我们来对之前的Future异步处理代码进行改造，改成await、async的形式。

我们知道，如果直接这样写代码，代码是不能正常执行的：

- 因为Future.delayed返回的是一个Future对象，我们不能把它看成同步的返回数据："network data"去使用
- 也就是我们不能把这个异步的代码当做同步一样去使用！

```
import "dart:io";

main(List<String> args) {
  print("main function start");
  print(getNetworkData());
  print("main function end");
}

String getNetworkData() {
  var result = Future.delayed(Duration(seconds: 3), () {
    return "network data";
  });

  return  "请求到的数据：" + result;
}
```
现在我使用await修改下面这句代码：

- 你会发现，我在Future.delayed函数前加了一个await。
- 一旦有了这个关键字，那么这个操作就会等待Future.delayed的执行完毕，并且等待它的结果。

```
String getNetworkData() {
  var result = await Future.delayed(Duration(seconds: 3), () {
    return "network data";
  });

  return  "请求到的数据：" + result;
}
```
修改后执行代码，会看到如下的错误：

- 错误非常明显：await关键字必须存在于async函数中。
- 所以我们需要将getNetworkData函数定义成async函数。

继续修改代码如下：

- 也非常简单，只需要在函数的()后面加上一个async关键字就可以了

```
String getNetworkData() async {
  var result = await Future.delayed(Duration(seconds: 3), () {
    return "network data";
  });

  return  "请求到的数据：" + result;
}
```
运行代码，依然报错（心想：你妹啊）：

- 错误非常明显：使用async标记的函数，必须返回一个Future对象。
- 所以我们需要继续修改代码，将返回值写成一个Future。

继续修改代码如下：

```
Future<String> getNetworkData() async {
  var result = await Future.delayed(Duration(seconds: 3), () {
    return "network data";
  });

  return "请求到的数据：" + result;
}
```
这段代码应该是我们理想当中执行的代码了

- 我们现在可以像同步代码一样去使用Future异步返回的结果；
- 等待拿到结果之后和其他数据进行拼接，然后一起返回；
- 返回的时候并不需要包装一个Future，直接返回即可，但是返回值会默认被包装在一个Future中；

## 读取json案例
> 我这里给出了一个在Flutter项目中，读取一个本地的json文件，并且转换成模型对象，返回出去的案例；
这个案例作为大家学习前面Future和await、async的一个参考，我并不打算展开来讲，因为需要用到Flutter的相关知识；
后面我会在后面的案例中再次讲解它在Flutter中我使用的过程中；

读取json案例代码（了解一下即可）

```
import 'package:flutter/services.dart' show rootBundle;
import 'dart:convert';
import 'dart:async';

main(List<String> args) {
  getAnchors().then((anchors) {
    print(anchors);
  });
}

class Anchor {
  String nickname;
  String roomName;
  String imageUrl;

  Anchor({
    this.nickname,
    this.roomName,
    this.imageUrl
  });

  Anchor.withMap(Map<String, dynamic> parsedMap) {
    this.nickname = parsedMap["nickname"];
    this.roomName = parsedMap["roomName"];
    this.imageUrl = parsedMap["roomSrc"];
  }
}

Future<List<Anchor>> getAnchors() async {
  // 1.读取json文件
  String jsonString = await rootBundle.loadString("assets/yz.json");

  // 2.转成List或Map类型
  final jsonResult = json.decode(jsonString);

  // 3.遍历List，并且转成Anchor对象放到另一个List中
  List<Anchor> anchors = new List();
  for (Map<String, dynamic> map in jsonResult) {
    anchors.add(Anchor.withMap(map));
  }
  return anchors;
}
```

# Dart的异步补充

## 任务执行顺序
### 认识微任务队列

在前面学习学习中，我们知道Dart中有一个事件循环（Event Loop）来执行我们的代码，里面存在一个事件队列（Event Queue），事件循环不断从事件队列中取出事件执行。

但是如果我们严格来划分的话，在Dart中还存在另一个队列：微任务队列（Microtask Queue）。

微任务队列的优先级要高于事件队列；

也就是说事件循环都是优先执行微任务队列中的任务，再执行 事件队列 中的任务；

那么在Flutter开发中，哪些是放在事件队列，哪些是放在微任务队列呢？

所有的外部事件任务都在事件队列中，如IO、计时器、点击、以及绘制事件等；

而微任务通常来源于Dart内部，并且微任务非常少。这是因为如果微任务非常多，就会造成事件队列排不上队，会阻塞任务队列的执行（比如用户点击没有反应的情况）；

说道这里，你可能已经有点凌乱了，在Dart的单线程中，代码到底是怎样执行的呢？

1、Dart的入口是main函数，所以main函数中的代码会优先执行；

2、main函数执行完后，会启动一个事件循环（Event Loop）就会启动，启动后开始执行队列中的任务；

3、首先，会按照先进先出的顺序，执行 微任务队列（Microtask Queue）中的所有任务；

4、其次，会按照先进先出的顺序，执行 事件队列（Event Queue）中的所有任务；

![image](http://ww1.sinaimg.cn/large/006BtgH3ly1gi7wp4em0qj30d30e2q30.jpg)

### 如何创建微任务
在开发中，我们可以通过dart中async下的scheduleMicrotask来创建一个微任务：

```
import "dart:async";

main(List<String> args) {
  scheduleMicrotask(() {
    print("Hello Microtask");
  });
}
```
在开发中，如果我们有一个任务不希望它放在Event Queue中依次排队，那么就可以创建一个微任务了。

Future的代码是加入到事件队列还是微任务队列呢？

Future中通常有两个函数执行体：

Future构造函数传入的函数体

then的函数体（catchError等同看待）

那么它们是加入到什么队列中的呢？

Future构造函数传入的函数体放在事件队列中

then的函数体要分成三种情况：

情况一：Future没有执行完成（有任务需要执行），那么then会直接被添加到Future的函数执行体后；

情况二：如果Future执行完后就then，该then的函数体被放到如微任务队列，当前Future执行完后执行微任务队列；

情况三：如果Future是链式调用，意味着then未执行完，下一个then不会执行；


```
// future_1加入到eventqueue中，紧随其后then_1被加入到eventqueue中
Future(() => print("future_1")).then((_) => print("then_1"));

// Future没有函数执行体，then_2被加入到microtaskqueue中
Future(() => null).then((_) => print("then_2"));

// future_3、then_3_a、then_3_b依次加入到eventqueue中
Future(() => print("future_3")).then((_) => print("then_3_a")).then((_) => print("then_3_b"));
```
### 代码执行顺序
我们根据前面的规则来学习一个终极的代码执行顺序案例：

```
import "dart:async";

main(List<String> args) {
  print("main start");

  Future(() => print("task1"));
	
  final future = Future(() => null);

  Future(() => print("task2")).then((_) {
    print("task3");
    scheduleMicrotask(() => print('task4'));
  }).then((_) => print("task5"));

  future.then((_) => print("task6"));
  scheduleMicrotask(() => print('task7'));

  Future(() => print('task8'))
    .then((_) => Future(() => print('task9')))
    .then((_) => print('task10'));

  print("main end");
}
```
代码执行的结果是：

```
main start
main end
task7
task1
task6
task2
task3
task5
task4
task8
task9
task10
```
代码分析：

1、main函数先执行，所以main start和main end先执行，没有任何问题；

2、main函数执行过程中，会将一些任务分别加入到EventQueue和MicrotaskQueue中；

3、task7通过scheduleMicrotask函数调用，所以它被最早加入到MicrotaskQueue，会被先执行；

4、然后开始执行EventQueue，task1被添加到EventQueue中被执行；

5、通过final future = Future(() => null);创建的future的then被添加到微任务中，微任务直接被优先执行，所以会执行task6；

6、一次在EventQueue中添加task2、task3、task5被执行；

7、task3的打印执行完后，调用scheduleMicrotask，那么在执行完这次的EventQueue后会执行，所以在task5后执行task4（注意：scheduleMicrotask的调用是作为task3的一部分代码，所以task4是要在task5之后执行的）

8、task8、task9、task10一次添加到EventQueue被执行；

事实上，上面的代码执行顺序有可能出现在面试中，我们开发中通常不会出现这种复杂的嵌套，并且需要完全搞清楚它的执行顺序；

但是，了解上面的代码执行顺序，会让你对EventQueue和microtaskQueue有更加深刻的理解。

## 多核CPU的利用

### Isolate的理解
在Dart中，有一个Isolate的概念，它是什么呢？

我们已经知道Dart是单线程的，这个线程有自己可以访问的内存空间以及需要运行的事件循环；

我们可以将这个空间系统称之为是一个Isolate；

比如Flutter中就有一个Root Isolate，负责运行Flutter的代码，比如UI渲染、用户交互等等；

在 Isolate 中，资源隔离做得非常好，每个 Isolate 都有自己的 Event Loop 与 Queue，

Isolate 之间不共享任何资源，只能依靠消息机制通信，因此也就没有资源抢占问题。

但是，如果只有一个Isolate，那么意味着我们只能永远利用一个线程，这对于多核CPU来说，是一种资源的浪费。

如果在开发中，我们有非常多耗时的计算，完全可以自己创建Isolate，在独立的Isolate中完成想要的计算操作。

**如何创建Isolate呢？**

创建Isolate是比较简单的，我们通过Isolate.spawn就可以创建了

```
import "dart:isolate";

main(List<String> args) {
  Isolate.spawn(foo, "Hello Isolate");
}

void foo(info) {
  print("新的isolate：$info");
}

```
### Isolate通信机制
但是在真实开发中，我们不会只是简单的开启一个新的Isolate，而不关心它的运行结果：

- 我们需要新的Isolate进行计算，并且将计算结果告知Main Isolate（也就是默认开启的Isolate）；
- Isolate 通过发送管道（SendPort）实现消息通信机制；
- 我们可以在启动并发Isolate时将Main Isolate的发送管道作为参数传递给它；
- 并发在执行完毕时，可以利用这个管道给Main Isolate发送消息；

```
import "dart:isolate";

main(List<String> args) async {
  // 1.创建管道
  ReceivePort receivePort= ReceivePort();

  // 2.创建新的Isolate
  Isolate isolate = await Isolate.spawn<SendPort>(foo, receivePort.sendPort);

  // 3.监听管道消息
  receivePort.listen((data) {
    print('Data：$data');
    // 不再使用时，我们会关闭管道
    receivePort.close();
    // 需要将isolate杀死
    isolate?.kill(priority: Isolate.immediate);
  });
}

void foo(SendPort sendPort) {
  sendPort.send("Hello World");
}
```
但是我们上面的通信变成了单向通信，如果需要双向通信呢？

- 事实上双向通信的代码会比较麻烦；
- Flutter提供了支持并发计算的compute函数，它内部封装了Isolate的创建和双向通信；
- 利用它我们可以充分利用多核心CPU，并且使用起来也非常简单；

注意：下面的代码不是dart的API，而是Flutter的API，所以只有在Flutter项目中才能运行

```
main(List<String> args) async {
  int result = await compute(powerNum, 5);
  print(result);
}

int powerNum(int num) {
  return num * num;
}
```
