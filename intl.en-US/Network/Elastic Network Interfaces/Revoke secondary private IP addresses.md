# Revoke secondary private IP addresses

You can revoke one or more secondary private IP addresses from an elastic network interface \(ENI\) when the ENI no longer needs them.

Before you revoke secondary private IP addresses from an ENI, make sure that the following requirements are met:

-   At least one secondary private IP address is assigned to the ENI.
-   The ENI is in the **Available** \(`Available`\) or **Bound** \(`InUse`\) state.
-   When you revoke secondary private IP addresses from a primary ENI, the ECS instance to which the primary ENI is bound is in the **Running** \(`Running`\) or **Stopped** \(`Stopped`\) state.

You can revoke one or more secondary private IP addresses. However, you cannot revoke primary private IP addresses.

1.  Log on to the [ECS console](https://ecs.console.aliyun.com).

2.  In the left-side navigation pane, choose **Network & Security** \> **ENIs**.

3.  In the top navigation bar, select a region.

4.  On the Network Interfaces page, find the ENI from which you want to revoke secondary private IP addresses. Click **Manage Secondary Private IP Address** in the **Actions** column.

5.  In the Manage Secondary Private IP Address dialog box, find the secondary private IP addresses that you want to revoke. Click **Unassign** corresponding to each IP address.

6.  Click **OK**.


**Related topics**  


[UnassignPrivateIpAddresses](/intl.en-US/API Reference/Elastic network interfaces/UnassignPrivateIpAddresses.md)

