mysql

### 1.select

#### 1)基础查询

- 查询之前指定使用的数据库

  ```sql
  use crud #库名
  ```

- ==``==在数据库中是着重号，区别关键字与其他值(如:下面是区分列名)

  ```sql
  select `name` from product_info
  ```

- 查询常量值

  ```sql
  select 100, `userwx_name` from user_info
  ```

- 查询表达式

  ```sql
  select `product_price`*10 from product_info
  ```

- 查询函数

  ```sql
  #concat函数链接字符串
  select concat('1','2','3'), concat(`product_id`,`product_name`)  from product_info;
  
  #IFNULL函数：当前值为null的时候使用其他值替换
  select concat(`userwx_name`,IFNULL(`userwxhead_image`,'....') ) from user_info
  ```

- 起别名(==as== 或者 ==空格==，别名建议使用单引号或者双引号引起来)

  ```sql
  select `product_price`*10 as 'pp_10' from product_info;
  select `product_price`*10 'pp_10' from product_info;
  ```

- 去重

  ```sql
  #distinct:去重关键字
  select distinct `buyer_name` from order_master
  ```

- \+ 号的作用

  ```sql
  #不能转换成数字类型，则直接使用0
  select 100+'ss'; #100
  # 如果有一个值是null，则结果直接是null
  select 100+ NULL; # null 
  # 字符串能转换成数字，则转换成数字再加
  select 100 + '12'; # 112
  ```

- select语句的执行顺序

  1. from语句

  2. where语句(结合条件)

  3. start with语句

  4. connect by语句

  5. where语句

     ==由于where在select 之前执行，所以where之中不能使用select里面取的别名==

     ```sql
     #报错：1054 - Unknown column 'acb' in 'where clause', Time: 0.000000s
     SELECT id , INSTR(id,'26') ins 
     FROM order_info 
     where ins > 0;
     ```

  6. group by语句

  7. having语句

  8. model语句

  9. select语句

  10. union、minus、intersect等集合演算演算

  11. order by语句


####2) 条件查询

- 条件运算符( =, <, >, <=, >=, != , <>)
    ==<>:为mysql的不等于，推荐使用这个==

  ```sql
select buyer_name, order_amount from order_master where order_amount != 100.0;  
select `buyer_name`, `order_amount` from `order_master` where `order_amount` <> 88.1
  ```

- 逻辑运算符(or,and,not)

  ```sql
select `buyer_name`, `order_amount` from `order_master` where `order_amount` >= 88.1 and `order_amount` <= 120.0;
select `buyer_name`, `order_amount` from `order_master` where  not(`order_amount` >= 88.1 and `order_amount` <= 120.0)
  ```

- 模糊匹配(like, between..and, in)

  - like (%:代表零个或者多个)

    | 通配符    | 通配符                                                  |
    | --------- | ------------------------------------------------------- |
    | %         | 替代零个或多个字符                                      |
    | _(下划线) | 替代单个字符                                            |
    | [ ]       | 指定范围 ([a-f]) 或集合 ([abcdef]) 中的任何单个字符     |
    | [^]       | 不属于指定范围 ([a-f]) 或集合 ([abcdef]) 的任何单个字符 |

    ```sql
    select `buyer_openid` from  order_master where buyer_openid  like '_3%';
    ```

  - Between...and

    ==column between A and B 等同于 column >= A and column <= B，左右都有等号==

    ```sql
    select `buyer_name`, `order_amount` from `order_master` where `order_amount` between 88.1 and 120.0
    ```

  - In

    ```sql
    select category_id,category_name  from product_category where category_name in ('衣服777','水果')
    ```

  - is null 或者 is not null,或者安全等于<=>

    |          | is               | <=>                         |
    | -------- | ---------------- | --------------------------- |
    | 作用范围 | 只能用在null上面 | 既可以是null,也可以是非null |
    | 可读性   | 高               | 低                          |

    ```sql
    select userwx_name from user_info where userwxhead_image is null
    select userwx_name from user_info where userwxhead_image <=> null
    ```

#### 3)排序查询

	==asc:升序，desc:降序,默认是asc==
	
	==order一般是放在查询语句后面，limit子句除外==

