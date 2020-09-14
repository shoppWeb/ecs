# Untitled

## 查询ECS实例

除ECS管理控制台外，您还能通过API管理ECS实例。阿里云提供了多语言版本SDK来封装API。本文基于Python语言介绍如何通过API查询ECS实例。

查询ECS实例相关信息的完整代码示例请参见完整代码，代码示例解析请参见：

* 查询当前账号支持的地域列表
* 查询指定地域下的ECS实例

### 获取RAM用户AccessKey

使用API管理ECS实例，您需要能访问ECS资源的AccessKey（AccessKey ID和AccessKey Secret）。为了保证云服务的安全，您需要创建一个能访问ECS资源的RAM用户，获取该用户的AccessKey，并使用这个RAM用户和API管理ECS实例。

按以下步骤获取RAM用户AccessKey：

1. 创建RAM用户。具体步骤请参见[创建RAM用户](/intl.zh-CN/用户管理/创建RAM用户.md)。
2. 获取RAM用户AccessKey。具体步骤请参见[创建AccessKey](https://www.alibabacloud.com/help/doc-detail/53045.htm)。
3. 为RAM用户授权，授予RAM用户管理云服务器服务（ECS）的权限。具体步骤请参见[为RAM用户授权](/intl.zh-CN/用户管理/为RAM用户授权.md)。

### 安装ECS Python SDK

首先确保您已经具备Python的Runtime，本文中使用的Python版本为2.7+。

运行以下命令，安装Python SDK：

```text
pip install aliyun-python-sdk-ecs
```

如果提示您没有权限，请切换sudo继续执行。

```text
sudo pip install aliyun-python-sdk-ecs
```

本文使用的SDK版本为2.1.2。

### 完整代码

本文的完整代码如下所示，您可以自行根据实际情况修改代码内容。

```text
#  coding=utf-8
# if the python sdk is not install using 'sudo pip install aliyun-python-sdk-ecs'
# if the python sdk is install using 'sudo pip install --upgrade aliyun-python-sdk-ecs'
# make sure the sdk version is greater than 2.1.2, you can use command 'pip show aliyun-python-sdk-ecs' to check
import json
import logging
from aliyunsdkcore import client
from aliyunsdkecs.request.v20140526.DescribeInstancesRequest import DescribeInstancesRequest
from aliyunsdkecs.request.v20140526.DescribeRegionsRequest import DescribeRegionsRequest
# configuration the log output formatter, if you want to save the output to file,
# append ",filename='ecs_invoke.log'" after datefmt.

logging.basicConfig(level=logging.INFO, format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s', datefmt='%a %d %b %Y %H:%M:%S')
clt = client.AcsClient('<accessKeyId>', '<accessSecret>', '<region-Id>')

# sample api to list aliyun open api.
def hello_aliyun_regions():
    request = DescribeRegionsRequest()
    response = _send_request(request)
    if response is not None:
        region_list = response.get('Regions').get('Region')
        assert response is not None
        assert region_list is not None
        result = map(_print_region_id, region_list)
        logging.info("region list: %s", result)

# output the instance owned in current region.
def list_instances():
    request = DescribeInstancesRequest()
    response = _send_request(request)
    if response is not None:
        instance_list = response.get('Instances').get('Instance')
        result = map(_print_instance_id, instance_list)
        logging.info("current region include instance %s", result)

def _print_instance_id(item):
    instance_id = item.get('InstanceId')
    return instance_id

def _print_region_id(item):
    region_id = item.get("RegionId")
    return region_id

# send open api request
def _send_request(request):
    request.set_accept_format('json')
    try:
        response_str = clt.do_action(request)
        logging.info(response_str)
        response_detail = json.loads(response_str)
        return response_detail
    except Exception as e:
        logging.error(e)

if __name__ == '__main__':
    logging.info("Hello Aliyun OpenApi!")
    hello_aliyun_regions()
    list_instances()
```

### 查询当前账号支持的地域列表

创建文件hello\_ecs\_api.py。为了使用SDK，首先实例化AcsClient对象，这里需要RAM用户的AccessKey ID和AccessKey Secret。

**说明：** AccessKey ID和AccessKey Secret是RAM用户访问阿里云ECS服务API的密钥，具有该账户完全的权限，请妥善保管，避免泄露。

```text
from aliyunsdkcore import client
from aliyunsdkecs.request.v20140526.DescribeInstancesRequest import DescribeInstancesRequest
from aliyunsdkecs.request.v20140526.DescribeRegionsRequest import DescribeRegionsRequest
clt = client.AcsClient('<accessKeyId>', '<accessSecret>', '<region-Id>')
```

完成实例化后。查询当前账号支持的地域列表。详情请参见[DescribeRegions](/intl.zh-CN/API参考/地域/DescribeRegions.md)。

```text
def hello_aliyun_regions():
    request = DescribeRegionsRequest()
    response = _send_request(request)
    region_list = response.get('Regions').get('Region')
    assert response is not None
    assert region_list is not None
    result = map(_print_region_id, region_list)
    logging.info("region list: %s", result)
def _print_region_id(item):
    region_id = item.get("RegionId")
    return region_id
def _send_request(request):
    request.set_accept_format('json')
    try:
        response_str = clt.do_action(request)
        logging.info(response_str)
        response_detail = json.loads(response_str)
        return response_detail
    except Exception as e:
        logging.error(e)
hello_aliyun_regions()
```

在命令行运行`python hello_ecs_api.py`会得到当前支持的Region列表。输出示例如下：

```text
[u'cn-shenzhen', u'ap-southeast-1', u'cn-qingdao', u'cn-beijing', u'cn-shanghai', u'us-east-1', u'cn-hongkong', u'me-east-1', u'ap-southeast-2', u'cn-hangzhou', u'eu-central-1', u'ap-northeast-1', u'us-west-1']
```

### 查询指定地域下的ECS实例

查询实例列表和查询Region列表的方法相似，替换入参对象为DescribeInstancesRequest并定制相关变量即可，更多查询实例列表API的详情，请参见[DescribeInstances](/intl.zh-CN/API参考/实例/DescribeInstances.md)。

```text
def list_instances():
    request = DescribeInstancesRequest()
    response = _send_request(request)
    if response is not None:
        instance_list = response.get('Instances').get('Instance')
        result = map(_print_instance_id, instance_list)
        logging.info("current region include instance %s", result)
def _print_instance_id(item):
    instance_id = item.get('InstanceId')
    return instance_id
```

输出结果示例如下：

```text
current region include instance [u'i-bp165p6xk2tmdhj0****', u'i-bp1a01zbqmsun5odo****'']
```

更多查询资源的API请参见[API概览](/intl.zh-CN/API参考/API概览.md)。例如，您可以尝试查询磁盘列表（[DescribeDisks](/intl.zh-CN/API参考/磁盘/DescribeDisks.md)），替换入参对象为DescribeDisksRequest并定制相关变量即可。

