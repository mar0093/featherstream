---
title: "Using IG API"
date: 2021-09-27
---

Here we disclose how to gather historical data, pricing data & how to make trades. The examples below are executed with a training account.

Using the [**API Companion**](https://labs.ig.com/sample-apps/api-companion/index.html) also allows us to test a lot of scripts we see here below.

---

### Historical Data

To Gather Historical information is simple enough but it has a few caveats. Most instruments allow for historical information t be displayed.

To display the historical information we require 3 parameters.

1. **epic**
  - The identifier for the market instrument
  - Example `CS.D.EURUSD.MINI.IP`
2. **resolution**
  - The grain at which information is supplied at, in appreciating order.
  - Examples
    - `S` Seconds
    - `MIN` Minutes
    - `D` Days
    - `W` Weeks
    - `M` Months
3. **num_points**
  - The number of rows returned
  - Example `5` (Would return 5 rows.)

  Putting this together we can use the `fetch_historical_prices_by_epic_and_num_points()` method as has been used below.

  ```py
  # Example of gathering Historical data
epic = 'CS.D.EURUSD.MINI.IP'
resolution = 'S'
num_points = 5
response = ig_service.fetch_historical_prices_by_epic_and_num_points(epic, resolution, num_points)
df_ask = response['prices']['ask']
print("ask prices:\n%s" % df_ask)
  ```

This Snippet will grab the market  `CS.D.EURUSD.MINI.IP` and return the last 5 secs of bids, out put as seen below:

```txt
ask prices:
                        Open     High      Low    Close
DateTime                                               
2021-09-27 13:45:37  1.17247  1.17248  1.17247  1.17248
2021-09-27 13:45:43  1.17247  1.17248  1.17247  1.17248
2021-09-27 13:45:46  1.17247  1.17247  1.17247  1.17247
2021-09-27 13:45:47  1.17248  1.17248  1.17248  1.17248
2021-09-27 13:45:56  1.17247  1.17247  1.17247  1.17247

Process finished with exit code 0
```

Unfortunately most Australian shares are not available to see historical trade data.
as can bee seen here:

```py
epic = 'AB.D.AXEAU.CASH.IP'
resolution = 'S'
num_points = 5
response = ig_service.fetch_historical_prices_by_epic_and_num_points(epic, resolution, num_points)
df_ask = response['prices']['ask']
print("ask prices:\n%s" % df_ask)
```

Produces the error:
```txt
Traceback (most recent call last):
  File "C:/Users/james/PycharmProjects/IG/trading_ig_hello_world.py", line 24, in <module>
    response = ig_service.fetch_historical_prices_by_epic_and_num_points(epic, resolution, num_points)
  File "C:\Users\james\PycharmProjects\IG\venv\lib\site-packages\trading_ig\rest.py", line 1438, in fetch_historical_prices_by_epic_and_num_points
    data = self.parse_response(response.text)
  File "C:\Users\james\PycharmProjects\IG\venv\lib\site-packages\trading_ig\rest.py", line 239, in parse_response
    raise (Exception(response["errorCode"]))
Exception: unauthorised.access.to.equity.exception
```

We can check whether an instrument has historical prices (or whether prices can be streamed by STREAM API) the `streamingPricesAvailable` dictionary option. As seen below.

```py
market = ig_service.fetch_market_by_epic('AB.D.AXEAU.CASH.IP')
print("Streaming Available: %s" % market.instrument.streamingPricesAvailable)
```

Producing the output:

```txt
Streaming Available: False
```

#### Output

The output of the historical information for a lot of the objects are pandas `series`, `dataframes` or `panels`. The method *`fetch_historical_prices_by_epic_and_num_points(epic, resolution, num_points)`* produces what is known as a series. A problem I found arising was the the response object returned as a dictionary didn't have a nice functionality to include data from 2 different streams into 1 data source.

The solution to this is to define 2 series and then combine. This is carried out below, using the response dict object we produced previously.

```py
df_last = response['prices']['last']['Volume']
df_ask = response['prices']['ask']
joineddf = pd.concat([df_last,df_ask], axis=1)

print("Including Volume prices:\n%s" % joineddf)
```

Producing the following:

```txt
Including Volume prices:
            Volume     Open     High      Low    Close
DateTime                                              
2021-09-22   93886  1.17312  1.17380  1.17152  1.17315
2021-09-23  136513  1.17314  1.17561  1.16838  1.17395
2021-09-24   96358  1.17396  1.17507  1.17009  1.17124
2021-09-25   29383  1.17123  1.17259  1.17047  1.17214
2021-09-27   18940  1.17275  1.17321  1.17170  1.17245
```



---
### Opening A Position

Here we will demonstrate how to conduct a trade using the `trading-ig` library. For this we will be using our demo account.

The snippet of code below opens a position:

```py
open_trade = ig_service.create_open_position(
    epic='AA.D.CBA.CASH.IP',
    expiry='-',
    direction='BUY',
    size='100',
    guaranteed_stop='false',
    order_type='MARKET',
    currency_code='AUD',
    force_open='false',
    level=None,
    limit_distance=None,
    limit_level=None,
    quote_id=None,
    stop_distance=None,
    stop_level=None,
    trailing_stop=None,
    trailing_stop_increment=None
    )
print(trade)
```
 As the output shows, the position as shown here in the API companion.

 ![order companion](/images/api/ig/using-ig/order-companion.png?classes=border,shadow "start now")

 ```txt
{'date': '2021-09-28T02:41:22.398', 'status': 'OPEN', 'reason': 'SUCCESS', 'dealStatus': 'ACCEPTED', 'epic': 'AA.D.CBA.CASH.IP', 'expiry': '-', 'dealReference': 'PBPKRFVDW5STZDZ', 'dealId': 'DIAAAAGLAR9A5AQ', 'affectedDeals': [{'dealId': 'DIAAAAGLAR9A5AQ', 'status': 'OPENED'}], 'level': 105.8, 'size': 100.0, 'direction': 'BUY', 'stopLevel': None, 'limitLevel': None, 'stopDistance': None, 'limitDistance': None, 'guaranteedStop': False, 'trailingStop': False, 'profit': None, 'profitCurrency': None}
 ```

We can buy CBA shares under a CFD account. It also allows us to buy 500 shares, which is worth ~$50,000 under a CFD account. need to check if we can get a demo share trading account and if the same rules apply.

We also note that under a CFD account certain shares cannot be purchased. An example of this is Marcher Material (AXE) Ltd. We know this by the `specialInfo` information returned.

```py
#Get info
market = ig_service.fetch_market_by_epic('AB.D.AXEAU.CASH.IP')
print("Special Info: %s" % market.instrument.specialInfo)
```

which the returned information exclaims:

```
Special Info: ['Non-leveraged only', 'Unborrowable', 'DEFAULT KNOCK OUT LEVEL DISTANCE', 'MAX KNOCK OUT LEVEL DISTANCE']
```
Which denotes that we cannot we cannot leverage this stock with a CFD account.

---

### Closing a position

To close a position pass a command similar to the following.

```py
close_trade = ig_service.close_open_position(
    deal_id=deal_id,
    direction='SELL',
    epic=None,
    expiry='-',
    level=None,
    order_type='MARKET',
    quote_id=None,
    size='100'
      )
```

Note that whilst an epic is a parameter that is available, we do not use it to close our position with the same epic.

#### Costs

The cost for trading shares id **$5 or 0.05%** (whichever is higher). For example, we bought
100 shares @ 105.8 = $10,580.00. We were charged $10.58. for commission. So that means we were charged 0.001% on the demo account for the purchase. We were then charged the same amount for the sale. So all up, the transaction cost 0.002%. This is lower than the expected change in price.

We also have been told that Buying and selling US, UK, Ireland & Germany shares are free*. As long as foreign exchange is [automated.](https://www.ig.com/au/share-trading/charges) this is yet to be tested.
