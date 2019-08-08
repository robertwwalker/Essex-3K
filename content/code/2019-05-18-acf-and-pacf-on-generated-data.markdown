---
title: ACF and PACF on Generated Data
author: RWW
date: '2019-05-18'
slug: acf-and-pacf-on-generated-data
categories:
  - R
  - time series
tags:
  - ARIMA
  - R
  - time series
---

# Simulating Data

Simulating data under an ARIMA model in R is made rather easy.  From the documentation on arima in `stats`, we obtain that $$ X_t = a_1 X_{t-1} + a_{2} X_{t-2} + \ldots + a_{p} X_{t-p} + b_1 \epsilon_{t-1} + b_[2] \epsilon_{t-2} + 
\ldots + b_{q} \epsilon_{t-q} $$ by construction.  This can be adjusted with the `rand.gen` argument.


```r
library(ggplot2)
library(forecast)
my.data <- arima.sim(list(ar=c(0.7,0.2)), n=100)
my.data2 <- data.frame(x=seq(1,100), y=my.data)
ggplot(my.data2, aes(x=x, y=y)) + geom_line() + labs(x="Period", title="A Simulated AR(2): (0.7,0.25)", subtitle = "Standard Normal disturbance")
```

<img src="/code/2019-05-18-acf-and-pacf-on-generated-data_files/figure-html/unnamed-chunk-1-1.png" width="672" />

What do the autocorrelation and partial look like?


```r
acf(my.data)
```

<img src="/code/2019-05-18-acf-and-pacf-on-generated-data_files/figure-html/unnamed-chunk-2-1.png" width="672" />


```r
pacf(my.data)
```

<img src="/code/2019-05-18-acf-and-pacf-on-generated-data_files/figure-html/unnamed-chunk-3-1.png" width="672" />

The pattern shows up, as it should, in the partial though the second term is sufficiently small that we will likely have little power.  First, an auto arima fit from `forecast`.


```r
auto.arima(my.data)
```

```
## Series: my.data 
## ARIMA(0,1,0) 
## 
## sigma^2 estimated as 1.05:  log likelihood=-142.87
## AIC=287.74   AICc=287.78   BIC=290.33
```



```r
summary(arima(my.data, order=c(2,0,0)))
```

```
## 
## Call:
## arima(x = my.data, order = c(2, 0, 0))
## 
## Coefficients:
##          ar1     ar2  intercept
##       0.7468  0.1162     0.0242
## s.e.  0.1033  0.1034     0.6755
## 
## sigma^2 estimated as 0.9624:  log likelihood = -140.62,  aic = 289.23
## 
## Training set error measures:
##                     ME     RMSE       MAE      MPE     MAPE      MASE
## Training set 0.0555143 0.981013 0.7891827 196.0373 339.3635 0.9867771
##                     ACF1
## Training set -0.04175208
```

