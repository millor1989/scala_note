### 注解

注解将元信息与定义相关联。

注解作用于其后的第一个定义或声明。在定义或声明之前可以有多个注解，不区分先后顺序。

#### 1、确保编码正确性

比如 `@tailrec` 确保方法是尾递归。对于计算阶乘的方法：

```scala
import scala.annotation.tailrec

def factorial(x: Int): Int = {

  @tailrec
  def factorialHelper(x: Int, accumulator: Int): Int = {
    if (x == 1) accumulator else factorialHelper(x - 1, accumulator * x)
  }
  factorialHelper(x, 1)
}
```

可以正常编译。而修改后的如下代码，因为不是尾递归，将编译失败：

```scala
import scala.annotation.tailrec

def factorial(x: Int): Int = {
  @tailrec
  def factorialHelper(x: Int): Int = {
    if (x == 1) 1 else x * factorialHelper(x - 1)
  }
  factorialHelper(x)
}
```

#### 2、影响代码生成

`@inline` 注解会影响生成的代码（即 jar 文件与不使用注解是有不同的字节）。内联表示在调用点插入备调用方法体中的代码。生成的字节码更长，但可能运行的更快。`@inline` 注解不确保内联，但是当满足某些生成代码大小的启发式算法时，它才会触发编译器执行此操作。

#### 3、Java 注解

编写与 Java 互操作的 Scala 代码时，要确保在开启 `-target:jvm-1.8` 选项时使用 Java 注解。

Java 注解有用户自定义元数据的形式，注解的一个关键特性是它们依赖于指定 name-value 对来初始化它们的元素。如下注解：

```java
@interface Source {
  public String URL();
  public String mail();
}
```

它的使用：

```java
@Source(URL = "https://coders.com/",
        mail = "support@coders.com")
public class MyClass extends HisClass ...
```

Scala 中实例化 Java 注解，必须使用命名参数：

```scalal
@Source(URL = "https://coders.com/",
        mail = "support@coders.com")
class MyScalaClass ...
```

如果注解只包含一个元素，这种语法就显得繁琐。如果将元素名称指定为 `value`，则可以使用类似构造函数的语法在 Java 中使用：

```java
@interface SourceURL {
    public String value();
    public String mail() default "";
}
```

使用方式：

```java
@SourceURL("https://coders.com/")
public class MyClass extends HisClass ...
```

Scala 使用方式：

```scala
@SourceURL("https://coders.com/")
class MyScalaClass ...
```

由于 `mail` 元素在定义时设有默认值，所以不需显式提供值。如果要提供值，则 Java 中不能混合使用两种方式：

```java
@SourceURL(value = "https://coders.com/",
           mail = "support@coders.com")
public class MyClass extends HisClass ...
```

但是 Scala 提供了更大的灵活性：

```scala
@SourceURL("https://coders.com/",
           mail = "support@coders.com")
    class MyScalaClass ...
```

