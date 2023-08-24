## 解析源码

```java
public class SpringApplication {
    
    public ConfigurableApplicationContext run(String... args) {
        //...
        ConfigurableApplicationContext context = null;
        //...
       	context = createApplicationContext();
        context.setApplicationStartup(this.applicationStartup);
    }
    
}
```

### 初始化创建

```java
public class SpringApplication {
    /**
	 * 用于创建｛@link ApplicationContext｝的策略方法。
	 * 默认情况下，在返回到合适的默认值之前，此方法将尊重任何显式设置的应用程序上下文类或工厂
	 * @return 应用程序上下文（尚未刷新）
	 * @see #setApplicationContextFactory(ApplicationContextFactory)
	 */
	protected ConfigurableApplicationContext createApplicationContext() {
		return this.applicationContextFactory.create(this.webApplicationType);
	}
    
    /**
     * 设置将被调用以创建应用程序上下文的工厂。
     * 如果未设置，则默认为一个工厂，该工厂将
     * 为servlet web应用程序创建｛@link AnnotationConfigServletWebServerApplicationContext｝，
     * 为反应式web应用程序建立｛@linkAnnotationConfiguration ReactiveWebServerApplicationContext}，
     * 为非web应用程序建立｛@LinkAnnotationConfig ApplicationContext｝。
     */
    public void setApplicationContextFactory(ApplicationContextFactory applicationContextFactory) {
		this.applicationContextFactory = (applicationContextFactory != null) ? applicationContextFactory
				: ApplicationContextFactory.DEFAULT;
	}
}
```

`createApplicationContext`：根据工厂创建上下文

`setApplicationContextFactory`：设置创建上下文的工厂

默认没有设置工厂将会使用`DefaultApplicationContextFactory`

```java
class DefaultApplicationContextFactory implements ApplicationContextFactory {
    /**
     * 根据给定的{@code-webApplicationType}，为{@linkSpringApplication}创建{@link ConfigurationApplicationContext 应用程序上下文}。
     */
    @Override
	public ConfigurableApplicationContext create(WebApplicationType webApplicationType) {
		try {
			return getFromSpringFactories(webApplicationType, ApplicationContextFactory::create,
					AnnotationConfigApplicationContext::new);
		}
		catch (Exception ex) {
			throw new IllegalStateException("Unable create a default ApplicationContext instance, "
					+ "you may need a custom ApplicationContextFactory", ex);
		}
	}

	private <T> T getFromSpringFactories(WebApplicationType webApplicationType,
			BiFunction<ApplicationContextFactory, WebApplicationType, T> action, Supplier<T> defaultResult) {
        //根据ApplicationContextFactory.class 从 spring.factories中读取对应实现的类，并实例化
		for (ApplicationContextFactory candidate : SpringFactoriesLoader.loadFactories(ApplicationContextFactory.class,
				getClass().getClassLoader())) {
			T result = action.apply(candidate, webApplicationType);
			if (result != null) {
				return result;
			}
		}
		return (defaultResult != null) ? defaultResult.get() : null;
	}
}
```

### AnnotationConfigApplicationContext

```java
/**
 * 独立的应用程序上下文，接受组件类作为输入——特别是@Configuration注释的类，但也接受使用javax.inject注释的普通@component类型和JSR-330兼容的类。
 * 允许使用register（Class…）逐个注册类，也允许使用scan（String…）扫描类路径。
 * 在多个@Configuration类的情况下，在后面的类中定义的@Bean方法将覆盖在前面的类中所定义的方法。这可以通过一个额外的@Configuration类来有意覆盖某些bean定义。
 * 有关用法示例，请参阅@Configuration的javadoc。
 * 请参阅:
 * register，scan，AnnotatedBeanDefinitionReader，ClassPathBeanDefinitionScanner，org.springframework.context.support.GenericXmlApplicationContext
 */
public class AnnotationConfigApplicationContext extends GenericApplicationContext implements AnnotationConfigRegistry {
    //...
}
```

[深入理解Spring容器初始化（一）：上下文的初始化 - Createsequence - 博客园 (cnblogs.com)](https://www.cnblogs.com/Createsequence/p/16585528.html)
