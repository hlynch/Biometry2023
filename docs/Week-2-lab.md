Week 2 Lab
=============

Confidence intervals
-----------------------

Before getting too far, we need to formally define a confidence interval. 

A 95th percentile confidence interval say “If I repeat this procedure 100 times using 100 different datasets, 95% of the time my confidence intervals will capture the true parameter”. It does NOT say that there is a 95% chance that the parameter is in the interval.

**Quiz time! (Don't worry, not a real quiz)**

*Important note*: This is an area where Aho is **WRONG**. Aho is correct on only one point. It is true that *once the 95th CI has been constructed*, it is no longer possible to assign a $\%$ to the probability that that CI contains the true value or not. Because that CI, once created, either DOES or DOES NOT contain the true value. However, we often talk about the interval in the abstract. **<span style="color: orangered;">When we say "There is a 95$\%$ chance that the interval contains the true value" what we mean is that there is a 95$\%$ probability that a CI created using that methodology would contain the true value.</span>**

Do not let Week 2 pass by without fundamentally understanding the interpretation of a confidence interval. 

So now we know the general idea behind confidence intervals but we don't yet know how to calculate them. To do that, we'll actually walk through an example of bootstrap using pennies. Each of you should have gathered the ages of five pennies. (If a penny was made in 2021, that penny would be 1 year old, etc.)

*Data*: 5 pennies that the students have

*Test statistic*: Median

Lets say we are trying to find the median age of all pennies in circulation. We can't figure this out exactly, because we can't collect all the pennies in circulation, but we each have a sample of 5 pennies. The median age of the pennies in our sample is a reasonable estimate for the median age of all pennies in circulation. 

What is our uncertainty about that number? How far might our estimate of the median age be from the true median age? In this case, we don't know the underlying distribution of penny ages. (Let's brainstorm this for a bit. Do we have any guesses what this distribution might look like? What might be a reasonable distribution to describe the shape of penny age?) 

Let’s use bootstrapped samples to calculate the s.e. associated with that estimate.

Procedure: 
1. Sample WITH REPLACEMENT a group of 5 pennies. (To sample with replacement you will have to sample one penny, write down the age, and repeat that 5 times.)
2. Calculate the median age from that sample of pennies.
3. Repeat

Do this a few times with your actual physical pennies, and then once you get the idea, you can make a vector in R of your penny ages and use R to speed up the sampling. Don't forget to sample with replacement.

Gather a collection of 100 median estimates, each one calculated using a different bootstrapped dataset. Calculate the Bias and the Variance of the estimator for the Median.

We now want to take this one step further and estimate the confidence intervals for the median age of a penny in circulation. We actually have two primary mechanisms for generating confidence intervals for the statistic.

**Method #1**: Simply take the quantiles directly from the distribution of $\hat{\theta}^{*}$:

$$
\theta_{LL} = \mbox{2.5th percentile of } \hat{\theta}^{*}
$$
$$
\theta_{UL} = \mbox{97.5th percentile of } \hat{\theta}^{*}
$$

Notice that (by construction) 95$%$ of the $\hat{\theta}^{*}$ values fall in the interval $(\theta_{LL},\theta_{UL})$. <span style="color: orangered;">This is the very definition of the 95th percentile confidence interval.</span>

**OR** 

**Method #2**: We can use the Normal approximation:

We have a second method that won't make 100\% sense until next week, but it turns out that if we assume the bootstrapped estimates follow a Normal distribution, 

$$
\hat{\theta^{*}} \sim N(\hat{\theta},\hat{se}^{2})
$$

we can use the fact that the 95th percentile confidence interval is approximately given by:

$$
\hat{\theta}_{LL}=\hat{\theta}-1.96*\hat{se}
$$
$$
\hat{\theta}_{UL}=\hat{\theta}+1.96*\hat{se}
$$

It turns out that 95$\%$ of the probability for a Standard Normal distribution lies between (-1.96$\sigma$,+1.96$\sigma$). (We will show this more formally next week.) 

NB: If you are going to go through the trouble of doing the bootstrap sampling, I don’t know why you would make a Normal approximation at the very end to construct the CIs. I recommend Method #1.

**<span style="color: green;">Checkpoint #1: Use your penny data to calculate the 95th percentile confidence interval using Method #1 and Method #2. What did you get?</span>**

Testing hypotheses through permutation
------------------------------------

These examples use data on the speeds of the top 20 racing pigeons from a race in Alma, GA on February 7,2021. 

**Example #1**: Use permutation methods to test whether Cock or Hen birds fly at different speeds (speeds are in meters-per-minute) (in other word: $H_{0}$: No difference in speeds between the C and H groups):

C=$\{1359.8,1355.3,1355.1,1353.0,1349.8,1348.8,1345.2\}$

H=$\{1357.5,1356.4,1355.1,1353.5,1353.2,1352.5,1350.0,1349.8,1346.2,1344.9,1344.4,1343.9,1342.6\}$

**<span style="color: green;">Checkpoint #2: Is this a one-tailed or a two-tailed test?</span>**

Make sure that you understand what is being done here, as this example is very closely related to the problem set.


**Example #2**: Using the same data, provide a 95% confidence interval for the difference in mean speed based on 1000 bootstrap samples

Note that these two approaches are very closely related. Do you see why either approach can be used to test the null hypothesis? **<span style="color: green;">Checkpoint #3: What is the null hypothesis here?</span>**

**Example #3**: Now we will do one slightly more complicated example from Phillip Good's book "Permutation tests: A practical guide to resampling methods and testing hypotheses":

Holmes and Williams (1954) studied tonsil size in children to verify a possible association with the virus \textit{S. pyrogenes}. Test for an association between \textit{S. pyrogenes} status and tonsil size. (Note that you will need to come up with a reasonable test statistic.)

