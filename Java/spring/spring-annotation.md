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

