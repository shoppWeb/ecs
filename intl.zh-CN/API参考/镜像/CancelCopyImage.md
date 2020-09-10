# CancelCopyImage

调用CancelCopyImage取消正在进行中的复制镜像（CopyImage）任务。

## 接口说明

调用该接口时，您需要注意：

-   取消复制镜像后，目标地域中新建的镜像会被自动删除，源镜像保持不变。
-   若复制镜像已完成，则操作失败并返回错误提示。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=CancelCopyImage&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|CancelCopyImage|系统规定参数。取值：CancelCopyImage |
|ImageId|String|是|m-bp1caf3yicx5jlfl\*\*\*\*|正在被复制的镜像ID。 |
|RegionId|String|是|cn-hangzhou|目标镜像所属的地域ID。您可以调用[DescribeRegions](~~25609~~)查看最新的阿里云地域列表。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|请求ID。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=CancelCopyImage
&ImageId=m-bp1caf3yicx5jlfl****
&RegionId=cn-hangzhou
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<CancelCopyImageResponse>
      <RequestId>C8B26B44-0189-443E-9816-D951F59623A9</RequestId>
</CancelCopyImageResponse>
```

`JSON` 格式

```
{
    "RequestId": "C8B26B44-0189-443E-9816-D951F59623A9"
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|404|InvalidRegionId.NotFound|The specified RegionId does not exist.|指定的地域ID不存在。|
|404|InvalidImageId.NotFound|The specified ImageId does not exist.|指定的镜像在该用户账号下不存在，请您检查镜像ID是否正确。|
|400|ImageCreatedNotFromCopy|The specified image is not the target image of a copy action.|这个镜像不是来源于拷贝的镜像，只能取消来自于拷贝的镜像。|
|400|IncorrectImageStatus|The image is copied finished.|复制镜像已完成。|
|400|InvalidDescription.Malformed|The specified description is wrongly formed.|指定的资源描述格式不合法。长度为2-256个字符，不能以http://和https://开头。|
|400|IncorrectImageStatus|The specified snapshot is not coping.|指定的快照不在复制中。|
|400|IncorrectImageStatus|The specified image is not coping.|指定的镜像不在复制中。|
|400|IncorrectImageStatus|The image has completed copy.|镜像已完成复制。|
|400|IncorrectImageStatus|The Image copy has been failed.|复制镜像失败。|
|500|InternalError|The request processing has failed due to some unknown error.|内部错误，请重试。如果多次尝试失败，请提交工单。|
|400|CancelNotSupported|The specified image coping can not be cancelled.|无法取消复制镜像。|

访问[错误中心](https://error-center.alibabacloud.com/status/product/Ecs)查看更多错误码。

