# DeleteImage

调用DeleteImage删除一份自定义镜像。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=DeleteImage&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DeleteImage|系统规定参数。取值：DeleteImage |
|ImageId|String|是|m-bp67acfmxazb4p\*\*\*\*|镜像ID。如果指定的自定义镜像不存在，则请求将被忽略。 |
|RegionId|String|是|cn-hangzhou|自定义镜像所在的地域ID。您可以调用[DescribeRegions](~~25609~~)查看最新的阿里云地域列表。 |
|Force|Boolean|否|false|是否执行强制删除。取值范围：

 -   true：强制删除自定义镜像，忽略当前镜像是否被其他实例使用。
-   false：正常删除自定义镜像，删除前检查当前镜像是否被其他实例使用。

 默认值：false |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|请求ID。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=DeleteImage
&ImageId=m-bp67acfmxazb4p****
&RegionId=cn-hangzhou
&Force=false
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<DeleteImageResponse>
       <RequestId>CEF72CEB-54B6-4AE8-B225-F876FF7BA984</RequestId>
</DeleteImageResponse>
```

`JSON` 格式

```
{
    "RequestId": "CEF72CEB-54B6-4AE8-B225-F876FF7BA984"
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|403|ImageUsingByInstance|The specified image has been used to create instances.|指定的镜像已被用于创建实例，无法删除。|
|403|ImageUseShared|The specified image has been shared to others.|指定的镜像已经分享给其它用户。|
|403|OperationDenied.ImageCopying|The Image is coping.|正在复制镜像，请您稍后再试。|
|403|ImageIsImporting|The specified Image is importing.|指定镜像正在导入，无法执行操作。|
|403|ImageIsExporting|The specified Image is exporting. Please cancel task first.|该镜像正在导出，请先取消任务。|
|404|InvalidImageId.NotFound|The specified ImageId does not exist.|指定的镜像在该用户账号下不存在，请您检查镜像ID是否正确。|

访问[错误中心](https://error-center.aliyun.com/status/product/Ecs)查看更多错误码。

