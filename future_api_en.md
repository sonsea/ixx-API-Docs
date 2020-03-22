### [中文](./future_api.md)

# Futures Trading API

* [API Summary](#open-api)

* [Limited Control](#open-apilimited)

* [Create API Key](#open-apisecret)

* [Code Example](#open-apicode)

* [Status Code](#open-apistatuscode)

* [Futures Trading List API](#open-apifuturelist)
  * [Get Trading All Pairs List](#open-apifuturelist-symbollist)
  * [Available Balance Of User](#open-apifuturelist-accountbalance)
  * [Change Margin During Trading](#open-apifuturelist-transfermargin)
  * [Place Order](#open-apifuturelist-order)
  * [Cancel Order](#open-apifuturelist-remove)  
  * [Closing](#open-apifuturelist-close)
  * [Before One Of Limit Order](#open-apifuturelist-activeorders)
  * [History Of Limit Order](#open-apifuturelist-orderhistory)
  * [Order Of Filled](#open-apifuturelist-orderfills)

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
|200	            |	Successful|    
|400	            |	Missing params|    
|401	            |	Unauthorized|    
|403	            |	Request too frequently|    
|413	            |	Request too large|    
|500	            |	Invalid request|    
|30001	            |	Invalid symbol|    
|30002	            |	Invalid amount|    
|30003	            |	Invalid total|    
|30101	            |	Insufficient balance|
|30011	            |	Order failed, price error of the passive order|
|30012	            |	Order failed, the max number of active order creatied is 200|
|30013	            |	The order failed, order leverage exceeded the max value|
|30014	            |	Order failed, orders qty reach top limit|

## <span id="open-apifuturelist">Futures List API </span>

### <span id="open-apifuturelist-symbollist">Get Trading All Pairs List POST /contract/symbol/list </span>
- parameters
  - nonce Timestamps
- Response 
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
  - id  Unique number ID 
  - name Contract name, transaction pair of BTC ，example BTC Perpetual Swap Positions
  - currency  Trading unit USD settlement 
  - product BTC contact
  - amount_scale Quantity accuracy
  - price_scale Price accuracy
  - value_scale Value accuracy
  - max_amount Max order quantity
  - max_total Max order amount
  - min_amount Min order quantity
  - min_total Min order amount
  - state 1 available 0 unavailable
  - make_rate Maker rate
  - take_rate taker rate
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/contract/symbol/list -d 'nonce=1536826456'
```

### <span id="open-apifuturelist-accountbalance">Available Balance Of User POST /future/account/balance/list </span>
- Parameters
  - nonce Timestamps
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
  - wallet_available  Available balance of wallet 
  - available  Available balance
  - currency Currency
  - holding  Number of current positions 
  - price  Opening price 
  - leverage  Leverage multiple 
  - margin_available  Margin balance 
  - position_margin  Position margin 
  - order_margin Commission margin
  - unrealized  Unrealized profit and loss 
  - realized  Realized profit and loss 
  - liq_price Estimated liquidation price
  - adl ADL value
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/future/account/balance/list -d 'nonce=1536826456'
```

### <span id="open-apifuturelist-transfermargin">Change Margin During Trading POST /future/account/transfer_margin </span>
- Parameters
  - nonce Timestamps
  - amount Increase or decrease in amount
  - currency Currency
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
  ok
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/future/account/transfer_margin -d 'nonce=1536826456&currency=BTCUSD&amount=-0.3302'
```

### <span id="open-apifuturelist-order">Place Order POST /contract/order </span>
- Parameters
  - nonce Timestamps
  - amount 
  - price 
  - type Order type 1 Limited price 2Market price 3stop limit 4 Market price stop loss  5 Limit profit  6 Market price surplus 
  - side Specify 2sell 1buy
  - symbol Trading pairs
  - leverage  Leverage multiple  -1crossed 10 20 100
  - passive  Passive entrustment or not  0 not 1yes
  - trigger_price Trigger price
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/contract/order -d 'nonce=1536826456&symbol=FUTURE_BTCUSD&side=1&type=1&price=3500&amount=1&leverage=10'
```

### <span id="open-apifuturelist-remove">Cancel Order POST /contract/remove</span>
- Parameters
  - nonce Timestamps
  - symbol Trading pairs 
  - order_id Order id 
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
- Limited access interval 
  -	1000 milliseconds
- Example
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/contract/remove -d 'nonce=1536826456&symbol=FUTURE_BTCUSD&order_id='
```

### <span id="open-apifuturelist-close">Closing POST /contract/close</span>
- Parameters
  - nonce Timestamps
  - symbol Trading pairs
  - price  Closed position price 
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/contract/close -d 'nonce=1536826456&symbol=FUTURE_BTCUSD&price=3600'
```

### <span id="open-apifuturelist-activeorders">Before One Of Limit Order POST /contract/activeorders</span>
- Parameters
  - nonce Timestamps
  - page
  - size
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field description
    - order id
    - symbol Contract type
    - amount Amount
    - price Limited price
    - total Amount of order filled
    - executed Ordered quantity
    - state Status 1 No transaction in commission  2 Partial transaction in commission 
    - create_time Create time 
    - update_time Update time
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/contract/activeorders -d 'nonce=1536826456&page=1&size=10'
```

### <span id="open-apifuturelist-orderhistory">History Of Limit Order POST /contract/orderhistory</span>
- Parameters
  - nonce Timestamps
  - page
  - size
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field Description
    - Order id
    - symbol Contract type
    - amount Amount
    - price Limited price
    - total Traded quantity 
    - executed Quantity of order filled
    - state Status 1 No transaction in commission  2 Partial transaction in commission 3:Fully Filled, 4:Canceled, 5:Cancel Filled, 6: Part of the transaction at market price , 7: Part of the market price was sold out 
    - create_time Create time
    - update_time Update time
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/contract/orderhistory -d 'nonce=1536826456&page=1&size=10'
```


### <span id="open-apifuturelist-orderfills">Order Of Filled POST /future/account/orderfills</span>
- Parameters
  - nonce Timestamps
  - page  Pagination
  - size  Offset
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Field description
    - id Orders ID
    - order_id Order ID
    - uid User ID
    - symbol Trading pairs
    - product  Commodity abbreviation 
    - currency Currency abbreviation
    - side 1buy 2sell
    - type 1Limited price 2market price 3 Stop profit  4 Stop loss 
    - price Traded price
    - amount Traded amount
    - amount_total Authorized amount
    - amount_last Last traded amount
    - total Traded total
    - fee fee
    - create_time Traded time
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/future/account/orderfills -d 'nonce=1536826456&page=1&size=10'
```
