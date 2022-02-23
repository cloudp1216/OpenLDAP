

## 一、OpenLDAP简介
#### 1. 概述
&emsp;&emsp; LDAP是一款轻量级目录访问协议（Lightweight Directory Access Protocol，简称LDAP），属于开源集中账号管理架构的实现，且支持众多系统版本，被广大互联网公司所采用。</p>
&emsp;&emsp; LDAP提供并实现目录服务的信息服务，目录服务是一种特殊的数据库系统，对于数据的读取、浏览、搜索有很好的效果。目录服务一般用来包含基于属性的描述性信息并支持精细复杂的过滤功能，但OpenLDAP目录服务不支持通用数据库的大量更新操作所需要的复杂的事务管理或回滚策略等。</p>
&emsp;&emsp; LDAP具有两个标准，分别是X.500和LDAP。OpenLDAP是基于X.500标准的，而且去除了X.500复杂的功能并且可以根据自我需求定制额外扩展功能，但与X.500也有不同之处，例如OpenLDAP支持TCP/IP协议等，目前TCP/IP是Internet上访问互联网的协议。</p>
&emsp;&emsp; OpenLDAP可以直接运行在更简单和更通用的TCP/IP或其他可靠的传输协议层上，避免了在OSI会话层和表示层的开销，使连接的建立和包的处理更简单、更快，对于互联网和企业网应用更理想。</p>
&emsp;&emsp; OpenLDAP目录中的信息是以树状的层次结构来存储数据（这很类同于DNS），最顶层即根部称作“基准DN”，形如“dc=speech,dc=local”类似于Windows AD中使用的方式。在根目录的下面有很多的文件和目录，为了把这些大量的数据从逻辑上分开，OpenLDAP像其它的目录服务协议一样使用OU（Organization Unit，组织单元），可以用来表示公司内部机构，如部门等，也可以用来表示设备、人员等。同时OU还可以有子OU，用来表示更为细致的分类。</p>

#### 2. OpenLDAP相关概念
- schema：主要用于控制目录树中各种条目的对象类以及各种属性的定义，并通过内部规范机制限定目录树条目所遵循的逻辑结构以及定义规范，保证整个目录树没有非法条目数据，避免不合法的条目存在目录树中，从而保证整个目录树信息的完整性、唯一性。（objectClass和Attribute由schema文件来规定）。

- objectClass：在OpenLDAP目录树中，每个条目必须包含一个属于自身条件的对象类（objectClass），然后再定义其条目属性及对应的值；OpenLDAP条目的属性能否添加取决于条目所继承的objectClass是否包含此属性，objectClass具有继承关系。

- Attribute：属性在目录树中主要用于描述条目相关信息，常用的属性包括：

| 属性 | 描述 |
| ------------ | ------------ |
| dn  | 唯一标识名 |
| rdn | 相对标识名  |
| uid | 一个用户的登录名称 |
| sn  | 一个人的姓氏 |
| giveName | 一个人的名字 |
| objectClass | 特殊属性，包含数据存储方式及相关属性信息 |
| dc | 一个域名 |
| cn | 一个对象的名称 |

- LDIF：轻量级目录访问协议数据交换格式的简称，是存储LDAP配置信息及目录内容的标准文本文件格式，通常在OpenLDAP服务器之间相互交换数据。


## 二、编译安装OpenLDAP
#### 1. 从官网下载OpenLDAP源码包，访问链接如下:
http://www.openldap.org/software/download/OpenLDAP/openldap-release/ </p>

#### 2. 安装相关依赖
```shell
[root@ldap ~]# yum install -y libtool-ltdl libtool-ltdl-devel openssl-devel
```

#### 3. 编译安装OpenLDAP
```shell
[root@ldap ~]# tar zxf openldap-2.4.44.tgz
[root@ldap ~]# cd openldap-2.4.44
[root@ldap openldap-2.4.44]# ./configure --prefix=/usr/local/openldap \
    --enable-bdb=no \
    --enable-hdb=no \
    --enable-ldap=yes \
    --enable-mdb=yes \
    --enable-monitor=no \
    --enable-accesslog \
    --enable-auditlog \
    --enable-syslog \
    --enable-modules \
    --enable-debug \
    --enable-overlays=yes \
    --enable-dyngroup=mod \
    --enable-dynlist=mod \
    --enable-memberof=mod \
    --enable-ppolicy=mod \
    --enable-crypt \
    --with-tls \

[root@ldap openldap-2.4.44]# make depend
[root@ldap openldap-2.4.44]# make 
[root@ldap openldap-2.4.44]# make install
```


