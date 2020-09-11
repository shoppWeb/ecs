# Use Alibaba Cloud Linux 2 images in an on-premises environment

Alibaba Cloud Linux 2 provides local images in various formats that contain the built-in cloud-init package. This topic describes how to use Alibaba Cloud Linux 2 images in an on-premises environment.

Alibaba Cloud Linux 2 images can only run on Kernel-based Virtual Machines \(KVMs\). Alibaba Cloud Linux 2 images cannot start KVMs directly. You must configure a boot image. In this topic, the local operating system is Linux. Alibaba Cloud Linux 2 is used to create a KVM, and cloud-init is used to initialize the system settings of the KVM. For more information about cloud-init, visit [cloud-init official website - Alibaba Cloud \(AliYun\)](https://cloudinit.readthedocs.io/en/latest/topics/datasources/aliyun.html?spm=a2c4g.11186623.2.24.1bec3fcaonbql3). The NoCloud data source is then used to create local configuration files. After the configuration files are attached to the KVM as a virtual disk, the KVM can be started.

This topic is applicable to users who are familiar with KVMs.

## Step 1: Download the Alibaba Cloud Linux 2 image to your local computer

You can download the Alibaba Cloud Linux 2 image to your local computer from [Alibaba Cloud Linux 2 On-premise Image](https://mirrors.aliyun.com/alinux/image/). Alibaba Cloud Linux 2 images in the VHD or qcow2 format are available.

## Step 2: Generate the seed.img boot image from your local computer

You must configure the network, account, and YUM repository of the boot image. Typically, the image name is set to seed.img. You can set another name for the image, but we recommend that you do not.

**Note:** The seed.img image only contains the configuration files that are required to start cloud-init. The image does not contain Alibaba Cloud Linux 2 system files.

You can use one of the following methods to generate the seed.img image:

-   Use the image file prepared by Alibaba Cloud Linux 2 to generate the seed.img image. You can download the image file from [Alibaba Cloud Linux 2 On-premise Image](https://mirrors.aliyun.com/alinux/image/). On the Alibaba Cloud Linux 2 On-premise Image page, click seed.img to download the image.

    You cannot change the configuration information in the boot image. Therefore, this image file is not ideal for all scenarios. Before you use this method to generate the seed.img image, make sure that you are already familiar with the image file.

-   Use the NoCloud data source to manually generate the seed.img image. Perform the following steps:

1.  In the same local directory, create two configuration files `meta-data` and `user-data`.

    1.  Create a directory named `seed` and go to the directory.

        ```
        mkdir seed
        cd seed/
        ```

    2.  Create the `meta-data` configuration file.

        The following example describes the configuration file content. You can modify the configuration as needed.

        ```
        #cloud-config
        #vim:syntax=yaml
        
        local-hostname: alinux-host
        # FIXME: doesn't work for systemd-networkd
        #network-interfaces: |
        #  iface eth0 inet static
        #  address 192.168.122.68
        #  network 192.168.122.0
        #  netmask 255.255.255.0
        #  broadcast 192.168.122.255
        #  gateway 192.168.122.1
        ```

    3.  Create the `user-data` configuration file.

        The following example describes the configuration file content. You can modify the configuration as needed.

        ```
        #cloud-config
        #vim:syntax=yaml
        
        # Create a user named alinux who is authorized to run sudo commands.
        users:
          - default
          - name: alinux
            sudo: ['ALL=(ALL)   ALL']
            plain_text_passwd: aliyun
            lock_passwd: false
        
        # Create the YUM repository for Alibaba Cloud Linux 2.
        yum_repos:
            base:
                baseurl: https://mirrors.aliyun.com/alinux/$releasever/os/$basearch/
                enabled: true
                gpgcheck: true
                gpgkey: https://mirrors.aliyun.com/alinux/RPM-GPG-KEY-ALIYUN
                name: Aliyun Linux - $releasever - Base - mirrors.aliyun.com
            updates:
                baseurl: https://mirrors.aliyun.com/alinux/$releasever/updates/$basearch/
                enabled: true
                gpgcheck: true
                gpgkey: https://mirrors.aliyun.com/alinux/RPM-GPG-KEY-ALIYUN
                name: Aliyun Linux - $releasever - Updates - mirrors.aliyun.com
            extras:
                baseurl: https://mirrors.aliyun.com/alinux/$releasever/extras/$basearch/
                enabled: true
                gpgcheck: true
                gpgkey: https://mirrors.aliyun.com/alinux/RPM-GPG-KEY-ALIYUN
                name: Aliyun Linux - $releasever - Extras - mirrors.aliyun.com
            plus:
                baseurl: https://mirrors.aliyun.com/alinux/$releasever/plus/$basearch/
                enabled: true
                gpgcheck: true
                gpgkey: https://mirrors.aliyun.com/alinux/RPM-GPG-KEY-ALIYUN
                name: Aliyun Linux - $releasever - Plus - mirrors.aliyun.com
        
        # Using cloud-init or systemd-networkd may cause the steps to fail when you create the meta-data configuration file. The alternative network configurations are as follows:
        write_files:
          - path: /etc/systemd/network/20-eth0.network
            permissions: 0644
            owner: root
            content: |
              [Match]
              Name=eth0
        
              [Network]
              Address=192.168. *. */24
              Gateway=192.168. *.1
        
        # You can also use the following alternative network configurations:
        runcmd:
          - ifdown eth0
          - systemctl restart systemd-networkd
        ```

2.  Install the `cloud-utils` software package on your local computer.

    ```
    yum install -y cloud-utils
    ```

3.  In the `seed` directory, run the following command to generate the `seed.img` image.

    ```
    cloud-localds seed.img user-data meta-data
    ```


## Step 3: Start the KVM

You can use one of the following methods to start the KVM. Then, use the account information in the `user-data` configuration file to log on to the KVM.

-   Use libvirt to start the KVM.
    1.  Create a configuration file of the XML format on your local computer. The name of the sample file is `alinux2.xml`. The content of the file is as follows. You can modify the XML-formatted configuration file as needed.

        ```
        <domain type='kvm'>
            <name>alinux2</name>
            <memory>1048576</memory> <! -- 1 GB memory. -->
            <vcpu>1</vcpu>
            <os>
                <type arch='x86_64'>hvm</type>
                <boot dev='hd'/>
            </os>
            <clock sync="localtime"/>
            <on_poweroff>destroy</on_poweroff>
            <on_reboot>restart</on_reboot>
            <on_crash>restart</on_crash>
            <devices>
                <emulator>/usr/bin/qemu-kvm</emulator>
                <disk type='file' device='disk'><! -- Specify the type parameter based on the image format. Set type to qcow2 for the qcow2 format and vpc for the VHD format. -->
                    <driver name='qemu' type='qcow2' cache='none' dataplane='on' io='native'/> <! -- If you want to create a snapshot of the qcow2 format, you must disable dataplane. -->
                    <source file='path'/> <! -- Enter the absolute path of the Alinyun Linux 2 image. -->
                    <target dev='vda' bus='virtio'/>
                </disk>
                <! -- Add the information of seed.img. -->
                <disk type='file' device='disk'>
                    <driver name='qemu' type='raw'/>
                    <source file='/path/to/your/seed.img'/> <! -- Enter the absolute path of seed.img. -->
                    <target dev='vdb' bus='virtio'/>
                </disk>
                <interface type='network'>
                    <source network='default'/>
                    <model type='virtio'/>
                </interface>
                <console type='pty'>
                    <target type='virtio' port='0'/>
                </console>
                <video>
                    <model type='cirrus' vram='9216' heads='1'/>
                    <alias name='video0'/>
                </video>
                <input type='tablet' bus='usb'/>
                <input type='mouse' bus='ps2'/>
                <graphics type='vnc' port='-1' autoport='yes'/>
            </devices>
        </domain>
        ```

    2.  Run the `virsh` command to start the KVM. The sample command is as follows:

        ```
        virsh define alinux2.xml
        virsh start KVMName    # Enter the actual name of the KVM.
        ```

-   Run the `qemu-kvm` command line to start the KVM. You must add the following parameter information to the command line. Change the `file` parameter to the actual absolute path of the seed.img image.

    ```
    -drive file=/path/to/your/seed.img,if=virtio,format=raw
    ```

    For more information about how to use the libvirt and qemu-kvm commands, visit [Installing Virtualization Packages Manually](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html-single/virtualization_deployment_and_administration_guide/index?spm=a2c63.p38356.879954.8.19345311iQWknm#sect-Installing_virtualization_packages_on_an_existing_Red_Hat_Enterprise_Linux_system-Installing_the_virtualization_packages_with_yum).

-   Use the graphical interface \(virt-manager\) to start the KVM. Before you start the KVM, find the configuration file of the KVM on your local computer and add the absolute path of the seed.img image file to the configuration file.

