# AttachKeyPair

调用AttachKeyPair绑定一个SSH密钥对到一台或多台Linux实例。

## 接口说明

当您使用该接口时，需要注意：

-   Windows实例不支持SSH密钥对。
-   绑定SSH密钥对后，将禁用用户名加密码的验证方式。
-   如果实例处于**运行中**（Running）状态，重启实例（[RebootInstance](~~25502~~)）后，SSH密钥对生效。
-   如果实例处于**已停止**（Stopped）状态，启动实例（[StartInstance](~~25500~~)）后，SSH密钥对生效。
-   如果实例已经绑定了SSH密钥对，新的SSH密钥对自动替换原来的SSH密钥对。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=AttachKeyPair&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|AttachKeyPair|系统规定参数。取值：AttachKeyPair |
|InstanceIds|String|是|\["i-bp1gtjxuuvwj17zr\*\*\*\*", "i-bp17b7zrsbjwvmfy\*\*\*\*", … "i-bp1h6jmbefj1ytos\*\*\*\*"\]|绑定SSH密钥对的实例ID。取值可以由多台实例ID组成一个JSON数组，最多支持50个ID，ID之间用半角逗号（,）隔开。 |
|KeyPairName|String|是|testKeyPairName|SSH密钥对名称。 |
|RegionId|String|是|cn-hangzhou|SSH密钥对所在的地域ID。您可以调用[DescribeRegions](~~25609~~)查看最新的阿里云地域列表。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|FailCount|String|0|绑定密钥对失败的实例数量。 |
|KeyPairName|String|testKeyPairName|密钥对的名称。 |
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|请求ID。 |
|Results|Array of Result| |绑定密钥对成功或失败的结果集合。 |
|Result| | | |
|Code|String|200|传递的操作状态码，其中200表示操作成功。 |
|InstanceId|String|i-m5eg7be9ndloji64\*\*\*\*|实例ID。 |
|Message|String|successful|传递的操作信息，当code=200时，message为successful。 |
|Success|String|true|此次操作是否成功。 |
|TotalCount|String|2|绑定密钥对的实例总数量。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=AttachKeyPair
&InstanceIds=["i-bp1gtjxuuvwj17zr****", "i-bp17b7zrsbjwvmfy****", … "i-bp1h6jmbefj1ytos****"]
&KeyPairName=testKeyPairName
&RegionId=cn-hangzhou
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<AttachKeyPairResponse>
	  <TotalCount>2</TotalCount>
	  <RequestId>4ADF7A06-66BD-4FBF-A2ED-2364E41D8C06</RequestId>
	  <Results>
		    <Result>
			      <Message>successful</Message>
			      <InstanceId>i-m5eg7be9ndloji64****</InstanceId>
			      <Success>true</Success>
			      <Code>200</Code>
		    </Result>
		    <Result>
			      <Message>successful</Message>
			      <InstanceId>i-m5e25x2mwr0hk33d****</InstanceId>
			      <Success>true</Success>
			      <Code>200</Code>
		    </Result>
	  </Results>
	  <FailCount>0</FailCount>
</AttachKeyPairResponse>
```

`JSON` 格式

```
{
    "TotalCount": 2,
    "RequestId": "4ADF7A06-66BD-4FBF-A2ED-2364E41D8C06",
    "Results": {
        "Result": [
            {
                "Message": "successful",
                "InstanceId": "i-m5eg7be9ndloji64****",
                "Success": true,
                "Code": "200"
            },
            {
                "Message": "successful",
                "InstanceId": "i-m5e25x2mwr0hk33d****",
                "Success": true,
                "Code": "200"
            }
        ]
    },
    "FailCount": 0
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|400|InvalidKeyPairName.NotFound|The specified KeyPairName does not exist in our records.|指定的KeyPairName不存在。|
|403|DependencyViolation.WindowsInstance|The instance creating is windows, cannot use ssh key pair to login|指定的实例是Windows操作系统，此类实例不支持SSH密钥对登录。|
|400|InvalidInstanceIds.ValueNotSupported|The specified parameter InstanceIds is not valid.|指定的InstanceId参数不合法。|
|400|DependencyViolation.IoOptimize|The specified parameter InstanceIds is not valid.|指定的实例ID不合法，指定的实例IO优化配置不合法。|

访问[错误中心](https://error-center.aliyun.com/status/product/Ecs)查看更多错误码。

