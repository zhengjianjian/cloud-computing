#              安装Ceph

1.添加sudo权限

echo "cephuser ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/cephuser

chmod 0440 /etc/sudoers.d/cephuser

sed -i s'/Defaults requiretty/#Defaults requiretty'/g /etc/sudoers

![](../Ceph/image/1.PNG)

2.安装配置ntp服务

yum install -y ntp ntpdate ntp-doc 

ntpdate 0.us.pool.ntp.org

hwclock --systohc

 systemctl enable ntpd.service

systemctl start ntpd.service

![](../Ceph/image/2.PNG)

禁用SELINUX

sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

3.全部节点相同步骤都已配置完毕，复制虚拟机

![](../Ceph/image/3.PNG)



为osd守护进程创建目录

osd1节点：

sudo mkdir /var/local/osd1

sudo chown ceph:ceph /var/local/osd1

osd2节点:

sudo mkdir /var/local/osd2

sudo chown ceph:ceph /var/local/osd2

主控节点

vi /etc/hosts

\#添加如下配置

IP1    ceph-admin

IP2   mon1

IP3    osd1

IP4    osd2

![](../Ceph/image/4.PNG)

设置免密登录ssh

su - cephuser

生成密钥

ssh-keygen

![](../Ceph/image/5.PNG)

vi ~/.ssh/config

\#添加如下配置

Host ceph-admin

Hostname ceph-admin

User cephuser

Host mon1

Hostname mon1

User cephuser

Host osd1

Hostname osd1

User cephuser

Host osd2

Hostname osd2

User cephuser

sudo chmod 644 ~/.ssh/config

ssh-keyscan osd1 osd2 mon1 >> ~/.ssh/known_hosts

分发密钥

ssh-copy-id ceph-admin

ssh-copy-id mon1

ssh-copy-id osd1

ssh-copy-id osd2

测试连接各节点ssh

ssh ceph-admin

ssh osd1

ssh osd2

ssh mon1

![](../Ceph/image/6.PNG)

ceph-deploy安装

*sudo rpm -Uhv http://download.ceph.com/rpm-jewel/el7/noarch/ceph-release-1-1.el7.noarch.rpm sudo yum update -y && sudo yum install ceph-deploy -y*

ceph-deploy –version

![](../Ceph/image/7.PNG)

创建集群

mkdir my-cluster && cd my-cluster

ceph-deploy new mon1

vi ceph.conf

osd pool default size = 2

public network = {ip-address}/{netmask}

在各个节点安装ceph

ceph-deploy install ceph-admin mon1 osd1 osd2

安装完成

初始化mon1节点

ceph-deploy mon create-initial

ceph-deploy gatherkeys mon1

完成后会多出几个密钥文件

将OSDS添加到集群

ceph-deploy osd prepare osd1:/var/local/osd1 osd2:/var/local/osd2

ceph-deploy osd activate osd1:/var/local/osd1 osd2:/var/local/osd2

