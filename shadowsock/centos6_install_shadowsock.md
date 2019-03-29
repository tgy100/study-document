### 1)shadowsock安装

#### (1) 安装ShadowSocks

```shell
yum -y install python-setuptools && easy_install pip 
pip install shadowsocks
```

#### (2)创建配置文件

​	/etc/shadowsocks.json

```
{ 
"server":"138.128.208.158", 
"server_port":443, 
"local_address": "127.0.0.1", 
"local_port":1080, 
"password":"MyPass", 
"timeout":600, 
"method":"aes-256-gcm"
}
```

##### ①.多用户

​	把 "password" 改为下面的即可，前面端口，后面密码

```
"port_password":{
 "9001":"10001",
 "9002":"10002",
 "9003":"10003"
 },
```

备注：加密方式官方默认使用aes-256-cfb，最强的选择是AEAD密码。推荐的选择是“chacha20-ietf-poly1305”或“aes-256-gcm”。

##### ②.各字段说明：

- server:服务器IP
- server_port:服务器端口
- local_port:本地端端口
- password:用来加密的密码
- timeout:超时时间（秒）
- method:加密方法，可选择 “bf-cfb”, “aes-256-cfb”, “des-cfb”, “rc4″等

#### (3) 启动shadowsocks服务

```shell
[root@localhost /]# ssserver -c /etc/shadowsocks.json -d start
```

**备注：若无配置文件，在后台可以使用以下命令运行：**

```shell
[root@localhost /]# ssserver -p 443 -k MyPass -m rc4-md5 -d start
```

#### (4) 停止shadowsocks服务

```shell
[root@localhost /]# ssserver -c /etc/shadowsocks.json -d stop
```



### 2)python2.6 get-pip.py 报错误

```python
_blocking_errnos = {errno.EAGAIN, errno.EWOULDBLOCK} pip
```

解决方案：

#### (1)从官方github获取具体版本的pip

```shell
wget  https://raw.githubusercontent.com/pypa/get-pip/master/2.6/get-pip.py
```

#### (2)运行刚才获取的pip文件

```python
 python get-pip.py
```