- order by 后面支持当个字段，多个字段，表达式，函数，别名

  ```sql
  SELECT id FROM message_log ORDER BY id DESC
  SELECT id FROM message_log ORDER BY  LENGTH(id) DESC  #函数
  SELECT * FROM order_info ORDER BY id DESC, message_log_id ASC #多个字段
  SELECT id, LENGTH(id) as len  FROM order_info ORDER BY len desc # 别名
  ```

#### 4)函数

- 单行函数	

  - 字符函数

    - length函数: 获取字符串长度

      ```sql
      SELECT id FROM message_log ORDER BY  LENGTH(id) DESC
      ```

    - concat函数:链接字符串

      ````sql
      select concat(`userwx_name`,IFNULL(`userwxhead_image`,'....') ) from user_info
      ````

    - substr:截取字符串函数

      ```sql
      #从第三个开始到结尾，从1开始数
      SELECT id as 'id', SUBSTR(id,3) as subid FROM order_info;
      #从第二位开始后取4位
      SELECT id as 'id', SUBSTR(id,2, 4) as subid FROM order_info;
      #取后四位
      SELECT id as 'id', SUBSTR(id,LENGTH(id) - 4, 4) as subid FROM order_info;
      ```

    - instr:查询源串中是否包含目标串，包含返回目标串在源串出现的第一个位置，不包含返回0,与java中的indexof类型。

      ```sql
      #where后面不能使用别名
      SELECT id , INSTR(id,'26') ins FROM order_info where INSTR(id,'26') > 0;
      ```

    - trim:去除前后字符，默认是空格

      ```sql
      SELECT TRIM(' ssssss.  ');
      #指定用 'a' 去除
      SELECT trim('a' FROM 'aaaabbbaaabbaaaa');
      ```

    - upper,lower:大小写转换

      ```sql
      SELECT UPPER(product_name), LOWER(product_name) FROM product_info;
      ```

    - LPAD,RPAD:左右填充

      ```sql
      #左填充
      SELECT LPAD('ppp',10,'ab');
      #右填充
      SELECT RPAD('ppp',10,'ab');
      ```

    - Replace:替换

      ```sql
      #替换
      SELECT REPLACE('哈sss哈哈sss','sss','haha');
      ```

  - 数学函数

    - ROUND:四舍五入

      ```sql
      #四舍五入
      SELECT ROUND(3.55,1);
      ```

    - floor:向下取整

      ```sql
      #向下取整
      SELECT FLOOR(3.001);
      ```

    - CEIL:向上取整

      ```sql
      #向上取整
      SELECT CEIL(3.001);
      ```

    - TRUNCATE:数字截取

      ```sql
      SELECT TRUNCATE(3.45,1);
      ```

    - mod:取模

      ```sql
      #取模 ,按照这个公式来: a-(a/b)*b
      SELECT MOD(10,-3);
      ```

  - 日期函数

    - now:日期，包含年月日时分秒

      ```sql
      #日期，包含年月日时分秒
      SELECT  NOW();
      ```

    - CURDATE:年月日

      ```sql
      #日期，包含年月日
      SELECT CURDATE();
      ```

    - CURTIME:时分秒

      ```sql
      #日期，包含时分秒
      SELECT CURTIME();
      ```

    - year,MONTH,day,hour,MINUTE,SECOND:获取单独的年月日时分秒

      ```sql
      #获取年
      SELECT year(now());
      # day,hour,MINUTE,SECOND
      SELECT MONTH(now());
      SELECT MONTHNAME(NOW());
      ```

    - 日期格式化

      | 格式 | 代表的意思                                                   |
      | ---- | ------------------------------------------------------------ |
      | %Y   | 代表4位的年份                                                |
      | %y   | 代表2为的年份                                                |
      | %m   | 代表月, 格式为(01……12)                                       |
      | %c   | 代表月, 格式为(1……12)                                        |
      | %d   | 代表月份中的天数,格式为(00……31)                              |
      | %e   | 代表月份中的天数, 格式为(0……31)                              |
      | %H   | 代表小时,格式为(00……23)                                      |
      | %k   | 代表 小时,格式为(0……23)                                      |
      | %h   | 代表小时,格式为(01……12)                                      |
      | %I   | 代表小时,格式为(01……12)                                      |
      | %l   | 代表小时,格式为(1……12)                                       |
      | %i   | 代表分钟, 格式为(00……59) 【只有这一个代表分钟，大写的I 不代表分钟代表小时】 |
      | %r   | 代表 时间,格式为12 小时(hh:mm:ss [AP]M)                      |
      | %T   | 代表 时间,格式为24 小时(hh:mm:ss)                            |
      | %S   | 代表 秒,格式为(00……59)                                       |
      | %s   | 代表 秒,格式为(00……59)                                       |

      - STR_TO_DATE：字符转日期

        ```sql
        SELECT STR_TO_DATE('2018-10-9','%Y-%m-%d');
        ```

      - DATE_FORMAT：日期转字符

        ```sql
        SELECT DATE_FORMAT(now(),'%m-%d-%Y');
        ```

  - 其他函数

    - version:mysql的版本

      ```sql
      SELECT VERSION();
      ```

    - DATABASE:数据库名字

      ```sql
      #数据库名字
      SELECT DATABASE();
      ```

    - USER:登录的用户名

      ```sql
      #当前用户名
      SELECT USER();
      ```

  - 控制函数

    - IF: 相当于java中的三目运算符 ? :

      ```sql
      SELECT IF(10 > 5,'大于','小于');
      ```

    - case, when,then

      ```sql
      #category_id是一个确切的值
      SELECT category_id ,
      CASE category_type
      	WHEN 1 THEN '电子产品'
        when 2 THEN '家具'
      	ELSE
      		'未知'
      END
      FROM product_category
      
      #if eles if else if else 
      # category_type是一个范围
      SELECT CASE 
      	WHEN category_type = 1 THEN '电子产品'
      	WHEN category_type = 2 then '家具'
      	ELSE
      		'未知'
      END
      from product_category
      
      ```

