#                  实验二 腾讯云创建个人网站

### 1.安装Apache Web服务器

  使用yum工具安装：

![1](../Website/image/1.PNG)

![](../Website/image/2.PNG)

启动Apache Web服务器：

sudo systemctl start httpd.service

测试Apache服务器是否成功运行，在浏览器输入：

http://49.235.61.89/

若运行正常，将出现如下界面：

![](../Website/image/3.PNG)

### 2.安装MYSQL

安装MariaDB:

sudo yum install mariadb-server mariadb

安装完成显示

![](../Website/image/4.PNG)