<div class="figure" style="text-align: center">
<img src="Table2categories.png" alt="Data on tonsil size and S. pyrogenes status. Source: Good (1994)" width="40%" />
<p class="caption">(\#fig:unnamed-chunk-1)Data on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Now lets consider the full dataset, where tonsil size is divided into three categories. How would we do the test now? **<span style="color: green;">Checkpoint #4: What is the new test statistic? (There are many options.)</span>** What 'labels' do you permute?

<div class="figure" style="text-align: center">
<img src="Table3categories.png" alt="Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)" width="50%" />
<p class="caption">(\#fig:unnamed-chunk-2)Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Basics of bootstrap and jackknife
------------------------------------

To get started with bootstrap and jackknife techniques, we start by working through a very simple example. First we simulate some data


```r
x<-seq(0,9,by=1)
```

This will constutute our "data". Let's print the result of sampling with replacement to get a sense for it...


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 2 3 4 5 7 9 
## 1 2 4 1 1 1
```

Now we will write a little script to take bootstrap samples and calculate the means of each of these bootstrap samples


```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
```

The actual number of bootstrapped samples is arbitrary *at this point* but there are ways of characterizing the precision of the bootstrap (jackknife-after-bootstrap) which might inform the number of bootstrap samples needed. *In practice*, people tend to pick some arbitrary but large number of bootstrap samples because computers are so fast that it is often easy to draw far more samples than are actually needed. When calculation of the statistic is slow (as might be the case if you are using the samples to construct a phylogeny, for example), then you would need to be more concerned with the number of bootstrap samples. 

First, lets just look at a histogram of the bootstrapped means and plot the actual sample mean on the histogram for comparison



```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-6-1.png" width="672" />

Calculating bias and standard error
-----------------------------------

From these we can calculate the bias and standard deviation for the mean (which is the "statistic"):

$$
\widehat{Bias_{boot}} = \left(\frac{1}{k}\sum^{k}_{i=1}\theta^{*}_{i}\right)-\hat{\theta}
$$


```r
bias.boot<-mean(xmeans)-mean(x)
bias.boot
```

```
## [1] -0.0027
```

```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=5,col="black")
abline(v=mean(xmeans),lwd=2,col="yellow")
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-7-1.png" width="672" />

$$
\widehat{s.e._{boot}} = \sqrt{\frac{1}{k-1}\sum^{k}_{i=1}(\theta^{*}_{i}-\bar{\theta^{*}})^{2}}
$$


```r
se.boot<-sd(xmeans)
```

We can find the confidence intervals in two ways:

Method #1: Assume the bootstrap statistics are normally distributed


```r
LL.boot<-mean(xmeans)-1.96*se.boot #where did 1.96 come from?
UL.boot<-mean(xmeans)+1.96*se.boot
LL.boot
```

```
## [1] 2.720082
```

```r
UL.boot
```

```
## [1] 6.274518
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.6   6.2
```

Let's compare this to what we would have gotten if we had used normal distribution theory. First we have to calculate the standard error:


```r
se.normal<-sqrt(var(x)/length(x))
LL.normal<-mean(x)-qt(0.975,length(x)-1)*se.normal
UL.normal<-mean(x)+qt(0.975,length(x)-1)*se.normal
LL.normal
```

```
## [1] 2.334149
```

```r
UL.normal
```

```
## [1] 6.665851
```

In this case, the confidence intervals we got from the normal distribution theory are too wide.

**<span style="color: green;">Checkpoint #6: Does it make sense why the normal distribution theory intervals are too wide?</span>** Because the original were were uniformly distributed, the data has higher variance than would be expected and therefore the standard error is higher than would be expected.

There are two packages that provide functions for bootstrapping, 'boot' and 'boostrap'. We will start by using the 'bootstrap' package, which was originally designed for Efron and Tibshirani's monograph on the bootstrap. 

To test the main functionality of the 'bootstrap' package, we will use the data we already have. The 'bootstrap' function requires the input of a user-defined function to calculate the statistic of interest. Here I will write a function that calculates the mean of the input values.


```r
library(bootstrap)
theta<-function(x)
  {
    mean(x)
  }
results<-bootstrap(x=x,nboot=1000,theta=theta)
results
```

```
## $thetastar
##    [1] 4.3 4.1 5.6 4.2 4.7 4.6 4.4 4.5 5.6 5.1 6.1 5.7 3.8 4.3 4.5 3.6 4.9 4.6
##   [19] 4.7 4.5 4.6 6.0 3.6 6.3 5.0 3.8 4.2 3.1 4.6 5.9 2.9 3.7 5.1 3.5 4.7 5.7
##   [37] 4.4 2.4 4.6 4.5 4.8 4.5 5.9 3.6 2.1 4.1 6.1 3.7 4.7 5.5 5.2 3.9 3.1 4.2
##   [55] 5.6 3.9 4.0 4.7 6.4 4.1 5.1 4.5 4.2 5.4 5.8 2.8 4.7 4.0 5.9 4.3 2.9 6.9
##   [73] 5.5 5.4 4.1 4.9 4.8 5.9 4.5 5.4 4.4 2.3 3.8 2.5 3.9 4.4 3.9 4.2 6.2 3.6
##   [91] 4.2 4.5 4.6 5.3 5.4 5.6 5.6 4.5 4.2 4.2 4.5 5.0 4.1 5.7 3.4 5.2 4.1 4.7
##  [109] 5.3 2.6 4.1 2.8 6.1 4.1 6.0 3.5 4.1 5.2 4.5 3.0 5.0 5.1 3.6 4.1 4.8 3.9
##  [127] 4.5 6.4 5.0 4.6 5.9 3.5 4.6 4.3 5.9 3.9 3.3 3.4 4.0 5.4 4.9 6.2 4.4 4.5
##  [145] 4.1 3.7 5.4 3.9 4.3 5.7 4.5 4.2 3.8 4.8 4.0 4.4 4.3 5.6 4.8 4.0 3.2 6.1
##  [163] 4.3 4.3 4.5 4.3 3.2 4.4 5.6 5.1 4.7 5.5 4.4 3.8 5.0 5.1 2.6 3.9 4.2 4.4
##  [181] 5.6 4.3 3.6 4.4 3.9 4.9 4.4 5.3 4.4 3.5 3.6 6.2 4.7 4.1 3.7 4.5 4.7 4.9
##  [199] 4.9 4.3 5.8 3.6 4.1 4.3 4.2 4.8 4.1 6.6 4.4 4.0 4.4 4.8 3.5 4.6 3.3 4.0
##  [217] 3.5 3.5 4.2 4.3 4.8 5.4 5.3 5.4 3.5 3.4 3.9 3.5 5.4 4.0 4.6 4.3 4.0 4.7
##  [235] 4.3 3.7 5.1 4.9 4.8 4.3 5.2 4.3 3.8 3.4 4.2 3.0 5.3 3.8 3.5 4.4 3.3 4.7
##  [253] 4.0 4.9 4.0 5.0 5.3 5.2 5.2 3.8 4.3 3.8 4.1 5.8 5.9 3.1 6.3 2.7 3.6 4.6
##  [271] 6.0 3.6 3.4 5.2 4.5 3.3 3.5 4.5 5.1 3.6 3.7 3.4 6.5 3.1 4.8 4.3 4.1 5.6
##  [289] 6.3 5.1 5.3 4.3 3.8 4.1 5.2 3.7 4.9 5.4 6.4 4.2 2.9 5.5 4.7 4.9 4.3 5.1
##  [307] 2.6 4.2 4.0 3.7 4.3 4.6 4.9 4.9 3.8 4.2 5.0 3.6 5.1 6.2 3.8 5.0 3.8 3.4
##  [325] 5.4 3.6 4.6 4.4 3.5 3.7 4.5 3.9 4.5 4.3 3.7 5.0 4.1 3.2 4.3 3.8 2.9 3.3
##  [343] 5.9 2.9 3.1 4.0 5.3 5.0 4.0 4.4 5.0 5.7 5.1 4.0 4.2 3.3 5.1 3.4 4.1 3.5
##  [361] 3.2 4.0 3.1 3.7 3.3 4.8 3.1 4.5 4.8 5.1 5.3 4.7 6.0 5.4 4.2 4.8 4.7 4.0
##  [379] 5.2 3.6 4.0 4.0 4.2 3.4 3.8 5.5 6.0 3.3 5.4 5.7 5.0 5.0 6.3 4.5 5.8 4.3
##  [397] 4.9 6.2 5.9 5.0 4.8 5.2 2.3 3.6 3.0 2.4 4.6 5.0 3.3 3.2 5.6 5.4 6.0 4.1
##  [415] 3.3 4.8 6.2 5.5 5.3 4.0 4.0 5.0 4.2 3.7 1.9 5.0 5.4 4.3 3.7 3.8 5.9 4.9
##  [433] 3.5 2.3 4.2 5.8 3.6 4.8 4.6 4.0 4.0 3.2 6.3 5.2 3.8 4.3 2.9 5.5 3.9 3.1
##  [451] 5.0 3.5 4.0 4.4 5.0 4.4 3.6 2.6 5.2 4.8 3.9 4.0 3.4 3.6 5.8 4.5 5.7 5.5
##  [469] 4.2 3.6 5.0 3.5 3.2 5.5 3.7 3.9 3.4 5.9 4.4 4.4 5.1 5.3 5.7 4.8 4.1 6.2
##  [487] 4.8 5.0 2.6 4.2 4.1 2.8 6.6 5.9 4.7 3.8 4.6 3.8 4.6 4.9 4.1 4.0 3.8 3.9
##  [505] 4.1 4.3 5.0 4.1 4.5 4.8 4.1 3.9 5.3 5.1 5.1 4.8 3.5 4.9 4.3 4.1 4.8 5.0
##  [523] 5.4 3.4 7.2 4.9 5.2 5.1 3.4 4.4 4.4 4.0 3.9 4.4 6.4 4.4 5.0 3.8 5.1 5.6
##  [541] 4.5 3.1 5.6 5.4 5.2 4.1 4.2 5.6 3.4 4.8 3.7 6.1 4.2 4.9 5.5 5.4 5.1 4.6
##  [559] 6.7 3.4 4.4 3.3 3.7 4.4 3.1 4.9 6.1 2.5 4.3 4.3 4.4 4.8 2.0 3.5 4.4 2.5
##  [577] 3.5 5.7 4.0 5.4 4.9 2.3 5.9 5.3 5.1 4.1 4.3 3.5 4.0 4.4 5.4 6.6 4.8 3.7
##  [595] 4.5 5.5 6.0 5.4 3.1 4.5 4.1 4.8 4.9 3.9 6.7 4.5 5.2 2.4 4.4 4.5 4.2 4.9
##  [613] 5.7 3.7 3.9 4.5 4.1 4.1 4.9 3.8 3.4 3.9 5.1 4.2 3.1 3.6 3.4 4.3 3.0 4.3
##  [631] 6.0 5.4 4.8 4.4 5.0 4.9 4.6 4.1 6.0 3.9 4.2 3.7 5.7 5.0 5.0 4.2 4.2 4.3
##  [649] 4.9 4.8 5.7 4.9 4.8 4.8 4.2 5.2 6.5 5.2 6.1 4.7 6.1 4.6 4.6 5.8 4.0 3.7
##  [667] 4.5 3.9 5.5 4.4 4.0 4.5 4.2 4.7 4.5 4.9 4.7 6.7 3.7 5.2 4.5 5.1 4.2 4.9
##  [685] 5.2 4.7 4.1 2.9 3.2 3.2 6.2 4.5 5.4 6.0 6.2 3.1 5.2 4.7 5.0 4.9 3.6 5.4
##  [703] 4.6 5.5 4.9 4.5 4.6 6.0 4.8 5.3 6.4 1.5 4.3 5.3 3.2 4.2 4.4 4.7 4.9 3.7
##  [721] 3.4 3.3 4.3 5.0 5.7 4.6 4.5 4.0 4.0 4.4 3.6 5.2 4.9 3.7 5.4 4.3 5.2 5.7
##  [739] 4.6 6.1 3.3 6.3 4.9 4.0 3.4 5.1 2.8 2.3 4.4 4.6 5.4 5.0 5.9 4.3 3.4 5.4
##  [757] 3.7 4.3 4.6 3.0 4.9 3.3 4.0 4.6 4.1 5.6 4.5 4.3 5.7 5.9 5.3 5.8 4.3 4.3
##  [775] 4.5 4.1 5.4 4.2 5.5 5.3 4.2 3.8 4.8 3.9 4.2 4.7 4.3 3.6 4.2 3.7 4.4 4.3
##  [793] 3.3 2.7 3.6 5.0 4.0 3.3 3.9 5.6 4.3 3.1 5.7 3.8 4.6 3.5 4.2 3.6 4.1 4.6
##  [811] 5.6 2.8 3.4 5.4 5.1 2.3 5.1 4.2 5.3 4.4 4.3 2.4 4.8 4.1 4.9 4.0 5.2 3.7
##  [829] 5.6 5.3 4.7 4.8 4.6 4.7 4.7 4.6 4.0 5.4 5.3 2.5 4.0 5.7 3.9 4.1 5.6 1.7
##  [847] 5.2 4.5 4.5 3.5 5.0 4.2 4.0 3.9 3.9 4.1 4.3 2.0 4.2 3.9 4.2 4.0 4.9 4.6
##  [865] 4.6 5.5 4.7 3.2 6.4 5.0 5.5 5.7 4.8 5.4 4.0 4.9 4.4 5.6 5.4 3.9 3.6 4.8
##  [883] 4.4 3.6 4.1 6.0 4.5 4.0 4.1 4.1 4.8 4.5 4.4 4.9 5.2 6.2 5.0 3.9 4.5 5.1
##  [901] 4.5 3.9 6.3 4.1 4.0 4.1 4.6 5.8 3.7 5.3 4.3 6.1 3.6 4.0 4.6 5.2 5.2 4.0
##  [919] 4.2 5.4 3.7 2.9 5.0 5.4 3.4 3.1 4.8 5.3 4.2 3.4 5.2 5.1 4.3 3.8 5.3 3.5
##  [937] 6.2 5.8 6.4 4.5 2.9 3.6 5.8 4.1 4.9 2.7 2.9 3.6 4.6 4.5 4.7 3.7 4.3 3.6
##  [955] 3.2 4.9 5.0 4.4 5.7 3.8 4.5 3.5 3.0 4.5 4.8 5.4 4.6 3.4 4.8 4.8 2.1 5.0
##  [973] 3.9 4.0 4.5 5.1 4.6 4.7 4.0 3.5 3.8 4.4 3.7 3.2 2.3 3.5 5.1 4.9 6.6 3.6
##  [991] 5.6 4.7 5.3 5.3 4.9 4.0 4.9 5.0 3.4 5.2
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta)
```

```r
quantile(results$thetastar,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.6000 6.2025
```

Notice that we get exactly what we got last time. This illustrates an important point, which is that the bootstrap functions are often no easier to use than something you could write yourself.

You can also define a function of the bootstrapped statistics (we have been calling this theta) to pull out immediately any summary statistics you are interested in from the bootstrapped thetas.

Here I will write a function that calculates the bias of my estimate of the mean (which is 4.5 [i.e. the mean of the number 0,1,2,3,4,5,6,7,8,9])


```r
bias<-function(x)
  {
  mean(x)-4.5
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=bias)
results
```

```
## $thetastar
##    [1] 4.9 3.9 3.7 3.6 3.3 5.0 5.2 4.3 3.9 4.9 4.3 4.7 5.2 3.6 5.1 3.7 4.7 4.2
##   [19] 4.0 4.7 5.1 5.1 4.6 5.1 4.7 5.7 4.6 4.6 4.9 4.5 5.4 5.1 5.9 4.1 3.7 2.5
##   [37] 3.8 3.2 7.4 5.5 5.4 3.7 5.5 5.5 3.9 4.3 3.5 4.3 4.9 4.3 2.9 5.1 5.3 3.9
##   [55] 4.9 5.3 5.0 5.7 3.6 4.0 5.0 4.7 4.6 4.6 4.4 4.1 6.0 4.5 5.0 4.6 4.9 4.4
##   [73] 3.6 3.9 5.3 4.2 4.0 5.9 5.3 4.9 3.2 4.6 4.9 4.9 4.4 3.5 5.2 4.7 4.8 5.6
##   [91] 4.9 5.5 5.5 5.2 3.5 6.2 4.9 4.9 3.5 4.7 4.8 5.5 5.5 5.4 4.6 3.4 5.2 4.5
##  [109] 4.9 4.4 4.5 3.0 2.5 5.3 4.8 3.2 4.6 4.9 5.6 4.9 5.3 3.4 4.1 4.8 4.2 4.6
##  [127] 3.3 5.4 4.5 3.7 4.0 3.5 2.7 2.5 2.5 3.8 2.9 4.8 3.6 4.5 4.0 3.6 5.4 4.7
##  [145] 4.8 5.5 4.0 5.4 3.7 4.2 5.9 4.9 3.9 3.6 3.7 3.4 2.5 2.7 5.2 2.7 6.0 5.9
##  [163] 5.2 4.0 2.9 4.0 5.2 4.6 5.3 3.9 5.3 3.3 3.2 3.7 4.8 5.3 4.5 4.8 3.4 3.9
##  [181] 4.7 2.4 6.1 5.2 4.3 3.9 2.3 6.9 6.1 3.4 3.8 4.1 6.0 4.1 4.2 3.8 4.3 4.5
##  [199] 4.5 3.6 3.9 5.6 4.4 3.7 3.4 2.9 4.9 4.0 4.7 4.3 3.0 4.5 5.2 4.5 4.1 5.0
##  [217] 4.6 3.9 4.0 5.0 5.0 4.5 5.6 5.4 3.2 4.9 5.2 5.6 4.6 5.8 4.7 5.2 3.5 3.8
##  [235] 5.1 4.9 5.5 5.0 6.1 5.7 5.7 4.2 4.8 5.2 4.4 4.2 4.0 5.4 5.0 5.8 5.1 4.3
##  [253] 5.0 5.9 5.0 6.2 5.1 4.8 5.5 5.0 4.6 6.6 5.2 4.7 3.7 4.7 4.9 5.2 3.2 6.1
##  [271] 2.7 4.8 3.7 5.8 3.1 4.3 3.3 3.8 5.3 5.5 4.7 3.9 4.1 4.7 6.1 4.7 4.9 5.2
##  [289] 3.9 2.4 5.1 6.2 4.3 5.6 5.8 5.1 3.3 5.4 4.2 5.0 6.6 5.3 4.6 5.0 4.3 4.2
##  [307] 4.0 5.4 4.1 2.5 4.3 4.8 4.3 4.9 5.7 4.8 3.8 4.0 4.2 5.5 3.7 4.3 4.0 4.8
##  [325] 5.8 3.5 3.8 3.7 3.7 5.1 3.7 4.2 4.3 5.3 3.6 4.6 4.3 4.4 4.8 2.5 4.4 3.1
##  [343] 4.0 3.9 3.8 5.0 5.1 6.5 4.7 4.2 6.0 5.8 5.2 4.6 3.7 4.7 4.9 3.0 5.4 4.9
##  [361] 4.2 4.8 5.4 4.5 4.1 3.1 4.8 6.0 3.5 5.0 3.7 2.2 4.3 4.9 5.7 3.2 4.8 4.0
##  [379] 5.1 5.9 4.5 4.0 6.3 4.5 4.6 5.2 3.5 4.6 4.3 4.8 4.1 4.0 5.2 4.2 4.7 4.2
##  [397] 4.8 2.7 6.1 4.2 6.0 3.0 4.0 4.8 3.8 5.7 5.5 5.4 5.0 3.4 3.9 4.6 5.0 5.8
##  [415] 4.4 3.5 4.5 3.0 5.7 5.9 4.5 4.6 5.2 5.1 4.4 4.7 3.9 3.9 3.4 4.1 3.2 6.6
##  [433] 5.2 4.7 3.2 4.6 4.5 5.5 4.1 4.7 3.3 4.6 3.5 4.1 5.3 4.5 5.3 4.7 4.5 5.3
##  [451] 5.4 5.5 5.1 3.8 4.6 5.0 3.3 5.0 4.6 4.2 2.9 4.9 6.5 4.2 3.7 3.8 5.1 3.7
##  [469] 4.6 4.4 3.8 4.0 3.8 6.0 4.8 5.2 5.3 5.1 3.8 5.0 4.5 3.8 5.4 2.8 4.4 6.1
##  [487] 5.3 5.6 3.9 4.6 5.8 4.6 6.6 4.5 4.1 3.1 3.9 5.2 4.1 5.0 3.7 5.4 5.2 5.0
##  [505] 3.8 4.0 4.9 5.6 3.5 4.9 4.4 3.6 4.6 5.3 5.6 4.4 4.7 4.4 5.3 4.9 4.1 2.5
##  [523] 3.1 4.2 5.9 4.4 5.2 5.3 3.2 3.8 5.6 5.5 5.5 2.7 4.7 4.7 2.8 3.3 3.2 4.3
##  [541] 3.8 4.3 4.2 4.9 6.0 5.2 4.2 4.6 3.7 3.9 3.6 4.1 4.8 5.2 4.3 3.9 4.8 5.7
##  [559] 4.4 3.9 4.2 3.1 3.6 1.9 4.7 4.0 3.5 6.5 4.8 3.4 4.2 3.9 3.7 3.2 3.9 4.6
##  [577] 4.0 5.9 5.2 3.1 4.5 3.2 4.2 4.3 3.3 3.9 6.2 5.5 3.9 4.8 3.6 5.5 5.7 6.1
##  [595] 3.9 3.2 3.4 4.5 4.2 6.1 2.8 4.0 5.1 3.1 4.9 4.0 4.2 5.2 3.3 3.6 4.8 4.8
##  [613] 5.2 4.1 3.7 6.0 5.0 3.0 4.1 4.1 4.1 4.9 4.0 3.1 3.0 4.6 3.6 5.1 4.6 5.6
##  [631] 5.4 4.7 6.5 4.5 3.8 4.7 3.7 3.4 5.7 3.8 2.3 3.5 5.1 4.1 5.4 4.2 6.4 3.6
##  [649] 3.9 4.2 4.4 3.6 4.9 5.6 3.1 5.7 3.2 4.0 6.0 2.7 4.4 4.4 2.9 4.4 5.5 5.3
##  [667] 3.5 3.0 4.4 4.0 4.1 4.1 6.1 4.9 3.1 4.6 3.5 5.2 4.7 5.7 5.1 5.2 4.4 4.3
##  [685] 5.7 3.3 4.8 4.0 2.5 5.0 3.3 3.7 4.8 5.4 4.3 4.8 4.2 3.3 5.4 5.6 5.4 3.4
##  [703] 5.1 5.2 3.2 5.9 4.3 5.3 4.4 4.6 4.9 5.0 4.8 5.1 6.1 5.0 6.5 3.5 5.5 4.9
##  [721] 4.6 4.9 2.1 5.0 4.0 4.9 5.4 4.5 4.3 4.1 4.2 5.7 4.7 5.2 6.1 5.9 2.9 4.1
##  [739] 4.8 3.1 4.0 4.2 4.1 5.1 3.9 3.4 4.8 6.5 3.7 6.0 5.8 5.4 2.7 4.0 3.7 5.4
##  [757] 5.1 3.2 4.9 6.3 4.5 4.3 4.3 4.1 3.9 4.0 3.1 5.9 5.1 4.7 4.9 6.7 3.0 5.6
##  [775] 2.7 5.7 5.5 4.2 3.5 3.7 4.3 5.5 4.3 5.2 5.0 4.7 4.5 4.6 4.6 3.9 3.0 5.0
##  [793] 5.3 5.6 4.4 3.8 4.9 2.1 5.6 4.8 5.7 5.2 5.4 3.4 4.5 5.3 3.6 3.2 4.7 5.5
##  [811] 4.9 3.4 5.5 4.3 5.1 3.7 4.3 4.9 3.8 3.1 3.4 4.5 5.1 4.3 5.0 6.2 6.2 3.9
##  [829] 5.2 4.0 5.4 3.4 3.5 5.0 4.3 5.7 3.7 5.1 4.1 4.3 3.3 4.6 5.7 4.9 4.6 4.3
##  [847] 3.4 4.2 3.1 4.4 3.1 3.8 3.6 4.4 3.9 5.3 4.3 4.9 4.4 4.7 4.0 5.0 3.2 3.8
##  [865] 5.8 6.2 4.2 5.2 4.0 5.4 3.5 6.3 3.7 3.9 3.6 3.9 5.3 2.9 3.4 4.1 3.1 5.7
##  [883] 4.2 5.2 5.9 4.5 4.0 5.0 5.1 4.9 3.6 4.2 4.2 3.6 4.9 4.7 4.2 3.8 4.5 5.3
##  [901] 3.5 3.0 6.2 3.7 4.0 4.0 3.0 4.8 3.9 5.4 3.4 5.5 5.2 4.8 4.5 4.7 4.0 5.0
##  [919] 4.4 4.2 3.6 4.0 4.4 5.8 4.8 5.8 4.5 4.1 4.3 3.7 4.2 4.2 3.5 3.1 3.6 3.7
##  [937] 5.3 5.3 4.5 4.0 5.4 4.5 3.0 4.1 5.1 4.7 4.7 4.6 5.0 4.4 6.2 4.3 4.4 4.9
##  [955] 3.4 5.0 3.5 6.8 4.2 5.8 5.5 3.4 4.4 3.6 4.9 5.0 3.6 2.3 5.3 3.6 4.2 4.2
##  [973] 5.5 5.3 4.4 3.7 3.7 3.8 3.6 5.2 4.1 6.9 3.8 2.6 5.8 3.8 4.6 4.6 3.7 5.3
##  [991] 6.0 5.2 3.7 4.2 4.2 3.9 6.3 2.6 3.8 4.9
## 
## $func.thetastar
## [1] -0.005
## 
## $jack.boot.val
##  [1]  0.46744186  0.45521472  0.25327869  0.15351351  0.04066265 -0.02628571
##  [7] -0.18276836 -0.20226131 -0.36303191 -0.52914110
## 
## $jack.boot.se
## [1] 0.9493641
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = bias)
```

Compare this to 'bias.boot' (our result from above). Why might it not be the same? Try running the same section of code several times. See how the value of the bias ($func.thetastar) jumps around? We should not be surprised by this because we can look at the jackknife-after-bootstrap estimate of the standard error of the function (in this case, that function is the bias) and we can see that it is not so small that we wouldn't expect some variation in these values.

Remember, everything we have discussed today are estimates. The statistic as applied to your data will change with new data, as will the standard error, the confidence intervals - everything! All of these values have sampling distributions and are subject to change if you repeated the procedure with new data.

Note that we can calculate any function of $\theta^{*}$. A simple example would be the 72nd percentile:


```r
perc72<-function(x)
  {
  quantile(x,probs=c(0.72))
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=perc72)
results
```

```
## $thetastar
##    [1] 4.5 4.5 4.7 4.6 3.1 4.1 4.8 4.7 4.3 4.0 5.3 4.2 5.1 3.6 4.5 2.8 4.6 4.5
##   [19] 4.2 4.3 5.3 5.4 4.2 4.8 3.1 2.6 4.9 4.0 3.8 3.8 4.7 6.7 4.6 2.9 5.5 4.8
##   [37] 5.2 3.9 4.9 5.5 6.1 4.2 4.7 3.5 5.4 3.1 3.3 3.8 4.5 3.4 3.9 6.1 4.7 4.3
##   [55] 4.9 4.3 5.2 4.0 5.4 6.4 4.0 5.6 6.2 4.5 5.4 4.9 5.1 4.4 5.4 4.5 4.1 3.5
##   [73] 6.0 4.7 4.5 5.3 3.6 3.1 4.9 5.8 5.2 4.1 4.5 5.6 4.0 2.8 2.7 4.0 4.3 6.2
##   [91] 3.3 5.0 3.2 5.0 4.5 4.7 4.8 3.8 4.3 4.6 5.1 4.4 3.8 4.6 4.7 4.4 4.0 3.9
##  [109] 5.7 5.9 4.6 5.0 5.0 3.8 5.0 4.6 4.7 2.9 4.4 3.1 4.6 4.7 4.9 5.0 5.4 6.0
##  [127] 4.8 3.9 5.4 4.2 4.8 4.1 4.4 4.3 5.0 4.6 4.6 4.4 5.5 5.7 4.0 2.6 4.1 6.6
##  [145] 6.6 3.7 4.7 3.7 3.4 5.0 5.2 4.5 5.4 4.6 4.2 5.4 4.3 4.7 5.2 6.0 3.4 4.8
##  [163] 4.4 4.2 2.9 5.1 4.7 4.7 5.1 5.1 4.6 4.2 4.5 3.7 5.0 5.5 4.6 4.2 4.2 3.7
##  [181] 4.3 4.9 6.2 3.3 4.8 3.9 2.6 5.0 3.5 4.2 5.8 4.0 4.1 3.6 4.6 3.6 5.3 5.6
##  [199] 6.5 5.1 4.8 4.4 5.5 3.8 4.1 4.9 4.3 3.5 4.8 3.8 4.7 6.3 6.0 4.4 3.2 4.2
##  [217] 2.2 5.2 4.8 2.8 4.4 3.8 5.8 3.5 4.0 6.3 4.4 5.3 3.8 4.8 4.3 4.7 3.3 3.5
##  [235] 2.8 4.4 4.7 5.4 6.1 4.6 4.6 5.4 5.6 4.6 4.1 5.4 3.5 6.0 5.6 3.1 4.8 5.2
##  [253] 5.0 4.4 5.7 4.0 3.4 4.1 5.7 4.3 4.2 3.9 3.1 5.8 3.7 3.3 6.0 4.3 4.8 4.7
##  [271] 5.1 4.0 4.1 3.1 4.2 3.3 3.6 5.2 4.3 4.2 3.6 3.4 4.8 4.8 4.1 4.3 4.5 4.6
##  [289] 4.9 3.4 2.7 5.9 3.8 4.4 4.6 3.4 4.2 4.6 5.8 5.1 3.9 3.7 5.6 3.3 5.8 3.7
##  [307] 4.0 4.6 3.5 5.8 5.8 5.3 4.4 6.4 4.5 5.1 6.9 5.9 4.2 3.0 5.1 4.3 4.1 3.6
##  [325] 5.0 4.5 4.5 4.0 6.4 3.7 3.8 4.9 5.8 5.4 6.2 4.1 4.4 3.5 6.4 4.4 6.5 3.8
##  [343] 2.4 4.9 3.5 4.3 5.0 3.8 3.0 3.9 4.1 5.3 5.6 5.5 4.0 3.4 4.2 3.9 4.1 3.1
##  [361] 4.9 4.1 4.7 5.4 4.1 3.9 2.5 3.8 5.2 4.4 2.9 4.5 4.6 4.1 5.2 4.9 3.3 6.4
##  [379] 5.3 5.5 6.0 5.1 4.8 4.3 4.2 3.6 4.0 4.6 4.9 3.6 5.9 5.6 3.2 5.6 4.1 3.5
##  [397] 3.5 5.4 3.4 4.7 4.5 5.5 6.1 3.6 2.7 4.9 3.3 4.9 3.6 2.9 3.8 5.5 6.1 5.1
##  [415] 4.7 3.8 3.6 2.3 6.5 5.0 5.3 4.9 3.1 5.1 4.9 3.3 2.7 3.6 3.7 3.5 4.7 3.0
##  [433] 3.8 4.0 4.0 5.0 2.6 5.1 3.4 4.3 3.6 5.3 4.7 3.9 4.0 5.9 4.6 6.2 4.5 4.4
##  [451] 6.0 4.2 4.9 5.0 4.8 3.1 3.8 4.1 5.0 4.1 3.0 4.5 5.6 4.7 3.8 5.4 4.5 4.5
##  [469] 4.7 3.8 4.2 3.1 5.4 4.4 3.1 3.1 4.0 5.9 5.2 4.3 3.3 3.9 4.2 7.0 3.1 4.9
##  [487] 5.7 5.0 5.4 6.0 4.3 5.0 3.6 5.7 3.4 3.9 3.9 3.7 3.2 5.5 4.2 4.3 4.5 4.0
##  [505] 4.6 2.1 5.1 5.4 3.8 4.8 4.7 5.1 4.0 4.4 4.4 3.7 5.2 3.7 3.6 5.6 5.6 4.9
##  [523] 3.7 4.3 4.5 4.6 4.6 6.7 5.0 3.5 6.7 3.3 4.7 4.2 4.6 3.3 4.4 2.5 5.1 4.3
##  [541] 4.9 4.9 2.7 4.4 5.2 4.2 4.2 6.3 4.5 3.6 5.2 3.8 6.9 4.2 4.3 5.1 4.7 3.7
##  [559] 5.1 5.4 4.5 4.7 3.6 4.4 6.4 4.5 4.6 3.0 5.2 4.9 4.2 3.1 5.1 5.4 3.8 3.2
##  [577] 4.6 6.2 3.3 4.0 4.8 4.4 5.6 4.1 5.7 5.0 4.2 5.6 4.1 3.6 4.3 5.0 4.0 4.3
##  [595] 2.9 5.2 5.0 4.1 3.6 3.9 6.3 3.2 3.8 6.2 4.0 3.2 5.6 4.5 2.9 3.2 6.0 5.4
##  [613] 3.6 4.6 4.4 3.6 3.6 4.4 4.5 3.4 5.7 3.4 3.3 5.0 5.3 4.3 4.1 4.6 4.2 4.4
##  [631] 4.4 4.2 3.1 6.8 3.4 4.6 3.4 5.2 4.2 4.4 4.8 3.6 4.8 3.0 5.1 3.3 3.3 4.7
##  [649] 4.8 6.0 5.2 5.2 5.3 7.2 4.0 3.7 4.5 3.8 3.9 4.7 4.8 3.4 5.3 4.0 3.3 2.8
##  [667] 5.3 4.1 4.8 4.6 4.2 4.5 5.7 5.5 5.2 4.6 4.8 5.1 4.4 3.2 4.6 4.6 5.3 5.2
##  [685] 5.1 3.6 3.5 4.4 4.1 4.5 5.1 4.6 5.4 5.3 4.8 5.8 5.9 4.3 3.7 4.8 5.1 5.2
##  [703] 3.7 4.5 3.4 4.4 5.2 4.2 2.9 5.2 5.1 3.9 4.2 5.6 3.7 5.7 4.9 3.3 4.8 5.9
##  [721] 4.7 4.2 4.9 2.7 5.3 5.5 5.7 6.0 3.2 5.9 5.2 4.5 3.7 4.3 3.3 5.6 4.7 5.1
##  [739] 4.6 5.3 3.7 6.1 4.0 3.8 4.1 5.3 5.4 4.6 6.1 5.0 4.6 4.4 4.7 4.8 4.8 3.1
##  [757] 4.5 4.3 5.5 4.2 4.7 2.3 5.6 3.8 4.6 3.8 5.0 6.2 4.8 3.8 5.0 5.5 3.6 5.3
##  [775] 3.3 6.1 4.1 6.6 4.5 5.4 5.5 4.1 4.3 3.8 5.0 2.9 5.1 6.0 3.9 6.0 4.4 4.0
##  [793] 2.6 4.8 5.3 4.3 3.6 5.2 5.0 4.7 5.3 5.0 5.7 4.5 4.9 4.1 4.4 4.2 5.6 3.9
##  [811] 4.5 4.1 4.7 3.7 4.7 4.1 5.6 5.6 4.7 3.9 5.8 2.1 3.8 5.3 4.3 2.4 4.4 3.3
##  [829] 4.6 4.1 3.4 4.2 4.5 4.6 3.8 6.0 4.7 4.4 3.7 3.5 5.8 5.1 4.5 4.3 3.2 4.5
##  [847] 4.8 3.6 4.7 4.6 6.5 4.6 4.6 5.0 3.2 4.7 3.8 4.5 4.6 5.0 4.6 3.8 3.0 4.6
##  [865] 2.9 2.5 5.9 4.6 4.1 4.7 4.7 5.2 4.1 3.3 5.0 3.5 4.3 3.1 4.6 5.0 3.5 5.3
##  [883] 4.4 3.9 4.9 4.6 4.7 4.8 4.3 4.2 6.1 5.1 5.6 3.7 4.7 5.6 4.6 5.8 4.7 5.0
##  [901] 5.4 4.8 4.4 4.6 4.7 4.3 4.4 5.5 2.8 4.6 3.7 4.4 3.6 5.3 5.3 5.5 5.7 5.0
##  [919] 3.9 4.1 1.7 5.3 3.8 4.6 3.3 4.9 4.2 4.6 5.5 3.4 4.4 3.8 2.5 5.6 3.5 3.1
##  [937] 5.0 5.7 4.4 4.4 4.2 6.1 6.2 3.6 5.4 3.8 4.1 3.9 5.6 4.9 3.8 5.3 4.0 4.4
##  [955] 4.7 4.4 3.9 3.4 4.1 3.3 4.2 4.0 4.2 4.0 5.8 5.6 6.1 5.6 6.6 3.4 3.3 3.8
##  [973] 3.9 3.7 4.2 5.2 3.4 5.4 5.5 4.2 3.9 4.4 5.2 4.8 4.7 3.9 4.0 4.4 5.3 5.0
##  [991] 4.4 4.0 5.1 3.8 3.9 5.7 4.8 4.3 6.3 5.1
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.300 5.156 5.000 5.000 4.792 4.800 4.700 4.500
## 
## $jack.boot.se
## [1] 0.9224254
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = perc72)
```

On Tuesday we went over an example in which we bootstrapped the correlation coefficient between LSAT scores and GPA. To do that, we sampled pairs of (LSAT,GPA) data with replacement. Here is a little script that would do something like that using (X,Y) data that are independently drawn from the normal distribution


```r
xdata<-matrix(rnorm(30),ncol=2)
```

Everyone's data is going to be different. With such a small sample size, it would be easy to get a positive or negative correlation by random change, but on average across everyone's datasets, there should be zero correlation because the two columns are drawn independently.


```r
n<-15
theta<-function(x,xdata)
  {
  cor(xdata[x,1],xdata[x,2])
  }
