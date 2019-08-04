---
slug: panel-Summary
title: Summarising Panel Data
author: RWW
date: '2019-05-12'
categories:
  - R
  - panel data
tags:
  - R
  - panel data
---

A summary function for panel data.


```r
xtsum <- function(formula, data) {
  pform <- terms(formula, data=data)
  unit <- pform[[2]]
  vars <- attr(pform, "term.labels")
  cls <- sapply(data, class)
  data <- data %>% select(which(cls%in%c("numeric","integer")))
  varnames <- intersect(names(data),vars)
  sumfunc <- function(data=data, varname, unit) {
  loc.unit <- enquo(unit)
  varname <- ensym(varname)
    ores <- data %>% filter(!is.na(!! varname)==TRUE) %>% summarise(
    O.mean=round(mean(`$`(data, !! varname), na.rm=TRUE), digits=3),
    O.sd=round(sd(`$`(data, !! varname), na.rm=TRUE), digits=3), 
    O.min = min(`$`(data, !! varname), na.rm=TRUE), 
    O.max=max(`$`(data, !! varname), na.rm=TRUE), 
    O.SumSQ=round(sum(scale(`$`(data, !! varname), center=TRUE, scale=FALSE)^2, na.rm=TRUE), digits=3), 
    O.N=sum(as.numeric((!is.na(`$`(data, !! varname))))))
 bmeans <- data %>% filter(!is.na(!! varname)==TRUE) %>% group_by(!! loc.unit) %>% summarise(
   meanx=mean(`$`(.data, !! varname), na.rm=T), 
   t.count=sum(as.numeric(!is.na(`$`(.data, !! varname)))))
    bres <- bmeans %>% ungroup() %>% summarise(
    B.mean = round(mean(meanx, na.rm=TRUE), digits=3),
    B.sd = round(sd(meanx, na.rm=TRUE), digits=3),
    B.min = min(meanx, na.rm=TRUE), 
    B.max=max(meanx, na.rm=TRUE), 
    B.Units=sum(as.numeric(!is.na(t.count))), 
    B.t.bar=round(mean(t.count, na.rm=TRUE), digits=3))
  wdat <- data %>% filter(!is.na(!! varname)==TRUE) %>% group_by(!! loc.unit) %>% mutate(
    W.x = scale(`$`(.data,!! varname), scale=FALSE))
  wres <- wdat %>% ungroup() %>% summarise(
    W.sd=round(sd(W.x, na.rm=TRUE), digits=3), 
    W.min=min(W.x, na.rm=TRUE), 
    W.max=max(W.x, na.rm=TRUE), 
    W.SumSQ=round(sum(W.x^2, na.rm=TRUE), digits=3))
    W.Ratio <- round(wres$W.SumSQ/ores$O.SumSQ, digits=3)
  return(c(ores,bres,wres,Within.Ovr.Ratio=W.Ratio))
  }
res1 <- sapply(varnames, function(x) {sumfunc(data, !!x, !!unit)})
return(data.frame(t(res1)))
}  
```

The example to illustrate the function is global mortality from a previous tidyTuesday.  


```r
library(skimr)
library(tidyverse)
library(rlang)
library(kableExtra)
# global_mortality <- readRDS("../../data/global_mortality.rds")
global_mortality <- readRDS(url("https://github.com/robertwwalker/academic-mymod/raw/master/data/global_mortality.rds"))
global_mortality$countryF <- as.factor(global_mortality$country)
global_mortality$countryN <- as.numeric(as.factor(global_mortality$country))
names(global_mortality) <- gsub(" \\(%\\)","",names(global_mortality))
# For some reason, the xtsum function does not respond to the weird variable names but will accept them devoid of (%)
myxt.res <- xtsum(countryN~., data=global_mortality)
```

## The Overall


