## mysql

### 一.**数据查询语言DQL**

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

      ![](./image/mysql-base/inner_outer.png)

    - 左外连接

      ![](./image/mysql-base/left_outer.png)

    - 最外连接加条件

      ![](./image/mysql-base/left_outer_add_codition.png)

    - 右外连接

      ![](./image/mysql-base/right_outer.png)

    - 右外加条件

      ![](./image/mysql-base/right_outer_add_codition.png)

    - 全外连接

      ![](./image/mysql-base/full_outer.png)

      mysql没有全外，使用union连接左外与右外加条件

      ```sql
      select * from t1 left join t2 on t1.id = t2.id
      union
      select * from t1 right join t2 on t1.id = t2.id where t1.id = null
      ```

    - 全外加条件

      ![](./image/mysql-base/full_outer_add_codition.png)

      使用union连接左外加条件与右外加条件

      ```sql
      select * from t1 left join t2 on t1.id = t2.id where t2.id = null
      union
      select * from t1 right join t2 on t1.id = t2.id where t1.id = null
      ```


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

### 二.数据操纵语言DML

### 1.insert

#### 1)分类

1. 第一种插入格式

   - 语法

     ```sql
     insert into 表(列名...) values(值...)
     ```

   - 注意事项

     - 插入值的类型要与列的类型一致或者兼容

       ```sql
       INSERT INTO woman(id,`name`,age,man_id,parent_id) 
       VALUES('62342342ddas2','刘涛',54,null,null);
       ```

     - 如果列允许为null，则有两种插入方式

       - 是对应的列，但是values里面使用null

         ```sql
         INSERT INTO woman(id,`name`,age,man_id,parent_id) VALUES('62342342ddas2','刘涛',54,null,null);
         ```

       - 在表后面不写对应的列名，默认就是使用null填充

         ```sql
         INSERT INTO woman(id,`name`,age) 
         VALUES('8e323ds','张曼玉',54);
         ```

     - 列的顺序可以调换，但values里面也要跟着调换

     - 列数和值数必须一致

     - 可以省略列名，默认所有列，列的顺序就是表字段的顺序

       ```sql
       INSERT INTO woman 
       VALUES('32222ddqwd','刘亦菲',35,null,null);
       ```

2. 第二种插入方式

   1. 语法

      ```sql
      insert into 表
      set 列名=值,列名=值....
      ```

      ```sql
      INSERT into woman set
      id = '212sd2kld', `name` = '赵薇', age = 41
      ```

#### 2)两种方式比较

1. 第一种可以批量插入，第二种不可以

   ```sql
   INSERT into man(id,`name`,age) 
   VALUES('xadsxsad3292','潘粤明',42),
   	  ('ghsdxd34242ddas','郑恺',32)
   ```

2. 第一种支持子查询，第二种不支持

   ```sql
   INSERT into man(id,`name`,age) 
   SELECT 'xosxasds',`name`,age
   FROM man 
   where id='xadsxsad3292'
   ```

### 2.update

#### 1)修复单表记录

- 语法

  ```sql
  update 表名
  set 列=新值,列=新值,...
  where 筛选条件
  ```

- 练习

  ```sql
  UPDATE man 
  SET age = 21
  WHERE `name` = '潘粤明'
  ```


#### 2)修改多表记录

- 语法

  ```sql
  #92语法
  update 表1 别名,表2 别名
  set 列=值, ...
  where 连接条件 and 筛选条件
  
  #99语法(推荐)
  update 表1 别名 
  inner|left|right join 表2 别名
  on 连接条件
  set  列=值, ...
  where 筛选条件
  ```

  ```sql
  #没有女朋友的男的年龄小于30都改成20
  UPDATE man m
  LEFT JOIN woman w
  on m.id = w.man_id
  set m.age = 20
  where w.man_id is null and m.age < 30
  ```

### 3.删除表数据

#### 1)使用delete删除

#####  1.删除单表

- 语法

  ```sql
  delete from 表名 
  where 筛选条件
  ```

  ```sql
  delete FROM man 
  where age < 30
  ```

##### 2.删除多表数据

- 语法

  ```sql
  delete 表1的别名,表2的别名 
  from 表1 别名 inner|left|right join 表2 别名
  on 连接条件
  where 筛选条件
  ```

  ```sql
  #删除张无忌以及张无忌女朋友信息
  DELETE m,w FROM
  man m INNER JOIN woman w 
  on m.id = w.man_id
  where m.`name` = '张无忌'
  
  #删除张无忌女朋友信息
  DELETE w FROM
  man m INNER JOIN woman w 
  on m.id = w.man_id
  where m.`name` = '张无忌'
  ```

#### 2)使用truncate删除

1. 语法

   ```sql
   truncate table 表
   ```

   ```sql
   TRUNCATE TABLE man;
   ```


#### 3)两种删除比较

|                        | delete         | truncate |
| ---------------------- | -------------- | -------- |
| 添加where条件          | 能             | 不能     |
| 效率                   | 低             | 高       |
| 自增长列删除之后再插入 | 从断点开始     | 从1开始  |
| 返回值                 | 返回删除的列数 | 无       |
| 事务回滚               | 能回滚         | 不能回滚 |

### 三.数据定义语言DDL

对库和表的管理，涉及到增删改

创建(create),修改(alter) ,删除(drop)

#### 1.库的管理

##### 1)库的创建

1. 语法

   ```sql
   create database IF NOT Exists 库名
   ```

   ==创建表的通用做法==

   ```sql
   drop database if exists 新库
   create database 新库
   ```


##### 2)库不能修复

1. 修改字符集

   ```sql
   alter database 库名 character set utf8
   ```

##### 3)库的删除

1. 语法

   ```sql
   drop database IF Exists 库名
   ```

#### 2.表的管理

##### 1)表的创建

1. 语法

   ```sql
   create table IF NOT EXISTS 表名(
   	列名 类型【长度，约束】,
   	列名 类型【长度，约束】,
   	列名 类型【长度，约束】,
   	...
   )
   ```

   ```sql
   CREATE table `book`(
   	`id` VARCHAR(32) not null,
   	`book_name` VARCHAR(32) not null,
   	`price` DECIMAL(8,2),
   	`author_id` int,
   	`publishDate` datetime
   )
   ```

   ```sql
   #查询表的定义
   desc 表名
   ```

   ==创建表的通用做法==

   ```sql
   drop table if exists 新表名
   create table 新表名
   ```

##### 2)表的修改

 总语法: 

```sql
ALTER TABLE book change|modify|add|rename to|drop column 列名 【列类型 约束】
```

1. 修改列名

   - 语法:

     ```sql
     alter table 表名 change column 旧列名 新列名 类型
     ```

     ```sql
     ALTER TABLE book CHANGE COLUMN publishdate pdate  datetime
     ```

2. 修改列的类型或者约束

   - 语法

     ```sql
     alter table 表名 modify column 列名 新类型|新约束
     ```

     ```sql
     alter table book modify column pdate TIMESTAMP;
     ```

3. 添加新列

   - 语法

     ```sql
     alter table 表名 add column 新列名 类型【约束】
     ```

     ```sql
     alter table book add column salay DOUBLE;
     ```

4. 删除列

   - 语法

     ```sql
     alter table 表名 drop column 列名
     ```

     ```sql
     alter table book drop column salay
     ```

5. 修改表名

   - 语法

     ```sql
     alter table 表名 rename to 新表名
     ```

     ```sql
     ALTER TABLE book rename to books;
     ```


##### 3)表的删除

1. 语法

   ```sql
   drop table IF Exists 表名
   ```

   ```sql
   DROP table if EXISTS books;
   ```

##### 4)表的复制

1. 复制表的结构

   - 语法

     ```sql
     CREATE table 新表名 like 以存在的表;
     ```

     ```sql
     CREATE table mybook like books;
     ```

2. 复制表的结构和数据(==根据where筛选条件，复制部分数据==)

   - 语法

     ```sql
     create table 表名
     select * from 以存在的表
     where 筛选条件
     ```

     ```sql
     CREATE table mybook
     SELECT * from books
     where price > 20;
     ```

3. 只复制表中的某些字段(不要数据)

   ```sql
   #复制books表中的ID，price字段到mybook01中，同时不要数据
   CREATE table mybook01
   SELECT id,price from books
   where 0; #使用where 0 是筛选条件为false 或者使用 where 1=2
   ```

### 四.数据类型

#### 1.数值类型

##### 1）整型

1. 分类

   |        | tinyint | sampling | mediumint | Int, integer | Biting |
   | ------ | ------- | -------- | --------- | ------------ | ------ |
   | 字节数 | 1       | 2        | 3         | 4            | 8      |

