### [中文](./quotes_websocket_api.md)

# WebSocket API Retrieve Market Data And Order Book Depth,Please refer to v1

## Connect Channel

Used WebSocket connect **/v1/ticker/EOS_ETH** channel take the **Beta** environment for example：

``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/ticker/EOS_ETH");
```

Client for request：

``` text
GET wss://ws.ixex.io/v1/ticker/EOS_ETH HTTP/1.1
Host: ws.ixex.io
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: zVu/qw6mod9ivrbSex2GBw==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

Server Response：

``` text
HTTP/1.1 101 Switching Protocols
Date: Thu, 14 Jun 2018 11:49:21 GMT
Connection: upgrade
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Cache-Control: no-cache, no-store, must-revalidate
Upgrade: WebSocket
Sec-WebSocket-Accept: ip3WBDpEnyzPMRPngEgDZgM+6lU=
```

If you connect to a channel that does not exist, the server responds with the HTTP 404 status code for example:

``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/unknown");
```

Client for request：
``` text
GET wss://ws.ixex.io/v1/unknown HTTP/1.1
Host: ws.ixex.io
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: BP2dOFyaJFIIb2+E77/7fA==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

Server response：

``` text
HTTP/1.1 404 Not Found
Date: Thu, 14 Jun 2018 11:51:47 GMT
Content-Type: text/html
Transfer-Encoding: chunked
Connection: keep-alive
Server: nginx
Content-Encoding: gzip
```

If the connected channel exists, but the parameters are wrong, for example, if the transaction pair does not exist, the server will actively disconnect after responding, for example;
``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/ticker/EOS");
```

Client Request：
``` text
GET wss://ws.ixex.io/v1/ticker/EOS HTTP/1.1
Host: ws.ixex.io
Connection: Upgrade
Pragma: no-cache
Cache-Control: no-cache
Upgrade: websocket
Sec-WebSocket-Version: 13
Sec-WebSocket-Key: /ulaHe5O/DCNxbF30evMWQ==
Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits
```

Server response：
``` text
HTTP/1.1 101 Switching Protocols
Date: Thu, 14 Jun 2018 11:54:49 GMT
Connection: upgrade
Expires: Thu, 01 Jan 1970 00:00:01 GMT
Cache-Control: no-cache, no-store, must-revalidate
Upgrade: WebSocket
Sec-WebSocket-Accept: 4RdyPxpw7+pNAG6bpU5l/gwSIEY=
```

## Response Format
All response message used JSON format, Include:
+ reserve status code(**code**);
+ data(**data**)

Example：
``` javascript
{
    "code":0,   // status code,the current version is not used
    "data":{
        ...     // response data
    }
}
```


## Public Channel

### Market Cycle histories data
URI：**/v1/market/histories/{period}**  

|name         | type        | description | Is it necessary |
| ------------- |:------------- | :-----| :-----:|
| period | string | market cycle histories | yes |

#### Cycle parameters(**period**)

|  parameter  | definition  |  parameter  | definition  |  parameter  | definition  |
| :---: | :---: | :---: | :---: | :---: | :---: |
|  1m   |  1 min  |  1h   |  1 hour  |  1d   |    1 day    |
|  5m   |  5 min  |  2h   |  2 hours  |  1w   |    1 week    |
|  15m  |  15 min |  4h   |  4 hours  |  1M   |    1 month    |
|  30m  |  30 min |  6h   |  6 hours  |
|       |         |  12h  |  12 hours |

Connect Example：
``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/market/histories/1d");
```

Push Example：
``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1529798400000,  // time，with milliseconds resolution  
            "pair": "ETH_BTC",                      
            "values": [                             
                "0.076973",         // start price   
                "0.076838",         // end price     
                "0.07606",          // lower price   
                "0.077378",         // highest price 
                "27.3411",          // trading volume 
                "2.1000977805",     // trading amount 
                "0.076973",         // last cycle end price  
                "121",              // trading number 
                "0.205"             // trade amount 
            ]
        },
        {
            "time": 1529798400000,
            "pair": "EOS_ETH",
            "values": [
                "0.017866",
                "0.017433",
                "0.017178",
                "0.018546",
                "2013.0353",
                "35.5771107184",
                "0.017866",
                "123",
                "18.4377"
            ]
        },
        {
            "time": 1529798400000,
            "pair": "EOS_ETH",
            "values": [
                "0.001372",
                "0.00135",
                "0.001346",
                "0.001386",
                "2446.2574",
                "3.3256018155",
                "0.001372",
                "128",
                "20.1341"
            ]
        },
        {
            "time": 1529798400000,
            "pair": "EOS_USDT",
            "values": [
                "8.4371",
                "8.2357",
                "8.1938",
                "8.5026",
                "2681.5773",
                "22255.92854128",
                "8.4371",
                "126",
                "18.2521"
            ]
        },
        {
            "time": 1529798400000,
            "pair": "BTC_USDT",
            "values": [
                "6136.14",
                "6072.41",
                "5937.46",
                "6210.94",
                "3.7433",
                "22847.165608",
                "6136.14",
                "134",
                "0.0267"
            ]
        },
        {
            "time": 1529798400000,
            "pair": "ETH_USDT",
            "values": [
                "473.25",
                "465.78",
                "463.37",
                "475.6",
                "55.9782",
                "26252.350092",
                "473.25",
                "128",
                "0.4091"
            ]
        }
    ]
}
```

###  Market Tickers Price
URI：**/v1/market/tickers**  

Connect example：
``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/market/tickers");
```