```r
myxt.res %>% select(starts_with("O.")) %>% kableExtra::kable()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> O.mean </th>
   <th style="text-align:left;"> O.sd </th>
   <th style="text-align:left;"> O.min </th>
   <th style="text-align:left;"> O.max </th>
   <th style="text-align:left;"> O.SumSQ </th>
   <th style="text-align:left;"> O.N </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> year </td>
   <td style="text-align:left;"> 2003 </td>
   <td style="text-align:left;"> 7.79 </td>
   <td style="text-align:left;"> 1990 </td>
   <td style="text-align:left;"> 2016 </td>
   <td style="text-align:left;"> 373464 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancers </td>
   <td style="text-align:left;"> 14.387 </td>
   <td style="text-align:left;"> 8.154 </td>
   <td style="text-align:left;"> 0.582272587715 </td>
   <td style="text-align:left;"> 33.6174987032 </td>
   <td style="text-align:left;"> 409267.071 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Diabetes </td>
   <td style="text-align:left;"> 6.286 </td>
   <td style="text-align:left;"> 4.436 </td>
   <td style="text-align:left;"> 0.327132851997 </td>
   <td style="text-align:left;"> 35.8161868396999 </td>
   <td style="text-align:left;"> 121099.189 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Dementia </td>
   <td style="text-align:left;"> 3.221 </td>
   <td style="text-align:left;"> 2.746 </td>
   <td style="text-align:left;"> 0.0447517795879 </td>
   <td style="text-align:left;"> 16.6724813569 </td>
   <td style="text-align:left;"> 46417.872 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Tuberculosis </td>
   <td style="text-align:left;"> 2.133 </td>
   <td style="text-align:left;"> 2.648 </td>
   <td style="text-align:left;"> 0.0108809115559 </td>
   <td style="text-align:left;"> 16.4658613491 </td>
   <td style="text-align:left;"> 43159.866 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Suicide </td>
   <td style="text-align:left;"> 1.391 </td>
   <td style="text-align:left;"> 1.111 </td>
   <td style="text-align:left;"> 0.101610325702999 </td>
   <td style="text-align:left;"> 15.4120175726 </td>
   <td style="text-align:left;"> 7591.252 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Malaria </td>
   <td style="text-align:left;"> 1.801 </td>
   <td style="text-align:left;"> 4.213 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 24.4259622088 </td>
   <td style="text-align:left;"> 109251.359 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Homicide </td>
   <td style="text-align:left;"> 0.983 </td>
   <td style="text-align:left;"> 1.454 </td>
   <td style="text-align:left;"> 0.0452020912394 </td>
   <td style="text-align:left;"> 14.2292611619 </td>
   <td style="text-align:left;"> 13007.955 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Meningitis </td>
   <td style="text-align:left;"> 0.783 </td>
   <td style="text-align:left;"> 0.967 </td>
   <td style="text-align:left;"> 0.0279860443492 </td>
   <td style="text-align:left;"> 6.98134580740999 </td>
   <td style="text-align:left;"> 5760.664 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Drowning </td>
   <td style="text-align:left;"> 0.714 </td>
   <td style="text-align:left;"> 0.514 </td>
   <td style="text-align:left;"> 0.053306377474 </td>
   <td style="text-align:left;"> 4.5109478491 </td>
   <td style="text-align:left;"> 1628.778 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Hepatitis </td>
   <td style="text-align:left;"> 0.161 </td>
   <td style="text-align:left;"> 0.165 </td>
   <td style="text-align:left;"> 0.00484788578833 </td>
   <td style="text-align:left;"> 1.58328931261999 </td>
   <td style="text-align:left;"> 167.675 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Fire </td>
   <td style="text-align:left;"> 0.334 </td>
   <td style="text-align:left;"> 0.181 </td>
   <td style="text-align:left;"> 0.0569132375453 </td>
   <td style="text-align:left;"> 1.34368648669 </td>
   <td style="text-align:left;"> 201.769 </td>
   <td style="text-align:left;"> 6156 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Conflict </td>
   <td style="text-align:left;"> 0.291 </td>
   <td style="text-align:left;"> 2.399 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 82.317 </td>
   <td style="text-align:left;"> 27372.607 </td>
   <td style="text-align:left;"> 4758 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Terrorism </td>
   <td style="text-align:left;"> 0.037 </td>
   <td style="text-align:left;"> 0.229 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 5.877 </td>
   <td style="text-align:left;"> 250.161 </td>
   <td style="text-align:left;"> 4758 </td>
  </tr>
</tbody>
</table>

## The Between


```r
myxt.res %>% select(starts_with("B.")) %>% kableExtra::kable()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> B.mean </th>
   <th style="text-align:left;"> B.sd </th>
   <th style="text-align:left;"> B.min </th>
   <th style="text-align:left;"> B.max </th>
   <th style="text-align:left;"> B.Units </th>
   <th style="text-align:left;"> B.t.bar </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> year </td>
   <td style="text-align:left;"> 2003 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 2003 </td>
   <td style="text-align:left;"> 2003 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancers </td>
   <td style="text-align:left;"> 14.387 </td>
   <td style="text-align:left;"> 8.034 </td>
   <td style="text-align:left;"> 3.10952579112629 </td>
   <td style="text-align:left;"> 30.1971632247185 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Diabetes </td>
   <td style="text-align:left;"> 6.286 </td>
   <td style="text-align:left;"> 4.24 </td>
   <td style="text-align:left;"> 1.18378662106407 </td>
   <td style="text-align:left;"> 27.7054687485259 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Dementia </td>
   <td style="text-align:left;"> 3.221 </td>
   <td style="text-align:left;"> 2.618 </td>
   <td style="text-align:left;"> 0.273432691278518 </td>
   <td style="text-align:left;"> 11.8213260531704 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Tuberculosis </td>
   <td style="text-align:left;"> 2.133 </td>
   <td style="text-align:left;"> 2.554 </td>
   <td style="text-align:left;"> 0.0320558939470148 </td>
   <td style="text-align:left;"> 14.8469776932555 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Suicide </td>
   <td style="text-align:left;"> 1.391 </td>
   <td style="text-align:left;"> 1.086 </td>
   <td style="text-align:left;"> 0.24411056203237 </td>
   <td style="text-align:left;"> 12.1645728187674 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Malaria </td>
   <td style="text-align:left;"> 1.801 </td>
   <td style="text-align:left;"> 4.11 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 20.1040837006259 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Homicide </td>
   <td style="text-align:left;"> 0.983 </td>
   <td style="text-align:left;"> 1.425 </td>
   <td style="text-align:left;"> 0.055930461119 </td>
   <td style="text-align:left;"> 11.0613278720589 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Meningitis </td>
   <td style="text-align:left;"> 0.783 </td>
   <td style="text-align:left;"> 0.951 </td>
   <td style="text-align:left;"> 0.0422193171260666 </td>
   <td style="text-align:left;"> 5.31515565909333 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Drowning </td>
   <td style="text-align:left;"> 0.714 </td>
   <td style="text-align:left;"> 0.487 </td>
   <td style="text-align:left;"> 0.0614298661120518 </td>
   <td style="text-align:left;"> 3.51533070170296 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Hepatitis </td>
   <td style="text-align:left;"> 0.161 </td>
   <td style="text-align:left;"> 0.158 </td>
   <td style="text-align:left;"> 0.00815661061311407 </td>
   <td style="text-align:left;"> 0.993252051033777 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Fire </td>
   <td style="text-align:left;"> 0.334 </td>
   <td style="text-align:left;"> 0.168 </td>
   <td style="text-align:left;"> 0.0700329635986741 </td>
   <td style="text-align:left;"> 0.995006600195815 </td>
   <td style="text-align:left;"> 228 </td>
   <td style="text-align:left;"> 27 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Conflict </td>
   <td style="text-align:left;"> 0.291 </td>
   <td style="text-align:left;"> 0.921 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 9.32088461538462 </td>
   <td style="text-align:left;"> 183 </td>
   <td style="text-align:left;"> 26 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Terrorism </td>
   <td style="text-align:left;"> 0.037 </td>
   <td style="text-align:left;"> 0.121 </td>
   <td style="text-align:left;"> 0 </td>
   <td style="text-align:left;"> 1.29896153846154 </td>
   <td style="text-align:left;"> 183 </td>
   <td style="text-align:left;"> 26 </td>
  </tr>
