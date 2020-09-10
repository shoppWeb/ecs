---
keyword: [network interface controller, elastic network interface, ENI, network interface, ECS, Alibaba Cloud, IP address]
---

# ENI overview

An elastic network interface \(ENI\) is a virtual network interface controller \(NIC\) that can be bound to a VPC-type ECS instance. You can use ENIs to deploy high availability clusters and perform low-cost failover and fine-grained network management.

## Attributes

An ENI is a virtual network interface that must be bound to a VPC-type instance before you can use the ENI. The following table lists the attributes of an ENI.

|Attribute|Description|
|---------|-----------|
|ENI type|ENIs consist of primary and secondary ENIs. -   Primary ENIs: created together with the instance. The lifecycle of a primary ENI is the same as the instance to which the ENI is bound. You cannot unbind a primary ENI from an instance.
-   Secondary ENIs: can be separately created. You can bind or unbind a secondary ENI to or from an instance. |
|VPC|Only VPC-type instances support ENIs. An ENI must be in the same VPC as the instance to which the ENI is bound.|
|Zone|The VSwitch to which the ENI belongs must be in the same zone as the instance to which the ENI is bound.|
|Security group|An ENI must be added to at least one security group. The security group controls the inbound and outbound traffic of the ENI.|
|EIP|An ENI can be associated with one or more elastic IP addresses \(EIPs\). For more information, see [Overview](/intl.en-US/User Guide/Associate an EIP with a cloud instance/Bind an EIP to a secondary ENI/Overview.md).|
|Primary private IP address|A primary IP address is an IP address customized by the user or assigned by the system during ENI creation. The primary IP address must be within the range of the idle CIDR block of the VSwitch.|
|Secondary private IP address|A secondary IP address must be within the range of the idle CIDR block of the VSwitch. You can assign or revoke the secondary IP address.|
|Media access control \(MAC\) address|A globally unique identifier of an ENI.|

## Features

An ENI is an independent virtual NIC that can be migrated among multiple instances to support the flexible scaling and migration of services. When you create an ENI together with an instance, the ENI is automatically bound to the instance. You can also separately create a secondary ENI and bind it to an instance.

ENIs have the following features:

-   In addition to the primary ENI that is created together with the instance, you can also bind multiple secondary ENIs to the instance. The ECS instance and the secondary ENIs that you want to bind to the instance must be in the same zone of the same VPC, but can belong to different VSwitches and security groups.
-   Each ENI can be assigned multiple secondary private IP addresses based on the instance type of the instance to which the ENI is bound.
-   When you unbind a secondary ENI from an instance and bind the ENI to another instance, the attributes of the ENI remain unchanged and the network traffic is switched to the new instance.
-   ENIs support hot-plug and can be migrated among instances. When you unbind an ENI from an instance and bind the ENI to another instance, services on the instances are not affected and you do not need to restart the instances.

## Limits

-   The following content provides limits on the resources supported by a single ENI:
    -   Primary private IP address: one.
    -   Secondary private IP address: one or more depending on the instance type of the instance to which the ENI is bound. For more information, see [Instance families](/intl.en-US/Instance/Instance families.md).
    -   EIP: one or more depending on how the EIPs are associated with the ENI. For more information, see [Overview](/intl.en-US/User Guide/Associate an EIP with a cloud instance/Bind an EIP to a secondary ENI/Overview.md).
    -   MAC address: one.
    -   Security group: one to five. At least one security group is required.
-   A limited number of ENIs can be created for one account in each region. For more information, see the "ENI limits" section in [Limits](/intl.en-US/Product Introduction/Limits.md).
-   The ENI and the instance to which the ENI is bound must be in the same zone of the same VPC, but can belong to different VSwitches and security groups.
-   The number of secondary ENIs that can be bound to an ECS instance depends on the instance type.
-   Only I/O optimized instance types support ENIs.
-   ECS instances in the classic network do not support ENIs.
-   The instance bandwidth varies based on the instance type. You cannot increase the bandwidth of an ECS instance by binding multiple secondary ENIs to the instance.

## Scenarios

ENIs are suitable for the following scenarios:

-   Deployment of high availability clusters

    Multiple ENIs can be bound to an ECS instance. This implements a high availability architecture.

-   Low-cost failover

    You can unbind an ENI from a failed ECS instance and bind the ENI to another instance to redirect traffic destined for the failed instance to the backup instance. This allows quick recovery of services.

