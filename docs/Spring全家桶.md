# Spring全家桶

### Spring的核心机制

- **控制反转（IOC）：**
- **面向切面编程（AOP）：**

### Spring容器中的Bean

开发者使用Spring框架主做两件事：1.开发Bean2.配置Bean，对于Spring框架要做的是根据配置文件来创建Bean实例，并调用Bean实例方法完成“依赖注入”------这就是IOC本质。Bean的作用域

- 单例（Singleton）作用域：在整个应用程序中，Spring容器只创建一个Bean实例。
- 原型（Prototype）作用域：每次通过容器的getBean()方法获取prototype作用域的Bean时，都将产生一个新的Bean实例。
- 会话（Session）作用域：每个会话（Session）都对应一个Bean实例。在Web应用程序中，对于每个用户会话，Spring容器都会创建一个独立的Bean实例。
- 请求（Request）作用域：每个HTTP请求都对应一个Bean实例。在Web应用程序中，对于每个请求，Spring容器都会创建一个独立的Bean实例。

**特别地：**

- 如果不指定Bean的作用域，Spring默认使用singleton作用域。
- prototype作用域的Bean的创建、销毁代价比较大。
- singleton作用域的Bean实例一旦创建，就可以重复使用，因此，应该尽量避免将Bean设置成prototype作用域。

### AOP的基本概念

- **切面（Aspect）：**切面由JoinPoint和Advice组成，既包含了横切逻辑的定义，也包括了切入点的定义。 Spring AOP就是负责实施切面的框架，它将切面所定义的横切逻辑**织入**到切面所指定的JoinPoint中。
- **目标对象（Target）：**目标对象指将要被增强的对象，即包含**主业务逻辑**的类对象。或者说是被一个或者多个切面所Advice的对象。
- **连接点（JoinPoint）：**连接点就是被拦截到的程序执行点，因为Spring只支持方法类型的连接点，所以在Spring中连接点就是被拦截到的方法。
- **切点（PoingtCut）：**切点就是对连接点进行拦截的条件定义
- **通知（Advice）：**通知是指拦截到连接点之后要执行的代码，包括了around、before、after等不同类型的通知，spring aop以拦截器来实现通知模型。并维护一个以连接点为中心的拦截器链。
- **织入（Weaving）：**织入是将切面和业务逻辑对象连接起来，并创建通知代理的过程，织入可以在**编译时、类加载时和运行时**完成，编译时为静态代理，运行时为动态代理。



