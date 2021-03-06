# 测试接入API

baseUrl: https://sandbox-walletapi.onethingpcs.com
## 1. 开发者注册

**功能**

测试注册接口，使用email地址。用户第一次注册，产生service_id/secret通过邮件形式发送给开发者。如果邮箱地址已经注册，返回错误码。

**请求**

方法：POST  
URL: /api/linktest/regist  
BODY: JSON

参数说明：

|参数名|参数类型|必须|说明|
|-|-|-|-|
|email|string|是|测试使用的email，主要用于接收测试消息|
|callback|string|否|扩展字段|

**响应**

|参数名|参数类型|必须|说明|
|-|-|-|-|
|code|int|是|错误码，0：成功 非0：失败|
|msg|string|是|错误提示信息|
|data|object|否|请求返回数据|

## 2. 测试账号充值

**功能**

申请将链克充值到指定的账户，开发者给自有的账户充值后用于测试活动。
每申请一次转入1个链克。

**请求**

方法：POST  
URL: /api/linktest/recharge  
BODY: JSON

参数说明：

|参数名|参数类型|必须|说明|
|-|-|-|-|
|email|string|是|测试使用的email，主要用于接收测试消息|
|address|string|是|需要充值的账号地址 0xaaaaaa...|
|sign|string|是|签名md5(email=xxx&address=xxx&secret=xxx), xxx填写请求的实际值|

**响应**

|参数名|参数类型|必须|说明|
|-|-|-|-|
|code|int|是|错误码，0：成功 非0：失败|
|msg|string|是|错误提示信息|
|data|object|否|请求返回数据|

## 3. 合约发布

**功能**

用户发布合约到测试环境，用户提供编译后的字节码。

**请求**

方法：POST  
URL: /api/linktest/contract/deploy  
BODY: JSON

参数说明：

|参数名|参数类型|必须|说明|
|-|-|-|-|
|email|string|是|测试使用的email，主要用于接收测试消息|
|bytecode|string|是|编译后的合约字节码，十六进制ABI格式 0x123123123.....|
|params|string|是|构造函数初始化参数，十六进制ABI格式|
|sign|string|是|签名md5(email=xxx&bytecode=xxx&params=xxx&secret=xxx), xxx填写请求的实际值|

**响应**

|参数名|参数类型|必须|说明|
|-|-|-|-|
|code|int|是|错误码，0：成功 非0：失败|
|msg|string|是|错误提示信息|
|data|object|否|请求返回数据|

**data**

|参数名|参数类型|必须|说明|
|-|-|-|-|
|id|string|是|合约部署产生的id,通过这个id可查询合约部署后的地址|

## 4. 合约地址查询

**功能**

通过合约部署产生的id查询合约账户地址。

**请求**

方法：POST  
URL: /api/linktest/contract/address  
BODY: JSON

参数说明：

|参数名|参数类型|必须|说明|
|-|-|-|-|
|email|string|是|测试使用的email，主要用于接收测试消息|
|id|int|是|合约部署返回的id|
|sign|string|是|签名md5(email=xxx&id=xxx&secret=xxx), xxx填写请求的实际值|

**响应**

|参数名|参数类型|必须|说明|
|-|-|-|-|
|code|int|是|错误码，0：成功 非0：失败|
|msg|string|是|错误提示信息|
|data|object|否|请求返回数据|

**data**

|参数名|参数类型|必须|说明|
|-|-|-|-|
|address|string|是|账户地址|

## 5. 查询最近部署的合约地址

**功能**

查询最近部署的合约id和合约地址。

**请求**

方法：POST  
URL: /api/linktest/contract/last  
BODY: JSON

参数说明：

|参数名|参数类型|必须|说明|
|-|-|-|-|
|email|string|是|测试使用的email，主要用于接收测试消息|
|sign|string|是|签名md5(email=xxx&secret=xxx), xxx填写请求的实际值|

**响应**

|参数名|参数类型|必须|说明|
|-|-|-|-|
|code|int|是|错误码，0：成功 非0：失败|
|msg|string|是|错误提示信息|
|data|object|否|请求返回数据|

**data**

|参数名|参数类型|必须|说明|
|-|-|-|-|
|id|string|是|合约ID|
|address|string|是|账户地址|

## 6. 唤起链克口袋协议

**唤起协议**

合约执行必须通过链克口袋，由第三方应用唤醒链克APP或者通过链克APP扫码。   
链克钱包唤起协议为scheme://host?

**请求参数说明**

