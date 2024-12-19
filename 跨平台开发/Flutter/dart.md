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