Push example：
``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1530862375429,                  // time，with milliseconds resolution
            "pair": "ETH_BTC",                      // trading pair name
            "current": "0.070269",                  // trade price
            "volume_current": "0.2399",             // trade amount
            "increment_24h": "-0.001124",           // trading volume for price of past 24 hours
            "highest_24h": "0.072012",              // highest price in the past 24 hours
            "lowest_24h": "0.070082",               // lowest price in the past 24 hours
            "volume_24h": "222.45380000000003",     // trading volume of past 24 hours
            "highest_bid": "0.069891",              // best bid price
            "highest_bid_amount": "0.1",            // best bid price amount
            "lowest_ask": "0.070642",               // best ask price
            "lowest_ask_amount": "0.1",             // best ask price amount
            "change_24h": "-1.5744"                 // Change pct in the past 24 hours
        },
        {
            "time": 1530862374942,
            "pair": "EOS_ETH",
            "current": "0.018232",
            "volume_current": "29.6759",
            "increment_24h": "-0.000795",
            "highest_24h": "0.019975",
            "lowest_24h": "0.017696",
            "volume_24h": "27341.4681",
            "highest_bid": "0.017182",
            "highest_bid_amount": "9.33",
            "lowest_ask": "0.018661",
            "lowest_ask_amount": "30.15",
            "change_24h": "-4.1783"
        },
        {
            "time": 1530862375218,
            "pair": "EOS_BTC",
            "current": "0.001289",
            "volume_current": "17.0712",
            "increment_24h": "-0.000074",
            "highest_24h": "0.001379",
            "lowest_24h": "0.001284",
            "volume_24h": "20040.206399999995",
            "highest_bid": "0.001277",
            "highest_bid_amount": "4.85",
            "lowest_ask": "0.001305",
            "lowest_ask_amount": "4.89",
            "change_24h": "-5.4292"
        },
        {
            "time": 1530862375103,
            "pair": "EOS_USDT",
            "current": "8.3349",
            "volume_current": "43.1394",
            "increment_24h": "-0.633",
            "highest_24h": "9.1656",
            "lowest_24h": "8.3123",
            "volume_24h": "46383.8888",
            "highest_bid": "8.2472",
            "highest_bid_amount": "7.66",
            "lowest_ask": "8.4231",
            "lowest_ask_amount": "7.76",
            "change_24h": "-7.0585"
        },
        {
            "time": 1530862375228,
            "pair": "BTC_USDT",
            "current": "6460.67",
            "volume_current": "0.06",
            "increment_24h": "-176.13",
            "highest_24h": "6812.95",
            "lowest_24h": "5922.07",
            "volume_24h": "59.355399999999996",
            "highest_bid": "6425.71",
            "highest_bid_amount": "0.0985",
            "lowest_ask": "6492.45",
            "lowest_ask_amount": "0.0971",
            "change_24h": "-2.6538"
        },
        {
            "time": 1530862374902,
            "pair": "ETH_USDT",
            "current": "453.87",
            "volume_current": "0.9173",
            "increment_24h": "-16.49",
            "highest_24h": "478.88",
            "lowest_24h": "452",
            "volume_24h": "494.0506",
            "highest_bid": "451.62",
            "highest_bid_amount": "0.098",
            "lowest_ask": "456.42",
            "lowest_ask_amount": "0.097",
            "change_24h": "-3.5058"
        }
    ]
}
```

### Quoted Price Data

URI：**/v1/ticker/{pair}**   
Parameters：

| name | type   | description       | Is it necessary |
| ------ | :----- | :--------- | :------: |
| pair   | string | Specify pairs of transactions |    yes    |

Connect example：
``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/ticker/EOS_ETH");
```

Push example：
``` javascript
{
    "code": 0,
    "data": {
        "time": 1530862854941,          // time，with milliseconds resolution
        "pair": "EOS_ETH",              // trading pair name
        "current": "0.018334",          // trrade price
        "volume_current": "0",          // trade amount
        "increment_24h": "-0.000658",   // trading volume for price of past 24 hours
        "highest_24h": "0.019975",      // highest price in the past 24 hours
        "lowest_24h": "0.017568",       // lowest price in the past 24 hours
        "volume_24h": "27285.2885",     // trading volume of past 24 hours
        "highest_bid": "0.018149",      // best bid price
        "highest_bid_amount": "7.62",   // best bid price amount
        "lowest_ask": "0.018516",       // best ask price
        "lowest_ask_amount": "7.7",     // best ask price amount
        "change_24h": "-3.4646"         // Change pct in the past 24 hours
    }
}
```





