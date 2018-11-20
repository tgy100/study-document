#### 1.@ComponentScan注解

根据给的包名，扫描包下的bean到spring容器中 。

##### (1) excludeFilters

排除扫描的bean，根据FilterType得知有如下方式

1. FilterType.ANNOTATION

   根据annotation排除

   ```java
   excludeFilters = {@ComponentScan.Filter(type=FilterType.ANNOTATION,classes = Controller.class)}
   ```

2. FilterType.ASSIGNABLE_TYPE

   根据类的类型排除

   ```java
   excludeFilters = {@ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE,classes = Person.class)}
   ```

3. FilterType.CUSTOM

   实现TypeFilter接口，自定义排除规则

   ```
   excludeFilters = {@ComponentScan.Filter(type = FilterType.CUSTOM,classes = MyTypeFilter.class)},
   ```

   ```java
   public class MyTypeFilter implements TypeFilter {
       @Override
       public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
   
           System.out.println(metadataReader.getClassMetadata().getClassName());
   
           if (metadataReader.getClassMetadata().getClassName().contains("My")){
   
               return true;
           }
   
           return false;
       }
   }
   ```

##### (2)includeFilters

​	包含扫描的bean，与排除类型一样，都有三种主要的保护方式，但是要使其起作用，必须设置ComponentScan的==use-default-Filters==为false

```java
@ComponentScan(value = "com.tgy.spring.annotation",
               includeFilters = {@ComponentScan.Filter(type = FilterType.CUSTOM,classes = MyTypeFilter.class)},
               useDefaultFilters = false)
```

#### 2.@Scope注解

1. ConfigurableBeanFactory.SCOPE_PROTOTYPE

   表示bean为多例，==每次获取创建一个新的bean==

   ```java
   @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
    @Bean
    public Person person(){
   
   	System.out.println("person创建");
   	return new Person();
    }
   ```

   ```java
   AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MyConfig01.class);
   
   Person person01 = applicationContext.getBean(Person.class);
   Person person02 = applicationContext.getBean(Person.class);
   //多例，下面输出false
   System.out.println(person01 == person02);
   ```

2. ConfigurableBeanFactory.SCOPE_SINGLETON

   表示bean为单例，在spring容器==初始化==的时候创建，以后每次获取都从缓存中获取。spring默认的bean都是该选项。即==默认不写就是单例==。

   ```java
    @Scope(value = ConfigurableBeanFactory.SCOPE_SINGLETON)
    @Bean
    public Person person(){
   
   	System.out.println("person创建");
   	return new Person();
    }
   ```

   ```java
   AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MyConfig01.class);
   
   Person person01 = applicationContext.getBean(Person.class);
   Person person02 = applicationContext.getBean(Person.class);
   //单例，下面输出true
   System.out.println(person01 == person02);
   ```

#### 3.@Lazy注解

​	由于spring默认bean是在Spring容器==初始化==的时候创建，为了把bean的创建推迟到==获取bean的时候创建==，可以使用该注解

```java
@Lazy
@Bean
public Person person(){

    System.out.println("person创建");
    return new Person();
}
```

```java
//在这里创建bean
Person person01 = applicationContext.getBean(Person.class);
```

#### 4.@Condition注解

 通过实现@Condition注解指定的Condition接口，对当前类是否加入spring容器做判断

1. 实现Condition接口

   ```java
   public class MyConditionImp implements Condition {
   
   
       public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
           //获取自定义的注解的参数信息
           Map<String, Object> myCondition = metadata.getAnnotationAttributes("con.tgy.annotation.MyCondition");
           System.out.println(myCondition);
           
           return false;
       }
   }
   ```

2. 在对应的方法上面加上该注解

   ```java
   @Conditional(MyConditionImp.class)
   @Bean
   public Animal animal(){
   
   	return new Animal();
   }
   ```

==注意:==

