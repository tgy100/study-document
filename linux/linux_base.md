### 一.根文件系统(rootfs):

root file system

LSB, FHS: (FileSystem Heirache Standard)
/etc, /usr, /var, /root, /home, /dev

#### (1) /boot:

	引导文件存放目录，内核文件(vmlinuz)、引导加载器(bootloader, grub)都存放于此目录:

#### (2) /bin:

	供所有用户使用的基本命令:不能关联至独立分区,OS启动即会用到的程序	

#### (3) /sbin

	管理类的基本命令:不能关联至独立分区,OS启动即会用到的程序:

#### (4) /lib

	基本共享库文件，以及内核模块文件(/lib/modules);

#### (5) /lib64

	专用于x86 64系统上的辅助共享库文件存放位置: 

#### (6) /etc

	配置文件目录(纯文本文件);
	
	/etc/man.config: man命令查找路径的配置文件

#### (7) /home/USERNAME

	普通用户家目录:

#### (8) /root:

	管理员的家目录

#### (9) /media

	便携式移动设备挂载点: cdrom,usb

#### (10) /mnt:

	临时文件系统挂载点;

#### (11) /dev

 	设备文件及特殊文件存储位置:
​	1.b: block device, 随机访问
​	2.c: character device, 线性访问

#### (12) /opt

	第三方应用程序的安装位置;

#### (13) /srv 

	系统上运行的服务用到的数据: 

#### (14) /tmp 

	临时文件存储位置:

#### (15) /usr: 

    universal shared, read -only data;

- bin:保证系统拥有完整功能而提供的应用程序:
- sbin:
- lib: 
- lib64:
- include: C程序的头文件(header files);
- share:结构化独立的数据，例如doc, man等;
- local:第三方应用程序的安装位置:
  里面有: bin, sbin, lib, lib64, etc, share

#### (16) /var

	variable data files

- cache:应用程序缓存数据目录;
- lib:应用程序状态信息数据:
- local:专用于为/usr/local下的应用程序存储可变数据;
- lock:锁文件
- log:日志目录及文件: 
- opt:专用于为/opt下的应用程序存储可变数据;
- run:运行中的进程相关的数据:通常用于存储进程的pid文件;
- spool:应用程序数据池:
- tmp:保存系统两次重启之间产生的临时数据;

#### (17) /proc:

	用于输出内核与进程信息相关的虚拟文件系统:

#### (18) /sys:

	用于输出当前系统上硬件设备相关信息的虚拟文件系统;

#### (19) /selinux: 

	security enhanced Linux, selinux相关的安 全策略等信息的存储位置:

#### (20)Linux上的应用程序的组成部分:

##### 1)二进制程序: 

	/bin, /sbin, /usr/bin, /usr/sbin, /usr/local/bin, /usr/local/sbin

##### 2)库文件: 

	/lib, /lib64, /usr/lib, /usr/lib64, /usr/local/lib, /usr/local/lib64

##### 3)配置文件: 

	/etc, /etc/DIRECTORY, /usr/local/etc

##### 4)帮助文件: 

	/usr/share/man, /usr/ share/doc, /usr/local/ share/man, /usr/local/ share/doc

#### (21)linux下的文件类型;

##### 1) - (f): 普通文件:

##### 2) d: 目录文件; 

##### 3) b: 块设备文件;

##### 4) c: 字符设备文件:

##### 5) l: 符号链接文件;

##### 6) p: 管道文件;

##### 7) s: 套接字文件; socket;

### 二.linux操作

#### (1)系统管理类命令:

##### 1)关机:

	halt, poweroff, shutdown, init 0

1. shutdown:
   shutdown [OPTION].... TIME [MESSAGE ]

   - -r: reboot 重启

   - -h: halt 关机

   - -c: cance 取消

   - TIME:

     now:立刻
     +m:相对时间表示法，从命令提交开始多久之后;例如+3;
     hh:mm:绝对时间表示，指明具体时间:

##### 2)重启:

	reboot, shutdown, init 6

1. reboot		
   - -f:强制，不调用shutdown
   - -p:切断电源	



##### 3)跟用户登录相关:

who, whoami, w

用户登录信息查看命令: 

1. **whoami**:显示当前登录有效用户
2. **who**:系统当前所有的登录会话:
3. **w**:系统当前所有的登录会话及所做的操作:

#### (2)bash的基础特性:

##### 1)命令历史:history 

1. 环境变量:

- HISTSIZE:命令历史记录的条数:

  ```shell
  echo $HISTFILE
  ```

- HISTFILE:  ~/.bash_history;

  ```shell
  echo $HISTFILE
  ```

- HISTFILESIZE:命令历史文件记录历史的条数: 

- history -d OFFSET :删除对应的历史

- -c :删除所有的历史记录

- history :显示历史中最近的#条命令;

- -a :手动追加当前会话缓冲区的命令历史至历史文件中;

2. 调用历史中的命令;

- !#: 重复执行第#条指令:
- !!: 重复执行上一个命令
- !string 
- !$: 调用上一条命令的最后一个参数
  ESC.
  Alt+.

3. 控制命令历史的记录方式:
   环境变量: HISTCONTROL
   - ignoredups:忽略重复的命令;连续且相同方为“重复”;
   - ignorespace:忽略所有以空白开头的命令:
   - ignoreboth: ignoredups, ignorespace 
     修改环境变量值的方式: export 变量名-"值"
     变量赋值:把赋值符号后面的数据存储于变量名指向内存空间:

##### 2)命令补全

bash执行命令:

- 内部命令:
- 外部命令: bash根据PATH环境变量定义的路径，自左而右在每个路径搜寻以给定命令名命名的文件，第一次找到即为要执行的命令;
  直接补全: Tab,用户给定的字符串只有一条惟一对应的命令;
  以用户给定的字符串为开头对应的命令不惟一，则再次Tab会给出列表:

##### 3)路径补全

把用户给出的字符串当做路径开头，并在其指定上级目录下搜索以指定的字符串开头的文件名:
如果惟一:则直接补全; 否则:再次Tab, 给出列表

##### 4)命令行展开

- ~ : 展开为用户的主目录 

- ~USERNAME: 展开为指定用户的主目录

  ```shell
  #跳转到tgy的home目录
  cd ~tgy
  ```

- {}: 可承载一个以逗号分隔的列表，并将其展开为多个路径
  /tmp/{a,b} 生成为: /tmp/a, /tmp/b
  /tmp/{tom, jerry}/hi 生成为: /tmp/tom/hi, /tmp/jerry/hi

  **练习:**

  1. 如何创建: /tmp/x/y1, /tmp/x/y2, /tmp/x/y1/a, /tmp/x/y1/b, /tmp/x/y2/a, /tmp/x/y2/b
      mkdir -p /tmp/x/{y1,y2}/{a,b}
  2. 如何创建 : x_m, y_m, x_n, y_n
      mkdir -p  {x,y}_{m,n}
  3. 如何创建 :/tmp/bin, /tmp/sbin, /tmp/usr, /tmp/usr/bin, /tmp/usr/sbin 
      mkdir -p /tmp/{bin, sbin, usr/{bin, sbin}}

##### 5)命令的执行结果状态

成功
失败

- bash使用特殊变虽**$?**保存最近一条命令的执行状态结果:
  1. 0:成功
  2. 1-255;失败
- 程序执行有两类结果:
  1. 程序的返回值:
  2. 程序的执行状态结果;

##### 6) 命令别名(alias )

通过alias命令实现:

1. alias 
   显示当前shell进程所有可用的命令别名;
2. alias NAME = 'VALUE '
   定义别名NAME,其相当于执行命令VALUE;
   注意:在命令行中定义的别名，仅对当前shel1进程有效;如果想永久有效，要定义在配置文件中:
   仅对当前用户:  ~/.bashrc
   对所有用户有效: /etc/bashrc
   Note:编辑配置给出的新配置不会立即生效:
   bash进程重新读取配置文件:
   source /path/to/config_ file
