### 问题

1、scala object，spark 应用中，Dataset 的 `foreachPartition` 中使用局部变量 Redis 连接池时，报错——连接池不能序列化。而将连接池定义为object 常量属性后，`foreachPartition` 中再使用它就没有问题了。不晓得为什么……

2、序列化是针对对象的状态，即堆内存的，只有堆内存中的内容可以被序列化，静态成员属于类的状态，位于 JVM 方法区中，不会被序列化，序列化信息中不会包含静态成员；读取静态属性是从方法区读取的，不存在序列化的问题。

##### 3、if ... else ... 优先级问题

```scala
val res =
  if (a) {
    xxxx
  } else {
    yyyy
  }
  + zzzz
```

因为知道 Scala `if else` 表达式是有返回值的，期望：条件 `a` 成立的时候，结果是 `xxxx + zzzz`，  条件 `a` 不成立的时候结果是 `yyyy + zzzz`。**但是**，经过实测，条件 `a` 成立的时候，结果是 `xxxx`（不会执行 `else` 对应的花括号后的运算！！！），条件 `a` 不成立的时候，结果是 `yyyy + zzzz`。即使 `xxxx`、`yyyy` 分别是代码块，仍然是这种结果。

不知道是不是优先级的问题（应该不是省略花括号的问题，因为 `if else` 分支是代码块的时候是不能省略花括号的），难道是 `else` 的优先级是最低的？？因为，经过测试，给 `if else` 加上 `()` 后可以达到期望：

```scala
val res =
  (if (a) {
    xxxx
  } else {
    yyyy
  })
  + zzzz
```

4.`List.tail` 返回的是一个 `List`（包含除了 `List.head` 之外的所有元素），不是最后一个元素。返回 `List` 最后一个元素应该使用 `List.last`。



一个不错的 [blog](https://dzone.com/users/4039047/danielciocirlan.html)