### classpath的作用

Java的源码格式是 .java，编译后生成了 .class 文件，class 文件才是JVM能够识别的文件格式。如果代码中用到了一个类，那么 JVM 需要知道到哪里去找这个类的 .class 文件。

`classpath` 是 JVM 用到的一个环境变量，它用来指示 JVM 如何搜索 `class`。



### classpath的形式

它设置的搜索路径与操作系统相关

- 在 Windows 系统上，用 `;` 分隔，带空格的目录用 `""` 括起来，可能长这样：

```shell
C:\work\project1\bin;C:\shared;"D:\My Documents\project1\bin"
```

- 在 Linux 系统上，用`:` 分隔，可能长这样：

```shell
/usr/shared:/usr/local/bin:/home/liaoxuefeng/bin
```



### JVM搜索classpath

假设我们的 classpath是`.;C:\work\project1\bin;C:\shared`，那么JVM加载 `abc.xyz.Hello` 这个类时，会依此查找：

- <当前目录>\abc\xyz\Hello.class
- C:\work\project1\bin\abc\xyz\Hello.class
- C:\shared\abc\xyz\Hello.class

如果 JVM 在某个路径下找到了对应的 `class` 文件，就不再往后继续搜索。如果所有路径下都没有找到，就报错。



### 设置 classpath

`classpath` 的设定有以下几种情况：

##### 在系统环境变量中设置 `classpath` 环境变量

- 不推荐，那样会污染整个系统环境

##### 在启动 JVM 时设置 `classpath` 变量

- 推荐

- 就是给 `java` 命令传入 `-classpath` 或 `-cp` 参数
  - java -classpath .;C:\work\project1\bin;C:\shared   abc.xyz.Hello
  - java -cp .;C:\work\project1\bin;C:\shared   abc.xyz.Hello

##### JVM 默认的 `classpath` 为当前目录`.`

- 即只在当前目录搜索 `Hello.class`：java   abc.xyz.Hello

- 对于绝大多数情况都够用了

##### IDE 自动传入的 `-cp` 参数是当前工程的 `bin` 目录和引入的 jar 包

##### 把目录打一个JAR包

- 如果有很多`.class` 文件，散落在各层目录中，肯定不便于管理，把目录打一个JAR包就方便多了

- 如果JAR包中不包括 /META-INF/MANIFEST.MF 文件，则需要在命令中指定主类：java -cp ./hello.jar abc.xyz.Hello，这样 JVM 会自动在 `hello.jar` 文件里去搜索某个类

- 如果包含了 /META-INF/MANIFEST.MF 文件，则不必在命令行指定启动的类名：java -jar hello.jar

- jar 包还可以包含其它 jar 包，这时就要在 `MANIFEST.MF` 文件里配置 `classpath` 了





### Reference

- [classpath 和 jar](https://www.liaoxuefeng.com/wiki/1252599548343744/1260466914339296)