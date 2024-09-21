# Kotlin

## kotlin数据类型

|基本数据类型|说明|
|:---:|:---:|
|Int|整型|
|Long|长整型|
|Float|浮点型|
|Double|双精度浮点型|
|Char|字符型|
|Boolean|布尔型|
|String|字符串型|

**变量声明**

```kotlin
var a: Int = 10
```

**简单变量转换**

|类型转换函数|说明|
|:---:|:---:|
|toInt()|转换为Int类型|
|toDouble()|转换为Double类型|
|toFloat()|转换为Float类型|
|toLong()|转换为Long类型|
|toChar()|转换为Char类型|
|toString()|转换为String类型|
|toBoolean()|转换为Boolean类型|

**数组变量的声明**

```kotlin
var int_array: IntArray = intArrayOf(1, 2, 3)
```

|基本数组类型|数组类型名字|初始化方法|
|:---:|:---:|:---:|
|整型数组|IntArray|intArrayOf()|
|长整型数组|LongArray|longArrayOf()|
|浮点型数组|FloatArray|floatArrayOf()|
|双精度浮点型数组|DoubleArray|doubleArrayOf()|
|字符型数组|CharArray|charArrayOf()|
|布尔型数组|BooleanArray|booleanArrayOf()|
|字符串型数组|Array\<String>|arrayOf()|

**数组元素的操作**

```kotlin
var int_array: IntArray = intArrayOf(1, 2, 3)

// 获取数组长度
println(int_array.size) // 输出3

// kotlin可以直接通过下标访问数组元素
println(int_array[0]) // 输出1
int_array[0] = 4 // 修改数组元素

// 不过kotlin还有get和set方法
var int_array: IntArray = intArrayOf(1, 2, 3)
println(int_array.get(0)) // 输出1
int_array.set(0, 4) // 修改数组元素
```

**字符串**

- 常用方法

|方法|说明|
|:---:|:---:|
|indexOf(str: String)|查找子字符串的位置|
|length()|获取字符串长度|
|substring(start: Int, end: Int)|获取子字符串|
|replace(oldChar: Char, newChar: Char)|替换字符串中的字符|
|split(regex: String)|按正则表达式分割字符串|

⚠️split()方法返回的是List[^1]\<String>类型

- 字符串模板

```kotlin
val name = "Kotlin"
val age = 18
println("My name is $name and I am $age years old.")

// 先运算后把结果拼接到字符串中
println("My name is ${name.toUpperCase()} and I am ${age + 1} years old.")

// 转义
println("美元金额为\$100")
// 或者
println("美元金额为${'$'}100")
```

[^1]: 这里的List和Array还有MutableList是三个类似数组的结构，他们的区别在于Array在初始化时就必须确定，之后不可以改变，List是只读的，连修改都做不到，而MutableList则可以修改。

**容器**

与java类似，kotlin也有三类基本容器：队列List、集合Set、映射Map，每类容器又分为只读和可变两种类型。
默认容器都是只读类型的，若需要允许修改容器变量，需要加上Mutable前缀

这三种都属于容器的话，势必会有一些公共方法
|方法|说明|
|:---:|:---:|
|contains(element: T)|判断容器是否包含元素|
|isEmpty()|判断容器是否为空|
|isNotEmpty()|判断容器是否不为空|
|clear()|清空容器|
|iterator()|返回容器的迭代器|
|count()|返回容器元素个数,与size()方法相同|

- 集合是无序的
- 集合的元素不能重复

⚠️队列和映射遍历时可以利用下标来遍历`mutablelist.indices`，而集合不行，因为是无序的。

- 映射存储的是键值对，键不能重复，值可以重复
- 在初始化映射时，有两种方法表达键值对元素

```kotlin
// 第一种方法
var map1 = mapOf("name" to "Kotlin", "age" to 18)

// 第二种方法
val map2 = mapOf(Pair("name", "Kotlin"), Pair("age", 18))
```

## 条件分支

1. if-else
```kotlin
val a = 10
val b = 20

// 写法1
if (a > b) {
    val c = true
} else {
    val c = false
}

// 写法2
val c = if (a > b){
    true
}else{
    false
}

// 写法3
val c = if (a > b) true else false

// 三种写法一模一样
```

2. when-else
```kotlin
val a = 10

when (a) {
    1 -> println("a等于1")
    2 -> println("a等于2")
    else -> println("a不等于1或2")
}

// 也可以使用表达式
when (a) {
    in 1..10 -> println("a在1到10之间")
    !in 1..10 -> println("a不在1到10之间")
}

// 也可以使用多个分支
when (a) {
    1 -> println("a等于1")
    2 -> println("a等于2")
    else -> {
        println("a不等于1或2")
        println("a大于2")
    }
}
```

