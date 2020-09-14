# 部署LAMP环境

本教程介绍如何使用云服务器ECS实例搭建LAMP平台，其中LAMP分别代表Linux、Apache、MySQL和PHP。

-   已注册阿里云账号。如还未注册，请先完成[账号注册](https://account.alibabacloud.com/register/intl_register.htm)。
-   已创建ECS实例并为实例分配公网IP地址，具体操作请参见[创建方式导航](/intl.zh-CN/实例/创建实例/创建方式导航.md)。

    本篇教程使用以下配置的ECS实例：

    -   实例规格：ecs.c6.large
    -   操作系统：公共镜像CentOS 7.8 64位
    -   网络类型：专有网络VPC
    -   IP地址：公网IP
-   已在实例安全组的入方向添加安全组规则并放行22、80端口。具体操作请参见[添加安全组规则](/intl.zh-CN/安全/安全组/添加安全组规则.md)。

本篇教程适用于熟悉Linux操作系统，初次使用阿里云进行建站的个人用户。在示例步骤中使用了以下版本的软件。操作时，请您以实际软件版本为准。

-   Apache：2.4.6
-   MySQL：5.7.31
-   PHP：7.0.33
-   phpMyAdmin：4.0.10.20

本篇教程主要说明手动安装LAMP平台的操作步骤，您也可以在[云市场](https://marketplace.alibabacloud.com/)购买LAMP镜像直接启动ECS，以便快速建站。

## 步骤一：准备工作

1.  [使用向导创建实例](/intl.zh-CN/实例/创建实例/使用向导创建实例.md)。

2.  [通过VNC远程连接登录Linux实例](/intl.zh-CN/实例/连接实例/连接Linux实例/通过VNC远程连接登录Linux实例.md)。

3.  运行命令cat /etc/redhat-release查看系统版本。

    ![CentOS 7.8](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7712359951/p147928.png)

4.  关闭防火墙。

    1.  运行systemctl status firewalld命令查看当前防火墙的状态。

        ![查看防火墙状态](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7712359951/p32172.png)

        -   如果防火墙的状态参数是inactive，则防火墙为关闭状态。
        -   如果防火墙的状态参数是active，则防火墙为开启状态。本示例中防火墙为开启状态，因此需要关闭防火墙。
    2.  关闭防火墙。如果防火墙为关闭状态，请忽略此步骤。

        -   如果您想临时关闭防火墙，运行命令systemctl stop firewalld。

            **说明：** 这只是暂时关闭防火墙，下次重启Linux后，防火墙还会开启。

        -   如果您想永久关闭防火墙，运行命令systemctl disable firewalld。

            **说明：** 如果您想重新开启防火墙，请参见[firewalld官网信息](https://firewalld.org/)。

5.  关闭SELinux。

    1.  运行getenforce命令查看SELinux的当前状态。

        ![查看SELinux状态](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7712359951/p21065.png)

        -   如果SELinux状态参数是Disabled， 则SELinux为关闭状态。
        -   如果SELinux状态参数是Enforcing，则SELinux为开启状态。本示例中SELinux为开启状态，因此需要关闭SELinux。
    2.  关闭SELinux。如果SELinux为关闭状态，请忽略此步骤。

        -   如果您想临时关闭SELinux，运行命令`setenforce 0`。

            **说明：** 这只是暂时关闭SELinux，下次重启Linux后，SELinux还会开启。

        -   如果您想永久关闭SELinux，运行命令vi /etc/selinux/config编辑SELinux配置文件。回车后，把光标移动到`SELINUX=enforcing`这一行，按i键，将其修改为`SELINUX=disabled`， 按Esc键，然后输入:wq并回车以保存并关闭SELinux配置文件。

            **说明：** 如果您想重新开启SELinux，请参见[SELinux的官方文档](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/ch-selinux#s1-SELinux-resources)。

            重启系统使设置生效。


## 步骤二：安装Apache

1.  运行以下命令安装Apache服务及扩展包。

    ```
    yum -y install httpd httpd-manual mod_ssl mod_perl mod_auth_mysql
    ```

2.  运行httpd -v命令可查看Apache的版本号。

    ![httpd -v](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7712359951/p147922.png)

3.  依次运行以下命令启动Apache服务并设置服务开机自启动。

    1.  ```
systemctl start httpd
```

    2.  ```
systemctl enable httpd
```

4.  查看安装结果。

    1.  登录[ECS管理控制台](https://ecs.console.aliyun.com)。

    2.  在左侧导航栏，单击**实例与镜像** \> **实例**。

    3.  在**实例列表**中找到正在部署环境的实例，从该实例的**IP地址**中复制公网IP。

    4.  在本地机器的浏览器地址栏中，输入`http://实例公网IP`并按Enter键。

        若返回页面如下图所示，说明Apache服务启动成功。

        ![httpd](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7712359951/p147934.png)


## 步骤三：安装并配置MySQL

1.  运行以下命令更新YUM源。

    ```
    rpm -Uvh  http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
    ```

2.  运行以下命令安装MySQL。

    **说明：** 如果您使用的操作系统内核版本为el8，可能会提示报错信息No match for argument。您需要先运行命令yum module disable mysql禁用默认的mysql模块，再安装MySQL。

    ```
    yum -y install mysql-community-server
    ```

3.  运行以下命令查看MySQL版本号。

    ```
    mysql -V
    ```

    返回结果如下所示，表示MySQL安装成功。

    ```
    mysql  Ver 14.14 Distrib 5.7.31, for Linux (x86_64) using  EditLine wrapper
    ```

4.  运行以下命令启动MySQL。

    ```
    systemctl start mysqld
    ```

5.  运行以下命令设置开机启动MySQL。

    ```
    systemctl enable mysqld
    systemctl daemon-reload
    ```

6.  运行以下命令查看MySQL的初始密码。

    ```
    grep "password" /var/log/mysqld.log
    ```

    返回结果示例如下，本示例中初始密码为`+47,uijcojcU`。

    ```
    2020-08-28T03:01:49.848762Z 1 [Note] A temporary password is generated for root@localhost: +47,uijcojcU
    ```

7.  运行以下命令配置MySQL的安全性。

    ```
    mysql_secure_installation
    ```

    安全性的配置包含以下五个方面：

    1.  重置root账号的密码。

        **说明：** 请您安全保管root账号的密码信息。

        ```
        Enter password for user root: #输入上一步获取的root用户初始密码
        The 'validate_password' plugin is installed on the server.
        The subsequent steps will run with the existing configuration of the plugin.
        Using existing password for root.
        Estimated strength of the password: 100 
        Change the password for root ? (Press y|Y for Yes, any other key for No) : Y #是否更改root用户密码，输入Y
        New password: #输入新密码，长度为8至30个字符，必须同时包含大小写英文字母、数字和特殊符号。特殊符号可以是()` ~!@#$%^&*-+=|{}[]:;‘<>,.?/
        Re-enter new password: #再次输入新密码
        Estimated strength of the password: 100 
        Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : Y
        ```

    2.  输入Y删除匿名用户账号。

        ```
        By default, a MySQL installation has an anonymous user, allowing anyone to log into MySQL without having to have a user account created for them. This is intended only for testing, and to make the installation go a bit smoother. You should remove them before moving into a production environment.
        Remove anonymous users? (Press y|Y for Yes, any other key for No) : Y  #是否删除匿名用户，输入Y
        Success.
        ```

    3.  输入Y禁止root账号远程登录。

        ```
        Disallow root login remotely? (Press y|Y for Yes, any other key for No) : Y #禁止root远程登录，输入Y
        Success.
        ```

    4.  输入Y删除test库以及对test库的访问权限。

        ```
        Remove test database and access to it? (Press y|Y for Yes, any other key for No) : Y #是否删除test库和对它的访问权限，输入Y
        - Dropping test database...
        Success.
        ```

    5.  输入Y重新加载授权表。

        ```
        Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y #是否重新加载授权表，输入Y
        Success.
        All done!
        ```


## 步骤四：安装PHP

1.  更新YUM源。

    1.  运行以下命令添加epel源。

        ```
        yum install -y \
        https://repo.ius.io/ius-release-el7.rpm \
        https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
        ```

    2.  运行以下命令添加Webtatic源。

        ```
        rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
        ```

2.  运行以下命令安装PHP。

    ```
    yum -y install php70w-devel php70w.x86_64 php70w-cli.x86_64 php70w-common.x86_64 php70w-gd.x86_64 php70w-ldap.x86_64 php70w-mbstring.x86_64 php70w-mcrypt.x86_64  php70w-pdo.x86_64   php70w-mysqlnd  php70w-fpm php70w-opcache php70w-pecl-redis php70w-pecl-mongodb
    ```

3.  运行以下命令查看PHP版本。

    ```
    php -v
    ```

    返回结果如下所示，表示安装成功。

    ```
    PHP 7.0.33 (cli) (built: Dec  6 2018 22:30:44) ( NTS )
    Copyright (c) 1997-2017 The PHP Group
    Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
        with Zend OPcache v7.0.33, Copyright (c) 1999-2017, by Zend Technologies                
    ```

4.  运行以下命令，在Apache网站根目录创建测试文件。

    ```
    echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php
    ```

5.  运行以下命令重启Apache服务。

    ```
    systemctl restart httpd
    ```

6.  在本地机器的浏览器地址栏中，输入`http://实例公网IP/phpinfo.php`并按Enter键。

    显示如下页面表示安装成功。

    ![PHP](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7712359951/p147964.png)


## 步骤五：安装phpMyAdmin

phpMyAdmin是一个MySQL数据库管理工具，通过Web接口管理数据库方便快捷。

1.  运行以下命令准备phpMyAdmin数据存放目录。

    ```
    mkdir -p /var/www/html/phpmyadmin
    ```

2.  运行以下命令下载phpMyAdmin压缩包并解压。

    1.  下载phpMyAdmin压缩包。

        ```
        cd
        wget https://files.phpmyadmin.net/phpMyAdmin/4.0.10.20/phpMyAdmin-4.0.10.20-all-languages.zip
        ```

    2.  解压phpMyAdmin压缩包。

        ```
        yum install -y unzip
        unzip phpMyAdmin-4.0.10.20-all-languages.zip
        ```

3.  运行以下命令复制phpMyAdmin文件到准备好的数据存放目录。

    ```
    mv phpMyAdmin-4.0.10.20-all-languages/*  /var/www/html/phpmyadmin
    ```

4.  在本地机器浏览器地址栏，输入`http://实例公网 IP/phpmyadmin`并按Enter键，访问phpMyAdmin登录页面。

    若返回页面如下图所示，说明phpMyAdmin安装成功。

    ![phpMyAdmin安装成功](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7712359951/p33718.png)

5.  输入MySQL的用户名和密码，单击**执行**。


