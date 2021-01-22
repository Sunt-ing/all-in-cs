### JAR包是什么

JAR 文件的全称 Java Archive File（Java 档案文件），其实就是把一个Java项目中的 .java 文件编译成 .class 文件后的东西外加一个 META-INF/MANIFEST.MF 文件一起压缩成一个ZIP包。MANIFEST.MF是一个清单文件，记录这个JAR包的Main class。下面就是一个某个JAR包中的MANIFEST.MF文件：

```yaml
Manifest-Version: 1.0
Main-Class: org.apache.flink.table.examples.java.basics.WordCountSQL
```

把项目打包成一个 JAR 包的好处就在于给别人用时，别人只需在 CLASSPATH 环境变量中添加这个 JAR 包，Java 虚拟机就会根据路径查找相应的文件，整个流程更加方便、优雅。



### IDEA编译Jar方法

你需要指定你要把哪一个module编译成JAR包，以及其中哪一个.class文件是我们的主类。JAR包中也可以加入一些其他的模块中的文件，这样就会在这个JAR包中添加更多的依赖。

过程

- 在菜单中选择 File->project structure
- 在弹出的窗口中左侧选中 "Artifacts"，点击 "+" 选择 JAR，然后选择 "from modules with dependencies"，然后把你想要打包的主类所在的那个module选中
- 在配置窗口中配置 "Main Class"，就是选中你想打包的主类
- 配置 “Directory for META-INF/MAINFEST.MF” 文件的存储位置，点击 OK 进入下一步。我个人建议选择 “extract to the target JAR”，这样所有依赖的 jar 包都会放在生成的 jar 包中
- 完成后，点击 OK，Apply 等按钮，回到 IDEA 的主菜单，选择 “Build - Build Artifacts” 下的 “Build” 或者 “Rebuild” 即可生成最终的可运行的 jar
- 在项目的 out/artifacts 目录下，你可以找到你的JAR包





### 参考

- https://blog.csdn.net/xuemengrui12/article/details/74984731