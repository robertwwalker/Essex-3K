---
aliases:
- /syllabus/
date: "2019-05-15T06:28:26-07:00"
draft: false
heading: Syllabus
title: Syllabus for 3K-2019
---


![AltText](/img/HLogo.jpg)

# Modelling Dynamics and Heterogeneity: 
## Time Series, Cross-Sectional Time Series, and Panel Data

**Harold D. Clarke, University of Texas Dallas and Robert Walker, Willamette University 
 5-16 August (two week course / 35 hrs)**

# TOPICS

## The Core and Related Problems of Heterogeneity and Dynamics

1. A Brief Review of Linear Models  
2. Summary and Visualisation of Time Series and Panels  
   + Using Stata and R to summarise and visualise single and multiple time series data  
   + The T = B + W decomposition [PanelSummary](/code/panelsummary/)  
   + Reading: [Day 1 Readings](/readings/day_1/)  

3. [Day 2] Univariate Time Series
   + Specification, estimation and diagnostics  
   + Forecasting with ARIMA models  
   + `forecast; auto.arima`; `ts::arimax`  
   + Interventions  
   + Stationarity  
   + Unit-Root tests: `fSeries::UnitrootTests` and `plm::purtest`  
   + Reading: [Day 2 Readings](/readings/day_2/)   
   + Applications: Clarke, Mishler and Whiteley, "Recapturing the Falklands"  

## Dynamic Regressions

1. [Day 3] Heterogeneity over Time  
  +  the lagged dependent variable [and dynsim]
  +  Specification, estimation and diagnostics  
  +  VAR
  +  ADL
  + interpretation
2. Reading: [Day 3 Readings](/readings/day_3/)  

## The Short and Long-Run

1.  Cointegration, ECM, and Vector Error Correction
	+ Non-stationary processes - implications and testing
	+ Dynamic equilibria and cointegrated processes
	+ Structural and Reduced Forms
	+ VECM Models
	+ `tsDyn, ecm`
2. Applications:
3. Reading: [Day 4 Readings](/readings/day_4/)

## Heterogeneity, Part I  

1.  ARCH and GARCH and Breaks  
  + Volatility and conditional heteroscedasticity in financial and public opinion data  
  + `rugarch`  
  + Structural Breaks [old school and Park's Bayes work]
2. Reading: [Day 5 Readings](/readings/day_5/)  
3. Applications: Clarke et al., 2009: ch. 4 (ARCH Models of Public Opinion)  
  + Applications: Park: Bayesian Structural Breaks  



## Varying Parameters

1.  Varying Coefficients Models in Time and Space
	- Hierarchical models: Bell, Fairborther, Jones 2018
	- Time-varying coefficients regression models

## Discrete Time Series, Latent Variables, and State Spaces

1.  Time Series Models of Latent Variables
	- State Space representations of time series processes
	- Dynamic factor analysis with covariates

Reading:
Commandeur and Koopman, chs. 1-9
Application: Jackman, "Pooling the Polls"
Application: Some Bond Ratings Paper [A Post](https://rww.science/post/pew-data-on-bond-ratings-and-rainy-day-funds/)

## Texts

Box-Steffensmeier, Janet et al. 2014. Time Series Analysis for the Social Sciences. New York: Cambridge 	University Press.


### Recommended Supplementary Texts

Asteriou, Dimitrios and Stephen G. Hall. 2011. Applied Econometrics: A Modern Approach Using 	EVIEWS and Microfit. 2nd Edition. London: Palgrave.

Colonescu, Constantin.  2016.  Principles of Econometrics with R. [Link](https://bookdown.org/ccolonescu/RPoE4/)

Commandeur, Jacques and Siem Jan Koopman. 2007. An Introduction to State Space Time Series Analysis. 	Oxford: Oxford University Press.

Enders, Walter. Applied Econometric Time Series. 4th edition. New York: John Wiley & Sons, 2014.

Hyndman, R.J., & Athanasopoulos, G.  2018. Forecasting: principles and practice, 2nd edition, OTexts: Melbourne, Australia. [Link](http://OTexts.com/fpp2).

Kleiber, Christian and Achim Zeileis. 2008. Applied Econometrics with R. New York: Springer.

Pfaff, Bernhard. 2006. Analysis of Integrated and Cointegrated Time Series With R. New York Springer.

## R Packages

[TSA: The R version:](https://rdrr.io/cran/TSA/)  
[Forecast Textbook:](https://otexts.com/fpp2/)  
[PLM: Panel Linear Models:](https://cran.r-project.org/web/packages/plm/plm.pdf)  


# REFERENCES

Baillie, R. T. and T. Bollerslev. 1994. “Cointegation, Fractional Cointegration and Exchange Rate 	Dynamics.” The Journal of Finance 49: 737-45.

Bell, A., Fairbrother, M. & Jones, K. Qual Quant (2019) 53: 1051. [Link](https://doi.org/10.1007/s11135-018-0802-x)

Box-Steffensmeier, Janet M. and Renée M. Smith. 1996. “The Dynamics of Aggregate Partisanship.” 	American Political Science Review 90: 567-80.

Charemza, W.W. and Derek F. Deadman. New Directions in Econometric Practice. 2nd edition. 	Aldershot: Edward Elgar, 1997.

Clarke, H. D., W. Mishler and P. Whiteley. 1990. "Recapturing the Falklands: Models of Conservative 	Popularity, 1979-83." British Journal of Political Science 20:63-82.

Clarke, H. D. et al. 2009. Performance Politics and the British Voter. Cambridge: Cambridge 	University Press, ch. 4.

Engle, R.  F. and C. W. J. Granger. 1987. "Co-integration and Error Correction: Representation, 	Estimation, and Testing." Econometrica 55:251-76.

Franses, Philip Hans. Time Series Models for Business and Economic Forecasting. Cambridge: 	Cambridge University Press, 1998.

Jackman, Simon. 2005. “Pooling the Polls Over an Election Campaign.” Australian Journal of Political 	Science 40: 499-517.

Keele, Luke and Suzanna DeBoef. 2008. "Taking Time Seriously: Dynamic Regression." American 	Journal of Political Science 52: 184-200.

Kwiatkowski, D., P.C.B. Phillips, P. Schmidt and Y. Shin. 1992. “Testing the Null Hypothesis of 	Stationairty Against the Alternative of a Unit Root.” Journal of Econometrics 54: 159-78.

Sims, C. 1980. "Macroeconomics and Reality." Econometrica 48:1-48.

Wawro, Gregrory. 2002. "Estimating Dynamic Panel Models in Political Science." Political Analysis 10: 	25-48.

Williams, Laron and Guy Whitten “But Wait, There’s More! Maximizing Substantive Inferences from 	TSCS 	Models” [dynsim](http://christophergandrud.github.io/dynsim/)