## 三、添加OpenLDAP第三方密码复杂度检测插件
#### 1. 相关文档及下载地址
http://www.meddeb.net/pqchecker/ </p>
https://bitbucket.org/ameddeb/pqchecker/downloads/?tab=tags </p>

#### 2. 编译安装扩展插件
```shell
[root@ldap ~]# tar zxf ameddeb-pqchecker-e013630479bc.tar.gz
[root@ldap ~]# cd ameddeb-pqchecker-e013630479bc
[root@ldap ameddeb-pqchecker-e013630479bc]# ./configure \
    LDAPSRC=/root/openldap/openldap-2.4.44 \
    JAVAHOME=/usr/local/jdk1.8.0_251 \
    libdir=/usr/local/openldap/libexec/openldap \
    PARAMDIR=/usr/local/openldap/etc/openldap \
[root@ldap ameddeb-pqchecker-e013630479bc]# make
[root@ldap ameddeb-pqchecker-e013630479bc]# make install
```


## 四、使用rpm包安装OpenLDAP
#### 1. 使用定制rpm包安装（基于以上源码参数编译打包，包括了一些定制的配置，安装部署非常便捷）
[OpenLDAP-2.4.44-10.el7.x86_64.rpm](./Software/OpenLDAP-2.4.44-10.el7.x86_64.rpm)