3. 类型判断
```kotlin
val a: Any = "hello"

if (a is String) {
    println("a是String类型")
} else if (a is Int) {
    println("a是Int类型")
} else {
    println("a不是String或Int类型")
}
```

## 循环
```kotlin
// 1. for循环
// 使用in关键字遍历集合，和python很类似
for (i in 1..10) {
    println(i)
}

// 2. while循环
// 和java一样，while和do-while都保留了下来，没什么不一样

// 3. 跳出循环
// 使用标签，break和continue关键字

outside@ while (true) {
    println("hello")
    break@outside
}
```

## 空安全
Kotlin校验字符串空值的几个方法
|函数名|说明|
|:---:|:---:|
|isNullOrEmpty()|为空指针或者字串长度为0时返回true，非空串与可空串均可调用|
|isNullOrBlank()|为空指针、字串长度为0或者全部为空格时返回true，非空串与可空串均可调用|
|isEmpty()|字串长度为0时返回true，可空串不可以调用|
|isBlank()|字串长度为0或者全部为空格时返回true，可空串不可以调用|
|isNotEmpty()|字串长度不为0时返回true，可空串不可以调用|
|isNotBlank()|字串长度不为0或者全部为空格时返回true，可空串不可以调用|

```kotlin
// 声明可空变量
var str: String? = "hello"

// 调用方法
// 在变量名称后面加问号，表示一旦变量为空就返回null
var len_null: Int? = str?.length

// 引入新运算符'?:'表示变量一旦为空，就返回运算符右边的表达式
var len_null2 = str?.length?: -1

// 新引入运算符'!!'通知编译器不做非空校验，如果发现变量为空，就扔出异常
var len_null3 = str!!.length
```

## 等式判断

**\==和===**

- '=='比较的是结构是否相等，引用不参与比较
- '==='比较的是结构和引用是否都相等

**is运算符**

写法形如`变量名称 is 类型`

**in运算符**

写法形如`元素 in 集合`

这两个运算符用法和python类似

## 函数
```kotlin
// 默认为public
fun add(a: Int=0, b: Int=0): Int {
    return a + b
}
```

**可变参数**

利用vararg关键字声明可变参数

```kotlin
fun add(vararg nums: Int): Int {
    var sum = 0
    for (num in nums) {
        sum += num
    }
    return sum
}

// 调用
add(1, 2, 3) // 6
add(1, 2, 3, 4, 5) // 15
```

**几种特殊函数**

- 泛型函数

```kotlin
// 在尖括号内部制定数组元素的类型，就是泛型的写法'<xxx>'
// 定义泛型函数时，要在函数名字前加'<T>',表示以T声明的参数在调用函数时必须指定类型

fun <T> abc(a: T, b: T, c: String?): T {
    // 这里的a,b,c都是T类型
    return a
}

// 调用 abc()时，必须指定类型
abc<Int>(1, 2, "hello")
```

- 内联函数

- 简化函数

```kotlin
// 简化函数的定义，省略了类型声明，直接用表达式作为函数体
fun add(a: Int, b: Int) = a + b

// 调用
add(1, 2) // 3
```

- 尾递归函数

```kotlin
// 在函数末尾的返回值重复调用了自身函数，名为尾递归函数
// 可以用tailrec关键字告诉编译器这是一个尾递归函数，则编译器会进行相应的优化
tailrec fun findFixPoint(x: Double = 1.0): Double = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x))

// 调用
findFixPoint() // 0.7390851332151607
```

- 高阶函数
把函数A当作参数传给另一个函数B，因为B的入参内嵌了函数，所以B称为高阶函数。

- 扩展函数
扩展函数允许开发者给系统类补写新的方法，无需另外编写额外工具类。
比如现在Array类提供了最大值max方法，也有排序sort方法，可是没有提供交换数组元素的swap方法。

```kotlin
fun Array<Int>.swap(i: Int, j: Int) {
    val temp = this[i]
    this[i] = this[j]
    this[j] = temp
}

// 然而这个只能用于整形数组，为了增强交换函数的通用性，可以写一个泛型函数

fun Array<T>.swap(i: Int, j: Int) {
    val temp = this[i]
    this[i] = this[j]
    this[j] = temp
}
```

- 日期时间函数

|日期时间格式|说明|
|:---:|:---:|
|yyyy|四位年份数组|
|MM|两位月份数字|
|dd|两位日期数字|
|HH|表示24小时制的小时数|
|hh|表示12小时制的小时数|
|mm|表示两位分钟数|
|ss|两位秒钟数字|
|SSS|表示毫秒数，3位数|

