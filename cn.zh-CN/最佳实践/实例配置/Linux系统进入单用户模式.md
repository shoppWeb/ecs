# Linux系统进入单用户模式

本文将分别介绍使用CentOS、Debian、SLES和Ubuntu操作系统镜像的ECS实例如何进入单用户模式。

-   已注册阿里云账号。如还未注册，请先完成[账号注册](https://account.aliyun.com/register/register.htm?)。
-   已经创建了一台ECS实例，具体操作步骤请参见[创建方式导航](/cn.zh-CN/实例/创建实例/创建方式导航.md)。本文中创建ecs.g6.large实例规格的ECS实例。

Linux系统的单用户模式是系统启动方式之一，您可以通过Linux系统的系统引导器（GRUB）进入单用户模式。进入单用户模式后，操作者拥有系统管理员权限并能修改全部系统配置信息。该模式常用于以下场景：

-   修改系统密码
-   排查启动故障
-   修复系统异常
-   维护硬盘分区

**说明：** 在单用户模式下，您能修改系统的关键配置，因此建议您在必要场景中设置该模式，并谨慎操作。

您也可以通过卸载系统盘功能来排查启动故障问题，详情请参见[卸载或挂载系统盘](/cn.zh-CN/块存储/云盘基础操作/卸载或挂载系统盘.md)。

## 示例导航

-   [示例一：CentOS操作系统进入单用户模式](#section_1nk_30k_rpj)
-   [示例二：Debian操作系统进入单用户模式](#section_mo5_s2t_m7i)
-   [示例三：SLES操作系统进入单用户模式](#section_im9_5nl_g9k)
-   [示例四：Ubuntu操作系统进入单用户模式](#section_4aa_ic6_c8p)

## 示例一：CentOS操作系统进入单用户模式

本示例中连接CentOS 8.0 64位操作系统的ECS实例。

1.  远程连接ECS实例。

    连接方式请参见[通过VNC远程连接登录Linux实例](/cn.zh-CN/实例/连接实例/连接Linux实例/通过VNC远程连接登录Linux实例.md)。

    **说明：** 使用Workbench和SSH命令远程连接的实例，在通过命令重启时不能直接进入启动系统页面，因此不建议使用这两种连接方式。

2.  运行`reboot`重启ECS实例，并在重启过程中出现选择启动系统界面时按下键盘e键，跳转至启动项配置界面。

    跳转界面如下。

    ![os1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/4895559951/p94739.png)

3.  使用键盘的方向键，移动光标至`linux`开头的一行，并在本行中将`ro`至末尾的内容替换为`rw init=/bin/sh crashkernel=auto`。

    修改后的信息如图所示。

    ![os2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/1350269951/p94742.png)

4.  按下键盘的ctrl+x组合键或按F10键。

    系统会直接进入单用户模式。重置系统密码示例如图所示。

    ![os3](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94745.png)


## 示例二：Debian操作系统进入单用户模式

本示例中连接Debian 10.2 64位操作系统的ECS实例。

1.  远程连接ECS实例。

    连接方式请参见[通过VNC远程连接登录Linux实例](/cn.zh-CN/实例/连接实例/连接Linux实例/通过VNC远程连接登录Linux实例.md)。

    **说明：** 使用Workbench和SSH命令远程连接的实例，在通过命令重启时不能直接进入启动系统页面，因此不建议使用这两种连接方式。

2.  运行`reboot`重启ECS实例，并在重启过程中出现内核项界面时按下键盘e键，进入GRUB界面。

    GRUB界面如下。

    ![db1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94723.png)

3.  使用键盘的方向键，移动光标至`linux`开头的一行，并在本行末尾添加`single`。

    修改后的信息如图所示。

    ![db3](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94715.png)

4.  按下键盘的ctrl+x组合键或按F10键启动系统，并输入root用户的密码。

    系统会进入单用户模式。

    ![db4](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94716.png)


## 示例三：SLES操作系统进入单用户模式

本示例中连接SUSE Linux Enterprise Server 15 SP1 64位操作系统的ECS实例。

1.  远程连接ECS实例。

    连接方式请参见[通过VNC远程连接登录Linux实例](/cn.zh-CN/实例/连接实例/连接Linux实例/通过VNC远程连接登录Linux实例.md)。

    **说明：** 使用Workbench和SSH命令远程连接的实例，在通过命令重启时不能直接进入启动系统页面，因此不建议使用这两种连接方式。

2.  运行`reboot`重启ECS实例，并在重启过程中出现内核项界面时按下键盘e键，进入GRUB界面。

    GRUB界面如下。

    ![sles1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94725.png)

3.  使用键盘的方向键，移动光标向下至`linux`开头的一行，并在本行末尾添加`single`。

    修改后的信息如图所示。

    ![sles2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94731.png)

4.  按下键盘的ctrl+x组合键或按F10键启动系统，并输入root用户的密码。

    系统会进入单用户模式。

    ![sles3](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94733.png)


## 示例四：Ubuntu操作系统进入单用户模式

本示例中连接Ubuntu 18.04 64位操作系统的ECS实例。

1.  远程连接ECS实例。

    连接方式请参见[通过VNC远程连接登录Linux实例](/cn.zh-CN/实例/连接实例/连接Linux实例/通过VNC远程连接登录Linux实例.md)。

    **说明：** 使用Workbench和SSH命令远程连接的实例，在通过命令重启时不能直接进入启动系统页面，因此不建议使用这两种连接方式。

2.  运行`reboot`重启ECS实例 ，并在重启过程中按下键盘shift键，进入GRUB界面。

    GRUB界面示例如下。

    ![ubt1](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94572.png)

3.  选择GRUB页面第二行的高级选项，并按下键盘的enter键。

4.  在跳转页面选择第二行的恢复模式，并按下键盘的e键编辑启动项。

    ![ubt2](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94573.png)

5.  在编辑页面，使用键盘的方向键，移动光标向下至`linux`开头的一行，并在本行中将`ro`至末尾的内容替换为`rw single init=/bin/bash`。

    修改结果如下图所示。

    ![ubt4](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94683.png)

6.  按下键盘的ctrl+x组合键或按F10键。

    系统会直接进入单用户模式。重置系统密码示例如图所示。

    ![ubt5](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5895559951/p94707.png)


