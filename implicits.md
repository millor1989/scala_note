### 隐式参数

方法可以有隐式参数列表，由参数列表开头的 `implicits` 关键字标记。如果参数列表中的参数没有如常传递，Scala将尝试获取正确的隐式值，并自动传递。

Scala 查找参数的位置分为两类：

- Scala 在调用包含有隐式参数块的方法时，将首先查找可以直接访问的饮食定义和隐式参数。
- 然后，在所有伴生对象中查找与隐式候选类型相关的有隐式标记的成员。

如下，定义了一个方法 `sum`，使用 `Monoid` 类的 `add` 和 `unit` 方法计算一个列表元素的总和。注意，**隐式值不能是顶级值**。

```scala
abstract class Monoid[A] {
  def add(x: A, y: A): A
  def unit: A
}

object ImplicitTest {
  implicit val stringMonoid: Monoid[String] = new Monoid[String] {
    def add(x: String, y: String): String = x concat y
    def unit: String = ""
  }
  
  implicit val intMonoid: Monoid[Int] = new Monoid[Int] {
    def add(x: Int, y: Int): Int = x + y
    def unit: Int = 0
  }
  
  def sum[A](xs: List[A])(implicit m: Monoid[A]): A =
    if (xs.isEmpty) m.unit
    else m.add(xs.head, sum(xs.tail))
    
  def main(args: Array[String]): Unit = {
    println(sum(List(1, 2, 3)))       // uses IntMonoid implicitly
    println(sum(List("a", "b", "c"))) // uses StringMonoid implicitly
  }
}
```

其中，类 `Monoid` 定义了一个名为 `add` 的操作，它将一对 `A` 类型的值相加并返回一个 `A`，以及一个名为 `unit` 的操作，用来创建一个（特定的） `A` 类型的值。

`StringMonoid` 和 `IntMonoid` 是可以隐式地使用的对象。

方法 `sum` 接收一个 `List[A]`，并返回 `A` 的值，它还有一个隐式参数 `m`——这意味着，如果 Scala 可以找到隐式对象 `Monoid[A]` 用于隐式参数 `m`，那么在调用 `sum` 方法时只用传入参数 `xs`。

### 隐式转换

使用函数类型 `S => T` 的隐式值来定义类型 `S` 到类型 `T` 的隐式转换，也可以用一个可转换为所需指的隐式方法来定义。

隐式转换的两种场景：

- 表达式 `e` 的类型为 `S`，并且类型 `S` 不符合表达式的期望类型 `T`
- 在一个类型为 `S` 的实例对象 `e` 中调用 `e.m`，如果被调用的 `m` 并没有在类型 `S` 中声明。

第一种情况下会搜索结果类型为 `T` 的隐式转换，第二种情况下，搜索转换结果包含成员 `m` 的转换。

如果上下文范围内存在隐式方法 `List[A] => Ordered[List[A]]`，以及隐式方法 `Int => Ordered[Int]`，那么对如下两个类型为 `List[Int]` 的列表的操作是合法的：

```scala
List(1,2,3) <= List(4,5)
```

`scala.Predef.intWrapper` 已经自动提供了一个隐式方法 `Int => Ordered[Int]`。如下，为提供隐式方法 `List[A] => Ordered[List[A]]` 的例子：

```scala
import scala.language.implicitConversions

implicit def list2ordered[A](x: List[A])
    (implicit elem2ordered: A => Ordered[A]): Ordered[List[A]] =
  new Ordered[List[A]] { 
    //replace with a more useful implementation
    def compare(that: List[A]): Int = 1
  }
```

自动导入的 `scala.Predef` 声明了几个预定义类型（比如，`Pair`）和方法（比如，`assert`），同时也声明了一些隐式转换。调用接收 `java.lang.Integer` 作为参数的 Java 方法时，可以传入 `Scala.Int` 是因为 `Predef` 包含了如下隐式转换：

```scala
import scala.language.implicitConversions

implicit def int2Integer(x: Int) =
  java.lang.Integer.valueOf(x)
```

由于不加选择地使用隐式转换会导致陷阱，**编译器会在编译隐式转换时发出警告**。若要关闭警告，可以执行如下操作之一：

- 将 `scala.language.implicitConversions` 导入到隐式转换定义的上下文范围内
- 启用编译器选项 `-language:implicitConversions`

