# Deploy SVN over HTTP

This topic describes how to deploy Apache Subversion \(SVN\) over HTTP.

-   An Alibaba Cloud account is created. To create an Alibaba Cloud account, go to the [account registration page](https://account.alibabacloud.com/register/intl_register.htm).
-   An instance that is of the ecs.c6.large instance type and runs the CentOS operating system is created. For more information, see [Creation method overview](/intl.en-US/Instance/Create an instance/Creation method overview.md).
-   Inbound rules are added to security groups of the instance to allow traffic on port 3690, which is the default port of SVN. For more information, see [Add security group rules](/intl.en-US/Security/Security groups/Add security group rules.md).

In this topic, the following software versions are used to manually deploy SVN. Choose appropriate software versions when you deploy SVN.

-   Operating system: a CentOS 7.2 64-bit public image
-   SVN: version 1.7.14
-   Apache HTTP Server: 2.4.6

You can also use Alibaba Cloud Marketplace images to deploy SVN. For example, you can use SVN images provided on Alibaba Cloud Marketplace to deploy SVN. For more information, see the "User guide" section in [SVN images \(CentOS 64-bit\)](https://market.aliyun.com/products/55530001/jxsc000061.html).

## Procedure

To deploy SVN over HTTP, perform the following steps:

1.  [Step 1: Install SVN](#section_gyu_lbx_856)
2.  [Step 2: Install Apache](#section_ily_okj_kbz)
3.  [Step 3: Install mod\_dav\_svn](#section_gbn_l1r_5yj)
4.  [Step 4: Configure SVN](#section_ed8_meq_o0d)
5.  [Step 5: Configure Apache](#section_dk7_05x_t3w)
6.  [Step 6: Use a browser to test access to SVN](#section_svb_rm4_e81)

## Step 1: Install SVN

1.  [Connect to the Linux instance](/intl.en-US/Instance/Connect to instances/Connect to Linux instances/Connect to a Linux instance by using a username and password.md).

2.  Run the following command to install SVN:

    ```
    yum install subversion
    ```

3.  Run the following command to check the SVN version:

    ```
    svnserve --version
    ```


## Step 2: Install Apache

1.  Run the following command to install httpd:

    ```
    yum install httpd
    ```

2.  Run the following command to check the httpd version:

    ```
    httpd -version
    ```


## Step 3: Install mod\_dav\_svn

Run the following command to install mod\_dav\_svn:

```
yum install mod_dav_svn
```

## Step 4: Configure SVN

1.  Run the following commands in sequence to create an SVN repository:

    ```
    mkdir /var/svn
    ```

    ```
    cd /var/svn
    svnadmin create /var/svn/svnrepos
    ```

2.  Run the following command to change the user group of the SVN repository to apache:

    ```
    chown -R apache:apache /var/svn/svnrepos
    ```

3.  Run the following commands in sequence to check files automatically generated in the SVN repository:

    ```
    cd svnrepos
    ```

    ```
    ls
    ```

    ![Check files in the SVN repository](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1829919951/p12529.png)

    The following table describes the SVN directories.

    |Directory|Description|
    |---------|-----------|
    |db|Stores all version control data files.|
    |hooks|Stores hook scripts.|
    |locks|The client used to track access to the SVN repository.|
    |format|A text file that contains only one integer, indicating the version number of the current SVN repository.|
    |conf|The configuration file of the SVN repository, including the username and permissions for accessing the repository.|

4.  Run the following command to add a username and password for the SVN repository.

    By default, the password for SVN is in plaintext. You must separately generate a passwd file for HTTP because HTTP does not support passwords in plaintext. In this example, the added username is `userTest` and the password is `passWDTest`. Run one of the following commands:

    -   If this is the first time that you add a user for the SVN repository, run the following command that contains `-c` to generate the passwd file:

        ```
        htpasswd -c /var/svn/svnrepos/conf/passwd userTest
        ```

    -   If this is not the first time that you add a user for the SVN repository, run the following command to generate the passwd file:

        ```
        htpasswd /var/svn/svnrepos/conf/passwd userTest
        ```

    Set the password of the user.

5.  Run the following command to go to the conf directory:

    ```
    cd /var/svn/svnrepos/conf/
    ```

6.  Set the read and write permissions for the account.

    1.  Run the `vi authz` command to open the access control file.

    2.  Press the `I` key to enter the edit mode.

    3.  Move the pointer over the end of the file and add the following code. In the code, userTest specifies the username, r specifies the read permission, and w specifies the write permission.

        ```
        [/]
        userTest=rw
        ```

        ![permission](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1829919951/p110705.png)

    4.  Press the `Esc` key to exit the edit mode, and enter `:wq` to save and close the file.

7.  Modify the configurations of the SVN service.

    1.  Run the `vi svnserve.conf` command to open the configuration file of the SVN service.

    2.  Press the `I` key to enter the edit mode.

    3.  Move the pointer over the following lines, and delete the number sign \(\#\) and space at the beginning of each line.

        **Note:** Each line cannot start with a space and there must be a space on both ends of the equal sign \(=\).

        ```
        anon-access = read # This assigns read permissions to anonymous users. You can also set anon-access to none to disable access by anonymous users, and then the revision history of the SVN service can show dates.
        auth-access = write # This authorizes write permissions.
        password-db = passwd # This specifies the password database file.
        authz-db = authz # This specifies the file that stores the authorization rules for path-based access control.
        realm = /var/svn/svnrepos # This specifies the authorization realm of the repository.
        ```

        ![Modify the configurations of the SVN service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1829919951/p12532.png)

    4.  Press the `Esc` key to exit the edit mode, and enter `:wq` to save and close the file.

8.  Run the following command to start the SVN repository:

    ```
    svnserve -d -r /var/svn/
    ```

    **Note:** Run the `killall svnserve` command to stop the SVN service.

9.  Run the `ps -ef |grep svn` command to check whether the SVN service has been started.

    If the following code returned, the SVN service is started.

    ![Check the SVN service status](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/2829919951/p12533.png)


## Step 5: Configure Apache

1.  Run the `vim /etc/httpd/conf.d/subversion.conf` command to open the httpd configuration file.

2.  Press the `I` key to enter the edit mode.

3.  Enter the following configuration information:

    ```
    <Location /svn>
    DAV svn
    SVNParentPath /var/svn
    AuthType Basic
    AuthName "Authorization SVN"
    AuthzSVNAccessFile /var/svn/svnrepos/conf/authz
    AuthUserFile /var/svn/svnrepos/conf/passwd
    Require valid-user
    </Location>
    ```

4.  Press the `Esc` key, and enter `:wq` to save and close the file.

5.  Run the following command to start the Apache service:

    ```
    systemctl start httpd.service
    ```


## Step 6: Use a browser to test access to SVN

1.  Open the browser in the local computer.

2.  In the address bar, enter a URL in the `http://<Public IP address of the ECS instance>/svn/<SVN repository name>` format, and press the Enter key. In this example, the SVN repository name is svnrepos.

3.  Enter your username and password that you configured in the passwd file. In this example, the username is userTest and the password is passWDTest.

    The following response indicates that you have accessed the created SVN repository.

    ![result](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/1790600061/p101777.png)


