# 搭建SSR

配置：

阿里云轻量应用服务器 | CentOS 7.3 

1核 - 1GB内存 - 系统盘25GB SSD云盘 - 中国（香港）



## 1.连接服务器

putty挺好用的

## 2.搭建SSR

yum -y install wget

wget --no-check-certificate https://freed.ga/github/shadowsocksR.sh; bash shadowsocksR.sh

 这里设置密码和端口，等待安装成功注意保存信息！！！！

## 3.加速(BBR)

先升级内核，然后开启BBR算法，此处与SS搭建中BBR加速一样的

## 4.加速(锐速)

锐速加速

wget --no-check-certificate -O rskernel.sh https://raw.githubusercontent.com/hombo125/doubi/master/rskernel.sh && bash rskernel.sh

这里会自动重启,然后执行下面

yum install net-tools -y && wget --no-check-certificate -O appex.sh https://raw.githubusercontent.com/0oVicero0/serverSpeeder_Install/master/appex.sh && bash appex.sh install

一路回车

**OK**

 