| 参数      | 类型   | 必须 | 说明                                                                                           |
| --------- | ------ | ---- | ---------------------------------------------------------------------------------------------- |
| scheme    | string | 是   | 链克口袋scheme otst                                                                            |
| host      | string | 是   | 链克口袋host值为：payment                                                                      |
| tx-data   | byte[] | 是   | Base64编码  主要包含支付的订单信息，key=value形式，以&连接                                     |
| resource  | byte[] | 是   | Base64编码 来源app                                                                             |
| cb-data   | byte[] | 是   | Base64编码（可选）支付调起者需要钱包回传的额外信息                                             |
| x-source  | string | 是   | 源app scheme eg. wky-app(可选 iOS调用回调，安卓不处理)                                         |
| x-success | string | 是   | 成功时回调。不填则不返回源app(可选 iOS调用回调，安卓不处理)                                    |
| x-error   | string | 是   | 失败时回调。不填则不返回源app。包含参数:error-Code和errorMessage(可选 iOS调用回调，安卓不处理) |
| x-cancel  | string | 是   | 取消时回调。不填则不返回源app(可选 iOS调用回调，安卓不处理)                                    |

**tx-data参数说明**

| 参数       | 类型   | 必须 | 说明                                                                                             |
| :--------- | :----- | :--- | :----------------------------------------------------------------------------------------------- |
| desc       | string | 是   | 合约执行描述，必须带上“合约执行-”前缀                                                          |
| callback   | string | 否   | 做url编码，后台回调链接                                                                          |
| to         | string | 是   | 转出钱包地址                                                                                     |
| value      | string | 是   | 玩客币数量（单位 wei）                                                                           |
| prepay_id  | string | 是   | 预支付订单号                                                                                     |
| service_id | string | 是   | 业务id，找交易中心申请service_id和签名秘钥（向后台获取） 预留字段（app提交到geth时转化为整形）   |
| data       | string | 是   | 执行的合约代码，十六进制字符串，以0x开头。包含函数地址和调用参数。只发起转账，这个内容为空。     |
| gas_limit  | string | 是   | 最大的支付Gas，用于计算合约执行手续费 （app提交到geth时转化为整形）                              |
| tx_type    | string | 是   | 交易类型，取值contract标识合约，第三方交易tx_third，第三方交易支持缺失默认值，兼容已有的客户端。 |
| sign       | string | 是   | 交易签名 sign=md5(sha512(callback=xxx&prepay_id=xxx&service_id=xxx&to=xxx&value=xxx&key=私钥))   |

**响应**
1. 成功

使用x-success为支付成功后返回的参数。字段由payment业务定义。

| 参数名 | 类型   | 必须 | 说明                                            |
| ------ | ------ | ---- | ----------------------------------------------- |
| hash   | string | 是   | 发起transaction成功后的hash，可拥有后续状态查询 |

2. 失败

使用x-error的url返回。字段由x-callback-url定义

| 参数名       | 类型   | 必须 | 说明                     |
| ------------ | ------ | ---- | ------------------------ |
| error-Code   | string | 是   | 错误码。注意是error-Code |
| errorMessage | string | 是   | 错误信息。               |

3. 取消

使用x-cancel的url返回

## 7. 合约constant方法查询

**该方法用来做合约方法查询。**
该方法是用来执行指定的message call，不会创建交易，因此该方法的调用是试用不会改变区块链状态数据库的，合约中的constant方法应该调用这个rpc方法。

**请求**
params参数：

| 字段    | 类型  | 约束 |  备注 |
| ------- | ----- | ---- | ---- |
| object	|对象	  | 	  | 交易调用对象 |
| quantity\|tag |	整型或者字符串 |	integer \| "latest" \| "earliest" \| "pending" | 	区块号，"latest"，"earliest" 或 "pending"，详见 [default block parameter](https://github.com/ethereum/wiki/wiki/JSON-RPC#the-default-block-parameter) | 

**object详细**
1. object - 交易调用对象
 
| 字段 | 类型 | 约束 | 大小 | 备注 |
| ---- | ----- | ---- | ---- | ---- |
| from | common.Address | 可选 | 20 bytes	| 交易的发起者 |
| to	 | common.Address | 必填 | 20 bytes	| 交易指向的地址，比如合约调用方法中就是合约地址 |
| gas	 | hexutil.Big    | 可选 |          | 执行交易的所需要的gas，eth_call 一般不需要消耗gas，但是这个参数可能在一些执行操作中需要用到 |
| gasPrice | hexutil.Big | 可选	|         | 每单位的gas所需要的价格  |
| value |	hexutil.Big | 可选	 |          |	该交易要发送的值 |
| data	| hexutil.Bytes | 可选 |          | Hash of the method signature and encoded parameters. 详见 [Ethereum Contract ABI](https://github.com/ethereum/wiki/wiki/Ethereum-Contract-ABI) |
 
**响应**

| 字段 | 类型 | 约束 | 大小 | 备注 |
| ---- | ---- | ---- | ---- | ---- |
| DATA | 交易调用对象 |  |  | 合约执行结果 |