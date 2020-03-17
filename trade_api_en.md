### [中文](./trade_api.md)

# Spot Trading API

* [API Summary](#open-api)

* [Limited Control](#open-apilimited)

* [Create API Key](#open-apisecret)

* [Code Example](#open-apicode)

* [Status Code](#open-apistatuscode)

* [Spot Trading List API](#open-apitradelist)
  * [获取所有交易对的详情](#open-apitradelist-symbollist)
  * [获取余额列表](#open-apitradelist-balancelist)
  * [下单](#open-apitradelist-ordercreate)
  * [撤单](#open-apitradelist-orderremove)
  * [查询委托](#open-apitradelist-orderactive)
  * [历史订单](#open-apitradelist-orderhistory)
  * [查询活跃委托订单详情](#open-apitradelist-orderquery)

-----------

## <span id="open-api">API Summary </span>

- 所有交易API请求都使用HTTP POST
- 交易API需要在官网申请API需要的key/secret
- 请求的header里添加version = '2.0'
- ixx平台请求的header里添加from = 'ixx'
- 请求的header里添加key/sign，sign=hash('sha256', $post_data.$secret)
- 请求的nonce参数为当前系统时间戳，单位为秒，nonce不早/晚于当前系统时间10秒
- 单个接口访问频率最快为100ms间隔

## <span id="open-apisecret">Limited Control </span>  
我们对API的请求频率进行控制，具体频率参数请参考接口详情

对 API 的请求，以下标头将被返回︰
```
"X-ratelimit-limit: 1000"
"X-ratelimit-next: 500"
```
X-ratelimit-limit为当前接口的频率控制间隔,具体因接口不同而参数不同
如果你已经被频率限制，你将收到 403 响应, 以及一个额外的标头X-ratelimit-next, 它意味着你在重试前需要等待的时间
 



## <span id="open-apisecret">Create API Key </span>

### Create an API Key
用户的API权限在网站的个人中心->我的API内获取。点击申请API即可获得，其中API Key是IX提供给API用户的访问密钥，API Secret是用于对请求参数签名的私钥。
官网地址: www.ix.com
备用地址: www.ixex.io

**_注意： 请勿向任何人泄露这两个参数，这两个参数关乎您账号的安全。_**    

### 请求认证
在调用 API 时，需要提供 API Key 作为每个请求的身份识别，并且通过secret对请求数据加签

#### 公共参数

字段名 | 字段释义 | 字段类型 | 是否必填 | 默认值 | 参数类型 | 说明
 :-: | :-: | :-: | :-: | :-: | :-: | :-: 
key | 在平台申请的API_KEY |  string | 是 | 无 | http header | 用于身份识别
sign | 签名信息 |  string | 是 | 无 |  http header |按照一定规则形成的签名信息
version| api版本| string | 是 | 2.0 | http header | 用于区分api版本
nonce | 请求发起时的时间戳,单位:秒 | string | 是 | 无 |  http body | nonce不早/晚于当前系统时间10秒


 #### 如何进行签名
 1. 将对应业务的接口参数和除sign外的公共参数以http GET请求形式拼接, 如下
 
 ``` php
$post_data = 'leverage=100&symbol=BTCUSD&nonce=1542434791';

 ```
 
 2. 对拼接后的字符串进行加签
 ``` php
$secret = 't7T0YlFnYXk0Fx3JswQsDrViLg1Gh3DUU5Mr';
$sign = hash('sha256', $post_data.$secret)
 // sign = 670e3e4aa32b243f2dedf1dafcec2fd17a440e71b05681550416507de591d908
 ```
 
 3.header附加上key和sign参数，发送http请求
 
 ```http
 POST /order/active HTTP/1.1
 Content-Type: application/x-www-form-urlencoded
 key: 43f2dedf1dafcec2fd17a440e71b056815
 sign: 670e3e4aa32b243f2dedf1dafcec2fd17a440e71b05681550416507de591d908
 version: 2.0 
 
leverage=100&symbol=BTCUSD&nonce=1542434791
 
 ```


## <span id="open-apicode">Code Example </span> 
Python：
``` Python
import requests
import hashlib
import json
import time
from urllib import parse

key = 'your key'
secret = 'your secret'
url = "https://api.ixex.io/order/active"
symbol = 'BTC_USDT'
nonce = int(time.time())
payload = {'nonce': nonce, 'symbol': symbol, 'page': 1, 'size': 10}
payload_str = parse.urlencode(payload)
sign = hashlib.sha256((payload_str+secret).encode("utf-8")).hexdigest()
headers = {'Content-Type': 'application/json', 'from': 'ixx',''version': '2.0', 'key': key, 'sign': sign}
response = requests.post(url, data=json.dumps(payload), headers=headers)
```

PHP：
``` PHP
$key = 'your key'
$secret = 'your secret'
$url = "https://api.ixex.io/order/active";
$symbol = 'BTC_USDT';
$nonce = time();
$payload = ['nonce' => $nonce, 'symbol' => $symbol, 'page' => 1, 'size' => 10];
$payload_str = http_build_query($payload, '', '&');
$sign = hash('sha256', urldecode($payload_str).$secret);
$headers = ['Content-Type:application/json','from': 'ixx','version:2.0', 'key:'.$key, 'sign:' .$sign ];
$response = Requests::post($url, $headers, json_encode($payload));
```

JavaScript：
``` JavaScript
let key = 'your key'
let secret = 'your secret'
let url = "https://api.ixex.io/order/active"
let symbol = 'BTC_USDT'
let nonce = Math.floor(Date.now() / 1000)
let sign = sha256("nonce="+ nonce + "&symbol=" + symbol + "&page=" + 1 + "&size=" + 10 + secret)
$.ajax({
  url: url,
  type: 'post',
  data: {
    nonce: nonce,
    symbol: symbol,
    page: 1,
    size: 10
  },
  headers: {
    from: 'ixx'
    version: '2.0',
    key: key,
    sign: sign
  },
  ...
```


## <span id="open-apistatuscode">Status Code </span>

| Error Code        | Detailed Description    |    
| :-----            | :-----   |    
|200	            |	Sucessful|    
|400	            |	Missing params|    
|401	            |	Unauthorized|    
|403	            |	Request too frequently|    
|413	            |	Request too large|    
|500	            |	Invalid request|    
|30001	            |	Invalid symbol|    
|30002	            |	Invalid amount|    
|30003	            |	Invalid total| 
|60045	            |	Fail to freeze funds|
|60046	            |	Order failed|
|60047	            |	Too many orders in the trading cycle|
|60048	            |	Failed to get current price|

## <span id="open-apitradelist">币币交易API列表 </span>

## <span id="open-apitradelist-symbollist">获取所有交易对的详情 POST /symbol/list</span>
- 参数
  - nonce 时间戳
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data(id/name/product/price_scale/amount_scale/min_amount/max_amount/min_total/max_total/make_rate/take_rate/state)
  - message
- 字段说明
  - id ID 
  - name 交易对名称 
  - product 商品 
  - currency 货币 
  - price_scale 报价小数位 
  - amount_scale 报量小数位 
  - min_amount 最小下单量 
  - max_amount 最大下单量 
  - min_total 最小下单额 
  - max_total 最大下单额 
  - make_rate 挂单手续费率 
  - take_rate 吃单手续费率 
  - state 状态 1上线 2下线
- 示例
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/symbol/list -d 'nonce=1536826456'
```

## <span id="open-apitradelist-balancelist">获取余额列表 POST /balance/list</span>
- 参数
  - nonce 时间戳
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data(user_id/currency/available/ordering/withdrawing/quota/max_quota/rates)
  - message
- 字段说明
  - user_id 用户ID 
  - currency 货币 
  - available 可用量 
  - ordering 委托锁定量 
  - withdrawing 提币锁定量 
  - quota 当前额度 
  - max_quota 最大额度 
  - rates 币种对各法币汇率 
- 示例
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/balance/list -d 'nonce=1536826456'
```

## <span id="open-apitradelist-ordercreate">下单 POST /order/create</span>
- 参数
  - nonce 时间戳
  - symbol 交易对名称
  - side 1买 2卖
  - type 1限价 2市价
  - price 限价单时报价，市价填0
  - amount 限价单时报量，市价买单填0
  - locked 市价买时的锁定额，市价买单必填
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data(id/user_id/symbol/side/type/price/amount/locked)
  - message
- 字段说明
  - id 订单ID 
  - user_id 用户ID 
  - symbol 交易对名称 
  - side 1买 2卖 
  - type 1限价 2市价
  - price 限价单时报价
  - amount 限价单时报量
  - locked 市价买时的锁定额
- 示例
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/create -d 'nonce=1536826456&symbol=BTC_USDT&side=1&type=1&price=6000&amount=1'
```

## <span id="open-apitradelist-orderremove">撤单 POST /order/remove</span>
- 参数
  - nonce 时间戳
  - symbol 交易对名称
  - order_id 订单ID
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 示例
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/remove -d 'nonce=1536826456&symbol=BTC_USDT&order_id=123'
```

## <span id="open-apitradelist-orderactive">查询委托 POST /order/active</span>
- 参数
  - nonce 时间戳
  - symbol 交易对名称
  - page 页码
  - size 每页数量
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data(id/user_id/symbol/product/currency/side/type/price/amount/locked/executed/total/fee/state/create_time/update_time)
  - message
- 字段说明
  - id 订单ID 
  - user_id 用户ID 
  - symbol 交易对名称 
  - product 商品 
  - currency 货币 
  - side 1买 2卖 
  - type 1限价 2市价
  - price 报价
  - amount 报量
  - locked 锁定量
  - executed 成交量
  - total 成交额
  - fee 手续费
  - state 1未成交 2部分成交
  - create_time 下单时间
  - update_time 最近成交时间
- 示例
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/active -d 'nonce=1536826456&symbol=BTC_USDT&page=1&size=10'
```

## <span id="open-apitradelist-orderhistory">历史订单 POST /order/history</span>
- 参数
  - nonce 时间戳
  - page 页码
  - size 每页数量
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data(id/user_id/symbol/product/currency/side/type/price/amount/locked/executed/total/fee/state/create_time/update_time)
  - message
- 示例
- 字段说明
  - id 订单ID 
  - user_id 用户ID 
  - symbol 交易对名称 
  - product 商品 
  - currency 货币 
  - side 1买 2卖 
  - type 1限价 2市价
  - price 报价
  - amount 报量
  - locked 锁定量
  - executed 成交量
  - total 成交额
  - fee 手续费
  - state 3全部成交 4未成交撤单 5部分成交撤单 6未成交系统取消 7部分成交系统取消
  - create_time 下单时间
  - update_time 最近成交时间
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/history -d 'nonce=1536826456&page=1&size=10'
```

### <span id="open-apitradelist-orderquery">查询活跃委托订单详情 POST /order/query</span>
- 参数
  - nonce 时间戳
  - symbol 交易对名称
  - order_id 订单ID
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data(id/user_id/symbol/product/currency/side/type/price/amount/locked/executed/total/fee/state/create_time/update_time)
  - message
- 示例
- 字段说明
  - id 订单ID 
  - user_id 用户ID 
  - symbol 交易对名称 
  - product 商品 
  - currency 货币 
  - side 1买 2卖 
  - type 1限价 2市价
  - price 报价
  - amount 报量
  - locked 锁定量
  - executed 成交量
  - total 成交额
  - fee 手续费
  - state 1未成交 2部分成交
  - create_time 下单时间
  - update_time 最近成交时间
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/query -d 'nonce=1536826456&symbol=BTC_USDT&order_id=123'
```
