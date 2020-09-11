# Use YUM to perform security updates

This topic describes how to use YUM to query, check, and install security updates for Alibaba Cloud Linux 2.

## Background

To ensure system security, Alibaba Cloud Linux 2 stays up to date on Common Vulnerabilities and Exposures \(CVE\) as a community-based effort supported by the industry, promptly updates software packages including the kernel, and fix software defects and security vulnerabilities. For information about Alibaba Cloud Linux 2 security updates, see [Alibaba Cloud Linux 2 security advisories](http://mirrors.aliyun.com/alinux/cve/alinux2.xml).

Based on the Common Vulnerability Scoring System \(CVSS3\) for CVE, Alibaba Cloud Linux 2 divides security updates into the following severity levels:

-   Critical: high-risk CVE, which you must update.
-   Important: relatively high-risk CVE, which Alibaba Cloud strongly recommends you to update.
-   Moderate: medium-risk CVE, which Alibaba Cloud recommends you to update.
-   Low: low-risk CVE, which are optional for updates.

## Query security updates

You can run the following command to query security updates:

```
yum updateinfo <command> [option]
```

The following table describes the parameters of the command.

|Variable name|Value|
|-------------|-----|
|command|-   `list`: queries the list of available security updates.
-   `info <update_id>`: queries details about a specific security update. The value of `<update_id>` is an advisory ID in Alibaba Cloud Linux 2 security advisories. |
|option|-   `--sec-severity=<SEVS>` or `--secseverity=<SEVS>`: specifies the security update severity level through the `<SEVS>` parameter.

**Note:** The values of security update severity levels are case-sensitive.

-   `--cve=<CVES>`: specifies the CVE IDs. You can obtain the CVE IDs from Alibaba Cloud Linux 2 security advisories. |

Usage examples of the commands used to query security updates are as follows:

-   You can run the `yum updateinfo --help` command to obtain the help information about the command.
-   You can run the `yum updateinfo` command to query all available security updates. Example:

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

-   You can run the `yum updateinfo list` command to query the list of available security updates. Example:

    ```
    # yum updateinfo list
    Loaded plugins: fastestmirror
    Loading mirror speeds from cached hostfile
    ALINUX2-SA-2019:0055 Moderate/Sec.  binutils-2.27-41.base.1.al7.x86_64
    ALINUX2-SA-2019:0058 Low/Sec.       curl-7.29.0-54.1.al7.x86_64
    ALINUX2-SA-2019:0059 Low/Sec.       elfutils-default-yama-scope-0.176-2.1.al7.n
    ...
    ```

-   You can run the `yum updateinfo info <update_id>` command to query the details of a specified security update. Example:

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


## Check security updates

You can run the `yum check-update --security` command to check for security updates available for the system. By appending `--secseverity=<SEVS>` to the command, you can check for security updates of a specific severity level. The `<SEVS>` parameter specifies the security update severity level.

**Note:** You can specify multiple security update severity levels and separate them with commas \(,\). The values of security update severity levels are case-sensitive.

Usage examples of checking for security updates are as follows:

-   Example 1:

    ```
    # yum check-update --security |grep available
    49 package(s) needed for security, out of 183 available
    ```

-   Example 2:

    ```
    # yum check-update --security --secseverity=Critical,Important |grep available
    30 package(s) needed for security, out of 183 available
    ```


## Install security updates

You can use the `yum upgrade` command to install security updates in one of the following ways:

-   You can run the `yum upgrade --security` command to install security updates. By appending `secseverity=<SEVS>` to the command, you can install security updates of a specific severity level. The `<SEVS>` parameter specifies the security update severity level.

    **Note:** You can specify multiple security update severity levels and separate them with commas \(,\). The values of security update severity levels are case-sensitive.

    Example:

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

-   You can run the `yum upgrade -cves=<CVES>` command to install security updates of a specific CVE. The `<CVES>` parameter specifies the CVE ID.

    **Note:** You can specify multiple CVE IDs and separate them with commas \(,\). The values of CVE IDs are case-sensitive.

    Example:

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


**Note:** The output of the `man yum` command shows that the `yum upgrade` command is equivalent to the `yum update --obsoletes` command. The `yum upgrade` command is also equivalent to the `yum update` command because `obsoletes` is enabled in the /etc/yum.conf configuration file by default.