#### 2. 安装OpenLDAP服务端
```shell
[root@ldap ~]# rpm -ivh OpenLDAP-2.4.44-10.el7.x86_64.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:OpenLDAP-2.4.44-10.el7           ################################# [100%]
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            3f:d0:7b:83:bb:97:f5:ba:c3:18:df:ce:fe:d9:7e:f8:21:dd:52:7b
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=CN, ST=BeiJing, L=BeiJing, O=LDAP, CN=example.local
        Validity
            Not Before: Jan 13 03:13:00 2022 GMT
            Not After : Feb 12 03:13:00 2022 GMT
        Subject: CN=example.local
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:ba:eb:fd:93:e0:3d:a3:71:55:8b:ec:a9:2d:3b:
                    15:57:ea:63:17:98:96:1b:64:7a:6f:92:f0:3a:ce:
                    dd:a7:47:51:2c:c4:bf:58:64:10:14:a3:3d:3d:47:
                    92:23:20:85:52:c2:10:fc:6e:0a:94:28:2b:12:08:
                    e9:fc:fa:09:79:50:0e:55:98:35:fd:f2:6d:08:1f:
                    34:b1:7f:e8:76:c5:50:c8:96:e7:a7:31:6e:f4:c1:
                    a7:14:c8:d5:79:e1:52:5e:cc:f7:88:13:ff:0b:68:
                    f9:ab:07:fc:c8:56:3b:ad:40:0d:f5:36:01:e0:83:
                    92:49:0e:c7:4e:ef:bc:e3:39:74:a5:3b:56:9d:e1:
                    8c:2c:e4:31:15:d3:80:51:35:90:76:74:0f:39:7b:
                    c7:bd:54:62:6b:68:08:db:0d:cf:4f:2d:21:86:e3:
                    e2:db:2c:99:a4:f9:ee:25:93:96:73:45:c0:f9:6b:
                    1e:58:88:25:53:3f:88:2f:34:89:99:5b:32:f8:b8:
                    56:e8:bf:8a:20:39:c2:06:86:94:e5:23:21:c6:1c:
                    73:cf:a2:65:37:3c:10:d2:8b:1e:c2:bd:f4:fb:c7:
                    05:ba:f5:1b:ac:f7:ea:6e:1e:ce:67:53:2a:e5:9e:
                    ac:58:29:21:84:c0:9f:ac:4d:cb:d6:db:9d:d9:e7:
                    8f:bb
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment
            X509v3 Extended Key Usage: 
                TLS Web Server Authentication, TLS Web Client Authentication
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Subject Key Identifier: 
                AC:11:61:DC:AB:47:94:1B:B5:1F:58:C9:25:FF:4C:36:47:F7:40:01
            X509v3 Authority Key Identifier: 
                keyid:AB:25:45:46:2F:AB:F1:AD:68:D4:49:01:2A:0A:EE:FA:3F:93:19:F4

            X509v3 Subject Alternative Name: 
                DNS:ldaps.example.local
    Signature Algorithm: sha256WithRSAEncryption
         05:0b:ff:35:f4:71:43:eb:92:16:71:96:a0:82:a8:68:62:d2:
         66:fd:fe:48:ed:30:f0:da:e8:48:59:03:ca:bd:1e:fd:01:7e:
         0f:39:1c:d4:fb:67:a2:fd:e7:39:50:4f:31:c0:46:1a:da:d5:
         6f:b0:15:ef:6c:34:19:b9:de:b8:e8:81:ce:8a:04:4b:b5:12:
         49:f7:75:42:94:95:1a:82:8c:b4:98:85:9b:a6:3f:43:79:a0:
         73:ab:b4:bd:ff:cc:85:fb:22:54:d0:87:57:87:4c:6a:36:7c:
         f4:85:d4:65:63:f6:08:ec:da:89:e0:6b:2f:b1:61:73:c8:75:
         23:c2:28:6f:a7:df:54:7a:c8:e3:f1:ca:87:65:cc:e6:63:eb:
         1f:0b:9f:4c:85:8f:05:7c:1b:f7:30:b3:c9:48:96:87:a2:30:
         d5:7e:f1:7d:6c:9c:1a:af:38:2c:12:0e:7c:fb:73:42:43:18:
         34:0d:df:84:6c:c3:0c:b2:5c:fe:e0:a5:83:6d:22:31:71:d9:
         00:bf:48:58:0d:04:e4:b4:a0:09:0b:d9:71:a2:6a:ba:56:0e:
         fe:d5:63:d3:b8:25:ce:73:53:a1:55:7b:39:c8:65:38:60:1d:
         f9:e7:78:a2:57:e2:97:70:0d:4a:e6:8f:7d:44:e5:02:f8:ee:
         7b:e0:2a:41

Starting OpenLDAP (pid: 51963)   [ OK ]

added: "dc=example,dc=local" (00000001)
added: "ou=ppolicy,dc=example,dc=local" (00000002)
added: "cn=defaults,ou=ppolicy,dc=example,dc=local" (00000003)
added: "ou=sudoers,dc=example,dc=local" (00000004)
added: "cn=defaults,ou=sudoers,dc=example,dc=local" (00000005)
_#################### 100.00% eta   none elapsed            none fast!         
Closing DB...
```

#### 3. OpenLDAP自动初始化（使用rpm包安装完OpenLDAP后会自动进行数据初始化，无需操作）
- 生成默认配置文件，位于"/usr/local/openldap/etc/openldap"目录下
- 修改hosts文件，添加"127.0.0.1 &emsp; ldaps.example.local"解析记录（注：默认域名为"ldaps.example.local"）
- 默认域为"dc=example,dc=local"
- 默认密码策略为"cn=defaults,ou=ppolicy,dc=example,dc=local"
- 默认密码复杂度为："0|00010101" （密码需要包含：1位小写字母、1位数字、1位特殊字符）
- 默认sudo配置为"cn=defaults,ou=sudoers,dc=example,dc=local"
- 服务启动为ldaps，监听636端口，证书位于"/usr/local/openldap/etc/openldap/cert"目录下
- 配置文件"/usr/local/openldap/etc/openldap/slapd.conf"定义了默认管理员账号密码，如下：</p>
&emsp;&emsp; 管理员账号：cn=admin,dc=example,dc=local </p>
&emsp;&emsp; 管理员密码：admin  &emsp;&emsp; （加密存储）</p>