```kotlin
fun Date.getNowDateTime(): String {
    val formatter = SimpleDateFormat("yyyy-MM-dd HH:mm:ss SSS")
    return formatter.format(this)
}

fun Date.getNowDate(): String {
    val formatter = SimpleDateFormat("yyyy-MM-dd")
    return formatter.format(this)
}

fun Date.getNowTime(): String {
    val formatter = SimpleDateFormat("HH:mm:ss")
    return formatter.format(this)
}

fun Date.getNowTimeDetail(): String {
    val formatter = SimpleDateFormat("HH:mm:ss.SSS")
    return formatter.format(this)
}

fun Date.getFormatTime(format: String=""): String {
    var ft: String = format
    val sdf = if (!ft.isEmpty()) SimpleDateFormat(ft) else SimpleDateFormat("yyyy-MM-dd HH:mm:ss SSS")
    return sdf.format(this)
}
```

- 单例对象

```kotlin
object DateUtils {
    val nowDateTime: String
        get() {
            val formatter = SimpleDateFormat("yyyy-MM-dd HH:mm:ss SSS")
            return formatter.format(Date())
        }

    val nowDate: String
        get() {
            val formatter = SimpleDateFormat("yyyy-MM-dd")
            return formatter.format(Date())
        }

    val nowTime: String
        get() {
            val formatter = SimpleDateFormat("HH:mm:ss")
            return formatter.format(Date())
        }

    val nowTimeDetail: String
        get() {
            val formatter = SimpleDateFormat("HH:mm:ss.SSS")
            return formatter.format(Date())
        }

    fun getFormatTime(format: String = ""): String {
        var ft: String = format
        val sdf = if (!ft.isEmpty()) SimpleDateFormat(ft) else SimpleDateFormat("yyyy-MM-dd HH:mm:ss SSS")
        return sdf.format(Date())
    }
}

// 调用
DateUtils.nowDateTime // 2021-09-10 10:52:35 123
DateUtils.getFormatTime("yyyy-MM-dd HH:mm:ss") // 2021-09-10 10:52:35
```

## 类

**类的构造**

```kotlin
class Animal{
    // 类的初始化函数
    init {
        // Kotlin用println代替java的System.out.println()
        println("Animal init")
    }
}

var dog = Animal()
```
由此看出Kotlin和java之间的区别
(1)kotlin默认就是public，所以忽略了public关键字
(2)kotlin用`:`替代了java的extends，即用冒号表示继承关系
(3)kotlin在继承时，父类后面多了括号
(4)kotlin对类进行初始化的函数叫init
(5)kotlin使用println代替java的System.out.println()
(6)在创建实例时省略了new关键字

```kotlin
// 如果主构造函数没有带@符号的注解说明，类名后面的constructor就可以省略
// class AnimalMain (context: Context, name: String) {

class AnimalMain constructor(context: Context, name: String) {
    init {
        context.toast("AnimalMain init")
    }
}

// 然而这个代码似乎存在问题，一个类可能有多个构造函数
// kotlin就这个问题引入了主构造函数和二级构造函数的概念，如下

class AnimalMain constructor(context: Context, name: String) {
    init {
        context.toast("AnimalMain init")
    }
    
    constructor(context: Context, name: String, sex: Int) : this(context, name) {
        // 二级构造函数，调用父类构造函数
        var sexname: String = if (sex == 0) "公" else "母"
        context.toast("AnimalMain 二级构造函数，性别是$sexname")
    }
}

// 二级构造函数没有函数名称，只用关键字constructor表示这是一个构造函数
// 二级构造函数需要调用主构造函数，`this(context, name)`这句在java中就是以`super(context, name)`的形式写在函数体内部的，而kotlin以冒号开头补充到输入参数的后面，这意味着二级构造函数实际上是从主构造函数上派生来的
```

而以上述的代码执行，那么toast会弹窗两次，我们现在能不能不要强制调用主构造函数，为了解决这个问题，kotlin设定了主构造函数不是必须的，也就是说，类可以把几个构造函数都放在类内部定义，从而都变成二级构造函数，这样就去掉了主构造函数

```kotlin

// 这样子就不存在主构造函数，两个二级构造函数之间就没有从属关系，他们的代码是互相独立的
class AnimalMain {
    constructor(context: Context, name: String) {
        context.toast("AnimalMain init")
    }

    constructor(context: Context, name: String, sex: Int) {
        var sexname: String = if (sex == 0) "公" else "母"
        context.toast("AnimalMain 二级构造函数，性别是$sexname")
    }
}
```

**类的成员**

