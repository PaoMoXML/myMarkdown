### 从启动类中的启动方法开始学习

#### `SpringApplication(org.springframework.core.io.ResourceLoader, java.lang.Class<?>...)`

```java
public SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
		this.resourceLoader = resourceLoader;
		Assert.notNull(primarySources, "PrimarySources must not be null");
		this.primarySources = new LinkedHashSet<>(Arrays.asList(primarySources));
		this.webApplicationType = WebApplicationType.deduceFromClasspath();
		this.bootstrapRegistryInitializers = new ArrayList<>(
				getSpringFactoriesInstances(BootstrapRegistryInitializer.class));
		setInitializers((Collection) getSpringFactoriesInstances(ApplicationContextInitializer.class));
		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
		this.mainApplicationClass = deduceMainApplicationClass();
	}
```

1. `resourceLoader`==未知待补充==

2. `primarySources` `springboot`加载的主要来源--->一般就是传入我们新建`springboot`项目时的那个有main方法的类

   1. 启动类不一定是传入自己，它其实需要传入的是带有`@SpringBootApplication`注解的类
   2. `@SpringBootApplication`进入源码后：在上面还有
      1. `@SpringBootConfiguration`==待补充==
      2. `@EnableAutoConfiguration`==待补充==
      3. `@ComponentScan`定义了需要扫描哪些路径下的`@Component`
         1. `excludeFilters`：符合条件的才是`bean`
         2. `includeFilters`：符合条件的不是`bean`
         3. 。。。==待补充==

3. `webApplicationType`是一个枚举类型，将返回三个状态：`NONE`、`SERVLET`、`REACTIVE`

   `WebApplicationType.deduceFromClasspath()`方法名`deduce`为推断：从类路径推断web应用类型

   其中有三个类路径

   1. `WEBMVC_INDICATOR_CLASS = "org.springframework.web.servlet.DispatcherServlet"`==含义待补充==
   2. `WEBFLUX_INDICATOR_CLASS = "org.springframework.web.reactive.DispatcherHandler"`==含义待补充==
   3. `JERSEY_INDICATOR_CLASS = "org.glassfish.jersey.servlet.ServletContainer"`==含义待补充==

   **我启动`spring-boot-smoke-test-aop`这个测试，经过`deduceFromClasspath`方法后返回的是`none`，程序运行完后直接结束了**

