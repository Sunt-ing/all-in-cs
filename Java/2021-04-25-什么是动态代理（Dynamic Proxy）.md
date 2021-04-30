### 动态代理（Dynamic Proxy）

Java中，动态代理就是用来不用编写实现类而直接创建某个interface的实例。其实本质上就是让JVM在运行期帮我们编写了一个实现类。

要导入的包包括：

```Java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
```

比如我们要创建的实例属于Hello接口：

```java
interface Hello {
    void morning(String name);
}
```

实现方式：

```java
    public static void main(String[] args) {
        // 实现一个handler，用来应对对实现的对象的调用
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println(method);
                // 应对某一种方法
                if (method.getName().equals("morning")) {
                    System.out.println("Good morning, " + args[0]);
                }
                return null;
            }
        };
        Hello hello = (Hello) Proxy.newProxyInstance(
            Hello.class.getClassLoader(), // 传入ClassLoader
            new Class[] { Hello.class }, // 传入要实现的接口
            handler); // 传入处理调用方法的InvocationHandler
        hello.morning("Bob");
    }
```

所以步骤就三步：

- 实现一个InvocationHandler，重载invoke方法，负责实现接口的各个方法
- 通过Proxy.newProxyInstance方法创建一个实例，它需要三个参数：
  - 需要实现的接口的ClassLoader
  - 需要实现的接口数组，至少需要传入一个接口进去
  - 用来实现接口方法的InvocationHandler

所以，动态代理其实本质上就是JVM在运行期动态创建class字节码的过程。不需要源码，直接生成字节码。





来源：https://www.liaoxuefeng.com/wiki/1252599548343744/1264804593397984