3. 撤消别名: unalias
   unalias [-a] name [name ...]
   Note:如果别名同原命令的名称，则如果要执行原命令，可使用"\COMMAND" ;

##### 7) gloab

bash中用于实现文件名“通配”
通配符: *, ?, []

1. \*: 任意长度的任意字符
   a*b;
   aab, ab, a123b,
   abc

2. ? :任意单个字符

   a?b:

   aab,ab, a12b, abc

3. []:匹配指定范围内的任意单个字符
   \[0-9\]:0到9

   \[a-z\]: 不区分字符大小写

   \[A-Z\]: 大写字母

4. [^]
   匹配指定范围外的任意单个字符
   [^\0-9\] 
5. 专用字符集合:

- \[:digit:\]: 任意数字，相当于0-9
- \[ :lower:\]:任意小写字母
- \[:upper:\]: 任意大写字母
- \[:alpha:\]: 任意大小写字母
- \[:alnum:\]: 任意数字或字母
- \[:space:\]: 表示任意空格
- \[:punct:\]: 标点符号

6. 练习

   - 显示/var目录下所有以l开头，以一个小写字母结尾，且中间出现至少一位数字的文件或目录；

     ls /var /l[[:lower:]]

   - 显示/etc目录下， 以任意一位数字开头，且以非数字结尾的文件或目录;

   - 显示/etc/目录下，以非字母开头，后面跟了一个字母及其它任意长度任意字符的文件或目录;

   - 复制/etc目录下，所有以m开头，以非数字结尾的文件或目录至/tmp/mageedu目录中:

   - 复制/etc目录下，所有以.d结尾的文件或目录至/ tmp/magedu.com目录中;

   - 复制/etc目录下，所以有.conf结尾，且以m,n,r,p开头的文件或目录至/tmp/mageedu.com目录中: 

##### 8) bash的快捷鍵

- Ctrl+1:清屏，相当于clear命令;
- Ctrl+a:跳輳至命令幵始処:
- Ctrlte:跳輳至命令結尾処: 
- Ctrl+c:取消命令的抗行: 
- Ctrl+u:刪除命令行首至光村所在赴的所有内容;
- Ctrl+k:刪除光禄所在処至命令行尾部的所有内容;

##### 9) bash的的I/O重定向

程序:指令+数据
读入数据: Input 
输出数据: Output
打开的文件都有一个fd: file descriptor (文件描述符)
标准输入: keyborad, 0
标准输出: monitor, 1
标准错误输出: monitor, 2
I/0重定向:改变标准位置

1. 输出重定向: COMMAND > NEW POS, COMMAND >> NEW POS
   - \>:覆盖重定向，目标文件中的原有内容会被清除:
   - \>>:追加重定向，新内容会追加至目标文件尾部:
   - set -C: 禁止将内容覆盖输出至已有文件中;
   - 强制覆盖: >|
   - set +C:允许将内容覆盖输出至已有文件中;

2. 错误输出重定向

   - 2>:覆盖重定向错误输出数据流:

   - 2>>:追加重定向错误输出数据流:

   - 标准输出和错误输出各自定向至不同位置:
     COMMAND > /path/to/file.out 2> /path/to/erron . out

     ```shell
     ech pppp > ball.txt 2> er.txt
     ```

   - 合井标准输出和错误输出为同一个数据流进行重定向:
     &>:覆盖重定向
     &>>;追加重定向
     COMMAND > /path/to/file.out 2> &1
     COMMAND >> /path/to/file.out 2>> &1

3. 输入重定向: <
   tr命令;转换或删除字符

   tr [OPTION]... SET1 [SET2]

   ```shell
   #把从ba.txt 读取的内容转成大些字母
   cat ba.txt | tr 'a-z' 'A-Z'
   #把从ba.txt读取的内容中删除掉 abc
   cat ba.txt | tr -d 'abc'
   ```

   Here Document 是在Linux Shell 中的一种特殊的重定向方式，它的基本的形式如下

   ```shell
   cmd << delimiter
     Here Document Content
   delimiter
   ```

   它的作用就是将两个 delimiter 之间的内容(Here Document Content 部分) 传递给cmd 作为输入参数。

   ```shell
   ~$ cat << EOF
   > First Line
   > Second Line
   > Third Line EOF
   > EOF
   First Line
   Second Line
   Third Line EOF
   ```

   **注意**

   1. EOF 只是一个标识而已，可以替换成任意的合法字符
   2. 作为结尾的delimiter一定要顶格写，前面不能有任何字符
   3. 作为结尾的delimiter后面也不能有任何的字符（包括空格）
   4. 作为起始的delimiter前后的空格会被省略掉

   Here Document 不仅可以在终端上使用，在shell 文件中也可以使用，例如下面的here.sh 文件

   ```shell
   cat << EOF > output.sh
   echo "hello"
   echo "world"
   EOF
   ```

##### 10)管道

COMMAND1| COMPAND2| COMMAND3 I ...
Note:最后一个命令会在当前shell进程的子shell进程中执行:

**tee命令**:  用于将数据重定向到文件，另一方面还可以提供一份重定向数据的副本作为后续命令的stdin
​	tee [oPTIoN]... [FILE]...

- -a：向文件中重定向时使用追加模式；
- -i：忽略中断（interrupt）信号。

练习:
1、将/etc/passwd文件中 的前5行非注释内容转换为大写后保存至/tmp/passwd. out文件中;

```shell
sed -e '/^#/d' passwd | head -n 5 | tee -a passwd5low.txt | tr 'a-z' 'A-Z' > passwd5Up.txt
```

2、将登录至将前系统上用户信息中的后3行的信息转换为大写后保存至/tmp/who.out文件中: 

```shell
who | tail -n 3| tr 'a-z' 'A-Z' > /tmp/who.out
```

##### 11) 文本处理工具: wc, cut, sort, uniq

- wc命令:
  wc  [OPTION]... [FILE]...

  - -l: lines
  - -w: words
  - -c: characters 

- cut命令:
  cut [OptIoN]... [FILE]...

  - -d (delimiter): 指明分隔符

  - -f :显示指定字段的内容

    - 第#个字段

    - \#,#[,#]:  高散的多个字段，例如1,3,6

    - \#-#: 连续的多个字段，例如1-6

    - 混合使用: 1-3,7

  - --output-delimiter=<字段分隔符>：指定输出内容时的字段分割符

- sort命令:
  sort [OPTIoN]... [FILE]...

  - -f: 忽略字符大小写
  - -r:逆序
  - -t DELIMITER: 字段分隔符
  - -k #: 以指定列数为标准排序
  - -n: 以数值大小进行排序
  - -u: uniq, 排序后去重

  ```shell
  cut -d':' -f1,3 --output-delimiter=' ' passwd | sort -t' ' -k 2 -un 
  ```

- uniq命令:
  uniq [OPTION]... [FILE]...

  - -c: 显示每行重复出现的次数:

  - -d; 仅显示重复过的行;

  - -u:仅显示不曾重复的行:

    **Note:连续且完全相同方为重复**

练习:

	以冒号分隔，取出/etc/passwd文件 的第6至第10行，并将这些信息按第3个字段的数值大小进行排序:最后仅显示的各自的第1个字段:

```shell
sed -e '/^#/d' passwd |head -n 10 | tail -n 5 | sort -t':' -k 3 -n | cut -d':' -f1
```

##### 12) 提供了编程环境

- 程序:指令+数据

- 程序编程风格:

  - 过程式:以指令为中心，数据服务于指令

  - 对象式:以数据为中心，指令服务于数据

    shell 程序;提供了编程能力，解释执行

- 过程式编程: 

  - 顺序执行
  - 循环执行
  - 选择执行

- shell编程:过程式、解释执行

