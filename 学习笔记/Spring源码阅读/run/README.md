## INDEX

#### [o.s.b.SpringApplication#run(java.lang.String...)](run(String... args).md)

##### 1. 启动上下文

- [ ] [ConfigurableBootstrapContext](./1.BootstrapContext.md) -> 40% 如何使用未掌握

##### 2. 环境准备

- [ ] ConfigurableEnvironment -> *0%*

##### 3. Spring事件发布和监听

- [x] [RunListeners和Listeners](./3.RunListeners和Listeners.md) -> *100%*

##### 4. 应用上下文和Bean

- [ ] [o.s.b.SpringApplication#createApplicationContext](./Bean/Context的创建.md) -> *10%*
  - [ ] Context创建


##### refresh

- [ ] [o.s.c.s.AbstractApplicationContext#refresh](./refresh/refresh.md) ->  *5%*
  - [x] [o.s.c.s.AbstractApplicationContext#prepareRefresh](./refresh/1.prepareRefresh.md) -> *100%*
    - [ ] prepareRefresh中initPropertySources方法的占位符相关 -> *0%*
  - [ ] obtainFreshBeanFactory
  - [ ] prepareBeanFactory
  - [ ] postProcessBeanFactory
  - [ ] invokeBeanFactoryPostProcessors
  - [ ] registerBeanPostProcessors
  - [ ] initMessageSource
  - [ ] initApplicationEventMulticaster
  - [ ] onRefresh
  - [ ] registerListeners
  - [ ] finishBeanFactoryInitialization
  - [ ] finishRefresh