2. 特点

   - 如果不设置无符号还是有符合默认是有符号，如果想设置无符号，需要添加unsigned关键字

     ```sql
     CREATE table t1(
     	id int UNSIGNED
     )
     ```

   - 如果插入的值超过了整数范围，回报out of range异常，并且插入==临界值==

   - 如果不设置长的，会使用默认长度

     ==长度代表的是显示的最大宽度，如果不够会用0在左边填充，但必须搭配zerofill使用==

     ```sql
     CREATE table t1(
     	id int(4) ZEROFILL
     )
     ```

##### 2）小数

1. 分类

   |        | float | double | dec(m,d)\| decimal(m,d) |
   | ------ | ----- | ------ | ----------------------- |
   | 字节数 | 4     | 8      | m+2                     |

   - 浮点类型
     - float(M,D)
     - double(M,D)
   - 定点类型
     - dec（M,D）
     - decimal(M,D)

2. 特点

   - M : 整数位数 + 小数位数，

     D: 小数位数

     如果超出范围，则插入临界值

   - M和D都可以省略

     - 如果是decimal类型，则M默认为10，D默认为0
     - 如果是float或者double类型，则会随着插入值的精度来决定精度

   - 定点型的精确度较高，如果要求插入的数值的精度较高如货币运行，则可以考虑使用

#### 2)字符类型

1. 分类

   | 字符串类型 | 最多字符数 | 描述及存储            | 特点           | 效率 |
   | ---------- | ---------- | --------------------- | -------------- | ---- |
   | char(m)    | M          | m为0～255之间的整数   | 固定长度的字符 | 高   |
   | varchar(m) | M          | m为0～65535之间的整数 | 可变长度的字符 | 低   |

   注意: char 与varchar的固定长度与可变长度的意思

   ```sql
   CREATE table Person(
   	id char(4),
   	p_name varchar(8)
   )
   #如果插入
   insert into Person set id='试试',p_name='张三'
   #则id字段分配4个字符,p_name分配2个字符，如果超出了定义时的长度，都会报'Data too long'的错误
   
   ```

#### 3.日期类型

1. 分类

   |           | 字节 | 范围      | 时区影响 |
   | --------- | ---- | --------- | -------- |
   | datetime  | 8    | 1000-9999 | 不受     |
   | timestamp | 4    | 1970-2038 | 受       |

   推荐使用==timestamp==

### 五.约束

#### 1)六大约束

| 约束        | 含义                                                         | 可使用字段         |
| ----------- | ------------------------------------------------------------ | ------------------ |
| not null    | 非空，用于保证该字段的值不为空                               | 姓名，学号字段等   |
| default     | 默认值，用于保证该字段有默认值                               | 性别等             |
| Primary key | 主键，保证该字段具有唯一性，且非空                           | 学号，编号等       |
| unique      | 唯一，用于保证字段的具体值唯一，可以为空                     | 座位等             |
| check       | 检查约束【mysql不支持】                                      | 年龄，性别         |
| foreign key | 外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列值，在从表添加外键约束，用于引用主表中某列的值 | 从表引用主表的主键 |

1. ==主键(primary key),唯一(unique)对比==

   |      | 保证唯一 | 是否允许为空 | 表中允许的个数 | 是否允许组合   |
   | ---- | -------- | ------------ | -------------- | -------------- |
   | 主键 | 是       | 否           | 一个           | 允许（不推荐） |
   | 唯一 | 是       | 是           | 多个           | 允许（不推荐） |

   ```sql
   INSERT into stuinfo(id,stu_name,sex,age,seat,major_id) VALUES(1,'张三','男',21,null,1);
   INSERT into stuinfo(id,stu_name,sex,age,seat,major_id) VALUES(2,'李四','女',20,null,1);
   ```

2. ==外键总结==

   - 要求在从表设置外键关系

   - 从表的外键列类型与主表的关联列类型一致，名称无要求

   - 主表的关联列必须是一个key(一般是主键或者唯一键)

   - 插入数据时，先插入主表，再插入从表

     删除数据时，先删从表，再删主表

#### 2)添加约束的时机

1. 创建表时
2. 修改表时

#### 3)约束的添加分类

1. 列级约束
   - 六大约束都支持，但外键没有效果
2. 表级约束
   - 除了非空，默认，其他都支持

#### 4)创建表时添加约束

##### 1.添加列级约束

```sql
CREATE table `stuinfo`(
	`id` int PRIMARY KEY,#主键
	 `stu_name` VARCHAR(32) Not null, #非空
	 `gender` CHAR(1) CHECK(gender='男' or gender='女'), #检查约束
	 `seat` int UNIQUE, #唯一约束
	 `age` int DEFAULT 21, #默认值
  	 major_id int REFERENCES major(id)#外键约束
);

CREATE table `major`(
	`id` int PRIMARY KEY,
	`major_name` VARCHAR(32) not null
)
```

1. 查看某个表中的索引

   ```sql
   show INDEX FROM stuinfo 
   ```

2. 语法

   直接在字段名和类型后面添加约束类型即可

3. 支持的类型

   主键(primary key)，默认(default),非空(not null), 唯一约束(unique)

##### 2.添加表级约束

```sql
CREATE table `stuinfo`(
		`id` int ,
		`stu_name` VARCHAR(32) Not null, #非空
		`gender` CHAR(1) ,
		`seat` int , 
		`age` int DEFAULT 21, #默认值
		`major_id` int,
		
		CONSTRAINT `pk` PRIMARY key(`id`), #主键
		CONSTRAINT `seat_uq` UNIQUE(`seat`), #唯一键
		CONSTRAINT `fk_stuinfo_major` FOREIGN KEY(`major_id`) REFERENCES major(`id`) #外键
);
```

1. 语法

   【constraint 名】约束类型(字段名) 

2. 通用写法

   ```sql
   CREATE TABLE `stuinfo`(
   	`id` int PRIMARY KEY,
   	`stu_name` VARCHAR(32) NOT null,
   	`sex` char(1),
   	`age` int DEFAULT 18,
   	`major_id` int,
       `seat` int unique,
   	CONSTRAINT fk_stuinfo_marg FOREIGN KEY(`major_id`) REFERENCES major(`id`)
   )
   ```

#### 5)修改表时添加约束

1. 添加非空约束

   ```sql
   alter table `stuinfo` modify column `stu_name` VARCHAR(32) NOT null
   ```

2. 添加默认约束

   ```sql
   alter table `stuinfo` modify column `age` int default 19
   ```

3. 添加主键

   - 列级约束

     ```sql
     alter table `stuinfo` modify column `id` primary key
     ```

   - 行级约束

     ```sql
     alter table `stuinfo` add  primary key(`id`)
     ```

4. 添加唯一键

   - 列级约束

     ```sql
     alter table `stuinfo` modify column `seat` int unique
     ```

   - 表级约束

     ```sql
     alter table `stuinfo` add  unique(seat)
     ```

5. 添加外键

   ```sql
   alter table `stuinfo` add constraint `fk_stuinfo_major` FOREIGN KEY(`major_id`) REFERENCES major(`id`)
   ```


#### 6)修改表时删除约束

1. 删除非空

   ```sql
   alter table `stuinfo` modify column `stu_name` VARCHAR(32)  null
   ```

2. 删除默认值

   ```sql
   alter table `stuinfo` modify column `age` int
   ```

3. 删除主键

   ```sql
   alter table `stuinfo` drop primary key 
   ```

4. 删除唯一键

   ```sql
   alter table `stuinfo` drop index `seat`
   ```

5. 删除外键

   ```sql
   alter table `stuinfo` drop foreigen key `fk_stuinfo_major`
   ```

#### 7)标识列（又称自增长列）

##### 1.创建表时设置标识列

```sql
CREATE table `major`(
	`id` int PRIMARY KEY auto_increment,
	`major_name` VARCHAR(32) not null
)
```

##### 2.修改表时设置标识列

```sql
alter table `major` modify column `id` int PRIMARY KEY auto_increment
```

##### 3.修改表时删除标识列

```sql
alter table `major` modify column `id` int PRIMARY KEY 
```

##### 4.总结

1. 标识列只能与是一个key(unique或者primary key)类型搭配使用

2. 一个表至多有一个自增长列

3. 自增长列的类型只能是数字类型(int,float,double)

4. 自增长列可以通过一下方式设置度长

   ```sql
   set auto_increment_increment = 2
   ```

   设置起始值可以在第一次插入的时候设置一个值，后面不用设置，就会自增长

   ```sql
   #从6开始，后面不用插入，自动增长
   INSERT INTO major VALUES(6,'呵呵');
   INSERT INTO major VALUES(null,'呵呵1');
   INSERT INTO major VALUES(null,'呵呵2');
   ```


