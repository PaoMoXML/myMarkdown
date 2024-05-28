[Spring5源码解析10-createBean方法和InstantiationAwareBeanPostProcessor - 掘金 (juejin.cn)](https://juejin.cn/post/6844903965465772046)



```java
protected <T> T doGetBean(
        String name, @Nullable Class<T> requiredType, @Nullable Object[] args, boolean typeCheckOnly)
        throws BeansException {
    
	// 如果这个 name 是 FactoryBean 的beanName (&+beanName),就删除& , 返回beanName ,传入的name也可以是别名,也需要做转换
	// 注意 beanName 和 name 变量的区别,beanName是经过处理的,经过处理的beanName就直接对应singletonObjects中的key
    String beanName = transformedBeanName(name);
    Object beanInstance;

    // Eagerly check singleton cache for manually registered singletons.
    // 根据beanName尝试从singletonObjects获取Bean
	// 获取不到则再尝试从earlySingletonObjects,singletonFactories获取Bean
	// 这段代码和解决循环依赖有关
    Object sharedInstance = getSingleton(beanName);
    // 第一次进入sharedInstance肯定为null
    if (sharedInstance != null && args == null) {
        if (logger.isTraceEnabled()) {
            if (isSingletonCurrentlyInCreation(beanName)) {
                logger.trace("Returning eagerly cached instance of singleton bean '" + beanName +
                        "' that is not fully initialized yet - a consequence of a circular reference");
            }
            else {
                logger.trace("Returning cached instance of singleton bean '" + beanName + "'");
            }
        }
        // 如果sharedInstance不为null,也就是非第一次进入
		// 为什么要调用 getObjectForBeanInstance 方法,判断当前Bean是不是FactoryBean,如果是,那么要不要调用getObject方法
		// 因为传入的name变量如果是(&+beanName),那么beanName变量就是(beanName),也就是说,程序在这里要返回FactoryBean
		// 如果传入的name变量(beanName),那么beanName变量也是(beanName),但是,之前获取的sharedInstance可能是FactoryBean,需要通过sharedInstance来获取对应的Bean
		// 如果传入的name变量(beanName),那么beanName变量也是(beanName),获取的sharedInstance就是对应的Bean的话,就直接返回Bean
        beanInstance = getObjectForBeanInstance(sharedInstance, name, beanName, null);
    }

    else {
        // Fail if we're already creating this bean instance:
        // We're assumably within a circular reference.
        // 判断是否循环依赖
        if (isPrototypeCurrentlyInCreation(beanName)) {
            throw new BeanCurrentlyInCreationException(beanName);
        }

        // Check if bean definition exists in this factory.
        // 获取父BeanFactory,一般情况下,父BeanFactory为null,如果存在父BeanFactory,就先去父级容器去查找
        BeanFactory parentBeanFactory = getParentBeanFactory();
        if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
            // Not found -> check parent.
            String nameToLookup = originalBeanName(name);
            if (parentBeanFactory instanceof AbstractBeanFactory) {
                return ((AbstractBeanFactory) parentBeanFactory).doGetBean(
                        nameToLookup, requiredType, args, typeCheckOnly);
            }
            else if (args != null) {
                // Delegation to parent with explicit args.
                return (T) parentBeanFactory.getBean(nameToLookup, args);
            }
            else if (requiredType != null) {
                // No args -> delegate to standard getBean method.
                return parentBeanFactory.getBean(nameToLookup, requiredType);
            }
            else {
                return (T) parentBeanFactory.getBean(nameToLookup);
            }
        }
        
		// 创建的Bean是否需要进行类型验证,一般情况下都不需要
        if (!typeCheckOnly) {
            // 标记 bean 已经被创建
            markBeanAsCreated(beanName);
        }

        StartupStep beanCreation = this.applicationStartup.start("spring.beans.instantiate")
                .tag("beanName", name);
        try {
            if (requiredType != null) {
                beanCreation.tag("beanType", requiredType::toString);
            }
            
            // 获取其父类Bean定义,子类合并父类公共属性
            RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
            checkMergedBeanDefinition(mbd, beanName, args);

            // Guarantee initialization of beans that the current bean depends on.
            // 获取当前Bean依赖的Bean的名称 ,@DependsOn
            String[] dependsOn = mbd.getDependsOn();
            if (dependsOn != null) {
                for (String dep : dependsOn) {
                    if (isDependent(beanName, dep)) {
                        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                "Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
                    }
                    // 如果当前Bean依赖其他Bean,把被依赖Bean注册给当前Bean
                    registerDependentBean(dep, beanName);
                    try {
                        // 先去创建所依赖的Bean
                        getBean(dep);
                    }
                    catch (NoSuchBeanDefinitionException ex) {
                        throw new BeanCreationException(mbd.getResourceDescription(), beanName,
                                "'" + beanName + "' depends on missing bean '" + dep + "'", ex);
                    }
                }
            }

            // Create bean instance.
            if (mbd.isSingleton()) {
                // 创建单例Bean
                sharedInstance = getSingleton(beanName, () -> {
                    try {
                        return createBean(beanName, mbd, args);
                    }
                    catch (BeansException ex) {
                        // Explicitly remove instance from singleton cache: It might have been put there
                        // eagerly by the creation process, to allow for circular reference resolution.
                        // Also remove any beans that received a temporary reference to the bean.
                        destroySingleton(beanName);
                        throw ex;
                    }
                });
                beanInstance = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
            }

            else if (mbd.isPrototype()) {
                // It's a prototype -> create a new instance.
                // 创建prototype Bean,每次都会创建一个新的对象
                Object prototypeInstance = null;
                try {
                    // 回调beforePrototypeCreation方法，注册当前创建的原型对象
                    beforePrototypeCreation(beanName);
                    // 创建对象
                    prototypeInstance = createBean(beanName, mbd, args);
                }
                finally {
                    // 回调 afterPrototypeCreation 方法，告诉容器该Bean的原型对象不再创建
                    afterPrototypeCreation(beanName);
                }
                beanInstance = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
            }

            else {
                // 如果既不是单例Bean,也不是prototype,则获取其Scope
                String scopeName = mbd.getScope();
                if (!StringUtils.hasLength(scopeName)) {
                    throw new IllegalStateException("No scope name defined for bean '" + beanName + "'");
                }
                Scope scope = this.scopes.get(scopeName);
                if (scope == null) {
                    throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
                }
                try {
                    // 创建对象
                    Object scopedInstance = scope.get(beanName, () -> {
                        beforePrototypeCreation(beanName);
                        try {
                            return createBean(beanName, mbd, args);
                        }
                        finally {
                            afterPrototypeCreation(beanName);
                        }
                    });
                    beanInstance = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
                }
                catch (IllegalStateException ex) {
                    throw new ScopeNotActiveException(beanName, scopeName, ex);
                }
            }
        }
        catch (BeansException ex) {
            beanCreation.tag("exception", ex.getClass().toString());
            beanCreation.tag("message", String.valueOf(ex.getMessage()));
            cleanupAfterBeanCreationFailure(beanName);
            throw ex;
        }
        finally {
            beanCreation.end();
        }
    }
	// 对创建的Bean进行类型检查
    return adaptBeanInstance(name, beanInstance, requiredType);
}
```

