# Menu

- [Authorization](#authorization)

- [HTTP Protocol API](#http-protocol-api) 

  - [Return Codes of Errors](#return-codes-of-errors)
  - [Public Data Methods](#public-data-methods)
    - [List of Public Pairs](#list-of-public-pairs)
    - [Specific Public Ticker Data](#specific-public-ticker-data)
    - [List of Order Book](#list-of-order-book)
    - [Market History Data](#market-history-data)
    - [Public Pair List](#public-pair-list)
    - [Depth List](#depth-list)
    - [List of Graphic Data KLine](#list-of-graphic-data-kline)
 
  - [Private Data Methods](#public-data-methods)
    - [List of Public Pairs](#list-of-public-pairs)
    - [Specific Public Ticker Data](#specific-public-ticker-data)
    - [List of Order Book](#list-of-order-book)
    - [Market History Data](#market-history-data)
    - [Public Pair List](#public-pair-list)
    - [Depth List](#depth-list)
    - [List of Graphic Data KLine](#list-of-graphic-data-kline)



- [WebSoket Protocol API](#websoket-protocol-api)


  - [Basic WS structure](#basic-ws-structure)
  - [PING-PONG Method](#ping-pong-method)
  - [System Time Method](#system-time-method)
  - [Web-Soket Authentication](#web-soket-authentication)

  - [KLine methods for Graph](#kline-methods-for-graph)
    - [KLine Query Method](#kline-query-method)
    - [KLine Subscribe Method](#kline-subscribe-method)
    - [KLine Unsubscribe Method](#kline-unsubscribe-method)
 
  - [Market Price Methods](#market-price-methods)
    - [Market Price Query Method](#market-price-query-method)
    - [Market Price Subscribe Method](#market-price-subscribe-method)
    - [Market Price Unsubscribe Method](#market-price-unsubscribe-method)

  - [Market Status Methods](#market-status-methods)
    - [Market Status Query Method](#market-status-query-method)
    - [Market Status Subscribe Method](#market-status-subscribe-method)
    - [Market Status Unsubscribe Method](#market-status-unsubscribe-method)

  - [Deals Methods](#deals-methods)
    - [Deals Query Method](#deals-query-method)
    - [Deals Subscribe Method](#deals-subscribe-method)
    - [Deals Unsubscribe Method](#deals-unsubscribe-method)
  - [Depth Methods](#depth-methods)
    - [Depth Query Method](#depth-query-method)
    - [Depth Subscribe Method](#depth-subscribe-method)
    - [Depth Unsubscribe Method](#depth-unsubscribe-method)

  - [Trade User Balances Methods](#trade-user-balances-methods)
    - [Asset Query Method](#asset-query-method)
    - [Asset Subscribe Method](#asset-subscribe-method)
    - [Asset Unsubscribe Method](#asset-unsubscribe-method)

  - [Active Orders and Orders History Methods](#active-orders-and-orders-history-methods)
    - [Order Query Method](#order-query-method)
    - [Order History Method](#order-history-method)
    - [Order Subscribe Method](#order-subscribe-method)
    - [Order Unsubscribe Method](#order-unsubscribe-method) 


# Authorization


Follow 5 simple steps to use private methods:

* Before authorization, you need to log in to https://localtrade.cc/login or register if the user has not yet been created https://localtrade.cc/register.
* After authorization, you need to go to https://localtrade.cc/user/security-main and activate 2-factor authentication.
* After enabling 2-factor authentication, you need to go to the API manager to create API keys that will be required for authorization in the API https://localtrade.cc/user/api.
* To create keys, click Add Key and the 1st package of keys for connecting the API will be created. Key packages can be up to 5 pieces.
* Before using the apkey, they must be activated - for this, on the created package, click on the switch which will switch them to the "Active" state.


Each package will contain 3 keys:
apiKey - public key
apiSecret - private key
weKey - key for websockets

**Important!**

  `nonce` - used as a parameter to protect against DDoS attacks and an excessive number of API requests. This parameter is most often used through a timestamp with a minimum step per second. Each next request must be greater than the next one in the `nonce` parameters.

Let's look at a few examples of authorization in different development languages:

**PHP**
  <details  open>
  <summary>
  </summary>
  
  
```php
use GuzzleHttp\Client;

public function callApi()
{
    $apiKey = '';
    $apiSecret = '';
		$request = ''; // /api/v1/order/new
    $baseUrl = 'https://api.localtrade.cc';

    $data = [
        'request' => $request,
        'nonce' => (string)\Carbon\Carbon::now()->timestamp,
    ];

    $completeUrl = $baseUrl . $request;
    $dataJsonStr = json_encode($data, JSON_UNESCAPED_SLASHES);
    $payload = base64_encode($dataJsonStr);
    $signature = hash_hmac('sha512', $payload, $apiSecret);

    $client = new Client();
    try {
        $res = $client->post($completeUrl, [
            'headers' => [
                'Content-type' => 'application/json',
                'X-TXC-APIKEY' => $apiKey,
                'X-TXC-PAYLOAD' => $payload,
                'X-TXC-SIGNATURE' => $signature
            ],
            'body' => json_encode($data, JSON_UNESCAPED_SLASHES)
        ]);
    } catch (\Exception $e) {
        return response()->json(['error' => $e->getMessage()]);
    }

    return response()->json(['result' => json_decode($res->getBody()->getContents())]);
}
```
  </details>


 
# HTTP Protocol API

  <details open>
  <summary>
  </summary>

**Public Methods:**

This method provides information via `GET`. The response will return all the information that was posted by the platform. To obtain private information, use the same methods via `POST` as an authorized user using api keys.


- [List of Public Pairs](#list-of-public-pairs) - returns the history of trades for all public pairs - used for tracking to compare prices in the market, control positions for many markets at once.
- [Specific Public Ticker Data](#specific-public-ticker-data) - returns the trading history for a specific selected pair - used to track a specific pair and track its key characteristics.
- [List of Order Book](#list-of-order-book) - Returns all positions for a specific market for all orders of the order book in the selected direction with pagination - used for full monitoring of the order book status, its changes, evaluation of its placed orders and their priority.
- [Market History Data](#market-history-data) - returns the history of trading on the market - used to track your own or someone else's executed orders, trading dynamics, control over buying / selling.
- [Public Pair List](#public-pair-list) - returns all public pairs - used to track new pairs, monitor pairs for MM.
- [Depth List](#depth-list) - returns data on the order book with pagination for a specific pair - used to track the depth of the order book, control placed orders, monitor the market in light mode.
- [List of Graphic Data KLine](#list-of-graphic-data-kline) - returns data on charts - is used to create personal charts, control market trends.




**Public Methods Via `POST`:**

First of all to use `POST` methods check how to made [Authorization](#authorization)
Use next methods via `POST` and obtain full information:

- [List of Public Pairs](#list-of-public-pairs)
- [Specific Public Ticker Data](#specific-public-ticker-data)
- [List of Order Book](#list-of-order-book)
- [Market History Data](#market-history-data)
- [Public Pair List](#public-pair-list)
- [Depth List](#depth-list)
- [List of Graphic Data KLine](#list-of-graphic-data-kline)




**Private Methods:**

All of this methods can be use only with `POST`. Before using check [Authorization](#authorization)

[List of Public Pairs](#list-of-public-pairs) 


</details>



## Return Codes of Errors
  <details open>
  <summary>
  </summary>

* HTTP `200` return code is used when the Succesful responce;
* HTTP `401` return code is used when the request made by Unauthorized user;
* HTTP `403` return code is used when the request made to Forbidden data from user without necessary permissions;
* HTTP `404` return code is used when the request made with undefined data and service can Not Found data for response.

  </details>




## Public Data Methods

All public methods requested with `GET` and consist only publick data.

### List of Public Pairs

  <details open>
  <summary>
  </summary>

```
/api/v1/public/markets
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
name | STRING | 
moneyPrec | NUMERIC |
stock | STRING | NO |
money | STRING | 
stockPrec | NUMERIC | 
feePrec | NUMERIC | 
minAmount | STRING | 



**Response:**
```javascript
{
  "code": 0,
  "message": {},
  "result": 
  [
    {
      "name":"BCH_BTC",
      "moneyPrec":8,
      "stock":"BCH",
      "money":"BTC",
      "stockPrec":8,
      "feePrec":8,
      "minAmount":"0.001"
    },
    {
      ... 
    },
  ],
}
```

</details>

### List of Public Tickers

  <details open>
  <summary>
  </summary>

```
/api/v1/public/tickers
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
name | STRING | 
bid | STRING |
ask | STRING |
open | STRING | 
high | STRING |
low | STRING |
last | STRING | 
volume | STRING | 
deal | STRING | 
change | STRING | 


**Response:**
```javascript
{
  "code": 0,
  "message": {},
  "result": 
  [
    {
      "name":"ETH_BTC",
      "bid":"0.05075249",
      "ask":"0.05075451",
      "open":"0.04799996",
      "high":"0.05086171",
      "low":"0.047891",
      "last":"0.05068996",
      "volume":"71.7808779340174791",
      "deal":"1446.73007651",
      "change":"5"
    },
    {
      ...
    },
  ],
}
```

</details>

### Specific Public Ticker Data


  <details open>
  <summary>
  </summary>

```
/api/v1/public/ticker
```

**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | STRING | YES | Any Public Market


**Request:**


```javascript
{
  market: ETH_BTC 
}
```

*url exapmle
```
/api/v1/public/ticker?market=ETH_BTC
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
name | STRING | 
bid | STRING |
ask | STRING |
open | STRING | 
high | STRING |
low | STRING |
last | STRING | 
volume | STRING | 
deal | STRING | 
change | STRING | 



**Response:**
```javascript
{
  "code": 0,
  "message": {},
  "result": 
  [
    {
      "name":"ETH_BTC",
      "bid":"0.05075249",
      "ask":"0.05075451",
      "open":"0.04799996",
      "high":"0.05086171",
      "low":"0.047891",
      "last":"0.05068996",
      "volume":"71.7808779340174791",
      "deal":"1446.73007651",
      "change":"5"
    },
  ],
}
```

</details>


### List of Order Book


  <details open>
  <summary>
  </summary>

```
/api/v1/public/book
```



**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | STRING | YES | Any Public Market
side | STRING | YES |  buy / sell
offset | NUMERIC | NO | Default 0
limit | NUMERIC | NO | Default 50; min 1; max 1000


**Request:**


```javascript
{
  market: "ETH_BTC" 
  side: "sell" 
  offset: 0 
  limit: 100 
}
```
url exapmle
```
/api/v1/public/book?market=ETH_BTC&side=sell&offset=0&limit=1
```

**Response Parameters:**

Name | Type | 
------------ | ------------ 
id | NUMERIC | 
market | STRING |
price | STRING |
amount | STRING | 
left | STRING |
type | STRING |
side | STRING | 
timestamp | NUMERIC | 
dealFee | STRING | 
takerFee | STRING | 
dealStock | STRING | 
dealMoney | STRING | 

**Response:**
```javascript
{
"code":200,
"success":true,
"message":"",
"result":
   {
    "offset":0,
    "limit":1,
    "total":337,
    "orders":
    [
      {
        "id":324101293,
        "market":"ETH_BTC",
        "price":"0.05065051",
        "amount":"1.462",
        "left":"1.462",
        "type":"limit",
        "side":"sell",
        "timestamp":1619691495.549000,
        "dealFee":"0",
        "takerFee":"0",
        "makerFee":"0",
        "dealStock":"0",
        "dealMoney":"0"
      },
      {
        ...
      },
    ],
  },
}
```

</details>

### Market History Data
Latest Deals on selected market


  <details open>
  <summary>
  </summary>

```
/api/v1/public/history/result
```


**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | STRING | YES | Any Public Market
since | NUMERIC | YES |  Min 1; Market History Since Selected `tid`
limit | NUMERIC | NO | Default 50; min 1; max 1000


**Request:**


```javascript
{
  market: "ETH_BTC" 
  since: 1
  limit: 100
}
```
url exapmle
```
/api/v1/public/history/result?market=ETH_BTC&since=1&limit=20
```

**Response Parameters:**

Name | Type | 
------------ | ------------ 
tid | NUMERIC | 
date | NUMERIC |
price | STRING |
type | STRING | 
amount | STRING |
total | STRING |


**Response:**
```javascript
[
  {
    "tid":23914004,
    "date":1619693164,
    "price":"0.05065949",
    "type":"buy",
    "amount":"0.01854886",
    "total":"0.00093968"
  },
  {
   ...
  },
]
```

</details>


### Public Pair List


  <details open>
  <summary>
  </summary>

```
/api/v1/public/symbols
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
Pair | STRING | 


**Response:**
```javascript
{
"code":200,
"success":true,
"message":"",
"result":
    [
      "AAVE_BTC",
      "BCH_BTC",
      "BCH_USD",
      "BNB_BTC",
      "BNB_ETH",
      "BNB_USD",
      "BNB_USDT",
    ]
}
```

</details>


### Depth List


  <details open>
  <summary>
  </summary>

```
/api/v1/public/depth/result
```



**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | STRING | YES | Any Public Market
limit | NUMERIC | NO | Default 50; min 1; max 1000


**Request:**


```javascript
{
  market: "ETH_BTC" 
  limit: 100 
}
```
url exapmle
```
/api/v1/public/depth/result?market=ETH_BTC&limit=2
```

**Response Parameters:**

Name | Type | Description
------------ | ------------ | ------------ 
type | STRING | asks (sell) / bids (buy)
price | STRING | 1st parametr - in left side currency
amount | STRING |2st parametr - in left side currency


**Response:**
```javascript
{
  "asks":
    [
      [
        "0.05084851", //price
        "21.437"      //amount
      ],
      [
        "0.05084951", //price
        "0.453"       //amount
      ]
    ],
  "bids":
    [
      [
        "0.05084649", //price
        "3.99"        //amount
      ],
      [
        "0.05084549", //price
        "18.15"       //amount
      ]
    ]
}
```

</details>

### List of Graphic Data KLine


  <details open>
  <summary>
  </summary>

```
/api/v1/public/kline
```



**Request Parameters:**

Name | Type | Mandatory | Description
------------ | ------------ | ------------ | ------------
market | STRING | YES | Any Public Market
start | NUMERIC | YES |  unixtime from
end | NUMERIC | YES | unixtime to
interval | NUMERIC | NO | Default 60; min 60; max 604800 sec


**Request:**


```javascript
{
  market: "ETH_BTC" 
  start: 1601359005 
  end: 1601389005 
  interval: 60 
}
```
url exapmle
```
/api/v1/public/kline?market=ETH_BTC&start=1619695680&end=1619699280&interval=60
```

**Response Parameters:**

Name | Type | 
------------ | ------------ 
time | NUMERIC | 
open | STRING |
close | STRING |
highest | STRING | 
lowest | STRING |
volume | STRING |
amount | STRING | 
market | STRING | 

**Response:**
```javascript
{
"code":200,
"success":true,
"message":"",
"result":
  {
    "market":"ETH_BTC",
    "start":1619695680,
    "end":1619699280,
    "interval":60,
    "kline":
      [
        {
          "time":1619695680,
          "open":"0.05068385",
          "close":"0.05068196",
          "highest":"0.05069765",
          "lowest":"0.0506626",
          "volume":"0.45754085",
          "amount":"0.0231873341977513",
          "market":"ETH_BTC"
        },
        {
          ...
        },
     ]
  }
}
```

</details>



# WebSoket Protocol API

The API is based on JSON RPC of Websocket protocol. 
Repeated subscription will be cancelled for the same data type.

**System Methods**

* [PING-PONG Method](#ping-pong-method) - used to test the health of sockets
* [System Time Method](#system-time-method) - used to synchronize with socket service times
* [Web-Soket Authentication](#web-soket-authentication) - used to subscribe on private methods

**Public Methods**

* [KLine methods for Graph](#kline-methods-for-graph) - used to get data on charts (the change comes after a trade and chart data change)
* [Market Price Methods](#market-price-methods) - used to get information about the market price for a specific pair (the change comes after a trade and a change in the market price)
* [Market Price Methods](#market-price-methods) - used to get information about market activity for a specific pair (the change comes after a trade and a change in market parameters)
* [Deals Methods](#deals-methods) - used to get information about the last trade in the market (the change comes after the execution of a trade)
* [Depth Methods](#depth-methods) - used to get information on order books and monitor their changes in the market (the change comes after placing, canceling, executing, changing an order)

**Private Methods**

* [Trade Users Balances Methods](#trade-user-balances-methods) - used to get the balances of the user from which the request is made (the change comes after updating the value of any balance) 
* [Active Orders and Orders History Methods](#active-orders-and-orders-history-methods) - used to get active orders and their history for a specific user from which the request is made (the change comes after placing, canceling, executing, changing an order)



## Basic WS structure

**Request**

* Endpoint
```
wss://localtrade.cc/ws
```

Name | Type | 
------------ | ------------ 
method | STRING | 
params | ARRAY |
id | INTEGER |


**Response**


Name | Type | Success | Error
------------ | ------------ | ------------ | ------------ 
result | JSON Object | Response | Failure
Error | JSON Object | NULL | Failure
id | Integer | Integer | Integer


**Notify**

Name | Type | 
------------ | ------------ 
method | STRING | 
params | ARRAY |
id | NULL |


**General error code:**

* invalid argument
* internal error
* service unavailable
* method not found
* service timeout
* require authentication


## PING-PONG Method
  <details open>
  <summary>
  </summary>


**Method**
```
server.ping
```

**Request**
```javascript
{
  "method":"server.ping",
  "params":[],
  "id":1000}
}
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
result | STRING | 
ID | NUMERIC |
Error | NULL |



**Response:**
```javascript
{
  "error": null, 
  "result": "pong",
  "id": 1000
}
```

</details>

## System Time Method
  <details open>
  <summary>
  </summary>


**Request:**

**Method**
```
server.time
```

**Request**
```javascript
{
  "method":"server.time",
  "params":[],
  "id":0
}
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
result | TimeStamp, INTEGER | 
ID | NUMERIC |
Error | NULL |



**Response:**
```javascript
{
  "error": null, 
  "result": 1493285895,
  "id": 1000
}
```

</details>


## Web-Soket Authentication
  <details open>
  <summary>
  </summary>


**Request:**

**Method**
```
server.auth
```


**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
Token | STRING | Personal user token ( check  [Authorization](#authorization) )
Source | STRING | Defoult: web; Max: 30 bytes; Custom configuration for understendong source of web-soket info



**Request**
```javascript
{
  "method":"server.auth",
  "params":
    [
      "$2y$10$J3gXRK...ZOH2yGXTlOcp9iBUq"
      "web"
    ],
  "id":0
}
```


**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
Status | STRING | Success - auth compleated




**Response:**
```javascript
{
  id: 2,
  params: 
    [
    ], 
  result: 
    {
      status: "success"
    },
  error: null
}
```

</details>

# KLine methods for Graph


### KLine Query Method
  <details open>
  <summary>
  </summary>


**Method**
```
kline.query
```

**Request Parameters:**

Name | Type | 
------------ | ------------ 
market | STRING | 
interval | NUMERIC |

**Request:**
```javascript
{
  "method":"kline.query",
  "params":
    [
      "ETH_BTC",
      3600
    ],
  "id":1000
}
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
time | TimeStamp, INTEGER | 
open | STRING |
close | STRING |
highest | STRING |
lowest | STRING |
volume | STRING |
amount | STRING |
market | STRING |



**Response:**
```javascript
{
  "method": "kline.query",
  "params": 
    [
      [
        1568548260, //time
        "0.018302", //open
        "0.018302", //close
        "0.018302", //highest
        "0.018302", //lowest
        "500",      //volume
        "15",       //amount
        "ETH_BTC"   //market
      ]
    ],
  "id": 1000
}
```

</details>

### KLine Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
kline.subscribe
```

**Request Parameters:**

Name | Type | 
------------ | ------------ 
market | STRING | 
interval | NUMERIC |

**Request:**
```javascript
{
  "method":"kline.subscribe",
  "params":
    [
      "ETH_BTC",
      3600
    ],
  "id":1000
}
```


**Response Parameters:**


```
kline.update
```


Name | Type | 
------------ | ------------ 
time | TimeStamp, INTEGER | 
open | STRING |
close | STRING |
highest | STRING |
lowest | STRING |
volume | STRING |
amount | STRING |
market | STRING |



**Response:**

```javascript
{
  "method": "kline.update",
  "params": 
    [
      [
        1568548260, //time
        "0.018302", //open
        "0.018302", //close
        "0.018302", //highest
        "0.018302", //lowest
        "500",      //volume
        "15",       //amount
        "ETH_BTC"   //market
      ]
    ],
  "id": null
}
```

</details>


### KLine Unsubscribe Method
  <details open>
  <summary>
  </summary>
  
  
**Method**

  
```
kline.unsubscribe
```

**Request**
```javascript
{
  "method":"kline.unsubscribe",
  "params":[],
  "id":16
}
```
</details>


## Market Price Methods


### Market Price Query Method
  <details open>
  <summary>
  </summary>


**Method**
```
price.query
```

**Request Parameters:**

Name | Type | Descriprion |
------------ | ------------ | ------------ 
market | STRING | Can be few markets per request


**Request:**
```javascript
{
  "method":"price.query",
  "params":
    [
      "ETH_BTC",
    ],
  "id":111
}
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
market | STRING |
price | STRING |



**Response:**
```javascript
{ 
  "method":"price.query",
  "params":
    [
      "ETH_BTC",
      "0.01860351"
    ],
  "id":111
}
```

</details>

### Market Price Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
price.subscribe
```


**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Can be few markets per request


**Request:**
```javascript
{
  "method":"price.subscribe",
  "params":
    [
      "ETH_BTC",
    ],
  "id":111
}
```

**Response Parameters:**


```
price.update
```


Name | Type | 
------------ | ------------ 
market | STRING |
price | STRING |



**Response:**
```javascript
{ 
  "method":"price.update",
  "params":
    [
      "ETH_BTC",
      "0.01860351"
    ],
  "id":null
}
```

</details>


### Market Price Unsubscribe Method
  <details open>
  <summary>
  </summary>
  
  
**Method**

  
```
price.unsubscribe
```

**Request**
```javascript
{
  "method":"price.unsubscribe",
  "params":[],
  "id":16
}
```
</details>


## Market Status Methods


### Market status Query Method
  <details open>
  <summary>
  </summary>


**Method**
```
state.query
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Any exchange market
period | NUMERIC | Default: 86400



**Request:**
```javascript
{
  "method":"state.query",
  "params":
    [
      "ETH_BTC",
      86400
    ],
  "id":111
}
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
period | NUMERIC |
volume | STRING |
last | STRING |
open | STRING |
low | STRING |
close | STRING |
high | STRING |
deal | STRING |


**Response:**
```javascript
{
  "result": 
    {
      "period": 86400, 
      "volume": "192238.62392908",
      "last": "0.018295",
      "open": "0.017526",
      "low": "0.0174",
      "close": "0.018295",
      "high": "0.02",
      "deal": "3479.25570915213257"
    },
  "error": null,
  "id": 111
}
```

</details>

### Market Status Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
state.subscribe
```


**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Any exchange market
period | NUMERIC | Default: 86400



**Request:**
```javascript
{
  "method":"state.subscribe",
  "params":
    [
      "ETH_BTC",
      86400
    ],
  "id":111
}
```

**Response Parameters:**

```
state.update
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
period | NUMERIC |
volume | STRING |
last | STRING |
open | STRING |
low | STRING |
close | STRING |
high | STRING |
deal | STRING |


**Response:**
```javascript
{
  "result": 
    {
      "period": 86400, 
      "volume": "192238.62392908",
      "last": "0.018295",
      "open": "0.017526",
      "low": "0.0174",
      "close": "0.018295",
      "high": "0.02",
      "deal": "3479.25570915213257"
    },
  "error": null,
  "id": null
}
```

</details>


### Market Status Unsubscribe Method
  <details open>
  <summary>
  </summary>
  
  
**Method**

  
```
state.unsubscribe
```

**Request**
```javascript
{
  "method":"state.unsubscribe",
  "params":[],
  "id":16
}
```
</details>

## Deals Methods 


### Deals Query Method 
  <details open>
  <summary>
  </summary>


**Method**
```
deals.query
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Can be few markets per request


**Request:**
```javascript
{
  "method":"deals.query",
  "params":
    [
      "ETH_BTC",
      "BTC_USD"
    ],
  "id":111
}
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
type | NUMERIC |
time | STRING |
id | NUMERIC |
amount | STRING |
price | STRING |


**Response:**
```javascript
{
  "method": "deals.query",
  "params": 
    [
      "ETH_BTC",
        [
          {
            "type": "sell",
            "time": 1568556382.1329091,
            "id": 5478754,
            "amount": "4.9193309",
            "price": "10365.40703518"
          }
        ],
      "BTC_USD",
        [
          {
            ...
          }
        ],
    ],
  "id": 111
}
```

</details>

### Deals Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
deals.subscribe
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Can be few markets per request



**Request:**
```javascript
{
  "method":"deals.subscribe",
  "params":
    [
      "ETH_BTC",
      "BTC_USD"
    ],
  "id":111
}
```

**Response Parameters:**

```
deals.update
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
type | NUMERIC |
time | STRING |
id | NUMERIC |
amount | STRING |
price | STRING |


**Response:**
```javascript
{
  "method": "deals.update",
  "params": 
    [
      "ETH_BTC",
        [
          {
            "type": "sell",
            "time": 1568556382.1329091,
            "id": 5478754,
            "amount": "4.9193309",
            "price": "10365.40703518"
          }
        ],
      "BTC_USD",
        [
          {
            ...
          }
        ],
    ],
  "id": null
}
```

</details>


### Deals Unsubscribe Method
  <details open>
  <summary>
  </summary>
  
  
**Method**

  
```
deals.unsubscribe
```

**Request**
```javascript
{
  "method":"deals.unsubscribe",
  "params":[],
  "id":16
}
```
</details>


## Depth Methods 


### Depth Query Method 
  <details open>
  <summary>
  </summary>


**Method**
```
depth.query
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Any market pair
limit | NUMERIC | Limit of order quantity
interval | STRING | Defoult: 1, No interval: 0, Step: 1

**Request:**
```javascript
{
  "method":"depth.query",
  "params":
    [
      "ETH_BTC",  //market
      1,          //limit
      "0"         //interval
    ],
  "id":111
}
```


**Response Parameters:**

Name | Type | 
------------ | ------------ 
type | STRING |
id | NUMERIC |
amount | STRING |
price | STRING |


**Response:**
```javascript
}
  "method":"depth.query",
  "result": 
   {
      "asks": 
       [
          [
            "8000.00", //price
            "9.6250"   //amount
          ]
       ],
      "bids": 
       [
         [
           "7000.00",  //price
           "0.1000"    //amount
         ]
       ]
   "id": 111,
}
```

</details>

### Depth Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
depth.subscribe
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Any market pair
limit | NUMERIC | Limit of order quantity
interval | STRING | Defoult: 1, No interval: 0, Step: 1

**Request:**
```javascript
{
  "method":"depth.subscribe",
  "params":
    [
      "ETH_BTC",    //market
      1,            //limit
      "0"           //interval
    ],
  "id":111
}
```

**Response Parameters:**

```
depth.update
```


**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
clean | BOOLEAN | FALSE: returned latest result, TRUE - no updates
limit | NUMERIC | Return update from last result with limit
market | STRING | Subscribed market
id | NUMERIC | Request ID
Type | String | Order type 
Amount | String | order amount in 1st Ticker
Price | String | order price in 1st Ticker



**Response:**
```javascript
{
  "method": "depth.update",
  "params": 
    [
      true, 
        {
          "asks": 
            [
              [
                "0.018519", //price
                "120.6"     //amount
              ]
            ],
          "bids": 
            [
              [
                "0.01806",    //price
                "90.31637262" //amount
              ]
            ]
        }, 
      "ETH_BTC"
    ],
  "id": null
}
```

</details>


### Depth Unsubscribe Method
  <details open>
  <summary>
  </summary>
  
  
**Method**

  
```
depth.unsubscribe
```

**Request**
```javascript
{
  "method":"depth.unsubscribe",
  "params":[],
  "id":16
}
```
</details>


## Trade User Balances Methods 


### Asset Query Method 
  <details open>
  <summary>
  </summary>


**Method**
```
asset.query
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
asset list | STRING | Null: for all; Asset List: for choosed


**Request:**
```javascript
{
  "method":"asset.query",
  "params":
    [
      "BTC"
    ],
  "id":111
}
```


**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Asset name
available | NUMERIC | Amount without active orders
freeze | STRING | active orders amount



**Response:**
```javascript
{
  "BTC":
    {
      "available": "1.10000000",
      "freeze": "9.90000000"
    }
}

```

</details>

### Asset Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
asset.subscribe
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
asset list | STRING | Null: for all; Asset List: for choosed

**Request:**
```javascript
{
  "method":"asset.subscribe",
  "params":
    [
      "BTC",
      "CNY"
    ],
  "id":111
}
```

**Response Parameters:**

```
asset.update
```


**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Asset name
available | NUMERIC | Amount without active orders
freeze | STRING | active orders amount



**Response:**
```javascript
[
  {
    "BTC: 
      {
        "available": "1.10000000",
        "freeze": "9.90000000"
      },
    "CNY": 
      {
      }
  }
]

```

</details>


### Asset Unsubscribe Method
  <details open>
  <summary>
  </summary>
  
  
**Method**

  
```
asset.unsubscribe
```

**Request**
```javascript
{
  "method":"asset.unsubscribe",
  "params":[],
  "id":111
}
```
</details>



## Active Orders and Orders History Methods 



### Order Query Method 
  <details open>
  <summary>
  </summary>


**Method**
```
order.query
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | For all pairs: ""; Special: Choosed Pair
offset | INTEGER | NO | Default 0
limit | INTEGER | Defoult: 1, No interval: 0, Step: 1

**Request:**
```javascript
{
  "method":"order.query",
  "params":
    [
      "",      //market
      1,          //offset
      30           //interval
    ],
  "id":6
}
```


**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
algorithm | INTEGER | Spot: 0; Margin: 1
amount | NUMERIC | Order amount in first ticker of Pair (Asset)
ctime | TIMESTAMP | Time of order placed
deal_fee | STRING | user fee coefficient 
deal_money | STRING | Order amount in second ticker of Pair (Market)
deal_stock | STRING | Order amount in first ticker of Pair (Asset)
id | INTEGER | Order ID
left | STRING | Order left (if not traded = amount = deal_stock; if 0 - order finished)
maker_fee | STRING | Fee of order placer
market | STRING | Order Pair
mtime | INTEGER | Matching time ( time of finishing)
platform | INTEGER | 0 - Limit / Market; 1 - Stop-limit; 2 - OCO
price | STRING | Order Price
side | INTEGER | 1 - Sell (Ask) 2- Buy (Bid)
source | STRING | Custom Parametr
taker_fee | STRING |  Fee of order tacker
type | INTEGER | 1 - Limit; 2 - Market
user | INTEGER | - User ID

**Response:**
```javascript

{
  id: 6,
  params:
    [
      6,
      "all",
      0,
      30
    ],
  result:
    {
      limit: 30
      market_name: "all"
      offset: 0
      records:
        {
          algorithm: 0
          amount: "1"
          ctime: 1620561060.783
          deal_fee: "0"
          deal_money: "0"
          deal_stock: "0"
          id: 224280948
          left: "1"
          maker_fee: "0.002"
          market: "ETH_EUR"
          mtime: 0
          platform: 0
          price: "5000"
          side: 1
          source: ""
          taker_fee: "0.002"
          type: 1
          user: 6
        }
      total: 1,
    }
}
  

```

</details>


### Order History Method 
  <details open>
  <summary>
  </summary>


**Method**
```
order.history
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | Any market pair
start_time | INTEGER | Unlimited: 0; Timestamp: custom
end_time | INTEGER | Unlimited: 0; Timestamp: custom
offset | INTEGER | NO | Default 0
limit | NUMERIC | Limit of order quantity
side | STRING | Unlimited: 0, Sell: 1, Buy: 2


**Request:**
```javascript
{
  method: "order.history",
  params: 
    [
      "",
      0,
      0,
      0,
      30
    ],
  id: 10
}

```



**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
algorithm | INTEGER | Spot: 0; Margin: 1
amount | NUMERIC | Order amount in first ticker of Pair (Asset)
ctime | TIMESTAMP | Time of order placed
deal_fee | STRING | user fee coefficient 
deal_money | STRING | Order amount in second ticker of Pair (Market)
deal_stock | STRING | Order amount in first ticker of Pair (Asset)
id | INTEGER | Order ID
left | STRING | Order left (if not traded = amount = deal_stock; if 0 - order finished)
maker_fee | STRING | Fee of order placer
market | STRING | Order Pair
mtime | INTEGER | Matching time ( time of finishing)
platform | INTEGER | 0 - Limit / Market; 1 - Stop-limit; 2 - OCO
price | STRING | Order Price
side | INTEGER | 1 - Sell (Ask) 2- Buy (Bid)
source | STRING | Custom Parametr
taker_fee | STRING |  Fee of order tacker
type | INTEGER | 1 - Limit; 2 - Market
user | INTEGER | - User ID

**Response:**
```javascript

{
  id: 6,
  params:
    [
      "",
      0,
      0,
      0,
      30
    ],
  result:
    {
      limit: 30
      start_time: 0
      end_time: 0
      market_name: ""
      side: 0
      offset: 0
      records:
        {
          algorithm: 0
          amount: "1"
          ctime: 1620561060.783
          deal_fee: "0"
          deal_money: "0"
          deal_stock: "0"
          id: 224280948
          left: "1"
          maker_fee: "0.002"
          market: "ETH_EUR"
          mtime: 0
          platform: 0
          price: "5000"
          side: 1
          source: ""
          taker_fee: "0.002"
          type: 1
          user: 6
        }
    }
}
```
</details>

### Order Subscribe Method
  <details open>
  <summary>
  </summary>

**Method**
```
order.subscribe
```

**Request Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
market | STRING | For all pairs: ""; Special: Choosed Pair
offset | INTEGER | NO | Default 0
limit | INTEGER | Defoult: 1, No interval: 0, Step: 1

**Request:**
```javascript
{
  "method":"order.subscribe",
  "params":
    [
      "BTC_USDT",      //market
    ],
  "id":6
}
```

</details>

```
order.update
```

**Response Parameters:**

Name | Type | Description |
------------ | ------------ | ------------ 
algorithm | INTEGER | Spot: 0; Margin: 1
amount | NUMERIC | Order amount in first ticker of Pair (Asset)
ctime | TIMESTAMP | Time of order placed
deal_fee | STRING | user fee coefficient 
deal_money | STRING | Order amount in second ticker of Pair (Market)
deal_stock | STRING | Order amount in first ticker of Pair (Asset)
id | INTEGER | Order ID
left | STRING | Order left (if not traded = amount = deal_stock; if 0 - order finished)
maker_fee | STRING | Fee of order placer
market | STRING | Order Pair
mtime | INTEGER | Matching time ( time of finishing)
platform | INTEGER | 0 - Limit / Market; 1 - Stop-limit; 2 - OCO
price | STRING | Order Price
side | INTEGER | 1 - Sell (Ask) 2- Buy (Bid)
source | STRING | Custom Parametr
taker_fee | STRING |  Fee of order tacker
type | INTEGER | 1 - Limit; 2 - Market
user | INTEGER | - User ID

**Response:**
```javascript
result:
    {
        {
          algorithm: 0
          amount: "1"
          ctime: 1620561060.783
          deal_fee: "0"
          deal_money: "0"
          deal_stock: "0"
          id: 224280948
          left: "1"
          maker_fee: "0.002"
          market: "ETH_EUR"
          mtime: 0
          platform: 0
          price: "5000"
          side: 1
          source: ""
          taker_fee: "0.002"
          type: 1
          user: 6
        }
    }

```
</details>


### Order Unsubscribe Method
  <details open>
  <summary>
  </summary>
  
  
**Method**

  
```
order.unsubscribe
```

**Request**
```javascript
{
  "method":"order.unsubscribe",
  "params":[],
  "id":16
}
```
</details>
