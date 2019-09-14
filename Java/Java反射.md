# 什么是反射

* 在运行中获取任何类的所有属性和方法；对于任意一个对象，能够调用它的任意方法和属性

* 功能
  * 在运行时判断任意一个对象所属的类；
  * 在运行时构造任意一个类的对象；
  * 在运行时判断任意一个类所具有的成员变量和方法（通过反射甚至可以调用private方法）；
  * 在运行时调用任意一个对象的方法

# 反射的主要用途

* IDE中输入一个对象想调用其方法或属性时，按点号会自动列出
* Spring框架（通过XML配置Bean），根据配置文件运行时动态加载不同的对象或类

# 反射的实现

* 获得 Class 对象

  * `class.getClass()`

    ```java
    StringBuilder str = new StringBuilder("123");
    Class<?> klass = str.getClass();
    ```

  * `Class.forName()`

    ```java
    public static Class<?> forName(String className)
    //比如在 JDBC 开发中常用此方法加载数据库驱动:
    //...java
     Class.forName(driver);
    ```

  * `ClassLoader.loadClass()`

  *  直接获取对象的Class

    ```java
    Class<?> klass = int.class;
    Class<?> classInt = Integer.TYPE;
    ```

* 判断是否为某个类的实例

  * `instanceof`关键字

  * Class对象的`isInstance()`

    ```java
    public native boolean isInstance(Object obj);
    ```

* 创建实例

  * `Class.forName()`
  * `ClassLoader.loadClass()`

## Class.forName与Classloader.loadClass区别

* 初始化不同

  * `Class.forName()`会对类初始化；`loadClass()`只会装载和链接
  * `forName()`在类加载时会执行静态代码块（初始化）；`loadClass()`只有在调用`newInstance()`时才会执行静态代码块

* 类加载器不同

  * `Class.forName(String)`只有一个参数，使用调用forName方法代码的类加载器

  * `ClassLoader.loadClass()`是一个实例方法（非静态方法），调用时需要指定类加载器

    ```java
    ClassLoader classLoader = new ClassLoader() {
                @Override
                public Class<?> loadClass(String name) throws ClassNotFoundException {
                    return super.loadClass(name);
                }
            };
    ```

    

*  获取方法
   *  `getDeclaredMethods()`
      *  返回类或接口的所有方法，不包括继承的方法
   *  `getMethods()` 
      *  返回某类所有public方法，包括其继承类的public方法
   *  `getMethod()` 
      *  返回特定的方法，第一个参数为方法名称，后面参数为方法的参数的Class类型

## 获取构造器信息

获取类构造器的用法与上述获取方法的用法类似。主要是通过Class类的getConstructor方法得到Constructor类的一个实例，而Constructor类有一个newInstance方法可以创建一个对象实例:

此方法可以根据传入的参数来调用对应的Constructor创建对象实例。

## 获取类的成员变量（字段）信息

主要是这几个方法，在此不再赘述：

* getFiled：访问公有的成员变量
* getDeclaredField：所有已声明的成员变量，但不能得到其父类的成员变量

getFileds 和 getDeclaredFields 方法用法同上（参照 Method）。

## 调用方法
当我们从类中获取了一个方法后，我们就可以用 invoke() 方法来调用这个方法。invoke 方法的原型为:

```java
public Object invoke(Object obj, Object... args)
        throws IllegalAccessException, IllegalArgumentException,
           InvocationTargetException
```

下面是一个实例：

```java
public class test1 {
    public static void main(String[] args) throws IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException {
        Class<?> klass = methodClass.class;
        //创建methodClass的实例
        Object obj = klass.newInstance();
        //获取methodClass类的add方法
        Method method = klass.getMethod("add",int.class,int.class);
        //调用method对应的方法 => add(1,4)
        Object result = method.invoke(obj,1,4);
        System.out.println(result);
    }
}
class methodClass {
    public final int fuck = 3;
    public int add(int a,int b) {
        return a+b;
    }
    public int sub(int a,int b) {
        return a+b;
    }
}
```

## 利用反射创建数组

数组在Java里是比较特殊的一种类型，它可以赋值给一个Object Reference。下面我们看一看利用反射创建数组的例子：

```java
public static void testArray() throws ClassNotFoundException {
        Class<?> cls = Class.forName("java.lang.String");
        Object array = Array.newInstance(cls,25);
        //往数组里添加内容
        Array.set(array,0,"hello");
        Array.set(array,1,"Java");
        Array.set(array,2,"fuck");
        Array.set(array,3,"Scala");
        Array.set(array,4,"Clojure");
        //获取某一项的内容
        System.out.println(Array.get(array,3));
    }
```

其中的Array类为java.lang.reflect.Array类。我们通过Array.newInstance()创建数组对象，它的原型是:
    
```java
 public static Object newInstance(Class<?> componentType, int length)
        throws NegativeArraySizeException {
        return newArray(componentType, length);
    }
```

而 newArray 方法是一个 native 方法，它在 HotSpot JVM 里的具体实现我们后边再研究，这里先把源码贴出来：
    
 ```java
private static native Object newArray(Class<?> componentType, int length)
        throws NegativeArraySizeException;
 ```

源码目录：

> openjdk\hotspot\src\share\vm\runtime\reflection.cpp

```c++
arrayOop Reflection::reflect_new_array(oop element_mirror, jint length, TRAPS) {
  if (element_mirror == NULL) {
    THROW_0(vmSymbols::java_lang_NullPointerException());
  }
  if (length < 0) {
    THROW_0(vmSymbols::java_lang_NegativeArraySizeException());
  }
  if (java_lang_Class::is_primitive(element_mirror)) {
    Klass* tak = basic_type_mirror_to_arrayklass(element_mirror, CHECK_NULL);
    return TypeArrayKlass::cast(tak)->allocate(length, THREAD);
  } else {
    Klass* k = java_lang_Class::as_Klass(element_mirror);
    if (k->oop_is_array() && ArrayKlass::cast(k)->dimension() >= MAX_DIM) {
      THROW_0(vmSymbols::java_lang_IllegalArgumentException());
    }
    return oopFactory::new_objArray(k, length, THREAD);
  }
}
```

另外，Array 类的 set 和 get 方法都为 native 方法，在 HotSpot JVM 里分别对应 Reflection::array_set 和 Reflection::array_get 方法，这里就不详细解析了。
    
# 反射的一些注意事项

由于反射会额外消耗一定的系统资源，因此如果不需要动态地创建一个对象，那么就不需要用反射。

另外，反射调用方法时可以忽略权限检查，因此可能会破坏封装性而导致安全问题。


