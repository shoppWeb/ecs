# DescribeClassicLinkInstances

调用DescribeClassicLinkInstances查询一台或多台与专有网络VPC建立了连接的经典网络类型实例。

## 接口说明

调用该接口时，您需要注意：

-   该接口仅支持经典网络类型实例。
-   单次最多只能查询100台经典网络类型实例。
-   参数`VpcId`和`InstanceId`不能同时为空。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=DescribeClassicLinkInstances&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DescribeClassicLinkInstances|系统规定参数。取值：DescribeClassicLinkInstances |
|RegionId|String|是|cn-hangzhou|实例所属的地域ID。您可以调用[DescribeRegions](~~25609~~)查看最新的阿里云地域列表。 |
|VpcId|String|否|vpc-bp1vwnn14rqpyiczj\*\*\*\*|VPC ID。目标VPC必须已开启ClassicLink功能，详情请参见[建立ClassicLink连接](~~65413~~)。 |
|InstanceId|String|否|i-bp1a5zr3u7nq9cxh\*\*\*\*|实例ID。最多指定100台实例ID，并使用半角逗号（,）隔开。 |
|PageNumber|String|否|1|当前页码。起始值：1

 默认值：1 |
|PageSize|String|否|10|分页查询时设置的每页行数。取值范围：1~100

 默认值：10 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|Links|Array of Link| |返回经典网络类型实例和VPC连接信息。 |
|Link| | | |
|InstanceId|String|i-test|实例ID。 |
|VpcId|String|vpc-test|VPC ID。 |
|PageNumber|Integer|1|分页查询的页码。 |
|PageSize|Integer|10|分页查询的每页行数。 |
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|请求ID。 |
|TotalCount|Integer|2|连接总数。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=DescribeClassicLinkInstances
&RegionId=cn-hangzhou
&VpcId=vpc-bp1vwnn14rqpyiczj****
&InstanceId=i-bp1a5zr3u7nq9cxh****
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<DescribeClassicLinkInstancesResponse> 
      <PageNumber>1</PageNumber>
      <Links>
            <Link>
                  <InstanceId>i-bp1a5zr3u7nq9cxh****</InstanceId>
                  <VpcId>vpc-bp1vwnn14rqpyiczj****</VpcId>
            </Link>
            <Link>
                  <InstanceId>i-bp1a5zr3u7nq9abc****</InstanceId>
                  <VpcId>vpc-bp1vwnn14rqpyiczj****</VpcId>
            </Link>
      </Links>
      <TotalCount>2</TotalCount>
      <PageSize>10</PageSize>
      <RequestId>B154D309-F3E1-4AB7-BA94-FEFCA8B89001</RequestId>
</DescribeClassicLinkInstancesResponse>
```

`JSON` 格式

```
{
    "PageNumber":1,
    "Links":{
        "Link":[{
            "InstanceId":"i-bp1a5zr3u7nq9cxh****",
            "VpcId":"vpc-bp1vwnn14rqpyiczj****"
            },
           {
           "InstanceId": "i-bp1a5zr3u7nq9abc****",
           "VpcId": "vpc-bp1vwnn14rqpyiczj****"
           }]
    },
    "TotalCount":2,
    "PageSize":10,
    "RequestId":"B154D309-F3E1-4AB7-BA94-FEFCA8B89001"
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|403|InvalidInstanceId.NotFound|The InstanceId provided does not exist in our records.|指定的实例不存在，请您检查实例ID是否正确。|
|403|InvalidParameter.ToManyInstanceIds|No more than 100 InstanceIds can be specified.|最多只能指定100个实例ID。|
|403|InvalidParameter.InvalidInstanceIdAndVpcId|The parameter InstanceId and VpcId are not allowed to be empty at the same time.|至少指定一个InstanceId或VpcId。|
|403|InvalidInstanceId.NotBelong|The specified instance is not belong to you.|指定的实例不在您账号下。|
|403|InvalidVpc.NotBelong|The specified vpc is not belong to you.|指定的VPC是不在您的账号下。|

访问[错误中心](https://error-center.aliyun.com/status/product/Ecs)查看更多错误码。

