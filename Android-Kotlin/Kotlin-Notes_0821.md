# Kotlin笔记

## 背景
![Kotlin and JVM](/Kotlin-Notes_0821/Kotlin%20and%20JVM.png)

## 变量声明与内置数据类型
**变量声明**
```kotlin
// 可读可改的变量声明
var name: String = "Lucas"

// 只读的变量声明
val age: Int = 25
```
**内置数据类型**
| 类型 | 说明 |
| :---: | :---: |
| Byte | 字节型，8位有符号整数 |
| Short | 短整型，16位有符号整数 |
| Int | 整型，32位有符号整数 |
| Long | 长整型，64位有符号整数 |
| Float | 浮点型，32位单精度浮点数 |
| Double | 双精度浮点型，64位双精度浮点数 |
| Boolean | 布尔型，true或false |
| Char | 字符型，单个Unicode字符 |
| String | 字符串型，一组字符 |
| List | 列表型，元素有序集合 |
| Set | 集合型，元素无序集合，元素不可重复 |
| Map | 映射型，键值对集合 |

**类型推断**
```kotlin
// 这里编译器会自动推断出age的类型为Int
var age: Int = 25
//可以写成如下方式
var age = 25
```

**定义编译时常量**
```kotlin
// 定义编译时常量
const val PI = 3.1415926

fun main() {
    // 这只是只读的常量，不能修改
    val info = "sgayjkhdfshgf"

    // 编译时常量只能在顶层作用域定义，不适用于函数内部(局部变量)
    // const val MAX_SIZE = 1000
    println(PI)
    println(info)
}
```
Q: 为什么编译时常量只能在顶层作用域定义？
A: 因为编译时常量在编译时就已经确定了值，所以在运行时不能修改，因此只能在顶层作用域定义。而如果定义在函数内部，就必须在运行时才能确定值，何来编译时常量的意义？

Q: 怎么查看Kotlin反编译后的字节码？
A: 在Android Studio中，点击菜单栏中的`Tools` -> `Kotlin` -> `Show Kotlin Bytecode`，即可查看Kotlin反编译后的字节码。

Q: 像Int、Float等等均是kotlin的引用类型，也就是说这些都是kotlin的类，那么会不会影响性能呢？
A: 不会影响性能，因为kotlin的类型系统是静态的，编译器会在编译时检查类型，并生成相应的字节码，运行时不会进行类型检查。

## 各大表达式的用法
**range表达式**
```kotlin
// range表达式就是用..表示一个范围，可以用来遍历数字、字符、字符串等等
val number = 149
if (number in 10 .. 59){
    println("out of limit")
}else if (number in 0 .. 9){
    println("sad")
}else{
    println("Yes")
}
```
**when表达式**
```kotlin
val week = 5
// Kotlin的when和if均是表达式，是有返回值的。
// 如果返回类型不一致，那么day的类型就是Any
val day = when (week) {
    1 -> println("Monday")
    2 -> println("Tuesday")
    3 -> println("Wednesday")
    4 -> println("Thursday")
    5 -> println("Friday")
    6 -> println("Saturday")
    7 -> println("Sunday")
    else -> println("Invalid week number")
}
println(day) // Output: kotlin.Unit 代替了java中的void关键字
```
**String模板**
```kotlin
val park = "Park Avenue"
val time = "10:30 AM"
println("Welcome to $park at ${time}")

val isLogined = false
println("You are logged in: ${if (isLogined) "Yes" else "No"}")
```

## 函数
![Kotlin Functions](/Kotlin-Notes_0821/func.png)
```kotlin
// 函数默认都是public的
// 其实Kotlin的函数更规范，先有输入，再有输出
private fun sayHello(age: Int, name: String): Any {
    println("Hello, $name! You are $age years old.")
    return 200
}
```
**默认参数**
```kotlin
const val age = 99

fun main() {
    action1("Lucas", age)
    action2("Lucas")
    action3()
    action3(age = 100)
    action3(name = "John")
}

private fun action1(name: String, age: Int){
    println("Action 1: $name, $age")
}

private fun action2(name: String, age: Int = 99){
    println("Action 1: $name, $age")
}

private fun action3(name: String = "Lucas", age: Int = 99){
    println("Action 1: $name, $age")
}
```
**具名函数参数**
```kotlin
fun main() {
    sayHello(name="Lucas", age=age, phone = "555-555-5555")
}

private fun sayHello(name: String, age: Int, phone: String) {
    println("Hello, my name is $name and I am $age years old. My phone number is $phone.")
}
```
**Unit函数特点**
```kotlin
// Unit不写。默认也有，相当于void
private fun sayHello(name: String, age: Int, phone: String): Unit {
    println("Hello, my name is $name and I am $age years old. My phone number is $phone.")
}
```
**反引号的使用**
```kotlin
fun main() {
    // 第一种情况
    `dskjlfhdshfjkadfakjfasdf`("Lucas", age)
    // 第二种情况，当函数名字是敏感词汇可以用反引号包裹
    abc.`is`()
    // 第三种情况，公司加密私有文档，用某种代码加密，可以用反引号包裹
    abc.`3243531223`()

}

private fun `dskjlfhdshfjkadfakjfasdf` (name: String, age: Int){
    println("Hello, $name! You are $age years old.")
}
```
**匿名函数lamaba表达式**
```kotlin
fun main() {
    val len = "Lucas".count()
    println("The length of 'Lucas' is $len")

    // it 等价于L u c a s的字符 Char
    val len2 = "Lucas".count{ it == 'a' }
    println("The length of 'Lucas' with only 'a's is $len2")
}
```
**kotlin的函数类型和隐式返回**
```kotlin
const val age = 99

fun main() {
    // 写函数
    // 1. 定义函数，声明函数的输入输出
    val methodAction: () -> String

    // 2. 实现函数的逻辑，其实这是个匿名函数，在{}里面，最后一行是匿名函数的返回值
    methodAction = {
        "Hello, Kotlin!"
        // 匿名函数不要写return，最后一行的表达式会作为返回值
    }

    // 3. 调用函数
    println(methodAction())

    val x = if (age > 18){
        "adult"
    }else{
        "teenager"
    }
    println(x)
}
```
**函数参数**
```kotlin
fun main() {
    val methodAction: (Int, String) -> String = { num, str ->
        "The number is $num and the string is $str"
    }
    println(methodAction(10, "Hello"))
}
```
**it关键字**
```kotlin
fun main() {
    val methodAction: (Int, String) -> String = { num, str ->
        "The number is $num and the string is $str"
    }
    println(methodAction(10, "Hello"))
    // methodAction(10, "Hello") === methodAction.invoke(10, "Hello")

    //匿名函数为一个参数，会自动生成一个it参数
    val methodAction2: (String) -> String = { "$it is a string" }
    println(methodAction2("Hello"))

    val methodAction3: (Int) -> Int = { it * it }
    println(methodAction3(10))
}
```
**Lambda的类型推断**
```kotlin
fun main() {
    // 如果使用冒号，必须指定参数类型和返回类型
    val method1: (Int) -> String = { num -> "Hello, Kotlin!" }
    // 如果没有冒号，会自动推断参数类型和返回类型
    val method2 = { v1: Int, v2:Float -> "v1: $v1, v2: $v2" }
    println(method2(1, 2.0f))
}
```