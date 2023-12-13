# TronEnergize公共Rest API
## TronEnergize公共Rest API概述：

TronEnergize公共Rest API为开发人员提供了与TronEnergize能源市场进行编程交互的强大工具集。该API允许用户访问基本市场数据，管理能源订单并执行交易操作。

## 主要特点：

- 市场数据：检索最低价格和可用能源以做出明智决策。
- 订单管理：查看活动订单列表，创建订单并检查现有订单的状态。
- 交易操作：为买单创建未签名的RAW交易，创建和支付来自内部钱包的订单。

## 身份验证：

通过API密钥和密钥对您的交易进行安全保护。

## HTTP返回码：

通过清晰的HTTP返回码和错误信息轻松解读响应。

## 使用便捷：

JSON响应使集成无缝，使开发人员能够构建自定义界面并自动化购买流程。

探索TronEnergize API，提升您在Tron能源市场的体验！

## 通用API信息
* 基本终端点为：**https://tronenergize.com/**
* 尼罗测试网络终端点为：**https://nile.tronenergize.com/**
* 所有终端点都返回JSON对象或数组。
* 数据以**升序**返回。最旧的在前，最新的在后。
* 所有时间和时间戳相关字段以**秒**为单位。

## HTTP返回码

* HTTP `4XX`返回码用于格式错误的请求；问题在发送方。
* HTTP `403`返回码用于违反WAF限制（Web应用防火墙）的情况。
* HTTP `429`返回码用于突破请求速率限制的情况。
* HTTP `418`返回码用于在接收到`429`返回码后继续发送请求的IP自动封禁的情况。
* HTTP `5XX`返回码用于内部错误；重要的是**不要**将其视为操作失败；执行状态为**未知**，可能是成功的。


## 错误代码。* 任何端点都可能返回错误

下面是示例负载：
```javascript
{
  "code": 404,
  "msg": "无效的账户。"
}
```

代码 | 原因 | 解决方案
--- | --- | ---
200 | 成功 | 
400 | 请求体参数格式错误 | 通常出现在POST请求中，请检查json结构是否正确
403 | 请求频率受限 | 每秒访问频率限制为5qps。请减少请求频率或添加新的密钥
404 | 缺少必需的参数 | 请仔细检查文档和实际请求的参数类型和值是否合理有效
444 | 数据不存在 | 
500 | 操作失败，请稍后重试 | 
502 | 余额不足 | 
505 | 未激活的地址 | 

## 端点的常规信息
* 对于`GET`端点，参数必须作为`查询字符串`发送。


# 公共 API 端点

## 常规端点
### 测试连接性
```
GET /api/v1/ping
```
测试与 Rest API 的连接性。

**参数：**
无

**响应：**
```javascript
{"time": 1695219834}
```
### 最低价格
```
GET /api/v1/minprices
```


**参数：**
无

**响应：**
```javascript
{
  "time": 1695219834,
  "data": {
      "1200":30,
      "7200":30
    },
}
```
### 可用能源
```
GET /api/v1/available
```

**参数：**
无

**响应：**
```javascript
{
  "time": 1695219834,
  "data": {
       "energy":50000000,
    }
}
```

## 市场数据端点
### 订单列表
```
GET /api/v1/market
```
此端点允许任何人获取所有活动订单的列表

**参数：**
无

**响应：**
```javascript
{
  "time": 1596477571, 
  "data": [
      {
        "id": 787, 
        "payout": 777.5, 
        "stake": 77000, 
        "energy": 1000000, 
        "price": "45 太阳/天", 
        "apy": "77%", 
        "duration": "7天",
        "rawduration": 201600,
        "receiver": "TLwpQv9N6uXZQeE4jUudLPjcRffbXXAuru"
      }
      ...
    ]
```

## 交易端点
### 为购买订单创建未签名的原始交易。
```
GET /api/v1/neworder
```**参数：**
名称 | 类型 | 必填 | 描述
------------ | ------------ | ------------ | ------------
buyer | STRING | 是 | 资源购买地址
energyamount | integer | 是 | SUN中的能量数量
price | integer | 是 | 最低30
duration | STRING | 是 | 支持1d、3d、7d、14d、1h和6h

**响应：**
```javascript
{
  "time": 1596477571, 
  "data":{
           "tx": {"visible": false, "txID": "6debe75130870086575f90792f.. ..f8aa9b031"},
           "amount": 1.28
         }
}
```

### 创建订单。
```
POST /api/v1/neworder
```

**参数：**
名称 | 类型 | 必填 | 描述
------------ | ------------ | ------------ | ------------
buyer | STRING | 是 | 资源购买地址
receiver | STRING | 是 | 资源接收地址
energyamount | integer | 是 | SUN中的能量数量
price | integer | 是 | 最低30
duration | STRING | 是 | 支持1d、3d、7d、14d、1h和6h
txid | STRING | 是 | 已签名并广播的交易哈希

**响应：**
```javascript
{
  "time": 1596477571, 
  "data": {
        "id": 787, 
        "payout": 777.5, 
        "stake": 77000, 
        "energy": 1000000, 
        "price": "45 SUN/Day", 
        "apy": "77%", 
        "duration": "7 days",
        "rawduration": 201600,
        "receiver": "TLwpQv9N6uXZQeE4jUudLPjcRffbXXAuru"
      }
}
```

### 订单状态。
```
GET /api/v1/status/[orderId]
```
该端点返回订单的状态（'active'、'filled'、'canceled'）。

**参数：**
名称 | 类型 | 必填 | 描述
------------ | ------------ | ------------ | ------------
orderid | STRING | 是 | 订单ID。

