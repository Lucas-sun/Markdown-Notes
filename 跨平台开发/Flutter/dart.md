# Flutter

## Dart 语法

Dart由谷歌开发，它可以被用于web、服务器、移动应用和物联网等领域开发。

诞生于2011年，号称要取代JavaScript，但一直不温不火，直到Flutter的出现才被人们重新重视。

**要学习Flutter必须学会Dart**

[官网：https://dart.dev/](https://dart.dev/)

### 环境搭建

[推荐方式]

- windows：进入官网，或者直接点击[下载地址](https://dart.dev/get-dart#install)
- macos：跟随官网安装说明安装，利用homebrew安装

### 基础语法

> [!NOTE]
>
> dart程序文件是以.dart结尾的

```dart
// dart程序入口
main() {
  print("Hello, World!");
}

// 依旧是入口函数，只是表示main函数没有返回值
void main() {
  print("Hello, World!");
}

/*也可以这样注释，和C语法很像*/
```

```dart
/*
dart是一个强大的脚本类语言，可以不预设变量类型，自动会进行类型倒推
dart中定义变量可以通过var关键字来声明变量
*/

// 变量声明
var name = "Lucas";
int age = 25;
double height = 1.75;
bool isMarried = true;
String occupation = "Software Engineer";
List hobbies = ["reading", "swimming", "traveling"];
List<String> fruits = ["apple", "banana", "orange"];
var numbers = <int>[1, 2, 3, 4, 5];
var kong = List.filled(5, 0); // 声明一个固定长度为5的列表，元素都是0，无法增加长度
Map person = {"name": "Lucas", "age": 25, "height": 1.75};
var map1 = new Map(); // 声明一个空的Map

// 常量声明
const PI = 3.1415926; // const修饰的变量只能赋值一次，不能修改，必须声明时就赋值，只能定义基本数据类型
final URL;
URL = "https://www.google.com"; // final修饰的变量只能赋值一次，不能修改，可以声明时不赋值，可以定义变量的内容

// 条件语句
if (age >= 18) {
  print("You are old enough to vote.");
} else {
  print("You are not old enough to vote.");
}

// 循环语句
for (int i = 0; i < 5; i++) {
  print(i);
}

// 函数声明
void sayHello() {
  print("Hello, World!");
}

// 调用函数
sayHello();

/*
dart命名规则：
1.变量名必须由数字、字母、下划线和美元符($)组成。
2.变量名不能以数字开头。
3.变量名不能是保留字和关键字。
4.变量名区分大小写
*/
```

```dart
// 字符串拼接
String name = "Lucas";
String message = "Hello, " + name + "!";
print(message);
print("Hello, $name!");

// List操作
List numbers = [1, 2, 3, 4, 5];
numbers.add(6); // 添加元素
numbers.remove(3); // 删除元素
numbers.insert(1, 10); // 插入元素
numbers.clear(); // 清空列表
numbers.length; // 列表长度
numbers[0]; // 访问第一个元素
numbers.sort(); // 排序
print(numbers);

// Map操作
Map person = {"name": "Lucas", "age": 25, "height": 1.75};
person["occupation"] = "Software Engineer"; // 添加元素
person.remove("age"); // 删除元素
person.containsKey("name"); // 判断是否存在key
person.values; // 获取所有值
person.keys; // 获取所有key
print(person);
```

```dart
// 判断数据类型，使用is关键字
if (name is String) {
  print("name is a String");
}else if (name is int) {
  print("name is a int");
}else if (name is double) {
  print("name is a double");
}else if (name is bool) {
  print("name is a bool");
}else if (name is List) {
  print("name is a List");
}else if (name is Map) {
  print("name is a Map");
}else {
  print("name is a unknown type");
}
```

```dart
// 算数运算符
int a = 10;
int b = 5;
print(a + b); // 15
print(a - b); // 5  
print(a * b); // 50
print(a / b); // 2
print(a % b); // 0
print(a ~/ b); // 2 取整
print(a < b); // false
print(a > b); // true
print(a == b); // false
print(a != b); // true
// 逻辑运算符
bool isMarried = true;
bool isSingle = false;
print(isMarried && isSingle); // false
print(isMarried || isSingle); // true
print(!isMarried); // false

b??= 0; // 如果b为null，则赋值为0
```

```dart
// 条件表达式
int a = 10;
switch (a) {
  case 10:
    print("a is 10");
    break;
  case 5:
    print("a is 5");
    break;
  default:
    print("a is not 10 or 5");
}

// 三目运算符
int a = 10;
int b = 5;
int max = a > b ? a : b;
print(max); // 10

// ??运算符
var a = null;
var b = 0;
var c = a ?? b; // 如果a为null，则返回b，否则返回a
print(c); // 0

// 类型转换
String str = "123";
int num = int.parse(str); // 字符串转int
double dbl = double.parse(str); // 字符串转double
String str2 = num.toString(); // int转字符串
```
```dart
// 捕获异常
try {
  throw Exception("Something went wrong");
} catch (e) {
  print(e);
} finally {
  print("This block always executes");
}
```

```dart
// for
for (int i = 0; i < 5; i++) {
  print(i);
}

// while
int i = 0;
while (i < 5) {
  print(i);
  i++;
}

// do-while
int i = 0;
do {
  print(i);
  i++;
} while (i < 5);
```

```dart
// List常用属性和方法
List numbers = [1, 2, 3, 4, 5];
numbers.length; // 5
numbers.isEmpty; // false
numbers.isNotEmpty; // true
numbers.first; // 1
numbers.last; // 5
numbers.reversed; // [5, 4, 3, 2, 1]
numbers.add(6); // [1, 2, 3, 4, 5, 6]
numbers.addAll([7, 8, 9]); // [1, 2, 3, 4, 5, 6, 7, 8, 9]
numbers.remove(3); // [1, 2, 4, 5, 6]
numbers.insert(1, 10); // [1, 10, 2, 4, 5, 6]
numbers.removeWhere((num) => num > 3); // [1, 10, 2, 4]
numbers.removeLast(); // [1, 10, 2, 4]
numbers.removeAt(1); // [1, 2, 4]
numbers.clear(); // []
numbers.contains(3); // true
numbers.indexOf(3); // 2
numbers.toSet(); // {1, 2, 3, 4, 5}
numbers.join(","); // "1,2,3,4,5"

/*箭头函数左边是参数，右边是返回值*/
numbers.map((num) => num * 2).toList(); // [2, 4, 6, 8, 10]
numbers.where((num) => num > 3).toList(); // [4, 5]
numbers.any((num) => num > 3); // true
numbers.every((num) => num > 0); // true
numbers.fold(0, (prev, curr) => prev + curr); // 15
numbers.forEach((num) => print(num)); // 1 2 3 4 5
var newNumbers = numbers.map((num){ return num * 2; }).toList(); // [2, 4, 6, 8, 10]
```

```dart
// 函数
void sayHello() {
  print("Hello, World!");
}

sayHello();

// 带参数的函数
void sayHello(String name) {
  print("Hello, $name!");
}

sayHello("Lucas");

// 可选参数
void sayHello(String name, String greeting = "Hello") {
  print("$greeting, $name!");
}

sayHello("Lucas"); // Hello, Lucas!
sayHello("Lucas", "Hi"); // Hi, Lucas!
// 命名参数
sayHello(name: "Lucas"); // Hello, Lucas!
sayHello(name: "Lucas", greeting: "Hi"); // Hi, Lucas!

// 匿名函数
void main() {
  var printNum = (int num) {
    print(num);
  };
}

// 自执行函数
void main() {
  (() {
    print("Hello, World!");
  })();
}
```

```dart
// 闭包
// 1.全局变量特点：常驻内存，污染全局
// 2.局部变量特点：不常驻内存，会被垃圾回收机制回收，不污染全局
// 3.闭包特点：函数嵌套函数，内部函数可以访问外部函数的变量
fn() {
  var a = 123;
  return () {
    a++;
    print(a);
  };
}

var f = fn();
f(); // 124
f(); // 125
```

```dart
// 面向对象编程的三个基本特征是：封装、继承和多态。
// Dart所有对象都继承自object类
// Dart是一门使用类和单继承的面向对象语言，所有对象都是类的实例，并且所有的类都是object的子类。
// 类
class Person { // 类名采用驼峰命名法，首字母大写
  String name;
  int age;
  double height;

  void sayHello() {
    print("Hello, my name is ${this.name}, I am ${this.age} years old, and my height is ${this.height} meters.");
  }
}

// 构造函数
class Person {
    String name;
    int age;
    double height;

    //默认构造函数 只允许定义一个
    Person(this.name, this.age, this.height){
    	print("Person created.");
    }
    
    //命名构造函数 可以定义多个
    Person.now(){
        print("Person now status.")
    }

    void sayHello() {
        print("Hello, my name is $name, I am $age years old, and my height is $height meters.");
    }
}


```



#### 类抽象为模块

```dart
/* ./lib/Person.dart */
class Person{
    String name;
    int age;
    
    Person(this.name, this.age);
    void printInfo(){
        print("${this.name}->${this.age}")
    }
}

/* ./main.dart */
import 'lib/Person.dart';

void main(){
    Person p1 = new Person("Jenny", 20);
    p1.printInfo();
}
```



#### Dart中的私有方法和私有属性

```dart
/*
Dart和其他面向对象语言不一样，它没有public private protected等访问修饰符
但可以用和python类似的'_'把一个属性或者方法定义成私有
*/

class Animal{
    String name;
    int _age;
    
    Animal(this.name, this._age);
    
    void printInfo(){
        print("${this.name}->${this._age}");
    }
}

void main(){
    Animal a = new Animal("dog", 3);
    print(a.name); // 公有属性 可以直接访问
    print(a._age); // 私有属性 如果类和main函数在一个文件中，那么私有属性也是可以访问的，如果要是将这个类抽象成另外一个文件，那么私有属性就不可以访问了
}
```



#### getter和setter修饰符使用

```dart
class Rect{
    num height;
    num width;
    Rect(this.height, this.width);
    // Rect():height=2,width=10{}  // 实例化之前先给属性赋值 就是初始化赋值
    get area{
        return this.height * this.width;
    }
    set areaHeight(value){
        this.height = value;
    }
}

void main(){
    Rect rect = new Rect(20, 2);
    print(rect.area); //40
    
    rect.areaHeight = 40;
    print(rect.area); //80
}
```



#### 类的详细使用方法

```dart
/*
dart中的静态成员：
	1.使用static关键字来实现类级别的变量和函数
	2.静态方法不能访问非静态成员，非静态方法可以访问静态成员
*/
class Person{
    static String name = "zhangsan";
    int age = 20;
    static void show(){
        print(name);
    }
    void printInfo(){
        print(name + age);
    }
}
void main(){
    Person.show(); // zhangsan
    
    new Person().printInfo(); // zhangsan20
}

/*
Dart中的对象操作符：
	？ 条件运算符
	as 类型转换
	is 类型判断
	.. 级联操作
*/
void main(){
    Person p;
    p?.printInfo(); // 如果p是空的，那么就不会调用printInfo方法
    
    if(p is Person){
        p.name = "lisi";
    }
    
    var p1;
    p1 = "";
    p1 = new Person();
    // p1.printInfo(); // 这里可能会报错，因为程序并不知道p1到底是什么类型的变量
    (p1 as Person).printInfo();
    
    Person p2 = new Person();
    p1.printInfo(); // zhangsan20
    
    p1..name = "lisi" // 级联操作
      ..age = 30
      ..printInfo(); // lisi30
    
}
```



#### 类的继承

```dart
/*
类的继承：
	1.子类使用extends关键字来继承父类
	2.子类会继承父类里面可见的属性和方法，但不会继承构造函数
	3.子类可以复写父类的方法 getter和setter
*/
class Person{
    String name = "zhangsan";
    int age = 20;
    void printInfo(){
        print(name + age);
    }
}

class Web extends Person{
    
}

void main(){
    Web w = new Web();
    print(w.name); // zhangsan
    w.printInfo(); // zhangsan20
}

//---------------------------------------------------------------------------

class Animal{
    String name;
    int age;
    Animal(this.name, this.age);
    void printInfo(){
        print("${this.name}---${this.age}");
    }
}

class Dog extends Animal{
    Dog(String name, num age): super(name, age); // 当创建类的对象时候，先执行一遍父类的构造函数
    
    /*覆写父类的方法*/
    @override
    void printInfo(){
        print("姓名：${this.name}---->年龄：${this.age}");
    }
    
    @override
    void printInfo2(){
        super.printInfo()
    }
}
```



#### 抽象类

```dart
/*
抽象类主要用于定义标准，子类可以继承抽象类，也可实现抽象类接口
	1.抽象类通过abstract 关键字来定义
	2.抽象方法不能用abstract声明，Dart中没有方法体的方法称为抽象方法
	3.如果子类继承抽象类必须得实现里面的抽象方法
	4.若把抽象类当作接口实现必须实现抽象类里面定义的所有方法和属性
	5.抽象类不能实例化，只有继承他的子类可以
extends抽象类和 implements的区别
	1.如果要复用抽象类里面的方法，并且要用抽象方法约束子类的话，就用extends继承抽象类
	2.如果只是把抽象类当作标准的话我们就用implements实现抽象类
*/

abstract class Animal{
    eat(); // 抽象方法，主要用于约束子类
    printInfo(){
        print('我是一个抽象类里面的普通方法');
    }
}

class Mao extends Animal{
    @override
    eat(){
        return "肉";
    }
}

void main(){
    mao m = new Mao();
    print(mao.eat());
}
```

