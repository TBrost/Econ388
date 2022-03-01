---
title: "HW 6"
author: "Tyson Brost"
date: "February 28, 2022"
output:
  html_document:  
    keep_md: true
    toc: true
    toc_float: true
    code_folding: hide
    fig_height: 6
    fig_width: 12
    fig_align: 'center'
---






```r
# Use this R-Chunk to import all your datasets!
IO_airfare <- read_excel("IO airfare.xls")
colnames(IO_airfare) <- c("year", "origin", "destin", "id", "dist", "passen", "fare", "bkmtshr", "ldist", "y98", "y99", "y00", "lfare","ldistsq", "concen", "lpassen")
IO_airfare$LargeShare <- case_when(IO_airfare$bkmtshr >= 0.75 ~ 1, IO_airfare$bkmtshr < 0.75 ~ 0 )
IO_airfare$y97 <- case_when(IO_airfare$year == 1997 ~ 1, IO_airfare$year != 1997 ~ 0 )
IO_airfare$distsq <- IO_airfare$dist^2
```

## Background

Using the regression equation that has been developed in the previous assignments conduct the Breusch-Pagan test, report the results of the test and interpret your findings.
If necessary from part a transform your data to correct for heteroskedasiticty, to verify that your correction worked (at least made it better, it may not remove it) re-run the Breusch-Pagan test and report the results of the test.
Run your regression with the robust standard error correction. Why do robust standard errors differ from traditional standard errors?
Explain why one should always check for heteroscedasticity in regression analysis. That is why does it matter?

## {.tabset .tabset-pills .tabset-fade}

### Problem #1 {.tabset .tabset-pills .tabset-fade}


#### Regression

$$
\underbrace{Y_i}_\text{fare} \underbrace{=}_{\sim} \overbrace{\beta_0}^{\stackrel{\text{y-int}}{\text{base fare}}} + \overbrace{\beta_1}^{\stackrel{\text{slope}}{\text{baseline}}} \underbrace{X_{1i}}_\text{distance} + \overbrace{\beta_2}^{\stackrel{\text{change in}}{\text{y-int}}}  \underbrace{X_{2i}}_\text{passen} + \overbrace{\beta_3}^{\stackrel{\text{change in}}{\text{y-int}}}  \underbrace{X_{3i}}_\text{LargeShare} + \overbrace{\beta_4}^{\stackrel{\text{change in}}{\text{slope}}} \underbrace{X_{1i}X_{2i}}_\text{dist:passen}  +  \overbrace{\beta_5}^{\stackrel{\text{change in}}{\text{slope}}} \underbrace{X_{3i}X_{2i}}_\text{LargeShare:passen} +\epsilon_i
$$


```r
lm.mult2 <-lm(fare ~ dist + passen + dist:passen + LargeShare + LargeShare:passen, data=IO_airfare)
summary(lm.mult2) %>%
pander(caption= "HW 4 Multiple regression results w/ extra estimators")
```


----------------------------------------------------------------------
        &nbsp;           Estimate    Std. Error   t value   Pr(>|t|)  
----------------------- ----------- ------------ --------- -----------
    **(Intercept)**        104.2       2.471       42.14        0     

       **dist**           0.07279     0.001843     39.51    4.24e-294 

      **passen**          -0.0137     0.001842    -7.436    1.232e-13 

    **LargeShare**         31.76       2.832       11.22    8.008e-29 

    **dist:passen**      1.166e-05   1.531e-06     7.616    3.164e-14 

 **passen:LargeShare**   -0.02899     0.003618    -8.011    1.429e-15 
----------------------------------------------------------------------


--------------------------------------------------------------
 Observations   Residual Std. Error   $R^2$    Adjusted $R^2$ 
-------------- --------------------- -------- ----------------
     4595              56.85          0.4242       0.4236     
--------------------------------------------------------------

Table: HW 4 Multiple regression results w/ extra estimators



$$
\underbrace{Y_i}_\text{fare} \underbrace{=}_{\sim} \overbrace{104.2}^{\stackrel{\text{y-int}}{\text{base fare}}} + \overbrace{0.0728}^{\stackrel{\text{slope}}{\text{baseline}}} \underbrace{X_{1i}}_\text{distance} + \overbrace{-0.014}^{\stackrel{\text{change in}}{\text{y-int}}}  \underbrace{X_{2i}}_\text{passen} + \overbrace{31.76}^{\stackrel{\text{change in}}{\text{y-int}}}  \underbrace{X_{3i}}_\text{LargeShare} + \overbrace{1.166e-05}^{\stackrel{\text{change in}}{\text{slope}}} \underbrace{X_{1i}X_{2i}}_\text{dist:passen}  +  \overbrace{-0.0289}^{\stackrel{\text{change in}}{\text{slope}}} \underbrace{X_{3i}X_{2i}}_\text{LargeShare:passen} +\epsilon_i
$$

