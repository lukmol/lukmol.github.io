---
layout: post
title: Realized Volatility in Bitcoin
date:   2021-10-25 18:40:00 -0300
categories: R HFT RV Crypto  
---


One of the most relevant characteristics of digital assets is the high volatility observed in their prices. In this context, it is necessary that we have an adequate estimate of this parameter. In addition, there is great value in models that seek to predict future asset volatility values, which can be seen in the extensive literature on this topic. Here we will manipulate a Bitcoin high frequency database, extracted from Binance to access the repository click [here](https://github.com/lukmol/BTC-HFT). Let's calculate realized volatility and forecast it.

```r
pacman::p_load(tidyverse,magrittr,dplyr,lubridate,zoo,xts,stringr,highfrequency)
sep = ','
df_trades <- read.csv2('data/dataset_trades.csv', sep = sep)

```
We will build from tick-by-tick to second-by-second data as follows
```r
scale_microseconds = 1000000
df_trades%<>% as_tibble() %>% mutate(timestamp =as_datetime(as.POSIXct(df_trades$timestamp / scale_microseconds 								origin = "1970-01-01",tz = 'UTC')))

```

```r
df_trades %<>% mutate(price = as.numeric(price),
                           amount = as.numeric(amount)) # Change some datatypes
	
df_trades %>% rename(SYMBOL = symbol,DT = timestamp,PRICE = price) %>%
  select(DT,PRICE, SYMBOL)%>% mutate(PRICE = as.numeric(PRICE))%>%
  data.table::data.table() -> df_BTC

```
![btc](/assets/images/btc.png)

The estimation of realized volatility is performed using the two times scale estimator (TTSE) by Zhang (2005). This estimator assumes a diffusion process for prices. Another point of TTSE is that microstructure noise is considered I.I.D..

```r

TTSE_RV<-rAVGCov(rData = df_BTC[, list(DT, PRICE)], alignBy = "minutes",alignPeriod = 15,
                 makeReturns = TRUE)
returns_btc <- df_BTC[, makeReturns(PRICE), by = list(DATE = as.Date(DT))]

btc <- as.xts(returns_btc$V1, order.by = as.POSIXct(as.character(returns_btc$DATE)))
```

In addition to realized volatility, it is of interest to estimate future realized volatility, we do this through the heterogeneous autoregressive (HAR) model of [Corsi (2009)](https://scholar.google.com/citations?user=0S6oWnAAAAAJ&hl=en).
```r
HAR_RV <- HARmodel(btc, periods = c(1,3),
                   RVest = c("rAVGCov"), type="HAR",
                   inputType = 'returns')
```

The HAR model is nothing more than an OLS that takes into account different periods according to how the information can be interpreted by each trader.


![output](/assets/images/HAR_output.png)


Below we can see the predicted and observed values:

![observed](/assets/images/RV_BTC.png)

It is always worth mentioning here, although the realized volatility at t is dependent on past period volatility, the relationship between realized volatility and returns is not equally significant.

# References
- [A Tale of Two Time Scales: Determining Integrated
Volatility With Noisy High-Frequency Data](https://www.nber.org/papers/w10111)
- [A Simple Approximate Long-Memory Model of Realized Volatility](https://academic.oup.com/jfec/article-abstract/7/2/174/856522)
-[Github Repository](https://github.com/lukmol/BTC-HFT)