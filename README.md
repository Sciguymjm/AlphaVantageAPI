# AlphaVantageAPI
*An Opinionated AlphaVantage API Wrapper in Python 3.7*

Access to the AlphaVantage requires a free API key, that can be found at http://www.alphavantage.co/support/#api-key.


## Description
This API was designed to simplify the process of aquiring **free** financial data for retail traders and investors. It only requires **Pandas** since it is a common Data Manipulation Package to facility cleaning and export data into a variety of file formats such as: _csv_ (default), _json_, _pkl_, _html_, and _txt_. If the _openpyxl_ package is also installed, it can also be saved as an **Excel** file format: _xlsx_.


## Main Features
* Built with Pandas for a simpler ETL pipeline.
* Available export formats: _csv_ (default), _json_, _pkl_, _html_, _txt_, and _xlsx_ (if _openpyxl_ package is installed)
* Extended the Pandas DataFrame with extension 'av'.  See the _*Extension Example*_ below.
* Like terse commands?  The 'av' extension also includes their alias.  For example: df.av.daily_adjusted('aapl') = df.av.DA('aapl').  See help('aliases') for more shorter length commands.
* Simplifies column names i.e. "1. open" -> "open" when
```clean=True```.
* A help method to reduce looking up 'required' and 'optional' parameters for each function.
* A call_history method to return all successful API calls.


## What's New?
* **There are some breaking changes!** Please see the _Classic Example_ and the _DataFrame Extension Example_ below.
* AlphaVantage has added Fundamental Data: _Company Overview_, _Balance Sheet_, _Cash Flow_, and _Income Statement_.

## What's Gone?
* AlphaVantage has depreciated ```batch```, ```digital_intraday```, and ```sectors```.  

