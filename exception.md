### 异常处理

Scala 的异常处理和 Java 类似。

#### 1、抛出异常

使用 `throw` 将异常抛出：

```scala
throw new IllegalArgumentException
```

#### 2、捕获异常

使用 `try ... catch` 捕获异常，也支持 `finally` 语句。其中 `try ...` 分支语句和 `catch ...` 分支语句可以有返回值，并作为 `try ... catch` 语句的返回值。

```scala
import java.io.FileReader
import java.io.FileNotFoundException
import java.io.IOException

object Test {
  def main(args: Array[String]) {
    val res: String =
      try {
        val f = new FileReader("input.txt")
        "success"
      } catch {
        case ex: FileNotFoundException => {
          println("Missing file exception")
          "file not found"
        }
        case ex: IOException => {
          println("IO Exception")
          "io exception"
        }
      } finally {
        println("Exiting finally...")
      }

    println(res)
  }
}
```

`finally` 语句没有返回值。