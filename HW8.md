---
title: "Homework 7 "
author: "Tyson Brost"
date: "March 09, 2022"
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

### Problem #1
##### State with reason whether the following statements are true, false or uncertain:

#### - Despite perfect multicollinearity, OLS estimators are BLUE
Technically this is false, the estimators would be the best and remain unbiased but realistically this regression would make little sense, it is essentially y=ax. Our regression basically shows us that the predicted value (or that one of the other variables) matches perfectly with one of our variables

#### - In case of high multicollinearity, it is not possible to assess the individual significance of one or more partial regression coefficients.
True: If the value of one variable significantly affects that of another variable its is difficult to tell how much the value of the second variable alone would affect the output. In reality it would be determined as a portion of both the first and the second, as well as their interaction.

#### - High pair-wise correlations do not suggest that there is high multi-collinearity
False: If high correlation exists between pairs this is a key indicator that these variables may be correlated as a whole. Although it is possible to observe multicollinearity with relatively low pair-wise correlations as well.

#### - Ceteris Paribus, the higher the VIF is, the larger the variances of OLS estimators
True: Variance of any given OLS estimator is multiplied by VIF so assuming all else remains equal a larger VIF will result in a larger variance.

### Problem #2

##### In the published paper “Futures contracts in water leasing: an experimental analysis using basin characteristics of the Rio Grande, NM” the following regression equation outputs were estimated to predict the price per acre foot of a water right:

![ ](hm-8.png)

#### Assess the predictability of this model. Is it a good model or not, explain in detail why, you should use multiple statistical inference techniques.

Initially looking to the R^2 value we see that the model is only able to account for ~20% of variation. Though with closer examination we can see that the F statistic and a large proportion of our individual terms are significant at the 1% level. 29 out of 38 variables to be exact, including the constant. So while we only get 20% of the variation we can do so with fairly substantial confidence. Some model is better than no model.

#### What does the term quantity with its estimated coefficient tell you about the price? Does this make sense?

Increasing the overall quantity of a deal decreases the cost per-acre foot by 1.95 dollars. This makes sense. Buying in bulk -> cheaper prices.

#### What do the terms 1 month 2 month and 3 month futures tell you about the price considering that the omitted variable here is the spot market? Do these three variables make sense in relation to the spot market price? 

That by making a purchase ahead of time you will pay less per acre-foot. This does make sense, risk associated with preemptive purchases must be accounted for by cheaper prices.

##### One of the main issues with regression is multicollinearity. A variance inflation factor was calculated for each of the variables and produced the following results for a few of the variables:


![ ](hm-8-VIF.png)

#### Is multicollinearity a problem in this model?  Explain why or why not?  Should any remediation be done to this model?

No, the general rule of thumb when using VIF to identify multicollinearity is that a VIF must exceed 10, none of the above values do so we would conculde that any correlation that does exist does not affect one variable to another to a significant or worrying degree.

### Problem #3

##### Explain two methods to correct for multicollinearity with the possible consequences of using these methods. 

The two primary methods for resolving multicollinearity are:

- Transformation of variables
  - lagging or leading variables may help but this is not an option when time is a variable of interest itself.
- Additional/new Data
  - By obtaining new data you have the chance to rerun the same testing but without any bias or sample error from the previous set. Obtaining additional or new data is costly and there remains the chance that this correlation is present in the population as a whole

