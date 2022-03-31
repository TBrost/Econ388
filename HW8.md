---
title: "Homework 9 "
author: "Tyson Brost"
date: "March 14, 2022"
output:
  html_document:  
    keep_md: true
    toc: false
    toc_float: false
    code_folding: hide
    fig_height: 6
    fig_width: 12
    fig_align: 'center'
    theme: journal
---






## {.tabset .tabset-pills .tabset-fade}

### Problem #1 - 

##### Consider the following simple regression model where scrap is the firm scrap rate and grant is a dummy variable indicating whether a firm received a job training grant.

![ ](hm9.png)

#### What else might contribute to scrap that is not in this current model

The current model literally just includes whether or not a company received a grant for training. the actual amount of training completed, number of employees, number of employees trained, types of training, salaries, work environment and uniion information could also be impactful on this model.

####  Estimate the simple regression model using the data in JTRAIN estimate the above model and interpret your findings.


```
## 
## Call:
## lm(formula = scrap ~ grant, data = JT)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -4.7291 -0.1239 -0.1239 -0.1239  4.0505 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)   
## (Intercept)  0.12390    0.04594   2.697  0.00725 **
## grant        0.08236    0.12298   0.670  0.50337   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.9268 on 471 degrees of freedom
## Multiple R-squared:  0.0009514,	Adjusted R-squared:  -0.00117 
## F-statistic: 0.4485 on 1 and 471 DF,  p-value: 0.5034
```

We calculate an insignificant variable of grants, with a P-val of 0.5, and an Adj-R^2 of -0.001, based on this model we can't conclude that grants have a significant effect on the scrap rate.

####  Calculate the Jarque-bera test in R studio. You will need to install the ‘tseries’ package to conduct this test.  Interpret your findings of this test


```
## 
## 	Jarque Bera Test
## 
## data:  residuals(lm.mult2)
## X-squared = 713.37, df = 2, p-value < 2.2e-16
```

With such a large Test-stat and low P-value we would reject the null Hypo. that the data are normally distributed and state significant evidence of the alternative, that the data (error terms) is not normally distributed.

####  What can be done to the model based upon your results from the Jarque-bera test?

Additional transformations could be performed on the data, if autocorrelation is a worry then lags or leads could be introduced, this would all be with the goal of decreasing the Test-stat to a level that we could fail to reject our null that the data is normally distributed.

### Problem #2

#### Create a summary for each variable and report what this tells you about each variable. Are there any potential outliers for any variable?  If so what should you do? 


```r
JT <- filter(JT, JT$employees != ".")
JT <- filter(JT, JT$sales != ".")
JT <- filter(JT, JT$avsalary != ".")
JT <- filter(JT, JT$totalhours != ".")

map(JT, favstats)
```

```
## $Year
##   min   Q1 median   Q3  max     mean        sd   n missing
##  1987 1987   1988 1989 1989 1988.036 0.8217384 304       0
## 
## $scrap
##        min Q1 median         Q3      max      mean        sd   n missing
##  -3.912023  0      0 0.07203385 4.174387 0.1933511 0.9854714 304       0
## 
## $grant
##  min Q1 median Q3 max      mean       sd   n missing
##    0  0      0  0   1 0.1414474 0.349057 304       0
## 
## $employees
##  min Q1 median Q3  max     mean       sd   n missing
##    4 15     36 77 1650 68.17434 135.4485 304       0
## 
## $sales
##  min      Q1  median      Q3     max    mean      sd   n missing
##   14 1501877 3389552 8347242 4.9e+07 6384481 7914385 304       0
## 
## $avsalary
##   min    Q1  median    Q3   max     mean       sd   n missing
##  4237 14000 17253.5 22000 42583 18714.31 6974.583 304       0
## 
## $totalhours
##  min Q1 median Q3 max     mean       sd   n missing
##    0  0   15.5 40 320 34.08553 53.12647 304       0
## 
## $totaltrain
##  min Q1 median    Q3 max     mean       sd   n missing
##    0  0      6 20.25 302 22.29605 41.23119 304       0
```

Based on the above summaries it may be worth looking into the sales and totalhours/totaltrain variables, the former having a significant gap within its quartiles and the later two being heavily skewed. Removal of these outliers could help us better understand a relationship within a smaller portion of the population.

#### If necessary from part a, appropriately clean up the data and then create a lag variable of one period for the sales variable. Run the following regression equation, report and evaluate the results of the regression


```r
JT <- filter(JT, JT$sales > 1501877 & JT$sales < 8347242)
JT <- filter(JT, JT$totalhours < 40)
JT <- filter(JT, JT$totaltrain < 20.25)
JT$sales_lag <- lag(JT$sales, n=1)
JT <- JT %>% drop_na()

lm.mult2 <-lm(scrap ~ grant + as.numeric(sales) + as.numeric(sales_lag), data=JT)
summary(lm.mult2)
```

```
## 
## Call:
## lm(formula = scrap ~ grant + as.numeric(sales) + as.numeric(sales_lag), 
##     data = JT)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -1.5134 -0.2363 -0.2309 -0.1183  2.1614 
## 
## Coefficients:
##                         Estimate Std. Error t value Pr(>|t|)  
## (Intercept)            2.368e-01  9.502e-02   2.492   0.0143 *
## grant                 -7.083e-01  3.543e-01  -1.999   0.0483 *
## as.numeric(sales)      3.865e-09  1.060e-08   0.365   0.7162  
## as.numeric(sales_lag) -6.093e-09  9.068e-09  -0.672   0.5031  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 0.6937 on 101 degrees of freedom
## Multiple R-squared:  0.04267,	Adjusted R-squared:  0.01423 
## F-statistic: 1.501 on 3 and 101 DF,  p-value: 0.219
```

```r
jarque.bera.test(residuals(lm.mult2))
```

```
## 
## 	Jarque Bera Test
## 
## data:  residuals(lm.mult2)
## X-squared = 81.682, df = 2, p-value < 2.2e-16
```


![ ](hm9.2.png)

Grant is now significant at a 0.05% significance level neither sales nor sales lagged are though. Our P-val has been significantly improved but still lacks significance. The JB test is still rejecting the null but the statistics has dropped from 800 to 80 so there was a ten fold movement closer to 0.

#### Are there any problems with this regression equation that you need to test for (think of the statistical diseases). If so report the results of those tests and then explain what method you would need to do to correct for those diseases.

There is also likely autocorrelation between the variables such as number of employee's and many of the other variables running the VIF test to verify and then if needed we would lag some of these variables as well. though it is difficult to do this to some extent and maintain a useful and interpretable dataset.
 

