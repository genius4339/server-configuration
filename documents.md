#  1.CentOS7 基础配置安装

## 1.1 服务器的初始化安装

* 安装`net-tools`后方可执行`ifconfig`查看本机ip情况

<pre>
  yum install net-tools
</pre>
 
* 安装其他一些基本工具

<pre>
  yum -y install sudo
</pre>
 
* ssh相关操作

<pre>
  mkdir -p /root/.ssh/
  echo ''  >> /root/.ssh/authorized_keys
  ssh-keygen -t rsa -C "your_email@example.com"
</pre>

# 2. LNMP环境搭建

## 2.1 MySQL安装

总所周知，MySQL 被 Oracle 收购后，CentOS 的镜像仓库中提供的默认的数据库也变为了 MariaDB

* 若要删除默认MariaDB的相关安装，可执行以下命令：

<pre>
  yum remove -y mariadb-libs
</pre>

* 添加 MySQL YUM 源 （mysql5.7）

<pre>
  wget 'https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm'
  sudo rpm -Uvh mysql57-community-release-el7-11.noarch.rpm
  yum repolist all | grep mysql
  mysql-connectors-community/x86_64 MySQL Connectors Community                  36
  mysql-tools-community/x86_64      MySQL Tools Community                       47
  mysql57-community/x86_64          MySQL 5.7 Community Server                 187
</pre>

* 如果想安装最新版本的，直接使用 yum 命令即可

<pre>
  yum install mysql-community-server
</pre>

* 如果想要安装 5.6 版本的，有2个方法。命令行支持 yum-config-manager 命令的话，可以使用如下命令：

<pre>
  $ sudo dnf config-manager --disable mysql57-community
  $ sudo dnf config-manager --enable mysql56-community
  $ yum repolist | grep mysql
  mysql-connectors-community/x86_64 MySQL Connectors Community                  36
  mysql-tools-community/x86_64      MySQL Tools Community                       47
  mysql56-community/x86_64          MySQL 5.6 Community Server                 327
</pre>

* 或者直接修改`/etc/yum.repos.d/mysql-community.repo`这个文件

<pre>
  [mysql56-community]
  name=MySQL 5.6 Community Server
  baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/7/$basearch/
  enabled=1 #表示当前版本是安装
  gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
  [mysql57-community]
  name=MySQL 5.7 Community Server
  baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/7/$basearch/
  enabled=0 #默认这个是 1
  gpgcheck=1
  gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
</pre>


* 通过设置 enabled 来决定安装哪个版本， 设置好之后使用 yum 安装即可。


# 3.CentOS7 搭建ShadowSocks

pip是 python 的包管理工具。将使用 python 版本的 shadowsocks，此版本的 shadowsocks 已发布到 pip 上，因此我们需要通过 pip 命令来安装。

<pre>
  curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"
  python get-pip.py
</pre>

* 安装配置 shadowsocks

<pre>
  pip install --upgrade pip
  pip install shadowsocks
</pre>

* 安装完成后，需要创建配置文件/etc/shadowsocks.json，内容如下：

<pre>
  {
    "server": "0.0.0.0",
    "server_port": 8388,
    "password": "u1zon57jd0v869t7w",
    "method": "aes-256-cfb"
  }
</pre>  

**说明**

* method为加密方法，可选`aes-128-cfb`、`aes-192-cfb`、`aes-256-cfb`、`bf-cfb`、 `cast5-cfb`、`des-cfb`、`rc4-md5`、`chacha20`、`salsa20`、`rc4`、`table`
* server_port为服务监听端口
* password为密码，可使用[密码生成工具](http://ucdok.com/project/generate_password.html)生成一个随机密码


**配置自动启动项**

* 新建启动脚本文件`/etc/systemd/system/shadowsocks.service`，内容如下：

<pre>
  [Unit]
  Description=ShadowSocks
  [Service]
  TimeoutStartSec=0
  ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json
  [Install]
  WantedBy=multi-user.target
</pre>


* 执行以下命令启动ShadowSocks服务：

<pre>
  systemctl enable shadowsocks
  systemctl start shadowsocks
</pre>

* 可以通过shell脚本去快速安装：

<pre>
  chmod +x install-shadowsocks.sh
  ./install-shadowsocks.sh
</pre>


 

