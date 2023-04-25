> 平常使用注解一般是直接在`类`、`方法`、`属性`上通过反射获取。如果使用的注解没有在定义时加上`@Inherited`，则无法通过反射父类直接获取到 ==该注解只作用于类==。但是`Spring`的注解如`@Controller`可以发现在注解的定义中还添加了`@Component`，真正实现功能的是其中的`@Component`。
>
> 在阅读源码时，将在[`getSpringFactoriesInstances`](./1.getSpringFactoriesInstances.md)中首先遇到

### 合并注解

#### `MergedAnnotations from(AnnotatedElement element, SearchStrategy searchStrategy)`

```java
static MergedAnnotations from(AnnotatedElement element, SearchStrategy searchStrategy,
        RepeatableContainers repeatableContainers) {

    return from(element, searchStrategy, repeatableContainers, AnnotationFilter.PLAIN);
}
/**
 * @param element 源
 * @param searchStrategy 注解搜索策略
 * @param repeatableContainers 可重入容器
 * @param annotationFilter 注解筛选器，将不需要考虑的注解过滤
 */
static MergedAnnotations from(AnnotatedElement element, SearchStrategy searchStrategy,
        RepeatableContainers repeatableContainers, AnnotationFilter annotationFilter) {

    Assert.notNull(repeatableContainers, "RepeatableContainers must not be null");
    Assert.notNull(annotationFilter, "AnnotationFilter must not be null");
    return TypeMappedAnnotations.from(element, searchStrategy, repeatableContainers, annotationFilter);
}
```

- 注解搜索策略：`SearchStrategy`
  
  > 是一个枚举，定义了注解搜索的策略
  
  - `DIRECT`：只查找元素上直接声明的注解，不包括通过`@Inherited`继承的注解；
  - `INHERITED_ANNOTATIONS`：只查找元素直接声明或通过`@Inherited`继承的注解；
  - `SUPERCLASS`：查找元素直接声明或所有父类的注解；
  - `TYPE_HIERARCHY`：查找元素、所有父类以及实现的父接口的全部注解；
  - `TYPE_HIERARCHY_AND_ENCLOSING_CLASSES`：查找查找元素、所有父类以及实现的父接口、封闭类以及其子类的全部注解。
  
- 可重入容器：`RepeatableContainers` ==待研究==
  
- 注解过滤器：`AnnotationFilter`

  > ```java
  > @FunctionalInterface
  > public interface AnnotationFilter {
  >     // 根据实例匹配
  >     default boolean matches(Annotation annotation) {
  >         return matches(annotation.annotationType());
  >     }
  >     // 根据类型匹配
  >     default boolean matches(Class<?> type) {
  >         return matches(type.getName());
  >     }
  >     // 根据名称匹配
  >     boolean matches(String typeName);
  > }
  > 
  > ```

  其中提供了四组实例：

  - `PLAIN`：忽略：`java.lang`、 `org.springframework.lang`包下的注解
  - `JAVA`：忽略：`java`、` javax`包下的注解
  - `ALL`：忽略所有
  - `NONE`：都不忽略

  > 此处过滤器选择了 `PLAIN`，即当查找的注解属于 `java.lang`、`org.springframework.lang` 包的时候就不进行查找，而是直接从被查找的元素直接声明的注解中获取。这个选择不难理解，`java.lang`包下提供的都是诸如`@Resource`或者 `@Target` 这样的注解，而`springframework.lang`包下提供的则都是 `@Nonnull` 这样的注解，这些注解基本不可能作为有特殊业务意义的元注解使用，因此默认忽略也是合理的。

##### `TypeMappedAnnotations.from(element, searchStrategy, repeatableContainers, annotationFilter);`

```java
static MergedAnnotations from(AnnotatedElement element, SearchStrategy searchStrategy,
			RepeatableContainers repeatableContainers, AnnotationFilter annotationFilter) {

    if (AnnotationsScanner.isKnownEmpty(element, searchStrategy)) {
        return NONE;
    }
    return new TypeMappedAnnotations(element, searchStrategy, repeatableContainers, annotationFilter);
}
```

###### LINE4

```java
static boolean isKnownEmpty(AnnotatedElement source, SearchStrategy searchStrategy) {
    //判断是否是`java`包下的内容 或者是 @Order注解
    if (hasPlainJavaAnnotationsOnly(source)) {
        return true;
    }
    //如果策略是直接搜索，source是class->没有父类，source是method->返回类没有父类 或 method是私有方法
    if (searchStrategy == SearchStrategy.DIRECT || isWithoutHierarchy(source, searchStrategy)) {
        //是桥接方法
        if (source instanceof Method && ((Method) source).isBridge()) {
            return false;
        }
        //类似于原生的getDeclaredAnnotations
        //获取所有的注解，不包含 AnnotationFilter.PLAIN 中的注解 ，不包含会抛出TypeNotPresentException错误的注解
        return getDeclaredAnnotations(source, false).length == 0;
    }
    return false;
}
```

###### LINE7

> `MergedAnnotations`是`MergedAnnotations`的实现类

```java
private TypeMappedAnnotations(AnnotatedElement element, SearchStrategy searchStrategy,
        RepeatableContainers repeatableContainers, AnnotationFilter annotationFilter) {

    this.source = element;
    this.element = element;
    this.searchStrategy = searchStrategy;
    this.annotations = null;
    this.repeatableContainers = repeatableContainers;
    this.annotationFilter = annotationFilter;
}
```

> 总结：可以发现，`MergedAnnotations from(AnnotatedElement element, SearchStrategy searchStrategy)`方法的返回值是`TypeMappedAnnotations`，只是将入参封装到了类中，并未真正的开始搜索注解

### 注解的搜索
