# Kotlin
总是说kotlin简洁，咱们举个列子来对比一下有什么简洁之处
Java
```java
package com.charon.kotlinstudydemo;

public class Person {
    private int age;
    private String name;
    private float height;
    private float weight;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public float getHeight() {
        return height;
    }

    public void setHeight(float height) {
        this.height = height;
    }

    public float getWeight() {
        return weight;
    }

    public void setWeight(float weight) {
        this.weight = weight;
    }

    @Override
    public String toString() {
        return "Person name is : " + name + " age is : " + age + " height is :"
                + height + " weight is :" + weight;
    }
}
```
再来看看Kotlin
```kotlin
package com.charon.kotlinstudydemo

data class Person(
        var name: String,
        var age: Int,
        var height: Float,
        var weight: Float)
```
看看这个类，相较于前面的java，它自动的生成了所有属性和访问器，唯一不一样的就是在每句后面不用加分号

## 创建新项目
创建完成之后会有下面初始化的代码
```kotlin
// 定义包
package com.charon.kotlinstudydemo

// 导入
import android.support.v7.app.AppCompatActivity
import android.os.Bundle

// 定义类，继承AppCompatActivity
class MainActivity : AppCompatActivity() {
	
	// 重写方法用overide，函数名用fun声明  参数是a: 类型的形式 ?是啥？它是指明该对象可能为null，
	// 如果有了?那在调用该方法的时候参数可以传递null进入，如果没有?传递null就会报错
    override fun onCreate(savedInstanceState: Bundle?) {
    	// super 
        super.onCreate(savedInstanceState)
        // 调用方法
        setContentView(R.layout.activity_main)
    }
}
```
咱们就从MainActivity开始介绍一些基本的语法
1. 变量
    定义变量的关键字是var(可读可写)和val(只读)，当然也可以显式的指定变量的类型，但我们一般不用去写，编译器可以自己推断出具体的类型。
    还有就是在kotlin中，一切都是对象，没有原始基本类型，与java中不同的是
    - 数字类型不会自动转型
    - 字符不能直接作为数字来处理，需要转成对应的数字再处理
    - 位运算符有点像lua，用or或者and等字符来进行运算
    - 字符串String也可以像数组那样子访问
    - 用const修饰符标记为编译器常量，就和java中的public static final一样，const只能修饰val，不能修饰var
2. 延迟初始化
    当我们想在类内声明一个null属性，在需要时进行初始化，我们可以声明一个属性并延迟初始化，此属性需要用`lateinit`关键字修饰
    `private lateinit var person: Person`
    记住，不管在什么时候使用，一定要先初始化，否则就算是判空也会报错
    也可以用`by laze {}`关键字修饰
    `private val person by lazy { Person() }`
    这二者的区别
    |`lateinit`|`by laze {}`|
    |:---:|:---:|
    |只能用于var属性|只能用于val属性|
    |不能用在可空的属性上和java的基本类型上|可以用在可空的属性上|
## 类的定义
使用class关键字定义类
类里面可以有
   - 构造函数和初始化块
   - 函数
   - 属性
   - 嵌套类和内部类
   - 对象声明
```kotlin
class MainActivity{

}

//如果有参数的话，可以在类型后加括号并在其中写上参数
class Person(name: String, age: Int)
```
在创建类对象的时候，不用`new`关键字了哟
- 构造函数
  - 在kotlin中可以有一个主构造函数和多个次构造函数
  - 主构造函数
    - `class Person constructor(name: String, surname: String) {}`
    - 如果主构造函数没有任何的注解或者可见性修饰符们可以省略`constructor`关键字
    - `class Person(name: String, surname: String) {}`
    - 要是构造函数有注解或者可见性修饰符，那必须要加上`constructor`关键字
        ```kotlin
        class Person private @Inject constructor(name: String, surname: String) {
            init {
                print("name is $name and surname is $surname")
            }
        }
        ```
  - 次构造函数
    声明前缀带有`constructor`的次构造函数
    ```kotlin
    class Person{
        constructor(name: String) {
            print("name is $name")
        }
    }
    ```