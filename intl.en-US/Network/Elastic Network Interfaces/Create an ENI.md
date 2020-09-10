---
keyword: [multiple private IP addresses, ENI, create an ENI, secondary ENI, ECS, Alibaba Cloud]
---

# Create an ENI

You can use elastic network interfaces \(ENIs\) to deploy high availability clusters and perform low-cost failover and fine-grained network management. This topic describes how to separately create an ENI in the ECS console.

-   A VPC is created in the specified region and a VSwitch is created in the VPC. For more information, see [Create a VPC](/intl.en-US/VPCs and VSwitches/VPC management/Create a VPC.md) and [Create a VSwitch](/intl.en-US/VPCs and VSwitches/VSwitch management/Create a VSwitch.md).
-   A security group is created in the specified VPC. For more information, see [Create a security group](/intl.en-US/Security/Security groups/Create a security group.md).

You can use one of the following methods to create an ENI. This topic describes how to separately create an ENI.

-   Create an ENI when you create an instance.

    When you create an instance, you can bind only one primary and one secondary ENIs to the instance. If you do not unbind the secondary ENI from the instance, the secondary ENI will be released together with the instance. For more information, see [Bind an ENI when you create an instance](/intl.en-US/Network/Elastic Network Interfaces/Attach an ENI.mdsection_zwv_4ps_lgb).

-   Create an ENI separately.

    ENIs that are separately created are secondary ENIs and can be bound to instances.


1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Network & Security** \> **ENIs**.

3.  In the top navigation bar, select a region.

4.  Click **Create ENI**.

5.  In the **Create ENI** dialog box, complete the following configurations.

    |Parameter|Description|
    |---------|-----------|
    |ENI Name|Enter a name in the ENI Name field.|
    |VPC|Select the VPC to which the instance to be bound belongs. After the ENI is created, you cannot change its VPC. **Note:** An ENI can be bound to only an instance that is in the same VPC as the ENI. |
    |VSwitch|Select a VSwitch that is in the same zone as the instance. After the ENI is created, you cannot change its VSwitch. **Note:** An ENI can be bound to only an instance that is in the same zone as the ENI. The instance and the ENI can belong to different VSwitches. |
    |Primary Private IP|Optional. Enter the primary private IPv4 address of the ENI. The IPv4 address must be within the range of the idle CIDR block of the VSwitch. If you do not specify an IPv4 address, a private IPv4 address is automatically assigned to your ENI after the ENI is created.|
    |Secondary Private IP Addresses|Optional. Set the secondary private IP addresses of the ENI.     -   **Not set**: No secondary private IP addresses are assigned to the ENI.
    -   **Auto**: You can enter an integer from 1 to 9 as the number of secondary private IP addresses of the ENI. The system then automatically assigns the corresponding number of idle IP addresses in the VSwitch to the ENI.
    -   **Manual**: You can manually add secondary private IP addresses. You can specify up to nine secondary private IP addresses. |
    |Security Group|Select security groups in the current VPC. You can specify one to five security groups. **Note:** You cannot select a basic security group and an advanced security group at the same time. |
    |Description|Optional. Enter the description of the ENI for later management.|
    |Resource Group|Optional. Select a resource group to which the ENI will be added. For more information about resource groups, see [Resource groups](/intl.en-US/Tag & Resource/Resource/Resource groups.md).|
    |Tag|Optional. Select one or more tags to be bound to the ENI. For more information about tags, see [Overview](/intl.en-US/Tag & Resource/Tags/Overview.md).|

6.  Click **OK**.


If the status of the ENI is **Available** in the ENI list, the creation succeeds.

After you separately create an ENI, you can bind it to an instance. For more information, see [Bind an ENI](/intl.en-US/Network/Elastic Network Interfaces/Attach an ENI.md).

**Related topics**  


[CreateNetworkInterface](/intl.en-US/API Reference/Elastic network interfaces/CreateNetworkInterface.md)