- 编程语言的基本结构: 

  - 数据存储:变量、数组
  - 表达式
  - 语句

- shell脚本:文本文件

- shebang : #!

   首先，这个符号（#!）的名称，叫做”Shebang”或者”Sha-bang”（还有其他一些名称，不过我一般就用这两个）。

    	Shebang这个符号通常在Unix系统的脚本中第一行开头中写到，它指明了执行这个脚本文件的解释程序。

    1. 如果脚本文件中没有#!这一行，那么它执行时会默认用当前Shell去解释这个脚本(即：$SHELL环境变量）。

    2. 如果#!之后的解释程序是一个可执行文件，那么执行这个脚本时，它就会把文件名及其参数一起作为参数传给那个解释程序去执行。

    3. 如果#!指定的解释程序没有可执行权限，则会报错“bad interpreter: Permission denied”。
         如果#!指定的解释程序不是一个可执行文件，那么指定的解释程序会被忽略，转而交给当前的SHELL去执行这个脚本。

    4. 如果#!指定的解释程序不存在，那么会报错“bad interpreter: No such file or directory”。
         注意：#!之后的解释程序，需要写其**绝对路径**（如：#!/bin/bash），它是不会自动到$PATH中寻找解释器的

    5. 当然，如果你使用”bash test.sh”这样的命令来执行脚本，那么#!这一行将会被忽略掉，解释器当然是用命令行中显式指定的bash。

  - \#! /bin/bash 
  - \#! /usr/bin/ python
  - \#! /usr/bin/perl 

- magic number:魔数

- 运行脚本: 
  1、给予执行权限，通过具体的文件路径指定文件执行:
  2、直接运行解释器，将脚本作为解释器程序的参数运行:

- 变量:
  命名的内存空间;

- 数据存储方式: ASCII
  字符:110: 24 bit
  数值:110 --> 8bit
  整型
  浮点型
  变量:变量类型
  作用:
  1、数据存储格式:
  2、参与的运算;
  3、表示的数据范围;

- 短路运算:

  - 与:
    - 第一个为0,结果必定为0;
    - 第一个为1,第二个必须要参与运算:
  - 或:
    - 第一个为1,结果必定为1:
    - 第一个为0,第二个必须要参与运算:

  练习:判断一个用户是否存在，存在输出 exisit , 不存在输出 no exisit

  ```shell
  id tgy > /dev/null && echo 'exisit' || echo 'no exisit'
  ```


#### (3)录管理类命令;

cd, pwd, ls
mkdir, rmdir, tree

##### 1)mkdir 

	[ options ] /path/ to/ somewhere

- -p: 存在于不报错，且可自动创建所需的各目录;
- -v: 显示详细信息
- -m MODE: 创建目录时直接指定权限;

##### 2) tree:

- -d: 只显示目录
- -L level: 指定显示的层级数目
- -P pattern: 只显示由指定pattern匹 配到的路径:

##### 3)rmdir;删除空目录

rmdir [OPTION]... DIRECTORY...

- -v: 显示过程:

#### (4)文本文件查看命令

cat, tac 
more, less, tail, head

##### 1) more

	more [OPTIONS...] FILE...

- -d: 显示翻页及退出提示

##### 2) less

	less [OPTIONS..] FILE

##### 3) head

	head [OPTION].. [FILE]

- -c #: 指定获取前#个字符
- -n #: 指定获取前#行
- \#: 直接获取前#行

##### 4) tail

- -c #:指定获取后#个字符
- -n #: 指定获取后#行
- \#: 指定获取后#行
- -f: 跟踪显示文件新追加的内容

#### (5) 文件的时间戳管理工具:

touch

1. 文件: metadata(元数据,就是文件的属性，rex,类型), data
2. 查看文件状态: stat
3. 三个时间戳:
   - access time:访问时间，简写为atime, 读取文件内容
   - modify time:修改时间，mtime, 改变文件内容(数据)
   - change time: 改变时间，ctime, 元数据发生改变
4. touch命令:
   touch [OPTION]... FILE...
   - -a: only atime
   - -m: only mtime
   - -t STAMP: 修改文件的时间戳
     [[CC]YY]MMDDhhmm[.ss]
   - -c: 如果文件不存，则不予创建

#### (6) 文件管理:

	cp, mv, rm

##### 1) 复制命令: cp

cp [OPTION]... [-T] SOURCE DEST
cp [OPTION]... SOURCE... DIRECTORY 
cp [OPTION]... -t DIRECTORY SOURCE...
cp SRC DEST

###### 1.功能解释

1. SRC是文件:

   - 如果目标不存在:新建DEST,并将SRC中内容填充至DEST中:
   - 如果目录存在:
     - 如果DEST是文件:将SRC中的内容覆盖至DEST中:
       此时 建议为cp命令使用-i选项;
     - 如果DEST是目录:在DEST 下新建与原文件同名的文件，并将SRC中内容填充至新文件中: 

2. SRC:多个文件
   DEST必须存在，且为目录，其它情形均会出错:

3. SRC是目录:
   此时使用选项: -r 

   - 如果DEST不存在:则创建指定目录，复制SRC 目录中所有文件至DEST中: 

   - 如果DEST存在:

     - 如果DEST是文件:报错

     - 如果DEST是目录:则把SRC的目录复制到DEST目录之中。

###### 2.常用选项

1. -i: 交换式cp
2. -r,-R: 递归复制
3. -a: 归档，相当于-dR --preserv=all
   - -d: 软连接不跟踪到原地址文件
   - -- p reserv[=ATTR_ LIST ]

##### 2)移动文件: mv

mv: move,移动文件
mv [OPTION]... [-T] SOURCE DEST
mv [OPTIoN]... SOURCE... DIRECTORY
mv [OPTION]... -t DIRECTORY SOURCE...
常用选项:

- i:交互式
- -f:强制

##### 3)删除 :rm

删除
rm [OPTION]... FILE.
..
常用选项:

- -i:交互式
- -f;强制删除
- -r: 递归
  rm -rf

##### 4)文本编辑器:nano

 全屏编辑器

#### (7) 用户和组管理

##### 1) 资源分派: 

- Authentication:认证

- Authorization:授权
- Accouting:审计
   Audition

##### 2) 用户登录系统的方式

- token ： token授权
- identity(username/pas sword)：用户密码

##### 3) Linux用户: 

	Username(用户名)/UID(用户对应的ID)

- 管理员: root, 0 

- 普通用户: 1-65535

  - 系统用户: 1-499 

    对守护进程获取资源进行权限分配;

  - 登录用户:500+
    交互式登录: 

##### 4) Linux组:

	 Grougname/GID

- 管理员组:root,0
- 普通组:
  - 系统组: 

     centos 6: 1-499 

    centos 7: 1-999

    在 /etc/login.defs目录中查看

  - 普通组: 

    centos 6: 500+

    centos 7: 1000+

##### 5) Linux安全上下文:

运行中的程序:进程(process)
以进程发起者的身份运行:
root:Cat
tom: cat
进程所能够访问的所有资源的权限取决于进程的发起者的身份;

##### 6) Linux组的类别: 

- 用户的基本组(主组):
   组名同用户名，且仅包含一个用户:私有组
- 用户的附加组(额外组):

##### 7) Linux用户和组相关的配置文件:

1. /etc/passwd:用户及其属性信息(名称、UID、基本组ID等等);

   - passwd文件每一列的含义:

     ```
     name :password:UID:GID: GECOS : directory:shell
     用户名;密码:UID:GID;GECOS:主目录:默认shel
     ```

2. /etc/group:组及其属性信息;

   - group文件每一列的含义:

     ```
     group_ name : password :GID:user_ list
     组名:组密码:GID:以当前组为附加组的用户列表(分隔符为逗号) 
     ```

