## 无原生类型

scala 没有 java 中的原生类型，所以可以对数字等基础类型调用方法

| 类型    | 描述                                      |
| ------- | ----------------------------------------- |
| Unit    | 等于 void。Unit 只有一个实例值，写成 ()。 |
| Null    | null 或空引用                             |
| Nothing | 所有其他类的子类                          |
| Any     | 所有其他类的超类                          |
| AnyRef  | 所有引用类 (reference class) 的基类       |

### 多行字符串的表示方法

用三个双引号来表示分隔符：

```Scala
val foo = """sun
    ting
    is"""
```

### Null 值

空值是 scala.Null 类型。

Scala.Null 和 scala.Nothing 是用统一的方式处理 Scala 面向对象类型系统的某些 "边界情况" 的特殊类型。

Null 类是 null 引用对象的类型，它是每个引用类（继承自 AnyRef 的类）的子类。Null 不兼容值类型。

## 变量和常量

#### 声明变量

```Scala
var myVar : String = "Foo"
var myVar : String = "Too"
```

#### 声明常量

```Scala
val myVal : String = "Foo"
```

#### 多声明

```Scala
val xmax, ymax = 100  // xmax, ymax都声明为100
```

#### 类型推断

```Scala
var myVar = 10;
val myVal = "Hello, Scala!";
```

#### 声明元组

```Scala
scala> val pa = (40,"Foo")
pa: (Int, String) = (40,Foo)
```





































