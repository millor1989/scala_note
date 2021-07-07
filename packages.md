### 包

Scala 使用包来创建命名空间，以创建模块化程序。

在 Scala 文件的头部声明一个或多个包名来创建包。

```scala
package users

class User
```

一个惯例是将包命名为与包含 Scala 文件的目录名相同。但是，**Scala 并未对文件布局做限制**。Scala 允许包嵌套，并提供了对范围和封装的更好控制。

```scala
package users {
  package administrators {
    class NormalUser
  }
  package normalusers {
    class NormalUser
  }
}
```

包名称应全部为小写。

### 导入

`import` 语句用于导入其它包的成员（类、特征、函数等）。使用相同包的成员不需要 `import` 语句。

导入语句可以有选择性：

```scala
// 导入包 users 中的所有成员
import users._
// 导入类 User
import users.User
// 仅导入选择的成员
import users.{User, UserPreferences}
// 导入类并且设置别名
import users.{UserPreferences => UPrefs}
```

Scala 可以在任何需要的地方使用导入：

```scala
def sqrtplus1(x: Int) = {
  import scala.math.sqrt
  sqrt(x) + 1.0
}
```

如果存在命名冲突并且你需要从项目的根目录导入，那么要在包名称前加上 `_root_`：

```scala
package accounts

import _root_.users._
```

注意：包 `scala` 和 `java.lang` 以及 `object Predef` 是默认导入的。

### 包对象

Scala 提供包对象作为整个包中方便的共享使用的容器。

包对象中可以定义任何内容，不仅是变量和方法。比如，包对象经常用于保存包级作用域的类型别名和隐式转换。包对象还可以继承 Scala 的类和特质。

按照惯例，包对象的代码通常放在名为 `package.scala` 的源文件中。

每个包都允许有一个包对象。在包对象中的任何定义都被认为是包自身的成员。

假设有一个类 `Fruit` 和三个 `Fruit` 对象在包 `gardening.fruits` 中：

```scala
package gardening.fruits

case class Fruit(name: String, color: String)
object Apple extends Fruit("Apple", "green")
object Plum extends Fruit("Plum", "blue")
object Banana extends Fruit("Banana", "yellow")
```

将变量 `planted` 和方法 `showFruit` 直接放入包 `gardening` 中：

```scala
package gardening
package object fruits {
  val planted = List(Apple, Plum, Banana)
  def showFruit(fruit: Fruit): Unit = {
    println(s"${fruit.name}s are ${fruit.color}")
  }
}
```

对象 `PrintPlanted` 用导入类 `Fruit` 相同的方式来导入 `planted` 和 `showFruit`，在导入包 `gardening.fruits` 时使用通配符：

```scala
import gardening.fruits._
object PrintPlanted {
  def main(args: Array[String]): Unit = {
    for (fruit <- planted) {
      showFruit(fruit)
    }
  }
}
```

包对象和其它对象类似可以使用继承来构建：

```scala
package object fruits extends FruitAliases with FruitHelpers {
  // helpers and variables follows here
}
```

