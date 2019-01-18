1. 学生表 如下:
   自动编号   学号   姓名 课程编号 课程名称 分数
   1        2005001 张三 0001     数学    69
   2        2005002 李四 0001      数学    89
   3        2005001 张三 0001      数学    69
   删除除了自动编号不同, 其他都相同的学生冗余信息

   ```sql
   DELETE s from crud_student s 
   where s.`id` not in (
     SELECT a.id FROM (
   		SELECT MIN(cs.`id`) id from crud_student cs
   		GROUP BY cs.`name`, cs.`num`
   	) a
   );
   ```

   注意:

   1. from 后面的表使用别名之后，delete 后面删除的表名必须使用别名

      ```sql
      #这样会报错
      DELETE `crud_student` FROM `crud_student` s where s.`id` not in(
      	1,2
      );
      ```

   2. 出现 **You can't specify target table 's' for update in FROM clause**问题

      把查找出来的结果当做一个表重新从这个临时表中查找

      ```sql
      SELECT a.id FROM (
      		SELECT MIN(cs.`id`) id from crud_student cs
      		GROUP BY cs.`name`, cs.`num`
      ) a
      ```

https://www.cnblogs.com/diffrent/p/8854995.html