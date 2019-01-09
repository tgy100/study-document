### 一.linux的文件系统:

根文件系统(rootfs):root file system

LSB, FHS: (FileSystem Heirache Standard)
/etc, /usr, /var, /root, /home, /dev

#### (1) /boot:

​	引导文件存放目录，内核文件(vmlinuz)、引导加载器(bootloader, grub)都存放于此目录:

#### (2) /bin:

​	供所有用户使用的基本命令:不能关联至独立分区,OS启动即会用到的程序	

#### (3) /sbin

​	管理类的基本命令:不能关联至独立分区,OS启动即会用到的程序:

#### (4) /lib

​	基本共享库文件，以及内核模块文件(/lib/modules);

#### (5) /lib64

​	专用于x86 64系统上的辅助共享库文件存放位置: 

#### (6) /etc

​	配置文件目录(纯文本文件);

​	/etc/man.config: man命令查找路径的配置文件

#### (7) /home/USERNAME

​	普通用户家目录:

#### (8) /root:

​	管理员的家目录

#### (9) /media

​	便携式移动设备挂载点: cdrom,usb

#### (10) /mnt:

​	临时文件系统挂载点;

#### (11) /dev

 	设备文件及特殊文件存储位置:
​	1.b: block device, 随机访问
​	2.c: character device, 线性访问

#### (12) /opt

​	第三方应用程序的安装位置;

#### (13) /srv 

​	系统上运行的服务用到的数据: 

#### (14) /tmp 

​	临时文件存储位置:

#### (15) /usr: 

​    universal shared, read -only data;

- bin:保证系统拥有完整功能而提供的应用程序:
- sbin:
- lib: 
- lib64:
- include: C程序的头文件(header files);
- share:结构化独立的数据，例如doc, man等;
- local:第三方应用程序的安装位置:
  里面有: bin, sbin, lib, lib64, etc, share

#### (16) /var

​	variable data files

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

​	用于输出内核与进程信息相关的虚拟文件系统:

#### (18) /sys:

​	用于输出当前系统上硬件设备相关信息的虚拟文件系统;

#### (19) /selinux: 

​	security enhanced Linux, selinux相关的安 全策略等信息的存储位置:

#### (20)Linux上的应用程序的组成部分:

##### 1)二进制程序: 

​	/bin, /sbin, /usr/bin, /usr/sbin, /usr/local/bin, /usr/local/sbin

##### 2)库文件: 

​	/lib, /lib64, /usr/lib, /usr/lib64, /usr/local/lib, /usr/local/lib64

##### 3)配置文件: 

​	/etc, /etc/DIRECTORY, /usr/local/etc

##### 4)帮助文件: 

​	/usr/share/man, /usr/ share/doc, /usr/local/ share/man, /usr/local/ share/doc

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

​	halt, poweroff, shutdown, init 0

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

​	reboot, shutdown, init 6

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

- HISTFILE:  ~/.bash_ history;
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

- {}: 可承载一个以逗号分隔的列表，并将其展开为多个路径
  /tmp/{a,b} 生成为: /tmp/a, /tmp/b
  /tmp/{tom, jerry}/hi 生成为: /tmp/tom/hi, /tmp/jerry/hi

  **练习:**

  1. 如何创建: /tmp/x/y1, /tmp/x/y2, /tmp/x/y1/a, /tmp/x/y1/b, /tmp/x/y2/a, /tmp/x/y2/b
     ​	mkdir -p /tmp/x/{y1,y2}/{a,b}
  2.  如何创建 : x_m, y_m, x_n, y_n
     ​	mkdir -p  {x,y}_{m,n}
  3. 如何创建 :/tmp/bin, /tmp/sbin, /tmp/usr, /tmp/usr/bin, /tmp/usr/sbin 
     ​	mkdir -p /tmp/{bin, sbin, usr/{bin, sbin}}

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
   注意:在命令行中定义的别名，仗对当前shel1进程有效;如果想永久有效，要定义在配置文件中:
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

  -  --output-delimiter=<字段分隔符>：指定输出内容时的字段分割符

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

​	以冒号分隔，取出/etc/passwd文件 的第6至第10行，并将这些信息按第3个字段的数值大小进行排序:最后仅显示的各自的第1个字段:

```shell
sed -e '/^#/d' passwd |head -n 10 | tail -n 5 | sort -t':' -k 3 -n | cut -d':' -f1
```



#### (3)录管理类命令;

cd, pwd, ls
mkdir, rmdir, tree

##### 1)mkdir 

​	[ options ] /path/ to/ somewhere

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

​	more [OPTIONS...] FILE...

- -d: 显示翻页及退出提示

##### 2) less

​	less [OPTIONS..] FILE

##### 3) head

​	head [OPTION].. [FILE]

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

​	cp, mv, rm

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
  ​	Audition

##### 2) 用户登录系统的方式

- token ： token授权
- identity(username/pas sword)：用户密码

##### 3) Linux用户: 

​	Username(用户名)/UID(用户对应的ID)

- 管理员: root, 0 

- 普通用户: 1-65535

  - 系统用户: 1-499 

    对守护进程获取资源进行权限分配;

  - 登录用户:500+
    交互式登录: 

##### 4) Linux组:

​	 Grougname/GID

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
  ​	组名同用户名，且仅包含一个用户:私有组
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

  ​	000 0
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