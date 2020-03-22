### [中文](./mix_api.md)

# Mix Trading API

* [API Summary](#open-api)

* [Limited Control](#open-apilimited)

* [Create API Key](#open-apisecret)

* [Code Example](#open-apicode)

* [Status Code](#open-apistatuscode)

* [Mix Trading List API](#open-apimixlist)
  * [Get Trading All Pairs List](#open-apimixlist-mixsymbollist)
  * [Available Balance Of User](#open-apimixlist-mixaccountbalancelist)
  * [Change Margin During Trading](#open-apimixlist-mixaccounttransfermargin)
  * [Place Order](#open-apimixlist-mixorder)
  * [Cancel Order](#open-apimixlist-mixremove)
  * [Batch Cancel Orders](#open-apimixlist-mixremoveall)
  * [Closing](#open-apimixlist-mixclose)
  * [Before One Of Limit Order](#open-apimixlist-mixactiveorders)
  * [History Of Limit Order](#open-apimixlist-mixorderhistory)
  * [Order Of Filled](#open-apimixlist-mixaccountorderfills)

-----------

## <span id="open-api">API Summary </span>

- All trading API request use HTTP POST 
- All trading API need key/secret, you can create the key/secret with IXX Official website
- Add version = '2.0' in the requested header
- Add key/sign in the requested header，the sign calculation method : sign=hash('sha256', $post_data.$secret)
- The parameter of nonce in the requested is current system time, Bar size in seconds, No earlier / no later than 10 seconds of the current system 
- The fastest access frequency is 100ms interval
- You must add from = 'ixx' in the requested header when you visit the currency pairs of IXX site

## <span id="open-apisecret">Limited Control </span>  
We control the API request rate limitts, Please refer to the interface details for specific frequency parameters.

For requests to the API, the following headers will be returned:
```
"X-ratelimit-limit: 1000"
"X-ratelimit-next: 500"
```
X-ratelimit-limit is the rate control interval of the current interface. The specific parameters are different due to different interfaces
If you've been limited by the rate, you'll receive a 403 response, along with an extra header x-ratelimit-next, which means the amount of time you need to wait before retrying
 


## <span id="open-apisecret">Create API Key </span>

### Create an API Key
The API access to users is obtained in the personal Center -> My API of the website. Click to apply for API, where API key is the access key provided by IX to API users, and API secret is the private key used to sign the request parameters.
Official website: www.ix.com
Standby domain website: www.ixex.io

**_Note: do not disclose these two parameters to anyone. These two parameters are related to the security of your account._**

###  Request authentication 
When request the API, you need to provide the API key as the identity of each request, and sign the request data through secret

#### Public Parameters

Field name |  Field definition  | Field type | Is it necessary | default | parameter type | description
 :-: | :-: | :-: | :-: | :-: | :-: | :-: 
key | Create API_KEY on the platform |  string | yes | nothing | http header |  For identification 
sign | Signature information |  string | yes | nothing |  http header | Signature information formed according to certain rules
version| api version| string | yes | 2.0 | http header | Used to differentiate API versions 
nonce |  Timestamp when the request is initiated, in seconds  | string | yes | nothing |  http body | nonce no earlier / later than current system time 10 seconds


 ####  How to sign 
 1. The interface parameters of the corresponding business and the public parameters except sign are spliced in the form of HTTP get request, as follows
 
 ``` php
$post_data = 'leverage=100&symbol=BTCUSD&nonce=1542434791';

 ```
 
 2. Sign the spliced string 
 ``` php
$secret = 't7T0YlFnYXk0Fx3JswQsDrViLg1Gh3DUU5Mr';
$sign = hash('sha256', $post_data.$secret)
 // sign = 670e3e4aa32b243f2dedf1dafcec2fd17a440e71b05681550416507de591d908
 ```
 
 3. Attach the key and sign parameters to the header to send the HTTP request 
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
headers = {'Content-Type': 'application/json', 'version': '2.0', 'key': key, 'sign': sign}
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
$headers = ['Content-Type' => 'application/json', 'version'=> '2.0', 'key' => $key, 'sign' => $sign];
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

## <span id="open-apimixlist">Mix Trading List API </span>

### <span id="open-apimixlist-mixsymbollist">Get Trading All Pairs List POST /mix/symbol/list</span>
- 参数
 - site 站点 1:ix,2:ixx
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
  - id 唯一编号
  - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT),永续合约接口里传的currency参数和symbol参数都改成这个合约名name做为参数
  - symbol 交易对名字 例:MIX_ETH
  - product MIX
  - currency 币种(BTC,ETH,EOS)
  - amount_scale 数量精度
  - price_scale 价格精度
  - min_amount 最小下单数量
  - max_amount 最大下单数量
  - im 基础 IM
  - mm 基础 MM
  - make_rate maker费率
  - take_rate taker费率
  - state 1可用 0不可用
  - site 站点 1:ix,2:ixx
  - multiplier 之前的乘数 现在用来表示单张合约价值
    - value_scale 价值精度
    - max_total 最大下单价值
    - min_total 最小下单价值
    - base_risk 基数风险限额
    - gap_risk 风险限额递增值
    - max_risk 最大风险额度
    - max_leverage 最大杠杆
    - leverages 所用杠杆
- 限定访问间隔时间
	-	1000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/symbol/list -d 'nonce=1536826456'
```

	
### <span id="open-apimixlist-mixaccountbalancelist">Available Balance Of User POST /mix/account/balance/list</span>
- 参数
  - user_id 用户id
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
  - user_id 用户id
  - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT)
  - available 资产净值(总余额)
  - available_balance 可用余额
  - currency 币种(ETH,EOS)
  - rate 汇率
  - side 1做多 2做空
  - holding 目前仓位数量
  - price 开仓价格
  - leverage 杠杆倍数
  - margin_user 用户设置的仓位保证金
  - margin_position 仓位保证金
  - margin_delegation 委托保证金
  - unrealized 未实现盈亏
  - realized 已实现盈亏
  - risk_limit 风险限额
  - liq_price 强平价格
  - mark_price 标记价格
  - adl ADL值
  - future_close_id 平仓委托ID
  - close_position_price 平仓委托价格
  - future_tp_id 止盈委托ID
  - future_sl_id 止损委托ID
  - tp_price 止盈委托价格
  - sl_price 止损委托价格
- 限定访问间隔时间
	-	1000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/account/balance/list -d 'nonce=1536826456'
```
	
### <span id="open-apimixlist-mixaccounttransfermargin">Change Margin During Trading POST /mix/account/transfer_margin</span>
- 参数
  - user_id 用户id
  - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT)
  - side 1做多 2做空
  - amount 增加或减少数量
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data 详见 /mix/account/balance/list
  - message
- 字段说明
- 限定访问间隔时间
	-	1000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/account/transfer_margin -d 'nonce=1536826456&name=BTCUSDT&side=1&amount=1'
```

## 订单相关
### <span id="open-apimixlist-mixorder">Place Order POST /mix/order</span>
- 参数
  - user_id
  - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT)
  - currency 币种(USDT,BTC,ETH,EOS)
  - side 订单类型 1开多(买入开多) 2开空(卖出开空) 3平多(卖出平多) 4平空(买入平空)
  - amount 数量必须大于0
  - price 价格必须大于0
  - type 下单类型 1 限价 2市价 3止盈止损
  - passive 是否被动委托 0否 1是
  - trigger_price 触发价格
  - trigger_type 触发类型 0默认 1盘口价格 2标记价格 3指数价格
  - tp_type 止盈触发类型 0默认 1盘口价格 2标记价格 3指数价格 如果是-1的话代表从仓位里下的触发单
  - tp_price 止盈价格
  - sl_type 止损触发类型 0默认 1盘口价格 2标记价格 3指数价格 如果是-1的话代表从仓位里下的触发单
  - sl_price 止损价格
  
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
- 限定访问间隔时间
	-	1000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/order -d 'nonce=1536826456'
```

### <span id="open-apimixlist-mixremove">Cancel Order POST /mix/remove</span>
- 参数
  - user_id
  - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT) 
  - order_id 订单id 
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
- 限定访问间隔时间
	-	100毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/remove -d 'nonce=1536826456&name=BTCUSDT&order_id=1'
```

### <span id="open-apimixlist-mixremoveall">Batch Cancel Orders POST /mix/remove_all</span>
- 参数
  - user_id
  - trigger 0活动委托 1止损委托
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
- 限定访问间隔时间
	-	5000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/remove_all -d 'nonce=1536826456&trigger=1'
```

### <span id="open-apimixlist-mixclose">Closing POST /mix/close</span>
- 参数
  - user_id
  - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT)
  - side 1做多 2做空
  - price 平仓价格
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
- 限定访问间隔时间
	-	1000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/close -d 'nonce=1536826456&name=BTCUSDT&side=1&price=13.00'
```

### <span id="open-apimixlist-mixactiveorders">Before One Of Limit Order POST /mix/activeorders</span>
- 参数
  - user_id
  - page
  - size
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
  - 订单id
    - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT)
    - currency 币种(USDT,BTC,ETH,EOS)
    - amount 数量
    - price 委托价格
    - total 已成交额
    - executed 已成交量
    - state 状态 1委托中未成交 2委托中部分成交
    - create_time 下单时间
    - update_time 更新时间
    - tp_type 止盈触发类型 0默认 1盘口价格 2标记价格 3指数价格
    - tp_price 止盈价格
    - sl_type 止损触发类型 0默认 1盘口价格 2标记价格 3指数价格
    - sl_price 止损价格
- 限定访问间隔时间
	-	1000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/activeorders -d 'nonce=1536826456&page=1&size=10'
```

### <span id="open-apimixlist-mixorderhistory">History Of Limit Order POST /mix/orderhistory</span>
- 参数
  - user_id
  - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT)（非必传）默认全部
  - page
  - size
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
  - 订单id
    - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT)
    - amount 数量(张)
    - price 委托价格
    - total 已成交额
    - executed 已成交量(张)
    - state 状态 1:委托中未成交, 2:委托中限价部分成交, 3:完全成交, 4:撤单全部, 5:撤单部分成交, 6:市价部分成交, 7:市价部分成交盘口全被吃空
    - create_time 下单时间
    - update_time 更新时间
- 限定访问间隔时间
	-	1000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/orderhistory -d 'nonce=1536826456&name=BTCUSDT&page=1&size=10'
```

### <span id="open-apimixlist-mixaccountorderfills">Order Of Filled POST /mix/account/orderfills</span>
- 参数
  - user_id
  - page
  - size
- 返回值
  - code(200表示正常读取data内容，非200则表示失败读取message失败信息)
  - data
  - message
- 字段说明
  - id 成交单ID
  - origin 1成交单,2强平单,3资金费率,4ADL减仓
  - order_id 订单ID
  - uid 用户ID
  - name 合约名(BTCUSDT,EHTUSDT,EOSUSDT)
  - side 方向 1开多(买入开多) 2开空(卖出开空) 3平多(卖出平多) 4平空(买入平空)
  - type 1限价 2市价 3止盈止损
  - price 成交价
  - amount 成交量
  - amount_total 委托数量
  - amount_last 成交量
  - total 成交额
  - fee 手续费
  - create_time 成交时间
- 限定访问间隔时间
	-	1000毫秒
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/mix/account/orderfills -d 'nonce=1536826456&page=1&size=10'
```