### 六.事务

#### 1)事件介绍

1. 事务的概念

   一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行，如转账

   ```sql
   #查看mysql支持的存储引擎
   show engines;
   ```

2. 事务支持的ACID属性

   - 原子性（Atomicity）

     事务的原子性是指事务必须是一个原子的操作序列单元。事务中包含的各项操作在一次执行过程中，只允许出现两种状态之一。
     全部执行成功
     全部执行失败
     任何一项操作都会导致整个事务的失败，同时其它已经被执行的操作都将被撤销并回滚，只有所有的操作全部成功，整个事务才算是成功完成。

   - 一致性（Consistency）

     事务的一致性是指事务的执行不能破坏数据库数据的完整性和一致性，一个事务在执行之前和执行之后，数据库都必须处以一致性状态。

     比如：如果从A账户转账到B账户，不可能因为A账户扣了钱，而B账户没有加钱。

   - 隔离性（Isolation）

     事务的隔离性是指在并发环境中，并发的事务是互相隔离的，一个事务的执行不能被其它事务干扰。也就是说，不同的事务并发操作相同的数据时，每个事务都有各自完整的数据空间。

     一个事务内部的操作及使用的数据对其它并发事务是隔离的，并发执行的各个事务是不能互相干扰的。

   - 持久性（Duration）

     事务的持久性是指事务一旦提交后，数据库中的数据必须被永久的保存下来。即使服务器系统崩溃或服务器宕机等故障。只要数据库重新启动，那么一定能够将其恢复到事务成功结束后的状态。

3. 数据库并发访问会出现的问题

   - 更新丢失

     当有两个并发执行的事务，更新同一行数据，那么有可能一个事务会把另一个事务的更新覆盖掉。 
     当数据库没有加任何锁操作的情况下会发生。

   - 脏读

     一个事务读到另一个尚未提交的事务中的数据。 该数据可能会被回滚从而失效。 如果第一个事务拿着失效的数据去处理那就发生错误了。

   - 不可重复读

     不可重复度的含义：一个事务对同一行数据读了两次，却得到了不同的结果。它具体分为如下两种情况： 

     1. 虚读

        在事务1两次读取同一记录的过程中，事务2对该记录进行了==修改==，从而事务1第二次读到了不一样的记录。 

     2. 幻读

        事务1在两次查询的过程中，事务2对该表进行了==插入、删除==操作，从而事务1第二次查询的结果发生了变化。

     3. 与『脏读』的区别

        脏读读到的是==尚未提交==的数据，而不可重复读读到的是==已经提交==的数据，只不过在两次读的过程中数据被另一个事务改过了。

4. 事务的隔离级别

   - Read uncommitted 读未提交

     在该级别下，一个事务对一行数据修改的过程中，不允许另一个事务对该行数据进行修改，但允许另一个事务对该行数据读。 
     因此本级别下，不会出现更新丢失，但会出现脏读、不可重复读。

   - Read committed 读提交

     在该级别下，未提交的写事务不允许其他事务访问该行，因此不会出现脏读；但是读取数据的事务允许其他事务的访问该行数据，因此会出现不可重复读的情况。

   - Repeatable read 重复读

     在该级别下，读事务禁止写事务，但允许读事务，因此不会出现同一事务两次读到不同的数据的情况（不可重复读），且写事务禁止其他一切事务。

   - Serializable 序列化

     该级别要求所有事务都必须串行执行，因此能避免一切因并发引起的问题，但效率很低。



   这四个级别可以逐个解决脏读 、不可重复读 、幻读 这几类问题

   ![](./image/mysql-transaction/01.png)

   mysql默认级别是：==Repeatable read 重复读==

#### 2)事务的创建

1. 隐式事务

   事务没有明显的开启和结束的标记，如:insert,update,delete语句

   ```sql
   #默认是on，自动开启的
   show VARIABLES LIKE 'autocommit';
   ```

2. 显式事务

   事务具有明显的开启和结束标记

   ==前提是必须设置自动提交功能为禁用==

   ```sql
   #关闭自动提交,只对当前会话有效
   set autocommit=0
   ```

   - 开启事务的步骤

     1. 开启事务

        ```sql
        set autocommit = 0
        start transaction; #可选的
        ```

     2. 编写事务中的sql语句(select,insert,update,delete)

     3. 结束事务

        ```sql
        #提交事务
        commit;
        #或者出现问题，回滚事务
        rollback;
        ```

     4. 实例

        ```sql
        set autocommit=0;
        START TRANSACTION;
        INSERT INTO major VALUES(null,'语文');
        update books set book_name='三体' where id='dsds';
        -- ROLLBACK;
        COMMIT;
        ```


#### 3)事务的保存点(savepoint)

回滚到指定位置

```sql
set autocommit = 0;
START TRANSACTION;
DELETE FROM major where id = 7;
SAVEPOINT p1; #设置保存点
DELETE FROM major where id = 10;
ROLLBACK TO p1;#回滚到保存点
```

### 七.视图(view)

#### 1)概念

	视图是从mysql5.0.1版本开始提供的视图功能。一种虚拟存在的表，行和列的数据都来自定义视图的查询中使用的表，并且是在使用视图时==动态生成的==，只保存了==sql逻辑==，不保存查询结果

#### 2)应用场景

1. 多个地方用到同样的查询结果
2. 该查询结果使用的sql语句比较复杂

#### 3)视图创建

1. 语法

   ```sql
   create view 视图名
   as
   查询语句;
   ```

   ```sql
   #创建视图
   CREATE VIEW woman_man_view
   as
   SELECT woman.`name` w_name,man.`name` m_name
   FROM woman INNER JOIN man
   on woman.man_id = man.id;
   #使用视图
   SELECT w_name, m_name
   FROM woman_man_view
   where w_name = '章子怡'
   ```

   ```sql
   #创建
   CREATE view woma_man_avg
   as
   SELECT woman.`name` w_name,AVG(man.age) ag
   FROM woman INNER JOIN man
   on woman.man_id = man.id
   GROUP BY woman.`name`
   #使用
   SELECT ar.`name`,wma.ag
   FROM age_rank ar INNER JOIN woma_man_avg wma
   ON wma.ag BETWEEN ar.`start` and ar.`end`;
   ```

2. 总结

   - 重用sql语句
   - 简化复杂的sql操作，不必知道其中的细节
   - 保护数据，提高安全性

#### 4)视图的修改

##### 1.语法

1. 第一种

   ```sql
   create or replace view 视图名
   as
   表名
   ```

   ```sql
   CREATE OR REPLACE view woma_man_avg
   as
   SELECT woman.`name` w_name,AVG(man.age) ag
   FROM woman INNER JOIN man
   on woman.man_id = man.id
   GROUP BY woman.`name`;
   ```

2. 第二种

   ```sql
   alter view 视图名
   as
   表名
   ```

   ```sql
   alter view woma_man_avg
   as
   SELECT woman.`name` w_name,AVG(man.age) `avg`
   FROM woman INNER JOIN man
   on woman.man_id = man.id
   GROUP BY woman.`name`;
   ```


#### 5)删除视图

##### 1.语法

```sql
drop view 视图名1,视图名2,视图名3...
```

```sql
drop view woman_man_view,woma_man_avg;
```

#### 6)查看视图结构

```sql
show create view 视图名
```

```sql
show CREATE view woma_man_avg;
```

#### 7)视图的更新(不允许更新数据)

1.不能更新的视图

  视图创建的sql语句中包含一下关键字不能更新

- 分组函数 ，distinct, group by,having,union或者union all

- 常量视图

  ```
  select 'abc';
  ```

- 子查询

- join

- from一个不能更新的视图

- where 子句的子查询引用了from子句中的表

2.能更新的视图

简单的没有上面限制的视图(一般也不会用视图)

3.总结

所以一般视图==不允许更新==

#### 8)视图与表的对比

|      | 创建语法关键字 | 是否实际占用物理空间 | 使用                   |
| ---- | -------------- | -------------------- | ---------------------- |
| 视图 | create view    | 只是保存了sql逻辑    | 查，数据一般不能增删改 |
| 表   | create table   | 保存了数据和逻辑     | 增删改查               |

### 八.变量

#### 1)分类

1. 系统变量
   - 全局变量
   - 会话变量
2. 自定义变量
   - 用户变量
   - 局部变量

#### 2)系统变量

1. 说明

   变量是由系统提供，不是用户定义的，属于服务器层面的

