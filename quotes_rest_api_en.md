### [中文](./quotes_rest_api.md)

# REST API Retrieve Market Data And Order Book Depth,Please refer to v1

## Response Format
All response message used JSON format, Include:
+ reserve status code(**code**);
+ data(**data**)

Example：
``` javascript
{
    "code":0,   // status code，Successful response code 0
    "data":{
        ...     // response data
    }
}
```

### Status Code

#### 1001 Missing parameters

Example：

``` javascript
{
    "code": 1001,     // status code
    "data": "pair"    // missing parameter
}
```

#### 1002 Invalid parameters

Example：

``` javascript
{
    "code": 1002,     // status code
    "data": "pair"    // Invalid parameters
}
```



#### 1003 Parameters type mismatch

Example：

``` javascript
{
    "code": 1003,     // status code
    "data": "pair"    // mismatch parameters
}
```

​

#### 1004 Invalid symbol

Example：

``` javascript
{
    "code":1004,      // code
    "data":"1111"     // Invalid symbol name
}
```

## Path

### All market paris data
URI：**/v1/market/pairs**
Request format：**GET**
Example Request：
``` text
curl https://q.ixex.io/v1/market/pairs
```

Example Response：
``` javascript
{
    "code": 0,
    "data": [
        "ETH_BTC",
        "EOS_BTC",
        "EOS_USDT",
        "BTC_USDT",
        "ETH_USDT"
    ]
}
```

### Market Cycle histories data
URI：**/v1/market/histories?period={period}&pairs={pairs}**
Request format：**GET**
Request parameters：

|name         | type        | description | Is it necessary |
| ------------- |:------------- | :-----| :-----:|
| period | string | market cycle histories | yes |
| pairs | string | Specify pairs of transactions separated by commas | no |

#### Cycle parameters(**period**)

|  parameter  | definition  |  parameter  | definition  |  parameter  | definition  |
| :---: | :---: | :---: | :---: | :---: | :---: |
|  1m   |  1 min  |  1h   |  1 hour  |  1d   |    1 day    |
|  5m   |  5 min  |  2h   |  2 hours  |  1w   |    1 week    |
|  15m  |  15 min |  4h   |  4 hours  |  1M   |    1 month    |
|  30m  |  30 min |  6h   |  6 hours  |
|       |         |  12h  |  12 hours |

Example Request：
The parameter **pairs** is not transferred
``` text
curl https://q.ixex.io/v1/market/histories?period=1d
```

Example Response ：
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
            "pair": "EOS_BTC",
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

Example Request：
Transferred the parameter of **pairs** and not exist symbol
``` text
curl https://q.ixex.io/v1/market/history?pairs=ETH_BTC,EOS_BTC,UNKNOWN&period=1d
```

Example Response：
``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1529798400000,  // time，with milliseconds resolution
            "pair": "ETH_BTC",
            "values": [
                "0.076973",         // begin price
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
            "pair": "EOS_BTC",
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
        }
    ]
}
```

### Market Tickers Price
URI：**/v1/market/tickers**
Request format：**GET**
Request Parameters：

| name         | type        | description | Is it necessary |
| ------------- |:------------- | :-----| :-----:|
| pairs | type | Specify pairs of transactions separated by commas | no |

Example Request：
The parameter **pairs** is not transferred
``` text
curl https://q.ixex.io/v1/market/tickers
```

Example Response：
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
            "highest_bid_amount": "0.1",            // best bid price aount
            "lowest_ask": "0.070642",               // best ask price
            "lowest_ask_amount": "0.1",             // best ask price amount
            "change_24h": "-1.5744"                 // Change pct in the past 24 hours
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

Example Request：
Transferred the parameter of **pairs** and not exist symbol
``` text
curl https://q.ixex.io/v1/market/tickers?pairs=ETH_BTC,EOS_BTC,UNKNOWN
```

Example Response：
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
        }
    ]
}
```

### Quoted Price Data

URI：**/v1/ticker/{pair}**
Request format：**GET**
Parameters：

| name | type   | description       | Is it necessary |
| ------ | :----- | :--------- | :------: |
| pair   | string | Specify pairs of transactions |    yes    |

