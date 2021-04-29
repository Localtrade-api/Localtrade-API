## Return Codes
  <details open>
  <summary>
  </summary>

* HTTP `200` return code is used when the Succesful responce;
* HTTP `401` return code is used when the request made by Unauthorized user;
* HTTP `403` return code is used when the request made to Forbidden data from user without necessary permissions;
* HTTP `404` return code is used when the request made with undefined data and service can Not Found data for response.

  </details>

## General Information on Endpoints
  <details open>
  <summary>
  </summary>

* All public methods requested with `GET` and consist only publick data. 
* To receive all data and work with private data, use `POST` with API keys (Read Authorization)

    </details>


## Methods with Puplic Data

All public methods requested with `GET` and consist only publick data.

### List of Puplic Pairs

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

### List of Puplic Tickers

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
      market: "ETH_BTC" //ETH_USDT, BTC-ETH ...etc
      side: "sell" //or buy
      offset: 0 //optional; default value 0
      limit: 100 //optional; default value = 50
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

### Market History Data ( Latest Deals on selected market )


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
        "0.05084851",
        "21.437"
      ],
      [
        "0.05084951",
        "0.453"
      ]
    ],
  "bids":
    [
      [
        "0.05084649",
        "3.99"
      ],
      [
        "0.05084549",
        "18.15"
      ]
    ]
}
```

</details>

### List of Graphic Data (KLine)


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
