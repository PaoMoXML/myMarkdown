### 加载这个文件使用了如下方法`org.springframework.boot.SpringApplication#getSpringFactoriesInstances(java.lang.Class<T>)`

```java
private <T> Collection<T> getSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes, Object... args) {
   ClassLoader classLoader = getClassLoader();
   // Use names and ensure unique to protect against duplicates
   Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader));
   List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names);
   AnnotationAwareOrderComparator.sort(instances);
   return instances;
}
```

#### 1.`ClassLoader classLoader = getClassLoader()`

`getClassLoader()`获取指定的类加载器

```java
public ClassLoader getClassLoader() {
   if (this.resourceLoader != null) {
      return this.resourceLoader.getClassLoader();
   }
   return ClassUtils.getDefaultClassLoader();
}
```

`this.resourceLoader`如果在`org.springframework.boot.SpringApplication#SpringApplication(org.springframework.core.io.ResourceLoader, java.lang.Class<?>...)`中指定了，则使用指定的`resourceLoader`的类加载器，如果没有指定就返回默认类加载器

#### 2.`Set<String> names = new LinkedHashSet<>(SpringFactoriesLoader.loadFactoryNames(type, classLoader))`

`org.springframework.core.io.support.SpringFactoriesLoader#loadFactoryNames`

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
   ClassLoader classLoaderToUse = classLoader;
   if (classLoaderToUse == null) {
      classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
   }
   String factoryTypeName = factoryType.getName();
   return loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

`return`中掉用了另一个方法：**`loadSpringFactories(ClassLoader classLoader)`返回值是一个`Map<String, List<String>>`**

> tips：`V getOrDefault(Object key, V defaultValue)`意为：如果`Map`中这个`key`不存在，就返回指定的`defaultValue`

```java
private static Map<String, List<String>> loadSpringFactories(ClassLoader classLoader) {
   Map<String, List<String>> result = cache.get(classLoader);
   if (result != null) {
      return result;
   }

   result = new HashMap<>();
   try {
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
![image-20230405103141185](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20230405103141185.png)

> tips:`unmodifiableList`是只读列表

#### 3.`List<T> instances = createSpringFactoriesInstances(type, parameterTypes, classLoader, args, names)`

```java
private <T> List<T> createSpringFactoriesInstances(Class<T> type, Class<?>[] parameterTypes,
			ClassLoader classLoader, Object[] args, Set<String> names) {
		List<T> instances = new ArrayList<>(names.size());
		for (String name : names) {
			try {
				Class<?> instanceClass = ClassUtils.forName(name, classLoader);
				Assert.isAssignable(type, instanceClass);
				Constructor<?> constructor = instanceClass.getDeclaredConstructor(parameterTypes);
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

参数`Class<?>[] parameterTypes`和`Object[] args`并未传入参数

- `Class<?> instanceClass = ClassUtils.forName(name, classLoader)`

  这个`forName`方法是Spring自己封装的，可以获取到原始类（如int）

  > tips:`Assert.isAssignable(type, instanceClass)`封装了`isAssignableFrom`用于判断`instanceClass`是否是`type`的子类或子接口