3. /etc/shadow:用户密码及其相关属性;

   - shadow文件每一列的含义:

     ```
     用户名:加密了的密码:最近一次更改密码的日期:密码的最小使用期限:最大密码使用期限:密码警告时间段:密码禁用期:账户过期日期:保留字段
     ```

   - 加密机制:
     加密:明文-->密文
     解密:密文-->明文
     单向加密:提取数据指纹
     md5: message digest, 128bits
     sha1: secure hash algorithm, 160bits
     sha224: 224bits
     sha256: 256bits 
     sha384: 384bits
     sha512: 512bits

     **linux密码使用sha512 同时加盐的方式加密,密码的加密方式保存在/etc/login.defs中**

     ```
     tgy:$6$FZrCT1gc$.pJlPMlv364J74rpdmSJsM5e3I.0Ajgcagxh70VrzIFv920MCGAwUw6vjgfM.BDFsTdPiIntc07BI1WUMMKk3.:17878:0:99999:7:::
     ```

     - 用户名: tgy

     - 密码

       - 6代表sha512

       - \$ 到下一个\$ 之间的(FZrCT1gc)就是sha512加的**随机盐**

   - 雪崩效应:初始的条件的微小改变，将会引起结果的巨大改变;
     定长输出

   - 密码的复杂性策略:
     1、 使用数字、大写字母、小写字母及特殊字符中至少3种
     2、足够长:
     3、使用随机密码: 
     4、定期更换;不要使用最近曾经使用过的密码;

4. /etc/gshadow:组密码及其相关属性;

##### 8) 用户和组相关的管理命令;

1. 用创建用户 useradd
   useradd [ options] LOGIN

   - -u UID: [UID MIN, UID_ MAX]， 定文在/etc/login.defs

   - -g GID: 指明用户所属基本组，可为组名，也可以GID(组id);

   - -c "comment": 用户的注释信息;

   - -d /PATH/TO/HOME_ DIR: 以指定的路径为用户的家目录;

   - s  SHELL: 指明用户的默认shell程序，可用列表在/etc/shells文件中;

   - G GROUP1[ , GROUP2,...[ ,GROUPN]. 为用户指明附加组：组必须事先存在;

   - -r 创建系统用户

     CentOS 6: uid < 500

     Centos 7: uid < 1000 

     默认值设置在: /etc/default/useradd文 件中

     ```
     GROUP=100
     HOME=/home
     INACTIVE=-1
     EXPIRE=
     SHELL=/bin/bash
     SKEL=/etc/skel
     CREATE_MAIL_SPOOL=yes
     ```

   - useradd -D -s SHELL

   练习;创建用户gentoo, 附加组为distro和linux, 默认shell为 /bin/csh , 注释信息为:"Gentoo Distribution";

   ```shell
   #创建组
   groupadd g_one
   groupadd distro
   
   useradd -G g_one,distro -s /bin/csh -c'this is new user' tgy03
   #查看用户组id信息
   id tgy03 
   ```


```shell
//看配置文件
sed -e '/^#\|^$/d' /etc/sudoers
```

2. groupadd 创建组

   groupadd [option...] groupName

   - -g #: 指定gid，[GID_MIN, GID_MAX],在 /etc/login.defs文件中指明了GID_MIN,GID_MAX

     centos 6 : [500 60000]

     centos 7: [1000 60000]

   - -r :创建系统组

     centos 6 : [1 499]

     centos 7: [1 999]

3. id 查看用户相关的id信息

4. 切换用户或以其他用户身份执行命令:su
   su [options...][-] [user [args...]]

   - 切换用户的方式:

     - su UserName: 非登录式切换，即不会读取目标用户的配置文件:
     - su - UserName: 登录式切换，会读取目标用户的配置文件:完全切换: 

      Note: root su至其他用户无须密码;非root用 户切换时需要密码;

   - 换个身份执行命令:

     su [-] UserName -c ' COMMAND'

     ```shell
     su - tgy03 -c 'ls /'
     ```

5. 用户属性修改: usermod
   usermod [OPTION] login

   - u UId: 新UID
   - g GID: 新基本组
   - -G GROUP1[ ,GROUP2,...[ ,GROUPN]]]: 新附加组，原来的附加组将会被覆盖;若保留原有，则要同时用-a选项，表示append;
   - -s  SHELL:新的默认SHELL:
   - -c 'COMMENT'; 新的注释信息;
   - -d HOME: 新的家目录:原有家目录中的文件不会同时移动至新的家目录:若要移动，则同时使用-m选项;
   - -l login name: 新的名字;
   - -L: lock指定用户
   - -U: unlock指定用户

6. 给用户添加密码: passwd
   passwd [OPTIONS] UserName: 修改指定用户的密码，仅root用户有权限
   passwd:  修改自己的密码;
   常用选项;

   - -l: 锁定指定用户

   - -u:解锁指定用户

   - -n mindays: 指定最短使用期限

   - -x maxdayss 最大使用期限

   - -W warndays:提前多少天开始警告

   - -i inactivedays: 非活幼期限; 

   - --stdin:
     从标准输入接收用户密码;
     echo "PASSWORD" |  passwd --stdin USERNAME

     ```sh w
     #设置tgy03的密码为:tgy123456,不提示修改结果
     echo 'tgy123456' | passwd --stdin tgy03 &> /dev/null
     #显示上一条命令执行结果
     echo $?
     ```

     Note: /dev/nu11, bit buckets
     ​	   /dev/zero, 产生0

7. 删除用户: userdel
   userdel [OPTION]... login
   -r: 删除用户家目录:

8. 组属性修改: groupmod
   groupmod [OPTION]... group
   -n group name: 新名字
   -g gID:新的GID;

9. 组删除: groupdel
   groupdel GROUP

10. 组密码: gpasswd
   gpasswd [OPTIoN] GROUP

   - -a user :
     将user添加至指定组中;
   - -d user:删除用户user的以当前组为组名的附加组
   - -A user1,user2,...: 设置有管理权限的用户列表

11. newgrp命令:

    临时切换基本组, 如果用户本不属于此组，则需要组密码;

    exit	:退出临时基本组

#### (8) 权限管理

##### 1)文件的权限主要针对三类对象进行定义:

- Owner: 属主，u
- group: 属组，g
- other:其他，o

##### 2)每个文件针对每类访问者都定义了三种权限:

- r: Readable
- w: Writable
- x; eXcutable

##### 3) 文件:

- r: 可使用文件查看类工具获取其内容:
- w: 可修改其内容:
- x: 可以把此文件用内核启动为一个进程执行:

##### 4) 目录:

- r:可以使用ls查看此目录中文件列表: 

- w:可在此目录中创建文件，也可删除此目录中的文件;

- x:可以使用ls -l查看此目录中文件列表，可以cd进入此目录;

   000 0
     --x 	001 1
     -w- 	010 2
     -wx  011 3
     r--	100 4
     r-x 	101 5
     rw- 	110 6
     rwx 111 7

##### 5)chmod:修改用户权限

chmod [OPTION]... MODE[ ,MODE]... FILE...
MODE:修改一类用户的所有权限:
u=
g=
o=
ug= :把用户权限和组权限设置为一样的

a= :同时设置所有权限
u=, g= : 分别设置用户权限和组权限
修改一类用户某位或某些位权限
u+	
u-

##### 6) 修改文件的属主(chown)和属组(chgrp):

**仅root可用;**

- 修改文件的属主: chown
  chown [0PTION].. [OWNER][:[GROUP]] FILE..
  用法:

  - owner : 设置属主

  - owner : group :设置属主与属组

  - : group 设置属组

    **Note:命令中的冒号可用. 替换**

  -R: 递归替换

- 修改文件的属组: chgrp

##### 7) 文件或目录创建时的遮罩码:umask

- 文件(file):  666 - umask
  Note:如果某类的用户的权限减得的结果中存在x权限，则将其权限+1
- 目录(DIR): 777- umask
- umask:查看
- umask #:设定
  命令总结: chmod, chown, chgrp, umask