- 这样实现方式不能在注解里面带参数，做判断。要想带参数，需要自定义自己的annotation

  1. 定义注解,在自定义的注解上面加上@Conditional注解，同时指定对应的实现了Condition注解的类。

     ```java
     @Target({ElementType.TYPE, ElementType.METHOD})
     @Retention(RetentionPolicy.RUNTIME)
     @Conditional(MyConditionImp.class)
     public @interface MyCondition {
     
         String className() default "";
     }
     ```

  2. 实现的Condition接口类与上面的一致

  3. 使用只是把@Condition换成自定义的@MyCondition

     ```java
     @MyCondition(className = "animal")
     @Bean
     public Animal animal(){
     
     	return new Animal();
     }
     ```

  4. 这里只是简单的演示，要想自定义condition注解，可以参考springBoot里面的==@ConditionalOnClass==和==@ConditionalOnBean==等待

- ==@Condition在spring源码中的处理==

  ==ConditionEvaluator==类的方法

  ```java
  public boolean shouldSkip(@Nullable AnnotatedTypeMetadata metadata, @Nullable ConfigurationPhase phase) {
  	if (metadata == null || !metadata.isAnnotated(Conditional.class.getName())) {
  		return false;
  	}
  
  	if (phase == null) {
  		if (metadata instanceof AnnotationMetadata &&
  				ConfigurationClassUtils.isConfigurationCandidate((AnnotationMetadata) metadata)) {
  			return shouldSkip(metadata, ConfigurationPhase.PARSE_CONFIGURATION);
  		}
  		return shouldSkip(metadata, ConfigurationPhase.REGISTER_BEAN);
  	}
  
      
  	List<Condition> conditions = new ArrayList<>();
  	for (String[] conditionClasses : getConditionClasses(metadata)) {
  		for (String conditionClass : conditionClasses) {
              //获取所有实现了Condition接口的类
  			Condition condition = getCondition(conditionClass, this.context.getClassLoader());
  			conditions.add(condition);
  		}
  	}
  
  	AnnotationAwareOrderComparator.sort(conditions);
  
  	for (Condition condition : conditions) {
  		ConfigurationPhase requiredPhase = null;
  		if (condition instanceof ConfigurationCondition) {
  			requiredPhase = ((ConfigurationCondition) condition).getConfigurationPhase();
  		}
          //定义Condition对应的接口
  		if ((requiredPhase == null || requiredPhase == phase) && !condition.matches(this.context, metadata)) {
  			return true;
  		}
  	}
  
  	return false;
  }
  ```

#### 5.@Import注解

1. 直接把要注册的bean放入到该注解中

   ```java
   //把Person加到Spring容器中
   @Configuration
   @Import({Person.class,MyImportSelector.class,MyImportBeanDefinitionRegistrar.class})
   @EnableAsync
   public class SpringConfig {
       
   }
   ```

2. 把实现了ImportSelector接口的类加入到注解中

   ```java
   //把实现了ImportSelect接口的MyImportSelector的selectImports返回的bean加到Spring容器中
   @Configuration
   @Import({MyImportSelector.class,MyImportBeanDefinitionRegistrar.class})
   @EnableAsync
   public class SpringConfig {
       
   }
   ```

   ```java
   public class MyImportSelector implements ImportSelector {
       @Override
       public String[] selectImports(AnnotationMetadata importingClassMetadata) {
   
   
           return new String[]{"con.tgy.bean.Red"};
       }
   }
   ```

3. 把实现了ImportBeanDefinitionRegistrar接口的类加入到注解中

   ```java
   //把实现了MyImportBeanDefinitionRegistrar接口的MyImportBeanDefinitionRegistrar的registerBeanDefinitions方法里面注册要加入的bean
   @Configuration
   @Import({MyImportBeanDefinitionRegistrar.class})
   @EnableAsync
   public class SpringConfig {
       
   }
   ```

   ```java
   public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {
       @Override
       public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
   
           RootBeanDefinition rootBeanDefinition  = new RootBeanDefinition();
           rootBeanDefinition.setBeanClass(Green.class);
           registry.registerBeanDefinition("green",rootBeanDefinition);
       }
   }
   
   ```

#### 6.FactoryBean接口

​	该接口的作用是实现把bean加入到spring容器中

