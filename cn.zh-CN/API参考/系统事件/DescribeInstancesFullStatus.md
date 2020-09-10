# DescribeInstancesFullStatus

调用DescribeInstancesFullStatus查询一台或多台ECS实例的全状态信息。全状态信息包括实例状态和实例系统事件状态，其中，实例状态为实例的生命周期状态，实例系统事件为维护事件的健康状态。

## 接口说明

返回结果包括实例状态和待执行（Scheduled）状态的实例系统事件。

如果指定一个时间段，则根据时间段筛选事件。

## 调试

[您可以在OpenAPI Explorer中直接运行该接口，免去您计算签名的困扰。运行成功后，OpenAPI Explorer可以自动生成SDK代码示例。](https://api.aliyun.com/#product=Ecs&api=DescribeInstancesFullStatus&type=RPC&version=2014-05-26)

## 请求参数

|名称|类型|是否必选|示例值|描述|
|--|--|----|---|--|
|Action|String|是|DescribeInstancesFullStatus|系统规定参数。取值：DescribeInstancesFullStatus |
|RegionId|String|是|cn-hangzhou|实例所在地域ID。您可以调用[DescribeRegions](~~25609~~)查看最新的阿里云地域列表。 |
|InstanceId.N|RepeatList|否|i-bp67acfmxazb4p\*\*\*\*|一台或者多台实例ID。N的取值范围：1~100，多个取值使用重复列表的形式。 |
|EventId.N|RepeatList|否|e-bp1hygp5b04o56l0\*\*\*\*|一个或者多个事件ID。N的取值范围：1~100，多个取值使用重复列表的形式。 |
|Status|String|否|Running|指定实例的生命周期状态。取值范围：

 -   Starting：启动中
-   Running：运行中
-   Stopped：已停止 |
|HealthStatus|String|否|Maintaining|指定实例的健康状态。取值范围：

 -   Impaired：服务损坏
-   Warning：服务降级
-   Maintaining：系统维护
-   Initializing：初始化中
-   InsufficientData：数据不足
-   NotApplicable：不适用

 以上参数取值均区分大小写。 |
|InstanceEventType.N|RepeatList|否|InstanceExpiration.Stop|一个或者多个事件的类型。N的取值范围：1~30，多个取值使用重复列表的形式。取值范围：

 -   SystemMaintenance.Reboot：因系统维护实例重启。
-   SystemFailure.Reboot：因系统错误实例重启。
-   InstanceFailure.Reboot：因实例错误实例重启。
-   InstanceExpiration.Stop：因包年包月期限到期，实例停止。
-   InstanceExpiration.Delete：因包年包月期限到期，实例释放。
-   AccountUnbalanced.Stop：因账号欠费，按量付费实例停止。
-   AccountUnbalanced.Delete：因账号欠费，按量付费实例释放。 |
|EventType|String|否|InstanceExpiration.Stop|一个事件的类型。EventType参数只在未指定InstanceEventType.N参数时有效。取值范围：

 -   SystemMaintenance.Reboot：因系统维护实例重启。
-   SystemFailure.Reboot：因系统错误实例重启。
-   InstanceFailure.Reboot：因实例错误实例重启。
-   InstanceExpiration.Stop：因包年包月期限到期，实例停止。
-   InstanceExpiration.Delete：因包年包月期限到期，实例释放。
-   AccountUnbalanced.Stop：因账号欠费，按量付费实例停止。
-   AccountUnbalanced.Delete：因账号欠费，按量付费实例释放。 |
|NotBefore.Start|String|否|2017-12-07T00:00:00Z|查询事件计划执行时间的开始时间。按照[ISO8601](~~25696~~)标准表示，并需要使用UTC时间，格式为yyyy-MM-ddTHH:mm:ssZ。 |
|NotBefore.End|String|否|2017-11-30T00:00:00Z|查询事件计划执行时间的结束时间。按照[ISO8601](~~25696~~)标准表示，并需要使用UTC时间，格式为yyyy-MM-ddTHH:mm:ssZ。 |
|EventPublishTime.Start|String|否|2017-11-30T00:00:00Z|查询事件发布时间的开始时间。按照[ISO8601](~~25696~~)标准表示，并需要使用UTC时间，格式为yyyy-MM-ddTHH:mm:ssZ。 |
|EventPublishTime.End|String|否|2017-12-07T00:00:00Z|查询事件发布时间的结束时间。按照[ISO8601](~~25696~~)标准表示，并需要使用UTC时间，格式为yyyy-MM-ddTHH:mm:ssZ。 |
|PageNumber|Integer|否|1|查询结果的页码。取值范围：正整数

 默认值：1。 |
|PageSize|Integer|否|10|查询结果的分页大小。取值范围：1~100

 默认值：10。 |

## 返回数据

|名称|类型|示例值|描述|
|--|--|---|--|
|InstanceFullStatusSet|Array of InstanceFullStatusType| |实例全状态数组。 |
|InstanceFullStatusType| | | |
|HealthStatus|Struct| |实例的健康状态。 |
|Code|Integer|64|健康状态代码。 |
|Name|String|Warning|健康状态名称。 |
|InstanceId|String|i-bp67acfmxazb4p\*\*\*\*|实例ID。 |
|ScheduledSystemEventSet|Array of ScheduledSystemEventType| |系统计划中的系统事件数组。 |
|ScheduledSystemEventType| | | |
|EventCycleStatus|Struct| |事件状态。 |
|Code|Integer|24|事件状态代码。 |
|Name|String|Scheduled|事件状态名称。 |
|EventId|String|e-bp1hygp5b04o56l0\*\*\*\*|实例事件ID。 |
|EventPublishTime|String|2017-11-30T06:32:31Z|事件的发布时间，使用UTC+0时间。 |
|EventType|Struct| |事件类型。 |
|Code|Integer|1|事件类型代码。 |
|Name|String|SystemMaintenance.Reboot|事件类型名称。 |
|ExtendedAttribute|Struct| |本地盘实例系统事件拓展属性。 |
|Device|String|/dev/vdb|本地盘设备名。 |
|DiskId|String|d-bp67acfmxazb4p\*\*\*\*|本地盘磁盘ID。 |
|InactiveDisks|Array of InactiveDisk| |已释放但需要清理的非活跃云盘或本地盘信息。 |
|InactiveDisk| | | |
|CreationTime|String|2018-07-27T13:53:25Z|云盘或本地盘的创建时间。按照[ISO8601](~~25696~~)标准表示，使用UTC时间，格式为yyyy-MM-ddTHH:mm:ssZ。 |
|DeviceCategory|String|cloud\_ssd|云盘或本地盘种类。可能值：

 -   cloud：普通云盘
-   cloud\_efficiency：高效云盘
-   cloud\_ssd：SSD盘
-   cloud\_essd：ESSD云盘
-   local\_ssd\_pro：I/O密集型本地盘
-   local\_hdd\_pro：吞吐密集型本地盘
-   ephemeral：（已停售）本地盘
-   ephemeral\_ssd：（已停售）本地SSD盘 |
|DeviceSize|String|80|云盘或本地盘大小，单位GiB。 |
|DeviceType|String|system|云盘或本地盘类型。可能值：

 -   system：系统盘
-   data：数据盘 |
|ReleaseTime|String|2019-07-27T13:53:25Z|云盘或本地盘的释放时间。按照[ISO8601](~~25696~~)标准表示，使用UTC时间，格式为yyyy-MM-ddTHH:mm:ssZ。 |
|ImpactLevel|String|100|影响级别。 |
|NotBefore|String|2017-12-07T00:00:00Z|事件的计划执行时间，使用UTC+0时间。 |
|Reason|String|A simulated event.|系统事件的计划原因。 |
|Status|Struct| |实例生命周期状态。 |
|Code|Integer|1|实例生命周期状态代码。 |
|Name|String|Running|实例生命周期状态名称。 |
|PageNumber|Integer|1|页码。 |
|PageSize|Integer|1|每页大小。 |
|RequestId|String|473469C7-AA6F-4DC5-B3DB-A3DC0DE3C83E|请求ID。 |
|TotalCount|Integer|2|总条数。 |

## 示例

请求示例

```
https://ecs.aliyuncs.com/?Action=DescribeInstancesFullStatus
&RegionId=cn-hangzhou
&InstanceId.1=i-bp67acfmxazb4p****
&EventId.1=e-bp1hygp5b04o56l0****
&Status=Running
&HealthStatus=Maintaining
&InstanceEventType.1=InstanceExpiration.Stop
&EventType=InstanceExpiration.Stop
&NotBefore.Start=2017-12-07T00:00:00Z
&NotBefore.End=2017-11-30T00:00:00Z
&EventPublishTime.Start=2017-11-30T00:00:00Z
&EventPublishTime.End=2017-12-07T00:00:00Z
&PageNumber=1
&PageSize=1
&<公共请求参数>
```

正常返回示例

`XML` 格式

```
<DescribeInstancesFullStatusResponse>
      <TotalCount>1</TotalCount>
      <RequestId>7EBCB534-1CE5-46E7-92A6-507B1654CC03</RequestId>
      <PageSize>10</PageSize>
      <PageNumber>1</PageNumber>
      <InstanceFullStatusSet>
            <InstanceFullStatusType>
                  <Status>
                        <Code>1</Code>
                        <Name>Running</Name>
                  </Status>
                  <InstanceId>i-bp19cx17f2yx****</InstanceId>
                  <HealthStatus>
                        <Code>64</Code>
                        <Name>Warning</Name>
                  </HealthStatus>
                  <ScheduledSystemEventSet>
                        <ScheduledSystemEventType>
                              <EventCycleStatus>
                                    <Code>24</Code>
                                    <Name>Scheduled</Name>
                              </EventCycleStatus>
                              <EventPublishTime>2020-04-24T07:09:37Z</EventPublishTime>
                              <EventType>
                                    <Code>65</Code>
                                    <Name>SystemFailure.Reboot</Name>
                              </EventType>
                              <ExtendedAttribute></ExtendedAttribute>
                              <EventId>e-bp1hygp5b04o56l0****</EventId>
                              <NotBefore>2020-04-24T10:32:31Z</NotBefore>
                              <Reason>A simulated event.</Reason>
                        </ScheduledSystemEventType>
                  </ScheduledSystemEventSet>
            </InstanceFullStatusType>
      </InstanceFullStatusSet>
</DescribeInstancesFullStatusResponse>
```

`JSON` 格式

```
{
	"TotalCount": 1,
	"RequestId": "7EBCB534-1CE5-46E7-92A6-507B1654CC03",
	"PageSize": 10,
	"PageNumber": 1,
	"InstanceFullStatusSet": {
		"InstanceFullStatusType": [
			{
				"Status": {
					"Code": 1,
					"Name": "Running"
				},
				"InstanceId": "i-bp19cx17f2yx****",
				"HealthStatus": {
					"Code": 64,
					"Name": "Warning"
				},
				"ScheduledSystemEventSet": {
					"ScheduledSystemEventType": [
						{
							"EventCycleStatus": {
								"Code": 24,
								"Name": "Scheduled"
							},
							"EventPublishTime": "2020-04-24T07:09:37Z",
							"EventType": {
								"Code": 65,
								"Name": "SystemFailure.Reboot"
							},
							"ExtendedAttribute": {},
							"EventId": "e-bp1hygp5b04o56l0****",
							"NotBefore": "2020-04-24T10:32:31Z",
							"Reason": "A simulated event."
						}
					]
				}
			}
		]
	}
}
```

## 错误码

|HttpCode|错误码|错误信息|描述|
|--------|---|----|--|
|404|MissingParameter|%s|缺失参数，请检查参数是否完整。|
|403|InvalidParameter|%s|无效的参数。|
|403|InvalidParameter.TimeEndBeforeStart|%s|您输入的参数无效，请确认结束时间是否早于开始时间。|
|403|OperationDenied.NotInWhiteList|%s|该操作无效，请先加入白名单。|
|403|InstanceIdLimitExceeded|%s|指定的InstanceId个数不能超过100个。|
|403|EventIdLimitExceeded|%s|一次最多能指定100个模拟事件ID。|

访问[错误中心](https://error-center.aliyun.com/status/product/Ecs)查看更多错误码。

