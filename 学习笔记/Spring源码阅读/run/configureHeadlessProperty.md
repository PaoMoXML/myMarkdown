### 源码

```java
public class SpringApplication {
	private void configureHeadlessProperty() {
		System.setProperty(SYSTEM_PROPERTY_JAVA_AWT_HEADLESS,
				System.getProperty(SYSTEM_PROPERTY_JAVA_AWT_HEADLESS, Boolean.toString(this.headless)));
	}
}
```

该方法只做了一件事:设置了一个名为java.awt.headless的系统属性.

其中:
```java
SYSTEM_PROPERTY_JAVA_AWT_HEADLESS="java.awt.headless";
```

又调用了System类的两个方法:

```java
setProperty(String key,String value);
getProperty(String a,String b);
```

上述含义是:

给属性设值System.setProperty(),它的值来源于System.getProperty(),奇怪了,为什么把属性从一个地方取出来,然后又设置到同一个地方,这不是多此一举吗?

其实这是因为System中的两个读写属性的方法不对等.

System中getProperty()有2个重载方法,但却只有一个setProperty()方法,其中getProperty()有单参和双参两方法,单参就是简单的获取属性,有就有,没有就没有,双参则聪明一点,在没有的时候会返回一个调用者指定的默认值,所以经过这样操作后,不管有没有那个属性,最终都能保证有.

所以先取后设.

那么:做了这样的操作后,SpringBoot想干什么呢?

其实是想设置该应用程序,即使没有检测到显示器,也允许其启动.

对于服务器来说,是不需要显示器的,所以要这样设置.