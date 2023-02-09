### `@Autowired`

```java
@Autowired
ServiceDemo2 serviceDemo2;
```

如果用该方式注入，`Spring`将使用**`ByType`**的方式注入 -> 即启动时将根据类型注入



### `@Resource`

```java
@Resource(name = "ServiceDemo1")
ServiceDemo1 serviceDemo1
```

JDK自带的，`spring`也支持，默认**`byName`**方式获取-> 即启动时将根据`@Service("name")`中定义的`name`注入

```java
@Resource(type = ServiceDemo1.class)
ServiceDemo1 serviceDemo1;
```

指定`type`后将根据**`ByType`**的方式注入



### `@Qualifier`

```java
//默认是byType
@Autowired
//加上Qualifier后将以byName方式获取
@Qualifier("ServiceDemo2")
ServiceDemo2 serviceDemo2;
```

如果使用`@Autowired`时也想用**`byName`**的方式，那需要在`@Autowired`下加上`@Qualifier`



### 总结

`@Resource`装配顺序

1. 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常
2. 如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常
3. 如果指定了type，则从上下文中找到类型匹配的唯一bean进行装配，找不到或者找到多个，都会抛出异常
4. 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配；



| 区别             | `@Autowired`                                                 | `@Resource`                      |
| ---------------- | ------------------------------------------------------------ | -------------------------------- |
| 是否支持`ByName` | √（配合`@Qualifier`）                                        | √                                |
| 是否支持`ByType` | √                                                            | √                                |
| 注解可用范围     | `@Target({ElementType.CONSTRUCTOR, ElementType.METHOD, ElementType.PARAMETER, ElementType.FIELD, ElementType.ANNOTATION_TYPE})` | `@Target({TYPE, FIELD, METHOD})` |

∴`@Autowired`和`@Resource`只在可用范围上有区别