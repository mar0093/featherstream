---
title: "Introduction to Transformer"
date: 2021-12-03
weight: 50
math: true
---

Transformers are a component used in neural network design

##References

CNNs / wavenet / transformer-based models | Forecasting big time series | Amazon Science
  - https://www.youtube.com/watch?v=GiD87DeadYY
Pytorch Transformers from Scratch (Attention is all you need)
  - https://www.youtube.com/watch?v=U0s0f995w14
N-BEATS Github
  - https://github.com/ElementAI/N-BEATS
Neural networks – from old to new | Forecasting big time series | Amazon Science  
  - https://www.youtube.com/watch?v=XCZHJHMLD2s
A quick complete tutorial to save and restore Tensorflow models
 - https://tinyurl.com/2p8rmf76

Here we are taking our transformer example and seeing if we can modify it to do the following things.

- Accept more features. (Moving average etc.)
- Predict 7 days ahead (instead of just currently the next day.)
- Accurately pass entire asx as a training data set.
- Saving our model to be re used.

#### Accept more features

To accept more features the first feature we are going to add to the file is the moving average.

We are adding in 3, 5, 7 & 14 day moving averages. This will be our test to see how this is done.

The first addition to to add the 4 new fields as columns to our dataframe

```py
df["Close_av_14"] = df['Close'].rolling(14).mean()
df["Close_av_7"] = df['Close'].rolling(7).mean()
df["Close_av_5"] = df['Close'].rolling(5).mean()
df["Close_av_3"] = df['Close'].rolling(3).mean()
```



We run the following code to check if our data appears as expected.  

```py
print(list(df.columns.values))
print(df.head())
```

Our output appears for the dataframe as below. We can see the 'NaN' values as we can't calculate moving averages without there have being that many days that occured before hand.

```txt
['Date', 'Open', 'High', 'Low', 'Close', 'Volume', 'Close_av_14', 'Close_av_7', 'Close_av_5', 'Close_av_3']
        Date       Open       High        Low      Close   Volume  Close_av_14  Close_av_7  Close_av_5  Close_av_3
0 2016-12-09  62.777748  63.626948  62.695191  63.430374  3383119          NaN         NaN         NaN         NaN
1 2016-12-12  63.430360  63.972905  63.280969  63.626934  2327789          NaN         NaN         NaN         NaN
2 2016-12-13  63.296689  63.485398  63.107980  63.202332  2324519          NaN         NaN         NaN   63.419880
3 2016-12-14  63.768470  63.965044  63.501133  63.831375  2872286          NaN         NaN         NaN   63.553547
4 2016-12-15  63.831380  63.996501  63.037221  63.406780  4138194          NaN         NaN   63.499559   63.480162
```

We then need to normalize our moving averages. We also drop the NaN values, which in this case is the first 13 rows of the dataset.

```py
df['Close_av_14'] = df['Close_av_14'].pct_change()
df['Close_av_7'] = df['Close_av_7'].pct_change()
df['Close_av_5'] = df['Close_av_5'].pct_change()
df['Close_av_3'] = df['Close_av_3'].pct_change()

df.dropna(how='any', axis=0, inplace=True) # Drop all rows with NaN values
```

we also have to change how we define the model, the model is expecting a explicit number of features, 5. We will change that to 9 for now.
(we can hopefully make it dynamic in future. )

```py
'''Construct model'''
in_seq = Input(shape=(seq_len, 9))
```

Whilst it looks to be running the new features, (which is great!) it does look to have highlighted a bug with the saving feature used in the example.
We will try and address this next. It doesn't seem to retain loss between training.

We also will want to try the model saving feature.

#### Accept more features
#### Predict 7 days ahead
#### Pass entire ASX as a training data set
#### Saving our model

When saving the model
