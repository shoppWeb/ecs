# CancelTask

调用CancelTask取消一件正在运行的任务。目前，您能取消正在运行的导入镜像任务（ImportImage）和导出镜像任务（ExportImage）。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=CancelTask&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|CancelTask|系统规定参数。取值：CancelTask |
|RegionId|String|是|cn-hangzhou|地域ID。您可以调用[DescribeRegions](~~25609~~)查看最新的阿里云地域列表。 |
|TaskId|String|是|t-bp198jigq7l0h5ac\*\*\*\*|任务ID。您可以调用[DescribeTasks](~~25622~~)查看任务ID列表。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|请求ID。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=CancelTask
&RegionId=cn-hangzhou
&TaskId=t-bp198jigq7l0h5ac****
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<CancelTaskResponse>
      <RequestId>4BE2C7FE-C7A4-4675-A153-174E032AABFB</RequestId>
</CancelTaskResponse>
```

`JSON` 格式

```
{
    "RequestId": "4BE2C7FE-C7A4-4675-A153-174E032AABFB"
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|400|MissingParameter|An input parameter "RegionId" that is mandatory for processing the request is not supplied.|参数RegionId不得为空。|
|400|MissingParameter|An input parameter "TaskId" that is mandatory for processing the request is not supplied.|参数TaskId不得为空。|
|400|InvalidRegionId.NotFound|The specified RegionId does not exist.|指定的地域ID不存在。|
|400|InvalidTaskId.NotFound|The specified "TaskId" is not found.|指定的TaskId不存在。|
|403|InvalidTaskId.TaskActionNotSupport|The specified task action not support.|不支持指定的任务操作。|
|403|InvalidTaskId.IncorrectTaskStatus|The specified task status is not invalid.|指定的任务状态不合法。|
|403|CancelTaskFailed|The task is failed to cancel, Please contact the administrator.|任务无法取消，请联系管理员。|

访问[错误中心](https://error-center.aliyun.com/status/product/Ecs)查看更多错误码。

