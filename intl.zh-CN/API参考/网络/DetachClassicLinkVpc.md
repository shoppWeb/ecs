# DetachClassicLinkVpc

调用DetachClassicLinkVpc取消一台经典网络类型ECS实例与专有网络VPC的连接（ClassicLink）。取消ClassicLink后，经典网络类型实例无法与VPC内的实例互通。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=DetachClassicLinkVpc&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DetachClassicLinkVpc|系统规定参数。取值：DetachClassicLinkVpc |
|InstanceId|String|是|i-bp67acfmxazb4p\*\*\*\*|经典网络类型实例ID。 |
|RegionId|String|是|cn-hangzhou|实例所属的地域ID。您可以调用[DescribeRegions](~~25609~~)查看最新的阿里云地域列表。 |
|VpcId|String|是|vpc-bp67acfmxazb4p\*\*\*\*|实例连接的VPC ID。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|请求ID。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=DetachClassicLinkVpc
&RegionId=cn-hangzhou
&VpcId=vpc-bp67acfmxazb4p****
&InstanceId=i-bp67acfmxazb4p****
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<DetachClassicLinkVpcResponse>
      <RequestId>C0003E8B-B930-4F59-ADC0-0E209A9012A8</RequestId>
</DetachClassicLinkVpcResponse>
```

`JSON` 格式

```
{
    "RequestId": "C0003E8B-B930-4F59-ADC0-0E209A9012A8"
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|403|InvalidInstanceId.NotFound|The InstanceId provided does not exist in our records.|指定的实例不存在，请您检查实例ID是否正确。|
|403|InvalidRegionId.Malformed|The specified parameter ?RegionId? is not valid.|指定的 RegionId 不合法。|
|403|InvalidVpcId.Malformed|The specified parameter ?VpcId? is not valid.|指定的 VpcId 不合法。|
|403|InvalidInstanceId.MalFormed|The specified instance is not a classic network instance.|指定的实例不是经典网络实例。|
|403|OperationDenied|The instances are not allowed to detach from the linked vpc.|不允许将此实例与已连接的VPC分离。|
|403|InvalidParameter.InvalidInstanceIdAndVpcId|The parameter InstanceId and VpcId are not allowed to be empty at the same time.|至少指定一个InstanceId或VpcId。|
|403|InvalidInstanceId.NotFound|The specified instance does not exist.|指定的实例不存在，请您检查实例ID是否正确。|
|403|InvalidStatus.InstanceStatus|The specified instance status is not support this operation ,expect status is running or shutted.|指定的实例状态不支持此操作，期望的操作状态是Running或者Stopped。|
|403|InvalidInstanceId.NotBelong|The specified instance is not belong to you.|指定的实例不在您账号下。|
|403|Forbidden.SubUser|User not authorized to operate on the specified resource.|您当前的子账号没有操作此资源的权限。|
|403|InvalidStatus.InstanceStatus|The specified instance status does not support this operation, expected status is Running or Stopped.|指定的实例状态不支持此操作，期望的操作状态是Running或者Stopped。|

访问[错误中心](https://error-center.alibabacloud.com/status/product/Ecs)查看更多错误码。

