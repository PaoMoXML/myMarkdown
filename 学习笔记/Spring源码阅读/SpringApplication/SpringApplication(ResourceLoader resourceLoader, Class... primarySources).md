### 从启动类中的启动方法开始学习

#### `SpringApplication(org.springframework.core.io.ResourceLoader, java.lang.Class<?>...)`

```java
//resourceLoader:
//primarySources：
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
		this.resourceLoader = resourceLoader;
		Assert.notNull(primarySources, "PrimarySources must not be null");
		this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
    	//根据路径推断应用类型
		this.webApplicationType = WebApplicationType.deduceFromClasspath();
		this.bootstrapRegistryInitializers = new ArrayList<>(
				getSpringFactoriesInstances(BootstrapRegistryInitializer.class));
		setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
		this.mainApplicationClass = deduceMainApplicationClass();
}
```

#### LINE1 LINE2 LINE3

> `resourceLoader`==未知待补充==
>
> `Assert.notNull(primarySources, "PrimarySources must not be null");`主类不能为空
>
> `this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));`

1. `primarySources` `springboot`加载的主要来源--->一般就是传入我们新建`springboot`项目时的那个有main方法的类
   1. 启动类不一定是传入自己，它其实需要传入的是带有`@SpringBootApplication`注解的类
   2. `@SpringBootApplication`进入源码后：在上面还有
      1. `@SpringBootConfiguration`==待补充==
      2. `@EnableAutoConfiguration`==待补充==
      3. `@ComponentScan`定义了需要扫描哪些路径下的`@Component`
         1. `excludeFilters`：符合条件的才是`bean`
         2. `includeFilters`：符合条件的不是`bean`
         3. 。。。==待补充==

#### LINE4

> `this.webApplicationType = WebApplicationType.deduceFromClasspath();`

1. `webApplicationType`是一个枚举类型，将返回三个状态：

   1. `NONE`：应用程序不应作为web应用程序运行，也不应启动嵌入式web服务器。

      > 非 web 应用程序（不内嵌服务器）

   2. `SERVLET`：该应用程序应该作为基于servlet的web应用程序运行，并且应该启动嵌入式servlet web服务器。

      > 内嵌基于 servlet 的 web 服务器（如：Tomcat，Jetty，Undertow 等）

   3. `REACTIVE`：该应用程序应作为响应式web应用程序运行，并应启动嵌入式响应式web服务器。

      > 内嵌基于反应式的 web 服务器（如： Netty）

2. `WebApplicationType.deduceFromClasspath()`方法名`deduce`为推断：从类路径推断web应用类型

   其中有三个类路径:

   - `SERVLET_INDICATOR_CLASSES = { "javax.servlet.Servlet",
           "org.springframework.web.context.ConfigurableWebApplicationContext" }`
   - `WEBMVC_INDICATOR_CLASS = "org.springframework.web.servlet.DispatcherServlet"`
   - `WEBFLUX_INDICATOR_CLASS = "org.springframework.web.reactive.DispatcherHandler"`
   - `JERSEY_INDICATOR_CLASS = "org.glassfish.jersey.servlet.ServletContainer"`

   1. 当项目中存在 `DispatcherHandler` 这个类，且不存在 `DispatcherServlet` 类和`ServletContainer`类时，程序的应用类型就是 **REACTIVE**，也就是他会**加载嵌入一个反应式的 web 服务器**。

   2. 当项目中 `Servlet`和 `ConfigurableWebApplicationContext`其中一个不存在时，则程序的应用类型为 **NONE**，它并不会加载内嵌任何 web 服务器。

      > 例：使用`smoketest.activemq.SampleActiveMQApplication`启动

   3. 除了上面两种情况外，其余的都按 SERVLET 类型处理，会内嵌一个 servlet 类型的 web 服务器。

      > 例：使用`smoketest.tomcat.SampleTomcatApplication`启动



#### LINE5

> `this.bootstrapRegistryInitializers = new ArrayList<>(
> 				getSpringFactoriesInstances(BootstrapRegistryInitializer.class));`

1. `BootstrapRegistryInitializer`是一个函数式接口==功能待补充==，[`getSpringFactoriesInstances()`](./getSpringFactoriesInstances.md)





