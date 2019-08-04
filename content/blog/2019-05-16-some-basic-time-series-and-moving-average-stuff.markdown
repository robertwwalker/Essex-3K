---
title: "Animating the Wealth Index of Selected Equities"
author: "RWW"
date: 2019-05-11
slug: tidyquant-meets-gganimate
categories:
  - R
tags: ["R", "time series", "ggplot"]
---

# Some Basic Time Series with Equities

First, let me get some data using `tidyquant`.

## tidyquant

`tidyquant` automates a lot of equity research and calculation using *tidy* concepts.  I will first use it to get the components of the S and P 500 and pick out those with weights over 1.1 percent.  In the next step, I download the data and finally calculate daily returns and a cumulative wealth index.


```r
library(tidyquant)
tq_index("SP500") %>% filter(weight > 0.011) %>% select(symbol,company) -> Tickers
Tickers <- Tickers %>% filter(symbol!="BRK.B") %>% filter(symbol!="GOOGL") %>% filter(symbol!="JNJ")
Eq.P <- Tickers %>%  tq_get(., from="2018/01/01", to="2019/05/01") 
Returns <- Eq.P %>% group_by(symbol) %>% tq_transmute(., adjusted, periodReturn, period="daily")
CumulativeReturns <- Eq.P %>% 
  group_by(symbol) %>% 
  tq_transmute(., adjusted, periodReturn, period="daily", type = "log", col_rename = "returns") %>% 
  mutate(wealth.index = 100 * cumprod(1 + returns))
library(skimr)
Returns %>% skim()
```

```
## Skim summary statistics
##  n obs: 2997 
##  n variables: 3 
##  group variables: symbol 
## 
## -- Variable type:Date ----------------------
##  symbol variable missing complete   n        min        max     median
##    AAPL     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##    AMZN     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##      FB     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##    GOOG     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##     JPM     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##    MSFT     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##      PG     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##       V     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##     XOM     date       0      333 333 2018-01-02 2019-04-30 2018-08-29
##  n_unique
##       333
##       333
##       333
##       333
##       333
##       333
##       333
##       333
##       333
## 
## -- Variable type:numeric -------------------
##  symbol      variable missing complete   n        mean    sd     p0
##    AAPL daily.returns       0      333 333     0.00068 0.018 -0.1  
##    AMZN daily.returns       0      333 333     0.0017  0.021 -0.078
##      FB daily.returns       0      333 333     0.00047 0.023 -0.19 
##    GOOG daily.returns       0      333 333     0.00048 0.017 -0.077
##     JPM daily.returns       0      333 333     0.00042 0.014 -0.048
##    MSFT daily.returns       0      333 333     0.0015  0.017 -0.054
##      PG daily.returns       0      333 333 7e-04       0.012 -0.04 
##       V daily.returns       0      333 333     0.0012  0.015 -0.048
##     XOM daily.returns       0      333 333     6.9e-05 0.013 -0.057
##      p25     p50    p75  p100     hist
##  -0.008  0.0013  0.0094 0.07  <U+2581><U+2581><U+2581><U+2582><U+2587><U+2585><U+2581><U+2581>
##  -0.0067 0.0021  0.013  0.094 <U+2581><U+2581><U+2582><U+2587><U+2585><U+2581><U+2581><U+2581>
##  -0.0092 0.00072 0.012  0.11  <U+2581><U+2581><U+2581><U+2581><U+2585><U+2587><U+2581><U+2581>
##  -0.007  0.00092 0.011  0.065 <U+2581><U+2581><U+2581><U+2583><U+2587><U+2583><U+2581><U+2581>
##  -0.0066 0       0.0078 0.047 <U+2581><U+2581><U+2582><U+2587><U+2587><U+2582><U+2581><U+2581>
##  -0.0064 0.0022  0.01   0.076 <U+2581><U+2581><U+2583><U+2587><U+2583><U+2581><U+2581><U+2581>
##  -0.0055 0.00082 0.0063 0.088 <U+2581><U+2582><U+2587><U+2582><U+2581><U+2581><U+2581><U+2581>
##  -0.0055 0.0019  0.0082 0.07  <U+2581><U+2581><U+2583><U+2587><U+2582><U+2581><U+2581><U+2581>
##  -0.0074 0.00074 0.0078 0.048 <U+2581><U+2581><U+2581><U+2585><U+2587><U+2583><U+2581><U+2581>
```

The final line provides a summary of the returns.  Now let me plot the data.


```r
library(ggplot2)
library(ggthemes)
CumulativeReturns %>% ggplot(., aes(x=date, y=wealth.index, color=symbol, group=symbol)) + geom_line() + labs(x="Date", y="Cumulative Wealth [Baseline 100]", color="Ticker") + theme_economist_white()
```

<img src="/blog/2019-05-16-some-basic-time-series-and-moving-average-stuff_files/figure-html/unnamed-chunk-1-1.png" width="672" />
 
And because I can, let me deploy gganimate to reveal the data in an animation.


```r
library(gganimate)
p <- CumulativeReturns %>% ggplot(., aes(x=date, y=wealth.index, color=symbol, group=symbol)) + geom_line() + labs(x="Date", y="Cumulative Wealth [Baseline 100]", color="Ticker") + theme_economist_white() + transition_reveal(date)
animate(p)
```

![](2019-05-16-some-basic-time-series-and-moving-average-stuff_files/figure-html/unnamed-chunk-2-1.gif)<!-- -->

