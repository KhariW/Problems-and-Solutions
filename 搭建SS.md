# 搭建SS

配置信息：

阿里云轻量应用服务器 | CentOS 7.3 

1核 - 1GB内存 - 系统盘25GB SSD云盘 - 中国（香港）

推荐文章（科普）：http://www.ttlsa.com/news/popular-science-about-shadowsocks/

## 1.安装shadowsocks

此次搭建用的是python版本的ss，而服务器自带python以及pip，所以直接安装shadowsocks即可

pip install shadowsocks

## 2.新建全局配置文件 /etc/shadowsocks.json

vi /etc/shadowsocks.json

配置如下：

{

  "server":"0.0.0.0",

  "local_address": "127.0.0.1",

  "local_port":1080,

  "port_password": {

​        "2333": "123654"

​    },

  "timeout":300,

  "method":"aes-256-cfb",

  "fast_open": false

}

## 3.配置自启动

vi /etc/systemd/system/shadowsocks.service



\# 这里进入vim文件编辑

\# 脚本内容如下：

[Unit]

Description=Shadowsocks

 

[Service]

TimeoutStartSec=0

ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json

 

[Install]

WantedBy=multi-user.target

## 4.启动shadowsocks服务

systemctl enable shadowsocks

systemctl start shadowsocks

查看状态

systemctl status shadowsocks -l

## 5.设置防火墙（默认关闭的，此步骤可跳过）

· 开启防火墙：

systemctl start firewalld

· 关闭防火墙：

systemctl stop firewalld

· 查看防火墙状态：

systemctl status firewalld

· 设置开机启动：

systemctl enable firewalld

·禁用开机启动：

systemctl disable firewalld

·重启防火墙：

firewall-cmd --reload

·开放端口（修改后需要重启防火墙方可生效）：

firewall-cmd --zone=public --add-port=8080/tcp --permanent

## 6.启用BBR加速算法

启用BBR算法之前首先要确定系统的Linux内核版本在4.9以上

### 查看当前内核版本

uname -r

### 升级内核

·更新yum源仓库

yum -y update

·启用 ELRepo 仓库

rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm

### 查看可用的系统内核包

yum --disablerepo="*" --enablerepo="elrepo-kernel" list available

### 安装最新版本内核

yum --enablerepo=elrepo-kernel install kernel-ml

### 设置 grub2

·查看系统上的所有可用内核

sudo awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg

·设置新的内核为grub2的默认版本

grub2-set-default 0

·生成 grub 配置文件并重启

grub2-mkconfig -o /boot/grub2/grub.cfg

reboot

### 验证

uname -r

### 启用BBR算法

echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf

echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf

sysctl -p

sysctl net.ipv4.tcp_available_congestion_control

·停止BBR

sed -i '/net.core.default_qdisc/d' /etc/sysctl.conf 

sed -i '/net.ipv4.tcp_congestion_control/d' /etc/sysctl.conf 

sysctl -p    

reboot