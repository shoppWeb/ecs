# ResetDisk

调用ResetDisk使用磁盘的历史快照回滚至某一阶段的磁盘状态。

## 接口说明

调用该接口时，您需要注意：

-   磁盘的状态必须为使用中（In\_Use）的状态。
-   磁盘挂载的实例的状态必须为已停止（Stopped）。
-   指定的参数SnapshotId必须是由DiskId创建的历史快照。
-   通过[DescribeInstances](~~25506~~)查询ECS实例信息时，如果返回数据中包含`{"OperationLocks": {"LockReason" : "security"}}`，则禁止一切操作。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=ResetDisk&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|ResetDisk|系统规定参数。取值：ResetDisk |
|DiskId|String|是|d-bp199lyny9b3\*\*\*\*|指定的磁盘设备ID。 |
|SnapshotId|String|是|s-bp199lyny9b3\*\*\*\*|需要恢复到某一磁盘阶段的历史快照ID。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|F3CD6886-D8D0-4FEE-B93E-1B73239673DE|请求ID。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=ResetDisk
&DiskId=d-bp199lyny9b3****
&SnapshotId=s-bp199lyny9b3****
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<ResetDiskResponse>
      <RequestId>F3CD6886-D8D0-4FEE-B93E-1B73239673DE</RequestId>
</ResetDiskResponse>
```

`JSON` 格式

```
{
    "RequestId":"F3CD6886-D8D0-4FEE-B93E-1B73239673DE"
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|404|InvalidDiskId.NotFound|The specified disk does not exist.|指定的磁盘不存在。请您检查磁盘ID是否正确。|
|404|Disk.NotFound|The specified disk does not exist.|指定的磁盘不存在，请您检查磁盘是否正确。|
|404|InvalidSnapshotId.NotFound|The specified SnapshotId does not exist.|指定的快照不存在，请您检查快照是否正确。|
|403|IncorrectDiskStatus|The current disk status does not support this operation.|当前的磁盘不支持此操作，请您确认磁盘处于正常使用状态，是否欠费。|
|403|IncorrectInstanceStatus|The current status of the resource does not support this operation.|该资源目前的状态不支持此操作。|
|403|InstanceLockedForSecurity|The instance is locked due to security.|您的资源被安全锁定，拒绝操作。|
|403|InvalidParameter.Mismatch|The specified snapshot is not created from the specified disk.|未加密的磁盘无法通过加密的快照重置。|
|403|InvalidSnapshot.TooOld|The snapshotId is created before 2013-07-15, it cannot be restored since the first time the disk detached.|创建于2013年7月15日之前的快照不支持此操作。|
|403|InstanceExpiredOrInArrears|The specified operation is denied as your prepay instance is expired \(prepay mode\) or in arrears \(afterpay mode\).|包年包月实例已过期，请您续费后再进行操作。|
|403|OperationDenied|The specified snapshot dees not support ResetDisk.|指定的快照不支持重置磁盘。|
|403|InvalidSnapshotId.NotReady|The specified snapshot has not completed yet.|指定的快照未完成。|
|500|InternalError|The request processing has failed due to some unknown error.|内部错误，请重试。如果多次尝试失败，请提交工单。|
|400|DiskCategory.OperationNotSupported|The operation is not supported to the specified disk due to its disk category|由于磁盘种类限制，指定的磁盘不支持该操作。|
|403|InvalidAccountStatus.NotEnoughBalance|Your account does not have enough balance.|账号余额不足，请您先充值再进行该操作。|
|403|InvalidAccountStatus.SnapshotServiceUnavailable|Snapshot service has not been opened yet.|快照服务未开通，操作无法执行。|
|404|InvalidInstanceId.NotFound|The specified InstanceId does not exist.|指定的实例不存在，请您检查实例ID是否正确。|
|403|Operation.Conflict|The operation may conflicts with others.|该操作与其他操作冲突。|
|403|UserNotInTheWhiteList|The user is not in disk white list.|您不在磁盘白名单中，请加入白名单后重试。|
|400|InvalidRegionId.MalFormed|The specified RegionId is not valid|指定的RegionId不合法。|
|500|InternalError|The request processing has failed due to some unknown error, exception or failure.|内部错误，请重试。如果多次尝试失败，请提交工单。|
|403|InvalidParameter.KMSKeyId.KMSUnauthorized|ECS service have no right to access your KMS.|ECS服务无权访问您的KMS。|

访问[错误中心](https://error-center.aliyun.com/status/product/Ecs)查看更多错误码。

