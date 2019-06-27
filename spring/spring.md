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

* @Configuration
  * 配置类
* @Conditional(linux.class)
  * linux类需实现Condition接口
  * 根据条件判断给容器中注册Bean组件
* @PropertiesSources（value=""）
* @Value
  * 基本数值
    * @Value("张三")
  *  #{}
    * #{20-2}
  * ${person.name}
    *  取出配置文件(在运行环境变量)中的值(properties文件)
* @Autowire(required=false)
  * 自动注入，Spring规范的注解
  * 标注位置
    * 方法位置
      * @Bean+方法参数，参数从容器中获取
    * 构造器上
      * 如果组件只有一个有参构造器，这个有参构造器的@Autowired可以省略，参数将从容器中获取
    * 放在参数位置
  * 默认按照类型从容器中找相应组件
    * applicationContext.getBean(xxx.class)
  * 如果找到多个相同类型组件，再将属性的名称作为组件的id去容器中查找
  * @Qualifier("bookDao")
    * 指定需要装配的组件id，而非属性名
  * @Primary
    * 让Spring装配时，默认选取首选的类
    * 可以使用@Qualifier指定需要装配的bean的名字
* @Resource
  * Java规范的注解，类似Autowired
  * 默认按照组件名称进行装配
    * @Resource（“指定bean name”）
* @Inject
  * Java规范的注解，类似Autowired
  * 导入javax.inject包
* @Profile("s")
  * Spring为我们提供的可以根据当前环境，动态地激活和切换一系列组件的功能
  * 加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境
  * 使用命令行动态参数
    * 在虚拟机参数位置加载  -Dspring.profiles.active=test
    * 代码方式激活环境
    * 没有标注环境标识的Bean在任何环境下都加载

## 给容器中注册组件（添加Bean）的方式

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
    * 默认获取到的是工厂Bean调用getObject创建的对象
    * 要获取工厂Bean本身，我们需要给id前面加一个&标识

## Spring Bean的生命周期

bean的生命周期：bean的创建--初始化--销毁的过程，由容器管理bean的生命周期

* 创建
  * 单实例，在容器启动的时候创建对象
  * 多实例，每次获取的时候创建对象
* 初始化
  * 对象创建完成，并赋值好，调用初始化方法
* 销毁
  * 单实例：容器关闭的时候
  * 多实例：容器不会管理这个bean；容器不会调用销毁方法
* 初始化和销毁方法

  * 通过@Bean指定init-method和destroy-method
  * Bean实现InitializingBean
    * 定义初始化逻辑（afterPropertiesSet方法）
  * Bean实现DisposableBean接口
    * 定义销毁逻辑(destroy方法)
  * @PostConstruct
    * bean创建完成并且属性赋值完成，未执行初始化方法
  * @PreDestroy
    * 在容器销毁bean之前通知进行清理工作
  * BeanPostProcessor接口
    * Bean的后置处理器
    * postProcessBeforeInitialization
      * 初始化之前
    * postProcessAfterInitialization
      * 初始化之后
* 自定义组件实现xxxAware
  * 在创建对象的时候，会调用接口元宝的方法注入相关组件：Aware
  * 把Spring底层一些组件注入到自定义的Bean中
  * xxxAware：功能使用xxxProcessor

---

#  Spring AOP

## AOP主要名词概念

在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式

- @Aspect
  - 切面类标识
- Target
  - 被织入Aspect的对象
- @EnableAspectJAutoProxy
  - 启用自动代理
- JoinPoint
  - 通过该参数获取方法信息
  - 需放在参数表的第一位
- @PointCut(execution（“”）)
  - Aspect实际被应用在的Join Point,支持正则
- Advice(通知方法)
  - 前置通知(@Before)
    - 在目标方法之前切入
  - 后置通知(@After)
  - 返回通知(@AfterReturning)
  - 异常通知(@AfterThrowing)
  - 环绕通知(@Around)
    - 动态代理，手动推进目标方法运行
    - joinPoint.procced()
- Weaving
  - AOP的实现过程

## AOP的三种织入方式

*  编译时织入
  * 需要特殊的Java编译器,如AspectJ
* 类加载时织入
  * 需要特殊的Java编译器,如AspectJ和AspectWerkz
* 运行时织入
  * Spring采用的方式,通过动态代理的方式,实现简单



 ![img](https://pic3.zhimg.com/80/v2-4800c187e3a7ddf36523311db7f914c6_hd.png)