#### (9)文本处理

Linux上文本处理三剑客:

- grep:文本过滤(模式: pattern)工具;
  grep, egrep, fgrep
- sed: stream editor,文本编辑工具:
- awk: Linux 上的实现gawk,文本报告生成器: 

##### 1) grep

	grep: Global search REgular expression and Print out the line.
	
	grep [OPTIONS] PATTERN [FILE...]

###### 1.作用:

	文本搜索工具，根据用户指定的“模式”对目标文本逐行进行匹配检查:打印匹配到的行:

- 模式:由正则表达式字符及文本字符所编写的过滤条件;
- REGEXP:由一类特殊字符及文本字符所编写的模式，其中有些字符不表示字符字面意义，而表示控制或通配的功能;
  **分两类:** 
  - 基本正则表达式: BRE
  - 扩展正则表达式: ERE

###### 2.选项: 

- --colorm auto:对匹配到的文本着色显示:
- -v:显示不能够被pattern匹配到的行;
- -i:忽略字符大小写:
- -o:仅显示匹配到的字符串;
- -q:静默模式，不输出任何信息:
- -A #: after,后#行
- -B #: before, 前#行
- -C #: context, 前后各#行
- -E: 使用ERE;

###### 3.基本正则(base REG)

1. 字符匹配:

   - . :匹配任意单个字符;
   - [] :匹配指定范围内的任意单个字符
   - \[^\]: 匹配指定范围外的任意单个字符
     - [:digit:], [:lower:], [:upper:],[:alpha:], [:alnum:],[:punct:],[:space:]

2. 匹配次数:用在要指定次数的字符后面，用于指定前面的字符要出现的次数;

   - \*: 匹配前面的字符任意次:
     - .\*: 任意长度的任意字符
   - \?; 匹配其前面的字符0或1次:即前面的可有可无
   - \\+: 匹配其前面的字符至少1次:
   - \\{m\\}:匹配前面的字符m次:
   - \\{m,n\\};匹配前面的字符至少m次，至多n次:
     \ \{0,n\\}:匹配前面的字符至多n次: 
     \\{m,\\}:匹配前面的字符至少m次:

3. 位置锚定:

   - ^ : 行首锚定;用于模式的最左侧;

   - $: 行尾锚定; 用于模式的最右侧;

   - ^pattern\$:用于模式匹配整行:

     - ^\$: 空行
     - ^[[:space:]]*$: 空白行

     ```shell
     #找出所有空行
     # -n 显示行号
     grep -n '^[[:space:]]*$' pattern.txt
     ```

   - \\< 或\b:词首锚定:用于单词模式的左侧:
     \\>或\b:词尾锚定;用于单词模式的右侧:
     \\<PATTERN\\> :匹配整个单词;

     ```shell
      #查找单词首部是p,尾部是x的单词
      grep -n '\<p[[:alpha:]]\+x' passwd 
      #查找单词root
      grep -n '\<root\>' passwd
     ```

   - 分组:
     \\(\\): 将一个或多个字符绑定在一起，当作一个整体进行处理;
     \\(xy\\)\*ab

   - 后向引用:引用前面的分组括号中的模式所匹配字符，(而非模式本身) 

     Note:分组括号中的模式匹配到的内容会被正则表达式引擎记录于内部的变量中，
     ​	 这些变量的命名方式为:    \1， \2， \3， ...
     ​	 1:从左侧起，第一个左括号以及与之匹配右括号之同的模式所匹配到的字符;

     ```shell
     \(ab\+\(xy\)*\)x
     \1: ab\+\(xy\)*
     \2: xy
     ```

   4. 组合

      \\|:组合两个pattern

   **练习:**

   1. 显示/proc/meminfo文件中以大小s开头的行: (要求:使用两种方式)

      ```shell
      grep -n '^[sS].*' /proc/meminfo
      grep -n '^s.*\|^S.*' /proc/meminfo 
      ```

   2. 显示/etc/passwd文件 中不以/bin/bash结尾的行:

      ```shell
      grep -v '.*\(/bin/bash\)$' passwd 
      ```

   3. 显示/etc/passwd文件中ID号最大的用户的用户名:

      ```shell
      sort -t: -k 3 -n passwd | cut -d: -f1| tail -1
      ```

   4. 如果用户root存在， 显示其默认的shell程序:

      ```shell
      grep '^\(root:\).*' passwd | cut -d: -f7
      ```

   5. 找出/etc/passwd中的两位或三位数:

      ```shell
      grep -o '\<[1-9][[:digit:]]\{1,2\}\>' passwd
      egrep -o '\<[0-9]{2,3}\>' passwd
      ```

   6. 显示/etc/rc.d/rc. sysinit文件中，至少以一个空白字符开头的且后面存非空自字符的行:

      ```shell
       grep '^\([[:space:]]\+\)[^[:space:]]\+' /etc/rc.d/rc.sysinit
       egrep '^([[:space:]]+)[^[:space:]]+'
      ```

   7. 找出"netstat -tan 命令的结果中以'LISTEN'后跟0、1或多个空白字符结尾的行

      ```shell
      netstat -tan |  grep -n 'LISTEN\([[:space:]]*\)$' net.txt
      netstat -tan | egrep -n 'LISTEN([[:space:]]*)$'
      ```

   8. 添加用户bash、 testbash、 basher以及nologin(其shell为/sbin/nologin); 而后找出/etc/passwd文件中用户名同shell名的行;

      ```shell
      grep '^\([^:]\+\):.*\1$' passwd
      egrep '^([[:alnum:]]+\>).*\1\>$' passwd
      ```

   9. 写一个脚本，实现如下功能
      如果user1用户存在，就显示其存在，否则添加之;
      显示添加的用户的id号等信息;

      ```shell
      #! /bin/bash
      
      id "$1" &> /dev/null && echo "$1 user have" || useradd "$1"
      id -u "$1"
      ```

   10. 写一个脚本，完成如下功能
      如果root用户登录了当前系统，就显示root用户在线:否则说明其未登录:

      ```shell
      (w | egrep '^root\>' &> /dev/null) && echo "root exisit" || echo "root no exisit"
      ```

      



##### 2）egrep

	egrep及扩展的正则表达式
	egrep =  grep -E
	egrep [OPTIONS] PATTERN [FILE...] I

1. 字符匹配:

   - . :匹配任意单个字符;
   - [] :匹配指定范围内的任意单个字符
   - \[^\]: 匹配指定范围外的任意单个字符
     - [:digit:], [:lower:], [:upper:],[:alpha:], [:alnum:],[:punct:],[:space:]

2. 匹配次数:用在要指定次数的字符后面，用于指定前面的字符要出现的次数;

   - \*: 匹配前面的字符任意次:
     - .\*: 任意长度的任意字符
   - ?; 匹配其前面的字符0或1次:即前面的可有可无
   - +: 匹配其前面的字符至少1次:
   - {m}:匹配前面的字符m次:
   - {m,n};匹配前面的字符至少m次，至多n次:
      {0,n}:匹配前面的字符至多n次: 
      {m,}:匹配前面的字符至少m次:

3. 位置锚定:

   - ^ : 行首锚定;用于模式的最左侧;

   - $: 行尾锚定; 用于模式的最右侧;

   - ^pattern\$:用于模式匹配整行:

     - ^\$: 空行
     - ^[[:space:]]*$: 空白行

   - \\< 或\b:词首锚定:用于单词模式的左侧:
     \\>或\b:词尾锚定;用于单词模式的右侧:
     \\<PATTERN\\> :匹配整个单词;

   - 分组:
     (): 将一个或多个字符绑定在一起，当作一个整体进行处理;
     (xy)\*ab

   - 后向引用:引用前面的分组括号中的模式所匹配字符，(而非模式本身) 

     Note:分组括号中的模式匹配到的内容会被正则表达式引擎记录于内部的变量中，
     ​	 这些变量的命名方式为:    \1， \2， \3， ...
     ​	 1:从左侧起，第一个左括号以及与之匹配右括号之同的模式所匹配到的字符;