</tbody>
</table>

# The Within


```r
myxt.res %>% select(starts_with("W.")) %>% kableExtra::kable()
```

<table>
 <thead>
  <tr>
   <th style="text-align:left;">   </th>
   <th style="text-align:left;"> W.sd </th>
   <th style="text-align:left;"> W.min </th>
   <th style="text-align:left;"> W.max </th>
   <th style="text-align:left;"> W.SumSQ </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> year </td>
   <td style="text-align:left;"> 7.79 </td>
   <td style="text-align:left;"> -13 </td>
   <td style="text-align:left;"> 13 </td>
   <td style="text-align:left;"> 373464 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Cancers </td>
   <td style="text-align:left;"> 1.489 </td>
   <td style="text-align:left;"> -8.01775678316665 </td>
   <td style="text-align:left;"> 5.44955180565917 </td>
   <td style="text-align:left;"> 13651.253 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Diabetes </td>
   <td style="text-align:left;"> 1.331 </td>
   <td style="text-align:left;"> -9.75005714192591 </td>
   <td style="text-align:left;"> 10.9286704664144 </td>
   <td style="text-align:left;"> 10897.803 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Dementia </td>
   <td style="text-align:left;"> 0.847 </td>
   <td style="text-align:left;"> -5.18319310059962 </td>
   <td style="text-align:left;"> 5.14501071395038 </td>
   <td style="text-align:left;"> 4420.371 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Tuberculosis </td>
   <td style="text-align:left;"> 0.72 </td>
   <td style="text-align:left;"> -6.16989797402704 </td>
   <td style="text-align:left;"> 7.15698179631445 </td>
   <td style="text-align:left;"> 3193.133 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Suicide </td>
   <td style="text-align:left;"> 0.243 </td>
   <td style="text-align:left;"> -2.2104640588474 </td>
   <td style="text-align:left;"> 3.2474447538326 </td>
   <td style="text-align:left;"> 364.808 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Malaria </td>
   <td style="text-align:left;"> 0.963 </td>
   <td style="text-align:left;"> -7.91665118507926 </td>
   <td style="text-align:left;"> 6.90359583646668 </td>
   <td style="text-align:left;"> 5711.216 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Homicide </td>
   <td style="text-align:left;"> 0.303 </td>
   <td style="text-align:left;"> -3.48785726948888 </td>
   <td style="text-align:left;"> 3.16793328984112 </td>
   <td style="text-align:left;"> 564.186 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Meningitis </td>
   <td style="text-align:left;"> 0.187 </td>
   <td style="text-align:left;"> -1.98472274401578 </td>
   <td style="text-align:left;"> 2.16671435778937 </td>
   <td style="text-align:left;"> 216.175 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Drowning </td>
   <td style="text-align:left;"> 0.168 </td>
   <td style="text-align:left;"> -1.77580790376296 </td>
   <td style="text-align:left;"> 1.2747173861074 </td>
   <td style="text-align:left;"> 174.405 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Hepatitis </td>
   <td style="text-align:left;"> 0.048 </td>
   <td style="text-align:left;"> -0.394690387248851 </td>
   <td style="text-align:left;"> 0.876379900818139 </td>
   <td style="text-align:left;"> 14.147 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Fire </td>
   <td style="text-align:left;"> 0.069 </td>
   <td style="text-align:left;"> -0.432961877175422 </td>
   <td style="text-align:left;"> 0.872176100059926 </td>
   <td style="text-align:left;"> 29.045 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Conflict </td>
   <td style="text-align:left;"> 2.216 </td>
   <td style="text-align:left;"> -9.32088461538462 </td>
   <td style="text-align:left;"> 78.3840384615385 </td>
   <td style="text-align:left;"> 23357.854 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Terrorism </td>
   <td style="text-align:left;"> 0.195 </td>
   <td style="text-align:left;"> -1.29896153846154 </td>
   <td style="text-align:left;"> 4.57803846153846 </td>
   <td style="text-align:left;"> 180.881 </td>
  </tr>
</tbody>
</table>

