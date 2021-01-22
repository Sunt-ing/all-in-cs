#### 抽象（abstract）方法声明

```scala
def functionName ([参数列表]) : [return type]
```

#### 方法隶属对象

```scala
def functionName ([参数列表]) : [return type] = { }
```

#### Unit 等于 Java 的 **void**

```scala
object Hello{
  def printMe( ) : Unit = {
   println("Hello, Scala!")
  }
}
```

#### 函数

```scala
val multiplier = (i:Int) => i * 10  
```

#### 闭包

闭包就是捕获了自由变量的函数，其对自由变量的修改对闭包外可见

```scala
var factor = 3  
val multiplier = (i:Int) => i * factor  
```















































