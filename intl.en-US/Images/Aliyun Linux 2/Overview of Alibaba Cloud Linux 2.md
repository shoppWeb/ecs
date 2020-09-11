---
keyword: [Alibaba Cloud, alibaba cloud linux 2, ecs, native Linux]
---

# Overview of Alibaba Cloud Linux 2

Alibaba Cloud Linux 2 is a next-generation proprietary Linux distribution developed by Alibaba Cloud. It provides a safe, stable, and high-performance customized running environment for applications on ECS instances. Alibaba Cloud Linux 2 is optimized for cloud infrastructure and aims to deliver a better runtime experience. You can create an instance by using the Alibaba Cloud Linux 2 image. Alibaba Cloud Linux 2 is free to use, and Alibaba Cloud provides long-term technical support.

For more information, see the [Alibaba Cloud Linux 2 product page](https://www.alibabacloud.com/en/products/alinux).

## Scenarios

Alibaba Cloud Linux 2 is suitable for the following scenarios:

-   Various workloads in cloud environments, such as databases, cloud-native containers, data analytics, web applications, and other workloads in the production environment.
-   Various instance families including ECS Bare Metal Instance families. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).
    -   Alibaba Cloud Linux 2 supports instance types that have 1 to 160 vCPUs.
    -   Alibaba Cloud Linux 2 supports instance types that have a memory of 0.5 GiB to 3,840 GiB.
    -   Alibaba Cloud Linux 2 does not support non-I/O optimized instances.

## Benefits

Compared with other Linux distributions, Alibaba Cloud Linux 2 has the following benefits:

-   Alibaba Cloud provides long-term free software maintenance and technical support for Alibaba Cloud Linux 2.
-   Alibaba Cloud Linux 2 is optimized through the combination with Alibaba Cloud infrastructure and features faster system startup and higher runtime performance.
-   Alibaba Cloud Linux 2 provides the latest enhanced features of the Linux community to support cloud-based application environments.
-   Alibaba Cloud Linux 2 is equipped with a custom Linux kernel, user mode packages, and toolkits that provide additional features to the operating system.
-   Alibaba Cloud Linux 2 offers a streamlined kernel and increased protection against security risks. Alibaba Cloud Linux 2 provides policies to monitor and fix security vulnerabilities and ensures constant system security.

## Features

-   Alibaba Cloud Linux 2 is distributed with the latest version of the Alibaba Cloud kernel. The kernel provides the following features:
    -   The Alibaba Cloud kernel is based on Linux kernel V4.19 with the long-term support \(LTS\) from the kernel community. It is optimized for cloud-based scenarios, improved performance, and bug fixes. For more information, see [Release notes of Alibaba Cloud Linux 2](/intl.en-US/Images/Aliyun Linux 2/Release notes of Alibaba Cloud Linux 2.md).
    -   Alibaba Cloud Linux 2 provides customized and optimized kernel startup parameters and system configuration parameters for the ECS instance environment.
    -   Alibaba Cloud Linux 2 provides the kernel failure dumping mechanism kdump when the operating system fails. You can enable or disable this feature without the need to restart the operating system.
    -   Alibaba Cloud Linux 2 provides Kernel Live Patching \(KLP\).
