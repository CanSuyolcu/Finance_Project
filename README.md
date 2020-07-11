# Finance_Project
In this data project I focuse on exploratory data analysis of stock prices and see how they progressed throughout the [financial crisis](https://en.wikipedia.org/wiki/Financial_crisis_of_2007%E2%80%932008) all the way to early 2016.

## The Imports

```python
from pandas_datareader import data, wb
import pandas as pd
import numpy as np
import datetime
%matplotlib inline
```
## Data

I get stock information using pandas datareader for the following banks:
- Bank of America
- CitiGroup
- Goldman Sachs
- JPMorgan Chase
- Morgan Stanley
- Wells Fargo

I use [this documentation page](https://pandas-datareader.readthedocs.io/en/latest/remote_data.html) to learn how to get the stock data from Jan 1st 2006 to Jan 1st 2016 for each of these banks.
I choose to use Google's stock prices.
```python
start = datetime.datetime(2006, 1, 1)
end = datetime.datetime(2016, 1, 1)
# Bank of America
BAC = data.DataReader("BAC", 'google', start, end)

# CitiGroup
C = data.DataReader("C", 'google', start, end)

# Goldman Sachs
GS = data.DataReader("GS", 'google', start, end)

# JPMorgan Chase
JPM = data.DataReader("JPM", 'google', start, end)

# Morgan Stanley
MS = data.DataReader("MS", 'google', start, end)

# Wells Fargo
WFC = data.DataReader("WFC", 'google', start, end)

I create a list of the ticker symbols (as strings) in alphabetical order and call this list: tickers. Then I use pd.concat to concatenate the bank dataframes together to a single data frame called bank_stocks. I set the keys argument equal to the tickers list. And set the column name levels.Now I can check the head of the bank_stocks dataframe.

```python
tickers = ['BAC', 'C', 'GS', 'JPM', 'MS', 'WFC']
bank_stocks = pd.concat([BAC, C, GS, JPM, MS, WFC],axis=1,keys=tickers)
bank_stocks.columns.names = ['Bank Ticker','Stock Info']
bank_stocks.head()
```
