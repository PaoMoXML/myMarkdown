### `getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args)`

> `org.springframework.boot.SpringApplication#getSpringFactoriesInstances(java.lang.Class<T>, java.lang.Class<?>[], java.lang.Object...)`
>
> **1.加载所有jar包下`META-INF/spring.factories`内容**
>
> **2.根据传入的类型、参数类型、入参获取对象实例**

```java
/**
 * @param type 指定的工厂接口类型
 * @param parameterTypes 工厂接口实现类 实例化时用到的 构造函数参数类型
 * @param args 构造函数传参
 * @return Collection 一个包含了类实例化对象的集合
 */
private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
   ClassLoader classLoader = getClassLoader();
   // Use names and ensure unique to protect against duplicates
   Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
   List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
   AnnotationAwareOrderComparator.sort(instances);
   return instances;
}
```

#### 1.LINE 1

> `ClassLoader classLoader = getClassLoader()`

`getClassLoader()`获取指定的类加载器

```java
/**
 * @return ClassLoader 类加载器
 */
public ClassLoader getClassLoader() {
    // this.resourceLoader是构造函数 SpringApplication 传入的
   if (this.resourceLoader != null) {
      return this.resourceLoader.getClassLoader();
   }
    // 当没有传入时（一般不传入）就获取默认类加载器
   return ClassUtils.getDefaultClassLoader();
}
```

[ClassUtils.getDefaultClassLoader()](../工具类/org.springframework.util.ClassUtils.md)

#### 2.LINE 2 LINE3

> `// Use names and ensure unique to protect against duplicates`
>
> `Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader))`

调用了`SpringFactoriesLoader.loadFactoryNames(type, classLoader)`

*步入**`SpringFactoriesLoader.loadFactoryNames(type, classLoader)`**方法*

```java
/**
 * @param factoryType @getSpringFactoriesInstances 传入的 指定的工厂接口类型
 * @param classLoader 指定的类加载器，此类加载器可以为空
 * @return List<String> 指定的工厂接口类型 的在"META-INF/spring.factories"中定义的需实例化类全路径
 */
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
   ClassLoader classLoaderToUse = classLoader;
   if (classLoaderToUse == null) {
       //类加载器如果为空，则使用自己的类加载器
      classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
   }
    //获取类名称
   String factoryTypeName = factoryType.getName();
    //调用完loadSpringFactories(ClassLoader classLoader)后就会返回一个map，根据工厂类型的类名称（factoryTypeName）获取需要实例化的一系列类名称（FactoryNames）
    //获取不到就返回空列表
   return loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

> tips：`V getOrDefault(Object key, V defaultValue)`意为：如果`Map`中这个`key`不存在，就返回指定的`defaultValue`

*步入**`loadSpringFactories(ClassLoader classLoader)`***

```java
/**
 * @param classLoader: 类加载器，目的是用这个类加载器获取指定文件
 * 指定文件 -> FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories"
 * @return "META-INF/spring.factories"文件中的内容封装成的map
 * 此时"spring.factories"中配置的类均未初始化
 */
