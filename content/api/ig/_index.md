---
title: "IG"
date: 2021-09-23
---

Here we disclose how the IG Trading API is connected to featherstream.

---

### Step 1. Sign up

First we need to sign up to register an account with IG. Visit the IG Labs website.

[https://labs.ig.com/](https://labs.ig.com/)

![IG website](/images/api/ig/ig-website.png?classes=border,shadow "ig-website")

Scroll to the bottom of the page and select "Start Now"

![Start now](/images/api/ig/start-now.png?classes=border,shadow "start now")

We then want to apply for a new account by clicking the link provided

[https://www.ig.com/au/application-form](https://www.ig.com/au/application-form)

![Apply Here](/images/api/ig/apply-here.png?classes=border,shadow "apply-here")

Follow the Sign Up prompts:

#### Profile

![Apply Here](/images/api/ig/profile.png?classes=border,shadow "profile")

#### Finances
Ensure that you enable *'Share Trading'* and also *'CFD and FX trading'*

Note that if using IG as a professional product IG will need to be consulted at the address:

[sales.au@ig.com](sales.au@ig.com)

---

### Step 2. Create Demo Account

Once the IG account has been created a demo account will need to be created. On top of the navigation bar click on *demo

![Apply Here](/images/api/ig/demo-account.png?classes=border,shadow "profile")

---

### Step 3. Install *trading_ig*

Using PIP installer for your environment install the light wrapper for the IG API *'trading_ig'*. Make sure you run this command in your terminal for the python environment.
```
pip install trading_ig
```

For dependency reasons you will also have to install `pandas` `tenacity` & `munch`

To read more as to why follow the bug forum below [here.](https://github.com/ig-python/ig-markets-api-python-library/issues/215)


### Creating a Configuration file

To use the IG API you need a configuration file to be set. Create one called `trading_ig_config.py` and should have the class config as it appears below:

```py
class config(object):
    username = "your_username"
    password = "your_password"
    api_key = "your_api_key"
    acc_type = "DEMO"
    acc_number = "your_account_number"
```

{{% notice info %}}
IG Demo account works 24 hours after setup. It make take time to be validated on the end of IG
{{% /notice %}}

### Creating Hello_World! Script

To test the configuration file run the following script.

```py
from trading_ig.rest import IGService
from trading_ig.config import config

# Create session
ig_service = IGService(config.username, config.password, config.api_key, config.acc_type)
ig_service.create_session()


# Searching for a market
market_search = ig_service.search_markets("asx")
print(market_search)

# Get market information
market = ig_service.fetch_market_by_epic('AA.D.ASX.CASH.IP')
print(market)
```

The script will *import* the REST API for IG & and the configuration file written in the section above.

It will then do 3 things
1. Create a session.
2. Search for a market
3. Gather market information

If you want to read further as to how to use the python wrapper you read read the docs [here.](https://trading-ig.readthedocs.io/en/latest/)