-   Pre-installed software and updates are described as follows:
    -   The user-mode package is compatible with the latest version of CentOS 7 and can run on Alibaba Cloud Linux 2.
    -   Alibaba Cloud Linux 2 is pre-installed with Alibaba Cloud CLI.
    -   The network module is changed from network.service to systemd-networkd.
    -   Fixes for Common Vulnerabilities and Exposures \(CVE\) will be continuously updated until the end of life \(EOL\) of Alibaba Cloud Linux 2. For more information, see [Alibaba Cloud Linux 2.1903 Security Advisories](http://mirrors.aliyun.com/alinux/cve/alinux2.xml). Alibaba Cloud Linux 2 provides automatic solutions to automatically fix vulnerabilities. For more information, see [Use YUM to perform security updates](/intl.en-US/Images/Aliyun Linux 2/Features and interfaces supported by Alibaba Cloud Linux 2/Use YUM to perform security updates.md).
-   Alibaba Cloud Linux 2 accelerates the startup process, improves runtime performance, and enhances system stability in the following ways:
    -   Alibaba Cloud Linux 2 optimizes the startup speed for ECS instances. Tests have proven that Alibaba Cloud Linux 2 can save 60% of startup time compared with other operating systems.
    -   Alibaba Cloud Linux 2 optimizes scheduling, memory, I/O, and network subsystems. In some open-source benchmark tests, Alibaba Cloud Linux 2 shows 10% to 30% performance improvement compared with other operating systems.
    -   Alibaba Cloud Linux 2 features enhanced system stability. According to statistics, Alibaba Cloud Linux 2 can reduce the downtime by 50% compared with other operating systems.

## Billing

Alibaba Cloud Linux 2 images are provided free of charge. However, you are charged for resources such as vCPUs, memory, storage, public bandwidth, and snapshots. For more information, see [Billing overview](/intl.en-US/Pricing/Billing overview.md).

## Obtain Alibaba Cloud Linux 2

You can use the following methods to obtain and use Alibaba Cloud Linux 2:

-   ECS instances
    -   When you create an ECS instance, select **Public Image** and then select Alibaba Cloud Linux and its version. For more information, see [Create an instance by using the provided wizard](/intl.en-US/Instance/Create an instance/Create an instance by using the provided wizard.md).
    -   Update the operating system of an existing ECS instance to Alibaba Cloud Linux 2 by replacing the system disk. For more information, see [Replace the system disk \(public images\)](/intl.en-US/Block Storage/Cloud disks/Change the operating system/Replace the system disk (public images).md).
-   On-premises environments such as a KVM-based virtualization environment

    Download and install the Alibaba Cloud Linux 2 image, and restart the system. For more information, see [Use Alibaba Cloud Linux 2 images in an on-premises environment](/intl.en-US/Images/Aliyun Linux 2/Features and interfaces supported by Alibaba Cloud Linux 2/Use Alibaba Cloud Linux 2 images in an on-premises environment.md).


## Use Alibaba Cloud Linux 2

-   View or modify system parameters

    You can run the sysctl command to view or modify the runtime parameters of Alibaba Cloud Linux 2. Alibaba Cloud Linux 2 has updated the following kernel configuration parameters in the /etc/sysctl.d/50-aliyun.conf file.

    |System parameter|Description|
    |----------------|-----------|
    |`kernel.hung_task_timeout_secs = 240`|Increases the kernel hung\_task timeout seconds to avoid frequent hung\_task prompts.|
    |`kernel.panic_on_oops = 1`|Throws the kernel panic exception when the kernel is experiencing an Oops error. System failure details are automatically captured if kdump is configured.|
    |`kernel.watchdog_thresh = 50`|Increases the thresholds for events such as hrtimer, NMI, soft lockup, and hard lockup to avoid potential kernel false positives.|
    |`kernel.hardlockup_panic = 1`|Throws the kernel panic exception when the kernel is experiencing a hard lockup error. System failure details are automatically captured if kdump is configured.|

-   View kernel parameters

    Alibaba Cloud Linux 2 has updated the following kernel parameters. You can run the `cat /proc/cmdline` command to view the kernel parameters of Alibaba Cloud Linux 2 at runtime.

    |Kernel parameter|Description|
    |----------------|-----------|
    |`crashkernel=0M-2G:0M,2G-8G:192M,8G-:256M`|Reserves memory space for the kdump feature.|
    |`cryptomgr.notests`|Disables crypto self-check during kernel startup to accelerate system startup.|
    |`cgroup.memory=nokmem`|Disables the kernel memory statistics function of memory cgroup to avoid potential kernel instability.|
    |`rcupdate.rcu_cpu_stall_timeout=300`|Increases the timeout threshold of RCU CPU Stall Detector to 300 seconds to avoid kernel false positives.|

-   Roll back the kernel version

    Alibaba Cloud Linux 2 is distributed with Alibaba Cloud kernel V4.19.y. The kernel version changes when you update the image. You can run the following commands to install and switch to a V3.10 series kernel that is compatible with CentOS 7 as required.

    **Note:** Replacing the kernel version may result in a boot failure. Exercise caution when you perform this operation.

    Run the following commands to roll back to the V3.10 kernel:

    ```
    # Install a V3.10 kernel.
    sudo yum install -y kernel-3.10.0
    # Configure the GRUB driver.
    sudo grub2-set-default "$(grep ^menuentry /boot/grub2/grub.cfg | grep 3.10.0 | awk -F\' '{ print $2 }')"
    # Apply changes to the configuration file.
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    # Restart the operating system for the new configurations to take effect.
    sudo reboot
    ```

-   Enable or disable kdump

    Alibaba Cloud Linux 2 provides the kdump service. After this service is enabled, kernel errors can be captured to help you analyze kernel failures.

    **Note:** If the memory of the selected instance type is less than or equal to 2 GiB, the kdump service cannot be used.

    -   Run the following commands to enable the kdump service:

        ```
        # Enable the kdump service first.
        sudo systemctl enable kdump.service
        # Restart the kdump service.
        sudo systemctl restart kdump.service
        ```

    -   Run the following commands to return the memory address space reserved by the kdump service to the operating system and disable the kdump service:

        ```
        # Modify the configuration in the /sys/kernel/kexec_crash_size file.
        sudo sh -c 'echo 0 > /sys/kernel/kexec_crash_size'
        # Disable the kdump service.
        sudo systemctl disable kdump.service
        # Stop the kdump service.
        sudo systemctl stop kdump.service
        ```

        **Note:** After the memory address space that is reserved by the kdump service is returned to the operating system, the operating system must be restarted to re-enable the kdump service.

-   Configure the network

    By default, Alibaba Cloud Linux 2 uses systemd-networkd to configure the network. The configuration file for DHCP or static IP addresses is located in the /etc/systemd/network/ directory.

    ```
    # Restart the network.
    sudo systemctl restart systemd-networkd
    ```

-   Obtain the Debuginfo package and the source code package
    -   Run the following commands to obtain the Debuginfo package:

        ```
        # Install yum-utils.
        sudo yum install -y yum-utils
        # Install the Debuginfo package by replacing packageName in the following command with the name of the target software package:
        sudo debuginfo-install -y <packageName>
        ```

    -   Run the following commands to obtain the source code package:

        ```
        # Install the source code.
        sudo yum install -y alinux-release-source
        # Install yum-utils.
        sudo yum install -y yum-utils
        # Install the source code package by replacing sourcePackageName in the following command with the name of the target software package:
        sudo yumdownloader --source <sourcePackageName>
        ```

-   Use experimental software packages

    Experimental software packages are provided by Alibaba Cloud, but are not fully tested. Alibaba Cloud does not guarantee the quality of these packages. Alibaba Cloud Linux 2 provides the following types of experimental packages:

    -   Experimental software packages that serve common purposes

        -   `Golang 1.12`
        -   `Golang 1.13`
        Run the following commands to install an experimental software package that serves common purposes:

        ```
        # Enable Yum repositories.
        sudo yum install -y alinux-release-experimentals
        # Install an experimental software package that serves common purposes by replacing packageName in the following command with the name of the target software package:
        sudo yum install -y <packageName>
        ```

    -   Development kits that support SCL plug-ins

        -   The development kit that is based on `GCC-7.3.1`: devtoolset-7
        -   The development kit that is based on `GCC-8.2.1`: devtoolset-8
        -   The development kit that is based on `GCC-9.1.1`: devtoolset-9
        Run the following commands to install an experimental software package that supports SCL plug-ins:

        ```
        # Install `scl-utils`.
        sudo yum install -y scl-utils
        # Enable Yum repositories.
        sudo yum install -y alinux-release-experimentals
        # Install the software packages that you need from the Yum repositories. The following sample commands install all development kits that support SCL plug-ins:
        sudo yum install -y devtoolset-7-gcc devtoolset-7-gdb devtoolset-7-binutils devtoolset-7-make
        sudo yum install -y devtoolset-8-gcc devtoolset-8-gdb devtoolset-8-binutils devtoolset-8-make
        sudo yum install -y devtoolset-9-gcc devtoolset-9-gdb devtoolset-9-binutils devtoolset-9-make
        ```

        After the installation is completed, you can use the later version of GCC and related tools. The sample code is as follows:

        ```
        # Specify the repository name to view an existing SCL. The following command uses the devtoolset-7 repository as an example:
        scl -l devtoolset-7
        #Run the related SCL software.
        scl enable devtoolset-7 'gcc --version'
        ```


## Update history

-   For more information about release notes of Alibaba Cloud Linux 2 images, see [Release notes of Alibaba Cloud Linux 2](/intl.en-US/Images/Aliyun Linux 2/Release notes of Alibaba Cloud Linux 2.md).
-   For more information about CVE updates of Alibaba Cloud Linux 2, see [Alibaba Cloud Linux 2.1903 Security Advisories](http://mirrors.aliyun.com/alinux/cve/alinux2.xml).

## Technical support

Alibaba Cloud provides the following support for Alibaba Cloud Linux 2:

-   5-year long-term support \(LTS\) in terms of security updates and vulnerability fixes is provided until the version lifecycle ends on March 31, 2024. You can obtain free LTS in the following ways:
    -   [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createIndex)
    -   [GitHub](https://alibaba.github.io/cloud-kernel/os.html?spm=5176.cnalinux.0.0.1f8323d1WpS5ZY&aly_as=32Di8ZOj)
-   Images are updated every four months. Updates cover new features, security updates, and vulnerability fixes.
-   Security updates are provided from Yum repositories. You can run the yum update command to update security to the latest version.