2. 使用语法

   - 查看所有的系统变量

     ```sql
     show global|session variables;
     ```

   - 查看满足条件的部分系统变量

     ```sql
     show global|【session】 variables like '%char%';
     ```

   - 查询指定的某个系统变量的值

     ```sql
     select @@global|session.系统名;
     ```

     ```sql
     #会话变量查询
     SELECT  @@session.auto_increment_increment; 或者
     SELECT  @@auto_increment_increment; 
     #全局变量查询
     SELECT  @@global.character_sets_dir;
     ```

   - 为某个变量赋值

     - 方式一

       ```sql
       set global|【session】 系统变量名=值
       ```

       ```sql
       set global  auto_increment_increment = 2;
       ```

     - 方式二

       ```sql
       set @global|session.系统变量名=值
       ```

       ```sql
       SET @@session.auto_increment_increment = 3;
       #或者如下方式写
       SET @@auto_increment_increment = 3;
       ```

   - ==注意事项==

     如果是全局级别，则需要加global，如果是会话级别，则需要加上session

     如果不写，默认是会话级别(session)

#### 3)自定义变量

##### 1.用户变量

1. 说明

   用户变量是用户自定义的变量，不是系统的变量

2. 使用步骤

   赋值操作符: ===== 或者 ==:===

   (1) 声明(三种方式)

   ```sql
   set @用户变量名=值;
   set @用户变量名:=值;
   select @用户变量名:=值;
   ```

   (2) 赋值

   1. 方式一

      ```sql
      set @用户变量名=值;
      set @用户变量名:=值;
      select @用户变量名:=值;
      ```

      ```sql
      set @name='张三';
      #没有定义类型，所以可以赋值各种类型
      set @name=100;
      ```

   2. 方式二

      ```sql
      #字段必须是一个
      select 字段 into @变量名
      from 表;
      ```

      ```sql
      set @count=0;
      SELECT COUNT(*) into @count 
      FROM woman;
      ```

   (3)查看值

   ```sql
   select @变量名;	
   ```

   ```sql
   SELECT @count;
   ```

3. 作用域

   针对当前会话（连接）有效，同于会话变量的作用域。

   应用在任何地方，也就是begin end 里面或者begin end外面

##### 2.局部变量

1. 作用域

   仅仅在定义它的begin end 中有效,并且==必须放在begin end第一句==

2. 声明

   ```sql
   declare 变量名 类型;
   declare 变量名 类型 default 值;
   ```

3. 赋值

   1. 通过set或者select

      ```sql
      set 局部变量名=值;
      set 局部变量名:=值;
      select @局部变量名:=值;
      ```

   2. 通过select into

      ```sql
      #字段必须是一个
      select 字段 into 局部变量名
      from 表;
      ```

   3. 查看值

      ```sql
      select 局部变量名;
      ```


##### 3.用户变量与局部变量对比

|          | 作用域       | 定义与使用位置                  | 语法                    |
| -------- | ------------ | ------------------------------- | ----------------------- |
| 用户变量 | 当前会话     | 会话中的任何位置                | 必须加@，同时不限定类型 |
| 局部变量 | begin end 中 | 只能在begin end中，并且是第一句 | 一般不加@，限定类型     |



### 九.存储过程

#### 1)说明

1. 作用

   类似于Java中的方法

2. 含义

   一组预选编译好的sql语句的集合，可以理解为批处理

3. 好处

   - 提高代码的重用性
   - 简化操作
   - 减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

#### 2)语法

##### 1.创建语法

```sql
create procedure 存储过程名(参数列表)
begin
	存储过程体(一组合法的sql语句)
end
```

==注意点:==

1. 参数列表包含三部分

   参数模式 参数名 参数类型

   ```sql
   in name VARCHAR(20)
   ```

   - 参数模式

     - in

       该参数可以作为输入，也就是该参数需要调用方法入值

     - out

       该参数可以作为输出，也就是该参数可以作为返回值

     - inout

       该参数既可以作为输入也可以作为输出，也就是该参数既需要传入值，又可以返回值

2. 如果存储过程体仅仅只有一条语句，则 begin end 可以省略

3. 存储过程体中的每条sql语句的结尾要求必须加上分号

4. 存储过程的结尾可以使用delimiter 重新设置

   语法

   ```sql
   delimiter 结束标记
   ```

##### 2.使用语法

```sql
call 存储过程名(实参列表)
```

#### 3)空参存储过程

```sql
#定义
delimiter $
CREATE PROCEDURE insert_age_rank()
BEGIN
	
	INSERT INTO age_rank(`name`,`start`,`end`) VALUES('F',0,10);
END $
#调用
CALL insert_age_rank()$
```

==注意==

 在命令行执行存储过程

#### 4)in模式的存储过程

```sql
delimiter $
CREATE PROCEDURE exist_data(in col_name VARCHAR(32))
BEGIN
		DECLARE cout int DEFAULT 0;
		SELECT COUNT(*) INTO cout
		from age_rank 
		where `name` = col_name;
		SELECT cout;
END $
```

#### 5)out 模式

1. 一个out参数

   ```sql
   delimiter $
   CREATE PROCEDURE woman_to_man(in woman_name VARCHAR(32), out man_name VARCHAR(32))
   begin
   	SELECT m.`name` into man_name
   	from woman w INNER JOIN man m 
   	on w.man_id = m.id
   	where w.`name` = woman_name;
   end $
   
   set @man_name=''$
   call woman_to_man('李冰冰',@man_name)$
   ```

2. 多个out 参数

   ```sql
   delimiter $
   CREATE PROCEDURE woman_to_man_age(in woman_name VARCHAR(32), out man_name VARCHAR(32), out man_age INT)
   begin
   	SELECT m.`name`, m.age into man_name, man_age
   	from woman w INNER JOIN man m 
   	on w.man_id = m.id
   	where w.`name` = woman_name;
   end $
   
   SET @man_age=0$
   
   call woman_to_man_age('李冰冰',@man_name,@man_age)$
   ```


#### 6)inout模式

```sql
CREATE PROCEDURE argument_double(inout a int, inout b int)
begin
			SET a = a * 2;
			set b = b * 2;
end $

set @a_1=2$
set	@a_2=4$
call argument_double(@a_1,@a_2)$
```

#### 7)删除存储过程

1. 语法

   ```sql
   drop procedure 存储过程名
   ```

2. 练习

   ```sql
   drop PROCEDURE  woman_to_man_age;、
   ```

#### 8)查看存储过程的信息

1. 语法

   ```sql
   show create procedure 存储过程名
   ```

   ```sql
   show create PROCEDURE woman_to_man;
   ```

### 十.函数

#### 1)说明

1. 作用

   类似于Java中的方法

2. 含义

   一组预选编译好的sql语句的集合，可以理解为批处理

3. 好处

   - 提高代码的重用性
   - 简化操作
   - 减少了编译次数并且减少了和数据库服务器的连接次数，提高了效率

#### 2)函数与存储过程的区别

|          | 返回值                                  | 作用                   |
| -------- | --------------------------------------- | ---------------------- |
| 函数     | 必须有一个，使用retures关键字           | 适合做批量插入，更新   |
| 存储过程 | 可以没有，也可以有使用(out,inout关键字) | 适合处理数据后返回一个 |

#### 3)语法

##### 1.创建语法

```sql
create function 函数名(参数列表) returns 返回类型
begin
	函数体
end 
```

==注意==

1. 参数列表包含两部分:

   参数名 参数类型

2. 函数体

   肯定会有return语句，如果没有回报错

   如果return 语句没有放在函数体最后也不会报错，但不建议

3. 函数体中只有一条语句，begin end 可以省略

4. 使用delimiter 设置结束标记

##### 2.函数的调用

```
select 函数名(参数列表);
```

#### 4)例子

##### 1.无参函数

```sql
delimiter $
CREATE FUNCTION fun_count() RETURNS INT
BEGIN
  DECLARE cou int DEFAULT 0;
	SELECT count(*) into cou 
	FROM woman;
	return cou;
END $

SELECT fun_count()$
```

##### 2.有参函数

```sql
delimiter $
CREATE FUNCTION fun_woman_to_man( woman_name VARCHAR(32)) RETURNS VARCHAR(32)
begin
	DECLARE man_name VARCHAR(32) DEFAULT '';
	SELECT m.`name` into man_name
	from woman w INNER JOIN man m 
	on w.man_id = m.id
	where w.`name` = woman_name;
	return man_name;
end $

SELECT fun_woman_to_man('李冰冰')$
```



#### 5)删除存储过程

1. 语法

   ```sql
   drop function 存储过程名
   ```

2. 练习

   ```sql
   drop FUNCTION fun_woman_to_man;
   ```

#### 6)查看存储过程的信息

