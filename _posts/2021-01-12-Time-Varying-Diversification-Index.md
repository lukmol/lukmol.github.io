---
layout: post
title:  "Is the diversification ratio time-varying?"
date:   2021-10-24 16:20:00 -0300
categories: Diversification Finance optmization
---
## Evidence for Brazilian Market

Today we are going to check whether the diversification index proposed by Yves Choueifaty and Yves Coignard (2008) varies over time and some characteristics of this index with `python`.The Bovespa Index is the main stock index in the Brazilian market and is composed of around 90 stocks. You can check the daily composition of the index [here](https://www.b3.com.br/pt_br/market-data-e-indices/indices/indices-amplos/indice-ibovespa-ibovespa-composicao-da-carteira.htm).

Remember the repository for this analysis can be found [here]().
The first thing we do is import the packages that will be used here. Stock data are obtained through the investpy package.

```python

import pandas as pd
import numpy as np
from investpy import get_stock_historical_data, get_index_historical_data, get_stocks_list

```
the function `get_index_historical_data` is needed to get the data from the ibovespa index. And the `get_stocks_list` function is for us to know which shares of B3 can be downloaded by investpy.

```python

data_stocks =  {'start_date': '01/01/2019',
				'country': ' brazil',
				'final_date':'21/10/2021' }


tickers_investpy = get_stocks_list(data_stocks.get('country'))

all_tickers = theorical_portfolio['Código'].tolist()
```


```python


```


```python


```





### References

- [**Yves Choueifaty and Yves Coignard, Toward Maximum Diversification***](https://jpm.pm-research.com/content/35/1/40.short)
- 
