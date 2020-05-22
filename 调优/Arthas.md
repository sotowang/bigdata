# [Arthas（阿尔萨斯）](https://alibaba.github.io/arthas/)

## Arthas解决的问题

* Arthas是Alibaba开源的Java诊断工具
* 解决的问题
  * 这个类从哪个 jar 包加载的？为什么会报各种类相关的 Exception？
  * 我改的代码为什么没有执行到？难道是我没 commit？分支搞错了？
  * 遇到问题无法在线上 debug，难道只能通过加日志再重新发布吗？
  * 线上遇到某个用户的数据处理有问题，但线上同样无法 debug，线下无法重现！
  * 是否有一个全局视角来查看系统的运行状况？
  * 有什么办法可以监控到JVM的实时运行状态？
  * 怎么快速定位应用的热点，生成火焰图？

## [案例：排查函数调用异常](https://alibaba.github.io/arthas/arthas-tutorials?language=cn&id=arthas-advanced)

* 启动Demo

  > wget https://github.com/hengyunabc/katacoda-scenarios/raw/master/demo-arthas-spring-boot.jar java -jar demo-arthas-spring-boot.jar

  * 启动之后，可以访问80端口： [https://2886795277-80-ollie02.environments.katacoda.com](https://2886795277-80-ollie02.environments.katacoda.com/)

* 启动arthas-boot

  > wget https://alibaba.github.io/arthas/arthas-boot.jar java -jar arthas-boot.jar --target-ip 0.0.0.0

* 目前，访问 http://localhost/user/0 ，会返回500异常：

  ```
  {"timestamp":1550223186170,"status":500,"error":"Internal Server Error","exception":"java.lang.IllegalArgumentException","message":"id < 1","path":"/user/0"}
  ```

  但请求的具体参数，异常栈是什么呢？

### 查看UserController的 参数/异常

  在Arthas里执行：

  ```bash
  watch com.example.demo.arthas.user.UserController * '{params, throwExp}'
  ```

    1. 第一个参数是类名，支持通配
    2. 第二个参数是函数名，支持通配

  访问 `curl http://localhost/user/0` ,`watch`命令会打印调用的参数和异常

  可以看到实际抛出的异常是`IllegalArgumentException`。

  可以输入 `Q` 或者 `Ctrl+C` 退出watch命令。

  如果想把获取到的结果展开，可以用`-x`参数：

  ```
  watch com.example.demo.arthas.user.UserController * '{params, throwExp}' -x 2
  ```

### 返回值表达式

  在上面的例子里，第三个参数是`返回值表达式`，它实际上是一个`ognl`表达式，它支持一些内置对象：

  - loader
  - clazz
  - method
  - target
  - params
  - returnObj
  - throwExp
  - isBefore
  - isThrow
  - isReturn

### 条件表达式

你可以利用这些内置对象来组成不同的表达式。比如返回一个数组：

```bash
watch com.example.demo.arthas.user.UserController * '{params[0], target, returnObj}'
```

`watch`命令支持在第4个参数里写条件表达式，比如：

```
watch com.example.demo.arthas.user.UserController * returnObj 'params[0] > 100'
```

当访问 https://2886795277-80-ollie02.environments.katacoda.com/user/1 时，`watch`命令没有输出

当访问 https://2886795277-80-ollie02.environments.katacoda.com/user/101 时，`watch`会打印出结果。

### 当异常时捕获

`watch`命令支持`-e`选项，表示只捕获抛出异常时的请求：

```bash
watch com.example.demo.arthas.user.UserController * "{params[0],throwExp}" -e
```

### 按照耗时进行过滤

watch命令支持按请求耗时进行过滤，比如：

```bash
watch com.example.demo.arthas.user.UserController * '{params, returnObj}' '#cost>200'
```

## [案例: 热更新代码](https://alibaba.github.io/arthas/arthas-tutorials?language=cn&id=arthas-advanced)

下面介绍通过`jad`/`mc`/`redefine` 命令实现动态更新代码的功能。

目前，访问 http://localhost/user/0 ，会返回500异常：

```bash
curl http://localhost/user/0
{"timestamp":1550223186170,"status":500,"error":"Internal Server Error","exception":"java.lang.IllegalArgumentException","message":"id < 1","path":"/user/0"}
```

下面通过热更新代码，修改这个逻辑。

### jad反编译UserController

```
jad --source-only com.example.demo.arthas.user.UserController > /tmp/UserController.java
```

jad反编译的结果保存在 `/tmp/UserController.java`文件里了。

再打开一个`Terminal 3`，然后用vim来编辑`/tmp/UserController.java`：

```
vim /tmp/UserController.java
```

比如当 user id 小于1时，也正常返回，不抛出异常.

### sc查找加载UserController的ClassLoader

```
sc -d *UserController | grep classLoaderHash
$ sc -d *UserController | grep classLoaderHash
 classLoaderHash   1be6f5c3
```

可以发现是 spring boot `LaunchedURLClassLoader@1be6f5c3` 加载的。

### mc

保存好`/tmp/UserController.java`之后，使用`mc`(Memory Compiler)命令来编译，并且通过`-c`参数指定ClassLoader：

```bash
mc -c 1be6f5c3 /tmp/UserController.java -d /tmp
$ mc -c 1be6f5c3 /tmp/UserController.java -d /tmp
Memory compiler output:
/tmp/com/example/demo/arthas/user/UserController.class
Affect(row-cnt:1) cost in 346 ms
```

### redefine

再使用`redefine`命令重新加载新编译好的`UserController.class`：

```
redefine /tmp/com/example/demo/arthas/user/UserController.class
```

### 热修改代码结果

`redefine`成功之后，再次访问 https://2886795277-80-ollie02.environments.katacoda.com/user/0 ，结果是：

```
{
  "id": 0,
  "name": "name0"
}
```

## [案例: 动态更新应用Logger Level](https://alibaba.github.io/arthas/arthas-tutorials?language=cn&id=arthas-advanced)

### 查找UserController的ClassLoader

```bash
sc -d com.example.demo.arthas.user.UserController | grep classLoaderHash

```

### 用ognl获取logger

```
ognl -c 1be6f5c3 '@com.example.demo.arthas.user.UserController@logger'

```

### 单独设置UserController的logger level

```
ognl -c 1be6f5c3 '@com.example.demo.arthas.user.UserController@logger.setLevel(@ch.qos.logback.classic.Level@DEBUG)'
```

再次获取`UserController@logger`，可以发现已经是`DEBUG`了：

```
ognl -c 1be6f5c3 '@com.example.demo.arthas.user.UserController@logger'
```

### 修改logback的全局logger level

通过获取`root` logger，可以修改全局的logger level：

```
ognl -c 1be6f5c3 '@org.slf4j.LoggerFactory@getLogger("root").setLevel(@ch.qos.logback.classic.Level@DEBUG)'
```