### Market Cycle Data 

URI：**/v1/history/{pair}/{period}**  
Parameters：

| parameter name         | type        | description | Is it necessary |
| ------------- |:------------- | :-----| :-----:|
| pair | string | Specify pairs of transactions | yes |
| period | string | market cycle | yes |

#### Cycle parameters(**period**)

|  parameter  | definition  |  parameter  | definition  |  parameter  | definition  |
| :---: | :---: | :---: | :---: | :---: | :---: |
|  1m   |  1 min  |  1h   |  1 hour  |  1d   |    1 day    |
|  5m   |  5 min  |  2h   |  2 hours  |  1w   |    1 week    |
|  15m  |  15 min |  4h   |  4 hours  |  1M   |    1 month    |
|  30m  |  30 min |  6h   |  6 hours  |
|       |         |  12h  |  12 hours |

Connect Example：
``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/history/EOS_ETH/1m");
```

Push Example：  

``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1529808300000,  // time，with milliseconds resolution 
            "pair": "EOS_ETH",      // Specify pairs of transactions
            "values": [
                "0.017436",         // begin price
                "0.017436",         // end price
                "0.017436",         // lower price
                "0.017436",         // highest price
                "0",                // trading volume
                "0",                // trading amount
                "0.017436",         // last cycle end price
                "0",                // trading number
                "17.5732"           // trade amount
            ]
        }
    ]
}
```

### Filled Orders

URI：**/v1/deal/{pair}**  
Parameters：

| parameter name         | type        | description  | Is it necessary |
| ------------- | :-----|:------------- | :-----:|
| pair | string | Specify pairs of transactions | yes |

Connect Example：
``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/deal/EOS_ETH");
```

Push Example：  

``` javascript
{
    "code": 0,
    "data": [        
        {
            "time": 1529811082258,  // time，with milliseconds resolution
            "side": "buy",          // filled direction
            "values": [
                "0.017262",         // filled price
                "16.1633"           // filled amount
            ]
        },
        {
            "time": 1529811012254,
            "side": "sell",
            "values": [
                "0.017277",
                "15.1537"
            ]
        }
    ]
}
```



### Orders Channel

URI：**/v1/orderbook/{pair}/{offset}/{accuracy}/{size}**  
Parameters：

| parameter name         | type        | description  | Is it necessary |
| ------------- |:------------- | :-----| :-----:|
| pair | type | Specify pairs of transactions | yes |
| size | int | Position，option：5、10、20 | yes |
| offset | int | Floating point bit offset | yes |
| accuracy | int |  Statistical accuracy  | yes |

#### Statistical accuracy(**accuracy**)
Used long and short position query and order push interface, carry bit half_even

| parameter |     definition      |
| :--: | :-----------: |
|  1   |  Accuracy continue to have 1  |
|  2   |  Accuracy continue to have 2.5 |
|  5   |  Accuracy continue to havve 5  |



#### Floating point bit offset(**offset**)

Used sell and buy side API，carry bit half_even，Options：**0～10**；
Max offset 10，When the floating-point precision of the transaction type is less than 10, the maximum offset is the floating-point precision of the transaction type

Response format：

| name         | type  | description          |
| :------------ | :-----|:------------- |
| asks | array | Sell side depth |
| bids | array | Buy side depth |

Connect Example:
``` javascript
var socket = new WebSocket("wss://ws.ixex.io/v1/orderbook/EOS_ETH/0/1/5");
```

Example：
``` javascript
{
    "code": 0,
    "data": {
        "asks": [
            {
                "values": [
                    "0.017409", // sell price
                    "7.96"      // sell amount
                ]
            },
            {
                "values": [
                    "0.017481",
                    "40"
                ]
            },
            {
                "values": [
                    "0.017535",
                    "23.39"
                ]
            },
            {
                "values": [
                    "0.017562",
                    "9.84"
                ]
            },
            {
                "values": [
                    "0.019102",
                    "17.74"
                ]
            }
        ],
        "bids": [
            {
                "values": [
                    "0.017021", // buy price
                    "6.81"      // buy amount
                ]
            },
            {
                "values": [
                    "0.0169",
                    "2.01"
                ]
            },
            {
                "values": [
                    "0.016827",
                    "71.92"
                ]
            },
            {
                "values": [
                    "0.01631",
                    "108.5"
                ]
            },
            {
                "values": [
                    "0.015604",
                    "62.15"
                ]
            }
        ]
    }
}
```