#### 4. OpenLDAP服务管理
```shell
[root@ldap ~]# ldapctl
Usage: /usr/sbin/ldapctl {start|stop|status|restart|check|pass|cat}

# ldapctl start             # 启动slapd服务
# ldapctl stop              # 停止slapd服务
# ldapctl status            # 查看slapd服务状态
# ldapctl restart           # 重启slapd服务
# ldapctl check             # 检查slapd配置文件是否正确
# ldapctl pass              # 创建加密密码
# ldapctl cat               # 查看ldap数据，用于数据备份
```

#### 5. 修改OpenLDAP管理员密码
```shell
[root@ldap ~]# ldapctl pass
New password:                                       # 新密码
Re-enter new password:                              # 重复新密码
{SSHA}OpdOgZEAgrBb4olpbSwSOTPEW7Q/4Myq              # 加密后的密码

[root@ldap ~]# vi /usr/local/openldap/etc/openldap/slapd.conf     # 修改配置文件
rootdn      "cn=admin,dc=example,dc=local"
rootpw      {SSHA}OpdOgZEAgrBb4olpbSwSOTPEW7Q/4Myq                # 将rootpw值进行替换

[root@ldap ~]# ldapctl restart                                    # 重启openldap服务
Stopping OpenLDAP (pid: 26939)   [ OK ]
Starting OpenLDAP (pid: 27019)   [ OK ]
```

#### 6. 调整密码复杂度（按需调整）
```shell
[root@ldap ~]# vi /usr/local/openldap/etc/openldap/pqparams.dat 
# Data format: 0|UULLDDSS@)..
# Or         : 1|UULLDDSS@)..
#
# 1st character is the modified passwords broadcast flag. 1 -> Broadcast, 0 -> Don't broadcast
# 2nd character is a separator
# U: Uppercase, L: Lowercase, D: Digit, S: Special characters -> from 3rd to 10th charater.
# From the 11th character begins the list of forbidden characters
# Defaulti: No broadcast, 1 Uppercase, 1 Lowercase, 1 digit, 1 Special and no forbidden characters
0|00010101
```
```shell
0|00000000  共10位
     第1,2位默认使用"0|"
     第3,4位表示密码必须包含几位大写字母，01包含1位、02则包含2位
     第5,6位表示密码必须包含几位小写字母
     第7,8位表示密码必须包含几位数字
     第9,10位表示密码必须包含几位特殊字符
     例如：
      0|01010101       表示用户密码必须包含1位大写字母，1位小写字母，1位数字和1位特殊字符
      0|03020100       表示用户密码必须包含3位大写字母，2位小写字母和1位数字
```


## 五、基于CentOS7的客户端安装
#### 1. 安装openldap客户端工具
```shell
[root@local ~]# yum install openldap-clients
```

#### 2. 复制服务端证书"/usr/local/openldap/etc/openldap/certs/ca.pem"到客户端"/etc/openldap/certs/"目录下
```shell
[root@local ~]# ls -lh /etc/openldap/certs/ca.pem 
-rw-r--r-- 1 root root 1.3K Jan 13  2022 /etc/openldap/certs/ca.pem
```

#### 3. 修改openldap客户端配置文件
```shell
[root@local ~]# vi /etc/openldap/ldap.conf   # 追加以下内容
URI ldaps://ldaps.example.local
BASE dc=example,dc=local
TLS_CACERT /etc/openldap/certs/ca.pem
TLS_REQCERT allow
SUDOERS_BASE ou=sudoers,dc=example,dc=local
```

#### 4. 使用命令`ldapsearch -x`能够查询到相关服务端信息
```shell
[root@local ~]# ldapsearch -x
# extended LDIF
#
# LDAPv3
# base <dc=example,dc=local> (default) with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# example.local
dn: dc=example,dc=local
...
```

#### 5. 安装配置nslcd服务
```shell
[root@local ~]# yum install nss-pam-ldapd
[root@local ~]# vi /etc/nslcd.conf           # 修改以下内容为
uri ldaps://ldaps.example.local
base dc=example,dc=local
tls_reqcert allow
tls_cacertfile /etc/openldap/certs/ca.pem
```
``` shell
[root@local ~]# systemctl restart nslcd
[root@local ~]# systemctl enable nslcd
```