1. 成员属性
在java中，想要将输入参数转化为成员变量，需要用this关键字重新赋值，但其实输入参数和成员属性这两者名字大部分时候是一样的，Kotlin提供了一些机制，如下

```kotlin
// 这是模仿java的kotlin写法
class WildAnimal (name: String, sex: Int=0) {
    var name: String
    val sex: Int
    init {
        this.name = name
        this.sex = sex
    }
}

// 这是kotlin的写法，和java的效果是一模一样的
class WildAnimal (var name: String, val sex: Int=0) {
    // 省略了init函数
}
```

2. 伴生对象
kotlin取消了static关键字，无法直接声明静态对象，但可以声明伴生对象，伴生对象是一个单例对象，可以用伴生对象来实现静态方法，如下

```kotlin
class WildAnimalCompanion (var name: String, val sex: Int=0) {
    var sexName: String
    init {
        this.sexName = if (sex == 0) "公" else "母"
    }

    // 在类加载时就运行伴生对象的代码块，其作用就相当于static {...}
    // 关键字companion表示伴随，object表示对象，WildAnimal就是伴生对象
    companion object WildAnimal {
        // 静态常量的值是不可变的，所以只要使用关键字val修饰
        val MALE = 0
        val FEMALE = 1
        val UNKNOWN = -1
        fun judgeSex(sexName: String):Int {
            var sex: Int = when (sexName) {
                "公" -> MALE
                "母" -> FEMALE
                else -> UNKNOWN
            }
            return sex
        }
    }
}

// 调用
WildAnimalCompanion.judgeSex("公") // 0
WildAnimalCompanion.WildAnimal.judgeSex("母") // 1
```

**类的继承**

在上述的WildAnimal类中，如果像java一样写一个tiger类继承WildAnimal类，直接继承是会报错的，因为默认类都是final类型，不允许被继承，所以kotlin提供了open关键字，表示允许被继承，如下

```kotlin
open class WildAnimal (var name: String, val sex: Int=0) {
    var sexName: String
    init {
        this.sexName = if (sex == 0) "公" else "母"
    }

    // 在类加载时就运行伴生对象的代码块，其作用就相当于static {...}
    // 关键字companion表示伴随，object表示对象，WildAnimal就是伴生对象
    companion object WildAnimal {
        // 静态常量的值是不可变的，所以只要使用关键字val修饰
        val MALE = 0
        val FEMALE = 1
        val UNKNOWN = -1
        fun judgeSex(sexName: String):Int {
            var sex: Int = when (sexName) {
                "公" -> MALE
                "母" -> FEMALE
                else -> UNKNOWN
            }
            return sex
        }
    }
}

class Tiger(name: String, sex: Int=0) : WildAnimal(name, sex) {
    override fun eat() {
        println("老虎吃肉")
    }
}


// 调用
var tiger = Tiger("老虎")
tiger.eat() // 老虎吃肉
```

|开放性修饰符|说明|
|:---:|:---:|
|open|允许被继承,如果要是修饰函数，那么子类可以重写父类函数|
|public|对所有人开放，默认|
|protected|只对自己和子类开放|
|internal|对本模块内部开发|
|private|只对自己开放，与open不兼容|

继承protected方法，标准写法是`open protected`

不过protected方法继承过来就是protected，所以可以省略，kotlin编译器会自动识别

继承之后可以将可见性升级，不能降级

**抽象类**

kotlin有和java类似的抽象类，之所以存在，是因为其内部有被关键字`abstract`修饰的抽象方法，抽象方法是没有函数体的，外部是无法直接调用的，只有在子类继承时重写，才能正常使用

```kotlin
// 因为抽象类不能直接使用，所以构造函数不必给默认参数赋值
abstract class Chicken (name: String, sex: Int, var voice: String) : WildAnimal(name, sex) {
    // 抽象方法必须在子类中重写，所以可以省略关键字open
    // open abstract fun callOut(times: Int): String
    abstract fun callOut(times: Int): String
}

class Cock(name:String="鸡", sex:Int=0, voice:String="喔喔喔"): Chicken(name, sex, voice) {
    override fun callOut(times: Int): String {
        var count = when {
            times <= 0 -> 0
            times > 10 -> 9
            else -> times
        }
        return "鸡叫了$count次"
    }
}
```

**接口**

接口不能定义构造函数
接口内部方法需要被实现他的类重写，这些方法默认为抽象类型
Kotlin允许接口内部实现某个方法