Example Request：
``` text
curl https://q.ixex.io/v1/ticker/ETH_USDT
```

Example Response：
``` javascript
{
    "code": 0,
    "data": {
        "time": 1568602164884,
        "pair": "ETH_USDT",
        "current": "193.95",
        "volume_current": "11.1249",
        "increment_24h": "6.32",
        "highest_24h": "194.29",
        "lowest_24h": "185.77",
        "volume_24h": "370981.88269999996",
        "highest_bid": "193.95",
        "highest_bid_amount": "1.0039",
        "lowest_ask": "193.96",
        "lowest_ask_amount": "5.0031",
        "change_24h": "3.3683"
    }
}
```

### Market Cycle Data 

### Query market cycle data by number of results per request.
URI：**/v1/history/{pair}?period={period}&size={size}**
Request format：**GET**
Parameters：

| parameter name         | type        | description | Is it necessary |
| ------------- |:------------- | :-----| :-----:|
| pair | string | Specify pairs of transactions | yes |
| period | string | market cycle | yes |
| size | int | number of results | yes |

#### Cycle parameters(**period**)

|  parameter  | definition  |  parameter  | definition  |  parameter  | definition  |
| :---: | :---: | :---: | :---: | :---: | :---: |
|  1m   |  1 min  |  1h   |  1 hour  |  1d   |    1 day    |
|  5m   |  5 min  |  2h   |  2 hours  |  1w   |    1 week    |
|  15m  |  15 min |  4h   |  4 hours  |  1M   |    1 month    |
|  30m  |  30 min |  6h   |  6 hours  |
|       |         |  12h  |  12 hours |

Example Request：
``` text
curl https://q.ixex.io/v1/history/ETH_USDT?period=1m&size=5
```

Example Response：

