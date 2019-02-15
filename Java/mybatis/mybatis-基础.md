### 一.使用

​	通过SqlSessionFactory获取一个sqlSession完成与数据库的会话

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@Slf4j
public class ReviewApplicationTests {

    @Autowired
    private SqlSessionFactory sqlSessionFactory;

    @Test
    public void test01(){

        SqlSession sqlSession = sqlSessionFactory.openSession();

        SysUserMapper mapper = sqlSession.getMapper(SysUserMapper.class);

        SysUser user = mapper.findUserById("asdads");

        log.info("{}",user);

    }
}
```

### 二.全局配置

#### 1) setting

##### (1) mapUnderScoreToCamelCase

```yaml
mybatis:
  configuration:
    map-underscore-to-camel-case: true
```

#### 2) typeAlias

##### (1).在spring.yml文件中指定

```yaml
mybatis:
 type-aliases-package: xxx
```

(2) 在entities类中使用@Alias注解

```java
@Alias("sysUser")
public class SysUser implements Serializable {
```

**注意: 别名不区分大小写**

### 三.映射配置文件(mapper文件)

#### 1)获取自增主键

​	mybatis是利用statement.getGenreatedKeys()获取主键的

- useGeneratedKeys

  使用自增主键获取主键值策略

- keyProperty 

  指定对应的主键属性，也就是mybatis获取到主键值以后，将这个值封装给javaBean的哪个属性

```xml
<insert id="insertRole" 
        useGeneratedKeys="true" 
        keyProperty="id">
</insert>
```

#### 2)对参数的处理

##### (1)单个参数

​	mybatis不会做特殊处理,

###### ①.取值方式

- \#{参数名}

##### (2)多个参数

​	多个参数会被封装成一个map。

- key: param1. . . paramN ,或者参数的索引也可以
- value:传入的参数值

\#{}就是从map中获取指定的key的值;

###### ①.取值方式

- \#{param0}, #{0}

##### (3)命名参数

​	明确指定封装参数时map的key,在定义接口的参数前面添加上:

```java
@Param("key") 
```

多个参数会被封装成一个map,

- key:使用@Param注解指定的值
- value:参数值

###### ①.取值方式

- \#{key}

##### (4)使用pojo

​	如果多个参数正好是我们业务逻辑的数据模型，我们就可以直接传入pojo;

###### ①.取值方式

- \#{属性名}

##### (5)使用map

​	如果多个参数不是业务模型中的数据，没有对应的pojo,不经常使用，为了方便，我们也可以传入map

###### ①.取值方式

- \#{key}

##### (6)使用TO

​	如果多个参数不是业务模型中的数据，但是经常要使用，推荐来编写一个TO (Transfer Object) 数据传输对象

如分页的page: 

```
Page{
int index;
int size;
}
```

###### ①.取值方式

- \#{key}

##### (7) collection集合或者Array数组

 	也会把集合或者数组放入map中

###### ①.key的取值

1. 如果是collect集合则key为: collection
2. 如果是list 则key为: list
3. 如果是array数组,则key为: array

###### ②,取值方式

1. 集合: #{collection.key}
2. list : #{list[0]}
3. array数组: #{array[0]}  

##### (8) 练习

```java
/**
	1. id 
		获取: #{id/param1} 
	2. lastName
		获取: #{param2}
 */
public Employee get Emp(@Param("id")Integer id, String lastName) ;
```

```java
/**
	1. id 
		获取 #{param1}
	2. lastName
		#{e.lastName} 或者 #{parma2.lastName}
 */
public Employee get Emp( Integer id, @Param("e") Employee emp);
```

#### 3) #与$ 

##### (1)作用

- \#{}:可以荻取map中的值或者pojo対象属性的值;

- ${}:可以荻取map中的值或者pojo対象属性的值;

##### (2)区别

- \#{} :是以预编译的形式，将参数设置到sql语句中; PreparedStatement;防止sql注入。

- ${}: 取出的值直接拼装在sql语句中;会有安全问题。

##### (3)使用

- \#{}

  1. 大多情况下，我们取参数的值都应该去使用#{};

  2. 规定参数的一些规则:
     javaType、jdbcType、 mode (存储过程)、numericScale、resultMap、typeHandler、 jdbc TypeName、expression (未来准备支持的功能) ;

     jdbcType通常需要在某种特定的条件下被设置:
     在我们数据为null的时候，有些数据库可能不能识别mybatis对null的默认处理。

     比如Oracle (报错) ;JdbcType OTHER: 无效的类型;

     因为mybatis对所有的null都映射的是原生Jdbc的OTHER类型，而Oracl 不支持这种这个数据类型
     由于全局配置中: jdbcTypeForNull=OTHER; 

     oracle不支持，有两种办法
     1、#{email, jdbcType=OTHER};
     2、设置mybatis的配置文件中的jdbcTypeForNull为NULL

- ${}

  原生jdbc不支持占位符的地方我们就可以使用\${ }进行取值，比如分表、排序。。。;

  按照年份分表拆分

  ```sql
  select * from \${year}_ salary where xxx;
  select * from tb1_ employee order by \${f_ name} ${order}
  ```

#### 4) 结果集

##### (1) list

​	直接在resultType中写list里面放的对象类型

- mapper.xml

  ```xml
   <select id="findUserLikeName" resultType="com.tgy.mybatis.review.entity.SysUser">
          select
          <include refid="user_field_columns_name">
  
          </include>
  
          from sys_user
          where  user_name like '${likeP}'    
  </select>
  ```

- mapper.java

  ```java
  List<SysUser> findUserLikeName(@Param("likeP") String likeParam);
  ```

##### (2)map

​	直接在resultType中写map  (mybatis 给Map取了别名为:map)

- mapper.xml

  ```xml
   <select id="findUserByIdToMap" resultType="map">
  
          select
          <include refid="user_field_columns_name">
  
          </include>
  
          from sys_user
          where  `id` = #{id}
  </select>
  ```

- mapper.java

  ```java
  Map findUserByIdToMap(String userid);
  ```

##### (3)map\<column,POJO\>

​	直接在resultType中写map中value对应的POJO类型

- mapper.xml

  ```xml
  <select id="findUserToKeyIdAndValueUser" resultType="com.tgy.mybatis.review.entity.SysUser">
  
      select
      <include refid="user_field_columns_name">
  
      </include>
  
      from sys_user
      where  user_name like '${like}'
  </select>
  ```

- mapper.java

  ```java
  //相当于去出SysUser中的createTime属性对应的值作为map的key
  @MapKey("createTime")
  Map<String,SysUser> findUserToKeyIdAndValueUser(@Param("like") String likeParma);
  ```

  注意：**@MapKey:指定key对应value中POJO的属性名。**

#### 5) resultMap

##### (1)基本使用

##### (2)association

###### ①. 直接查询

###### ②.分布查询

###### ③.延迟加载

##### (3)collection

###### ①. 直接查询

###### ②.分布查询

###### ③.延迟加载

##### (4)discriminator