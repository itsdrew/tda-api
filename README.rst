``tda-api``: A TD Ameritrade API Wrapper
========================================

.. image:: https://readthedocs.org/projects/tda-api/badge/?version=latest
  :target: https://tda-api.readthedocs.io/en/latest/?badge=latest

.. image:: https://circleci.com/gh/alexgolec/tda-api.svg?style=shield
  :target: https://circleci.com/gh/alexgolec/tda-api

.. image:: https://badge.fury.io/py/tda-api.svg
  :target: https://badge.fury.io/py/tda-api

.. image:: http://codecov.io/github/alexgolec/tda-api/coverage.svg?branch=master
  :target: http://codecov.io/github/alexgolec/tda-api?branch=master

What is ``tda-api``?
--------------------

``tda-api`` is an unofficial wrapper around the `TD Ameritrade APIs
<https://developer.tdameritrade.com/apis>`__. It strives to be as thin and
unopinionated as possible, offering an elegant programmatic interface over each
endpoint. Notable functionality includes:
* Login and authentication
* Quotes, fundamentals, and historical pricing data
* Options chains
* Trades and trade management
* Account info and preferences

How do I use ``tda-api``?
-------------------------

For a full description of ``tda-api``'s functionality, checkout the 
`Documentation <https://tda-api.readthedocs.io/en/latest/>`__. Meawhile, here's 
a quick getting started guide:

Before you do anything, create an account and an application on the
`TD Ameritrade developer website <https://developer.tdameritrade.com/>`__.
You'll receive an API key, also known as a Client Id, which you can pass to this 
wrapper. You'll also want to take note of your callback URI, as the login flow 
requires it.

Next, install ``tda-api``:

.. code-block:: python

  pip install tda-api

You're good to go! To demonstrate, here's how you can authenticate and fetch
daily historical price data for the past twenty years:

.. code-block:: python

  from tda import auth, client
  import json

  token_path = '/path/to/token.pickle'
  api_key = 'YOUR_API_KEY@AMER.OAUTHAP'
  redirect_uri = 'https://your.redirecturi.com'
  try:
      c = auth.client_from_token_file(token_path, api_key)
  except FileNotFoundError:
      from selenium import webdriver
      with webdriver.Chrome() as driver:
          c = auth.client_from_login_flow(
              driver, api_key, redirect_uri, token_path)

  r = c.get_price_history('AAPL',
          period_type=client.Client.PriceHistory.PeriodType.YEAR,
          period=client.Client.PriceHistory.Period.TWENTY_YEARS,
          frequency_type=client.Client.PriceHistory.FrequencyType.DAILY,
          frequency=client.Client.PriceHistory.Frequency.DAILY)
  assert r.ok, r.raise_for_status()
  print(json.dumps(r.json(), indent=4))

Why should I use ``tda-api``?
-----------------------------

``tda-api`` was designed to provide a few important pieces of functionality:

1. **Safe Authentication**: TD Ameritrade's API supports OAuth authentication, 
   but too many people online end up rolling their own implementation of the 
   OAuth callback flow. This is both unnecessarily complex and dangerous. 
   ``tda-api`` handles token fetch and refreshing for you.

2. **Minimal API Wrapping**: Unlike some other API wrappers, which build in lots 
   of logic and validation, ``tda-api`` takes raw values and returns raw 
   responses, allowing you to interpret the complex API responses as you see 
   fit. Anything you can do with raw HTTP requests you can do with ``tda-api``, 
   only more easily.

What else?
----------

Bug reports, suggestions, and patches are always welcome! Submit issues
`here <https://github.com/alexgolec/tda-api/issues>`__ and pull requests
`here <https://github.com/alexgolec/tda-api/pulls>`__.

``tda-api`` is released under the
`MIT license <https://github.com/alexgolec/tda-api/blob/master/LICENSE>`__.

**Disclaimer:** *tda-api is an unofficial API wrapper. It is in no way 
endorsed by or affiliated with TD Ameritrade or any associated organization.
Make sure to read and understand the terms of service of the underlying API 
before using this package. This authors accept no responsibility for any
damage that might stem from use of this package. See the LICENSE file for
more details.*