- 分组函数

  [sql语句执行顺序](https://www.jb51.net/article/125741.htm)

  - 分类

    |       | 作用     | 适用范围                           | null处理 |
    | ----- | -------- | ---------------------------------- | -------- |
    | sum   | 求和     | 数字                               | 忽略     |
    | avg   | 取平均值 | 数字                               | 忽略     |
    | max   | 最大值   | 可以比较大小的(数字，字符串，日期) | 忽略     |
    | Min   | 最小值   | 可以比较大小的(数字，字符串，日期) | 忽略     |
    | count | 总行数   | 都可以                             | 忽略     |

    ```sql
    SELECT SUM(product_price),ROUND(AVG(product_price),2),
    	   MAX(product_price),MAX(product_price),COUNT(*)
    FROM product_info;
    ```

  - 与distincts结合使用,与上面五个函数都可以结合使用

    ```sql
    SELECT SUM(DISTINCT product_price) from product_info;
    ```

  - count函数说明

    - 执行效果

      |             | 说明                                                         |
      | ----------- | ------------------------------------------------------------ |
      | count(*)    | 包括了所有的列，相当于行数，在统计结果的时候，不会忽略列值为NULL |
      | count(1)    | 包括了忽略所有列，用1代表代码行,相当于在表中插入都是1的一列，在统计结果的时候，不会忽略列值为NULL |
      | count(列名) | 只包括列名那一列，在统计结果的时候，会忽略列值为空（这里的空不是只空字符串或者0，而是表示null）的计数，即某个字段值为NULL时，不统计。 |

    - 执行效率

      | 引擎   | 对比                                                         |
      | ------ | ------------------------------------------------------------ |
      | MYISAM | count(*)效率最高                                             |
      | INNODB | count(*)与count(1*)差不多，但是比count(列名)（列名是主键除外） |

      综上：推荐使用count(*)

#### 5)分组查询

- 语法

  ```sql
   select 分组函数, 列(必须在group by后面出现)
   from 表
  【where 筛选条件】
   group by 分组列表
  【having 分组后表的筛选条件】
  【order by 排序字段 【asc|desc】】
  【limit 页数,一个的个数】
  ```

  ==注意==

  - 查询的列必须在group by后面出现

- 简单使用

  ```sql
  #查询重名的名字
  SELECT COUNT(*), buyer_name
  FROM order_master
  GROUP BY buyer_name
  HAVING COUNT(*) > 1
  ```

- group by之前添加筛选条件(使用where)

  ```sql
  SELECT COUNT(*) num, buyer_name
  FROM order_master
  WHERE buyer_address in ('武汉','长沙','	成都')
  GROUP BY buyer_name
  ```

- group by之后添加筛选条件(使用having)

  ```sql
  #查询重名的名字
  SELECT COUNT(*), buyer_name
  FROM order_master
  GROUP BY buyer_name
  HAVING COUNT(*) > 1
  ```

  ==注意==

  - 分组函数作为条件的肯定是放在having后面的
  - 能用分组前筛选的，就优先考虑使用分组前筛选

- group by 后面可以使用函数

  ```sql
  SELECT COUNT(*) num, LENGTH(buyer_openid) len
  FROM order_master
  GROUP BY len
  HAVING num > 1
  ```

  ==注意==

  - group by和having后面支持别名

- group by后面可以使用多个字段分组，这些字段全部都相同为一组

  ```sql
  SELECT SUM(order_amount),COUNT(0),buyer_phone,buyer_name
  FROM order_master
  GROUP BY buyer_phone,buyer_name
  ```

  ==注意==

  - group by 中的分组函数(sum,count,max,min,avg)是处理分组之后的每一组

- 总结

  1.分组查询中的筛选条件分类

  |            | 数据源         | 位置             | 关键字 |
  | ---------- | -------------- | ---------------- | ------ |
  | 分组前筛选 | 原始表         | group by子句前面 | where  |
  | 分组后筛选 | 分组后的结果集 | group by子句后面 | having |

  - 分组函数作为条件的肯定是放在having后面的
  - 能用分组前筛选的，就优先考虑使用分组前筛选

  2. group by 子句支持单字段分组，多字段分组，表达式函数分组
  3. 也可以添加排序（排序一般放在整个分组查询条件之后）

  ```sql
  SELECT COUNT(*), MAX(order_amount),MIN(order_amount),MAX(order_amount) - MIN(order_amount) dif
  FROM order_master
  GROUP BY buyer_name
  HAVING dif > 0
  ```

#### 6)连接查询

- 分类

  - 按年代分类
    - sql92标准:仅仅支持内链接
    - sql99标准【推荐】: 支持内连接 + 外连接(左外+右外) + 交叉连接
  - 按功能分类
    - 内链接
      - 等值链接
      - 非等值链接
      - 自链接
    - 外连接
      - 左外连接
      - 右外连接
      - 全外连接
    - 交叉连接

- sql92标准

  - 等值链接

    ==等值链接是取多个表之间的交集,当表为两个表时，不关心两表的顺序==

    ```sql
    SELECT w.`name`, m.`name` FROM man m,woman w
    WHERE w.man_id = m.id;
    ```

    ```sql
    SELECT COUNT(*) num, m.`name` 
    FROM man m,woman w
    WHERE w.man_id = m.id
    GROUP BY m.`name`
    HAVING num > 1
    ```

  - 非等值连接

    ```sql
    SELECT w.age, ar.`name` 
    FROM woman w , age_rank ar
    WHERE w.age BETWEEN ar.`start` and ar.`end`
    ORDER BY w.age desc
    ```

  - 自连接（自己跟自己等值连接）

    ```sql
    SELECT w1.`name`, w2.`name`
    from woman w1,woman w2
    where w1.id = w2.parent_id
    ```

- sql99标准

  - 语法

    ```sql
    select 查询列表
    from 表1 别名【连接类型】
    join 表2 别名
    on 连接条件
    【where 筛选条件】
    【group by 分组】
    【having 筛选条件】
    【order by 筛选条件】
    ```

  - 连接类型使用的关键字

    | 名       | 关键字                     |
    | -------- | :------------------------- |
    | 内连接   | inner                      |
    | 左外连接 | left【outer】              |
    | 右外连接 | right【outer】             |
    | 全外连接 | full【outer】(mysql不支持) |
    | 交叉连接 | cross                      |

  - 内链接(inner)

    - 等值连接

      ```sql
      SELECT w.id, w.`name`, m.`name` 
      FROM man m inner JOIN woman w
      on w.man_id = m.id
      WHERE w.id LIKE '%a%'
      ```

      1.可以添加排序，分组，筛选

      2.inner可以省略

      3.筛选条件放在where之后，连接条件放在on之后，分离条件，便于阅读

    - 非等值连接

      ```sql
      SELECT w.`name`,w.age, ar.`name` FROM woman w inner join age_rank ar
      on w.age BETWEEN ar.`start` and ar.`end`
      where age > 30
      ORDER BY w.age desc
      ```

    - 自连接

      ```sql
      SELECT w1.`name`, w2.`name`
      from woman w1 inner join woman w2
      on w1.id = w2.parent_id
      ```

  - 外连接

    应用场景:==用于查询一个表中有，另一个表中没有的记录

    特点:

    1. 外连接的查询结果为主表中的所有记录

    	如果从表中有和它匹配，则显示匹配的值

    	如果从表中没有和它匹配的，则显示null

    	==外连接查询结果== = ==内链接结果 + 主表中有的而从表中没有的记录==

    2. 左外连接，left join 左边的是主表，右边的是从表

       右外连接，right join 右边的是主表，左边的是从表

    3. 左外与右外交换两表的顺序，可以实现同样的效果

    4. 全外连接的结果 = 内连接 + 主表有但从表没有的+ 从表有但主表没有的



    - 左外连接
    
      ```sql
      SELECT * FROM woman w left JOIN man m 
      on w.man_id = m.id
      WHERE m.id is null
      ```
    
    - 右外连接(交换表的顺序即可)
    
      ```sql
      SELECT * FROM man m right JOIN  woman w 
      on w.man_id = m.id
      WHERE m.id is null
      ```
    
    - 全外连接(mysql不支持)
    
      ```sql
      #全外连接的结果 = 内连接 + 主表有但从表没有的+ 从表有但主表没有的
      SELECT * FROM mam m FULL JOIN woman w
      on m.id = w.man_id
      ```

  - 交叉连接

    交叉联接返回左表中的所有行，左表中的每一行与右表中的所有行组合。==交叉联接也称作笛卡尔积==。

    ```sql
    SELECT * from man m cross join woman w
    ```

- 总结

  - Sql92 与sql99比较

    |       | 功能 | 可读性 |
    | :---: | ---- | ------ |
    | sql92 | 少   | 低     |
    | sql99 | 多   | 高     |

    ==推荐使用sql99==

  - 使用图形表示连接类型

    - 内链接(inner)

      ![](/Users/tgy/Documents/Java/mysql/image/mysql-base/inner_outer.png)

    - 左外连接

      ![](/Users/tgy/Documents/Java/mysql/image/mysql-base/left_outer.png)

    - 最外连接加条件

      ![](/Users/tgy/Documents/Java/mysql/image/mysql-base/left_outer_add_codition.png)

    - 右外连接

      ![](/Users/tgy/Documents/Java/mysql/image/mysql-base/right_outer.png)

    - 右外加条件

      ![](/Users/tgy/Documents/Java/mysql/image/mysql-base/right_outer_add_codition.png)

    - 全外连接

      ![](/Users/tgy/Documents/Java/mysql/image/mysql-base/full_outer.png)

    - 全外加条件

      ![](/Users/tgy/Documents/Java/mysql/image/mysql-base/full_outer_add_codition.png)

#### 7)子查询

- 含义

  出现在其他语句中的select语句，称为子查询或者内查询，外部的查询语句称为住查询或者外查询

- 分类

  - 按子查询出现的位置：
    - select后面
      - 仅支持标量子查询
    - from后面
      - 支持表子查询
    - where或者having后面
      - 标量子查询（单行）
      - 支持列子查询 （多行）
      - 行子查询
    - exists后面(相关子查询)
      - 表子查询
  - 按功能
    - 标量子查询(结果集只有一行一列)
    - 列子查询(结果集只有一列多行)
    - 行子查询（结果集有一行多列）
    - 表子查询(结果集多行多列)

- where或者having后面

  - 特点

    - 子查询都放在小括号内

    - 子查询一般放在条件的右侧

    - 标量子查询一般搭配单行操作符使用（<,>,=,<>,...）

      列子查询一般搭配着多行操作符使用（in, any/some,all）

  - 标量子查询（单行）

    ```sql
    #谁的年龄比范冰冰大
    SELECT * FROM woman where age > (
    	SELECT age FROM woman where `name` = '范冰冰'
    )
    ```

    ```sql
    #大于平均年龄的人信息
    SELECT * FROM woman where age > (
    	SELECT avg(age) FROM woman
    )
    ```

    ```sql
    #查询男生女朋友最小年龄大于男生id为22332wwqw的女朋友的最小年龄
    SELECT MIN(age) m ,man_id
    FROM woman 
    WHERE man_id IS not null
    GROUP BY man_id
    HAVING m > (
    	SELECT MIN(age) 
    	FROM woman
    	where man_id = '22332wwqw'
    )
    ```

  - 列子查询 （多行）

    - 多行操作符

      | 操作符    | 含义                           |
      | --------- | ------------------------------ |
      | in/not in | 等于列表中的==任意一个==       |
      | any/some  | 与子查询返回的==某一个==值比较 |
      | all       | 与子查询返回的==所有==值比较   |

      ```sql
      #返回张无忌和汪峰的女朋友
      SELECT * FROM woman 
      WHERE man_id in (
      
      	SELECT DISTINCT id FROM man 
      	where `name` = '张无忌' or `name` = '汪峰'
      )
      
      # in 可以使用any代替
      SELECT * FROM woman 
      WHERE man_id = any (
      
      	SELECT DISTINCT id FROM man 
      	where `name` = '张无忌' or `name` = '汪峰'
      )
      
      # not in 可以使用 <>all替换
      
      ```

      ```sql
      #使用any
      SELECT * FROM woman
      WHERE age < any(
      	SELECT age 
      	FROM woman 
      	WHERE man_id = 'ssas9012312'
      ) and man_id != 'ssas9012312'
      #使用max
      SELECT * FROM woman
      WHERE age < (
      	SELECT MAX(age) 
      	FROM woman 
      	WHERE man_id = 'ssas9012312'
      ) and man_id != 'ssas9012312'
      ```

  - 行子查询(结果集是一行多列或者多行多列)

    ```sql
    #行子查询
    SELECT * from woman
    WHERE (id,age) = (
    	SELECT MIN(id),MIN(age) FROM woman
    )
    
    #分开写的
    SELECT * FROM woman
    WHERE id  = (
    
    	SELECT  MIN(id) 
    	FROM woman
    	
    ) and age = (
    	
    	SELECT MIN(age) FROM woman
    )
    
    SELECT * FROM woman
    WHERE LENGTH(id)  = (
    
    	SELECT LENGTH(id) len
    	FROM woman
    	GROUP BY LENGTH(id)
    	ORDER BY len ASC
    	LIMIT 0,1
    ) and age = (
    	
    	SELECT MIN(age) FROM woman
    )
    
    ```

- select后面
  注意:==仅支持标量子查询(一行一列)==

  ```sql
  #查询男生的女朋友个数
  SELECT m.*, (
  	SELECT COUNT(*)
  	FROM woman w
  	WHERE w.man_id = m.id
  ) 个数 FROM man m
  ```

- from后面
  注意:==把子查询充当一张表，该表必须取别名==

  ```sql
  #平均年龄的等级表
  SELECT agt.ag, ar.`name` 
  FROM (SELECT AVG(age) ag
  			FROM woman
  			GROUP BY man_id
  ) agt INNER JOIN age_rank ar
  on agt.ag >= ar.`start` and agt.ag < ar.`end`
  ```

- exists后面(相关子查询)

  ==exists与其他子查询的区别:exists是先查询外查询，然后根据exists条件筛选外查询集合==

  ```sql
  #有男朋友的女生
  SELECT * FROM woman w
  WHERE EXISTS (
  	SELECT * from man m
  	where m.id = w.man_id
  )
  ```

#### 8)分页查询

- 语法

  ```sql
   select 查询列表						(7)
   from 表  				  			  (1)
  【 join type join 表2 	 			 (2)
     on 连接条件 				      		(3)
     where 筛选条件						(4)
     group by 分组字段					(5)
     having 分组后的筛选	   			   (6)
     order by 排序关键字				    (8)
     limit 位置(offset),大小(size)		(9)
   】
  ```

- 特点

  - limit总是放在所有语句==最后==
  - 公式
    - offset = (page - 1) * size
    - limit offset, size

#### 9)联合查询

- 应用场景

  要查询的结果来自多个表，且多个表没有直接的联系，但查询的信息一致

- 语法

  ```sql
  查询语句1
  union
  查询语句2
  union
  ....
  ```

- 特点

  1. 要求多条查询语句的查询列数是一致的
  2. 要求多条查询语句的查询的每一列的类型和顺序最好一致
  3. union关键字默认是去重的，如果使用union all 可以包含重复项

- 实例

  ```sql
  SELECT `name`, age
  from woman where age > 30
  UNION 
  SELECT `name`, age 
  from man where age > 30
  ```