private static Map<String, List<String>> loadSpringFactories(ClassLoader classLoader) {
    //static final Map<ClassLoader, Map<String, List<String>>> cache = new ConcurrentReferenceHashMap<>();
	//相当于本地缓存
   Map<String, List<String>> result = cache.get(classLoader);
   if (result != null) {
      return result;
   }

   result = new HashMap<>();
   try {
       //加载目录下数据
       //FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories"
      Enumeration<URL> urls = classLoader.getResources(FACTORIES_RESOURCE_LOCATION);
      while (urls.hasMoreElements()) {
         URL url = urls.nextElement();
         UrlResource resource = new UrlResource(url);
         Properties properties = PropertiesLoaderUtils.loadProperties(resource);
         for (Map.Entry<?, ?> entry : properties.entrySet()) {
            String factoryTypeName = ((String) entry.getKey()).trim();
            String[] factoryImplementationNames =
                  StringUtils.commaDelimitedListToStringArray((String) entry.getValue());
            for (String factoryImplementationName : factoryImplementationNames) {
               result.computeIfAbsent(factoryTypeName, key -> new ArrayList<>())
                     .add(factoryImplementationName.trim());
            }
         }
      }

      // Replace all lists with unmodifiable lists containing unique elements
      result.replaceAll((factoryType, implementations) -> implementations.stream().distinct()
            .collect(Collectors.collectingAndThen(Collectors.toList(), Collections::unmodifiableList)));
      cache.put(classLoader, result);
   }
   catch (IOException ex) {
      throw new IllegalArgumentException("Unable to load factories from location [" +
            FACTORIES_RESOURCE_LOCATION + "]", ex);
   }
   return result;
}
```

这个方法就是从`META-INF/spring.factories`文件中获取数据并封装成`Map`的本体
使用这条语句可以获取到
`/E:/MyProjects/spring-boot-2.7.7/spring-boot-project/spring-boot-autoconfigure/build/resources/main/META-INF/spring.factories`
`/E:/MyProjects/spring-boot-2.7.7/spring-boot-project/spring-boot/build/resources/main/META-INF/spring.factories`==其他目录下也有spring.factories，为什么是这两个文件？==
`Enumeration<URL> urls = classLoader.getResources(FACTORIES_RESOURCE_LOCATION)`==待研究==
`UrlResource resource = new UrlResource(url)`==待研究==
返回结果如下
![image-20230405103141185](https://cdn.jsdelivr.net/gh/PaoMoXML/image@main/img/image-20230405103141185.png)

> tips:`unmodifiableList`是只读列表

#### 3.LINE4

> `List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names)`

```java
// 看类名就知道，是要创建Spring工厂的实例
//Class<T> type: @getSpringFactoriesInstances 传入的 指定的工厂接口类型
//Class<?>[] parameterTypes: 反射实例化时用到的对象构造函数入参类型
//ClassLoader classLoader: 类加载器
//Object[] args: 类构造函数入参
//Set<String> names: 需要实例化的类全路径集合
private <T> List<T> createSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes,
			ClassLoader classLoader, Object[] args, Set<String> names) {
		List<T> instances = new ArrayList<>(names.size());
		for (String name : names) {
			try {
                //name是类全路径
				Class<?> instanceClass = ClassUtils.forName(name, classLoader);
                //判断需要实例化的类，是否是`指定的工厂接口类型`的子类或子接口
				Assert.isAssignable(type, instanceClass);
				Constructor<?> constructor = instanceClass.getDeclaredConstructor(parameterTypes);
                //此BeanUtils是spring自己封装的
				T instance = (T) BeanUtils.instantiateClass(constructor, args);
				instances.add(instance);
			}
			catch (Throwable ex) {
				throw new IllegalArgumentException("Cannot instantiate " + type + " : " + name, ex);
			}
		}
		return instances;
	}
