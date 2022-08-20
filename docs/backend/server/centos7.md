# 服务器初始化

## 系统安装
- CentOS 7.x

## 添加用户/组
```shell scrip
groupadd apps
useradd apps -g apps
passwd apps
```


## 系统升级，依赖安装
```shell scrip
# 更新：
yum update -y
# 修改主机名：
hostnamectl set-hostname lzser
# 安装工具： 
yum install -y epel-release yum-utils
yum install -y vim net-tools numactl fontconfig lrzsz zip unzip wget htop git telnet
yum install -y gcc automake autoconf libtool make
```

## 时间同步
```shell
# 安装时间同步工具
yum install -y ntp
# 设置及启动时间同步工具
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
systemctl start ntpd
systemctl enable ntpd
# 进行一次时间同步
ntpdate -u time.nist.gov
# 重启时间相关组件
systemctl restart rsyslog
systemctl restart crond
```

## selinux
```shell script
vim /etc/selinux/config
SELINUX=disabled
```
- 仅改配置，重启后生效
- 当前生效：setenforce 0 【不是配置生效，是临时关闭 SELINUX】

## 生成公钥
```shell script
ssh-keygen -t rsa -C "dev01@wkclz.com"
```

## 配置公钥
```shell script
mkdir .ssh
chmod 700 .ssh/
cd .ssh
vim authorized_keys
chmod 600 authorized_keys
```

## firewalld
```shell script
systemctl start firewalld
systemctl enable firewalld

firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --permanent --zone=public --add-port=443/tcp
firewall-cmd --permanent --zone=public --add-port=3306/tcp
firewall-cmd --permanent --zone=public --add-port=8080/tcp
firewall-cmd --reload
firewall-cmd --list-ports
firewall-cmd --list-all
```


## 其他工具
```shell script
maven 自行下载
```


## 开机执行指定脚本
```shell script
# vim   /etc/rc.d/rc.local 
# 以 apps 执行 /opt/tomcat-jenkins-9012/bin/startup.sh

su - apps -c '/bin/sh /opt/tomcat-jenkins-9012/bin/startup.sh'

:wq

chmod +x /etc/rc.d/rc.local
```


## 运维

### VirtualBox虚拟机磁盘扩容
- 问题：扩容CentOS7磁盘规划太小，又不想重装，可以使用扩容方式扩大磁盘空间
- 其他讨论：此处仅为一个实践场景，还可以有其他方式扩充空间

#### 扩充 vdi 虚拟磁盘空间
1. Windows 下，vdi 虚拟磁盘扩容到40G
在虚拟机关闭的场景下，执行如下命令
```shell
VBoxManage.exe modifyhd "/path/to/vdi" --resize 40960
```
执行命令之后，vdi 会在磁盘后方产生一块空白区域

2. CentOS 下,将分区初始化
- fdisk /dev/sda # 对sda 进行分区管理
  - n # 新建分区
  - p # 新建主分区
  - Enter,Enter #  两次回车，将会自动选择开始和结束扇区，创建 sda3
  - t # 修改分区类型
  - 3 # 选择要修改的分区
  - 8e # 将分区类型修改为 Linux LVM
  - w # 写入和退出
  - init 6#  重启后才能识别  sda3 (为什么需要重启才能识别 sda3呢)

3. 将sda3 扩容到根节点
  - pvcreate /dev/sda3 #  初始化 sda3为物理卷
  - vgextend centos /dev/sda3 # 在 VG 内增加额外的 PV
  - lvextend -l 100%VG /dev/mapper/centos-root # 扩容/
  - xfs_growfs /dev/mapper/centos-root # 自动扩展文件系统到最大的可用大小

#### 其他命令
- pvs 查看逻辑卷
- vgs 查看卷组

#### 感谢
- 感谢何老板的指导，扩充了对磁盘卷的认知，也搞明白了不换磁盘也能扩充磁盘空间的原理