1. 语法

   ```sql
   show create function 存储过程名
   ```

   ```sql
   show create function fun_woman_to_man;
   ```

### 十一.流程控制

#### 1)分支结构

1. IF函数

   - 功能

     实现简单的双分支

   - 语法

     ```
     IF(表达式1,表达式2,表达式3)
     ```

     执行顺序: 如果表达式1成立，返回表达式2的值，否则返回表达式3的值

   - 应用

     可以应用到任何地方

     ```sql
     SELECT IF(1=2,'哈哈哈','呵呵');
     ```

2. case结构

   - 情况一: 类似于Java中的switch语句，一般用于实现等值判断

     - 语法

       ```
       case 变量|表达式|字段
       when 要判断的值 then 返回值1或者语句1;
       when 要判断的值 then 返回值2或者语句2;
       ...
       else 要返回的值n或者语句n;
       end 【case】;
       ```

   - 情况二: 类似Java中的多重IF语句，一般用于区间判断

     - 语法

       ```
       case 
       when 要判断的条件1 then 返回值1或者语句1;
       when 要判断的条件1 then 返回值2或者语句2;
       ...
       else 要返回的值n或者语句n;
       end 【case】;
       ```

   - 特点

     - 如果then后面是值，可以放在任何地方，包括begin end里面和外面
     - 如果then后面是语句，则只能放在begin end 之中
     - 如果then后面是语句，则结尾要加分号结尾
     - 如果then后面是值，则最后不要case，如果是语句，则要加上case

     ```sql
     SET @age=21;
     
     SELECT CASE @age
     	WHEN 10 THEN '10'
     	WHEN 20 THEN '20'
     	ELSE
     		'其他'
     END;
     ```

     ```sql
     delimiter $
     CREATE PROCEDURE range_word(in num INT, out rank CHAR(1))
     begin
     	CASE 
     	WHEN num > 100 THEN
     		set rank='A';
     	WHEN num > 90 THEN
     	  SET rank='B';
     	WHEN num > 80 THEN
     	  SET rank='C';
     	ELSE
     		SET rank='D';
     	end case;
     END $
     
     #使用
     set @re=''$
     call range_word(91,@re)$
     SELECT @re$
     ```

3. if结构

   - 功能

     实现多重分支

   - 语法

     ```
     if 条件1 then 语句1;
     else if 条件2 then 语句2;
     ...
     【else 语句n;】
     end if;
     ```

   - 注意

     ==只能应用在begin end中==

     ```sql
     delimiter $
     CREATE PROCEDURE range_word(in num INT, out rank CHAR(1))
     begin
     	IF num > 100 THEN set rank='A';
     	ELSEIF num > 90 THEN set rank='B';
     	ELSEIF num > 80 THEN set rank='C';
     	ELSE set rank='D';
       END IF;
     END $
     ```

#### 2)循环结构

1. 分类

   - while

     - 语法

       ```sql
       【标签:】while 循环条件 do
       	循环体
        end while 【标签】;
       ```

   - loop

     - 语法

       ```sql
       【标签:】loop 
       	循环体
        end loop 【标签】;
       ```

   - repeat

     - 语法

       ```
       【标签:】repeat  
       	循环体
        until 循环结束条件
        end repeat 【标签】;
       ```

2. 循环控制语句

   - Iterate 

     类似于continue

   - leave

     类似break	

3. 例子

   ```sql
   CREATE PROCEDURE insert_data_to_my_test( in num int)
   begin
   	DECLARE i int DEFAULT 0;
   	
   	a:WHILE i < num DO
   		SET i = i + 1;
   		IF MOD(i,2) <> 0 THEN
   			ITERATE a;
   		END IF;
   		insert into my_test(my_name) VALUES(CONCAT('lisi-',i ));
   	END WHILE a;
   
   END $
   ```

4. 经典例子

   随机生成字符串，插入到表中

   ```sql
   delimiter $
   CREATE PROCEDURE insert_data_to_my_test_two(in num int)
   begin
   	 
   	 DECLARE all_str char(26) DEFAULT 'abcdefghijklmnopqrstuvwxyz';
   	 DECLARE last_str varchar(26) DEFAULT '';
   	 DECLARE len int DEFAULT 0;
   	 DECLARE i int DEFAULT 0;
   	 DECLARE loc int DEFAULT 0;
   	 
   	 WHILE i < num DO
   	 
   			set len = FLOOR(RAND() * 26) + 1;
   			set last_str = '';
   			
   			WHILE len > 0 DO
   			 set loc = FLOOR(RAND() * 26) + 1;
   			 SET last_str = CONCAT(last_str,SUBSTR(all_str,loc,1));
   			 set len = len - 1;
   			END WHILE;
   			
   			INSERT into my_test(my_name) VALUES(last_str);
   			SET i = i + 1;
   	 END WHILE;
   end $
   ```

   去重之后插入

   ```sql
   delimiter $
   CREATE PROCEDURE insert_data_to_my_test_two(in num int)
   begin
   	 
   	 DECLARE all_str char(26) DEFAULT 'abcdefghijklmnopqrstuvwxyz';
   	 DECLARE last_str varchar(26) DEFAULT '';
   	 DECLARE len int DEFAULT 0;
   	 DECLARE i int DEFAULT 0;
   	 DECLARE loc int DEFAULT 0;
   	 DECLARE flag int DEFAULT 0;
   	 
   	 a:WHILE i < num DO
   	 
   			set len = FLOOR(RAND() * 26) + 1;
   			set last_str = '';
   			set flag = 0;
   			
   			WHILE len > 0 DO
   			 set loc = FLOOR(RAND() * 26) + 1;
   			 SET last_str = CONCAT(last_str,SUBSTR(all_str,loc,1));
   			 set len = len - 1;
   			END WHILE;
   			
   			SELECT COUNT(*) into flag 
   			FROM my_test
   			where my_name = last_str;
   			
   			IF flag > 0 THEN
   				ITERATE a;
   			END IF;
   
   			INSERT into my_test(my_name) VALUES(last_str);
   			SET i = i + 1;
   		
   	 END WHILE a;
   end $
   ```


### 十二.索引

#### 1)索引是什么

1. 索引是帮助mysql高效的获取数据的数据结构

   ==排好序的快速查找数据结构==(B树的结构)

2. 数据本身除外，数据库还维护着一个满足特定查找算法的数据结构，这些数据结构以某种方式指向数据，这样就可以在这些数据结构的基础上实现高级查询算法，这种数据结构就是索引。

3. 一般来说索引本身也很大，不可能全部储存在内存中，因此索引往往以索引文件的形式存储在磁盘上

#### 2)索引的优势与劣势

##### 1优势

1. 通过创建唯一性索引，可以保证数据库表中每一行数据的唯一性.
2. 可以大大加快数据的检索速度，这也是创建索引的最主要的原因
3. 可以加速表和表之间的连接，特别是在实现数据的参考完整性方面特别有意义
4. 在使用分组和排序子句进行数据检索时，同样可以显著减少查询中分组和排序的时间。
5. 通过使用索引，可以在查询的过程中，使用优化隐藏器，提高系统的性能。

##### 2.劣势

1. 创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增加。
2. 索引需要占物理空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间，如果要建立聚簇索引，那么需要的空间就会更大。
3. 当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，这样就降低了数据的维护速度。

#### 3)索引分类

##### 1.单值索引

即一个索引只包含单个列，一个表可以有多个单列索引

##### 2.唯一索引

索引列的值必须唯一，但允许为null

##### 3.复合索引

即一个索引包含多个列

#### 4)索引的操作

##### 1)索引的创建

1. 方式一

   ```sql
   create 【unique】index 索引名 on 表名(表的列名1(长度),表的列名1(长度)...)
   ```

   ```sql
   CREATE UNIQUE INDEX name_index on my_test(my_name);
   ```

2. 方式二

   ```sql
   alter table 表名 add 【unique】index 索引名 (表的列名1(长度),表的列名1(长度)...)
   ```

   ```sql
   alter TABLE my_test add UNIQUE INDEX name_index (my_name);
   ```

   - 添加PRIMARY KEY（主键索引）  

     ```sql
     ALTER TABLE `table_name` ADD PRIMARY KEY ( `column` )
     ```

   - 添加UNIQUE(唯一索引) 

     ```sql
     ALTER TABLE `table_name` ADD UNIQUE ( `column` ) 
     ```

   - 添加INDEX(普通索引) 

     ```sql
     ALTER TABLE `table_name` ADD INDEX index_name ( `column` )
     ```

   - 添加FULLTEXT(全文索引)

     ```sql
     ALTER TABLE `table_name` ADD FULLTEXT ( `column`) 
     ```

   - 添加多列索引 

     ```sql
     ALTER TABLE `table_name` ADD INDEX index_name ( `column1`, `column2`, `column3`)
     ```

