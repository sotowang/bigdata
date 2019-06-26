# [ IOC](https://www.zhihu.com/question/23277575/answer/169698662)

**控制反转( Inversion of Control )**, 我觉得有必要先了解软件设计的一个重要思想：**依赖倒置原则（Dependency Inversion Principle ）**。

## 依赖倒置原则？

假设我们设计一辆汽车：先设计轮子，然后根据轮子大小设计底盘，接着根据底盘设计车身，最后根据车身设计好整个汽车。这里就出现了一个“依赖”关系：汽车依赖车身，车身依赖底盘，底盘依赖轮子。

<img src="https://pic3.zhimg.com/v2-c68248bb5d9b4d64d22600571e996446_b.png" data-caption="" data-rawwidth="1562" data-rawheight="186" class="origin_image zh-lightbox-thumb" width="1562" data-original="https://pic3.zhimg.com/v2-c68248bb5d9b4d64d22600571e996446_r.jpg">![img](https://pic3.zhimg.com/80/v2-c68248bb5d9b4d64d22600571e996446_hd.png)

**这样的设计看起来没问题，但是可维护性却很低。**假设设计完工之后，上司却突然说根据市场需求的变动，要我们把车子的轮子设计都改大一码。这下我们就蛋疼了：因为我们是根据轮子的尺寸设计的底盘，轮子的尺寸一改，底盘的设计就得修改；同样因为我们是根据底盘设计的车身，那么车身也得改，同理汽车设计也得改——整个设计几乎都得改！

我们现在换一种思路。我们先设计汽车的大概样子，然后根据汽车的样子来设计车身，根据车身来设计底盘，最后根据底盘来设计轮子。这时候，依赖关系就倒置过来了：轮子依赖底盘， 底盘依赖车身， 车身依赖汽车。

<img src="https://pic2.zhimg.com/v2-e64bf72c5c04412f626b21753aa9e1a1_b.png" data-caption="" data-rawwidth="1504" data-rawheight="190" class="origin_image zh-lightbox-thumb" width="1504" data-original="https://pic2.zhimg.com/v2-e64bf72c5c04412f626b21753aa9e1a1_r.jpg">![img](https://pic2.zhimg.com/80/v2-e64bf72c5c04412f626b21753aa9e1a1_hd.png)



这时候，上司再说要改动轮子的设计，我们就只需要改动轮子的设计，而不需要动底盘，车身，汽车的设计了。

依赖倒置原则——把原本的高层建筑依赖底层建筑“倒置”过来，变成底层建筑依赖高层建筑。高层建筑决定需要什么，底层去实现这样的需求，但是高层并不用管底层是怎么实现的。这样就不会出现前面的“牵一发动全身”的情况。

## 依赖注入

 **控制反转（Inversion of Control）** 就是依赖倒置原则的一种代码设计的思路。具体采用的方法就是所谓的**依赖注入（Dependency Injection）**。其实这些概念初次接触都会感到云里雾里的。说穿了，这几种概念的关系大概如下：

<img src="https://pic2.zhimg.com/v2-ee924f8693cff51785ad6637ac5b21c1_b.png" data-caption="" data-rawwidth="1398" data-rawheight="630" class="origin_image zh-lightbox-thumb" width="1398" data-original="https://pic2.zhimg.com/v2-ee924f8693cff51785ad6637ac5b21c1_r.jpg">

**为了理解这几个概念，我们还是用上面汽车的例子。**只不过这次换成代码。我们先定义四个Class，车，车身，底盘，轮胎。然后初始化这辆车，最后跑这辆车。代码结构如下：

![img](https://pic4.zhimg.com/80/v2-8ec294de7d0f9013788e3fb5c76069ef_hd.png) 

  

这样，就相当于上面第一个例子，**上层建筑依赖下层建筑**——每一个类的构造函数都直接调用了底层代码的构造函数。假设我们需要改动一下轮胎（Tire）类，把它的尺寸变成动态的，而不是一直都是30。我们需要这样改：

<img src="https://pic2.zhimg.com/v2-64e8b19eeb70d9cf87c27fe4c5c0fc81_b.png" data-caption="" data-rawwidth="534" data-rawheight="154" class="origin_image zh-lightbox-thumb" width="534" data-original="https://pic2.zhimg.com/v2-64e8b19eeb70d9cf87c27fe4c5c0fc81_r.jpg">![img](https://pic2.zhimg.com/80/v2-64e8b19eeb70d9cf87c27fe4c5c0fc81_hd.png)

由于我们修改了轮胎的定义，为了让整个程序正常运行，我们需要做以下改动：

<img src="https://pic3.zhimg.com/v2-82e0c12a1b26f7979ed9241e169affda_b.png" data-caption="" data-rawwidth="1186" data-rawheight="1452" class="origin_image zh-lightbox-thumb" width="1186" data-original="https://pic3.zhimg.com/v2-82e0c12a1b26f7979ed9241e169affda_r.jpg">



由此我们可以看到，仅仅是为了修改轮胎的构造函数，这种设计却需要**修改整个上层所有类的构造函数**！在软件工程中，**这样的设计几乎是不可维护的**——在实际工程项目中，有的类可能会是几千个类的底层，如果每次修改这个类，我们都要修改所有以它作为依赖的类，那软件的维护成本就太高了。

所以我们需要进行控制反转（IoC），及上层控制下层，而不是下层控制着上层。我们用依赖注入（Dependency Injection）这种方式来实现控制反转。**所谓依赖注入，就是把底层类作为参数传入上层类，实现上层类对下层类的“控制**”。这里我们用**构造方法传递的依赖注入方式**重新写车类的定义：

<img src="https://pic2.zhimg.com/v2-c920a0540ce0651003a5326f6ef9891d_b.png" data-caption="" data-rawwidth="1338" data-rawheight="1424" class="origin_image zh-lightbox-thumb" width="1338" data-original="https://pic2.zhimg.com/v2-c920a0540ce0651003a5326f6ef9891d_r.jpg">

 这里我们再把轮胎尺寸变成动态的，同样为了让整个系统顺利运行，我们需要做如下修改：

<img src="https://pic2.zhimg.com/v2-99ad2cd809fcb86dd791ff7f65fb1779_b.png" data-caption="" data-rawwidth="1344" data-rawheight="1424" class="origin_image zh-lightbox-thumb" width="1344" data-original="https://pic2.zhimg.com/v2-99ad2cd809fcb86dd791ff7f65fb1779_r.jpg">

 看到没？这里**我只需要修改轮胎类就行了，不用修改其他任何上层类。**这显然是更容易维护的代码。不仅如此，在实际的工程中，这种设计模式还有利于**不同组的协同合作和单元测试：**比如开发这四个类的分别是四个不同的组，那么只要定义好了接口，四个不同的组可以同时进行开发而不相互受限制；而对于单元测试，如果我们要写Car类的单元测试，**就只需要Mock一下Framework类传入Car就行了**，而不用把Framework, Bottom, Tire全部new一遍再来构造Car。

### 依赖注入方式

* Setter
* Interface
* Constructor
* Annotation

这里我们是采用的**构造函数传入**的方式进行的依赖注入。其实还有另外两种方法：**Setter传递**和**接口传递**。这里就不多讲了，核心思路都是一样的，都是为了实现**控制反转**。

 ![preview](https://pic2.zhimg.com/v2-861683acac47577c81f2b7493dd05649_r.jpg)

 

## 控制反转容器

什么是**控制反转容器(IoC Container)**呢？其实上面的例子中，对车类进行初始化的那段代码发生的地方，就是控制反转容器。

![img](https://pic3.zhimg.com/80/v2-c845802f9187953ed576e0555f76da42_hd.png) 

因为采用了依赖注入，在初始化的过程中就不可避免的会写大量的new。这里IoC容器就解决了这个问题。**这个容器可以自动对你的代码进行初始化，你只需要维护一个Configuration（可以是xml可以是一段代码），而不用每次初始化一辆车都要亲手去写那一大段初始化的代码**。这是引入IoC Container的第一个好处。

IoC Container的第二个好处是：**我们在创建实例的时候不需要了解其中的细节。**在上面的例子中，我们自己手动创建一个车instance时候，是从底层往上层new的：

<img src="https://pic4.zhimg.com/v2-555b2be7d76e78511a6d6fed3304927f_b.png" data-caption="" data-rawwidth="2430" data-rawheight="168" class="origin_image zh-lightbox-thumb" width="2430" data-original="https://pic4.zhimg.com/v2-555b2be7d76e78511a6d6fed3304927f_r.jpg">![img](https://pic4.zhimg.com/80/v2-555b2be7d76e78511a6d6fed3304927f_hd.png)

这个过程中，我们需要了解整个Car/Framework/Bottom/Tire类构造函数是怎么定义的，才能一步一步new/注入。

而IoC Container在进行这个工作的时候是反过来的，它先从最上层开始往下找依赖关系，到达最底层之后再往上一步一步new（有点像深度优先遍历）：

![img](https://pic3.zhimg.com/80/v2-24a96669241e81439c636e83976ba152_hd.png)

这里IoC Container可以直接隐藏具体的创建实例的细节，在我们来看它就像一个工厂：

![img](https://pic2.zhimg.com/80/v2-5ca61395f37cef73c7bbe7808f9ea219_hd.png) 

 我们就像是工厂的客户。我们只需要向工厂请求一个Car实例，然后它就给我们按照Config创建了一个Car实例。我们完全不用管这个Car实例是怎么一步一步被创建出来。

实际项目中，有的Service Class可能是十年前写的，有几百个类作为它的底层。假设我们新写的一个API需要实例化这个Service，我们总不可能回头去搞清楚这几百个类的构造函数吧？IoC Container的这个特性就很完美的解决了这类问题——**因为这个架构要求你在写class的时候需要写相应的Config文件，所以你要初始化很久以前的Service类的时候，前人都已经写好了Config文件，你直接在需要用的地方注入这个Service就可以了**。这大大增加了项目的可维护性且降低了开发难度。

# Spring IOC过程

* 读取Bean配置信息
* 根据Bean注册表实例化Bean
* 将Bean实例放到Spring容器中
* 使用Bean

## Spring IOC容器的核心接口

* BeanFactory  （Spring框架最核心的接口）
  * 提供IOC的配置机制
  * 包含Bean的各种定义，便于实例化Bean
  * 建立Bean之间的依赖关系
  * Bean生命周期控制
* ApplicationContext（继承多个接口）
  * BeanFactory：能够管理，装配Bean
  * ResourcePatternResolve：能够加载资源文件
  * MessageSource：能够实现国际化等工能
  * ApplicationEventPublisher：能够注册监听器，实现监听机制

**BeanFactory与ApplicationContext比较**

* BeanFactory是Spring框架的基础设施，面向Spring
* ApplicationContext面向使用Spring框架的开发者

## Spring IOC 支持的功能

* **依赖注入**
* 依赖检查
* **自动装配**
* 支持集合
* 指定初始化方法和销毁方法
* 支持回调方法

## getBean方法的代码逻辑

* 转换beanName
* 从缓存中加载实例
* 实例化Bean
* 检测parentBeanFactory
* 初始化依赖的Bean
* 创建Bean

## Spring Bean的作用域(scope控制)

* singleton(默认)

  * 容器里拥有唯一的Bean实例
  * IOC容器启动会调用方法创建对象放到ico容器中
  * 以后每次获取直接从窗口(map.get())创建
  * 可使用@Lazy懒加载
* prototype
  * 针对每个getBean请求,容器都会创建一个Bean实例
  * IOC容器启动并不会去调用方法创建对象放在容器中
  * 每次获取的时候才会调用方法创建对象
* request
  * 会为每个Http请求创建一个Bean实例
* session
  * 会为每个session创建一个Bean实例
* globalSession
  * 会为每个全局Http Session 创建一个Bean实例,该作用域仅对Porlet有效

## 注解

* @Conditional
  * 根据条件判断给容器中注册Bean组件
* 给容器中注册组件
  * @Controller/@Service/@Repository/@Component
    * 包扫描+组件标注注解
  * @Bean
    * 导入的的第三方包里面的组件
  * @Import
    * @Import
      * 快速给容器中导入一个组件
    * 实现ImportSelector接口
      * 返回需要导入组件的全类名的数组
    * 实现ImportBeanDefinitionRefistrar
      * BeanDefinitionRegistry的registerBeanDefinition方法手工注册
  * 实现FactoryBean(工厂Bean)接口

## Spring Bean的生命周期(待写)

​    

---

# [ Spring AOP](https://zhuanlan.zhihu.com/p/25522841)

## AOP的三种织入方式

*  编译时织入
  * 需要特殊的Java编译器,如AspectJ
* 类加载时织入
  * 需要特殊的Java编译器,如AspectJ和AspectWerkz
* 运行时织入
  * Spring采用的方式,通过动态代理的方式,实现简单

事实上我们知道诸如日志，权限，事务，性能监测等业务几乎涉及到了所有的核心模块，如果把这些特殊的业务代码直接到核心业务模块的代码中就会造成上述的问题，**而工程师更希望的是这些模块可以实现热插拔特性而且无需把外围的代码入侵到核心模块中，这样在日后的维护和扩展也将会有更佳的表现**，假设现在我们把日志、权限、事务、性能监测等外围业务看作单独的关注点(也可以理解为单独的模块)，每个关注点都可以在需要它们的时刻及时被运用而且无需提前整合到核心模块中，这种形式相当下图：

 ![img](https://pic3.zhimg.com/80/v2-4800c187e3a7ddf36523311db7f914c6_hd.png)

 从图可以看出，每个关注点与核心业务模块分离，作为单独的功能，横切几个核心业务模块，这样的做的好处是显而易见的，**每份功能代码不再单独入侵到核心业务类的代码中，即核心模块只需关注自己相关的业务**，当需要外围业务(日志，权限，性能监测、事务控制)时，这些外围业务会通过一种特殊的技术自动应用到核心模块中，这些关注点有个特殊的名称，叫做“横切关注点”，上图也很好的表现出这个概念，**另外这种抽象级别的技术也叫AOP（面向切面编程）**，正如上图所展示的横切核心模块的整面，因此AOP的概念就出现了，而所谓的特殊技术也就面向切面编程的实现技术，**AOP的实现技术有多种，其中与Java无缝对接的是一种称为AspectJ的技术。**

## AspectJ在Java中的应用

- **神一样的AspectJ-AOP的领跑者**

编写一个HelloWord的类，然后利用AspectJ技术切入该类的执行过程。

```java
/**
 * Created by zejian on 2017/2/15.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
public class HelloWord {

    public void sayHello(){
        System.out.println("hello world !");
    }
    public static void main(String args[]){
        HelloWord helloWord =new HelloWord();
        helloWord.sayHello();
    }
}
```

编写AspectJ类，注意关键字为aspect(MyAspectJDemo.aj,其中aj为AspectJ的后缀)，含义与class相同，即定义一个AspectJ的类

```java
/**
 * Created by zejian on 2017/2/15.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 * 切面类
 */
public aspect MyAspectJDemo {
    /**
     * 定义切点,日志记录切点
     */
    pointcut recordLog():call(* HelloWord.sayHello(..));

    /**
     * 定义切点,权限验证(实际开发中日志和权限一般会放在不同的切面中,这里仅为方便演示)
     */
    pointcut authCheck():call(* HelloWord.sayHello(..));

    /**
     * 定义前置通知!
     */
    before():authCheck(){
        System.out.println("sayHello方法执行前验证权限");
    }

    /**
     * 定义后置通知
     */
    after():recordLog(){
        System.out.println("sayHello方法执行后记录日志");
    }
}
```

ok~，运行helloworld的main函数：

 ![img](https://pic2.zhimg.com/80/v2-b5f464a00d61cbfe863538cfb698c4b5_hd.png)

 对于结果不必太惊讶，完全是意料之中。我们发现，明明只运行了main函数，却在sayHello函数运行前后分别进行了权限验证和日志记录，事实上这就是AspectJ的功劳了。对aspectJ有了感性的认识后，再来聊聊

## AsspectJ到底是什么？

AspectJ是一个java实现的AOP框架，**它能够对java代码进行AOP编译（一般在编译期进行），让java代码具有AspectJ的AOP功能（当然需要特殊的编译器）**，可以这样说AspectJ是目前实现AOP框架中最成熟，功能最丰富的语言，更幸运的是，AspectJ与java程序完全兼容，几乎是无缝关联，因此对于有java编程基础的工程师，上手和使用都非常容易。

在案例中，我们使用**aspect关键字定义了一个类，这个类就是一个切面**，它可以是单独的日志切面(功能)，也可以是权限切面或者其他，在切面内部使用了pointcut定义了两个切点，一个用于权限验证，一个用于日志记录，**而所谓的切点就是那些需要应用切面的方法**，如需要在sayHello方法执行前后进行权限验证和日志记录，那么就需要捕捉该方法，而pointcut就是定义这些需要捕捉的方法（常常是不止一个方法的），这些方法也称为目标方法，最后还定义了两个通知**，通知就是那些需要在目标方法前后执行的函数，**如before()即前置通知在目标方法之前执行，即在sayHello()方法执行前进行权限验证，另一个是after()即后置通知，在sayHello()之后执行，如进行日志记录。到这里也就可以确定，切面就是切点和通知的组合体，组成一个单独的结构供后续使用，下图协助理解。

 ![img](https://pic1.zhimg.com/80/v2-209c76c103f8d27b25ace1f64ae572b8_hd.png)

 这里简单说明一下切点的定义语法：关键字为pointcut，定义切点，后面跟着函数名称，最后编写匹配表达式，此时函数一般使用call()或者execution()进行匹配，这里我们统一使用call()

> pointcut 函数名 : 匹配表达式

案例：**recordLog()是函数名称，自定义的，* 表示任意返回值，接着就是需要拦截的目标函数，sayHello(..)的..，表示任意参数类型。这里理解即可，后面Spring AOP会有关于切点表达式的分析，整行代码的意思是使用pointcut定义一个名为recordLog的切点函数，其需要拦截的(切入)的目标方法是HelloWord类下的sayHello方法，参数不限。**

```java
pointcut recordLog():call(* HelloWord.sayHello(..));
```

关于定义通知的语法：首先通知有5种类型分别如下：

- before 目标方法执行前执行，前置通知
- after 目标方法执行后执行，后置通知
- after returning 目标方法返回时执行 ，后置返回通知
- after throwing 目标方法抛出异常时执行 异常通知
- around 在目标函数执行中执行，可控制目标函数是否执行，环绕通知

语法：

```
[返回值类型] 通知函数名称(参数) [returning/throwing 表达式]：连接点函数(切点函数){ 函数体 
}
```

案例如下，其中要注意around通知即环绕通知，可以通过proceed()方法控制目标函数是否执行。

```java
/**
  * 定义前置通知
  *
  * before(参数):连接点函数{
  *     函数体
  * }
  */
 before():authCheck(){
     System.out.println("sayHello方法执行前验证权限");
 }

 /**
  * 定义后置通知
  * after(参数):连接点函数{
  *     函数体
  * }
  */
 after():recordLog(){
     System.out.println("sayHello方法执行后记录日志");
 }


 /**
  * 定义后置通知带返回值
  * after(参数)returning(返回值类型):连接点函数{
  *     函数体
  * }
  */
 after()returning(int x): get(){
     System.out.println("返回值为:"+x);
 }

 /**
  * 异常通知
  * after(参数) throwing(返回值类型):连接点函数{
  *     函数体
  * }
  */
 after() throwing(Exception e):sayHello2(){
     System.out.println("抛出异常:"+e.toString());
 }

 /**
  * 环绕通知 可通过proceed()控制目标函数是否执行
  * Object around(参数):连接点函数{
  *     函数体
  *     Object result=proceed();//执行目标函数
  *     return result;
  * }
  */
 Object around():aroundAdvice(){
     System.out.println("sayAround 执行前执行");
     Object result=proceed();//执行目标函数
     System.out.println("sayAround 执行后执行");
     return result;
 }
```

 切入点(pointcut)和通知(advice)的概念已比较清晰，而切面则是定义切入点和通知的组合如上述使用aspect关键字定义的MyAspectJDemo，**把切面应用到目标函数的过程称为织入(weaving)**。在前面定义的HelloWord类中除了sayHello函数外，还有main函数，以后可能还会定义其他函数，而这些函数都可以称为目标函数，也就是说这些函数执行前后也都可以切入通知的代码，**这些目标函数统称为连接点**，切入点(pointcut)的定义正是从这些连接点中过滤出来的，下图协助理解。

 ![img](https://pic2.zhimg.com/80/v2-b5c523e28bba91c11c8d7f510ab84a79_hd.png)

## **AspectJ的织入方式及其原理概要**

经过前面的简单介绍，我们已初步掌握了AspectJ的一些语法和概念，但这样仍然是不够的，我们仍需要了解AspectJ应用到java代码的过程（这个过程称为织入），对于织入这个概念，可以简单理解为aspect(切面)应用到目标函数(类)的过程。

对于这个过程，**一般分为动态织入和静态织入**，动态织入的方式是在运行时动态将要增强的代码织入到目标类中，这样往往是通过动态代理技术完成的，如[Java ](http://link.zhihu.com/?target=http%3A//lib.csdn.net/base/java)JDK的动态代理(Proxy，底层通过反射实现)或者CGLIB的动态代理(底层通过继承实现)，Spring AOP采用的就是基于运行时增强的代理技术，这点后面会分析，这里主要重点分析一下静态织入，**ApectJ采用的就是静态织入的方式。ApectJ主要采用的是编译期织入**，在这个期间使用AspectJ的acj编译器(类似javac)把aspect类编译成class字节码后，在java目标类编译时织入，即先编译aspect类再编译目标类。

![img](https://pic2.zhimg.com/80/v2-a4f70efe3e2bb0b438ea89a3ae927011_hd.png)

关于ajc编译器，是一种能够识别aspect语法的编译器，它是采用java语言编写的，由于javac并不能识别aspect语法，便有了ajc编译器，注意ajc编译器也可编译java文件。为了更直观了解aspect的织入方式，我们打开前面案例中已编译完成的HelloWord.class文件，反编译后的java代码如下：

```java
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package com.zejian.demo;

import com.zejian.demo.MyAspectJDemo;
//编译后织入aspect类的HelloWord字节码反编译类
public class HelloWord {
    public HelloWord() {
    }

    public void sayHello() {
        System.out.println("hello world !");
    }

    public static void main(String[] args) {
        HelloWord helloWord = new HelloWord();
        HelloWord var10000 = helloWord;

   try {
        //MyAspectJDemo 切面类的前置通知织入
        MyAspectJDemo.aspectOf().ajc$before$com_zejian_demo_MyAspectJDemo$1$22c5541();
        //目标类函数的调用
           var10000.sayHello();
        } catch (Throwable var3) {
        MyAspectJDemo.aspectOf().ajc$after$com_zejian_demo_MyAspectJDemo$2$4d789574();
            throw var3;
        }

        //MyAspectJDemo 切面类的后置通知织入 
        MyAspectJDemo.aspectOf().ajc$after$com_zejian_demo_MyAspectJDemo$2$4d789574();
    }
}
```

显然AspectJ的织入原理已很明朗了，当然除了编译期织入，还存在链接期(编译后)织入，即将aspect类和java目标类同时编译成字节码文件后，再进行织入处理，这种方式比较有助于已编译好的第三方jar和Class文件进行织入操作，由于这不是本篇的重点，暂且不过多分析，掌握以上AspectJ知识点就足以协助理解Spring AOP了。有些同学可能想自己编写aspect程序进行测试练习，博主在这简单介绍运行环境的搭建，首先博主使用的idea的IDE，因此只对idea进行介绍(eclipse，呵呵)。首先通过maven仓库下载工具包aspectjtools-1.8.9.jar，该工具包包含ajc核心编译器，然后打开idea检查是否已安装aspectJ的插件：

![img](https://pic4.zhimg.com/80/v2-f3ff87bce0c2125e2f6310431776bfdb_hd.png)



配置项目使用ajc编译器(替换javac)如下图：

 

![img](https://pic3.zhimg.com/80/v2-cd16695e353ed951df8276402ee74462_hd.png)

如果使用maven开发（否则在libs目录自行引入jar）则在pom文件中添加aspectJ的核心依赖包，包含了AspectJ运行时的核心库文件：



```xml
<dependency>
    <groupId>aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.5.4</version>
</dependency>
```

新建文件处创建aspectJ文件，然后就可以像运行java文件一样，操作aspect文件了。

![img](https://pic1.zhimg.com/80/v2-591d4421677a4145612d0b19b1b4ec28_hd.png)

ok~，关于AspectJ就简单介绍到这。

##  **基于Aspect Spring AOP 开发**

Spring AOP 与ApectJ 的目的一致，都是为了统一处理横切业务，但与AspectJ不同的是，Spring AOP 并不尝试提供完整的AOP功能(即使它完全可以实现)，Spring AOP 更注重的是与Spring IOC容器的结合，并结合该优势来解决横切业务的问题，因此在AOP的功能完善方面，相对来说AspectJ具有更大的优势。

同时，**Spring注意到AspectJ在AOP的实现方式上依赖于特殊编译器(ajc编译器)，因此Spring很机智回避了这点，转向采用动态代理技术的实现原理来构建Spring AOP的内部机制（动态织入）**，这是与AspectJ（静态织入）最根本的区别。在AspectJ 1.5后，**引入@Aspect形式的注解风格的开发**，Spring也非常快地跟进了这种方式，因此Spring 2.0后便使用了与AspectJ一样的注解。

请注意，Spring 只是使用了与 AspectJ 5 一样的注解，但仍然没有使用 AspectJ 的编译器，底层依是动态代理技术的实现，因此并不依赖于 AspectJ 的编译器。下面我们先通过一个简单的案例来演示Spring AOP的入门程序

- **简单案例快速入门**

 

定义目标类接口和实现类

```java
/**
 * Created by zejian on 2017/2/19.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
 //接口类
public interface UserDao {

    int addUser();

    void updateUser();

    void deleteUser();

    void findUser();
}

//实现类
import com.zejian.spring.springAop.dao.UserDao;
import org.springframework.stereotype.Repository;

/**
 * Created by zejian on 2017/2/19.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
@Repository
public class UserDaoImp implements UserDao {
    @Override
    public int addUser() {
        System.out.println("add user ......");
        return 6666;
    }

    @Override
    public void updateUser() {
        System.out.println("update user ......");
    }

    @Override
    public void deleteUser() {
        System.out.println("delete user ......");
    }

    @Override
    public void findUser() {
        System.out.println("find user ......");
    }
}
```

使用Spring 2.0引入的注解方式，编写Spring AOP的aspect 类：

```java
@Aspect
public class MyAspect {

    /**
     * 前置通知
     */
    @Before("execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
    public void before(){
        System.out.println("前置通知....");
    }

    /**
     * 后置通知
     * returnVal,切点方法执行后的返回值
     */
    @AfterReturning(value="execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))",returning = "returnVal")
    public void AfterReturning(Object returnVal){
        System.out.println("后置通知...."+returnVal);
    }


    /**
     * 环绕通知
     * @param joinPoint 可用于执行切点的类
     * @return
     * @throws Throwable
     */
    @Around("execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("环绕通知前....");
        Object obj= (Object) joinPoint.proceed();
        System.out.println("环绕通知后....");
        return obj;
    }

    /**
     * 抛出通知
     * @param e
     */
    @AfterThrowing(value="execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))",throwing = "e")
    public void afterThrowable(Throwable e){
        System.out.println("出现异常:msg="+e.getMessage());
    }

    /**
     * 无论什么情况下都会执行的方法
     */
    @After(value="execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
    public void after(){
        System.out.println("最终通知....");
    }
}
```

编写配置文件交由Spring IOC容器管理

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 启动@aspectj的自动代理支持-->
    <aop:aspectj-autoproxy />

    <!-- 定义目标对象 -->
    <bean id="userDaos" class="com.zejian.spring.springAop.dao.daoimp.UserDaoImp" />
    <!-- 定义aspect类 -->
    <bean name="myAspectJ" class="com.zejian.spring.springAop.AspectJ.MyAspect"/>
</beans>
```

编写测试类

```java
/**
 * Created by zejian on 2017/2/19.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations= "classpath:spring/spring-aspectj.xml")
public class UserDaoAspectJ {
    @Autowired
    UserDao userDao;

    @Test
    public void aspectJTest(){
        userDao.addUser();
    }
}
```

简单说明一下，定义了一个目标类UserDaoImpl，利用Spring2.0引入的aspect注解开发功能定义aspect类即MyAspect，在该aspect类中，编写了5种注解类型的通知函数，分别是前置通知@Before、后置通知@AfterReturning、环绕通知@Around、异常通知@AfterThrowing、最终通知@After，这5种通知与前面分析AspectJ的通知类型几乎是一样的，并注解通知上使用execution关键字定义的切点表达式，即指明该通知要应用的目标函数，**当只有一个execution参数时，value属性可以省略，当含两个以上的参数，value必须注明，如存在返回值时**。当然除了把切点表达式直接传递给通知注解类型外，还可以使用@pointcut来定义切点匹配表达式，这个与AspectJ使用关键字pointcut是一样的，后面分析。目标类和aspect类定义完成后，最后需要在xml配置文件中进行配置，同样的所有类的创建都交由SpringIOC容器处理，注意，使用Spring AOP 的aspectJ功能时，需要使用以下代码启动aspect的注解功能支持：

```xml
<aop:aspectj-autoproxy />
```

ok~，运行程序，结果符合预期：

![img](https://pic3.zhimg.com/80/v2-1af459f144df58959ecbc13f29f0ba2e_hd.png)

## AOP主要名词概念
* Aspect
  * 通用功能的代码实现
* Target
  * 被织入Aspect的对象
* Join Point
  * 可以作为切入点的机会,所有方法都可以作为切入点
* PointCut
  * Aspect实际被应用在的Join Point,支持正则
* Advice
  * 类里的方法以及这个方法如何织入到目标方法的方式
  * 前置通知(Before)
  * 后置通知(AfterReturning)
  * 异常通知(AfterThrowing)
  * 最终通知(After)
  * 环绕通知(Around)
* Weaving
  * AOP的实现过程



##   **基于注解的Spring AroundOP开发**

- **定义切入点函数**

 

在案例中，定义过滤切入点函数时，是直接把execution已定义匹配表达式作为值传递给通知类型的如下：

```java
@After(value="execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
  public void after(){
      System.out.println("最终通知....");
  }
```

**除了上述方式外，还可采用与ApectJ中使用pointcut关键字类似的方式定义切入点表达式如下，使用@Pointcut注**解：

```java
/**
 * 使用Pointcut定义切点
 */
@Pointcut("execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
private void myPointcut(){}

/**
 * 应用切入点函数
 */
@After(value="myPointcut()")
public void afterDemo(){
    System.out.println("最终通知....");
}
```

使用@Pointcut注解进行定义，应用到通知函数afterDemo()时直接传递切点表达式的函数名称myPointcut()即可，比较简单，下面接着介绍切点指示符。

## **切入点指示符**

为了方法通知应用到相应过滤的目标方法上，SpringAOP提供了匹配表达式，这些表达式也叫切入点指示符，在前面的案例中，它们已多次出现。

**通配符**

在定义匹配表达式时，通配符几乎随处可见，如*、.. 、+ ，它们的含义如下：

- .. ：匹配方法定义中的任意数量的参数，此外还匹配类定义中的任意数量包

```
//任意返回值，任意名称，任意参数的公共方法
execution(public * *(..))
//匹配com.zejian.dao包及其子包中所有类中的所有方法
within(com.zejian.dao..*)
```

- ＋ ：匹配给定类的任意子类

```
//匹配实现了DaoUser接口的所有子类的方法
within(com.zejian.dao.DaoUser+)
```

- ＊ ：匹配任意数量的字符

```java
//匹配com.zejian.service包及其子包中所有类的所有方法
within(com.zejian.service..*)
//匹配以set开头，参数为int类型，任意返回值的方法
execution(* set*(int))
```



## **类型签名表达式**

为了方便类型（如接口、类名、包名）过滤方法，Spring AOP 提供了within关键字。其语法格式如下：

```
within(<type name>)
```

type name 则使用包名或者类名替换即可，来点案例吧。

```java
//匹配com.zejian.dao包及其子包中所有类中的所有方法
@Pointcut("within(com.zejian.dao..*)")

//匹配UserDaoImpl类中所有方法
@Pointcut("within(com.zejian.dao.UserDaoImpl)")

//匹配UserDaoImpl类及其子类中所有方法
@Pointcut("within(com.zejian.dao.UserDaoImpl+)")

//匹配所有实现UserDao接口的类的所有方法
@Pointcut("within(com.zejian.dao.UserDao+)")
```

## **方法签名表达式**

如果想根据方法签名进行过滤，关键字execution可以帮到我们，语法表达式如下

```java
//scope ：方法作用域，如public,private,protect
//returnt-type：方法返回值类型
//fully-qualified-class-name：方法所在类的完全限定名称
//parameters 方法参数
execution(<scope> <return-type> <fully-qualified-class-name>.*(parameters))
```

对于给定的作用域、返回值类型、完全限定类名以及参数匹配的方法将会应用切点函数指定的通知，这里给出模型案例：

```java
//匹配UserDaoImpl类中的所有方法
@Pointcut("execution(* com.zejian.dao.UserDaoImpl.*(..))")

//匹配UserDaoImpl类中的所有公共的方法
@Pointcut("execution(public * com.zejian.dao.UserDaoImpl.*(..))")

//匹配UserDaoImpl类中的所有公共方法并且返回值为int类型
@Pointcut("execution(public int com.zejian.dao.UserDaoImpl.*(..))")

//匹配UserDaoImpl类中第一个参数为int类型的所有公共的方法
@Pointcut("execution(public * com.zejian.dao.UserDaoImpl.*(int , ..))")
```

**其他指示符**

- bean：Spring AOP扩展的，AspectJ没有对于指示符，用于匹配特定名称的Bean对象的执行方法；

```java
//匹配名称中带有后缀Service的Bean。
@Pointcut("bean(*Service)")
private void myPointcut1(){}
```

- this ：用于匹配当前AOP代理对象类型的执行方法；请注意是AOP代理对象的类型匹配，这样就可能包括引入接口也类型匹配

 

```java
//匹配了任意实现了UserDao接口的代理对象的方法进行过滤
@Pointcut("this(com.zejian.spring.springAop.dao.UserDao)")
private void myPointcut2(){}
```

- target ：用于匹配当前目标对象类型的执行方法；

 

```java
//匹配了任意实现了UserDao接口的目标对象的方法进行过滤
@Pointcut("target(com.zejian.spring.springAop.dao.UserDao)")
private void myPointcut3(){}
```

- @within：用于匹配所以持有指定注解类型内的方法；请注意与within是有区别的， within是用于匹配指定类型内的方法执行；

```java
//匹配使用了MarkerAnnotation注解的类(注意是类)
@Pointcut("@within(com.zejian.spring.annotation.MarkerAnnotation)")
private void myPointcut4(){}
```

- @annotation(com.zejian.spring.MarkerMethodAnnotation) : 根据所应用的注解进行方法过滤

```java
//匹配使用了MarkerAnnotation注解的方法(注意是方法)
@Pointcut("@annotation(com.zejian.spring.annotation.MarkerAnnotation)")
private void myPointcut5(){}
```

ok~，关于表达式指示符就介绍到这，我们主要关心前面几个常用的即可，不常用过印象即可。这里最后说明一点，**切点指示符可以使用运算符语法进行表达式的混编，如and、or、not（或者&&、||、！）**，如下一个简单例子：

```java
//匹配了任意实现了UserDao接口的目标对象的方法并且该接口不在com.zejian.dao包及其子包下
@Pointcut("target(com.zejian.spring.springAop.dao.UserDao) ！within(com.zejian.dao..*)")
private void myPointcut6(){}
//匹配了任意实现了UserDao接口的目标对象的方法并且该方法名称为addUser
@Pointcut("target(com.zejian.spring.springAop.dao.UserDao)&&execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
private void myPointcut7(){}
```

## **通知函数以及传递参数**

 

**5种通知函数**

通知在前面的aspectJ和Spring AOP的入门案例已见过面，在Spring中与AspectJ一样，通知主要分5种类型，分别是前置通知、后置通知、异常通知、最终通知以及环绕通知，下面分别介绍。

- 前置通知@Before

 前置通知通过@Before注解进行标注，并可直接传入切点表达式的值，该通知在目标函数执行前执行，注意JoinPoint，是Spring提供的静态变量，通过joinPoint 参数，可以获取目标对象的信息,如类名称,方法参数,方法名称等，，该参数是可选的。

```java
/**
 * 前置通知
 * @param joinPoint 该参数可以获取目标对象的信息,如类名称,方法参数,方法名称等
 */
@Before("execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))")
public void before(JoinPoint joinPoint){
    System.out.println("我是前置通知");
}
```

* 后置通知@AfterReturning

通过@AfterReturning注解进行标注，该函数在目标函数执行完成后执行，并可以获取到目标函数最终的返回值returnVal，当目标函数没有返回值时，returnVal将返回null，必须通过returning = “returnVal”注明参数的名称而且必须与通知函数的参数名称相同。**请注意，在任何通知中这些参数都是可选的，需要使用时直接填写即可，不需要使用时，可以完成不用声明出来**。如下

```java
/**
* 后置通知，不需要参数时可以不提供
*/
@AfterReturning(value="execution(* com.zejian.spring.springAop.dao.UserDao.*User(..))")
public void AfterReturning(){
   System.out.println("我是后置通知...");
}
```

```java
/**
* 后置通知
* returnVal,切点方法执行后的返回值
*/
@AfterReturning(value="execution(* com.zejian.spring.springAop.dao.UserDao.*User(..))",returning = "returnVal")
public void AfterReturning(JoinPoint joinPoint,Object returnVal){
   System.out.println("我是后置通知...returnVal+"+returnVal);
}
```

- 异常通知 @AfterThrowing

该通知只有在异常时才会被触发，并由throwing来声明一个接收异常信息的变量，同样异常通知也用于Joinpoint参数，需要时加上即可，如下：

```java
/**
* 抛出通知
* @param e 抛出异常的信息
*/
@AfterThrowing(value="execution(* com.zejian.spring.springAop.dao.UserDao.addUser(..))",throwing = "e")
public void afterThrowable(Throwable e){
  System.out.println("出现异常:msg="+e.getMessage());
}
```

- 最终通知 @After

该通知有点类似于finally代码块，只要应用了无论什么情况下都会执行。

```java
/**
 * 无论什么情况下都会执行的方法
 * joinPoint 参数
 */
@After("execution(* com.zejian.spring.springAop.dao.UserDao.*User(..))")
public void after(JoinPoint joinPoint) {
    System.out.println("最终通知....");
}
```

- 环绕通知@Around

 环绕通知既可以在目标方法前执行也可在目标方法之后执行，更重要的是环绕通知可以控制目标方法是否指向执行，但即使如此，我们应该尽量以最简单的方式满足需求，在仅需在目标方法前执行时，应该采用前置通知而非环绕通知。案例代码如下第一个参数必须是ProceedingJoinPoint，通过该对象的proceed()方法来执行目标函数，proceed()的返回值就是环绕通知的返回值。同样的，ProceedingJoinPoint对象也是可以获取目标对象的信息，如类名称，方法参数，方法名称等等。

```java
@Around("execution(* com.zejian.spring.springAop.dao.UserDao.*User(..))")
public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
    System.out.println("我是环绕通知前....");
    //执行目标函数
    Object obj= (Object) joinPoint.proceed();
    System.out.println("我是环绕通知后....");
    return obj;
}
```

**通知传递参数**

在Spring AOP中，**除了execution和bean指示符不能传递参数给通知方法，其他指示符都可以将匹配的方法相应参数或对象自动传递给通知方法。获取到匹配的方法参数后通过”argNames”属性指定参数名。**如下，需要注意的是**args(指示符)、argNames的参数名与before()方法中参数名 必须保持一致即param**。

```java
@Before(value="args(param)", argNames="param") //明确指定了    
public void before(int param) {    
    System.out.println("param:" + param);    
}  
```

当然也可以直接使用args指示符不带argNames声明参数，如下：

```java
@Before("execution(public * com.zejian..*.addUser(..)) && args(userId,..)")  
public void before(int userId) {  
    //调用addUser的方法时如果与addUser的参数匹配则会传递进来会传递进来
    System.out.println("userId:" + userId);  
}  
```

args(userId,..)该表达式会保证只匹配那些至少接收一个参数而且传入的类型必须与userId一致的方法，记住传递的参数可以简单类型或者对象，而且只有参数和目标方法也匹配时才有会有值传递进来。来个

## **Aspect优先级**

在不同的切面中**，如果有多个通知需要在同一个切点函数指定的过滤目标方法上执行，那些在目标方法前执行(”进入”)的通知函数，最高优先级的通知将会先执行，在执行在目标方法后执行(“退出”)的通知函数，最高优先级会最后执行**。而对于在同一个切面定义的通知函数将会根据在类中的声明顺序执行。如下：

```java
package com.zejian.spring.springAop.AspectJ;

import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;

/**
 * Created by zejian on 2017/2/20.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
@Aspect
public class AspectOne {

    /**
     * Pointcut定义切点函数
     */
    @Pointcut("execution(* com.zejian.spring.springAop.dao.UserDao.deleteUser(..))")
    private void myPointcut(){}

    @Before("myPointcut()")
    public void beforeOne(){
        System.out.println("前置通知....执行顺序1");
    }

    @Before("myPointcut()")
    public void beforeTwo(){
        System.out.println("前置通知....执行顺序2");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningThree(){
        System.out.println("后置通知....执行顺序3");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningFour(){
        System.out.println("后置通知....执行顺序4");
    }
}
```

在同一个切面中定义多个通知响应同一个切点函数，执行顺序为声明顺序：

![img](https://pic4.zhimg.com/80/v2-e675e3d76d07b08ca65711486cc11bd3_hd.png)

 如果在不同的切面中定义多个通知响应同一个切点，进入时则优先级高的切面类中的通知函数优先执行，退出时则最后执行，如下定义AspectOne类和AspectTwo类并实现org.springframework.core.Ordered 接口，**该接口用于控制切面类的优先级，同时重写getOrder方法，定制返回值，返回值(int 类型)越小优先级越大**。其中AspectOne返回值为0，AspectTwo的返回值为3，显然AspectOne优先级高于AspectTwo。

```java
/**
 * Created by zejian on 2017/2/20.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
@Aspect
public class AspectOne implements Ordered {

    /**
     * Pointcut定义切点函数
     */
    @Pointcut("execution(* com.zejian.spring.springAop.dao.UserDao.deleteUser(..))")
    private void myPointcut(){}

    @Before("myPointcut()")
    public void beforeOne(){
        System.out.println("前置通知..AspectOne..执行顺序1");
    }

    @Before("myPointcut()")
    public void beforeTwo(){
        System.out.println("前置通知..AspectOne..执行顺序2");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningThree(){
        System.out.println("后置通知..AspectOne..执行顺序3");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningFour(){
        System.out.println("后置通知..AspectOne..执行顺序4");
    }

    /**
     * 定义优先级,值越低,优先级越高
     * @return
     */
    @Override
    public int getOrder() {
        return 0;
    }
}


//切面类 AspectTwo.java
@Aspect
public class AspectTwo implements Ordered {

    /**
     * Pointcut定义切点函数
     */
    @Pointcut("execution(* com.zejian.spring.springAop.dao.UserDao.deleteUser(..))")
    private void myPointcut(){}

    @Before("myPointcut()")
    public void beforeOne(){
        System.out.println("前置通知....执行顺序1--AspectTwo");
    }

    @Before("myPointcut()")
    public void beforeTwo(){
        System.out.println("前置通知....执行顺序2--AspectTwo");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningThree(){
        System.out.println("后置通知....执行顺序3--AspectTwo");
    }

    @AfterReturning(value = "myPointcut()")
    public void AfterReturningFour(){
        System.out.println("后置通知....执行顺序4--AspectTwo");
    }

    /**
     * 定义优先级,值越低,优先级越高
     * @return
     */
    @Override
    public int getOrder() {
        return 2;
    }
}
```



运行结果如下：

![img](https://pic4.zhimg.com/80/v2-cabe31d72fc9b8ef8a3fee088e8b3e5f_hd.png)

案例中虽然只演示了前置通知和后置通知，但其他通知也遵循相同的规则，有兴趣可自行测试。到此基于注解的Spring AOP 分析就结束了，**但请注意，在配置文件中启动@Aspect支持后，Spring容器只会尝试自动识别带@Aspect的Bean，前提是任何定义的切面类都必须已在配置文件以Bean的形式声明**。

```java
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--<context:component-scan base-package=""-->
    <!-- 启动@aspectj的自动代理支持-->
    <aop:aspectj-autoproxy />

    <!-- 定义目标对象 -->
    <bean id="userDaos" class="com.zejian.spring.springAop.dao.daoimp.UserDaoImp" />
    <!-- 定义aspect类 -->
    <bean name="myAspectJ" class="com.zejian.spring.springAop.AspectJ.MyAspect"/>

    <bean name="aspectOne" class="com.zejian.spring.springAop.AspectJ.AspectOne"  />

    <bean name="aspectTwo" class="com.zejian.spring.springAop.AspectJ.AspectTwo" />
</beans>
```

## **基于XML的开发**

 

前面分析完基于注解支持的开发是日常应用中最常见的，即使如此我们还是有必要了解一下基于xml形式的Spring AOP开发，这里会以一个案例的形式对xml的开发形式进行简要分析，定义一个切面类

```java
/**
 * Created by zejian on 2017/2/20.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
public class MyAspectXML {

    public void before(){
        System.out.println("MyAspectXML====前置通知");
    }

    public void afterReturn(Object returnVal){
        System.out.println("后置通知-->返回值:"+returnVal);
    }

    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("MyAspectXML=====环绕通知前");
        Object object= joinPoint.proceed();
        System.out.println("MyAspectXML=====环绕通知后");
        return object;
    }

    public void afterThrowing(Throwable throwable){
        System.out.println("MyAspectXML======异常通知:"+ throwable.getMessage());
    }

    public void after(){
        System.out.println("MyAspectXML=====最终通知..来了");
    }
}
```

通过配置文件的方式声明如下（spring-aspectj-xml.xml）：

```java
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--<context:component-scan base-package=""-->

    <!-- 定义目标对象 -->
    <bean name="productDao" class="com.zejian.spring.springAop.dao.daoimp.ProductDaoImpl" />

    <!-- 定义切面 -->
    <bean name="myAspectXML" class="com.zejian.spring.springAop.AspectJ.MyAspectXML" />
    <!-- 配置AOP 切面 -->
    <aop:config>
        <!-- 定义切点函数 -->
        <aop:pointcut id="pointcut" expression="execution(* com.zejian.spring.springAop.dao.ProductDao.add(..))" />

        <!-- 定义其他切点函数 -->
        <aop:pointcut id="delPointcut" expression="execution(* com.zejian.spring.springAop.dao.ProductDao.delete(..))" />

        <!-- 定义通知 order 定义优先级,值越小优先级越大-->
        <aop:aspect ref="myAspectXML" order="0">
            <!-- 定义通知
            method 指定通知方法名,必须与MyAspectXML中的相同
            pointcut 指定切点函数
            -->
            <aop:before method="before" pointcut-ref="pointcut" />

            <!-- 后置通知  returning="returnVal" 定义返回值 必须与类中声明的名称一样-->
            <aop:after-returning method="afterReturn" pointcut-ref="pointcut"  returning="returnVal" />

            <!-- 环绕通知 -->
            <aop:around method="around" pointcut-ref="pointcut"  />

            <!--异常通知 throwing="throwable" 指定异常通知错误信息变量,必须与类中声明的名称一样-->
            <aop:after-throwing method="afterThrowing" pointcut-ref="pointcut" throwing="throwable"/>

            <!--
                 method : 通知的方法(最终通知)
                 pointcut-ref : 通知应用到的切点方法
                -->
            <aop:after method="after" pointcut-ref="pointcut"/>
        </aop:aspect>
    </aop:config>
</beans>
```

声明方式和定义方式在代码中已很清晰了，了解一下即可，在实际开发中，会更倾向与使用注解的方式开发，毕竟更简单更简洁。

## **Spring AOP 简单应用场景**

接下来的Spring AOP应用案例将会加深我们对于其的熟悉程度，这里将会模拟两种开发中常用的场景，注意这些场景的代码并**不一定完全适合所有情况，但它们确实是可运用到实际软件开发中**的，同时案例中的代码并不会以完整的项目演示而更倾向于模拟AOP的应用代码，那就由此开始吧。性能监测对于成熟的系统是不可或缺的，以下的代码段将程序消耗时间方面给我们带来直观的数据(消耗时间的大小)：

```java
// 计算消耗的时间
long start = System.currentTimeMillis();
//其他代码操作
long time = System.currentTimeMillis() - start;
```

**可预知这样的代码将系统中多次出现而且横跨各个模块，显然可以抽取横切关注点，使用AOP来完成**，下面的代码将会模拟监测api接口访问的性能并记录每个接口的名称包含类名以及消耗时间等，简单定义一个监控信息记录类：

```java
/**
 * Created by wuzejian on 2017/2/20.
 * 性能监控信息类，简单模拟
 */
public class MonitorTime {

    private String className;
    private String methodName;
    private Date logTime;
    private long comsumeTime;
    //......其他属性

    //省略set 和 get

 }
```

定义一个监控的切面类：

```java
/**
 * Created by wuzejian on 2017/2/20.
 * API接口性能分析
 * 这里只是简单模拟，可以根据业务需求自行定义
 */
@Aspect
public class TimerAspect {
    /**
     * 定义切点函数，过滤controller包下的名称以Controller结尾的类所有方法
     */
    @Pointcut("execution(* com.zejian.spring.controller.*Controller.*(..))")
    void timer() {
    }

    @Around("timer()")
    public Object logTimer(ProceedingJoinPoint thisJoinPoint) throws Throwable {

        MonitorTime monitorTime=new MonitorTime();
        //获取目标类名称
        String clazzName = thisJoinPoint.getTarget().getClass().getName();
        //获取目标类方法名称
        String methodName = thisJoinPoint.getSignature().getName();

        monitorTime.setClassName(clazzName);//记录类名称
        monitorTime.setMethodName(methodName);//记录对应方法名称
        monitorTime.setLogTime(new Date());//记录时间

        // 计时并调用目标函数
        long start = System.currentTimeMillis();
        Object result = thisJoinPoint.proceed();
        long time = System.currentTimeMillis() - start;

        //设置消耗时间
        monitorTime.setComsumeTime(time);
        //把monitorTime记录的信息上传给监控系统，并没有实现，需要自行实现即可
        //MonitoruUtils.report(monitorTime)

        return result;
    }
}
```

然后在xml文件声明该切面：

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!--<context:component-scan base-package=""-->
    <!-- 启动@aspectj的自动代理支持-->
    <aop:aspectj-autoproxy />

    <!-- 定义目标对象 -->
    <bean id="userController" class="com.zejian.spring.controller.UserController" />
    <!-- 定义aspect类 -->
    <bean name="timerAspect" class="com.zejian.spring.SpringAopAction.TimerAspect"/>
</beans>
```

在每次访问切点函数定义过滤的方法时就计算出每个接口消耗的时间，以此来作为访问接口的性能指标。**除了性能监控，可能还有比这更重要的异常监控，而异常的处理在各个模块中都是不可避免的，为了避免到处编写try/catch，统一处理异常是个非常不错的主意，Spring AOP 显然可以胜任** ，我们完全可以在切面类中使用环绕通知统一处理异常信息，并把异常信息封装上报给后台日志系统，以下的代码将体现出这一机智的操作。首先编写一个异常封装类(仅简单模拟)：

```java
/**
 * Created by wuzejian on 2017/2/20.
 * 封装异常信息的类
 */
public class ExceptionInfo {

    private String className;
    private String methodName;
    private Date logTime;//异常记录时间
    private String message;//异常信息
    //.....其他属性
    //省略set get
    }
```

统一处理异常的切面类如下：

```java
/**
 * Created by wuzejian on 2017/2/20.
 * 异常监测切面类
 */
@Aspect
public class ExceptionMonitor {

    /**
     * 定义异常监控类
     */
    @Pointcut("execution(com.zejian.spring *(..))")
    void exceptionMethod() {
    }

    @Around("exceptionMethod()")
    public Object monitorMethods(ProceedingJoinPoint thisJoinPoint) {
        try {
            return thisJoinPoint.proceed();
        } catch (Throwable e) {
            ExceptionInfo info=new ExceptionInfo();
            //异常类记录
            info.setClassName(thisJoinPoint.getTarget().getClass().getName());
            info.setMethodName(thisJoinPoint.getSignature().getName());
            info.setLogTime(new Date());
            info.setMessage(e.toString());
            //上传日志系统,自行完善
            //ExceptionReportUtils.report(info);
            return null;
        }
    }
}
```

关于异常监控ExceptionMonitor中的通知函数体，这里仅简单演示代码实现，实际开发中根据业务需求修改即可。AOP的应用远不止这两种，诸如缓存，权限验证、内容处理、事务控制等都可以使用AOP实现，其中事务控制Spring中提供了专门的处理方式，限于篇幅就先聊到这。

##  **Spring AOP的实现原理概要**

前面的分析中，我们谈到Spring AOP的实现原理是基**于动态织入的动态代理技术**，而AspectJ则是静态织入，**而动态代理技术又分为Java JDK动态代理和CGLIB动态代理**，前者是基于反射技术的实现，后者是基于继承的机制实现，下面通过一个简单的例子来分析这两种技术的代码实现。

- **JDK动态代理**

 

先看一个简单的例子，声明一个A类并实现ExInterface接口，利用JDK动态代理技术在execute()执行前后织入权限验证和日志记录，注意这里仅是演示代码并不代表实际应用。

```java
/**
 * Created by zejian on 2017/2/11.
 * Blog : http://blog.csdn.net/javazejian [原文地址,请尊重原创]
 */
//自定义的接口类，JDK动态代理的实现必须有对应的接口类
public interface ExInterface {
    void execute();
}

//A类，实现了ExInterface接口类
public class A implements ExInterface{
    public void execute(){
        System.out.println("执行A的execute方法...");
    }
}
//代理类的实现
public class JDKProxy implements InvocationHandler{

    /**
     * 要被代理的目标对象
     */
    private A target;

    public JDKProxy(A target){
        this.target=target;
    }

    /**
     * 创建代理类
     * @return
     */
    public ExInterface createProxy(){
        return (ExInterface) Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }

    /**
     * 调用被代理类(目标对象)的任意方法都会触发invoke方法
     * @param proxy 代理类
     * @param method 被代理类的方法
     * @param args 被代理类的方法参数
     * @return
     * @throws Throwable
     */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //过滤不需要该业务的方法
        if("execute".equals(method.getName())) {
            //调用前验证权限
            AuthCheck.authCheck();
            //调用目标对象的方法
            Object result = method.invoke(target, args);
            //记录日志数据
            Report.recordLog();
            return result;
        }eles if("delete".equals(method.getName())){
            //.....
        }
        //如果不需要增强直接执行原方法
        return method.invoke(target,args);
    }
}

 //测试验证
 public static void main(String args[]){
      A a=new A();
      //创建JDK代理
      JDKProxy jdkProxy=new JDKProxy(a);
      //创建代理对象
      ExInterface proxy=jdkProxy.createProxy();
      //执行代理对象方法
      proxy.execute();
  }
```

行结果：

![img](https://pic4.zhimg.com/80/v2-c7e7a1a6e741eab824cda3a4826ee1d3_hd.png)

在A的execute方法里面并没有调用任何权限和日志的代码，也没有直接操作a对象，相反地只是调用了proxy代理对象的方法，最终结果却是预期的，这就是动态代理技术，是不是跟Spring AOP似曾相识？实际上动态代理的底层是通过反射技术来实现，只要拿到A类的class文件和A类的实现接口，很自然就可以生成相同接口的代理类并调用a对象的方法了，关于底层反射技术的实现，暂且不过多讨论，请注意实现java的动态代理是有先决条件的，该条件是目标对象必须带接口，如A类的接口是ExInterface，通过ExInterface接口动态代理技术便可以创建与A类类型相同的代理对象，如下代码演示了创建并调用时利用多态生成的proxy对象：

```java
A a=new A();
 //创建JDK代理类
 JDKProxy jdkProxy=new JDKProxy(a);
 //创建代理对象，代理对象也实现了ExInterface，通过Proxy实现
 ExInterface proxy=jdkProxy.createProxy();
 //执行代理对象方法
 proxy.execute();
```



代理对象的创建是通过Proxy类达到的，Proxy类由Java JDK提供，利用Proxy#newProxyInstance方法便可以动态生成代理对象(proxy)，底层通过反射实现的，该方法需要3个参数

```java
/**
* @param loader 类加载器，一般传递目标对象(A类即被代理的对象)的类加载器
* @param interfaces 目标对象(A)的实现接口
* @param h 回调处理句柄(后面会分析到)
*/
public static Object newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)
```

创建代理类proxy的代码如下：

```java
public ExInterface createProxy(){
   return (ExInterface) Proxy.newProxyInstance(target.getClass().getClassLoader(),target.getClass().getInterfaces(),this);
    }
```

到此并没结束，因为有接口还是远远不够，代理类（Demo中的JDKProxy）还需要实现InvocationHandler接口，也是由JDK提供，代理类必须实现的并重写invoke方法，完全可以把InvocationHandler看成一个回调函数(Callback)，Proxy方法创建代理对象proxy后，当调用execute方法(代理对象也实现ExInterface)时,将会回调InvocationHandler#invoke方法，因此我们可以在invoke方法中来控制被代理对象(目标对象)的方法执行，从而在该方法前后动态增加其他需要执行的业务，Demo中的代码很好体现了这点：

```java
@Override
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    //过滤不需要该业务的方法
    if("execute".equals(method.getName())) {
        //调用前验证权限(动态添加其他要执行业务)
        AuthCheck.authCheck();

        //调用目标对象的方法(执行A对象即被代理对象的execute方法)
        Object result = method.invoke(target, args);

        //记录日志数据(动态添加其他要执行业务)
        Report.recordLog();

        return result;
    }eles if("delete".equals(method.getName())){
     //.....
     return method.invoke(target, args);
    }
    //如果不需要增强直接执行原方法
    return method.invoke(target,args);
```

invoke方法有三个参数：

- Object proxy ：生成的代理对象
- Method method：目标对象的方法，通过反射调用
- Object[] args：目标对象方法的参数

这就是Java JDK动态代理的代码实现过程，小结一下，运用JDK动态代理，被代理类(目标对象，如A类)，必须已有实现接口如(ExInterface)，因为JDK提供的Proxy类将通过目标对象的类加载器ClassLoader和Interface，以及句柄(Callback)创建与A类拥有相同接口的代理对象proxy，该代理对象将拥有接口ExInterface中的所有方法，同时代理类必须实现一个类似回调函数的InvocationHandler接口并重写该接口中的invoke方法，当调用proxy的每个方法(如案例中的proxy#execute())时，invoke方法将被调用，利用该特性，可以在invoke方法中对目标对象(被代理对象如A)方法执行的前后动态添加其他外围业务操作，此时无需触及目标对象的任何代码，也就实现了外围业务的操作与目标对象(被代理对象如A)完全解耦合的目的。当然缺点也很明显需要拥有接口，这也就有了后来的CGLIB动态代理了

此处省略一大堆。。。。。。。。。。。。。。。。

关于JDK代理技术 和 CGLIB代理技术到这就讲完了，我们也应该明白 Spring AOP 确实是通过 CGLIB或者JDK代理 来动态地生成代理对象，这个代理对象指的就是 AOP 代理类，而 AOP 代理类的方法则通过在目标对象的切入点动态地织入增强处理，从而完成了对目标方法的增强。这里并没有非常深入去分析这两种技术，更倾向于向大家演示Spring AOP 底层实现的最简化的模型代码，Spring AOP内部已都实现了这两种技术，Spring AOP 在使用时机上也进行自动化调整，当有接口时会自动选择JDK动态代理技术，如果没有则选择CGLIB技术，当然Spring AOP的底层实现并没有这么简单，为更简便生成代理对象，Spring AOP 内部实现了一个专注于生成代理对象的工厂类，这样就避免了大量的手动编码，这点也是十分人性化的，但最核心的还是动态代理技术。从性能上来说，Spring AOP 虽然无需特殊编译器协助，但性能上并不优于AspectJ的静态织入，这点了解一下即可。最后给出Spring AOP简化的原理模型如下图，附属简要源码，[【GITHUB源码下载】](http://link.zhihu.com/?target=https%3A//github.com/shinezejian/spring-test)

![img](https://pic3.zhimg.com/80/v2-b091ac6fd64f493eaeabfeff4cee7fee_hd.png)





































