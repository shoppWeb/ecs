# 基于YUM的安全更新操作

本文主要介绍如何使用yum查询、检查以及安装Alibaba Cloud Linux 2操作系统的安全更新。

## 背景信息

Alibaba Cloud Linux 2发行版为保障系统的安全性，会紧密跟进业界与社区发现的软件问题及安全漏洞（CVE），及时更新包括内核在内的软件包，修复软件缺陷，以及修补安全漏洞，增强安全功能。Alibaba Cloud Linux 2安全更新记录请参见[Alibaba Cloud Linux 2安全公告](http://mirrors.aliyun.com/alinux/cve/alinux2.xml)。

Alibaba Cloud Linux 2安全更新根据CVE的通用漏洞评估方法（CVSS3）的评分，将安全更新分为以下四个等级：

-   Critical：高风险，必须更新
-   Important：较高风险，强烈建议更新
-   Moderate：中等风险，推荐更新
-   Low：低风险，可选更新

## 查询安全更新

查询安全更新的yum命令格式如下。

```
yum updateinfo <command> [option]
```

命令内参数的取值说明如下。

|变量名称|取值|
|----|--|
|command|-   `list`：查询可用的安全更新列表。
-   `info <update_id>`：查询指定的安全更新详情。其中参数`<update_id>`的取值为Alibaba Cloud Linux 2安全公告中的Advisory ID。 |
|option|-   `--sec-severity=<SEVS>`或`--secseverity=<SEVS>`：指定安全更新级别，参数`<SEVS>`为指定的安全更新级别。

**说明：** 安全更新级别的取值严格区分大小写。

-   `--cve=<CVES>`：指定CVE ID。CVE ID可从Alibaba Cloud Linux 2安全公告中获取。 |

查询安全更新的命令使用示例如下。

-   命令`yum updateinfo --help`可以获取命令的帮助信息。
-   命令`yum updateinfo`可以查询当前全部可用的安全更新信息，使用示例如下。

    ```
    # yum updateinfo
    Loaded plugins: fastestmirror
    Determining fastest mirrors
    base                                                                                                                                                  | 3.1 kB  00:00:00
    extras                                                                                                                                                | 2.5 kB  00:00:00
    plus                                                                                                                                                  | 2.5 kB  00:00:00
    updates                                                                                                                                               | 2.9 kB  00:00:00
    (1/6): extras/2.1903/x86_64/primary_db                                                                                                                | 149 kB  00:00:00
    (2/6): base/2.1903/x86_64/group_gz                                                                                                                    | 101 kB  00:00:00
    (3/6): updates/2.1903/x86_64/updateinfo                                                                                                               |  81 kB  00:00:00
    (4/6): plus/2.1903/x86_64/primary_db                                                                                                                  | 1.5 MB  00:00:00
    (5/6): base/2.1903/x86_64/primary_db                                                                                                                  | 4.9 MB  00:00:00
    (6/6): updates/2.1903/x86_64/primary_db                                                                                                               | 6.1 MB  00:00:00
    Updates Information Summary: updates
        17 Security notice(s)
             7 Important Security notice(s)
             6 Moderate Security notice(s)
             4 Low Security notice(s)
    updateinfo summary done
    ```

-   命令`yum updateinfo list`可以查询当前可用的安全更新列表，使用示例如下。

    ```
    # yum updateinfo list
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
    ALINUX2-SA-2019:0055 Moderate/Sec.  binutils-2.27-41.base.1.al7.x86_64
    ALINUX2-SA-2019:0058 Low/Sec.       curl-7.29.0-54.1.al7.x86_64
    ALINUX2-SA-2019:0059 Low/Sec.       elfutils-default-yama-scope-0.176-2.1.al7.n
    ...
    ```

-   命令`yum updateinfo info <update_id>`可以查询指定安全更新的内容，使用示例如下。

    ```
    # yum updateinfo info ALINUX2-SA-2020:0005
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
    
    ===============================================================================
      ALINUX2-SA-2020:0005: nss, nss-softokn, nss-util security update (Important)
    ===============================================================================
      Update ID : ALINUX2-SA-2020:0005
        Release : Alibaba Cloud Linux 2.1903
           Type : security
         Status : stable
         Issued : 2020-01-03
           CVEs : CVE-2019-11729
                : CVE-2019-11745
    Description : Package updates are available for Alibaba Cloud Linux 2.1903 that fix
                : the following vulnerabilities:
                :
                : CVE-2019-11729:
                : Empty or malformed p256-ECDH public keys may
                : trigger a segmentation fault due values being
                : improperly sanitized before being copied into
                : memory and used. This vulnerability affects
                : Firefox ESR < 60.8, Firefox < 68, and Thunderbird
                : < 60.8.
                :
                : CVE-2019-11745:
                : When encrypting with a block cipher, if a call to
                : NSC_EncryptUpdate was made with data smaller than
                : the block size, a small out of bounds write could
                : occur. This could have caused heap corruption and
                : a potentially exploitable crash. This
                : vulnerability affects Thunderbird < 68.3, Firefox
                : ESR < 68.3, and Firefox < 71.
                :
       Severity : Important
    updateinfo info done
    ```


## 检查安全更新

命令`yum check-update --security`可以检查系统当前可用的安全更新信息。可以在命令后追加参数`--secseverity=<SEVS>`来检查指定级别的安全更新，参数`<SEVS>`为指定的安全更新级别。

**说明：** 您可以指定多个安全更新的级别，以逗号（,）分隔，严格区分大小写。

检查安全更新的使用示例如下。

-   示例一。

    ```
    # yum check-update --security |grep available
    49 package(s) needed for security, out of 183 available
    ```

-   示例二。

    ```
    # yum check-update --security --secseverity=Critical,Important |grep available
    30 package(s) needed for security, out of 183 available
    ```


## 安装安全更新

您可以使用命令`yum upgrade`通过以下任意一种方式安装安全更新。

-   命令`yum upgrade --security`可以安装安全更新，可在该命令后追加参数`--secseverity=<SEVS>`来安装指定级别的安全更新，参数`<SEVS>`为指定的安全更新级别。

    **说明：** 您可以指定多个安全更新的级别，以逗号（,）分隔，严格区分大小写。

    该命令的使用示例如下。

    ```
    # yum upgrade --security --secseverity=Critical,Important
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
    ...
    [snipped]
    ...
    Transaction Summary
    =============================================================================================================================================================================
    Upgrade  30 Packages (+1 Dependent package)
    
    Total download size: 91 M
    Is this ok [y/d/N]:
    ```

-   命令`yum upgrade -cves=<CVES>`可以安装指定CVE的安全更新，参数`<CVES>`为指定的CVE ID。

    **说明：** 您可以指定多个CVE ID，以逗号（,）分隔，严格区分大小写。

    该命令的使用示例如下。

    ```
    # yum upgrade --cve=CVE-2019-11729,CVE-2019-11745
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
    ...
    [snipped]
    ...
    Dependencies Resolved
    
    =============================================================================================================================================================================
     Package                                         Arch                                Version                                      Repository                            Size
    =============================================================================================================================================================================
    Updating:
     nss                                             x86_64                              3.44.0-7.1.al7                               updates                              854 k
     nss-softokn                                     x86_64                              3.44.0-8.1.al7                               updates                              330 k
     nss-softokn-freebl                              x86_64                              3.44.0-8.1.al7                               updates                              225 k
     nss-sysinit                                     x86_64                              3.44.0-7.1.al7                               updates                               65 k
     nss-tools                                       x86_64                              3.44.0-7.1.al7                               updates                              528 k
     nss-util                                        x86_64                              3.44.0-4.1.al7                               updates                               79 k
    Updating for dependencies:
     nspr                                            x86_64                              4.21.0-1.1.al7                               updates                              127 k
    
    Transaction Summary
    =============================================================================================================================================================================
    Upgrade  6 Packages (+1 Dependent package)
    
    Total download size: 2.2 M
    Is this ok [y/d/N]:
    ```


**说明：** 通过命令`man yum`可知，`yum upgrade`命令等同于`yum update --obsoletes`。因配置文件/etc/yum.conf中默认开启了`obsoletes`，所以`yum upgrade`也等同于`yum update`。