##### 2)索引的删除

```sql
drop index 索引名 on 表名
```

```sql
DROP INDEX name_index on my_test;
```

##### 3)查看索引

```sql
show index 索引名 from 表名
```

```sql
show INDEX from my_test; 
```



#### 5）索引结构

##### 1)BTree索引

##### 2)Hash索引

##### 3)full-text全文索引

##### 4)R-Tree索引

#### 6)可以创建索引的条件

1. 主键自动建立唯一索引
2. 频繁作为查询条件的字段应该创建索引
3. 查询中与其他表关联的字段，外键关系建立索引
4. 频繁更新的字段不适合创建索引，因为每次更新还要更新索引，加重了io负担
5. 在高并发下倾向创建组合索引
6. 查询中的排序字段应该建立索引。排序字段若通过索引将大大提高排序速度
7. 查询中的统计或者分组字段应该建立索引。

#### 7)不可以创建索引的条件

1. 表记录太少
2. 经常增删改的表或者字段
3. 数据重复并且分布均匀的表字段
4. where条件里用不到的字段不要创建索引

### 十三.explain

#### 1)介绍

	使用explain关键字可以模拟优化器执行sql查询语句，从而知道mysql是如何处理你的sql语句，分析查询语句或者表结构的性能瓶颈

#### 2）作用

1. 获取表的读取顺序
2. 数据读取操作的操作类型
3. 哪些索引可以使用
4. 哪些索引被实际使用了
5. 表之间的引用
6. 每张表有多少行被优化器查询

#### 3）使用

```sql
explain sql语句
```

```sql
explain SELECT * from my_test LIMIT 90,10;
```

执行结果:

![](./image/mysql-explain/column_name.png)

#### 4)explain执行结果列解释

##### 1) id

1. 含义

   select 查询的序列号，包含一组数字，表示查询中执行select 子句或者操作表的顺序

2. 出现的三种情况

   - id相同:执行顺序由上至下

   - id不同:如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行

   - id相同不同:同时存在（如果相同id，则走第一个规律，不同走第二个）


##### 2)select_type

1. 含义

   查询的类型，主要是用于区别普通查询，联合查询，子查询等查询

 2.可选类型

- simple

  进行不需要Union操作或不含子查询的简单select查询时，响应查询语句的select_type 即为simple（查询中包含连接的情形也一样）。无论查询语句是多么复杂，执行计划中select_type为simple的单位查询一定只有一个。最外侧的select查询的select_type通常为simple

  ```sql
  SELECT `user_id`, `amount` FROM `coupons`  WHERE `locked`= 0 AND `expired_at`= 1476892800 
  ```

- primary

  一个需要Union操作或含子查询的select查询执行计划中，位于最外层的select_type即为primary。与simple一样，select_type为primary的单位select查询也只存在1个，位于查询最外侧的select单位查询的select_type为primary

- union

  由union操作联合而成的单位select查询中，除第一个外，第二个以后的所有单位select查询的select_type都为union。union的第一个单位select的select_type不是union，而是DERIVED。它是一个临时表，用于存储联合（Union）后的查询结果。

  ```sql
  explain 
  select * from(
  (select emp_no from employees el limit 10)
  union all 
  (select emp_no from employees e2 limit 10)
  union all
  (select emp_no from employees e3 limit 10)
  ) tb;
  ```

  ![](./image/mysql-explain/primary_union.png)

  3个联合（union）的select 查询中，只有第一个（el数据表）不是union，其余两个的select_type均为union。union的第一个查询设置为代表整个union结果的select_type类型。此外要将3个子查询的结果用union all进行联合，并创建临时表进行使用，所以union all的第一个查询的select_type为DERIVED。

- derived

  产生虚拟表的产生查询的selectType

  ```sql
  EXPLAIN SELECT ar.`name` ,wa.ag from (
  SELECT AVG(w.age) ag
  FROM woman w
  GROUP BY age
  ) wa left JOIN age_rank  ar
  on wa.ag > ar.`start` and wa.ag <= ar.`end`;
  ```

  ![](./image/mysql-explain/derived.png) 

  上面的虚拟表derived2是由下面的w表执行查询产生的，索引w的selec_type是derived

- UNION RESULT

  UNION操作的结果，id值通常为NULL

  ```sql
  EXPLAIN SELECT * from woman where id > 5 UNION SELECT * from woman where id > 4
  ```

  ![](./image/mysql-explain/union_result.png)

- subquery

  在select或者query列表中包含子查询

##### 3)table

	显示查询来自哪一张表

##### 4)type

1. 含义

   显示查询用了哪种类型

2. 可以选的类型

   - system

     CONST的特例，当表上只有一条元组匹配

   - const 

     表示通过索引一次就找到了，const用于比较primary key或者union索引。因为这两个都是唯一的，比较一次就可以查到

     ```sql
     EXPLAIN SELECT * from woman where id='212sd2kld';
     ```

     const在==单表==查询中使用到了唯一索引,只查到了==最多一条==数据

   - eq_ref

     参与连接运算的表是内表（在代码实现的算法中，两表连接时作为循环中的内循环遍历的对象，这样的表称为内表）。

     基于索引（连接字段上存在唯一索引或者主键索引，且操作符必须是“=”谓词，索引值不能为NULL）做扫描，使得对外表的一条元组，内表只有==唯一一条元组==与之对应。

     ==内表就是左外查询的右表，右外查询的左表==

     ```sql
     EXPLAIN SELECT w.* from woman w left JOIN man m 
     on w.man_id = m.id;
     ```

     ![](./image/mysql-explain/eq_ref.png)

     ==多表==查询中内表使用了唯一索引查询,只查到了==最多一条==数据，则对应的查询的type类型就是eq_ref

   - ref

     非唯一索引扫描，返回匹配的某个==单值==的所有行(==多行==结果)

     ```sql
     #创建索引
     CREATE index idx_man_id on woman(man_id);
     
     EXPLAIN SELECT * from woman where man_id='212sd2kld';
     ```

   - range

      范围扫描，基于索引做范围扫描，为诸如BETWEEN，IN，>=，LIKE类操作提供支持

     ```sql
     EXPLAIN SELECT w.age from woman w where w.age >= 28;
     ```

     ==注意:==

     1. 只遍历==索引的一部分==，是range，==全部==是index

        如果上面的sql中age最小值小于28，则是range

        如果上面的sql中age最小值大于28，则是index,因为要查找全部索引

   - index

     遍历索引，获取到了数据

     ```sql
     EXPLAIN SELECT w.age from woman w where w.age >= 28;
     ```

   - all

     全表扫描或者范围扫描：不使用索引，顺序扫描，直接读取表上的数据（访问数据文件）

##### 5)possible_keys

	可能用在查询中的索引，一个或者多个

##### 6)key

   实际使用的索引，如果为null,则没有使用索引

- 覆盖索引

  就是select的数据列只用从索引中就能够取得，不必从数据表中读取，换句话说查询列要被所使用的索引覆盖。

  在 extra中标有==using index== 表明使用了覆盖索引

##### 7)key_len

	表示索引中使用的字节数，可以通过该列计算查询中使用的索引长度，在不损失精确性的情况下，长度越短越好
	
	该值显示的是可能的最大长度，并非实际的值。即key_len是根据定义计算而得，不是通过表内检索出来的

##### 8)ref

	关联其他表或者条件的参数类型或者来源

```sql
EXPLAIN SELECT * from 
woman w INNER JOIN man m  on w.man_id = m.id 
where w.age = 30;
```

![](./image/mysql-explain/ref.png)

const 表明：w.age = 30中的30是常量

crud.w.man_id 表明 w.man_id = m.id 中的w.man_id 来自数据库crud的w表的man_id字段

##### 9)rows

查询记录所需要读取的行数

##### 10)extra

额外信息

1. using index

   出现这个说明mysql使用了==覆盖索引==，避免访问了表的数据行，效率不错！

2. using where

   这说明服务器在存储引擎收到行后将进行过滤。有些where中的条件会有属于索引的列，当它读取使用索引的时候，就会被过滤，所以会出现有些where语句并没有在extra列中出现using where这么一个说明。

3. using temporary

   这意味着mysql对查询结果进行排序的时候使用了一张临时表。==效率不行==，需要优化

4. using filesort

   这个说明mysql会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。==效率不行==，需要优化

#### 5)两表关联索引建立(相反加)

1. 如果是左外，则左边的表都要遍历，在右边表中查询，所以应该在右表关联字段建立索引
2. 如果是右外，则右边的表都要遍历，在左边表中查询，所以应该在左表关联字段建立索引
3. 多张表一次类推

