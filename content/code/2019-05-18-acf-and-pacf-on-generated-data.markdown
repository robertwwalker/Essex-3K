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
## ARIMA(1,1,0) 
## 
## Coefficients:
##           ar1
##       -0.3176
## s.e.   0.0947
## 
## sigma^2 estimated as 0.9515:  log likelihood=-137.56
## AIC=279.13   AICc=279.25   BIC=284.32
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
##       0.6384  0.2807    -1.3623
## s.e.  0.0950  0.0949     1.0340
## 
## sigma^2 estimated as 0.9081:  log likelihood = -137.93,  aic = 283.86
## 
## Training set error measures:
##                        ME      RMSE       MAE      MPE     MAPE      MASE
## Training set 0.0001928708 0.9529562 0.7534692 76.20425 139.1123 0.9337599
##                    ACF1
## Training set 0.00904509
```

