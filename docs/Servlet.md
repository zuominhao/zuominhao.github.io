1. 进程/线程

2. IO

3. 集合

4. 异常

   



### 说一下对Servlet的理解？

Servlet可以说是一个接口，一种规范，要实现servlet的类须实现servlet的几个方法，比如生命周期方法init（）&destory（），还有一个处理请求的service。

但是Servlet不会直接和客户端打交道，它不能直接处理请求，它需要部署在一个servlet容器里面，比如tomcat，tomcat可以说是服务器和servlet容器的结合体，它是负责处理客户端发送过来的http请求，解析URl，然后分发给对应的servlet去处理，然后调用servlet的service方法，service方法返回一个response对象，Tomcat再把这个响应返回给客户端。

SpringMVC框架中的DispatcherServlet是servlet的一个实例，而不是一个Servlet容器，它是Spring MVC框架中的一个特殊的Servlet，部署在Tomcat里面，负责接收所有的HTTP请求并将它们分发到相应的Controller进行处理。除此之外，DispatcherServlet还负责管理和协调Spring MVC框架的其他关键组件，包括处理器映射器、处理器适配器、和视图解析器等，它还负责异常的处理。