results<-bootstrap(x=1:n,nboot=50,theta=theta,xdata=xdata) 
#NB: xdata is passed to the theta function, not needed for bootstrap function itself
```

Notice the parameters that get passed to the 'bootstrap' function are: (1) the indexes which will be sampled with replacement. This is different that the raw data but the end result is the same because both the indices and the raw data get passed to the function 'theta' (2) the number of bootrapped samples (in this case 50) (3) the function to calculate the statistic (4) the raw data.

Lets look at a histogram of the bootstrapped statistics $\theta^{*}$ and draw a vertical line for the statistic as applied to the original data.


```r
hist(results$thetastar,breaks=30,col="pink")
abline(v=cor(xdata[,1],xdata[,2]),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-17-1.png" width="672" />

Parametric bootstrap
---------------------

Let's do one quick example of a parametric bootstrap. We haven't introduced distributions yet (except for the Gaussian, or Normal, distribution, which is the most familiar), so lets spend a few minutes exploring the Gamma distribution, just so we have it to work with for testing out parametric bootstrap. All we need to know is that the Gamma distribution is a continuous, non-negative distribution that takes two parameters, which we call "shape" and "rate". Lets plot a few examples just to see what a Gamma distribution looks like. (Note that the Gamma distribution can be parameterized by "shape" and "rate" OR by "shape" and "scale", where "scale" is just 1/"rate". R will allow you to use either (shape,rate) or (shape,scale) as long as you specify which you are providing.

<img src="Week-2-lab_files/figure-html/unnamed-chunk-18-1.png" width="672" />


Let's generate some fairly sparse data from a Gamma distribution


```r
original.data<-rgamma(10,3,5)
```

and calculate the skew of the data using the R function 'skewness' from the 'moments' package. 


```r
library(moments)
theta<-skewness(original.data)
head(theta)
```

```
## [1] 0.6500064
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##      shape       rate   
##   2.1710214   3.0100787 
##  (0.9062435) (1.4128359)
```

Now lets sample with replacement from this new distribution and calculate the skewness at each step:


```r
results<-c()
for (i in 1:1000)
  {
  x.star<-rgamma(length(original.data),shape=fit$estimate[1],rate=fit$estimate[2])
  results<-c(results,skewness(x.star))
  }
head(results)
```

```
## [1]  0.65904032  0.34721350  0.32190697 -0.03967544  0.68062648  0.81623097
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-22-1.png" width="672" />

Now we have the bootstrap distribution for skewness (the $\theta^{*}$ s), we can compare that to the equivalent non-parametric bootstrap:


```r
results2<-bootstrap(x=original.data,nboot=1000,theta=skewness)
results2
```

```
## $thetastar
##    [1]  1.0222663420  0.8109914404  0.5652381538  0.1183327977 -0.0587293678
##    [6] -0.1042044231  0.9032095324  0.4228983142  0.8405591450  0.6294154040
##   [11]  0.8226280035  1.5261982354  1.4383635849  0.3512564078  1.1629495677
##   [16] -0.1907990763  1.4434465510  1.0912836588 -0.0197041294  0.4651595638
##   [21]  1.0369792596  0.6765644781  0.9367411767  0.0872745296  0.7303706107
##   [26]  0.3346258453  1.2618129286  0.2989375543 -0.0500229721  0.7450771438
##   [31] -0.3145829134  0.9605792378  0.0462108359  0.0012758316  0.6531025004
##   [36]  0.5609699235  0.1736974343  0.3699798987 -0.1771047774  0.7230490142
##   [41]  0.3054914969  0.1686875485  0.7200851683  1.0099933780 -0.0786125907
##   [46]  0.1364880824  1.4843007751  0.6595967848  0.1063065811 -0.1793848149
##   [51]  0.3219337644  1.0719574835  0.4502801377  0.1623242360  1.1493867241
##   [56]  0.6621452003  0.3938016116  0.1100757608  1.0416960632  0.8575094153
##   [61]  0.8686147380  0.4753868320  0.1628700462  1.5168860065  0.9128338586
##   [66]  0.8481581935  0.0214455655  0.1436584342  0.1607818472  0.8666325821
##   [71]  2.4976737067  0.6051311183 -0.0532959110  0.2342221623  0.8944492666
##   [76]  0.5001663833  0.2983800049  1.1492562230  1.5010047712  0.1271828458
##   [81]  0.6474498349 -0.3603322113  0.1643889756  0.7303062058  0.9119919099
##   [86]  2.1221035196  0.8171577906  1.8144390691  1.0375623072  0.3793273235
##   [91]  0.8738893067  0.9172393795  0.2269409024  0.0677799406  0.3550571166
##   [96] -0.1027944974  1.6839898768  0.6730248680  1.0870652805  1.6968672940
##  [101]  0.8739134064  0.4537743870  0.3884147249  0.1241365760  0.4986767320
##  [106]  0.2925906711  0.8377447223  0.2377863356  1.4236154094  1.1407639864
##  [111]  0.1416182384  0.1515951041  0.7385233153  0.8039022443  0.9814471077
##  [116]  0.5907164441  0.3368219791  0.5857704693  0.6892828097  0.9258751384
##  [121]  0.9484222894  1.2517687216  1.0030427553  0.9776020185  1.2692038885
##  [126]  0.3785954228  0.8794097373  0.3391058303  0.5697239373  0.4616105252
##  [131]  1.2084420359  1.0202683140  0.5826169967  0.1502157212 -0.0050317922
##  [136] -0.3951796702  0.5681299747  0.8429164423  0.3030035703 -0.0265515922
##  [141]  1.3611250256  1.5557703605 -0.1170742854  0.3242700659  0.5217210036
##  [146]  0.5867070397  1.0144024958 -0.0642498268  0.4327892427  0.1095523998
##  [151]  0.6135670942 -0.3145182137  0.2571587982  0.8722563361  1.8262030818
##  [156]  0.7815161055  0.8380550306  0.4977106515  0.8900165128  1.1210745101
##  [161]  0.5480141228  0.9604651651  0.1915716193  1.8920708775  0.4019053126
##  [166]  0.2966443552  0.2346905046  0.3201750319  0.7459817728  0.5260349882
##  [171]  0.8903412245  0.4357536221  1.1009175236  0.0462021435  0.5534709509
##  [176]  1.1232464461  0.3959887101  0.7390518039  1.6368511639  1.2918343677
##  [181]  0.7728651214  0.2508459796  0.2031992250  0.2674873650  0.1760591287
##  [186]  0.9822779616  0.3118733403  0.6441573661  0.6010841855  0.5165897270
##  [191]  0.6478634936  1.0374185487  0.5639807746  0.5038145761 -0.1737865554
##  [196]  0.7389168475  0.2978024543  0.5982562597  0.1498783021  0.0367603314
##  [201]  0.9900766889  0.7231381363  0.5923587891  0.7617744951  0.7566415027
##  [206]  0.3584486872  0.9327363809  1.3149834214  0.9141402245 -0.4214062240
##  [211]  0.6932721662  0.9004812101  1.2738460130  0.5850195916  0.0045177937
##  [216]  0.5642802345 -0.5879660091  1.1130115723  0.3543531136  1.2072853165
##  [221]  1.0853999520  0.7247000416 -0.1489158837  0.4009931067  0.3005181812
##  [226]  0.7973032119  1.0305372570  0.3884147249  1.5093810923  0.2734803732
##  [231] -0.0513233043  0.5430689678  1.0856298321  0.8905311932  0.8970793947
##  [236]  0.4999071476  1.6594669810 -0.1416587377  0.3019028622  0.0313019193
##  [241]  0.9108100232  0.3903788592  1.1517207462  1.1656175979  0.2544702688
##  [246]  0.3302273115  0.0345211126  0.2976764999  0.0831354286  0.7469726880
##  [251]  1.3883048550  0.9248441434  0.8309194962  0.6513312588  1.0166458509
##  [256]  1.0693626930  1.0472406034  0.5546579112  0.3394054037  0.0087259356
##  [261]  0.3777119983 -0.0919721146 -0.0884189354  0.6131506159  0.2011880825
##  [266]  1.8308168471  0.7327171659  0.7005063736 -0.0512676051  1.2259328972
##  [271]  0.5100371407  0.0061013582  1.1286736141  0.5238059345  0.0093186417
##  [276]  1.6822171780  0.0228693965  0.4336979651  0.5599263584  1.3464333541
##  [281] -0.0009758865  1.0411512861  1.2475021036  0.4193951254  0.7021365813
##  [286]  0.4179278663  1.2751260300 -0.0052495366  0.8990434204  0.4876693488
##  [291]  0.2702488646 -0.0312539577  0.3988427863  0.4824930501  0.6098808882
##  [296]  0.8351066021  0.4682844034 -0.0432829604  1.2460991943 -0.2813393477
##  [301]  0.5067477955  0.0954669439  0.8287731652  0.4169837523  1.4427250554
##  [306]  0.3001393827  1.1118915510  1.2285575810  0.0815607459  0.1264717752
##  [311]  0.4612132975  1.2292962561  0.9236475191  0.1694111501  0.4751566266
##  [316]  1.1877739763  1.3884481603  1.0295782332  0.0123768632  0.5757952065
##  [321]  1.2236031695  0.0657299227  1.0444782150  0.1330411029  0.4903222661
##  [326]  0.1142686894 -0.6177537089  0.7750679603  0.1673300875  0.0505894163
##  [331]  0.7976444620  1.3447074137  0.9368766557  0.3492510504  0.7243977032
##  [336] -0.3155554922  0.7375763536  0.1406581583  1.1805576566 -0.0647159826
##  [341]  0.8774192372  0.5724350515  0.4959015302  0.4132649173  0.9089185195
##  [346] -0.2551665430  0.2726652036  0.1743896054  0.6142040594 -0.4594964096
##  [351]  0.6701930659  0.6372918438  1.1523595449  1.8094340561 -0.0832959790
##  [356]  0.2909030230  1.6620650473  2.0955364423  0.6687400676  0.5252471196
##  [361]  0.9552441333  0.3461774223  0.3744146202  1.4398366791  1.5715578579
##  [366]  1.7208628780  0.6362292087  0.4850204268  0.0245044280 -0.0970967678
##  [371]  0.4314104154  1.1298462378  0.1660706021  0.4932343592  0.4425961845
##  [376]  0.0998414378  1.1805368821  0.5171242814  0.6313822730  0.8321395157
##  [381]  1.2742006434  0.0486935826  1.0223909707  1.0237914326  0.5939955289
##  [386] -1.5058133307  0.4687015540  0.8629891674  0.2445467030  0.6630167449
##  [391]  1.2079190258  0.6786374463  0.3179068043  0.9952354689  0.0003094670
##  [396]  0.2502023284 -0.0776941173  0.9528424366  1.5314421545  1.1937113893
##  [401]  0.7166566401  1.2528816719  0.3136297135  0.7464742338  0.1639955007
##  [406] -0.3534492871  1.0619360943  0.3512256636  0.1594343085 -0.1432465640
##  [411]  0.5606019248  0.6948336637  1.0355703935  0.2289175809  1.5303985027
##  [416]  0.7925777164  1.3849689939 -1.1393193475  0.7317934485  0.3113265673
##  [421]  1.9823653362  0.7219800063  0.6602096070  0.6500063656  0.6645553415
##  [426]  0.3508520892  0.1986456865  0.1661105874  0.5806091434  0.9721605775
##  [431]  0.3455654363  0.5779854606  0.2570020268  0.2517718964  0.1107768302
##  [436]  0.3073434363  1.0636070175 -0.1392597071  0.0707189232  1.7566976491
##  [441]  1.2019050281  0.0395937206 -0.0105393310  0.3783981329  1.2918567754
##  [446]  0.5391889227  1.6187314365  0.4928715406 -0.0516923788  0.5253740005
##  [451] -0.4155878897  0.2206200228  0.3587907897  0.7130443139  0.5348159358
##  [456]  0.3535368082  0.8578911267  0.3428327464  1.8935980263  0.6976296490
##  [461]  0.7327348287 -0.0556011222  1.0657442736  1.0166015273  0.2444894761
##  [466]  0.1254097328  0.1226045391  0.3615622988 -0.1454181536 -0.2713972139
##  [471]  1.4045966204 -0.0570530991  0.7009961922  0.6903717369  0.9521856167
##  [476]  1.2881154126  1.0737705345  1.4089464784  1.0168922611  0.6351450628
##  [481]  0.0319960021  0.8358160274  0.9928140378  1.5002208938  0.5066925728
##  [486]  0.5171237150  0.2651838481  0.5200902343  0.8279758798  0.5474908737
##  [491]  1.1078607293  0.5010400035  1.0509009870  0.5053879756  0.1129160529
##  [496]  0.6829521778  1.1660877780 -1.2940905569  1.1027881504  0.6958566841
##  [501]  0.9731926367  0.1917480550  0.2997599565  1.0061846819 -0.2063731281
##  [506]  1.3873117416  0.7409375604  1.7267360476  0.2550304847  0.5252902443
##  [511]  0.6920121032  0.7832149531  1.0387049711  0.9848306963  0.5565016870
##  [516]  0.7334692521 -0.5411718959  0.1468331857  0.8881389792  0.5629061570
##  [521]  1.4005237881  0.8432083526  0.7205638099  1.2068238997  0.3733164023
##  [526]  0.4274611551  0.9661459821  1.4600339472  1.3519343978  0.9169569464
##  [531]  0.5975170637  1.7677727849  1.1229748142  1.1382180775 -0.0152551651
##  [536]  0.4444424479  0.2464094104  1.4640598119  0.4221284555  0.9826267418
##  [541] -0.1134434176  1.8311929323  0.1311800298  0.1466870831  1.3345393437
##  [546]  0.1934809441  1.2240104541  1.3289495689  0.0227037095  0.2977486373
##  [551]  1.3691020077  0.8397572961  0.9952157822  0.7022167241  1.1120815315
##  [556]  0.9108100232  0.5389024998  0.6418132297  0.2599888300  0.4162705912
##  [561]  0.6951012976  0.6417729731  0.0020790381  0.3973378649  0.6858133448
##  [566]  0.3334573406  0.1844042843  0.7046699143 -0.1918178277  1.4340601546
##  [571] -0.1013295902 -0.0337653550  1.2231046465  1.0561956720  0.5783225343
##  [576]  0.5181509208  0.8970191481  0.1428199541  1.4712106367  0.7510428645
##  [581]  2.0240171319  0.3846089261 -1.1573096155  0.5946499584 -0.3297285815
##  [586]  0.5629243949  0.4818246612  0.1100057930 -0.0947992997  0.2709375991
##  [591]  0.3526812731  0.8728336737  0.3259311006  0.3365988531  1.2629516920
##  [596]  1.2176632169  1.1895257249  0.7984695407  0.5389037990  0.3667672008
##  [601]  1.7461628353  0.6971447636  0.6866606593  0.8533575060  2.0158183010
##  [606]  0.5425629671  0.3939395900 -0.2563747556 -0.2898359703  0.9200608744
##  [611]  0.8561577325  0.7484104827  0.9398164261  0.4011360818  1.2740184875
##  [616]  0.0839646573  0.0132781432 -0.0999983873  0.5683663168  1.2844760141
##  [621] -0.1945958689  1.1737844567  0.0926858104  0.6737942718  0.3114447385
##  [626]  0.7994825116  1.0102897571  0.0918411383  1.2039033486  2.1177076816
##  [631]  0.6660311608  0.1758192027  0.9218506585  0.9859262720  0.9058905443
##  [636]  0.3783543338  0.2761395493  1.4198625823  0.6667782967  1.1047784493
##  [641]  0.5066156470  0.4147140730  0.3177830182 -0.7810958791  0.6222029600
##  [646]  1.2420787298  0.2740537591  0.8738370242  0.9559303995  0.9510882470
##  [651]  1.3627362267  1.3770867776  0.1634754995  1.4017064274  0.4856473955
##  [656]  0.2217836456  1.6404904757  0.8948166979  1.2487146472  0.9025819312
##  [661]  0.8434284558 -0.7414027206  0.5297669889  0.7635298811  0.3381381252
##  [666]  0.2257055401 -0.1107329661  0.9102419591  1.9998890515 -0.3095566831
##  [671] -0.3380735858  0.5762499633 -0.3698608984  0.6960053163  0.4967494430
##  [676]  0.6349280949  0.1971799207  0.7653344691  1.1111016686  0.5243070671
##  [681]  0.4198804860 -0.3920636219  0.1323854723  0.1974788899 -0.0678770782
##  [686]  0.1910424774 -0.0273182358  0.6500063656  0.0798868348  0.6401376816
##  [691]  1.7147448862  0.2587581531 -0.3305788227 -0.1134434176  0.8163062987
##  [696]  1.1166705834  1.4236154094  0.0858372985  0.8856520641  0.5791269053
##  [701]  1.1043093890  0.6011619559  0.3395857294  1.4264344758  0.9697171995
##  [706]  0.5080141576  0.2005921948  0.2635500695 -0.3876846935  1.4704683593
##  [711]  1.0279449712 -0.2328120977  0.4120056785  0.8411465563  0.1913967727
##  [716]  0.4210594723 -0.0916788759  0.9311921454  1.1648855584  0.3739998435
##  [721]  0.4477380571 -0.0511425818  0.7698154804  0.0992705527  0.4263082447
##  [726]  0.0430505821  1.1324015887  0.8694182095  1.2963711897  0.1320181065
##  [731]  0.2822602106  1.5069605561  0.7446606128  1.1391512824  0.5058117811
##  [736]  2.0593649477  0.7471192702  1.2587640528  1.7988343118  0.9600214361
##  [741]  0.5368044674  0.1741253488 -0.0727487210  0.0115851510  1.5279044053
##  [746]  0.8689935395  1.3310480952  0.9183547230 -0.0606702859  0.7153438944
##  [751]  0.9552441333  1.0758222941  0.6735711023  0.5247465305  0.5936406710
##  [756]  0.3570332501 -0.1622556007  0.8195650456  0.7023982727  1.4285198801
##  [761]  0.8690822952  0.1676897418  1.0317730906  0.8311848519 -0.3315989234
##  [766]  0.8536683366  0.6427574216  0.7523983149 -0.1513257540  0.1419706242
##  [771]  0.6114536015  1.2005445896 -0.3422162906  0.9033093553  0.3734190460
##  [776]  1.2076141622  1.1251434577  1.3742808166  0.5589327684  0.6151535036
##  [781]  1.2604689390  1.3333619830  0.4394078584 -0.8618356373  1.6460469869
##  [786] -0.5138532509  1.2276822981  0.6130341196  0.5341966986  0.2116622983
##  [791]  1.0472465607  0.2469438092  0.2721469429  1.3176078792  1.2800801183
##  [796]  0.0502415735 -0.0891635447  0.4066252324  0.4144427264  0.8709858566
##  [801] -0.2643694763  0.0387910072  1.0015962704  1.3948705650  1.8560843516
##  [806]  0.7426380366  0.5673209369  0.1482250099  0.9238057554  0.7595962804
##  [811]  0.7689862262 -0.1266528093  0.3250979961  0.2996717201  0.3399976998
##  [816]  0.1324348672  0.6462400526  1.4058769604  0.2575780706  0.7819368651
##  [821]  0.0189959258  0.0914704261  0.7568016061  1.1268360825  0.2013710117
##  [826] -0.1483931693  0.3423697180  0.0527325011  0.5549261959  0.5442766915
##  [831]  0.5310586794  1.0835032722  2.1177118769  0.4763801046  0.5512630841
##  [836]  0.3172856047  0.2082013744  0.6053776784  0.8671945656  1.0717448606
##  [841]  1.0000162573  1.1618109573  0.9681328941  0.7311727211  0.3780651693
##  [846]  0.7587707668  1.3786732363  0.4645925416  1.2574383518  0.6734789439
##  [851]  0.6937479505  0.2974650207  0.5656993599  0.7819607596  0.6667782967
##  [856]  0.1167333538  0.5763743045  0.1677804150  0.6157237784  0.9347356831
##  [861]  0.7313603096  0.9346303194  0.5857691145  0.7141862136  0.3044937282
##  [866]  1.2671385425  0.9665192323  0.2709028069  0.3509359320  0.8134973509
##  [871]  1.3413629375  1.6993536334  0.2972986012  1.3169301114  1.3292426735
##  [876]  0.3983867106  1.2815375613  0.7917849442 -0.0292665899  0.4112135231
##  [881]  0.6319141555  0.2156215848  0.8693259312  0.3154340794  0.5026906085
##  [886]  1.7396971457  0.3461879849  1.2245287614  0.1654672607 -0.2279320007
##  [891]  1.3842991684  0.1377806800  0.4896687187  1.4793510799  1.8935980263
##  [896]  0.9139585268  1.9375334801  0.8922664326  0.7123902785  0.6052779660
##  [901]  1.1448175227  0.8186637727  0.0487554677  0.4992721892 -0.1195944999
##  [906]  0.3858621368  1.9989006670  1.7337747204  1.0139190728  1.2660398968
##  [911]  1.1663613071  0.5728696104  0.2771926390  0.2470623432  1.7510050004
##  [916]  0.9905826635  0.7188237490  0.6064703333  1.2624240266 -0.1980243653
##  [921]  0.5997410179  0.6165751162  0.6295309865  0.3759969870  0.4600653024
##  [926]  0.3340850464  1.0449160355  0.8297575431  1.1646847551  0.7956926678
##  [931]  1.4488898473  0.3117438621  1.3710717380  0.6418604662  0.8597450232
##  [936]  0.7831763693  0.7509585715  0.9262256018 -0.2008110487  1.1065410802
##  [941]  0.8805893319  1.4104358724  1.0799926272  0.1268074235  0.6161814140
##  [946]  1.1313515298 -0.0985947422  1.4267246332 -1.4256987892 -0.2593412204
##  [951]  0.3124654725  0.7219854631  0.5966479474  1.5612473356  0.3034947682
##  [956] -0.0924040829 -0.0026530676  0.1127138917  1.0931334547  0.5319510872
##  [961]  0.4306223033  0.0929561564  0.6858133448  1.1270983547  0.1518107231
##  [966]  0.8131138580  0.2147862956 -0.0612585230  0.9217257957 -0.2184106364
##  [971]  1.7937107492  0.9509842079  0.8439790708  0.1455132492  0.1194132019
##  [976]  0.7328186608  0.1152103043  0.4296412988  1.2200902370  0.9695080820
##  [981]  0.5561464062  0.3626884515  1.0158272339  0.2733619134  1.0855311441
##  [986]  0.7627522924  0.6899125723  0.7272816875  0.9106488806  0.2723591223
##  [991]  0.4973881734 -0.0785197954  0.4217218227  0.0105205471  0.6304865631
##  [996]  2.0988019308  2.0273738783  0.2365896632  0.5035295751  0.8639726874
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = original.data, nboot = 1000, theta = skewness)
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-23-1.png" width="672" />

What would have happened if we would have fit a normal distribution instead of a gamma distribution?


```r
fit2<-fitdistr(original.data,dnorm,start=list(mean=1,sd=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##      mean         sd    
##   0.7212442   0.4616387 
##  (0.1459830) (0.1032273)
```

```r
results.norm<-c()
for (i in 1:1000)
  {
  x.star<-rnorm(length(original.data),mean=fit2$estimate[1],sd=fit2$estimate[2])
  results.norm<-c(results.norm,skewness(x.star))
  }
head(results.norm)
```

```
## [1]  0.117958729 -0.410129498 -0.918791147 -0.410142130  0.185896530
## [6] -0.003916234
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results.norm,breaks=30,col="lightgreen",freq=F,add=T)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-24-1.png" width="672" />

All three methods (two parametric and one non-parametric) really do give different distributions for the bootstrapped statistic, so the choice of which method is best depends a lot on the situation, how much data you have, and what you might already know about the underlying distribution.

Jackknifing is just as easy at bootstrapping. Here we will do a trivial example for illustration. We will write a little function for the mean even though you could put the function in directly with 'jackknife(x,mean)'


```r
theta<-function(x)
  {
  mean(x)
  }
x<-seq(0,9,by=1)
results<-jackknife(x=x,theta=theta)
results
```

```
## $jack.se
## [1] 0.9574271
## 
## $jack.bias
## [1] 0
## 
## $jack.values
##  [1] 5.000000 4.888889 4.777778 4.666667 4.555556 4.444444 4.333333 4.222222
##  [9] 4.111111 4.000000
## 
## $call
## jackknife(x = x, theta = theta)
```

**<span style="color: green;">Checkpoint #7: Why do we not have to tell the 'jackknife' function how many replicates to do?</span>**

Let's compare this with what we would have obtained from bootstrapping


```r
results2<-bootstrap(x,1000,theta)
mean(results2$thetastar)-mean(x)  #this is the bias
```

```
## [1] -0.0472
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9112203
```


Everything we have done to this point used the R package 'bootstrap' - now lets compare that with the R package 'boot'. To avoid any confusion (a.k.a. masking) between the two packages, I recommend detaching the bootstrap package from the workspace with


```r
detach("package:bootstrap")
```


The 'boot' package is now recommended over the 'bootstrap' package, but they give the same answers and to some extent it is personal preference which one prefers to use.

We will still use the mean as the statistic of interest, but we will have to write a new function for it because the syntax of the 'boot' package is slightly different:


```r
library(boot)
theta<-function(x,index)
  {
  mean(x[index])
  }
boot(x,theta,R=999)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = x, statistic = theta, R = 999)
## 
## 
## Bootstrap Statistics :
##     original      bias    std. error
## t1*      4.5 -0.02802803   0.9236891
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 5 6 8 9 
## 1 1 2 2 1 2 1
```

```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
mean(x)
```

```
## [1] 4.5
```

```r
bias<-mean(xmeans)-mean(x)
se.boot<-sd(xmeans)
bias
```

```
## [1] 0.0086
```

```r
se.boot
```

```
## [1] 0.8947327
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