```

- `Class<?> instanceClass = ClassUtils.forName(name, classLoader)`

  这个`forName`方法是Spring自己封装的，可以获取到原始类（如int）

  > tips:`Assert.isAssignable(type, instanceClass)`封装了`isAssignableFrom`用于判断`instanceClass`是否是`type`的子类或子接口
  >
  > `isAssignableFrom`是直接判断类，`instanceof`是判断实例

- `getDeclaredConstructor()`返回指定参数类型的`private`和`public`构造器。

#### 4.LINE5

> `AnnotationAwareOrderComparator.sort(instances);`

判断类上是否指定了注解`@Order`，或者java提供的注解`@Priority`

> 总体思路：
>
> 1. `sort`使用了`AnnotationAwareOrderComparator`，他继承了Spring实现的比较器`OrderComparator`
>
> 2. 首先判断list中的内容（`instances`实例化后的类）
>
>    - 实现了`Ordered`接口，如果实现了就直接调用接口中的`getOrder()`获取排序
>
>    - 未实现`Ordered`接口，执行`findOrderFromAnnotation`
>
>      ```java
>      private Integer findOrderFromAnnotation(Object obj) {
>         AnnotatedElement element = (obj instanceof AnnotatedElement ? (AnnotatedElement) obj : obj.getClass());
>         MergedAnnotations annotations = MergedAnnotations.from(element, SearchStrategy.TYPE_HIERARCHY);
>         Integer order = OrderUtils.getOrderFromAnnotations(element, annotations);
>         if (order == null && obj instanceof DecoratingProxy) {
>            return findOrderFromAnnotation(((DecoratingProxy) obj).getDecoratedClass());
>         }
>         return order;
>      }
>      ```
>      
>      1. 首先判断这个类是否是一个`AnnotatedElement（被注解的元素）`的实例，如果不是则使用`getClass`方法获取`Class`-->即为一个`AnnotatedElement`
>      
>         > TIPS：以下类实现了这个接口
>         >
>         > `AccessibleObject`（可访问对象，如：方法、构造器、属性等）
>         >
>         > `Class`（类，就是你用Java语言编程时每天都要写的那个东西）
>         >
>         > `Constructor`（构造器，类的构造方法的类型）
>         >
>         > `Executable`（可执行的，如构造器和方法）
>         >
>         > `Field`（属性，类中属性的类型）
>         >
>         > `Method`（方法，类中方法的类型）
>         >
>         > `Package`（包，你每天都在声明的包的类型）
>         >
>         > `Parameter`（参数，主要指方法或函数的参数，其实是这些参数的类型）
>         >
>         > 只要是这些类 就可以说他是一个`AnnotatedElement`的实例
>      
>      2. 使用`MergedAnnotations from(AnnotatedElement element, SearchStrategy searchStrategy)`方法，获取这个元素的`MergedAnnotations（合并注解）` 
>
>      3. 其后使用`OrderUtils`中的`getOrderFromAnnotations(AnnotatedElement element, MergedAnnotations annotations)`方法判断
>
>         1. 判断是否是类，不是类直接执行真正的获取`@Order`注解值的方法`findOrder(MergedAnnotations annotations)`==未理解为什么这么做==
>      
>            > 详见 [注解的搜索](../注解的搜索/1.MergedAnnotations.md)
>      
>         2. 如果是类，先去缓存中查找，没有则执行`findOrder`
>      
>      4. `findOrder(MergedAnnotations annotations)`==需要注意MergedAnnotation的getInt方法，很奇妙==
>      
>         ```java
>              private static Integer findOrder(MergedAnnotations annotations) {
>             //直接获取`@Order`注解的进行封装后的MergedAnnotation
>                  //此get方法如果发现这个`MergedAnnotations`并没有`Order.class`则会直接返回一个`MissingMergedAnnotation`对象
>            MergedAnnotation<Order> orderAnnotation = annotations.get(Order.class);
>             //如果这里不是`MissingMergedAnnotation`对象`isPresent()`方法就会返回true
>                 if (orderAnnotation.isPresent()) {
>                //MergedAnnotation.VALUE = "value"
>                     //此处也很复杂，没怎么看懂
>                //总之就是去过去注解中，名为`value`的属性
>               return orderAnnotation.getInt(MergedAnnotation.VALUE);
>            }
>             //JAVAX_PRIORITY_ANNOTATION = "javax.annotation.Priority"
>             //@Priority注解，java提供
>             //这边可以看出 Spring的`@Order`注解的生效等级更高
>             //但是网上说@Priority等级高，可能还需要研究下
>            MergedAnnotation<?> priorityAnnotation = annotations.get(JAVAX_PRIORITY_ANNOTATION);
>            if (priorityAnnotation.isPresent()) {
>                //同上
>               return priorityAnnotation.getInt(MergedAnnotation.VALUE);
>            }
>            return null;
>         }
>         ```
>      
>         这里的返回值，就是`@Order`中定义的值了
>      
>      5. **如果一个类没有加上`@Order`注解，那么默认Order值是`Integer.MAX_VALUE`即加载顺序为最低**