#### 6)索引失效

##### 1.最佳做前缀

- 如果索引了多列，则查询的顺序要与索引的顺序一致，不能出现最前面的不一致或者中间一致

  ```sql
  ALTER TABLE woman add index idx_name_age_man_id (`name`,`age`,`man_id`);
  ```

  - 全都有

    ```sql
    EXPLAIN SELECT * from woman where  `name`='aas' and age = 34 and man_id='sas';
    ```

    ![](./image/mysql-explain/all.png)

  - 如果没有头部

    ```sql
    EXPLAIN SELECT * from woman where   age = 34 and man_id='sas';
    ```

    ![](./image/mysql-explain/no_header.png)

  - 没有中间

    ```sql
    EXPLAIN SELECT * from woman where  `name`='aas'  and man_id='sas';
    ```

    ![](./image/mysql-explain/no_center.png)

  - 尾部与中间一样

  总结：最好与创建索引时的列一样，如果不一样，==一定要保证头部是一样的==，不然该索引不会使用

##### 2)请不要在索引列上做任何操作

1. 操作包括

   (计算，函数，(自动或者手动)类型转换)

2. 结果

   会导致索引失效，转向搜索全表

3. 例子

   ```sql
   EXPLAIN SELECT * from woman where  left(`name`,2)='aas';
   ```

   ![](./image/mysql-explain/add_column.png)	

   添加了left(`name`,2)之后，索引失效

##### 3)存储引擎不能使用索引中范围条件右边的列

```sql
EXPLAIN SELECT * from woman where  `name`='aas' and age > 34 and man_id='sas';
```

![](./image/mysql-explain/range.png)

总结:==在条件中加了 > , < ,in , between and ,在该字段之后的字段不会使用到索引中==

##### 4)尽量使用覆盖索引，减少使用select *

##### 5)使用不等号(!=, <>)会使索引失效

```sql
EXPLAIN SELECT * from woman where  `name`!='aas';
```

![](/Users/tgy/Documents/Java/study-doc/mysql/image/mysql-explain/not_eq.png)

##### 6)使用 is not null 或者 is null  会使索引失效

##### 7) like ==左边==使索引失效

```sql
EXPLAIN SELECT * from woman where  `name` like '%as';
```

![](./image/mysql-explain/like_left.png)

==使用覆盖索引，解决like使索引失效问题==

```sql
EXPLAIN SELECT id, age,`name` from woman where  `name` like 'a%s%';
```

![](./image/mysql-explain/cover_index_like.png)

##### 8)字符串不加单引号会使索引失效

```sql
EXPLAIN SELECT * from woman where `name` = 2000;
```

![](./image/mysql-explain/string_no_quote.png)

##### 9)少用or，用or也会使索引失效

```sql
EXPLAIN SELECT * from woman where `name` = '2000' or age = 23;
```

![](./image/mysql-explain/or_index.png)

#### 7）练习

![](./image/mysql-explain/all_pra.png)

#### 8)索引失效总结

	==全值匹配我最爱，最左前缀要遵守；==
	
	==带头大哥不能死，中间兄弟不能断；==
	
	==索引列上少计算，范围之后全失效；==
	
	==like百分写最右，覆盖索引不写星；==
	
	==不等空值还有or，索引失效要少些；==
	
	==var引号不能丢，sql高级也不难；==

#### 9)慢sql处理过程

##### 1.操作

1. 观察，至少跑一天，看看生产的慢sql情况
2. 开启慢查询日志，设置阀值，比如超过5秒的就是慢sql，并把这些sql抓取出来
3. explain  + 慢sql分析
4. show profile
5. 运营经理或者DBA，进行sql数据库服务器的参数调优

##### 2.过程

1. 慢查询的开启并捕获
2. explain + 慢sql分析
3. show profile 查询SQL在mysql服务器里面的执行细节和生命周期情况
4. SQL数据库服务器的参数调优

#### 10)小表驱动大表

##### 1)优化原则

小表驱动大表，即小的数据集驱动大的数据集

1. in

   ```sql
   select * from A where id 
   in (select id from B)
   
   #等价于
   select id from B;
   select * from A where B.id = A.id 
   ```

2. exists

   ```sql
   select * from A where 
   exists 
   (select 1 from B where A.id = B.id)
   
   #等价于
   select * from A;
   select 1 from B where B.id = A.id
   ```

   ==当B表的数据小于A表时，in 优于exists==

   ==当B表的数据大于A表时，exists 优于in==

##### 2)exists总结

1. 语法

   ```sql
   select * from 表明 where exists (子查询)	
   ```

   将主查询的数据，放在子查询中做条件验证，根据验证结果(true 或者false)来决定查询的结果是否保留

2. 总结

   - exists(子查询) 只返回true或者false，因此子查询中select * from 也可以是select 1 或者 selec 'x',官方表明实际执行过程中会忽略select 清单，因此没有区别
   - Exists 子查询的实际执行过程可能经过了优化而不是我们理解的逐句对比。如果担心效率问题，可进行实际验证以确定是否有效率问题
   - exists 子查询往往也可以用条件表达式，其他子查询或者join来替代，何种最优需要具体问题具体分析 



#### 11) order by 优化



##### 1.Mysql两种排序方式

1. 文件排序
2. 扫描有序索引排序(==推荐==)

##### 2.Mysql 能为排序与查询使用相同索引

##### 3.order by 使用情况

假设索引建立是:

```sql
alter A add index idx_a_b_c (a,b,c);
```

1. Order by 能使用索引最左前缀

   ```sql
   order by a
   order by a,b
   order by a,b,c
   order by a desc, b desc, c desc
   ```

2. 如果where 使用索引的最左前缀定义为常量，则order by能使用索引

   ```sql
   where a=const order by b,c
   where a=const order by b=const order by c
   where a=const order by b>const order by b,c
   ```

3. ==不能==使用索引的order by

   ```sql
   order by a asc,b desc,c asc #排序不一样
   where g=const order by b,c #丢失a索引
   where a=const order by c #丢失b索引
   where g=const order by a,d #d不是索引的一部分
   where a in (a1,a2,a3,...) order by b,c #对于排序来说，多个相等条件也是范围查询
   ```

#### 12)group by 优化

1. order by适用的group by都使用

2. group by的实质是先排序再分组，遵照索引建立的最佳左前缀

3. 当无法使用索引列，增大max_length_for_sort_data参数的设置+ 增大sort_buffer_size的参数设置

4. where 高于having，能写在where限定的条件就不要写在having中


#### 13）慢查询日志

1. 查询是否设置了

   ```sql
   show VARIABLES LIKE '%slow_query_log%';
   ```

2. 设置慢查询日志

   ```sql
   set global slow_query_log=1
   ```

3. 设置阀值

   ```sql
   #查看默认值:默认是10秒
   show VARIABLES LIKE '%long_query_time%';
   #设置时间为3秒
   set global long_query_time=3;
   ```

4. 查看日志文件的位置

   ```sql
   show VARIABLES LIKE '%slow_query_log_file%';
   ```

5. 查询有多少条慢sql

   ```sql
   show GLOBAL STATUS LIKE '%slow_queries%';
   ```

6. 借助mysqldumpslow明令使用

   - 可加的参数

     ![](./image/mysql-explain/mysqldumpslow_help.png)

   - 例子

     ![](./image/mysql-explain/mysqldumpslow_eg.png)

#### 14)批量插入数据

##### 1)创建表

- 员工表

  ```sql
  create table `employee`(
   `id` varchar(32) PRIMARY KEY,
   `employee_name` VARCHAR(32) not null,
   `employee_age` int DEFAULT 0,
   `department_id` int
  );
  ```

- 部门表

  ```sql
  create table `department`(
  	`id` int auto_increment,
  	`department_name` varchar(32) not null,
  	PRIMARY key(id)
  );
  ```

##### 2)函数和存储过程

- 生成随机字符串的函数

  ```sql
  delimiter $
  CREATE FUNCTION random_str(num INT) RETURNS VARCHAR(32)
  BEGIN
  	DECLARE all_str VARCHAR(26) DEFAULT 'abcdefghijklmnopqrstuvwxyz';
  	DECLARE return_str VARCHAR(32) DEFAULT '';
  	DECLARE i int DEFAULT 0;
  	
  	WHILE i < num DO
  		set return_str = CONCAT(return_str,SUBSTR(all_str,FLOOR(RAND() * 26) + 1,1));
  		set i = i + 1;
  	END WHILE;
  	return return_str;
  end $
  ```

