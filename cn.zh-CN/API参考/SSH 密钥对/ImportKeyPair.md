# ImportKeyPair

调用ImportKeyPair导入由其他工具产生的RSA密钥对的公钥部分。导入密钥对后，阿里云为您保管公钥部分，您需要自行妥善保存密钥对的私钥部分。

## 接口说明

调用该接口时，您需要注意：

-   您在每个地域的密钥对数最高为500对。
-   导入的密钥对必须支持下列任一种加密方式：
    -   rsa
    -   dsa
    -   ssh-rsa
    -   ssh-dss
    -   ecdsa
    -   ssh-rsa-cert-v00@openssh.com
    -   ssh-dss-cert-v00@openssh.com
    -   ssh-rsa-cert-v01@openssh.com
    -   ssh-dss-cert-v01@openssh.com
    -   ecdsa-sha2-nistp256-cert-v01@openssh.com
    -   ecdsa-sha2-nistp384-cert-v01@openssh.com
    -   ecdsa-sha2-nistp521-cert-v01@openssh.com

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=ImportKeyPair&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|ImportKeyPair|系统规定参数。取值：ImportKeyPair |
|KeyPairName|String|是|testKeyPairName|密钥对名称。必须保持名称唯一性。 长度为2~128个英文或中文字符。必须以大小字母或中文开头，不能以 http:// 和 https:// 开头。可以包含数字、半角冒号（:）、下划线（\_）或者连字符（-）。 |
|PublicKeyBody|String|是|ABC1234567|密钥对的公钥内容。 |
|RegionId|String|是|cn-hangzhou|地域ID。您可以调用[DescribeRegions](~~25609~~)查看最新的阿里云地域列表。 |
|Tag.N.Key|String|否|TestKey|密钥对的标签键。N的取值范围：1~20。一旦传入该值，则不允许为空字符串。最多支持128个字符，不能以aliyun和acs:开头，不能包含http://或者https://。 |
|Tag.N.Value|String|否|TestValue|密钥对的标签值。N的取值范围：1~20。一旦传入该值，允许为空字符串。最多支持128个字符，不能以acs:开头，不能包含http://或者https://。 |
|ResourceGroupId|String|否|rg-bp67acfmxazb4p\*\*\*\*|SSH密钥对所在的企业资源组ID。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|KeyPairFingerPrint|String|89:f0:ba:62:ac:b8:aa:e1:61:5e:fd:81:69:86:6d:6b:f0:c0:5a:\*\*|密钥对的指纹。根据RFC4716定义的公钥指纹格式，采用MD5信息摘要算法。 |
|KeyPairName|String|testKeyPairName|密钥对名称。 |
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|请求ID。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=ImportKeyPair
&RegionId=cn-qingdao
&PublicKeyBody=ABC1234567
&KeyPairName=testKeyPairName
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<ImportKeyPairResponse>
      <RequestId>473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E</RequestId>
      <KeyPairName>testKeyPairName</KeyPairName>
      <KeyPairFingerPrint>89:f0:ba:62:ac:b8:aa:e1:61:5e:fd:81:69:86:6d:6b:f0:c0:5a:**</KeyPairFingerPrint>
</ImportKeyPairResponse>
```

`JSON` 格式

```
{
    "RequestId": "473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E",
    "KeyPairName": "testKeyPairName",
    "KeyPairFingerPrint": "89:f0:ba:62:ac:b8:aa:e1:61:5e:fd:81:69:86:6d:6b:f0:c0:5a:**"
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|400|InvalidKeyPairName.Malformed|Specified Key Pair name is not valid.|密钥对名称不合法。|
|403|QuotaExceed.KeyPair|The key pair quota exceeds.|密钥对数量已达上限。|
|400|InvalidPublicKeyBody.Malformed|The PublicKeyBody format is not supported.|公钥格式不支持。|
|400|MissingParameter|The input parameter "PublicKeyBody" that is mandatory for processing this request is not supplied.|未提供必需的PublicKeyBody。|
|400|KeyPair.AlreadyExist|The key pair already exist.|密钥对已存在，请不要重复添加。|
|500|InternalError|The request processing has failed due to some unknown error.|内部错误，请重试。如果多次尝试失败，请提交工单。|
|404|InvalidResourceGroup.NotFound|The ResourceGroup provided does not exist in our records.|资源组并不在记录中。|

访问[错误中心](https://error-center.aliyun.com/status/product/Ecs)查看更多错误码。

