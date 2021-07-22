### 基础

#### 1、表达式（Expression）

表达式是可计算的语句。

```scala
 1 + 1
```

可以用 `println` 输出表达式的结果。

```scala
println(1) // 1
println(1 + 1) // 2
println("Hello!") // Hello!
println("Hello," + " world!") // Hello, world!
```

##### 1.1、常量（Values）

可以用 `val` 关键字给表达式结果命名。

```scala
val x = 1 + 1
println(x) // 2
```

对于结果，比如 `x` 的命名，被称为常量。引用一个常量不会再次计算。

常量不能被重新赋值。

常量的类型可以被推断，也可以显式的指明常量类型：

```scala
val x: Int = 1 + 1
```

标识符和类型之间用 `:` 分隔。

##### 1.2、变量（Variables）

用 `var` 关键字定义变量，变量可以重新赋值，可以显式指明类型。

```
var x = 1 + 1
x = 3 // This compiles because "x" is declared with the "var" keyword.
println(x * x) // 9

var x: Int = 1 + 1
```

引用变量也不会再次计算。

#### 2、代码块（Blocks）

用`{}` 包括的，几个表达式的组合称为**代码块**，代码块最后一个表达式的结果是整个代码块的结果。

```scala
println({
  val x = 1 + 1
  x + 1
}) // 3
```

#### 3、函数（Function）

**函数是带有参数的表达式**。

匿名函数：

```scala
(x: Int) => x + 1
```

`=>` 的左边是参数列表，右边是包含参数的表达式。

给函数命名：

```scala
val addOne = (x: Int) => x + 1
println(addOne(1)) // 2
```

函数可以有多个参数、也可以没有参数：

```scala
val add = (x: Int, y: Int) => x + y
println(add(1, 2)) // 3


val getTheAnswer = () => 42
println(getTheAnswer()) // 42
```

#### 4、方法（Method）

方法的表现和行为和函数类似。

方法由 `def` 关键字定义。`def` 后面是方法名、参数列表、返回类型和方法体：

```scala
def add(x: Int, y: Int): Int = x + y
println(add(1, 2)) // 3
```

方法可以有**多个参数列表**：

```scala
def addThenMultiply(x: Int, y: Int)(multiplier: Int): Int = (x + y) * multiplier
println(addThenMultiply(1, 2)(3)) // 9
```

方法也可以**没有参数列表**：

```scala
def name: String = System.getProperty("user.name")
println("Hello, " + name + "!")
```

方法可以有多行表达式，最后一行表达式是方法的返回值：

```scala
def getSquareString(input: Double): String = {
  val square = input * input
  square.toString
}
println(getSquareString(2.5)) // 6.25
```

Scala 中有`return` 关键字，但是很少用。

方法可以没有返回值—— `Unit`：

```scala
  def greet(name: String): Unit =
    println(prefix + name + suffix)
```

##### 4.1、命名参数

```scala
def printName(first: String, last: String): Unit = {
  println(first + " " + last)
}

printName("John", "Smith")  // Prints "John Smith"
printName(first = "John", last = "Smith")  // Prints "John Smith"
printName(last = "Smith", first = "John")  // Prints "John Smith"
```

使用命名参数时，可以不按照参数定义的顺序。可以只对部分参数命名，此时，未命名参数要按照方法签名中的参数顺序放置。

Scala 中可以**为参数提供默认值**，在调用的时候，可以忽略具有默认值的参数：

```scala
def log(message: String, level: String = "INFO") = println(s"$level: $message")

log("System starting")  // prints INFO: System starting
log("User not found", "WARNING")  // prints WARNING: User not found
```

但是如果有多个提供默认值得参数，忽略一个参数后，传入其它参数传入就必须带参数名：

```scala
class Point(val x: Double = 0, val y: Double = 0)

val point1 = new Point(y = 1)
```

Java 代码调用 Scala 代码时，Scala 中的默认参数都是必填的。

##### 4.1、传名参数

*传名参数* 仅在被使用时触发实际参数的求值运算，与 *传值参数* 刚好相反。在参数类型前使用 `=>` 将参数标记为传名参数：

```scala
def calculate(input: => Int) = input * 37
```

传名参数的优点是，如果它们在函数体中没有被使用，则不会对它们进行求值；而传值参数的优点是仅被计算一次。如下为一个实现 while 循环的例子：

```scala
def whileLoop(condition: => Boolean)(body: => Unit): Unit =
  if (condition) {
    body
    whileLoop(condition)(body)
  }

var i = 2

whileLoop (i > 0) {
  println(i)
  i -= 1
}  // prints 2 1
```

`whileLoop` 方法使用多参数列表分别获取循环条件和循环体。只有 `condition` 为 true 时才计算 `body`，而 `condition` 为 false 时，不会计算 `body`，因为 `body` 为传名参数。

对于计算密集型的参数或长时间运行的代码块，使用传名参数可以帮助提高性能。

#### 5、类（Class）

使用 `class` 关键字后跟类名和**构造参数**来定义类：

```scala
class Greeter(prefix: String, suffix: String) {
  def greet(name: String): Unit =
    println(prefix + name + suffix)
}
```

可以使用`new` 关键字创建一个类的实例：

```scala
val greeter = new Greeter("Hello, ", "!")
greeter.greet("Scala developer") // Hello, Scala developer!
```

#### 6、样例类（Case Class）

样例类是一种特殊的类，一般用于不可变对象，并且可以做值比较。

```scala
case class Point(x: Int, y: Int)
```

不用 `new` 关键字就可以来实例化样例类，因为样例类有一个默认的 `apply` 方法来负责对象的创建：

```scala
val point = Point(1, 2)
val anotherPoint = Point(1, 2)
val yetAnotherPoint = Point(2, 2)
```

