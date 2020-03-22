### [中文](./unit_api.md)

# Unit Trading API

* [API Summary](#open-api)

* [Limited Control](#open-apilimited)

* [Create API Key](#open-apisecret)

* [Code Example](#open-apicode)

* [Status Code](#open-apistatuscode)

* [Unit Trading List API](#open-apiunitlist)
  * [Get Trading All Pairs List](#open-apiunitlist-unitsymbollist)
  * [Available Balance Of User](#open-apiunitlist-unitaccountbalancelist)
  * [Change Margin During Trading](#open-apiunitlist-unitaccounttransfermargin)
  * [Place Order](#open-apiunitlist-unitorder)
  * [Cancel Order](#open-apiunitlist-unitremove)
  * [Batch Cancel Orders](#open-apiunitlist-unitremoveall)
  * [Closing](#open-apiunitlist-unitclose)
  * [Before One Of Limit Order](#open-apiunitlist-unitactiveorders)
  * [History Of Limit Order](#open-apiunitlist-unitorderhistory)
  * [Order Of Filled](#open-apiunitlist-unitaccountorderfills)

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

## <span id="open-apicode">Code Example</span> 
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

## <span id="open-apiunitlist">Unit Trading List API </span>

### <span id="open-apiunitlist-unitsymbollist">Get Trading All Pairs List POST /unit/symbol/list</span>
- Parameters
 - site Website 1:ix,2:ixx
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
  - id Unique ID 
  - name Contract name, transaction pair of BTC
  - symbol Trading pairs for example:UNIT_ETH
  - product UNIT
  - currency Currency(ETH,EOS)
  - amount_scale Quantity accuracy
  - price_scale Price accuracy
  - value_scale Value accuracy
  - max_amount Max order quantity
  - max_total Max order amount
  - min_amount Min order quantity
  - min_total Min order amount
  - state 1 available 0 unavailable
  - make_rate maker rate
  - take_rate taker rate
  - max_leverage Max leverage
  - multiplier Multiplier (In Unit trading the parameter multiplier  value is 1,this parameter will be ignored.)
  - im Base IM
  - mm Base MM
  - base_risk Basic risk limit
  - gap_risk Incremental risk limit 
  - max_risk Maximum risk limit 
  - site Website 1:ix,2:ixx
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/symbol/list -d 'nonce=1536826456'
```

	
### <span id="open-apiunitlist-unitaccountbalancelist">Available Balance Of User POST /unit/account/balance/list</span>
- Parameters
  - user_id User id
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
  - user_id User ID
  - currency Currency(ETH,EOS)
  - available Net asset value
  - name Trading pairs name(EHTUSD,EOSUSD)
  - available_balance Available
  - holding Number of current positions 
  - price Opening price
  - leverage Leverage
  - margin_user User set position margin 
  - margin_position Margin position
  - margin_delegation Margin delegation
  - unrealized Unrealized profit and loss 
  - realized Realized profit and loss
  - risk_limit Risk limit
  - liq_price Liquidation price 
  - mark_price Mark price
  - adl ADL Value
  - future_close_id Closed trigger ID
  - close_position_price Closed trigger price
  - future_tp_id Stop profit trigger ID
  - future_sl_id Stop loss trigger ID
  - tp_price Stop profit trigger price
  - sl_price Stop loss trigger price
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/account/balance/list -d 'nonce=1536826456'
```
	
### <span id="open-apiunitlist-unitaccounttransfermargin">Change Margin During Trading POST /unit/account/transfer_margin</span>
- Parameters
  - user_id User ID
  - name Trading pairs name(EHTUSD,EOSUSD)
  - amount Increase or decrease in amount
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data See of details /unit/account/balance/list
  - message
- Field Description
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/account/transfer_margin -d 'nonce=1536826456&name=EHTUSD&amount=1'
```

## Orders
### <span id="open-apiunitlist-unitorder">Place Order POST /unit/order</span>
- Parameters
  - user_id
  - name Trading pairs name(EHTUSD,EOSUSD)
  - amount Amount
  - price Price
  - type 1:Limited price 2:Market price 3:limit price stop loss 4:Market price stop loss  5:Limit profit  6:Market price surplus 
  - side Side of order 2:sell 1:buy
  - passive  Passive entrustment or not (0:no 1:yes)
  - trigger_price Trigger price
  - trigger_type Trigger type 0:default 1:Offer price 2:Mark price 3:Index price
  - trigger_close Actual forced-liquidation trigger price 0:no checked 1:checked
  - tp_type Interference stop trigger type  0:default 1:Mark price 2:Offer price 3:index price (If it is - 1, it means the trigger list from the whole warehouse)
  - tp_price Stop profit price
  - sl_type Stop loss trigger price 0:default 1:Mark price 2:Offer price 3:index price (If it is - 1, it means the trigger list from the whole warehouse)
  - sl_price Stop loss price
  
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/order -d 'nonce=1536826456'
```

### <span id="open-apiunitlist-unitremove">Cancel Order POST /unit/remove</span>
- Parameters
  - user_id
  - name Trading pairs name(EHTUSD,EOSUSD) 
  - order_id Order id 
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/remove -d 'nonce=1536826456&name=EHTUSD&order_id='
```

### <span id="open-apiunitlist-unitremoveall">Batch Cancel Orders POST /unit/remove_all</span>
- Parameters
  - user_id
  - trigger 0:Activity limited 1:Limited to loss
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/remove_all -d 'nonce=1536826456&trigger=0'
```

### <span id="open-apiunitlist-unitclose">Closing POST /unit/close</span>
- Parameters
  - user_id
  - name Trading pairs name(EHTUSD,EOSUSD)
  - price Closed position price
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/close -d 'nonce=1536826456&name=EHTUSD&price='
```

### <span id="open-apiunitlist-unitactiveorders">Before One Of Limit Order POST /unit/activeorders</span>
- Parameters
  - user_id
  - page
  - size
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
    - id Order ID
  	- name Trading pairs name(EHTUSD,EOSUSD)
    - amount Amount
    - price Trigger price
    - total Traded total
    - executed Traded amount
    - state Status 1 No transaction in commission  2 Partial transaction in commission 
    - create_time Time of order creation
    - update_time Time of filled
    - tp_type Interference stop trigger type  0:default 1:Mark price 2:Offer price 3:index price
    - tp_price Stop profit price
    - sl_type Stop loss trigger price 0:default 1:Mark price 2:Offer price 3:index price 
    - sl_price Stop loss price
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/activeorders -d 'nonce=1536826456&page=1&size=10'
```

### <span id="open-apiunitlist-unitorderhistory">History Of Limit Order POST /unit/orderhistory</span>
- Parameters
  - user_id
  - name Trading pairs(EHTUSD,EOSUSD)（Closed position price）default all
  - page
  - size
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
    - id Order ID
 	- name Trading pairs name(EHTUSD,EOSUSD)
    - amount Amount
    - price Limited price
    - total Traded quantity 
    - executed Quantity of order filled
    - state Status 1 No transaction in commission  2 Partial transaction in commission 3:Fully Filled, 4:Canceled, 5:Cancel Filled, 6: Part of the transaction at market price , 7: Part of the market price was sold out 
    - create_time Time of order creation
    - update_time Time of filled
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/orderhistory -d 'nonce=1536826456&name=EHTUSD&page=1&size=10'
```

### <span id="open-apiunitlist-unitaccountorderfills">Order Of Filled POST /unit/account/orderfills</span>
- Parameters
  - user_id
  - page
  - size
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
    - id Unique ID
    - origin 1:Filled Orders,2:Liquidation Orders,3: Capital rate ,4:ADL partial liquidation
    - order_id Order ID
    - uid User ID
    - name Trading pairs name(EHTUSD,EOSUSD)
    - side 1:buy 2:sell
    - type 1:Limited price 2:Market price 3:limit price stop loss 4:Market price stop loss  5:Limit profit  6:Market price surplus 
    - price Traded price
    - amount Traded amount
    - amount_total Authorized amount
    - amount_last Last traded amount
    - total Traded total
    - fee Fee
    - create_time Time of order creation
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/unit/account/orderfills -d 'nonce=1536826456&page=1&size=10'
```
