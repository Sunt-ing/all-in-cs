类名首字母大写，方法名称首字母小写

程序文件的名称尽量与对象名称一样

以 "$" 开头的标识符为保留的 Scala 编译器产生的标志符使用



### 定义包

方法一：和 Java 一样，在文件的头定义包名

方法二：为了在一个文件中定义多个包，可以：

```Scala
package com.runoob {
  class HelloWorld 
}
```

默认引入 java.lang._ 、 scala._ 和 Predef._*



### 引用

```scala
import java.awt._  // 引入包内所有成员
 
def handler(evt: event.ActionEvent) { // java.awt.event.ActionEvent
  ...  // 因为引入了java.awt，所以可以省去前面的部分
}
```



```scala
// 用selector（选取器）引入包中的几个成员
import java.awt.{Color, Font}
 
// 重命名成员
import java.util.{HashMap => JavaHashMap}
 
// 隐藏成员
import java.util.{HashMap => _, _} // 引入了util包的所有成员，但是HashMap被隐藏了
```