样例类的参数是 `public` 的 `val`，所以不**能给样例类的实例重新赋值**。样例类中使用 `var` 参数也是可以的，但是不推荐。

可以使用样例类的 `copy` 方法对样例类实例进行**浅拷贝**，并且可以指定构造参数来做一些改变：

```scala
case class Message(sender: String, recipient: String, body: String)
val message4 = Message("julien@bretagne.fr", "travis@washington.us", "Me zo o komz gant ma amezeg")
val message5 = message4.copy(sender = message4.recipient, recipient = "claire@bourgogne.fr")
message5.sender  // travis@washington.us
message5.recipient // claire@bourgogne.fr
message5.body  // "Me zo o komz gant ma amezeg"
```

其中 `copy` 方法重新定义了`sender`和`recipient`，但是`body`是相同的。

样例类实例**比较**：

```scala
if (point == anotherPoint) {
  println(point + " and " + anotherPoint + " are the same.")
} else {
  println(point + " and " + anotherPoint + " are different.")
} // Point(1,2) and Point(1,2) are the same.

if (point == yetAnotherPoint) {
  println(point + " and " + yetAnotherPoint + " are the same.")
} else {
  println(point + " and " + yetAnotherPoint + " are different.")
} // Point(1,2) and Point(2,2) are different.
```

#### 7、对象（Object）

对象可以被看作它本身的类的单实例，使用 `object` 关键字定义对象：

```scala
object IdFactory {
  private var counter = 0
  def create(): Int = {
    counter += 1
    counter
  }
}
```

可以使用对象名来访问对象：

```scala
val newId: Int = IdFactory.create()
println(newId) // 1
val newerId: Int = IdFactory.create()
println(newerId) // 2
```

对象——单例对象，与惰性变量一样，单例对象是延迟创建的——第一次被使用时创建。

当对象定义于顶层时(即没有包含在其他类中)，单例对象只有一个实例。

当对象定义在一个类或方法中时，单例对象表现得和惰性变量一样。

##### 伴生对象

单例对象和某个类共享一个名称时，单例对象被称为伴生对象；类则被称为单例对象的伴生类。

类和它的伴生对象可以互相访问其私有成员。

可以使用伴生对象来定义那些在伴生类中不依赖于类的实例化对象而存在的成员变量或方法。

```scala
import scala.math._

case class Circle(radius: Double) {
  import Circle._
  def area: Double = calculateArea(radius)
}

object Circle {
  private def calculateArea(radius: Double): Double = Pi * pow(radius, 2.0)
}

val circle1 = Circle(5.0)

circle1.area
```

##### 提取器对象

提取器对象是一个包含有 `unapply` 方法的单例对象。`apply` 方法如同构造器，接受参数创建对象，而 `unapply` 恰好相反，接收一个实例对象然后返回最初创建它所用的参数。提取器常用在模式匹配和偏函数中。

```scala
import scala.util.Random

object CustomerID {

  def apply(name: String) = s"$name--${Random.nextLong}"

  def unapply(customerID: String): Option[String] = {
    val stringArray: Array[String] = customerID.split("--")
    if (stringArray.tail.nonEmpty) Some(stringArray.head) else None
  }
}

val customer1ID = CustomerID("Sukyoung")  // Sukyoung--23098234908
customer1ID match {
  case CustomerID(name) => println(name)  // prints Sukyoung
  case _ => println("Could not extract a CustomerID")
}
```

变量调用可以使用模式引入变量，提取器可以用来初始化这个变量，使用`unapply` 方法生成值：

```scala
val customer2ID = CustomerID("Nico")
val CustomerID(name) = customer2ID
println(name)  // prints Nico
```

调用提取器时，如果没有匹配的值，会抛出`scala.MatchError`。

`unapply` 方法的返回值应当符合下面的一条：

- 如果只用来判断真假，可以返回一个 `Boolean` 类型的值。
- 如果只提取单个 T 类型的值，可以返回 `Option[T]`。
- 如果提取多个值，类型为 `T1,...,Tn`，可以把它们放在一个可选的元组 `Option[(T1,...,Tn)]`。

如果提取的值得数量不是固定的，可以使用 `unapplySeq` 方法来定义提取器，该方法返回 `Option[Seq[T]]`。常见的例子有，用 `case List(x,y,z) =>` 来解构一个列表 `List`，以及用一个正则表达式 `Regex` 来分解一个字符串 `String`，例如 `case r(name, remainingFields @ _*) =>`

#### 8、特质（Trait）

特质是包含某些字段和方法的类型。可以组合多个特质。使用 `trait` 关键字定义特质：

```scala
trait Greeter {
  def greet(name: String): Unit
}
```

特质可以有默认实现：

```scala
trait Greeter {
  def greet(name: String): Unit =
    println("Hello, " + name + "!")
}
```

使用 `extends` 关键字来继承特质，使用 `override` 关键字来覆盖默认的实现：

```scala
class DefaultGreeter extends Greeter

class CustomizableGreeter(prefix: String, postfix: String) extends Greeter {
  override def greet(name: String): Unit = {
    println(prefix + name + postfix)
  }
}

val greeter = new DefaultGreeter()
greeter.greet("Scala developer") // Hello, Scala developer!

val customGreeter = new CustomizableGreeter("How are you, ", "?")
customGreeter.greet("Scala developer") // How are you, Scala developer?
```

一个类可以继承多个特质。

#### 9、主方法（Main Method）

主方法是程序的入口。JVM 要求一个名为 `main` 的主方法，接受一个字符串数组作为参数。使用对象定义一个主方法：

```scala
object Main {
  def main(args: Array[String]): Unit =
    println("Hello, Scala developer!")
}
```