```java
public interface FactoryBean<T> {

	/**
	 *	返回需要加入Spring容器的实例
	 */
	@Nullable
	T getObject() throws Exception;

	/**
	 *	返回需要加入Spring容器的类型
	 */
	@Nullable
	Class<?> getObjectType();

	/**
	 *	创建的bean是否是单列
	 */
	default boolean isSingleton() {
		return true;
	}
}

```



#### 7.bean的初始化和销毁

​	bean的初始化指的是bean所有的属性都装配完成之后调用的方法。销毁指的是容器关闭(调用ApplicationContext的close方法)的时候调用的方法。

##### (1)在@Bean中指定

1. 使用方式

   (1). 定义两个方法init,destroy，分别对应初始化与销毁

   ```java
   public class Person {
   
      
       public void init(){
   
           //InitDestroyAnnotationBeanPostProcessor 处理的@PostConstruct注解
           //初始化在类创建同时属性赋值好之后调用
           System.out.println(getClass().getName() + "初始化");
       }
   
       public void destroy(){
   
           //在spring容器停止之前调用
           System.out.println(getClass().getName() + "容器销毁");
       }
   }
   ```

   (2). 在@bean中分别指定initMethod和destroyMethod

   ```java
   @Bean(initMethod = "init",destroyMethod = "destroy")
   public Person animal(@Value("${person.name}") String name){
   
       System.out.println(name);
       return new Person();
   }
   ```

2. 源码分析

   在AbstractAutowireCapableBeanFactory类的invokeInitMethods方法调用初始化方法

   ```java
   if (mbd != null && bean.getClass() != NullBean.class) {
   String initMethodName = mbd.getInitMethodName();
   	if (StringUtils.hasLength(initMethodName) &&
   			!(isInitializingBean && "afterPropertiesSet".equals(initMethodName)) &&
   			!mbd.isExternallyManagedInitMethod(initMethodName)) {
           //执行初始化方法
   		invokeCustomInitMethod(beanName, bean, mbd);
       }
   }
   ```

##### (2)使用@PostConstruct和@PreDestroy

1. 使用方式

   在对应的初始化方法和销毁方法上分别加上对应的注解即可

   ```
   public class Person {
   
       @PostConstruct
       public void init(){
   
           //InitDestroyAnnotationBeanPostProcessor 处理的@PostConstruct注解
           //初始化在类创建同时属性赋值好之后调用
           System.out.println(getClass().getName() + "初始化");
       }
   
       @PreDestroy
       public void destroy(){
   
           //在spring容器停止之前调用
           System.out.println(getClass().getName() + "容器销毁");
       }
   
   }
   
   ```

2. 源码分析

   spring定义了一个proccessor来处理该注解 InitDestroyAnnotationBeanPostProcessor

```java
@Override
public void postProcessBeforeDestruction(Object bean, String beanName) throws BeansException {
	
    //获取metadata
	LifecycleMetadata metadata = findLifecycleMetadata(bean.getClass());
    //调用init方法
	metadata.invokeDestroyMethods(bean, beanName);
}
```

(3)实现InitializingBean和DisposableBean接口

1. 使用

   InitializingBean接口对应初始化方法是：afterPropertiesSet，DisposableBean销毁方法是destroy

   ```java
   public class Green implements InitializingBean,DisposableBean {
   
       /**
        * invokeInitMethods 方法的
        *((InitializingBean) bean).afterPropertiesSet();
        *
        * @throws Exception
        */
       @Override
       public void afterPropertiesSet() throws Exception {
   
   
           System.out.println(getClass().getName() + "初始化");
       }
   
       @Override
       public void destroy() throws Exception {
   
           System.out.println(getClass().getName() + "销毁");
       }
   }
   ```

2. 源码

   在AbstractAutowireCapableBeanFactory类的invokeInitMethods方法调用初始化方法

   ```java
   ((InitializingBean) bean).afterPropertiesSet();
   ```

#### 8.bean的后置处理器(BeanPostProcessor)

在BeanPostProcessor接口中有两个方法，