4. 组合

   pattern | pattern 

   C|cat : C 或者cat

**练习:**

1. 显示当前系统root、nologin 或testbash用户的默认shell和UID;

   ```shell
   egrep '^(root|nologin|testbash)\>' passwd | cut -d: -f7,3
   ```

2. 找出/etc/rc . d/init. d/ functions文件(centos6)中某单词后面跟一个小括号的行;

   ```shell
   egrep '\<[[:alpha:]]+\>\(' egrep_test.txt
   ```

3. 使用echo输出一绝对路径，使用egrep取出其基名:

   ```shell
   echo /usr/local/bin | egrep -o  '[^/]+$'
   ```

4. 找出ifconfig命 令结果中1- 255之间的数值

5. 找出ifconfig命令结果中的IP地址;

   ```shell
   ifconfig | egrep -o '((2[0-4][[:digit:]]|25[0-5]|[01]?[[:digit:]][[:digit:]]?)\.){3}(2[0-4][[:digit:]]|25[0-5]|[01]?[[:digit:]][[:digit:]]?)'
   ```

#### (10) shell编程

##### 1) 变量类型:

​	数据存储格式、存储空间大小、参与运算种类:

- 字符型

- 数值型:

  - 整型
  - 浮点型

- 强类型:

  定义变量时必须指定类型、参与运算必须符合类型要求;调用未声明变量会产生错误;

- 弱类型;

  无须指定类型，默认均为字符型:参与运算会自动进行隐式类型转换;变量无须事先定义可直接调用;
  bash

- bash中的变量的种类:
  根据变量的生效范围等标准:

  - 本地变量:

    生效范围为当前shell进程:对当前shell之外的其它shell进程，包括当前shell的子shell进程均无效;

    - 变量赋值: name= 'value

    - 可以使用引用:
      value : 

      1. 可以是直接字串; name="username"

      2. 变量引用: name="\$username"

      3. 命令引用: name= COMMAND , name=​$ (COMMAND)

         执行COMMAND，把结果赋值给name

    - 变量引用: \${name}, ​$name

      - "": 弱引用，其中的变量引用会被替换为变量值:
      - '' : 强引用，其中的变量引用不会被替换为变量值，而保持原字符串;

    - 显示已定义的所有变量
      set 

    - 销毁变量:
      unset name

  - 环境变量:

    生效范围为当前shell进程及其子进程;

    - 变量声明、赋值:
      export name  = VALUE
      declare -x name = VALUE

    - 变量引用: \$name, ​${name}

    - 显示所有环境变量:

      - export
      - env
      - printenv

    - 销毁:
      unset name

    - bash有许多内建的环境变量: 

      PATH, SHELL, UID, HISTSIZE, HOME, PWD, OLDPWD,HISTILE, PS1

  - 局部变量:

    生效范围为当前shell进程中某代码片断(通常指函数);

  - 位置变量:$1,\$2,...

    来表示，用于让脚本在脚本代码中调用通过命令行传递给它的参数:

    ```shell
     LINES=$(wc -l $1 | cut -d' ' -f1)
     echo "file : $1,all lines: ${LINES} "
    ```

    

  - 特殊变量:

    - \$?: bash执行后返回的状态，0: 成功，非0: 失败 
    - \$0: 运行脚本的本身
    - \$* : 运行脚本的参数
    - \$@:  运行脚本的参数
    - \$#: 运行脚本的参数个数

  - 只读变量:

    - 声明:

      readonly name 
      declare -r name

      **只读变量声明后就不能修改，也不能删除，只能等到进程结束之后删除**

##### 2) bash的配置文件

###### 1.按生效范围划分

存在两类:

