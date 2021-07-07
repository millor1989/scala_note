### 统一类型（unified-types）

Scala中，所有的值（包括数值和函数）都有类型。

![unified-types](/assets/unified-types-diagram.svg)

#### 1、类型层次结构

`Any` 是所有类型的超类型，即顶级类型。它定义了一些通用的方法，比如`equals`、`hashCode`、`toString`。

`Any` 有两个直接子类：`AnyVal` 和 `AnyRef`。

- `AnyVal` 代表值类型。

  9个预定义的非空值类型：`Double`、`Float`、`Long`、`Int`、`Short`、`Byte`、`Char`、`Unit`和`Boolean`。`Unit`  是不带任何意义的值类型，用于表示没有任何返回的函数的返回值类型。

- `AnyRef`代表引用类型。

  所有的非值类型都被定义为引用类型。

  每个用户自定义的类型都是 `AnyRef` 的子类型。

  如果 `Scala` 被应用到 Java 的运行环境中，`AnyRef` 相当于 `java.lang.Object`。

#### 2、类型转换

值类型只可以按照下图的方向进行转换，反过来则不行：

![Scala Type Hierarchy](/assets/type-casting-diagram.svg)

#### 3、`Nothing` 和 `Null`

`Nothing` 是所有类型的子类型，也称为底部类型。没有值是 `Nothing` 类型的，它的作用是给出非正常终止的信号。

`Null` 是所有引用类型的子类型。它有一个单例值由 `null` 关键字所定义。`Null` 在Scala 代码中几乎没用，主要用于满足和其它 JVM 语言的互操作性。