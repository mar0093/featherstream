---
title: "Using IG API"
date: 2021-09-27
---

Here we disclose how to gather historical data, pricing data & how to make trades. The examples below are executed with a training account.

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
### Submitting A Trade

Here we will demonstrate how to conduct a trade using the `trading-ig` library. For this we will be using our demo account.

The snippet of code below opens a position:

```py
trade = ig_service.create_open_position(
    epic='AB.D.AXEAU.CASH.IP',
    expiry='-',
    direction='BUY',
    size=100,
    guaranteed_stop='false',
    order_type='MARKET',
    currency_code='AUD',
    force_open='true',
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
 {'date': '2021-09-27T07:58:53.637', 'status': None, 'reason': 'MARKET_CLOSED_WITH_EDITS', 'dealStatus': 'REJECTED', 'epic': 'AB.D.AXEAU.CASH.IP', 'expiry': None, 'dealReference': 'CAHYUTYLJWNTZDZ', 'dealId': 'DIAAAAGK3Z9HABB', 'affectedDeals': [], 'level': None, 'size': None, 'direction': 'BUY', 'stopLevel': None, 'limitLevel': None, 'stopDistance': None, 'limitDistance': None, 'guaranteedStop': False, 'trailingStop': False, 'profit': None, 'profitCurrency': None}
 ```

 This will have to be tested during the day.