1. 全局配置:
   - /etc/profile
     	/etc/profile.d/* .sh
   - /etc/bashrc
2. 个人配置: 
   ~/ .bash_profile
   ~/ . bashrc

###### 2.按功能划分

存在两类: 

1. profile类:为交互式登录的shell提供配置
   - 全局: /etc/profile, /etc/profile.d/*.sh
   - 个人: ~/.bash_ profile
   - 功用:
     1. 用于定义环境变量
     2. 运行命令或脚本: 
2. bashrc类:为非交互式登录的shell提供配置
   - 全局: /etc/bashrc 
   - 个人: ~/.bashrc
   - 功用:
     1. 定义命令别名;
     2. 定义本地变量:

##### 3) shell登录

###### ①.交互式登录:

1. 登录方式

   - 直接通过终端输入账号密码登录;
   - 使用“su - UserName”或“su -l UserName”切换的用户

2. 读取配置文件的顺序

   ```
   /etc/profile --> /etc/profile.d/*.sh --> ~/.bash_profile --> ~/.bashrc --> /etc/bashrc
   ```

###### ②.非交互式登录:

1. 登录方式

   - su UserName
   - 图形界面下打开的终端

2. 读取配置文件的顺序

   ```
   ~/.bashrc --> /etc/bashrc --> /etc/profile.d/* .sh
   ```

###### ③.问题

1. 定义对所有用户都生效的别名?

   在 /etc/baserc 文件中定义

   ```
   alias tgy='echo "tgy"'
   ```

2. 让用户的PATH环境变量的值多出一个路径， 例如多如/usr/local/apache2/bin

   - root用户: /etc/profile

   - 所有用户: /etc/profile.d/xx.sh

     ```shell
     PATH="$PATH xxx"
     ```


##### 4) 运算符

​	+ ,- , *, / ,% ,  **

###### ①.实现算术运算:

1. let var=算术表达式
2. var=\$[算术表达式]
3. var=\$((算术表达式))
4. var=\$(expr arg1 arg2 arg3 ...)
   乘法符号有些场景中需要转义:

###### ②.bash有内建的随机数生成器: 

​	$RANDOM

###### ③. 增强型赋值: 

​	+=, -=, *=, /=, %=
​	let varOPERvalue

```shell
# 自加1，等同于: count=$[$count+1]
let count+=1
```

###### ④. 自增，自减:

```shell
let var+=1
let var++
let var-=1 
let var--
```

###### ⑤.练习

1. 写一个脚本
   计算/etc/passwd文件中的第10个用户和第20用户的ID之和;

   ```shell
   #! /bin/bash
   ONEUSERID=$(head -n 10 /etc/passwd | tail -n 1| cut -d: -f3)
   #echo "$ONEUSERID"
   TWOUSERID=$(head -n 20 /etc/passwd | tail -n 1| cut -d: -f3)
   #echo "$TWOUSERID"
   IDSUM=$[$ONEUSERID+$TWOUSERID]
   echo "two user id : $IDSUM"
   ```

2. 写一个脚本
   传递两个文件路径作为参数给脚本，计算这两个文件中所有空白行之和;

   ```shell
   #! /bin/bash
   
   num1=$(egrep '^[[:space:]]*$' $1 | wc -l | cut -d" " -f1)
   echo $num1
   num2=$(egrep '^[[:space:]]*$' $2 | wc -l | cut -d" " -f1)
   echo $num2
   echo "总共: $(($num1+$num2))"
   ```

3. 统计/etc, /var, /usr目录共有多少个一级子目录和文件:

   ```shell
   ls -l /etc | grep '^d'| wc -l
   ```

##### 5) 条件测试:

​	判断某需求是否满足，需要由测试机制来实现;
​	Note:专用的测试表达式需要由测试命令辅助完成测试过程:

###### ①.测试命令:

1. test EXPRESSION

2. [ EXPRESSION]

3. [[ EXPRESSION ]]

   Note: **EXPRESSION前 后必须有空白字符**

###### ②. 测试类型:

1. 数值测试:
   - -gt: 是否大于:
   - -ge: 是否大于等于:
   - -eq: 是否等于: 
   - -ne: 是否不等于: 
   - -lt: 是否小于:
   - -le: 是否小于等于:
2. 字符串测试:
   - ==; 是否等于: 
   - \>:是否大于:
   - <: 是否小于:
   - !=: 是否不等于:
   - =~: 左侧字符串是否能够被右侧的PATTERN所匹配:
   - -z "STRING": 测试字符串是否为空，空则为真，不空则为假;
   - -n "STRINC": 测试字符串是否不空，不空则为真，空则为假: 
     Note:用于字符串比较时的用到的操作数都应该使用引号:
3. 文件测试
   - 存在性测试
     - -a FILE: 文件是否存在
     - -e FILE:文件存在性测试，存在为真，否则为假:
   - 存在性及类别测试
     - -b FILE: 是否存在且为块设备文件:
     - -c FILE: 是否存在且为字符设备文件;
     - -d FILE: 是否存在且为目录文件;
     - -f FILE: 是否存在且为普通文件;
     - -h FILE或-L FILE: 存在且为符号链接文件:
     - -p FILE: 是否存在且为命名管道文件:
     - -S FILE: 是否存在且为套接字文件;
   - 文件权限测试:
     - -r FILE: 是否存在且可读
     - -w FILE:是否存在且可写
     - -x FILE: 是否存在且可执行
   - 文件特殊权限测试:
     - -g FILE: 是否存在且拥有sgid权限;
     - -u FILE: 是否存在且拥有suid权限;
     - -k FILE: 是否存在且拥有sticky权限;
   - 文件大小测试:
     - -s FILE: 是否存且非空: 
   - 文件是否打开:
     - -t fd: fd表示文件描述符是否已经打开且与某终端相关
     - -N FILE: 文件自动上一次被读取之后是否被修改过;
     - -O FILE: 当前有效用户是否为文件属主: 
     - -G FILE: 当前有效用户是否为文件属组:
   - 双目测试
     - FILE1 -ef FILE2 : FILE1与FILE2是否指向同一个设备上的相同inode
     - FILE1 -nt FILE2 : FILE1是否新于FILE2或者FILE1存在FILE2不存在
     - FILE1 -ot FILE2 : FILE1是否旧于FILE2或者FILE2存在FILE1不存在

###### ③. 组合测试条件

逻辑运算:

1. 第一种方式:
   COMMAND1 && COmMAND2
   COMMAND1 || COMMAND2
   ! COMMAND

   ```shell
   #文件存在并且可读
   [ -e FILE ] && [-r  FILE]
   ```

2. 第二种方式:
   EXPRESSION1 -a EXPRESSION2
   EXPRESSION1 -o EXPRESSION2
   ! EXPRESSION

   必须使用测试命令进行:

   ```shell
   [ -z"$hostName" -o "$hosName"=="localhost . localdomain"] && hostname www.magedu.com
   [ -z "$HOSTNAME" -o "$HOSTNAME"="centos-7.shared" ]
   [ -f /bin/cat -a -x /bin/cat] && cat /etc/fstab
   ```

   

##### 6) 自定义退出状态码

###### ①. 定义方式

​	exit [n]:自定义退出状态码;

###### ②.注意事项

1. 脚本中一且遇到exit命令，脚本会立即终止:终止退出状态取决于exit命令后面的数字:
2. 如果未给脚本指定退出状态码，整个脚本的退出状态码取决于脚本中执行的最后一条命令的状态码;

###### ③. 练习

写一个脚本
接受一个文件路径作为参数;
如果参数个数小于1，则提示用户“至少应该给一个参数”，并立即退出:
如果参数个数不小于1，则显示第一个参数所指向的文件中的空白行数:

```shell
#! /bin/bash
([ $# -ge 1 ] || (echo "至少一个参数" && exit 1)) && (grep '^[[:space:]]*$' $1 | wc -l)
```

#### (11) vim 编辑器

##### 1) 基本模式:

1. 编辑模式

   也称之为:命令模式, vim进去就是这个模式

2. 输入模式

3. 末行模式:
   内置的命令行接口

##### 2) vim 打开文件方式

​	vim [OPTIoN]... FILE...

- +#: 打开文件后，直接让光标处于第#行的行首:
- +/PATTERN: 打开文件后，直接让光标处于第一个被PATTERN匹配到的行的行首;
- \+ : 打开文件后，光标处于最后一行行首。

##### 3) 模式转换: 

###### ①. 编辑模式-->输入模式

- i:  insert, 在光标所在处输入;
- a: append, 在光标所在处后面输入:
- o: open, 在当前光标所在行的下方打开一个新行:
- I: 在当前光标所在行的行首输入:
- A: 在当前光标所在行的行尾输入;
- O: 在当前光标所在行的上方打开一个新行:
  C
  C

###### ②. 输入模式--> 编辑模式

​	ESC

###### ③. 编辑模式-->末行模式

​	:

###### ④. 末行模式--> 编辑模式

​	ESC

##### 4) 关闭文件:

- :q退出

- :q!强制退出，丢弃做出的修改;

- :wq 强制性写入文件并退出。即使文件没有被修改也强制写入，并更新文件的atime,mtime,ctime。

- :x  写入文件并退出。仅当文件被修改时才写入，并更新文件修改时间，否则不会更新文件修改时间,只更新文件的访问时间(actime)。

- :w /PATH/TO/SOMEWHERE : 把选中的行写到指定位置

  ```shell
  #把第一行到第五行的内容输出到test_b.txt中
  :1,5w ./test_b.txt
  ```

- ZZ: 这个是在**编辑模式**下的保存退出

##### 5) 光标跳转:

###### ①. 字符司跳转;

- h:左

- l:右

- j;下

- k:上

  记忆：韩国(Korea)在日本(Japan)上面,k 向上，j向下

**#COMMAND: 跳转由#指定的个数的字符:** 

###### ②. 单词间跳转:

- w: 下一个单词的词首
- e: 当前或下一单词的词尾
- b: 当前或前一个单词的词首

**\#COMMAND:由#指定一次跳转的单词数**

###### ③. 行首行尾跳转:

- ^: 跳转至行首的第一个非空白字符:
- 0: 跳转至行首; 
- $: 跳转至行尾;

###### ④. 行间移动:

- \#G;跳转至由#指定行

- G:最后一行:

- 1G 或者 gg: 第一行;

  

##### 6) 删除命令;

​	d:删除命令，可结合**光标跳转字符**，实现范围删除:

- d$: 删除到行尾
- d^: 删除到行首不是空白字符处。，
- d0: 删除到行首
- dw: 删除后一个单词开始处
- de: 删除到当前单词或者下一个单词词尾
- db: 删除到当前单词或者上一个单词词首 

**\#COMMAND: 由#指定一次删除的数**

- dd:删除光标所在的行;

  **\#dd: 多行删除;**

##### 7) 粘贴命令(p, put, paste)

- p: 缓冲区存的如果为整行，则粘贴当前光标所在行的下方:否则，则粘贴至当前光标所在处的后面;
- P: 缓冲区存的如果为整行，则粘贴当前光标所在行的上方:否则，则粘贴至当前光标所在处的前面:

##### 8) 复制命令(y, yank):

​	y:复制，工作行为相似于d命令: 

- y$ : 复制到行尾
- y0 : 复制到行首
- y^: 复制到行首不是空白字符
- ye:
- yw:
- yb:

**\#Command: 由#指定一次复制的数**

- yy:复制行

  **\#yy:复制多行;**

##### 9) 替换命令(r, replace)

r: 替换光标所在处的字符

##### 10) 改变命令(c，change)

​	c: 修改,与d类似，只是c删除后有编辑模式--> 输入模式

- c$: 删除到行尾,结束后进入输入模式
- c^: 
- c0:
- cb:
- ce:
- Cw:

**\#COMMAND: 由#指定一次删除的数**

- cc: 删除并输入新内容

  **\#cc: 删除多行**

##### 11)其它编辑操作

###### ①. 可视化模式:

- v:按字符选定
- V:按行行定
  Note:经常结合编辑命令: d, c , y

###### ②. 撒消此前的编辑;

- u(undo):撤消此前的操作;

**\#u:撤消指定次数的操作;**

###### ③. 撤消此前的撤消:

​	Ctrl+r

###### ④. 重复前一个编辑操作;

​	.

###### ⑤. 翻异操作:

- Ctrl+f:向文件尾部翻一屏;
- Ctrl+b:向文件首部翻一屏;
- Ctrl+d:向文件尾部翻半屏;
- Ctrl+u:向文件首部翻半屏;

###### ⑥. vim自带的练习教程:

- vimtutor

##### 12) vim中的末行模式:

内建的命令行接口

###### ①. 地址定界

​	:start_ pos ,end pos

- \ #: 具体第#行，例如2表示第2行:

- \#,#: 从左侧#表示行起始，到右侧#表示行结尾;

- \#,+#:从左侧#表示的行起始，加上右侧#表示的行数:

- . :当前行

- \$:最后一行 

  \.​ , \$-1: 从当前行到倒数第一行

- %;全文，相当于1,$

- /pat1/,/pat2/: 从第次被pat1模式匹配到的行开始，一直到第一次被pat2匹配到的行结束:

  - \#,/pat/ : 从第#行到第一次匹配到的行

  - /pat/,$ ： 从第一次匹配到的行到文件末行

**使用方式:** 
后跟一个编辑命令

- \#1,#2d : 删除#1行到#2行
- ., #y: 从当前行复制到#行
- \#1,#2w /PATH/TO/SOMEWHERE: 将范围内的行另存至指定文件中
- \#r /PATH/FROM/SOMEFILE: 在#位置插入指定文件中的所有内容;
- r /PATH/FROM/SOMEFILE: 在光标下一行插入指定文件中的内容

###### ②. 查找

- /PATTERN:从当前光标所在处向文件尾部查找;
- ?PATTERN:从当前光标所在处向文件首部查找:
  n:与命令同方向;
  N: 与命令反方向;

###### ③. 查找并替换

- s:在末行模式下完成查找替换操作
  s/要查找的内容/替换为的内容/修饰符

  - 要查找的内容:可使用模式

  - 替换为的内容:不能使用模式，但可以使用\1, \2, ...等后 向引用符号:

    还可以使用“&”引用前面查找时查找到的整个内容;

  - 修饰符:

    - i:忽略大小写
    - g:全局替换:默认情况下，每一行只替换第一次出现:

  - 查找替换中的分隔符/可替换为其它字符，例如
    s@@@ 
    s###

    ```shell
    :%s@^e@E@g
    ```

###### ④. 练习:

1. 复制/etc/grub2.cfg至/tmp/目录， 用查找替换命令删除/tmp/grub2.cfg文件中的行首的空白字符;

   ```shell
   :%s/^[[:space:]]\+//g
   ```

2. 复制/etc/rc .d/init. d/functions文件至/tmp目录，用查的替换命令为/tmp/functions的每行开头为空白字符的行的行首添加一个#号: 

   ```shell
   :%s/^[[:space:]]\+/#/g
   ```


##### 13) 多文件模式:

​	vim FILE1 FILE2 FILE3

- : next 下一个
- : prev 前一个
- :first 第一个
- :last最后一个
- :wall 或者wa 保存所有
- :qall 或者qa 退出所有

##### 14) 窗口分隔模式r

​	vim -o|-O FILE1 FILE2

- -o: 水平分割
- -O: 垂宜分割
- 在窗口间切换: Ctrl+w, Arrow(上下左右箭头)：

##### 15) 单文件窗ロ分割;

- : sp, 水平分割
- : vsp, 垂直分割

##### 16) 定制vim的工作特性:

​	配置文件:永久有效

- 全局: /etc/vimrc

- 个人: ~/.vimrc

  末行:当前vim进程有效

###### ①. 行号

- 显示: set number,简写为set nu
- 取消显示: set nonumber, 简写为set nonu

###### ②. 括号匹配

- 匹配: set showmatch, 简写为set sm 
- 取消: set nosm

###### ③. 自动缩进

- 启用: set ai
- 禁用: set noai

###### ④. 高亮搜索

- 启用: set hlsearch
- 禁用: set nohlsearch

###### ⑤. 语法高亮

- 启用: syntax on
- 禁用: syntax off

###### ⑥. 忽略字符的大小写

- 启用: set ic:
- 不忽略: set noic

##### 17) 获取帮助: 

- :help
- :help subject

##### 18) 练习:

1. 如何设置tab缩进为4个字符?

   ```shell
   set tabstop=4
   ```

2. 复制/etc/rc .d/init .d/ functions文件至/tmp目录:替换/tmp/functions文件中的/etc/sysconfig/init为/var/log

   ```shell
   :%s/\/etc\/sysconfig\/init/\var\/log/g
   ```

3. 删除/tmp/functions文件中所有以#开头，且#后 面至少有一个空白字符的行的行首的#号:

   ```
   :%s/^#\([[:space:]]\+\)/\1/g
   ```

#### (12) 文件查找

在文件系統.上査找符合条件的文件;
文件査找: locate, find

- 非实时査找(数据库査找): locate
- 实时査找: find

##### 1) locate:

依赖于事先构建的索引:索引的构建是在系统较为空闲时自动进行(周期性任务);手动更新数据库(updatedb);
索引构建过程需要遍历整个根文件系统，极消耗资源: 

1. 工作特点:
   - 査找速度快:
   - 模糊査找;
   - 非实时査找:
2. locate KEYWORD 查找

##### 2) find

###### ①. 实时查找工具

​	通过遍历指定路径下的文件系统完成文件查找:

###### ②. 工作特点:

- 查找速度略慢; 
- 精确查找;
- 实时查找;

###### ③. 语法:

find [OPTION]... [查找路径] [查找条件] [处理动作]

- 查找路径:指定具体目标路径;默认为当前目录:
- 查找条件:指定的查找标准，可以文件名、大小、类型、权限等标准进行:默认为找出指定路径下的所有文件:
- 处理动作:对符合条件的文件做什么操作;默认输出至屏幕;

###### ④. 查找条件:

1. 根据文件名查找:

   - -name“文件名称":支持使用glob
     ?, [], [^]
   - -iname “文件名称":不区分字母大小写
   - -regex "PATTERN": 以PATTERN匹配整个文件路径字符串，而不仅仅是文件名称:

2. 根据属主、属组查找:

   - -user USERNAME:查找属主为指定用户的文件: 
   - -group GRPNAME:查找属组为指定组的文件:
   - -uid UserID; 查找属主为指定的UID号的文件:
   - -gid GroupID: 查找属组为指定的GID号的文件;
   - -nouser:查找没有属主的文件
   - -nogroup:查找没有属组的文件:

3. 根据文件类型查找:
   -type TYPE:

   - f: 普通文件

   - d: 目录文件

   - l:  符号链接文件

   - s: 套接字文件

   - b: 块设备文件

   - c: 字符设备文件

   - p: 管道文件

   - ```shell
     find /home -type s -ls
     ```

4. 组合条件

   - 与 : -a
   - 或: -o
   - 非: -not, !

   **摩根定理:**

   ```
   !A -a !B = !(A -o B)
   !A -o !B = !(A -a B)
   ```

   找出/tmp目录下,属主不是root, 且文件名不是fstab的文件

   ```shell
   find /tmp \( -not -user "root" -a -not -name "fstab" \) -ls
   find /tmp \( ! -user "root" -a ! -name "fstab" \) -ls
   find /tmp -not \( -user "root" -o -name "fstab" \) -ls
   find /tmp ! \( -user "root" -o -name "fstab" \) -ls
   ```

   