- 生成随机整数

  ```sql
  drop FUNCTION if EXISTS rand_int$
  CREATE FUNCTION rand_int(base int, rand int) returns Int
  begin
  
  	DECLARE return_int int DEFAULT 0;
  	set return_int = floor(base + RAND() * rand);
  	return return_int;
  end $
  ```

- 插入数据的存储过程

  ```sql
  drop PROCEDURE if EXISTS insert_data_to_emp_dep$
  CREATE PROCEDURE insert_data_to_emp_dep(in num int)
  BEGIN
  	DECLARE i int DEFAULT 0;
  	DECLARE flag int DEFAULT 0;
  
  	SELECT COUNT(*) into flag from department;
  	-- 	手动管理事务
  	set autocommit = 0;
  	WHILE i < num DO
  		IF  flag = 0 and i < 10 THEN
  			INSERT into department(`department_name`) VALUES(CONCAT('人事部-',i + 1));
  		END IF;	
  		insert into employee(id,employee_name,employee_age,department_id) VALUES(random_str(15),random_str(9),rand_int(20,30),rand_int(1,10));
  		set i = i + 1;
  	END WHILE;
  	commit;
  end $
  ```

##### 3)设置log_bin_trust_function_creators

```sql
show VARIABLES LIKE '%log_bin_trust_function_creators%';
set GLOBAL log_bin_trust_function_creators = 1;
```

##### 4)插入随机数

```sql
call insert_data_to_emp_dep(500000)$
```

#### 15)show Profile的使用

##### 1.作用

	查询sql各个步骤具体使用的时间以及各种资源

##### 2.使用过程

1. 开启

   ```sql
   #查看
   show VARIABLES like '%profiling%';
   #设置
   set profiling=on;
   ```

2. 查看所有的sql

   ```sql
   show PROFILES;
   ```

3. 查看指定sql

   ```sql
   show PROFILE cpu,block io for QUERY 53;
   ```

   可以选的参数

   ![](./image/mysql-explain/profile_arg.png)


##### 3.出现如下列说明sql有问题

1. Coverting heap to myisam 

   查询结果太大，内存不够往磁盘上面搬

2. creating tmp table

   创建临时表(拷贝数据到临时表，用完删除)

3. Coping to tmp table on disk 

   把内存中临时的表复制到磁盘中

4. locked

#### 16)全局查询日志

1. 开启

   ```sql
   #查看
   SHOW VARIABLES LIKE '%general_log%';
   #开启
   set GLOBAL general_log=on;
   #设置输出到表中
   SET GLOBAL log_output='table';
   ```

2. 使用

   ```sql
   SELECT * from mysql.general_log;
   ```

### 十四.数据库锁

#### 1)表锁(MyISAM引擎)

1. 表锁引擎必须是==MyISAM==，设置mysql引擎

   - 创建表时设置

     ```sql
     create 表名(
     	字段
     )engine 引擎名
     ```

     ```sql
     CREATE TABLE `woman` (
       `id` varchar(32) NOT NULL,
       `name` varchar(64) NOT NULL,
       `age` int(11) NOT NULL,
       `man_id` varchar(32) DEFAULT NULL,
       `parent_id` varchar(32) DEFAULT NULL,
       PRIMARY KEY (`id`),
       KEY `idx_name_age_man_id` (`name`,`age`,`man_id`)
     ) ENGINE=MyISAM DEFAULT CHARSET=utf8mb4;
     ```

   - 修改表

     ```java
     ALTER TABLE 表名 ENGINE = 引擎名;
     ```

     ```sql
     ALTER TABLE `crud`.`woman` ENGINE = MyISAM;
     ```

2. 读写锁语法

   - 读或者写锁语法

     ```sql
     lock table 表名 READ|write, 表名 READ|write ...
     ```

   - 解锁

     ```sql
     unlock tables;
     ```

   - 查看表锁的情况

     ```sql
     SHOW OPEN TABLES;
     ```

3. 读锁特性

   ![](image/mysql-lock/read_lock.png)

4. 写锁特性

   | Session 01                                                   | Session 02                             |
   | ------------------------------------------------------------ | -------------------------------------- |
   | LOCK TABLE woman WRITE;  <br>==使用写锁把表锁了==            |                                        |
   | select * from woman <br>==直接是等待中==                     |                                        |
   |                                                              | select * from woman ==直接也是等待中== |
   | UPDATE woman set age = 32 where id='212sd2kld';<br/>INsert into woman(id,`name`,age) VALUES('posasd','赵雅芝',78);<br>可以修改和插入 |                                        |
   |                                                              | 不能可以修改和插入                     |
   | SELECT * from books;<br>查询其他表报错:Table 'books' was not locked with LOCK TABLES, |                                        |
   | Unlock tables;                                               |                                        |
   |                                                              | select * from woman ==显示结果==       |

5. 总结

   ![](image/mysql-lock/lock_all.png)

   简而言之: ==就是读锁会阻塞写，但是不会阻塞读,而写则会把写与读都阻塞==

6. 表锁分析

   - 查看哪些表锁了

     ```sql
     show open tables;
     ```

   - 分析表锁定

     可以通过检查table_locks_waited 和table_locks_immediate 状态变量分析系统上的表锁定

     ```sql
     show STATUS like 'table%';
     ```

     table_locks_immediate:产生表级锁定的次数，表示可以立即获取锁的查询次数，每理解获取锁值加1

     table_locks_waited:出现表级锁竞争而发生等待的次数，不能立即获取锁的次数，没等待一次值加1，此值高则说明存在严重的表级锁竞争情况。

     此外，Myisam的读写锁调度是==写优先==，这也是myisam不适合做写为主表的引擎。因为写锁后，其他线程不能做任何操作，大量的更新会使查询很难得到锁，从而造成永远阻塞

#### 2)行锁(Innodb)

1. 特点

   - 行锁，InnoDB存储引擎，开销大，加锁慢;会出现死锁;锁定粒度最小，发生锁冲突的概率最低,并发度也最高。
   - InnoDB.与MyISAM的最大不同有两点:
     - 一是支持事务(TRANSACTION) ;
     - 二是采用了行级锁

2. 行锁的基本演示

   | Session01                                                    | Session02                                                    |
   | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | set autocommit=0;<br>UPDATE man set age = 34 where id='3323477'; |                                                              |
   |                                                              | set autocommit=0;<br>UPDATE man set age = 34 where id='3323477';<br>等待中..... |
   |                                                              | UPDATE man set age = 35 where id='422322';<br>可以更新       |
   | commit                                                       |                                                              |
   |                                                              | UPDATE man set age = 34 where id='3323477';<br/>立即         |
   |                                                              | commit                                                       |

3. 如果在执行一个SQL语句时MySQL不能确定要扫描的范围，InnoDB表同样会==锁全表==

   ```sql
   update line_lock set a=1 where b like '%aaa%';
   ```

4. 间隙锁

   当使用范围更新是，如果中间有的范围没有，则insert 中间行，则该行会被锁住，直到前一个session提交

   | Session01                                                    | Session02                                                    |
   | ------------------------------------------------------------ | ------------------------------------------------------------ |
   | set commit;<br>update line_lock set b='rtr' where a BETWEEN 1 and 4; |                                                              |
   |                                                              | set commit;<br/>INSERT into line_lock VALUES(2,'sadd');<bre>等待中... |
   | commit;                                                      |                                                              |
   |                                                              | 执行完成.                                                    |
   |                                                              | Commit                                                       |

5. 锁定一行

   ```sql
   SELECT * from line_lock where a=4 for UPDATE;
   ```

6. 查询锁的效率

   ```sql
   show status like 'innodb_row_lock%';
   ```

   ![image-20181108103126241](image/mysql-lock/innodb_lock_view.png)

   Innodb_ row_ lock_ current_ waits: 当前正在等待锁定的数量;
   Innodb_ row_ lock_ time: 从系统启动到现在锁定总时间长度;
   Innodb_ row_ lock_ time_ avg: 每次等待所花平均时间;
   Innodb_ row_ lock_ time_ max: 从系统启动到现在等待最常的一次所花的时间;
   Innodb_row_lock_waits:系统启动后到现在总共等待的次数;
   对于这5个状态变量，比较重要的主要是
   ==Innodb_ row_ lock_ time_ avg (等待平均时长)== ,
   ==Innodb_ row_ lock_ _waits (等待总次数)==
   ==Innodb_ row_ lock_ time (等待总时长)这三项==。
   尤其是当等待次数很高，而且每次等待时长也不小的时候，我们就需要分析系统中为什么会有如此多的等待，然后根据分析结果着手指定优化计划。

### 十五.触发器

https://www.cnblogs.com/duodushu/p/5446384.html

https://www.cnblogs.com/phpper/p/7587031.html