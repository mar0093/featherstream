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
At this point I don't believe demo accounts work. But we will have to keep trying.
