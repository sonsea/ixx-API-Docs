### [中文](./trade_api.md)

# Spot Trading API

* [API Summary](#open-api)

* [Limited Control](#open-apilimited)

* [Create API Key](#open-apisecret)

* [Code Example](#open-apicode)

* [Status Code](#open-apistatuscode)

* [Spot Trading List API](#open-apitradelist)
  * [Get Trading All Pairs List](#open-apitradelist-symbollist)
  * [Available Balance Of User](#open-apitradelist-balancelist)
  * [Place Order](#open-apitradelist-ordercreate)
  * [Cancel Order](#open-apitradelist-orderremove)
  * [Before One Of Limit Order](#open-apitradelist-orderactive)
  * [History Of Order](#open-apitradelist-orderhistory)
  * [Query Actived Detail Of Limit Order](#open-apitradelist-orderquery)

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

## <span id="open-apitradelist">Spot Trading List API </span>

## <span id="open-apitradelist-symbollist">Get Trading All Pairs List POST /symbol/list</span>
- Parameters
  - nonce Timestamps
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data(id/name/product/price_scale/amount_scale/min_amount/max_amount/min_total/max_total/make_rate/take_rate/state)
  - message
- Field Description
  - id  Unique number ID 
  - name Contract name, transaction pair of BTC 
  - product Product 
  - currency Currency 
  - price_scale Price accuracy 
  - amount_scale Quantity accuracy 
  - min_amount Min amount 
  - max_amount Max amount 
  - min_total Min order amount 
  - max_total Max order amount 
  - make_rate make order fee rate 
  - take_rate take order fee rate 
  - state status 1:online 2:offline
- Limited access interval 
  -	1000 milliseconds
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/symbol/list -d 'nonce=1536826456'
```

## <span id="open-apitradelist-balancelist">Available Balance Of User POST /balance/list</span>
- parameters
  - nonce Timestamps
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data(user_id/currency/available/ordering/withdrawing/quota/max_quota/rates)
  - message
- Field Description
  - user_id User ID 
  - currency Currency 
  - available Available quantity 
  - ordering  Entrusted lock quantity  
  - withdrawing  Locked amount of withdrawal 
  - quota Current quota 
  - max_quota Max quota 
  - rates Rates 
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/balance/list -d 'nonce=1536826456'
```

## <span id="open-apitradelist-ordercreate">Place Order POST /order/create</span>
- Parameters
  - nonce Timestamps
  - symbol Trading pairs
  - side 1:buy 2:sell
  - type 1:limited price 2:market price
  - price Price limit quotation ,market price:0
  - amount Price limit quotation amount, market price amount:0
  - locked Locked amount at market price , Market price is required 
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data(id/user_id/symbol/side/type/price/amount/locked)
  - message
- Field Description
  - id Order ID 
  - user_id User ID 
  - symbol Trading pairs 
  - side 1:buy 2:sell
  - type 1:limited price 2:market price
  - price  Price limit quotation 
  - amount Price limit quotation amount
  - locked Locked amount at market price 
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/create -d 'nonce=1536826456&symbol=BTC_USDT&side=1&type=1&price=6000&amount=1'
```

## <span id="open-apitradelist-orderremove">Cancel Order POST /order/remove</span>
- Parameters
  - nonce Timestamps
  - symbol Trading pairs
  - order_id Order ID
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data
  - message
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/remove -d 'nonce=1536826456&symbol=BTC_USDT&order_id=123'
```

## <span id="open-apitradelist-orderactive">Before One Of Limit Order POST /order/active</span>
- Parameters
  - nonce Timestamps
  - symbol Trading pairs 
  - page Pagination
  - size Number of per page
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data(id/user_id/symbol/product/currency/side/type/price/amount/locked/executed/total/fee/state/create_time/update_time)
  - message
- Field Description
  - id Order ID 
  - user_id User ID 
  - symbol Trading pairs 
  - product Product 
  - currency Currency 
  - side 1:buy 2:sell 
  - type 1:limited price 2:market price
  - price Offer price
  - amount Offer quantity
  - locked Locked quantity
  - executed Filled quantity
  - total Filled total
  - fee 手fee
  - state 1:Open 2:partially filled
  - create_time Time of order creation
  - update_time Time of filled
- Example
```
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/active -d 'nonce=1536826456&symbol=BTC_USDT&page=1&size=10'
```

## <span id="open-apitradelist-orderhistory">History Of Order POST /order/history</span>
- Parameters
  - nonce Timestamps
  - page Pagination
  - size Number of per page
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data(id/user_id/symbol/product/currency/side/type/price/amount/locked/executed/total/fee/state/create_time/update_time)
  - message
- Field Description
  - id Order ID 
  - user_id User ID 
  - symbol Trading pairs 
  - product Product 
  - currency Currency 
  - side 1buy 2sell 
  - type 1Limited price 2 Market price
  - price Offer price
  - amount Offer amount
  - locked Locked quantity
  - executed Filled quantity
  - total Filled total
  - fee fee
  - state 3Filled 4 Unsettled cancellation  5 Partial transaction cancellation  6 Cancellation of non transaction system  7 Cancellation of partial transaction system 
  - create_time Time of order creation
  - update_time Time of filled
```
- Example
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/history -d 'nonce=1536826456&page=1&size=10'
```

### <span id="open-apitradelist-orderquery">Query Actived Detail Of Limit Order POST /order/query</span>
- Parameters
  - nonce Timestamps
  - symbol Trading pairs 
  - order_id Order ID
- Response
  - code(200 indicates normal reading of data content, and non 200 indicates failure to read message failure message)
  - data(id/user_id/symbol/product/currency/side/type/price/amount/locked/executed/total/fee/state/create_time/update_time)
  - message
- Field Description
  - id Order ID 
  - user_id User ID 
  - symbol Trading pairs 
  - product Product 
  - currency Currency 
  - side 1buy 2sell 
  - type 1Limited price 2 Market price
  - price Offer price
  - amount Offer amount
  - locked Locked quantity
  - executed Filled amount
  - total Filled total
  - fee fee
  - state 1Open  2partially filled
  - create_time Time of order creation
  - update_time Time of filled
```
- Example
curl -H 'key: xxx' -H 'sign: yyy' -H 'version: 2.0' -X POST https://api.ixex.io/order/query -d 'nonce=1536826456&symbol=BTC_USDT&order_id=123'
```