## Need Real Time Intraday Data?
**AlphaVantage** has partnered with [**Polygon.io**](https://polygon.io/) with partner code: ```ALPHAV```.

# Installation
```shell
$ pip install pandas
$ pip install alphaVantage-api
$ pip install openpyxl # For Excel export
```

## For the most up to date version
<!-- Source installation: -->
```shell
$ git clone https://github.com/twopirllc/AlphaVantageAPI.git
```

# Alpha Vantage Class
## Parameter Defaults
```python
api_key: str     = None
premium: bool    = False
output_size: str = 'compact'
datatype: str    = 'json'
export: bool     = False
export_path: str = '~/av_data'
output: str      = 'csv'
clean: bool      = False
proxy: dict      = {}
```

# API Parameter Descriptions

## **api_key**
If None, then do not forget to set your environment variable AV_API_KEY to API key. Otherwise set it in the class constructor.  If you have a Premium API key, do not forget to set the premium property to True as well.
## **premium**
Got premium?  Excellent! You do not need to wait _15.0001 seconds_ between each API call.
## **output_size**
The other option is 'full'. See AlphaVantage API documentation for more details.
## **datatype**
The preferred request type. See AlphaVantage API documentation for more details.
## **export**
Set it to True if you want to save the file locally according to the export_path property.
## **export_path**
The path of where you want to save the data.
## **output**
How to save/export the data locally. Other options are 'json', 'pkl', 'html', and 'txt'.  If _openpyxl_ is installed, then you can save as 'xlsz'.
## **clean**
Simplifies the column header names for instance: "1. open" -> "open".
## **proxy**
See requests API documentation for more details.


# **Example**: Classic Class Behavior

## Initialization
```python
import alphaVantageAPI as AV

# Initialize the AlphaVantage Class with default values
av = AV.AlphaVantage(
        api_key=None,
        premium=False,
        output_size="compact",
        datatype='json',
        export=False,
        export_path="~/av_data",
        output="csv",
        clean=False,
        proxy={}
    )
```

## Help!
```python
# Help: lists all the functions and indicators AlphaVantage API supports
av.help()

# Print 'function' aliases
av.help("aliases")

# Help with a specific API function
av.help("TIME_SERIES_DAILY")

# Help with an indicator
av.help("BBANDS")
```

## Classic Usage
```python
import pandas as pd
import alphaVantageAPI as AV

# Initialize, clean and save "full" locally in "csv" to "~/av_data"
av = AV.AlphaVantage(api_key="demo", export=True, clean=True, output_size="full")

# Check Settings
print(av)

# Parameters
query = "AA"
ticker, crypto = "MSFT", "BTC"
base_fx, to_fx = "USD", "CAD"

# Symbol Search
found_symbols = av.search(query)

# Global Quote
quote_df = av.quote(ticker)

# Company Overview
overview_df = av.overview(ticker)

# Balance Sheet, Income Statement and Cash Flow each return a list or a dict (if
# as_dict=True). Default: List with two DataFrames: [Quarterlydf, Annuallydf]
balance_list = av.balance(ticker)   # Balance Sheet
QuarterlyBSdf = balance_list[0]
AnnuallyBSdf = balance_list[1]

cashflow_list = av.cashflow(ticker) # Cash Flow
income_list = av.income(ticker)     # Income Statement

# FX / Currency
fx_rate_df = av.fxrate(from_symbol=base_fx, to_symbol=to_fx) # Rate
fx_I5_df = av.fx(from_symbol=base_fx, to_symbol=to_fx, function="FXI", interval=5) # Intraday as int
fx_I60_df = av.fx(from_symbol=base_fx, to_symbol=to_fx, function="FXI", interval="60min") # Intraday as str
fx_D_df = av.fx(from_symbol=base_fx, to_symbol=to_fx, function="FXD") # Daily
fx_W_df = av.fx(from_symbol=base_fx, to_symbol=to_fx, function="FXW") # Weekly
fx_M_df = av.fx(from_symbol=base_fx, to_symbol=to_fx, function="FXM") # Monthly

## Crypto Rating
btc_rating_df = av.crypto_rating(symbol=crypto) # == ds.crypto_rating(crypto)

## Digital/Crypto
btc_usd_D_df = av.digital(symbol=crypto, market=base_fx, function="CD") # Daily
btc_usd_W_df = av.digital(symbol=crypto, market=base_fx, function="CW") # Weekly
btc_usd_M_df = av.digital(symbol=crypto, market=base_fx, function="CM") # Monthly

## Generic Equity/ETF calls
ticker_I5_df = av.intraday(symbol=ticker, interval=5) # Intraday as int
ticker_I60_df = av.intraday(symbol=ticker, interval="60min") # Intraday as str
ticker_D_df = av.data(symbol=ticker, function="D") # Daily
ticker_DA_df = av.data(symbol=ticker, function="DA") # Daily Adjusted
ticker_W_df = av.data(symbol=ticker, function="W") # Weekly
ticker_WA_df = av.data(symbol=ticker, function="WA") # Weekly Adjusted
ticker_M_df = av.data(symbol=ticker, function="M") # Monthly
ticker_MA_df = av.data(symbol=ticker, function="MA") # Monthly Adjusted


## Indicators
# SMA(close, 20)
ticker_SMA_20_df = av.data(symbol=ticker, function="SMA", series_type="close", time_period=20)

# STOCH(close)
ticker_STOCH_df = av.data("STOCH", symbols[2], interval="daily", series_type="close")

# List of symbols Daily
symbols = ["AAPL", "MSFT", "XLK"]
techs = av.data(symbols, "D")  # returns dict of DataFrames
[print(techs[s]) for s in symbols]

## Call History
history = pd.DataFrame(av.call_history())
print(history)
```

## Call History
```python
# Returns all successfull calls to the API
history_list = av.call_history()

# Pretty display of Call History
history_df = pd.DataFrame(history_list)[["symbol", "function", "interval", "time_period"]]
print(history_df)
```


# **Example**: DataFrame Extension 'av' Behavior

## Initialization
**For simplicity and protection of your AV API key, the extension uses the environment variable AV_API_KEY upon import of the module.**
```python
import pandas as pd
import alphaVantageAPI
```

## Empty DataFrame: No current data, no problem!
Since 'av' is an extension of a Pandas DataFrame, we need a DataFrame to work from.  Simply create an empty DataFrame, it's contents will be replaced anyhow.
```python
df = pd.DataFrame()
```

## Setting properties
Same as the Class properties above. Use the 'av' extension to change them prior to requesting data or adjusting on the fly
```python
# API KEY
df.av.api_key = "DEMO" # Default: None
# Premium
df.av.premium = False # Default: False
# Clean
df.av.clean = True # Default: False
# Export
df.av.export = True # Default: False
# Outpsize
df.av.output_size = "full" # Default: "compact"
# Retrieval format
df.av.datatype = "json" # Default: "json"
# Path to save output format
df.av.export_path = "~/av_data" # Default: "~/av_data"
# Final output format
df.av.output = "csv" # Default: "csv"
# Proxy to use
df.av.proxy = {} # Default: {}
```

## Help!
```python
# Help: lists all the functions and indicators AlphaVantage API supports
df.av.help()
# Print 'function' aliases
df.av.help("aliases")
# Help with a specific API function
df.av.help("TIME_SERIES_DAILY")
# Help with an indicator
df.av.help("BBANDS")
```


## Data Acquisition Methods
```python
import pandas as pd
import alphaVantageAPI

# Create an Empty DataFrame to store the results
df = pd.DataFrame()

# Apply AV settings to: Clean and save "full" locally in "csv" to "~/av_data"
df.av.api_key = "DEMO"
df.av.export = True
df.av.clean = True
df.av.output_size="full"

# Parameters
query = "AA"
ticker, crypto = "MSFT", "BTC"
base_fx, to_fx = "USD", "CAD"

found_symbols = df.av.search(query)

# Global Quote
quote_df = df.av.Q(ticker) # => df.av.quote(ticker)
quote_df.av.name # returns "MSFT"

## Company Information
ticker_overview = df.av.overview(ticker)

# Balance Sheet, Cash Flow, and the Income Statement each return two DataFrames
ticker_quarterly_balance, ticker_annual_balance = df.av.balance(ticker)
ticker_quarterly_cashflow, ticker_annual_cashflow = df.av.cashflow(ticker)
ticker_quarterly_income, ticker_annual_income = df.av.income(ticker)

# FX / Currency
fx_I5_df = df.av.fx_intraday(base_fx, to_currency=to_fx, interval=5) # Intraday as int
fx_I60_df = df.av.fx_intraday(base_fx, to_currency=to_fx, interval="60min") # Intraday as str
fx_D_df = df.av.fx_daily(base_fx, to_currency=to_fx) # Daily
fx_W_df = df.av.fx_weekly(base_fx, to_currency=to_fx) # Weekly
fx_M_df = df.av.fx_monthly(base_fx, to_currency=to_fx) # Monthly

# Crypto Rating
btc_rating_df = df.av.crypto_rating(crypto)
btc_rating_df.av.name # returns "BTC"

## Digital/Crypto
btc_usd_D_df = df.av.digital_daily(crypto, market=base_fx) # Daily
btc_usd_D_df.av.name # returns "BTC.USD"
btc_usd_W_df = df.av.digital_weekly(crypto, market=base_fx) # Weekly
btc_usd_M_df = df.av.digital_monthly(crypto, market=base_fx) # Monthly

## Equities/ ETFs
ticker_I5_df = df.av.intraday(ticker, interval=5) # Intraday as int
ticker_I5_df.av.name # returns "MSFT"
ticker_I60_df = df.av.intraday(ticker, interval="60min") # Intraday as str
ticker_D_df = df.av.daily(ticker) # Daily
ticker_DA_df = df.av.daily_adjusted(ticker) # Daily Adjusted
ticker_W_df = df.av.weekly(ticker) # Weekly
ticker_WA_df = df.av.weekly_adjusted(ticker) # Weekly Adjusted
ticker_M_df = df.av.monthly(ticker) # Monthly
ticker_MA_df = df.av.monthly_adjusted(ticker) # Monthly Adjusted
```



# Contributing
Contributions are welcome and I am open to new ideas or implementations.

# Inspiration
If this module does not suit your style or workflow, consider some of the following *AlphaVantage API Python Wrapper* implementations by:

Romel Torres: https://github.com/RomelTorres/alpha_vantage

portfoliome: https://github.com/portfoliome/alphavantage