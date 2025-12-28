# robcluster2

A Stata program to combine robust regression estimation (M or MM) with multiway clustered standard errors. The program accompanies the Gassen & Veenman (2025) study on "Estimation Precision and Robust Inference in Archival Research" (https://ssrn.com/abstract=4975569).

**Comments/feedback welcome**

## Overview

`robcluster2` is a program that produces multiway cluster-robust standard errors for a robust regression estimator. It computes standard errors for robust estimators based on equation 3.11 of [Croux, Dhaene, and Hoorelbeke 2003](https://ideas.repec.org/p/ete/ceswps/ces0316.html). Multiway clustered standard errors are obtained based on (1) the intuition of [Zeileis, Koll, and Graham (2020)](https://www.jstatsoft.org/article/view/v095i01) for sandwich estimators to obtain cluster-robust standard errors and (2) the formulas for multiway cluster-robust standard errors from [Thompson (2011)](https://doi.org/10.1016/j.jfineco.2010.08.016), [Cameron, Gelbach, and Miller (2011)](https://doi.org/10.1198/jbes.2010.07136), and [Gu and Yoo (2019)](https://journals.sagepub.com/doi/full/10.1177/1536867X19893637). Significance levels of reported *t*-values are based on the degrees of freedom defined by the cluster dimension with the lowest number of unique clusters (G-1). 

## Installation and dependencies

The program can be installed by simply saving the \*.ado files into your local Stata directory that contains additional ado programs. To identify this folder, type and execute "sysdir" in your Stata command window and go to the folder listed at "PLUS". Make sure you place the program in the folder with the correct starting letter of the program (i.e., the folder names "r" for all programs) and the file extension is correctly specified as \*.ado.

The program requires the latest versions of `moremata` and `robreg` to be installed in Stata:
```
ssc inst moremata, replace
ssc inst robreg, replace
```

## Program details: 

Syntax:
**robcluster2** *subcmd* *depvar* *indepvars*, *cluster(varlist)* [*options*]

This program can be used to estimate a robust regression with cluster-robust standard errors in the two or three dimensions specified in *cluster(varlist)*.

- The subcommand options available in [*subcmd*] follow **robreg** and include *mm*, *s*, *m*, and *q*. When not followed by any of these subcommands, the program can be used as a post-estimation tool after use of **robreg mm**, **robreg s**, or **robreg m**.

- The options available in [*options*] are:  
  - *eff(value)*: specifies the desired level of estimation efficiency of the estimator in case the regression errors follow a normal distribution. This option is required for the default MM-estimator and the M-estimator when using the *m* option.
  - *biweight* (optional): when combined with option *m*, this option replaces the default Huber objective function in the M-estimator with the biweight objective function.
  - *tol(value)* (optional): specifies the tolerance for iterative algorithms (default is 1e-6).
  - *m()* (optional): specifies the (indicator/categorical) variables to be partialled out with the S-estimator (applicable to S-estimation and MM-estimation).
  - *weightvar(name)* (optional): stores the robust regression weights in new variable ''name''. 

**Output**: 
The program produces standard regression output, with the number of observations used in the estimation, the degrees of freedom used to compute the critical values of the *t*-statistics (G-1, where G is the number of clusters in the dimension with lowest number of clusters), as well as the number of clusters in each dimension. Below the regression output, the program also lists information on the average weights assigned to observations in the final weighted least squares estimation, as well as the fraction of observations with weights below 0.5 and weights equal to zero. Illustration of output:

![image](https://github.com/user-attachments/assets/37f3ef64-f544-4759-98e4-a3c475c56aa5)

**Examples of syntax with standard implementation**: 
```
robcluster2 mm y x x2 x3 x4 x5 x6, eff(95) cluster(firm year)
robcluster2 m y x x2 x3 x4 x5 x6, eff(70) cluster(firm year)
robcluster2 m y x x2 x3 x4 x5 x6, eff(95) cluster(firm year) biweight
robcluster2 s y x x2 x3 x4 x5 x6, cluster(firm year)
robcluster2 mm y i.year x x2 x3 x4 x5 x6, eff(95) cluster(firm year) m(i.year)
robcluster2 mm y x x2 x3 x4 x5 x6, eff(95) cluster(firm year dimension3)

```

**Example of syntax with post-estimation implementation**: 
```
robreg mm y x x2 x3 x4 x5 x6, eff(95) cluster(firm)
robcluster2, cluster(firm year)
```