#### 6. 修改"/etc/nsswitch.conf"文件
```shell
[root@local ~]# cp /etc/nsswitch.conf /etc/nsswitch.conf.old
[root@local ~]# vi /etc/nsswitch.conf       # 修以下三项内容为
passwd:     files ldap
shadow:     files ldap
group:      files ldap
```
```shell
sudoers:    files ldap                      # 在最后追加，sudoers需要此项
```

#### 7. 修改"etc/pam.d/system-auth"和"/etc/pam.d/password-auth"文件
```shell
[root@local ~]# vi /etc/pam.d/system-auth && vi /etc/pam.d/password-auth
auth        sufficient    pam_ldap.so use_first_pass                  # 在auth项的-2行插入
account     [success=ok user_unknown=ignore default=bad] pam_ldap.so  # 在account项的-2行插入
password    sufficient    pam_ldap.so use_autook                      # 在password项的-2行插入
session     optional      pam_ldap.so                                 # 在session项的-1行插入
session     optional      pam_mkhomedir.so                            # 在session项的-1行插入，用户第一次登录会自动创建home
```

#### 8. 备份"/etc/sudo-ldap.conf"文件并创建到"/etc/openldap/ldap.conf"的链接
```shell
[root@local ~]# mv /etc/sudo-ldap.conf /etc/sudo-ldap.conf.old
[root@local ~]# ln -sv /etc/openldap/ldap.conf /etc/sudo-ldap.conf
```


## 六、基于Ubuntu16、Ubuntu18、Debian9、Debian10的客户端安装
#### 1. 安装openldap客户端工具
```shell
root@local:~# apt install ldap-utils
```

#### 2. 复制服务端证书"/usr/local/openldap/etc/openldap/cert/ca.crt"到客户端"/etc/ssl/certs/"目录下
```shell
root@local:~# mkdir -p /etc/ldap/certs
root@local:~# ls -lh /etc/ldap/certs/ca.pem 
-rw-r--r-- 1 root root 1.3K Jan 12 22:18 /etc/ldap/certs/ca.pem
```

#### 3. 修改openldap客户端配置文件
```shell
root@local:~# vi /etc/ldap/ldap.conf
URI ldaps://ldaps.example.local
BASE dc=example,dc=local
TLS_CACERT /etc/ldap/certs/ca.pem
TLS_REQCERT allow
SUDOERS_BASE ou=sudoers,dc=example,dc=local
```

#### 4、使用命令`ldapsearch -x`能够查询到相关服务端信息
```shell
root@local:~# ldapsearch -x
# extended LDIF
#
# LDAPv3
# base <dc=example,dc=local> (default) with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# example.local
dn: dc=example,dc=local
...
```

#### 5. 安装软件包libpam-ldapd和libnss-ldapd，安装完后修改"/etc/nslcd.conf"指定证书文件位置并重启服务
```shell
root@local:~# apt install libpam-ldapd libnss-ldapd
```
![](./img/ldap-1.jpg)
![](./img/ldap-2.jpg)
![](./img/ldap-3.jpg)
![](./img/ldap-4.jpg)


```shell
# vim /etc/nslcd.conf
tls_cacertfile /etc/ssl/certs/ca.crt      # 修改此项，指定ca.crt证书

# systemctl restart nslcd                 # 重启nslcd服务

# systemctl stop nscd                     # 关闭nscd服务（如果存在）
# systemctl disable nscd
```

**6、修改“/etc/nsswitch.conf”文件**
```shell
# vim /etc/nsswitch.conf       # 修以下三项为
passwd:     files ldap
shadow:     files ldap
group:      files ldap
```
```shell
sudoers:    files ldap         # 在最后添加，sudoers需要此项
```

**7、修改“/etc/pam.d/common-session”文件**
```shell
# vim /etc/pam.d/common-session
session optional        pam_mkhomedir.so   # 在最后行添加，用户第一次登录会自动创建home
```

**8、安装sudo-ldap**
```shell
# apt install sudo-ldap
```