-   Fine-grained network management

    You can configure multiple ENIs for an instance. For example, you can use some ENIs for internal management and other ENIs for Internet business access to isolate confidential data from business data. You can also configure specific security group rules for each ENI based on the source IP addresses, protocols, and ports to achieve access control.

-   Configuration of multiple private IP addresses for one instance

    You can assign multiple secondary private IP addresses to an ENI. If your ECS instance hosts multiple applications, you can assign an independent IP address for each application and improve the utilization of your instance.

-   Configuration of multiple public IP addresses for one instance

    An ECS instance that has no ENIs bound can be assigned only one public IP address. You can assign multiple public IP addresses to an instance by associating EIPs with one or more ENIs of the instance. In NAT mode, all private IP addresses of an ENI can have EIPs associated.


## Operations in the console

The following table lists the operations that you can perform in the ECS console to manage ENIs.

|Management task|Description|Reference|
|---------------|-----------|---------|
|Create an ENI|You can create an ENI together with an instance or separately create an ENI.|[Create an ENI](/intl.en-US/Network/Elastic Network Interfaces/Create an ENI.md)|
|Bind an ENI|When you create an ENI together with an instance, the ENI is automatically bound to the instance. You can also separately create an ENI and bind it to an instance. An ENI can be bound to only one ECS instance at a time. However, an ECS instance can have multiple ENIs.|[Bind an ENI](/intl.en-US/Network/Elastic Network Interfaces/Attach an ENI.md)|
|Configure an ENI|For the instances whose images cannot identify secondary ENIs, log on to the instance to configure the ENIs. **Note:** If the instance runs an image of CentOS 7.3 64-bit, CentOS 6.8 64-bit, or Windows Server 2008 R2 or later, you do not need to configure ENIs.

|[Configure an ENI](/intl.en-US/Network/Elastic Network Interfaces/Configure an ENI.md)|
|Assign or revoke secondary private IP addresses|You can assign or revoke multiple secondary private IP addresses to or from an ENI.|-   [Assign secondary private IP addresses](/intl.en-US/Network/Elastic Network Interfaces/Assign a secondary private IP address.md)
-   [Revoke secondary private IP addresses](/intl.en-US/Network/Elastic Network Interfaces/Revoke secondary private IP addresses.md) |
|Modify an ENI|You can modify the security groups to which the primary and secondary ENIs belong. You can also modify the names and descriptions of secondary ENIs.|[Modify an ENI](/intl.en-US/Network/Elastic Network Interfaces/Modify an ENI.md)|
|Unbind an ENI|You can unbind an ENI from an instance.|[Detach an ENI from an instance](/intl.en-US/Network/Elastic Network Interfaces/Detach an ENI from an instance.md)|
|Delete an ENI|You can delete an ENI after you unbind it from an instance.|[Delete an ENI](/intl.en-US/Network/Elastic Network Interfaces/Delete an ENI.md)|

## API operations

The following table lists the API operations that you can use to manage ENIs.

|API operation|Description|
|-------------|-----------|
|[CreateNetworkInterface](/intl.en-US/API Reference/Elastic network interfaces/CreateNetworkInterface.md)|Creates a secondary ENI.|
|[DeleteNetworkInterface](/intl.en-US/API Reference/Elastic network interfaces/DeleteNetworkInterface.md)|Deletes a secondary ENI.|
|[DescribeNetworkInterfaces](/intl.en-US/API Reference/Elastic network interfaces/DescribeNetworkInterfaces.md)|Queries the list of ENIs.|
|[AttachNetworkInterface](/intl.en-US/API Reference/Elastic network interfaces/AttachNetworkInterface.md)|Binds a secondary ENI to an instance.|
|[AssignPrivateIpAddresses](/intl.en-US/API Reference/Elastic network interfaces/AssignPrivateIpAddresses.md)|Assigns one or more secondary private IP addresses to an ENI.|
|[UnassignPrivateIpAddresses](/intl.en-US/API Reference/Elastic network interfaces/UnassignPrivateIpAddresses.md)|Revokes one or more secondary private IP addresses from an ENI.|
|[DetachNetworkInterface](/intl.en-US/API Reference/Elastic network interfaces/DetachNetworkInterface.md)|Unbinds a secondary ENI from an instance.|
|[ModifyNetworkInterfaceAttribute](/intl.en-US/API Reference/Elastic network interfaces/ModifyNetworkInterfaceAttribute.md)|Modifies the name and description of a secondary ENI, and the security group to which the secondary ENI belongs.|
|[DescribeInstances](/intl.en-US/API Reference/Instances/DescribeInstances.md)|Queries information of ENIs that are bound to an instance.|

