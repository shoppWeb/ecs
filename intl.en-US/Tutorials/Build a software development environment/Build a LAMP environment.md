# Build a LAMP environment

This topic describes how to build a LAMP stack \(also known as LAMP\) on an ECS instance. LAMP is an acronym of the names of its original four components: the Linux operating system, Apache HTTP Server, MySQL relational database management system, and PHP programming language.

-   An Alibaba Cloud account is created. To create an Alibaba Cloud account, go to the [account registration page](https://account.alibabacloud.com/register/intl_register.htm).
-   An ECS instance is created and a public IP address is assigned to the instance. For more information, see [Creation method overview](/intl.en-US/Instance/Create an instance/Creation method overview.md).

    In the examples in this topic, an ECS instance that has the following configurations is used:

    -   Instance type: ecs.c6.large
    -   Operating system: CentOS 7.8 64-bit public image
    -   Network type: VPC
    -   IP address: a public IP address
-   An inbound rule is added to a security group of the ECS instance to allow traffic on ports 22 and 80. For more information, see [Add security group rules](/intl.en-US/Security/Security groups/Add security group rules.md).

This topic is intended for individual users who are familiar with Linux operating systems but new to using Alibaba Cloud ECS to build websites. The following software versions are used in the examples in this topic. The operations may vary depending on the versions of your software.

-   Apache 2.4.6
-   MySQL 5.7.31
-   PHP 7.0.33
-   phpMyAdmin 4.0.10.20

This topic describes how to manually build a LAMP stack. You can also purchase a LAMP image on [Alibaba Cloud Marketplace](https://marketplace.alibabacloud.com/) and create an ECS instance from the image to build websites.

## Step 1: Prepare for building a LAMP environment

1.  [Create an instance by using the provided wizard](/intl.en-US/Instance/Create an instance/Create an instance by using the provided wizard.md).

2.  [Connect to a Linux instance by using VNC](/intl.en-US/Instance/Connect to instances/Connect to Linux instances/Connect to a Linux instance by using VNC.md).

3.  Run the cat /etc/redhat-release command to check the system version.

    ![CentOS 7.8](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6592089951/p147928.png)

4.  Disable the firewall.

    1.  Run the systemctl status firewalld command to check the status of the firewall.

        ![Check the status of the firewall](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8229919951/p32172.png)

        -   If the firewall is in the inactive state, the firewall is disabled.
        -   If the firewall is in the active state, the firewall is enabled. In this example, the firewall is in the active state. Therefore, you must disable the firewall.
    2.  Disable the firewall. Skip this step if the firewall is already disabled.

        -   To temporarily disable the firewall, run the systemctl stop firewalld command.

            **Note:** After you run this command, the firewall is temporarily disabled. It will enter the active state when you restart the Linux operating system.

        -   To permanently disable the firewall, run the systemctl disable firewalld command.

            **Note:** You can re-enable the firewall after it is disabled. For more information, visit the [official firewalld website](https://firewalld.org/).

5.  Disable Security-Enhanced Linux \(SELinux\).

    1.  Run the getenforce command to check the status of SELinux.

        ![Check the status of SELinux](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/8229919951/p21065.png)

        -   If SELinux is in the Disabled state, SELinux is disabled.
        -   If SELinux is in the Enforcing state, SELinux is enabled. In this example, SELinux is in the Enforcing state. Therefore, you must disable SELinux.
    2.  Disable SELinux. Skip this step if SELinux is already disabled.

        -   To temporarily disable SELinux, run the `setenforce 0` command.

            **Note:** After you run this command, SELinux is temporarily disabled. It will enter the Enforcing state when you restart the Linux operating system.

        -   To permanently disable SELinux, run the vi /etc/selinux/config command to edit the SELinux configuration file. Press the Enter key. Move the pointer to the `SELINUX=enforcing` line and press the I key to switch to the edit mode. Change the line to `SELINUX=disabled` and press the Esc key. Enter :wq and press the Enter key to save and close the SELinux configuration file.

            **Note:** You can re-enable SELinux after it is disabled. For more information, see [SELinux documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/deployment_guide/ch-selinux#s1-SELinux-resources).

            Restart the system to apply the settings.


## Step 2: Install Apache

1.  Run the following command to install Apache and its extension package:

    ```
    yum -y install httpd httpd-manual mod_ssl mod_perl mod_auth_mysql
    ```

2.  Run the httpd -v command to check the Apache version.

    ![httpd -v](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6592089951/p147922.png)

3.  Run the following commands in sequence to start Apache and enable Apache to start upon system startup:

    1.  ```
systemctl start httpd
```

    2.  ```
systemctl enable httpd
```

4.  Check the installation result.

    1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

    2.  In the left-side navigation pane, choose **Instances & Images** \> **Instances**.

    3.  On the **Instances** page, find the instance on which you want to build a LAMP environment and copy its public IP address from the **IP address** column.

    4.  Enter `http://<Public IP address of the ECS instance>` in the address bar of your browser and press the Enter key.

        If the following page is displayed, Apache is started.

        ![httpd](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9531600061/p147934.png)


## Step 3: Install and configure MySQL

1.  Run the following command to update the YUM repository:

    ```
    rpm -Uvh  http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
    ```

2.  Run the following command to install MySQL:

    **Note:** If you are using an operating system whose kernel version is el8, you may receive the No match for argument error message. In this case, run the yum module disable mysql command to disable the default mysql module before you install MySQL.

    ```
    yum -y install mysql-community-server
    ```

3.  Run the following command to check the MySQL version:

    ```
    mysql -V
    ```

    The following command output indicates that MySQL is installed:

    ```
    mysql  Ver 14.14 Distrib 5.7.31, for Linux (x86_64) using  EditLine wrapper
    ```

4.  Run the following command to start MySQL:

    ```
    systemctl start mysqld
    ```

5.  Run the following command to enable MySQL to start upon system startup:

    ```
    systemctl enable mysqld
    systemctl daemon-reload
    ```

6.  Run the following command to check the initial password used to log on to the MySQL database:

    ```
    grep "password" /var/log/mysqld.log
    ```

    The following response provides an example. In this example, the initial password is `+47,uijcojcU`.

    ```
    2020-08-28T03:01:49.848762Z 1 [Note] A temporary password is generated for root@localhost: +47,uijcojcU
    ```

7.  Run the following command to perform security configurations for MySQL:

    ```
    mysql_secure_installation
    ```

    Follow these steps:

    1.  Reset the password of the root user.

        **Note:** You must keep the password of the root user secure.

        ```
        Enter password for user root: # Enter the initial password that you obtained in the previous step.
        The 'validate_password' plugin is installed on the server.
        The subsequent steps will run with the existing configuration of the plugin.
        Using existing password for root.
        Estimated strength of the password: 100 
        Change the password for root ? (Press y|Y for Yes, any other key for No) : Y # Enter Y to change the password of the root user.
        New password: # Enter a new password that is 8 to 30 characters in length. It must contain uppercase letters, lowercase letters, digits, and special characters. Special characters include ( ) ` ~ ! @ # $ % ^ & * - + = | {} [] : ; ' < > , . ? /
        Re-enter new password: # Enter the new password again for confirmation.
        Estimated strength of the password: 100 
        Do you wish to continue with the password provided?( Press y|Y for Yes, any other key for No) : Y
        ```

    2.  Enter Y to delete anonymous users.

        ```
        By default, a MySQL installation has an anonymous user, allowing anyone to log into MySQL without having to have a user account created for them. This is intended only for testing, and to make the installation go a bit smoother. You should remove them before moving into a production environment.
        Remove anonymous users? (Press y|Y for Yes, any other key for No) : Y # Enter Y to delete anonymous users.
        Success.
        ```

    3.  Enter Y to deny remote access from the root user.

        ```
        Disallow root login remotely? (Press y|Y for Yes, any other key for No) : Y # Enter Y to deny remote access from the root user.
        Success.
        ```

    4.  Enter Y to delete the test database and access permissions on this database.

        ```
        Remove test database and access to it? (Press y|Y for Yes, any other key for No) : Enter Y to delete the test database and access permissions on this database.
        - Dropping test database...
        Success.
        ```

    5.  Enter Y to reload privilege tables.

        ```
        Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y # Enter Y to reload privilege tables.
        Success.
        All done!
        ```


## Step 4: Install PHP

1.  Update the YUM repository.

    1.  Run the following commands to add the EPEL repository:

        ```
        yum install -y \
        https://repo.ius.io/ius-release-el7.rpm \
        https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
        ```

    2.  Run the following command to add the Webtatic repository:

        ```
        rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm
        ```

2.  Run the following command to install PHP:

    ```
    yum -y install php70w-devel php70w.x86_64 php70w-cli.x86_64 php70w-common.x86_64 php70w-gd.x86_64 php70w-ldap.x86_64 php70w-mbstring.x86_64 php70w-mcrypt.x86_64  php70w-pdo.x86_64   php70w-mysqlnd  php70w-fpm php70w-opcache php70w-pecl-redis php70w-pecl-mongodb
    ```

3.  Run the following command to check the PHP version:

    ```
    php -v
    ```

    The following command output indicates that PHP is installed:

    ```
    PHP 7.0.33 (cli) (built: Dec  6 2018 22:30:44) ( NTS )
    Copyright (c) 1997-2017 The PHP Group
    Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
        with Zend OPcache v7.0.33, Copyright (c) 1999-2017, by Zend Technologies                
    ```

4.  Run the following command to create a test file in the root directory of the Apache website:

    ```
    echo "<? php phpinfo(); ? >" > /var/www/html/phpinfo.php
    ```

5.  Run the following command to restart Apache:

    ```
    systemctl restart httpd
    ```

6.  Enter `http://Public IP address of the ECS instance/phpinfo.php` in the address bar of your browser and press the Enter key.

    If the following page is displayed, PHP is installed.

    ![PHP](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/6592089951/p147964.png)


## Step 5: Install phpMyAdmin

phpMyAdmin is a MySQL databases management tool that enables you to manage databases conveniently by using web interfaces.

1.  Run the following commands to prepare a directory to store phpMyAdmin data:

    ```
    mkdir -p /var/www/html/phpmyadmin
    ```

2.  Run the following commands to download and decompress the phpMyAdmin package.

    1.  Run the following command to download the phpMyAdmin package:

        ```
        cd
        wget https://files.phpmyadmin.net/phpMyAdmin/4.0.10.20/phpMyAdmin-4.0.10.20-all-languages.zip
        ```

    2.  Run the following command to decompress the phpMyAdmin package:

        ```
        yum install -y unzip
        unzip phpMyAdmin-4.0.10.20-all-languages.zip
        ```

3.  Run the following command to copy the phpMyAdmin files to the prepared directory:

    ```
    mv phpMyAdmin-4.0.10.20-all-languages/*  /var/www/html/phpmyadmin
    ```

4.  Enter `http://Public IP address of the ECS instance/phpmyadmin` in the address bar of your browser and press the Enter key to go to the logon page of phpMyAdmin.

    If the following page is displayed, phpMyAdmin is installed.

    ![phpMyAdmin is installed](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/9029919951/p33718.png)

5.  Enter the MySQL username and password. Click **Go**.


