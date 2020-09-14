# ModifyDiskChargeType

You can call this operation to change the billing method for one or up to 16 disks attached to a single ECS instance.

## Description

After you change the billing method, automatic payment is enabled by default. Make sure that you have sufficient balance in your account. Otherwise, your order becomes invalid and must be canceled. If your account balance is insufficient, you can set the AutoPay parameter to false to generate an unpaid order. Then, you can log on to the [ECS console](https://ecs.console.aliyun.com/) to pay for the order.

When you call this operation, take note of the following items:

-   You can change the billing method from subscription to pay-as-you-go for subscription disks that are attached to a subscription instance.
-   You can change the billing method from pay-as-you-go to subscription for pay-as-you-go data disks that are attached to a subscription or pay-as-you-go instance.
-   The instance cannot be in the Stopped state due to overdue payments.
-   You can change the billing method for each disk three times at most. A maximum of three refunds can be made for price differences for a single instance.
-   The price difference is refunded to the payment account you used. Coupons that have been redeemed are not refundable.
-   You cannot change the billing method of a disk again within five minutes after the billing method is changed.

## Debugging

[OpenAPI Explorer automatically calculates the signature value. For your convenience, we recommend that you call this operation in OpenAPI Explorer. OpenAPI Explorer dynamically generates the sample code of the operation for different SDKs.](https://api.aliyun.com/#product=Ecs&api=ModifyDiskChargeType&type=RPC&version=2014-05-26)

## Request parameters

|Parameter|Type|Required|Example|Description|
|---------|----|--------|-------|-----------|
|Action|String|Yes|ModifyDiskChargeType|The operation that you want to perform. Set the value to ModifyDiskChargeType. |
|DiskIds|String|Yes|\["d-bp67acfmxazb4ph\*\*\*\*", "d-bp67acfmxazb4pi\*\*\*\*", … "d-bp67acfmxazb4pj\*\*\*\*"\]|The list of disk IDs. The value is a JSON array that consists of up to 16 disk IDs. Separate multiple disk IDs with commas \(,\). |
|InstanceId|String|Yes|i-bp1i778bq705cvx1\*\*\*\*|The ID of the instance to which the disk is attached. |
|RegionId|String|Yes|cn-hangzhou|The region ID of the instance. You can call the [DescribeRegions](~~25609~~) operation to query the most recent region list. |
|AutoPay|Boolean|No|true|Specifies whether to enable automatic payment. Default value: true. Valid values:

-   true: Automatic payment is enabled. Make sure that you have sufficient balance in your account. Otherwise, your order becomes invalid and must be canceled.
-   false: An order is generated but no payment is made. If your account balance is insufficient, you can set the AutoPay parameter to false to generate an unpaid order. Then, you can log on to the ECS console to pay for the order. |
|ClientToken|String|No|123e4567-e89b-12d3-a456-426655440000|The client token that is used to ensure the idempotence of the request. You can use the client to generate the value, but you must make sure that it is unique among different requests. The **ClientToken** value must contain only ASCII characters and cannot exceed 64 characters in length. For more information, see [How to ensure idempotence](~~25693~~). |
|DiskChargeType|String|No|PostPaid|The new billing method of the disk. Default value: PrePaid. Valid values:

-   PrePaid: changes the billing method from pay-as-you-go to subscription.
-   PostPaid: changes the billing method from subscription to pay-as-you-go. |

## Response parameters

|Parameter|Type|Example|Description|
|---------|----|-------|-----------|
|OrderId|String|1234567890|The ID of the generated order. |
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|The ID of the request. |

## Examples

Sample requests

```
https://ecs.aliyuncs.com/?Action=ModifyDiskChargeType
&DiskIds=["d-bp67acfmxazb4ph****", "d-bp67acfmxazb4pi****", … "d-bp67acfmxazb4pj****"]
&InstanceId=i-bp1i778bq705cvx1****
&RegionId=cn-hangzhou
&AutoPay=true
&ClientToken=123e4567-e89b-12d3-a456-426655440000
&DiskChargeType=PostPaid
&<Common request parameters>
```

Sample success responses

`XML` format

```
<ModifyDiskChargeType>
        <RequestId>04F0F334-1335-436C-A1D7-6C044FE73368</RequestId>
        <Order>1234567890</Order>
</ModifyDiskChargeType>
```

`JSON` format

```
{
    "RequestId": "04F0F334-1335-436C-A1D7-6C044FE73368",
    "Order": "1234567890"
}
```

## Error codes

|HTTP status code|Error code|Error message|Description|
|----------------|----------|-------------|-----------|
|404|InvalidRegionId.NotFound|The RegionId provided does not exist.|The error message returned because the specified RegionId parameter does not exist. Check whether the region ID is correct.|
|400|InvalidInstanceType.ValueUnauthorized|The specified InstanceType is not authorized.|The error message returned because you are not authorized to use the specified instance type.|
|400|InvalidInstanceType.ValueNotSupported|The specified InstanceType is not supported.|The error message returned because the specified instance type is not supported. Try another instance type.|
|400|MissingParameter.RegionId|RegionId should not be null.|The error message returned because the required RegionId parameter is not specified.|
|400|MissingParameter.InstanceIdNotSupported|InstanceId should not be null.|The error message returned because the required InstanceId parameter is not specified.|
|400|ChargeTypeViolation|The operation is not permitted due to charge type of the instance.|The error message returned because the operation is not supported while the instance uses the current billing method.|
|400|InvalidInstanceId.Released|The specified Instance is not exist.|The error message returned because the specified InstanceId parameter does not exist. Check whether the instance ID is correct.|
|400|InvalidInstance.PurchaseNotFound|The specified Instance has no purchase.|The error message returned because the specified instance cannot be purchased.|
|400|InvalidInstance.UnPaidOrder|The specified Instance has unpaid order.|The error message returned because the specified instance has outstanding orders. You must pay for the orders before you proceed.|
|400|InvalidClientToken.ValueNotSupported|The ClientToken provided is invalid.|The error message returned because the specified ClientToken parameter is invalid.|
|400|Account.Arrearage|Your account has been in arrears.|The error message returned because your account balance is insufficient. Add funds to your account and try again.|
|400|Idempotence.SignatureMismatch|There is a idempotence signature mismatch between this and last request.|The error message returned because the ClientToken value is the same in the current and last requests but the other parameters in these requests do not match.|
|400|InvalidInstanceType.ValueUnauthorized|The specified InstanceType is not Supported.|The error message returned because you are not authorized to use the specified instance type.|
|400|OrderCreationFailed|Create Order failed, please check your parameters and try it later.|The error message returned because the order failed to be created. Check your parameter settings and try again.|
|400|Throttling|Request was denied due to request throttling, please try again after 5 minutes.|The error message returned because your request is denied due to throttling. Try again five minutes later.|
|403|Forbidden|%s|The error message returned because you are not authorized to use the specified resource.|
|404|PaymentMethodNotFound|No billing method has been registered on the account.|The error message returned because you have not selected a payment method. Select a payment method and try again.|
|404|InvalidRamRole.NotFound|The specified parameter "RAMRoleName" does not exist.|The error message returned because the specified RAM role does not exist.|
|400|InstanceDowngrade.QuotaExceed|Quota of instance downgrade is exceed.|The error message returned because the maximum number of configuration downgrades allowed for the instance has reached.|
|404|InvalidDiskIds.NotFound|Some of the specified data disks do not exist.|The error message returned because some data disks specified by the DiskIds parameter do not exist.|
|404|InvalidDiskIds.NotPortable|The specified DiskId is not portable.|The error message returned because the specified disk is not removable.|
|500|InternalError|The request processing has failed due to some unknown error.|The error message returned because an internal error has occurred. Try again later. If the problem persists, submit a ticket.|
|403|InvalidAccountStatus.NotEnoughBalance|Your account does not have enough balance.|The error message returned because your account balance is insufficient. Add funds to your account and try again.|
|403|InvalidInstanceChargeType.NotFound|The chargeType of the instance does not support this operation.|The error message returned because instances that use the specified billing method do not support the operation.|
|404|InvalidDataDiskSize.ValueNotSupported|The specified parameter "Size" is not supported.|The error message returned because the specified DataDiskSize parameter is not supported.|
|404|InvalidAction.NotSupported|The specified action is not supported.|The error message returned because the specified Action parameter is not supported.|
|404|InvalidInstanceStatus.NotSupported|The status of the specified instance is invalid.|The error message returned because the operation is not supported while the instance is in the current state.|
|404|InvalidInstanceId.NOT\_FOUND|The specified instance is not exist.|The error message returned because the specified InstanceId parameter does not exist.|
|400|InvalidInstanceId.NotFound|The specified InstanceId does not exist.|The error message returned because the specified InstanceId parameter does not exist. Check whether the instance ID is correct.|
|400|LastOrderProcessing|The previous order is still processing, please try again later.|The error message returned because the previous order is being processed. Try again later.|

For a list of error codes, visit the [API Error Center](https://error-center.alibabacloud.com/status/product/Ecs).

