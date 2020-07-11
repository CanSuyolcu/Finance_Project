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
```

I create a list of the ticker symbols (as strings) in alphabetical order and call this list: tickers. Then I use pd.concat to concatenate the bank dataframes together to a single data frame called bank_stocks. I set the keys argument equal to the tickers list. And set the column name levels.Now I can check the head of the bank_stocks dataframe.

```python
tickers = ['BAC', 'C', 'GS', 'JPM', 'MS', 'WFC']
bank_stocks = pd.concat([BAC, C, GS, JPM, MS, WFC],axis=1,keys=tickers)
bank_stocks.columns.names = ['Bank Ticker','Stock Info']
bank_stocks.head()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220282-27827700-c36b-11ea-9de3-af03fb34f742.png" width = 1000>

Lets answer some questions, what is the max Close price for each bank's stock throughout the time period?
```python
bank_stocks.xs(key='Close',axis=1,level='Stock Info').max()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220535-3702bf80-c36d-11ea-9d6b-0f0f31e9ef72.png" width = 150>

I create a new empty DataFrame called returns. This dataframe will contain the returns for each bank's stock. returns are typically defined by:

<img src= "https://user-images.githubusercontent.com/66487971/87220561-7df0b500-c36d-11ea-9759-45d09dbe9f38.png" width = 200>

```python
returns = pd.DataFrame()
```
 I use pandas pct_change() method on the Close column to create a column representing this return value. Then I create a for loop that goes and for each Bank Stock Ticker creates this returns column and set's it as a column in the returns DataFrame.

```python
for tick in tickers:
    returns[tick+' Return'] = bank_stocks[tick]['Close'].pct_change()
returns.head()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220616-10915400-c36e-11ea-95de-4e942ecf146e.png" width = 500>

Now I create a pairplot using seaborn of the returns dataframe.
```python
import seaborn as sns
sns.pairplot(returns[1:])
```
<img src= "https://user-images.githubusercontent.com/66487971/87220643-66fe9280-c36e-11ea-8403-b879d2666fe8.png" width = 1000>

 Citigroup's crush can be easily seen from this plot.More information [here](https://en.wikipedia.org/wiki/Citigroup#November_2008.2C_Collapse_.26_US_Government_Intervention_.28part_of_the_Global_Financial_Crisis.29)

Using this returns DataFrame, I look at  dates each bank stock had the best and worst single day returns.
```python
returns.idxmin()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220757-77fbd380-c36f-11ea-810b-8185fcb53726.png" width = 200>

```python
returns.idmax()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220906-85fe2400-c370-11ea-99bc-4391e808582a.png" width = 200>

I see that 4 of the banks share the same day for the worst drop. This may be correlated to Obama's inaugural speech.

I also notice that Citigroup's largest drop and biggest gain were very close to one another, and see that [Citigroup had a stock split](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=citigroup+stock+2011+may)
When I check the standard deviation of the returns, I see that Citigroup is the riskiest over the entire time period.
```python
returns.std()
```
<img src= "https://user-images.githubusercontent.com/66487971/87220975-3a984580-c371-11ea-974f-b6117cf62962.png" width = 200>
 
But if I check just the year 2015, I see that they have very similar risk profiles, but Morgan Stanley or BofA may be the riskiest.

```python
returns.ix['2015-01-01':'2015-12-31'].std()
```
<img src= "https://user-images.githubusercontent.com/66487971/87221022-8ba83980-c371-11ea-998b-92eb612d7ece.png" width = 200>

I create a distplot using seaborn of the 2015 returns for Morgan Stanley.

```python
sns.distplot(returns.ix['2015-01-01':'2015-12-31']['MS Return'],color='green',bins=100)
```
<img src= "https://user-images.githubusercontent.com/66487971/87221203-e42c0680-c372-11ea-9733-f4b3a19e4974.png" width = 1000>

Now I create a distplot using seaborn of the 2008 returns for CitiGroup.

```python
sns.distplot(returns.ix['2008-01-01':'2008-12-31']['C Return'],color='red',bins=100)
```
<img src= "https://user-images.githubusercontent.com/66487971/87221278-72a08800-c373-11ea-97cb-cd873a925f3e.png" width = 1000>

It can be seen from this two plots that Morgan Stanley's stock prices were more stable compared to Citigroup's stock prices.

# More Visualization

## Imports

```python

import matplotlib.pyplot as plt
import seaborn as sns
sns.set_style('whitegrid')
%matplotlib inline

# Optional Plotly Method Imports
import plotly
import cufflinks as cf
cf.go_offline()
```

Now I create a line plot showing Close price for each bank for the entire index of time.

```python
for tick in tickers:
    bank_stocks[tick]['Close'].plot(figsize=(12,4),label=tick)
plt.legend()
```
<img src= "https://user-images.githubusercontent.com/66487971/87221410-80a2d880-c374-11ea-8323-e47f4eeb7eaa.png" width = 900>

Let's analyze the moving averages for these stocks in the year 2008.I  plot the rolling 30 day average against the Close Price for Bank Of America's stock for the year 2008.

```pythton
plt.figure(figsize=(12,6))
BAC['Close'].ix['2008-01-01':'2009-01-01'].rolling(window=30).mean().plot(label='30 Day Avg')
BAC['Close'].ix['2008-01-01':'2009-01-01'].plot(label='BAC CLOSE')
plt.legend()
```
<img src= "https://user-images.githubusercontent.com/66487971/87221488-12aae100-c375-11ea-9f2e-2cdfc911b3b4.png" width = 300>

Now I create a heatmap of the correlation between the stocks Close Price.
```python
sns.heatmap(bank_stocks.xs(key='Close',axis=1,level='Stock Info').corr(),annot=True)
```
<img src= "https://user-images.githubusercontent.com/66487971/87221522-4b4aba80-c375-11ea-9a44-4aad6b87297c.png" width = 300>

And I use seaborn's clustermap to cluster the correlations together.

```python
sns.clustermap(bank_stocks.xs(key='Close',axis=1,level='Stock Info').corr(),annot=True)
```
<img src= "https://user-images.githubusercontent.com/66487971/87221559-94027380-c375-11ea-9913-60edd98aabbc.png" width = 1100>

It can be easily seen that the Wells Fargo and JPMorgan Chase had strong correlation, and so does CitiGroup, Bank of America and Morgan Stanley in between.

## This concludes my project here. Thanks for reading all the way through.












