### 一. linux磁盘管理

​	I/O Ports:  I/O设备地址;

#### 1). 一切皆文件 

​	open()，read(), write(), close()

##### (1)设备类型

- 块设备: block, 存取单位“块” :磁盘
- 字符设备: char, 存取单位“字符” :键盘

##### (2). 设备文件:

​	关联至一个设备驱动程序，进而能够跟与之对应硬件设备进行通信;

##### (3). 设备号码

- 主设备号: major number,标识设备类型
- 次设备号: minor number, 标识同一类型下的不同设备

##### (4). 硬盘接口类型:

- 并行
  - IDE: 133MB/s
  - SCSI: 640MB/s 
- 串口:
  - SATA: 6Gbps
  - SAS: 6Gbps
  - USB: 480MB/s

##### (5). 磁盘设备的设备文件命名:

 /dev/DEV_ FILE

- IDE: /dev/hd
- SCSI, SATA, SAS, USB: /dev/sd
  - 不同设备: a-z
    /dev/sda, /dev/sdb, ...
  - 同一设备上的不同分区; 1,2, ....
    /dev/sda1, /dev/sda5