``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1568601960000,
            "values": [
                "193.8",
                "193.81",
                "193.8",
                "193.86",
                "282.1253",
                "54678.79599",
                "193.8",
                "21",
                "25.0009"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568602020000,
            "values": [
                "193.81",
                "193.81",
                "193.81",
                "193.82",
                "188.6281",
                "36558.844994",
                "193.81",
                "20",
                "89.0834"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568602080000,
            "values": [
                "193.81",
                "193.91",
                "193.81",
                "193.91",
                "248.0868",
                "48088.16252",
                "193.81",
                "27",
                "2.3108"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568602140000,
            "values": [
                "193.92",
                "193.97",
                "193.91",
                "193.98",
                "186.2488",
                "36123.025196",
                "193.91",
                "31",
                "4.3299"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568602200000,
            "values": [
                "193.97",
                "194.15",
                "193.97",
                "194.15",
                "153.3314",
                "29757.231385",
                "193.97",
                "22",
                "5.0043"
            ],
            "pair": "ETH_USDT"
        }
    ]
}
```

#### Query cycle data in specified time range
URI：**/v1/history/millis/{pair}?period={period}&begin={begin}&end={end}**
Request format：**GET**
Request Parameters：

| parameter name         | type        | description  | Is it necessary |
| ------------- |:------------- | :-----| :-----:|
| pair | string | Specify pairs of transactions | yes |
| period | string | market cycle | yes |
| begin | long int | begin time，with milliseconds resolution | yes |
| end | long int | end time，with milliseconds resolution | yes |

#### Cycle parameters(**period**)

|  parameter  | definition  |  parameter  | definition  |  parameter  | definition  |
| :---: | :---: | :---: | :---: | :---: | :---: |
|  1m   |  1 min  |  1h   |  1 hour  |  1d   |    1 day    |
|  5m   |  5 min  |  2h   |  2 hours  |  1w   |    1 week    |
|  15m  |  15 min |  4h   |  4 hours  |  1M   |    1 month    |
|  30m  |  30 min |  6h   |  6 hours  |
|       |         |  12h  |  12 hours |

Example Request：
``` text
curl https://q.ixex.io/v1/history/millis/ETH_USDT?period=1d&begin=1567958400000&end=1568476800000
```

Example Response：
``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1567958400000,
            "values": [
                "181.37",
                "181.37",
                "175.99",
                "185.3",
                "392780.6035",
                "70810311.774031",
                "181.37",
                "43724",
                "10.0015"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568044800000,
            "values": [
                "181.37",
                "179.47",
                "178.48",
                "184.35",
                "408539.3988",
                "74061785.942075",
                "181.37",
                "43300",
                "40.5147"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568131200000,
            "values": [
                "179.47",
                "175.98",
                "175.61",
                "182.58",
                "378805.6519",
                "67816205.332472",
                "179.47",
                "40747",
                "34.95"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568217600000,
            "values": [
                "175.97",
                "179.72",
                "174.43",
                "180.28",
                "352382.5907",
                "62668340.355944",
                "175.98",
                "35627",
                "25"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568304000000,
            "values": [
                "179.72",
                "178.6",
                "178.17",
                "182.38",
                "362144.485",
                "65030852.706215",
                "179.72",
                "36070",
                "5"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568390400000,
            "values": [
                "178.6",
                "184.91",
                "177.63",
                "185.4",
                "337678.8329",
                "60967057.282",
                "178.6",
                "34076",
                "6.0047"
            ],
            "pair": "ETH_USDT"
        },
        {
            "time": 1568476800000,
            "values": [
                "184.92",
                "188.82",
                "184.52",
                "190.33",
                "387109.1587",
                "72609585.423441",
                "184.91",
                "38728",
                "4.165"
            ],
            "pair": "ETH_USDT"
        }
    ]
}
```

### Filled Orders

URI：**/v1/deal/{pair}?size={size}**
Request format：**GET**
Parameters：

| parameter name         | type        | description  | Is it necessary |
| ------------- | :-----|:------------- | :-----:|
| pair | string | Specify pairs of transactions | yes |
| size | int | number of results | yes |

Example Request：
``` text
curl https://q.ixex.io/v1/deal/ETH_USDT?size=2
```

Example Response：
``` javascript
{
    "code": 0,
    "data": [
        {
            "time": 1568602551855,
            "values": [
                "194.34",
                "25.002"
            ],
            "side": "sell"
        },
        {
            "time": 1568602549743,
            "values": [
                "194.34",
                "4.5"
            ],
            "side": "sell"
        },
        {
            "time": 1568602545478,
            "values": [
                "194.34",
                "36.4118"
            ],
            "side": "buy"
        }
    ]
}
```



### Orders

URI：**/v1/orderbook/{pair}?offset={offset}&accuracy={accuracy}&size={size}**
Request format：**GET**
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

Example Request:
``` text
curl https://q.ixex.io/v1/orderbook/ETH_USDT?offset=0&accuracy=1&size=5
```

Example Response:
``` javascript
{
    "code": 0,
    "data": {
        "asks": [
            {
                "values": [
                    "194.12",
                    "9.155"
                ]
            },
            {
                "values": [
                    "194.13",
                    "17.0067"
                ]
            },
            {
                "values": [
                    "194.14",
                    "1.0031"
                ]
            },
            {
                "values": [
                    "194.15",
                    "2.5036"
                ]
            },
            {
                "values": [
                    "194.16",
                    "50.0015"
                ]
            }
        ],
        "bids": [
            {
                "values": [
                    "194.11",
                    "1.665"
                ]
            },
            {
                "values": [
                    "194.1",
                    "6.6469"
                ]
            },
            {
                "values": [
                    "194.09",
                    "5.0039"
                ]
            },
            {
                "values": [
                    "194.03",
                    "10.0045"
                ]
            },
            {
                "values": [
                    "194.02",
                    "25.139"
                ]
            }
        ]
    }
}
```

### Getting Server Time

URI：**/v1/time**
Request format：**GET**
Request parameters：no
Example Request：
``` text
curl https://q.ixex.io/v1/time
```
Example Response：
``` javascript
{
    "iso":"2018-06-19T16:28:22.755Z",   // ISO8601 (yyyy-MM-ddTHH:mm:ss.SSSZZ)  format Timestamps
    "mills":1529425702755,              // Timestamps, with milliseconds resolution
    "epoch":1529425702                  // Unix time
}
```
