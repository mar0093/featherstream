---
title: "Transformer"
date: 2021-11-18
weight: 35
---

Here we will work through the example Authored by Jan Schmitz. We will breakdown his juypter notebook for his trading bot to get a greater understanding of how to ustilise
the Transformer Neural Net model to predict stocker prices.

### Setup

First we run the following lines of code.

```py
import numpy as np
import pandas as pd
import tensorflow as tf
from tensorflow.keras.models import *
from tensorflow.keras.layers import *
print('Tensorflow version: {}'.format(tf.__version__))

import matplotlib.pyplot as plt
plt.style.use('seaborn')

import warnings
warnings.filterwarnings('ignore')
```

For this realise we need to install Tensorflow, at the time of writing this the version is 2.7.0

```sh
pip install tensorflow
```

We also are required to install 11.0 CUDA which we can do through the following link

https://developer.nvidia.com/cuda-11.0-download-archive?target_os=Windows&target_arch=x86_64&target_version=10&target_type=exenetwork

we should have the returned in our terminal

```sh
Tensorflow version: 2.7.0
```

### References

https://github.com/JanSchm/CapMarket/blob/master/bot_experiments/IBM_PriceFeatures.ipynb?fbclid=IwAR3wxD-n7_iI8yWA5zolltev8FkYqNe79Wqd1B5PKP8rgkCcNMpU-ygMBYc