**响应：**
```javascript
{
  "time": 1596477571, 
  "data": [{
        "id": 787, 
        "payout": 777.5, 
        "stake": 77000, 
        "energy": 1000000, 
        "price": "45 SUN/Day", 
        "apy": "77%", 
        "duration": "7 days",
        "rawduration": 201600,
        "receiver": "TLwpQv9N6uXZQeE4jUudLPjcRffbXXAuru",
        "status": "active",
        "delegations": [".{
```json
{
  "txid": "7089e44e52e...513eba2bdfa78",
  "energy": 1000000
},
...
]
}]
}
```
# 鉴权
对TronEnergize API进行身份验证需要有效的API密钥。
您的API密钥标识您的账户（将其视为用户名），而API密钥验证您的账户（将其视为密码）。请按照以下说明保护您的API密钥和API密钥：

* 不要在API请求中发送API密钥。只发送API密钥。
* 不要与任何人分享您的API密钥或API密钥。
* 不要将API密钥提交到源代码控制系统（如github）中。
* 如果您丢失了API密钥或密钥，请立即联系Tronenergize支持。

请注意，如果您的API密钥泄漏，您的资金将面临风险。

# 端点安全类型
* 每个下一个端点都有一个安全类型，确定您将如何与其交互。这将显示在端点的名称旁边。
* 如果未指定安全类型，则默认安全类型为NONE。
* API密钥通过`X-TR-APIKEY`标头传递到Rest API中。
* API密钥和密钥区分大小写。

# 签名端点安全
* “签名”端点需要发送一个额外的参数，“签名”到`查询字符串`或`请求体`或`标题`中。
* “签名”不区分大小写。

## 定时安全
* “签名”端点还需要发送一个名为“时间戳”的参数，该参数应是创建和发送请求的第二个时间戳。

## 请求签名
身份验证调用要求您在每个请求中都发送一个请求签名。每个请求都应重新生成该签名。

### 示例API密钥/ API密钥：
Key | Value
------------ | ------------
`apiKey` | API_my6mqen60xpwnewo2efckxtz
`secretKey` | S2808_61xkvn_bprxy5_s3szia

## POST /api/v1/order的签名端点示例。以下是一个逐步示例，演示如何使用`echo`、`openssl`和`curl`在Linux命令行上发送有效签名的负载。

参数 | 值
------------ | ------------
`receiver` | TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t
`energyamount` | 32000
`price` | 35
`duration` | 1d
`timestamp` | 1695219834

#### 示例1：作为请求体
* **请求体：** receiver=TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t&energyamount=32000&price=35&duration=1d&timestamp=1695219834
* **HMAC SHA256 签名：**

    ```
    [linux]$ echo -n "receiver=TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t&energyamount=32000&price=35&duration=1d&timestamp=1695219834" | openssl dgst -sha256 -hmac "S2808_61xkvn_bprxy5_s3szia"
    (stdin)= 4e79a51a5bb2df5d3b894095d27d994c02f9f7705bc95eb4e4296b08b0af35fb
    ```

* **curl 命令：**

    ```
    (HMAC SHA256)
    [linux]$ curl -H "X-TR-APIKEY: API_my6mqen60xpwnewo2efckxtz" -X POST 'https://nile.tronenergize.com/api/v1/order' -d 'receiver=TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t&energyamount=32000&price=35&duration=1d&timestamp=1695219834&signature=4e79a51a5bb2df5d3b894095d27d994c02f9f7705bc95eb4e4296b08b0af35fb'
    ```

#### 示例2：作为查询字符串
* **查询字符串：** receiver=TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t&energyamount=32000&price=35&duration=1d&timestamp=1695219834
* **HMAC SHA256 签名：**

    ```
    [linux]$ echo -n "receiver=TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t&energyamount=32000&price=35&duration=1d&timestamp=1695219834" | openssl dgst -sha256 -hmac "S2808_61xkvn_bprxy5_s3szia"
    (stdin)= 4e79a51a5bb2df5d3b894095d27d994c02f9f7705bc95eb4e4296b08b0af35fb
    ```

* **curl 命令：**

    ```
    (HMAC SHA256)
    [linux]$ curl -H "X-TR-APIKEY: API_my6mqen60xpwnewo2efckxtz" -X GET 'https://nile.tronenergize.com/api/v1/order?receiver=TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t&energyamount=32000&price=35&duration=1d&timestamp=1695219834&signature=4e79a51a5bb2df5d3b894095d27d994c02f9f7705bc95eb4e4296b08b0af35fb'
    ```

### 创建订单并从内部钱包付款。
```
POST /api/v1/order
```

**请求头：**Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
X-TR-APIKEY | 字符串 | 是 | 您的 API 密钥


**参数:**
Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
receiver | 字符串 | 是 | 资源接收地址
energyamount | 整数 | 是 | SUN 中的能量数量
price | 整数 | 是 | 最低值为 30
duration | 字符串 | 是 | 支持 1 天、3 天、7 天、14 天、1 小时和 6 小时
timestamp | 整数 | 是 | 当前时间戳（以秒为单位）
signature | 字符串 | 是 | HMAC SHA256 签名

**响应:**
```javascript
{
  "time": 1596477571,
  "data":{
        "id": 787,
        "payout": 777.5,
        "stake": 77000,
        "energy": 1000000,
        "price": "45 SUN/Day",
        "duration": "7 天",
        "txid": "8b317f47d79547a2...24cc6317d84d9",
        "receiver": "TLwpQv9N6uXZQeE4jUudLPjcRffbXXAuru"
      }
}
```