This regression just tells us that every mile traveled increases fare by 7 cents, every passenger decreases fare by 1.3 cents, and the more concentrated the market is, the more fare is as well. Then the interaction terms give us an idea of if there are statistically significant interactions between distance, passengers, and market share. In interpreting the interaction variables, we see that market share and passengers interact with each other in a way that decreases the price of airfare, while distance and passengers interact to increase it. 

#### Breusch-Pagan Test 


```r
#load lmtest library
library(lmtest)


#perform Breusch-Pagan Test
bptest(lm.mult2)
```

```
## 
## 	studentized Breusch-Pagan test
## 
## data:  lm.mult2
## BP = 119.1, df = 5, p-value < 2.2e-16
```

I ran every possible combination of logs for the fare, distance and passen, none of these resulted in an improved BP result, the closest being a BP-stat of 126 rather than 119. As such the best model remains the initial regression. Squaring terms likewise failed to result in an improved BP statistic.


#### Robust Coefficients


```r
library("sandwich")

# Robust t test
coeftest(lm.mult2, vcov = vcovHC(lm.mult2, type = "HC1"))
```

```
## 
## t test of coefficients:
## 
##                      Estimate  Std. Error t value  Pr(>|t|)    
## (Intercept)        1.0415e+02  2.5914e+00 40.1913 < 2.2e-16 ***
## dist               7.2794e-02  2.0691e-03 35.1818 < 2.2e-16 ***
## passen            -1.3697e-02  1.9856e-03 -6.8981 5.986e-12 ***
## LargeShare         3.1761e+01  3.0288e+00 10.4864 < 2.2e-16 ***
## dist:passen        1.1659e-05  1.7646e-06  6.6070 4.369e-11 ***
## passen:LargeShare -2.8987e-02  3.8270e-03 -7.5743 4.340e-14 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

Above we see the results of an HC1 method for residual weighting. The variation present all of this tests cases resulted in a larger SE for each beta. This is due to us placing an increased weight on the residuals when calculating our least squares estimators.

#### Issues with Heteroskewdasticity

Models which suffer from Heteroskewdasticity cannot be relied upon accurately, the SE of the estimators will be incorrect as they influence one anothers and share values. With inccorect SE's the resulting T-scores and hypotheses will be inaccurate and unreliable. Generally when heteroskewdasticity is present it is a sign that our model either fails to account for a significant variable or incorrectly applies one of the already included values.

### Problem 2 

![ ](HW6.png)
Here, trmgpa is term GPA, crsgpa is a weighted average of overall GPA in courses taken, cumgpa is GPA prior to the current semester, tothrs is total credit hours prior to the semester, sat is SAT score, hsperc is graduating percentile in high school class, female is a gender dummy, and season is a dummy variable equal to unity of the student’s sport is in season during the fall.  The usual standard errors are in () while the robust standard errors are in []. 


```r
GPA3 = filter(gpa3, term == 1 & frstsem ==0)
lm.mult3 <-lm(trmgpa ~ crsgpa + cumgpa + tothrs + sat + hsperc + female + season, data=GPA3)
summary(lm.mult3) %>%
pander()
```


----------------------------------------------------------------
     &nbsp;        Estimate    Std. Error   t value   Pr(>|t|)  
----------------- ----------- ------------ --------- -----------
 **(Intercept)**     -2.12       0.5519     -3.841    0.0001538 

   **crsgpa**       0.8998       0.1748      5.147    5.204e-07 

   **cumgpa**       0.1928      0.06404      3.011     0.00286  

   **tothrs**      0.001378     0.001246     1.106     0.2699   

     **sat**       0.001793     0.000212     8.457    1.975e-15 

   **hsperc**      -0.003944    0.001814    -2.174     0.0306   

   **female**       0.3515      0.08541      4.115    5.189e-05 

   **season**       -0.157      0.09768     -1.608     0.1091   
----------------------------------------------------------------


--------------------------------------------------------------
 Observations   Residual Std. Error   $R^2$    Adjusted $R^2$ 
-------------- --------------------- -------- ----------------
     269              0.5408          0.4648       0.4505     
--------------------------------------------------------------

Table: Fitting linear model: trmgpa ~ crsgpa + cumgpa + tothrs + sat + hsperc + female + season

```r
coeftest(lm.mult3, vcov = vcovHC(lm.mult3, type = "HC1"))
```

```
## 
## t test of coefficients:
## 
##                Estimate  Std. Error t value  Pr(>|t|)    
## (Intercept) -2.12008930  0.55468521 -3.8221 0.0001655 ***
## crsgpa       0.89978033  0.16804178  5.3545 1.882e-07 ***
## cumgpa       0.19280964  0.07540711  2.5569 0.0111268 *  
## tothrs       0.00137801  0.00122176  1.1279 0.2604017    
## sat          0.00179270  0.00019689  9.1049 < 2.2e-16 ***
## hsperc      -0.00394381  0.00193024 -2.0432 0.0420400 *  
## female       0.35149557  0.07986881  4.4009 1.573e-05 ***
## season      -0.15704384  0.08071297 -1.9457 0.0527633 .  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
bptest(lm.mult3)
```

```
## 
## 	studentized Breusch-Pagan test
## 
## data:  lm.mult3
## BP = 13.696, df = 7, p-value = 0.05687
```

#### Part 1

Do the variables crsgpa, cumgpa, and tothrs have the expected estimated effects? Which of these variables are statistically significant at the 5% level?  Does it matter which standard errors are used?

I feel like crsgpa and cumgpa are reasonable, higher prior gpa's and higher average gpa for the given courses should result in a higher gpa expectation. whereas total hours I would say is more likely to have a power relationship, increasing and decreasing at certain points. in this equation it has a small positive effect but is not significant at the 5% like the previous two variables.

Usage of the robust vs normal standard errors is questionable, a BP test results in a P-value of 0.057, so we are probably ok with using just the normal estimates though none of our conclusions change. 
#### Part 2

Why does the hypothesis Ho: B1(crsgpa)=1 make sense?  Test this hypothesis against the two sided alternative at the 5% level using both standard errors.  Describe your conclusions from this test.

Somewhat, the average GPA of the course is an excellent predictor alone though we would not want this to be our null, the rather our null would be that this value is equal to 0. if it was equal to 1 that would mean the students GPA is perfectly reflected on a 1-1 level by the average of the course.In reality there are many other factors involved. This is made clear by the results of the BP test, with a significant P-val of 0.0004, indicating Heteroskewdasticity.


In either case with each SE we observe significant P-values. 

Thus while significant, this model almost without a doubt requires adjustment for heteroskewdasticity.

```r
lm.mult4 <-lm(trmgpa ~ crsgpa , data=GPA3)
summary(lm.mult4) %>%
pander()
```


--------------------------------------------------------------
     &nbsp;        Estimate   Std. Error   t value   Pr(>|t|) 
----------------- ---------- ------------ --------- ----------
 **(Intercept)**   -0.1507      0.6159     -0.2447    0.8069  

   **crsgpa**       0.895       0.2192      4.083    5.88e-05 
--------------------------------------------------------------


---------------------------------------------------------------
 Observations   Residual Std. Error    $R^2$    Adjusted $R^2$ 
-------------- --------------------- --------- ----------------
     269              0.7091          0.05877      0.05524     
---------------------------------------------------------------

Table: Fitting linear model: trmgpa ~ crsgpa

```r
#confint(lm.mult4, level = 0.95)
2*pt(q=4.083+0.2192, df=268, lower.tail=FALSE)
```

```
## [1] 2.371936e-05
```


```r
bptest(lm.mult4)
```

```
## 
## 	studentized Breusch-Pagan test
## 
## data:  lm.mult4
## BP = 12.433, df = 1, p-value = 0.0004219
```

```r
coeftest(lm.mult4, vcov = vcovHC(lm.mult4, type = "HC1"))
```

```
## 
## t test of coefficients:
## 
##             Estimate Std. Error t value  Pr(>|t|)    
## (Intercept) -0.15072    0.60446 -0.2493    0.8033    
## crsgpa       0.89497    0.21050  4.2516 2.938e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
2*pt(q=4.2516+0.2105, df=268, lower.tail=FALSE)
```

```
## [1] 1.195865e-05
```


#### Part 3

Test whether there is an in-season effect on term GAP, using both standard errors. Does the significant level at which the null can be rejected depend on the standard error used?


```r
lm.mult6 <-lm(trmgpa ~ season , data=GPA3)
summary(lm.mult6) %>%
pander()
```


---------------------------------------------------------------
     &nbsp;        Estimate   Std. Error   t value   Pr(>|t|)  
----------------- ---------- ------------ --------- -----------
 **(Intercept)**    2.522       0.1197      21.07    1.022e-58 

   **season**      -0.1899      0.1289     -1.474     0.1418   
---------------------------------------------------------------


----------------------------------------------------------------
 Observations   Residual Std. Error    $R^2$     Adjusted $R^2$ 
-------------- --------------------- ---------- ----------------
     269               0.728          0.008067      0.004352    
----------------------------------------------------------------

Table: Fitting linear model: trmgpa ~ season

```r
#confint(lm.mult4, level = 0.95)
2*pt(q=1.474+0.2192, df=268, lower.tail=FALSE)
```

```
## [1] 0.09157938
```



```r
bptest(lm.mult6)
```

```
## 
## 	studentized Breusch-Pagan test
## 
## data:  lm.mult6
## BP = 6.0709, df = 1, p-value = 0.01374
```

```r
coeftest(lm.mult6, vcov = vcovHC(lm.mult6, type = "HC1"))
```

```
## 
## t test of coefficients:
## 
##              Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  2.521622   0.084296 29.9139  < 2e-16 ***
## season      -0.189897   0.097845 -1.9408  0.05334 .  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

```r
2*pt(q=1.9408+0.0978, df=268, lower.tail=FALSE)
```

```
## [1] 0.04247138
```

As shown above if using normal SE values the significance level can only be rejected at the 0.1 level where as when using robust SE we can reject our null at a level of 0.05.

##