```kotlin
// kotlin与java一样不允许多重继承
// 接口不能带构造函数

// interface Behavior (val action:String) {
interface Behavior {
    // 接口内部的方法默认就是抽象的，所以加不加abstract和open都无所谓
    open abstract fun fly():String
    fun swim():String

    // kotlin与java区别在于kotlin接口内部允许实现方法
    // 此时该方法不是抽象方法，不能加上abstract关键字
    // 不过此时仍然是open类型，接口内部所有方法默认都是open类型
    fun run():String {
        return "跑"
    }
    // kotlin的接口允许声明抽象属性，实现该接口的类必须重载该属性
    // 与接口内部方法一样，抽象属性前面的open和abstract关键字都可以省略
    // open abstract val skilledSports:String
    val skilledSports:String
}
```

**接口代理**

**几种特殊类**

1. 嵌套类
一个类定义在另一个类的内部被称作嵌套类，与java的区别是java的嵌套类允许访问外部类的成员，而kotlin不允许访问外部类的成员，在调用时得在嵌套类的类名前添加外部类的类名

2. 内部类
在嵌套类的基础上增加inner关键字就变成了内部类，内部类和嵌套类区别在与是否可以访问外部类的成员，在调用内部类时一定要先实例化外部类，在通过外部类的实例调用内部类的构造函数

3. 枚举类
将关键字enum修饰class就变成了枚举类

```kotlin
enum class Sex(val sexname: String) {
    MALE("男"),
    FEMALE("女")
}
// 调用
var sex = Sex.MALE
println(sex.sexname) // 男
```

4. 密封类
密封类就像是更加严格的枚举类，在class关键字前面加上关键字sealed作为标记，在使用when表达式时，就无须用else了

```kotlin
sealed class SeasonSealed {
    // 密封类内部的每个嵌套类都必须继承该类
    class Spring:SeasonSealed()
    class Summer:SeasonSealed()
    class Autumn:SeasonSealed()
    class Winter:SeasonSealed()
}

// 调用
var season = SeasonSealed.Spring()
// 密封类是一种严格的枚举类，他的值是一个有限的集合
// 他确保了条件分支覆盖了所有的枚举类型，因此不再需要else分支
when (season) {
    is SeasonSealed.Spring -> println("春天")
    is SeasonSealed.Summer -> println("夏天")
    is SeasonSealed.Autumn -> println("秋天")
    is SeasonSealed.Winter -> println("冬天")
}
```

5. 数据类
只需在class声明data即可转化成数据类，可以实现
自动声明与构造函数入参同名的属性字段
自动实现每个属性字段的get/set方法
自动提供equals方法，比较两个数据对象是否相等
自动提供copy方法，允许完整复制某个数据对象，也可在复制后单独修改某几个字段的值
自动提供toString方法，打印数据对象的内容

```kotlin
data class Person(val name: String, val age: Int){}
// 数据类必须要有主构造函数
// 主构造函数的输入参数前面必须添加关键字val或者var这保证每个入参都会声明同名的属性字段
// 数据类有自己的一套行事规则，所以只能是一个独立的类，不能是其他类型的类，否则会冲突
```

6. 模版类
和泛型函数很类似

```kotlin
class MyArray<T>(val size: Int, val initValue: T) {
    var values: Array<T> = arrayOfNulls(size)
    init {
        for (i in 0 until size) {
            values[i] = initValue
        }
    }

    fun get(index: Int): T {
        return values[index]
    }

    fun set(index: Int, value: T) {
        values[index] = value
    }
}

// 调用
var myArray = MyArray<Int>(3, 0)
myArray.set(1, 1)
println(myArray.get(1)) // 1
```

## Android布局

**线性布局LinearLayout**

顾名思义，线性布局就像是一根线串起来，不过排列顺序只能指定一维方向的视图次序，手机屏幕是二维平面，意味着还有另外一维需要指定对齐方式，因此线性布局主要有以下两种属性设置方法
(1)setOrientation(int orientation)
设置布局的方向，有水平方向和垂直方向两种，分别用LinearLayout.HORIZONTAL和LinearLayout.VERTICAL表示
(2)setGravity(int gravity)
设置布局的对齐方式，有左对齐、右对齐、居中对齐、靠上对齐、靠下对齐、居中对齐等，具体用法参考Gravity类
|Gravity类的对齐方式|说明|
|:---:|:---:|
|Gravity.LEFT|左对齐|
|Gravity.RIGHT|右对齐|
|Gravity.CENTER|居中对齐|
|Gravity.TOP|靠上对齐|
|Gravity.BOTTOM|靠下对齐|

**相对布局RelativeLayout**

由于线性布局视图排列方式比较固定，不能重叠显示，也不能灵活布局，相对布局内部视图位置不依赖排序规则而是依赖于指定的参照物

**约束布局ConstraintLayout**








