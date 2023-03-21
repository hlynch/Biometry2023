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
## 0 3 5 6 8 9 
## 2 1 3 2 1 1
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
## [1] -0.013
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
## [1] 2.718231
```

```r
UL.boot
```

```
## [1] 6.255769
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.8000 6.2025
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
##    [1] 4.0 6.5 3.2 5.2 4.9 5.8 4.9 4.2 5.3 3.4 5.0 5.3 5.8 4.6 5.1 5.9 4.0 4.7
##   [19] 4.5 3.5 3.9 4.6 5.1 4.6 5.1 4.4 3.8 3.7 4.1 3.5 3.4 3.2 4.4 5.5 4.3 2.7
##   [37] 4.6 5.2 5.0 5.5 5.7 5.3 5.0 2.5 3.4 5.0 4.9 4.2 4.1 4.8 3.9 3.8 6.3 4.0
##   [55] 5.1 5.7 5.4 3.6 4.3 4.9 4.6 3.1 4.6 5.5 4.4 4.4 4.4 4.2 2.9 3.8 2.7 3.8
##   [73] 5.5 3.9 2.7 4.7 4.3 7.1 5.0 4.6 4.8 5.1 6.0 3.6 2.6 4.3 4.7 3.8 3.0 2.8
##   [91] 5.3 5.2 4.5 5.1 4.3 4.0 2.4 5.1 4.6 4.9 5.2 4.5 5.5 4.4 4.7 4.2 5.1 5.4
##  [109] 4.0 4.3 3.2 4.2 4.8 4.7 4.1 5.1 4.8 6.0 2.9 4.1 5.3 5.6 3.3 4.6 5.1 3.2
##  [127] 4.8 2.8 4.9 3.4 4.8 5.9 4.5 3.8 4.3 4.1 5.7 5.4 4.5 3.8 6.0 4.2 5.3 5.4
##  [145] 5.6 4.0 3.5 6.1 4.0 2.3 4.4 3.8 6.1 4.8 3.1 5.0 3.1 3.6 5.4 4.0 4.6 3.9
##  [163] 3.7 4.1 4.5 7.0 3.1 2.5 4.7 3.9 5.8 5.3 4.7 4.2 2.9 5.2 3.8 3.4 5.0 4.6
##  [181] 3.8 3.4 5.0 3.8 3.4 4.8 3.8 5.0 6.0 4.8 2.5 5.1 3.8 5.3 4.8 3.0 3.8 4.9
##  [199] 4.4 5.0 4.5 3.9 4.6 4.9 4.9 3.3 5.1 5.5 4.2 4.1 4.2 3.5 4.6 5.5 4.0 3.6
##  [217] 5.5 4.2 2.9 5.7 5.3 3.4 4.4 3.7 2.9 3.9 5.9 4.1 5.0 5.7 5.2 4.8 5.6 2.9
##  [235] 3.6 4.2 3.8 3.5 5.9 4.0 4.9 4.5 4.5 4.9 3.6 4.2 4.2 4.2 4.5 4.5 4.9 4.7
##  [253] 5.8 4.2 5.0 3.9 2.8 6.0 5.7 4.1 6.1 3.7 3.5 6.4 5.0 5.2 6.7 4.5 4.7 4.1
##  [271] 4.4 3.4 2.8 3.5 3.1 4.6 5.4 4.9 4.2 4.4 6.1 6.5 3.0 3.8 4.5 4.8 4.5 4.5
##  [289] 5.4 5.4 5.1 4.9 5.8 5.8 4.9 4.5 5.0 5.7 4.2 5.5 4.7 4.2 4.4 6.0 4.0 5.4
##  [307] 4.1 4.6 5.6 5.6 5.2 5.4 5.3 3.5 4.0 4.4 4.9 2.3 3.8 5.2 6.4 3.7 3.9 4.7
##  [325] 5.0 4.2 4.9 5.0 5.3 5.6 4.6 4.8 4.1 3.6 4.5 4.5 4.4 5.8 3.3 4.9 4.1 6.2
##  [343] 4.6 4.8 3.9 4.4 4.2 3.8 4.5 2.9 5.5 4.6 6.2 3.7 2.4 4.9 4.2 4.2 4.1 6.5
##  [361] 6.3 4.5 4.6 3.0 3.4 3.7 3.5 5.0 5.2 2.9 4.2 3.3 4.0 5.1 4.2 4.8 3.8 4.6
##  [379] 3.8 4.6 4.5 3.7 6.0 3.7 4.1 3.9 4.5 5.1 3.3 3.9 4.5 6.0 3.2 5.5 4.9 4.0
##  [397] 3.3 5.1 4.3 3.6 4.0 4.7 4.7 4.8 3.8 3.2 3.9 5.4 4.3 4.8 4.8 4.5 3.7 4.1
##  [415] 3.8 3.4 4.6 2.6 4.5 3.6 6.5 4.2 4.3 4.1 4.8 5.3 3.6 3.5 4.3 4.3 4.6 5.4
##  [433] 3.6 5.5 3.9 3.6 3.7 5.4 4.9 3.8 5.8 3.3 5.1 3.2 2.5 6.4 4.9 4.0 5.6 4.3
##  [451] 6.9 5.4 3.7 5.0 3.4 2.2 4.0 6.1 3.3 5.7 4.6 3.2 3.6 3.8 4.7 4.1 4.8 3.2
##  [469] 5.3 3.4 5.3 3.5 5.5 3.1 3.1 5.1 5.3 5.7 5.7 4.4 3.8 3.7 4.7 5.0 3.4 5.7
##  [487] 4.7 4.2 5.0 4.8 4.7 3.8 4.1 3.3 4.8 3.4 4.8 5.1 5.2 3.8 4.1 6.2 5.7 4.2
##  [505] 4.7 4.2 5.7 3.4 3.9 5.9 4.7 5.1 4.7 6.8 4.1 4.4 4.5 4.0 4.2 2.7 3.7 5.1
##  [523] 2.7 6.1 4.9 4.3 4.0 4.8 4.1 4.0 5.8 5.9 4.0 4.4 6.2 4.2 4.5 5.0 3.8 5.4
##  [541] 3.8 3.3 4.3 4.1 4.0 3.9 5.7 5.0 4.2 5.1 6.1 4.0 6.8 4.2 4.2 3.9 3.8 4.8
##  [559] 3.0 5.0 5.4 4.1 4.7 4.6 4.4 5.6 4.8 5.8 2.8 4.3 4.8 3.7 5.3 5.8 5.3 3.8
##  [577] 4.0 2.9 5.2 4.7 3.3 5.0 5.4 1.9 5.1 5.1 4.0 4.6 3.2 4.2 3.5 6.6 4.4 4.7
##  [595] 4.7 4.3 4.5 4.5 3.2 5.1 2.6 4.8 4.4 5.6 4.1 4.4 4.4 2.6 5.6 3.6 5.2 4.5
##  [613] 4.5 4.6 6.5 5.4 5.2 3.7 4.6 6.4 2.6 4.7 3.6 5.1 3.7 3.9 4.5 5.9 5.1 4.7
##  [631] 4.1 4.7 4.3 3.8 3.5 5.0 3.0 4.5 5.7 4.3 4.4 5.4 5.3 4.4 5.4 5.5 4.8 4.7
##  [649] 6.5 6.5 3.4 5.4 5.4 5.0 5.5 4.1 4.9 5.0 6.3 3.0 3.0 4.8 4.8 4.2 4.1 2.5
##  [667] 5.1 4.1 5.5 4.8 3.9 4.3 5.8 4.3 5.4 4.9 5.2 4.6 4.2 4.4 5.2 5.2 3.9 5.3
##  [685] 4.1 4.6 4.0 4.8 5.0 3.6 5.5 3.6 4.5 4.4 5.3 3.5 3.0 4.5 4.5 5.1 5.4 5.3
##  [703] 4.9 4.0 4.4 3.9 4.0 4.8 4.1 5.8 4.0 5.0 5.7 4.3 3.6 5.9 6.3 6.8 6.1 3.9
##  [721] 4.8 2.9 3.7 3.9 5.1 6.5 4.2 2.1 2.8 4.1 4.3 2.6 5.6 3.7 6.4 5.0 5.2 4.1
##  [739] 3.6 5.0 4.2 4.9 5.4 5.8 3.8 3.1 3.6 6.4 4.8 6.1 2.9 6.2 4.3 4.9 5.2 3.9
##  [757] 7.1 4.5 5.8 5.9 4.1 4.2 3.6 5.1 4.6 3.9 5.7 4.8 4.2 6.5 5.4 4.1 4.6 4.5
##  [775] 4.4 3.1 5.0 4.0 3.6 3.8 4.4 4.3 5.1 3.6 5.2 4.8 4.4 5.4 5.9 5.1 2.5 4.7
##  [793] 5.3 4.3 6.1 5.0 5.0 4.4 5.2 3.9 4.9 5.5 3.9 5.5 4.0 4.1 4.3 5.9 3.9 5.5
##  [811] 4.4 3.9 4.4 4.4 4.6 4.1 5.5 3.9 4.4 4.8 6.5 4.2 3.7 4.7 3.0 4.0 5.0 4.9
##  [829] 5.5 4.7 5.8 3.9 4.2 4.1 5.6 4.8 4.1 5.2 5.1 5.3 4.6 4.4 5.5 3.6 4.2 4.6
##  [847] 4.6 5.3 5.4 4.6 5.3 3.6 5.0 3.3 4.2 5.4 4.7 3.9 5.8 3.5 3.5 5.7 4.3 4.6
##  [865] 5.1 3.3 5.5 2.7 5.7 3.7 6.9 6.1 4.7 3.5 4.6 5.3 4.2 4.3 3.2 5.7 4.7 5.3
##  [883] 4.5 3.2 6.1 4.7 4.7 4.9 5.3 5.4 5.2 5.0 6.1 5.6 3.5 4.1 5.5 4.0 4.3 4.3
##  [901] 4.2 4.6 4.2 3.8 6.1 2.7 5.9 5.3 2.8 5.6 6.0 3.3 4.3 4.7 4.8 4.9 4.4 5.3
##  [919] 5.3 2.8 4.6 4.3 3.7 4.0 3.9 5.1 3.3 3.6 3.9 4.9 6.1 6.7 3.7 7.4 3.6 4.4
##  [937] 5.5 3.8 6.8 4.4 5.1 4.3 3.5 3.6 3.5 4.3 4.6 7.3 5.7 4.4 3.9 4.6 4.4 4.5
##  [955] 5.1 3.6 5.9 6.5 4.9 5.8 4.5 4.5 4.6 3.3 3.0 3.6 3.7 3.2 2.4 4.6 3.1 4.8
##  [973] 5.3 4.7 3.9 4.1 3.6 4.3 5.6 3.9 5.6 4.0 2.7 4.1 4.7 4.0 3.2 4.3 4.6 4.3
##  [991] 6.1 5.4 5.5 5.0 4.2 5.8 5.7 6.0 4.7 2.4
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
## 2.7000 6.4025
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
##    [1] 5.8 3.6 5.9 2.9 3.5 5.2 3.9 4.3 2.6 4.1 5.0 3.0 4.2 6.8 4.1 3.8 3.5 3.8
##   [19] 4.2 3.6 4.3 4.7 3.3 6.2 3.7 4.6 3.4 5.2 3.4 4.6 5.5 4.9 3.9 5.1 4.6 5.3
##   [37] 4.9 4.5 5.5 3.8 4.0 5.3 4.2 4.7 3.8 6.0 4.6 3.0 4.2 3.7 5.0 5.0 3.6 3.7
##   [55] 4.0 3.5 3.9 5.4 3.5 4.8 5.7 5.7 3.7 5.9 5.1 3.5 4.2 3.9 3.7 4.9 4.4 6.4
##   [73] 4.4 4.1 4.1 4.3 4.5 5.7 5.7 6.2 5.5 5.1 3.2 3.9 5.0 5.5 5.1 5.8 5.6 5.8
##   [91] 3.5 3.4 5.2 4.1 4.3 4.0 3.2 6.4 5.3 3.8 4.6 4.5 4.6 5.0 4.1 3.5 6.1 3.8
##  [109] 5.3 5.7 5.3 5.3 3.7 4.1 3.9 5.1 5.7 3.5 5.1 4.7 4.4 3.1 4.0 4.2 3.9 3.7
##  [127] 5.8 4.1 4.2 4.9 4.8 3.0 3.3 4.3 4.1 5.1 5.0 4.4 4.8 4.1 4.3 5.3 4.6 3.4
##  [145] 6.7 4.7 4.2 4.0 3.0 3.4 3.6 4.2 4.4 3.6 2.1 5.9 4.2 5.2 4.4 4.5 4.1 4.1
##  [163] 4.4 4.0 3.3 4.3 3.8 5.7 4.3 4.9 5.3 4.5 6.3 4.7 3.7 4.4 4.0 2.9 5.1 6.4
##  [181] 5.2 3.7 5.6 4.7 3.7 4.0 4.0 4.0 4.4 4.3 3.4 5.1 3.4 4.4 4.2 4.6 5.0 4.0
##  [199] 3.5 4.9 5.1 6.7 3.8 5.5 6.0 4.5 4.6 4.9 4.8 2.9 3.7 5.3 2.9 5.3 4.5 4.4
##  [217] 4.9 2.6 5.0 5.0 5.6 4.5 4.7 4.9 3.9 4.5 3.7 5.2 5.5 4.6 5.2 5.3 4.3 4.6
##  [235] 4.7 5.5 6.1 3.5 4.7 5.7 5.7 4.3 5.7 2.9 5.8 5.3 4.7 4.4 4.8 4.4 4.8 5.2
##  [253] 4.4 5.4 3.6 3.4 5.4 3.3 3.6 6.5 3.6 5.3 4.3 5.3 5.1 4.6 4.5 3.9 4.5 4.0
##  [271] 3.4 3.1 5.5 4.3 3.5 4.5 5.2 4.9 3.5 4.1 3.8 4.3 5.4 5.7 5.5 4.9 5.3 6.0
##  [289] 3.7 4.8 4.1 4.2 3.8 4.2 3.1 4.1 5.2 4.4 6.4 5.9 5.5 3.8 6.4 6.5 4.2 4.7
##  [307] 5.1 3.8 5.0 4.2 5.7 5.3 5.2 5.2 2.8 4.5 5.7 4.7 6.0 4.6 4.8 2.8 4.7 5.0
##  [325] 4.0 4.3 5.0 3.8 2.7 4.5 5.7 4.7 5.5 4.7 3.4 4.9 3.8 5.7 4.6 4.6 2.8 4.4
##  [343] 5.5 4.8 5.6 4.3 4.3 4.3 3.9 5.6 4.7 4.0 3.9 4.8 4.0 4.7 3.0 5.1 3.7 5.9
##  [361] 4.4 3.7 4.1 4.5 4.7 3.7 5.5 3.7 4.2 3.6 3.1 4.6 5.1 4.3 2.5 3.7 5.8 3.6
##  [379] 3.6 6.3 4.7 4.2 2.7 4.6 3.5 4.1 3.7 2.7 4.2 4.3 4.7 3.4 5.9 5.6 3.9 5.4
##  [397] 4.6 5.5 4.9 4.3 3.8 3.3 5.4 4.9 4.5 5.0 5.4 4.8 3.9 3.7 5.1 5.7 4.3 4.6
##  [415] 6.2 4.4 5.1 4.5 4.8 4.9 4.5 4.7 4.7 4.6 3.7 4.5 4.1 5.3 3.0 4.1 4.4 4.4
##  [433] 4.7 4.7 4.6 5.5 5.1 3.0 4.9 3.6 2.0 4.3 5.0 4.8 6.4 5.2 4.8 3.0 4.8 4.6
##  [451] 5.3 3.4 5.0 4.7 4.1 4.3 3.6 2.9 4.0 3.5 4.8 5.6 4.8 2.8 4.9 4.6 3.4 2.9
##  [469] 5.6 5.6 4.6 5.1 5.5 5.2 5.5 3.5 2.8 6.0 2.7 4.5 3.1 4.3 4.7 4.4 5.4 4.2
##  [487] 4.8 4.2 4.9 4.8 5.5 3.0 4.3 2.9 3.7 5.7 5.3 3.5 4.5 4.7 4.0 4.4 5.2 5.1
##  [505] 4.1 3.1 2.9 5.3 5.1 4.4 4.4 4.3 4.3 5.2 4.7 4.6 5.5 5.1 5.2 5.0 5.0 5.2
##  [523] 3.8 4.4 7.1 4.5 4.9 3.9 3.6 4.7 5.0 3.8 3.6 5.4 4.2 3.9 5.1 6.4 4.9 5.5
##  [541] 3.5 3.8 4.2 4.8 6.6 4.6 5.1 6.1 4.1 4.5 4.6 6.3 5.0 6.6 4.7 5.7 3.8 4.2
##  [559] 5.4 4.0 6.1 4.4 5.9 4.3 4.2 3.5 5.7 5.8 4.1 5.3 4.8 3.8 5.3 6.0 3.5 4.1
##  [577] 4.7 4.7 3.7 4.7 5.6 4.0 4.3 3.5 5.2 5.3 3.6 4.4 6.3 5.2 4.2 3.5 4.3 3.8
##  [595] 3.8 4.5 4.3 3.2 5.8 4.8 5.5 4.8 3.9 4.1 3.8 4.6 4.6 6.3 3.3 4.0 5.1 4.0
##  [613] 4.9 5.6 2.5 5.0 3.3 3.9 4.6 3.5 4.8 3.4 4.7 4.1 4.2 4.1 5.7 3.1 3.0 3.3
##  [631] 3.7 4.1 3.2 5.5 3.0 5.4 6.2 5.4 4.5 3.2 5.3 5.2 4.5 4.4 3.9 3.4 5.1 5.4
##  [649] 4.2 3.5 4.7 5.7 5.3 5.1 5.3 5.1 3.9 5.0 4.7 4.1 4.6 4.3 5.2 4.5 3.4 5.0
##  [667] 3.8 5.7 4.5 5.3 3.6 6.0 5.1 4.2 2.8 5.3 4.3 4.6 3.0 3.6 5.6 6.2 4.0 4.6
##  [685] 4.3 3.6 5.8 6.3 4.1 2.9 3.3 4.6 3.8 3.9 5.2 5.9 4.7 3.3 4.3 4.4 3.3 5.7
##  [703] 3.8 3.9 4.1 6.3 4.1 5.5 4.7 5.0 2.5 4.4 3.5 5.5 4.2 3.2 4.4 4.1 5.2 6.1
##  [721] 3.9 3.4 5.1 5.4 4.1 3.7 3.7 4.2 3.9 4.8 3.7 3.6 6.4 4.0 3.3 4.3 3.6 4.7
##  [739] 4.7 6.6 4.0 4.9 3.9 1.8 5.5 6.6 4.1 3.4 5.5 4.6 3.9 5.0 3.8 5.9 4.2 4.5
##  [757] 5.9 4.4 5.4 5.0 4.0 2.6 4.6 5.8 5.5 5.3 6.4 6.1 5.3 5.5 4.0 4.1 3.9 4.5
##  [775] 3.3 5.2 5.4 5.4 4.7 4.0 4.2 5.2 5.1 5.5 5.0 4.0 5.2 4.2 3.6 3.8 5.9 5.3
##  [793] 4.4 4.0 3.5 4.1 4.7 5.6 5.3 3.7 3.6 4.6 5.3 5.0 4.3 3.9 4.7 3.8 5.2 4.7
##  [811] 4.9 5.6 3.8 3.6 3.9 4.4 4.3 5.5 5.1 3.5 5.3 5.3 5.6 4.8 4.8 4.6 4.3 6.6
##  [829] 5.9 4.0 4.2 3.4 4.2 3.4 5.6 2.9 4.0 3.2 3.4 4.0 4.1 3.5 5.0 3.5 3.9 5.1
##  [847] 5.2 5.4 3.2 4.7 3.8 3.0 3.8 4.7 4.6 3.5 5.0 5.1 3.2 4.7 4.9 3.5 4.8 4.6
##  [865] 5.9 4.4 5.6 4.4 4.7 4.2 4.6 4.2 4.0 4.6 4.8 5.4 4.2 6.0 3.0 3.6 5.4 4.7
##  [883] 4.8 5.9 4.0 5.2 5.6 6.3 4.3 4.4 5.5 4.4 5.6 4.7 4.6 3.6 4.8 4.9 3.4 2.2
##  [901] 4.6 4.7 3.6 5.3 4.0 3.6 4.8 3.8 4.8 4.8 5.3 5.5 2.7 6.2 4.0 4.5 4.1 4.6
##  [919] 4.7 4.6 4.4 4.7 4.4 3.6 4.6 3.4 2.6 4.6 3.3 5.4 4.7 4.1 3.9 3.7 5.1 3.8
##  [937] 4.6 4.5 5.6 2.5 4.0 4.9 3.6 4.4 5.0 4.1 4.9 5.4 2.7 4.9 5.1 2.7 4.0 3.8
##  [955] 5.8 4.6 5.0 3.4 4.8 4.1 4.3 4.3 3.7 4.0 4.9 4.7 4.7 6.2 5.2 4.8 3.7 5.4
##  [973] 3.1 5.1 6.1 5.2 3.9 4.5 4.8 3.1 3.2 4.8 2.3 3.5 3.7 4.6 3.2 5.5 4.9 4.3
##  [991] 5.3 4.2 5.9 5.0 4.3 5.1 4.6 5.8 4.8 3.6
## 
## $func.thetastar
## [1] 0.0135
## 
## $jack.boot.val
##  [1]  0.46859504  0.33391813  0.30221607  0.20932203  0.06617647  0.02396450
##  [7] -0.18375350 -0.28126801 -0.44428969 -0.43926380
## 
## $jack.boot.se
## [1] 0.9363174
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
##    [1] 5.4 5.2 6.0 5.0 4.5 4.5 4.6 4.2 4.3 4.3 3.4 3.9 5.0 2.7 5.6 4.1 4.4 5.7
##   [19] 4.5 3.9 6.6 4.0 4.3 5.5 4.0 5.3 5.3 4.3 3.5 4.8 5.2 5.5 6.1 4.3 3.7 4.4
##   [37] 3.7 2.4 4.1 4.3 4.3 4.3 4.7 3.7 2.9 5.0 3.7 4.6 6.1 3.8 4.9 5.4 3.8 3.4
##   [55] 3.7 4.5 5.2 3.8 3.7 6.0 3.5 3.4 2.4 3.9 3.4 4.5 4.2 4.8 5.0 1.7 5.6 4.6
##   [73] 6.1 6.1 3.1 4.4 5.1 4.8 5.4 4.9 4.8 5.0 5.3 4.6 6.4 4.7 4.1 5.2 3.0 3.8
##   [91] 5.4 3.5 4.6 4.9 5.6 3.9 4.0 4.6 5.7 3.7 4.7 5.2 5.0 5.3 4.4 4.8 5.4 6.8
##  [109] 5.2 5.3 3.8 3.9 3.9 3.7 4.4 4.7 3.6 4.8 3.3 3.1 5.7 4.6 4.7 5.8 4.6 5.0
##  [127] 4.9 3.4 4.9 4.7 5.9 3.9 4.5 5.1 4.3 4.6 4.6 3.4 5.0 4.5 4.2 5.9 5.9 4.8
##  [145] 6.4 3.9 3.3 5.3 4.1 3.9 4.9 3.4 4.9 5.4 5.5 3.6 4.4 3.9 4.1 4.5 3.9 4.2
##  [163] 4.7 4.9 5.7 4.4 3.0 5.2 3.6 4.7 3.5 4.4 5.7 5.0 5.2 3.5 3.4 3.5 5.0 5.2
##  [181] 5.0 4.7 5.9 4.8 4.0 5.1 4.6 4.2 3.9 5.2 3.0 2.6 3.1 2.8 3.2 5.3 4.8 4.4
##  [199] 5.0 3.9 4.1 3.8 4.7 3.6 4.6 6.2 3.6 5.5 5.0 4.9 4.8 5.1 4.3 3.2 4.4 4.1
##  [217] 4.7 3.3 4.2 3.5 5.2 3.4 4.4 3.8 4.9 4.4 4.7 5.0 3.7 5.7 5.1 5.4 4.3 4.1
##  [235] 5.1 5.4 4.5 5.0 3.4 4.8 4.4 4.2 4.6 3.4 5.9 3.7 6.4 4.3 4.5 3.8 5.6 5.9
##  [253] 3.4 6.2 4.4 5.5 3.9 5.4 4.1 6.1 5.5 5.0 3.7 4.6 4.1 4.1 3.8 5.1 3.2 3.8
##  [271] 6.0 5.6 4.0 2.6 4.6 3.4 5.3 3.4 5.7 3.3 4.5 3.9 3.6 4.6 4.4 5.5 4.2 5.8
##  [289] 3.7 4.3 3.1 5.1 5.4 3.3 5.3 2.1 5.2 3.2 4.9 4.1 3.6 5.0 5.4 4.1 5.6 3.9
##  [307] 4.0 4.7 3.5 6.2 5.7 3.6 4.8 6.0 4.2 3.8 6.7 3.4 5.7 7.2 4.6 5.4 6.0 4.5
##  [325] 4.3 4.4 5.2 4.4 5.4 5.3 3.3 6.2 5.4 5.2 4.1 4.0 4.1 4.1 4.1 3.7 3.6 4.4
##  [343] 4.8 6.2 4.0 4.0 3.5 3.4 4.2 5.3 4.4 4.1 5.0 4.5 4.7 5.8 4.7 2.8 4.4 2.6
##  [361] 4.3 4.1 3.3 3.8 4.4 5.7 6.0 4.1 4.6 6.1 5.0 4.6 4.5 3.3 4.5 4.8 6.1 4.9
##  [379] 4.5 4.9 5.4 5.2 5.8 4.7 4.4 3.2 4.4 3.8 4.8 4.2 4.0 5.5 3.5 3.3 4.9 5.2
##  [397] 5.2 4.2 5.6 3.7 3.7 4.9 4.3 3.9 4.0 3.5 3.9 5.0 4.0 5.5 3.9 3.5 2.3 5.8
##  [415] 5.3 5.4 5.3 6.4 4.5 5.7 3.6 4.4 4.3 4.9 4.0 6.1 4.0 4.5 4.1 5.1 5.7 4.0
##  [433] 4.4 4.7 3.2 3.9 5.5 4.2 4.3 4.7 5.1 5.3 5.3 4.1 3.6 4.9 3.4 4.3 4.5 5.8
##  [451] 4.8 4.1 5.2 3.1 4.4 6.5 5.0 5.2 5.4 4.8 2.1 7.3 3.8 5.4 5.6 4.3 3.8 5.5
##  [469] 4.9 3.9 5.2 3.9 4.1 4.4 3.3 4.0 4.6 5.3 4.3 4.4 3.8 2.9 3.3 2.9 3.7 4.7
##  [487] 6.8 5.0 4.8 3.8 5.2 4.9 5.7 4.0 4.5 5.0 3.7 5.5 4.6 3.9 4.9 3.7 4.3 5.4
##  [505] 3.9 4.9 3.2 4.4 3.1 4.4 3.7 5.3 5.7 5.4 4.9 3.5 3.6 3.3 2.8 4.9 5.1 3.1
##  [523] 4.7 3.7 6.2 5.1 4.7 5.3 4.5 6.4 5.0 5.7 4.6 5.8 5.1 4.4 3.2 2.4 4.5 5.9
##  [541] 3.7 5.8 3.7 3.9 6.1 6.0 5.7 5.0 3.9 4.1 4.9 4.0 4.2 3.9 3.6 4.6 5.8 4.2
##  [559] 5.5 3.3 5.7 2.9 3.1 3.9 3.9 5.8 4.0 4.5 4.6 3.9 4.0 5.8 4.0 3.6 3.9 4.1
##  [577] 4.4 4.4 3.1 5.3 3.5 7.5 5.3 4.5 4.4 4.8 5.3 4.8 5.0 4.5 6.7 2.9 4.4 3.6
##  [595] 4.0 3.9 3.7 4.6 3.9 4.7 4.5 5.1 4.6 4.9 4.2 4.2 4.6 5.2 5.1 4.9 6.5 3.4
##  [613] 4.4 5.4 3.8 6.2 4.2 3.9 3.5 2.9 3.5 5.7 2.7 4.9 3.5 5.0 4.8 4.0 3.2 4.5
##  [631] 3.6 6.3 3.9 5.2 4.0 3.8 4.0 3.6 3.8 5.5 5.5 6.7 4.6 3.4 2.8 1.8 3.9 5.2
##  [649] 5.3 5.7 4.5 5.2 5.9 4.9 4.5 3.6 6.4 4.0 3.8 3.5 4.0 4.7 4.6 5.1 5.6 5.2
##  [667] 4.6 5.6 4.9 3.4 4.2 2.7 3.8 5.6 4.7 3.0 4.7 3.3 4.2 4.3 5.4 5.3 4.9 4.3
##  [685] 4.4 5.1 5.4 5.7 4.9 5.6 4.8 4.2 3.5 5.3 4.3 3.6 3.7 3.1 5.0 4.9 3.2 3.1
##  [703] 4.8 4.6 3.3 3.0 5.2 4.9 3.0 4.9 3.4 5.1 4.3 5.1 3.6 4.5 4.0 5.9 4.9 5.4
##  [721] 3.8 3.9 4.3 6.2 4.3 4.9 4.9 5.7 5.8 4.9 3.4 4.5 6.1 3.8 4.4 4.8 6.2 5.1
##  [739] 5.1 2.9 5.3 4.1 4.6 3.8 4.7 4.6 4.8 4.5 3.9 5.5 5.4 3.5 4.5 2.4 5.2 4.9
##  [757] 4.8 3.6 4.2 6.9 3.0 3.7 3.1 2.5 4.4 4.6 4.6 4.1 5.3 5.7 5.8 2.9 4.9 4.6
##  [775] 3.9 5.5 4.7 3.9 4.7 4.8 3.6 5.8 4.5 5.4 5.8 4.7 4.9 5.7 4.4 4.0 3.2 4.5
##  [793] 4.6 4.4 5.7 5.1 4.6 5.4 5.9 3.2 5.5 3.2 4.6 4.6 3.5 5.4 5.4 4.8 5.0 4.5
##  [811] 3.3 4.6 4.3 3.8 4.6 2.6 3.9 4.5 6.4 4.7 5.8 4.0 4.0 4.8 4.4 5.0 5.0 3.8
##  [829] 4.1 5.8 4.3 5.8 6.2 4.9 4.2 4.7 4.2 5.1 4.3 4.2 4.7 5.2 5.2 4.3 5.2 5.2
##  [847] 4.6 4.5 4.4 4.8 6.2 5.0 4.4 2.8 3.9 3.9 4.5 3.8 4.1 5.3 6.0 2.5 4.2 4.9
##  [865] 5.5 3.1 5.8 5.2 3.7 4.3 2.6 5.5 3.4 3.7 5.6 3.8 5.1 4.3 4.8 4.1 3.8 4.3
##  [883] 4.6 3.5 5.7 5.5 3.7 3.1 5.2 5.1 4.9 5.7 4.7 3.5 4.6 4.8 4.2 4.4 4.2 3.5
##  [901] 5.1 5.2 5.7 5.4 5.4 4.5 5.5 1.9 3.6 4.0 3.1 4.7 3.1 4.5 5.6 4.8 4.4 3.8
##  [919] 4.0 5.4 5.6 4.6 4.7 4.8 4.1 4.4 4.3 4.7 5.6 5.6 4.9 5.2 5.9 5.1 3.3 4.2
##  [937] 5.1 6.5 6.0 5.2 5.8 6.0 5.5 2.3 4.6 4.1 4.0 4.5 5.9 2.8 4.4 4.9 3.9 4.4
##  [955] 5.2 4.9 4.9 6.2 3.5 5.1 5.2 3.9 4.2 6.7 4.4 4.7 4.0 4.1 4.6 3.3 5.0 1.5
##  [973] 4.5 3.0 6.0 2.9 3.8 4.0 2.4 3.7 5.4 3.3 3.4 5.0 4.8 5.8 4.4 4.2 6.5 4.5
##  [991] 5.5 6.3 4.0 2.9 4.7 4.1 3.4 4.5 2.9 4.3
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.3 5.3 5.2 5.0 4.8 4.8 4.6 4.5
## 
## $jack.boot.se
## [1] 1.032715
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
## [1] 0.5143897
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##      shape       rate   
##   2.3698258   3.9809619 
##  (0.9942824) (1.8596979)
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
## [1] 2.3637902 0.6140191 0.5417741 0.6069077 0.4636535 0.2822984
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
##    [1]  0.579941742  0.361511048  0.352981890  0.626167707 -0.130756086
##    [6] -0.016942840 -0.699086366  0.844467948  0.219892424  0.156717500
##   [11]  0.032026633  0.507033536  0.054277926 -0.554770029  0.324400917
##   [16]  0.628032041  0.113160388  0.836664376  0.592156107 -0.936759402
##   [21]  0.636683577  0.564436914  0.764376366  0.382085928  0.799372035
##   [26]  1.277657047  0.406380634  0.745108678  0.508127451  0.368346004
##   [31]  0.537550221  0.126178697  0.155403024  0.722850716 -0.110803243
##   [36] -0.757684748  0.791473521 -0.205713204  0.546538000  2.067086770
##   [41]  0.607599440  0.211815819  0.567679820  0.811433115  0.425784755
##   [46] -0.072539366  0.240710101  0.009695150  1.235520145 -0.551753263
##   [51] -0.203830483  0.052520306  0.839915386  1.395144590  0.050348261
##   [56] -0.715545982  1.278624001  0.414877665 -0.111792470  0.524450654
##   [61]  0.905778192  0.495856093  0.572430319  0.537544287  0.415147568
##   [66]  0.475359914 -0.227244240 -0.119128966 -0.725271983  0.441108144
##   [71]  1.174896039  0.352222607  0.934750018  1.301935814  0.898878421
##   [76]  0.544016538  0.922213724  0.011728371  0.185816779  0.665624308
##   [81]  0.389726110  0.207320560  0.702576127  0.553212155 -0.373664293
##   [86]  0.907193503  0.018093514  0.082902739  1.151134271  0.072278459
##   [91]  0.762661713 -0.304163627  0.136422721  0.218700852 -0.730759953
##   [96]  1.120744300  0.320175905  0.677726598  2.122581121  0.865334085
##  [101]  0.816157528  1.051076708 -0.181891644  1.066064526  0.179240085
##  [106]  0.380551186  1.091869729  0.849936582  0.269596676 -0.097799741
##  [111] -0.445386298  1.060518555  0.425744745  0.050915287  0.396059888
##  [116]  0.822625146  0.318327484  0.350451887  0.638653597  1.091259290
##  [121] -0.006837174  0.453430693  0.901935299  0.322706975  0.413572571
##  [126]  0.361849391  0.525109470  0.651568885  1.666059619  0.232248359
##  [131] -0.357841455  0.094819244  0.003665003  0.699664230 -0.078617204
##  [136]  0.127341632  0.309530508  0.308009244  1.350845220  0.435390972
##  [141] -0.069022938  0.518224283  0.829868349  0.463484139 -0.419500269
##  [146]  0.317993385  0.553040633  1.061529584  0.644102481 -0.002234073
##  [151] -0.234125466  0.779508203 -0.459938534  0.706514248  1.032932422
##  [156] -0.332766639  1.187634856  0.035339896 -0.411433154  1.639240465
##  [161] -0.405964237  0.446207219  1.153407325  0.672276316  0.025089563
##  [166]  0.128772218  0.765869342  0.055113016  0.090466296  0.321372917
##  [171] -0.372147223 -0.660176596  0.452564066  0.693985104 -0.117999750
##  [176] -0.112459216  0.162766216  0.688218698  0.709238180  0.303258512
##  [181]  0.307954052 -0.810035107  1.245605899  0.380584967  0.709148593
##  [186]  0.754600947 -0.263370304  1.253836016  1.686198495  0.182172582
##  [191]  0.973828275  0.438275526 -0.091160550  0.492634349  0.095152579
##  [196] -0.181415478  0.299674037 -0.292949423  0.062668523  0.145845823
##  [201] -0.161517918  0.859876717  0.478134362  1.839519664  0.849968680
##  [206]  0.133251145 -0.260324174  0.043521453  0.030532039  0.150781770
##  [211] -0.301054175  0.809702098  0.851474689  0.952384871  0.629107047
##  [216]  0.764906141  0.052262603  0.327281775 -0.507091041  0.106162089
##  [221]  0.397294001 -0.207842548 -0.228873253  1.099748616  0.868746387
##  [226]  0.297896668  0.377372101  0.531958738  0.679589144 -0.492973979
##  [231]  0.534768640  0.736482976  0.118624274  0.257029602  1.420238556
##  [236]  0.099984414  0.337442676  1.072222524  0.333882938  1.057603270
##  [241]  0.676623316  0.792536880  0.856140842  0.275051167 -0.469768653
##  [246]  0.789903984  0.620850944 -0.219741399  0.626107570  0.037540409
##  [251]  0.062198750  0.329279664  0.169776971 -0.226797093 -0.361158733
##  [256] -0.161213442 -0.156249257 -0.044225690  0.715395429  1.697413997
##  [261]  0.101785053  1.558334764 -0.197595683  0.418787225  0.721646644
##  [266] -0.216147027  1.718056726 -0.079287706 -0.567109149 -0.579579786
##  [271] -0.037773653 -0.090141065  0.679682849  0.852028247  0.542806860
##  [276]  0.308211849 -0.199639172 -0.325337363  0.941579093  0.569214855
##  [281] -0.229742440 -0.015172842 -0.391202027  0.094576302  1.134547966
##  [286] -0.301054175  0.198395544 -1.250666760  0.297791489  0.789788410
##  [291]  0.786396309  0.093316510  1.709707274  0.623390509  0.456107849
##  [296]  0.104349553  0.388399445 -0.121796626  0.211303684  0.783634708
##  [301]  0.700797597 -0.048416653  0.100930847  1.606024836  0.781012666
##  [306]  0.912118035  1.069604089  0.259533403  0.415358068  0.230590253
##  [311]  0.312186959  1.288470802  1.748259272  0.191234851 -0.417597241
##  [316]  0.345122659  0.229509435  0.309073598  0.837120291  0.755497580
##  [321]  0.444304451 -0.087554656  0.375538733  0.485326650  0.607252017
##  [326]  0.352166202  0.073648405  0.620079972 -0.436069832 -0.195215881
##  [331] -0.336707644  0.013737380 -0.006241996 -0.543458572  0.515412847
##  [336]  0.066683647  0.255810102  0.862487718  0.716587247  0.105831840
##  [341]  0.607599440  0.500785509  1.277281011 -0.154680605  1.177177785
##  [346]  0.303887037  0.862394336  0.304113436  0.366982046 -0.193945271
##  [351]  1.579024614  1.262212641  0.212297542  1.354676563  1.298561478
##  [356]  0.444787355  0.210138573 -0.009302983  0.532015304 -0.623965877
##  [361]  0.416473667  0.137575866  0.121955139  0.540950798 -0.015550255
##  [366]  0.029236770  1.073084825  0.889623551  0.180386215 -0.658699270
##  [371]  0.193729331  0.629632910  0.296344613 -0.221184482  0.511928161
##  [376] -0.271142736  0.432839031  0.350212920 -0.717000481 -0.489459711
##  [381]  1.363813370  1.062379589 -0.830195533  1.233161812  0.056291313
##  [386]  0.023567123 -0.450084157  1.404027823  0.928418071  0.164774735
##  [391]  0.985448821  1.215453402  0.378168221  0.805159747  0.820597666
##  [396]  1.161157724  0.512007513  0.340117813  0.089018272  0.616481165
##  [401]  0.271325486 -0.188782882  0.635648570  0.133638400 -0.483568516
##  [406]  0.316371757 -0.030023360  0.543500605 -0.295958905  1.023112995
##  [411]  0.090502133  0.974546295  0.450213111  0.290568990  0.339224089
##  [416] -0.195921398  0.995266174  0.411268168 -0.060954227  0.155843130
##  [421]  1.491370877  0.774947636  0.781857705  0.156105638  0.878364005
##  [426]  1.030268280 -0.113780649 -0.452926559  0.130325585  0.937875476
##  [431] -0.610229085  0.445827769  1.643731241 -0.832910189  0.433806104
##  [436] -0.834029781  0.051215259  1.486256811  0.121944204  0.574794790
##  [441] -0.281878098 -0.017022271  0.962668350 -0.314456981  1.605099549
##  [446]  0.267559658  0.924024150  0.407545049  0.107906331  0.012126330
##  [451]  0.207335075 -0.052064059 -0.199861049 -0.055480449 -0.224697460
##  [456]  0.004284380  0.451599686  0.252203281 -0.150494431  0.507565033
##  [461] -0.205617864  0.552042619 -0.204677536  0.412347437  0.649018782
##  [466] -0.223579565 -0.239680251 -0.876600802  0.771948950  0.819589410
##  [471]  1.021078435  0.220970627  0.065603727  1.337616504  0.247590548
##  [476]  0.038542713 -0.252162002  1.028728349  0.236860539  0.778774525
##  [481]  0.614444957  0.339998219 -0.033543176  0.124419349  0.132630912
##  [486]  0.236283468  1.512280484  0.171109356  0.111829237  0.392659119
##  [491] -0.481800752  1.235455679 -0.166789089  0.796910779  0.942840812
##  [496] -0.033939733  0.757975094  0.616707822  1.028728349  0.207335075
##  [501]  0.209622685  0.106492232  0.315163139  0.337926485 -0.083478969
##  [506]  0.353143817 -0.294898882  0.539539512  0.729704496  0.367569071
##  [511]  0.703648864  0.223948934  0.095817538  0.099172035  0.334200408
##  [516] -0.431331052  0.171507123 -0.580894930 -0.480568226  0.265466249
##  [521]  0.974546295  0.608840365 -0.233177948  0.637549305  0.122295733
##  [526]  0.463680303  0.019024310  0.393629982  0.912118035  0.836489863
##  [531]  0.654095845  0.380492333  0.377799612 -0.573618063  0.268854495
##  [536] -0.470049580  0.191661971  0.782754287  0.364142665  0.810926169
##  [541]  0.380074531  0.951882555  1.745135787  0.353421074  0.231043674
##  [546]  0.158234230  0.904335765  0.468522573 -0.136459353  1.146044702
##  [551]  0.453047712  0.493309293  0.399882234  0.958127858  0.943056211
##  [556] -0.448109338  0.211206641  0.413820951  0.018707494  0.607599440
##  [561] -0.364973754  1.012544644  0.796666512  0.536024684  0.043487002
##  [566]  0.248993152  1.637339567  1.019801050  0.774976092 -0.193981567
##  [571]  1.021921153  0.796936902  0.985001836  0.855178714  0.838413491
##  [576]  0.256417711  0.280613561  0.257220432  0.098157819 -0.249682805
##  [581]  0.093533429  0.462191397  0.070830387  1.132920657  1.208716739
##  [586] -0.049120592  0.293620920  0.677454052  0.595474996 -0.097038069
##  [591]  0.820371373  0.388168564  0.926237614  0.568414400  1.228204149
##  [596]  0.171921408  0.155531215 -0.236002251  0.483470226 -0.267896836
##  [601]  0.785015602  0.818113556  0.431447339  0.865334085 -0.149814556
##  [606] -0.095627781 -0.405944002  0.450524842  0.456258476  0.683709781
##  [611]  0.074892197  0.186885364  0.635198544 -0.338487218 -0.561418686
##  [616]  1.560848468  1.076633726  0.298836900  0.359113776  0.473360982
##  [621]  0.946057868  0.717568946  1.331583498  1.010742816  0.317499665
##  [626]  0.357766138  1.220267936 -0.296935664 -0.598455773  0.965461887
##  [631] -0.034306712  0.500047367  0.299677758  0.341163335  0.309464450
##  [636]  0.859119229 -0.005221555 -0.160903423 -0.164906339 -0.401346562
##  [641]  0.163243125  0.030969513  1.009169165 -0.049771716 -0.750854796
##  [646]  0.205288000  0.160400785  1.422150589 -0.152118571  0.520452820
##  [651]  0.271816813 -0.072036301  0.241079141  0.578087401  0.975137639
##  [656] -0.062837783  0.180390318  0.697836531 -0.060667855  0.151674276
##  [661] -0.962159630  0.167607736  0.318522612  1.543945165 -0.114640274
##  [666]  0.280509843  0.713891389  0.052233537  0.894682271  0.086745262
##  [671]  0.056095574  0.536981096  0.816716555  0.609126009  0.467235795
##  [676]  0.126120335  0.340117813 -0.320686036  0.528468873  1.611375344
##  [681]  1.111928991  0.680999036  0.153117682  1.093783368  0.606909295
##  [686] -0.331691087  0.364217294  0.320951660  0.816638481 -0.141457354
##  [691]  0.322729632  0.316190536  1.279094917  0.514692553  0.195472228
##  [696]  0.347890963  1.152806317 -0.425935202  1.283185535 -0.111044697
##  [701]  0.812348356  0.060315934  0.741422592 -0.306703164  0.274948397
##  [706]  0.168149380  0.366010308  0.594924840  0.306815256  1.310651599
##  [711]  0.341029267  1.185089557  0.787094820  1.387713640  0.447393877
##  [716]  0.609215365  0.223420042  0.355779523 -1.070854228  0.643569298
##  [721]  0.106948842  0.287569948  0.813407286  0.114285412  0.869193270
##  [726] -0.461166621  0.761183295 -0.063489884  0.757452879  1.088484785
##  [731]  0.302708429  0.475380205  0.741876888 -0.284569070  0.526838888
##  [736]  0.111088125  0.115437052  0.340659800  0.076042777  0.666725215
##  [741]  0.390758977  0.752778657  0.777679381  0.517034449  0.825340896
##  [746]  0.281624468  0.083097290  1.222524714  1.561276275  0.252036576
##  [751]  0.610123672  0.274202490 -0.134655794  1.090285117  0.067417645
##  [756]  0.553015277  0.045289746  0.594193282 -0.485900317  0.505124767
##  [761]  0.108628810  0.954122064  0.109361546  0.022943727  0.782366907
##  [766]  0.721857211  0.397936291  0.506424268  0.693963176  0.676286570
##  [771] -0.057072139  0.408065521  0.577934562  1.022167567  0.397074026
##  [776]  0.619394681 -0.001150652 -0.408305332  0.373156581  0.394068709
##  [781]  0.352909943 -0.307817328  0.108204943 -0.766902196 -0.179115812
##  [786] -0.122095680  0.125627946  0.263070149  0.229751388 -0.730864754
##  [791]  0.229747504  0.088110288  0.191478958  0.295278841 -0.183867109
##  [796]  0.839071384  0.187955174 -0.047548783  1.695335031  0.262050000
##  [801]  0.671020574  0.374717842 -0.352518537 -0.001249689  0.198839352
##  [806] -0.382960910  0.863074881  0.518399918  0.035045436  0.429445082
##  [811]  1.140848611  0.142455920  0.995080780  0.110982956  0.867460441
##  [816]  0.258733712  0.433002234  0.396124263  0.878693309  0.467754904
##  [821]  0.895165600 -0.324259108 -0.247895494 -0.014328422  0.464549088
##  [826] -0.300764561  0.708435709  0.975346217  0.150781770  0.314767060
##  [831]  1.279137641  0.129137606  0.819589410 -0.152878114  1.063941575
##  [836]  0.337812946  0.594984243  0.302585025  0.702565185 -0.490394326
##  [841]  0.305188764  0.368038962  0.700797597  0.177977471  0.417306966
##  [846]  0.174568760  0.630406474  0.506488322  1.093460784  0.456199742
##  [851] -0.063957432  0.386285471  0.876426051  0.161855880  0.978740218
##  [856]  0.034667410  0.725151842  0.245749682  1.362427532  0.678661244
##  [861]  1.042968575  0.154993514 -0.268121799  0.844811883  0.269071535
##  [866]  0.317948424 -0.176643277  1.053244661 -0.081481130  1.213858272
##  [871]  0.829093217  0.441707089  0.486057364  0.379471141  1.030777654
##  [876]  0.217910799  0.406925285  0.152758415  0.038706562  0.660322169
##  [881]  0.578874785  1.629630642  1.234554323  0.267238452  0.442178953
##  [886] -0.061495610  0.765697832 -0.602498927  0.922075245  1.382072731
##  [891] -0.197485985  0.719903150  0.289456215  0.304331285  0.822579664
##  [896]  0.095441803  0.066005471  0.318983512  0.032006445 -0.096357755
##  [901]  0.587003329  0.040694649 -0.055503018 -0.272592600  1.748926134
##  [906] -0.591461469  0.139191664  0.297337546 -0.372888346  0.966996578
##  [911]  0.627476865  0.080906838  0.034987473  0.516532758  0.126607758
##  [916] -0.046513718  0.054777605  0.948929963  1.078957403  0.690043286
##  [921]  0.996061040 -0.158402986  0.034941603  0.913438232 -0.450945937
##  [926] -0.270509633 -0.948411884  1.109747861  0.586842492  0.094847435
##  [931]  0.665691046  0.203950385  0.998456822  0.641113310  0.829612323
##  [936]  0.095635527 -0.119586219  0.580649456  0.098901102  0.762302111
##  [941]  0.626615453  0.553310570  0.691003364 -0.129482491  0.270205240
##  [946]  0.743926858  0.843322211 -0.128422679  0.711650819  0.396876298
##  [951]  0.304967652  0.343395757  0.616576757  0.210169942 -0.496014917
##  [956] -0.032480140  1.511632769  0.379870853  0.748152022  1.091750984
##  [961]  0.079895850  0.184740267  0.314347640  0.488168785  0.241268911
##  [966]  0.046430260  0.323274119 -0.355493193 -0.111165264  0.908946209
##  [971]  0.499566158  0.679283624  0.303887037  0.382382121  0.072058631
##  [976]  1.333159614  0.402338305  0.099264942 -0.166733606  0.206885403
##  [981]  1.025872211  0.493309293  0.309999950  0.566708424  1.533644584
##  [986]  0.093147429  0.789388348  0.319184523  1.213118226  0.800778011
##  [991]  0.180640034  0.093899406 -0.081025337  0.399036564 -0.197268223
##  [996]  0.594193282  0.384145686 -0.030659690  0.305906046  0.060315934
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
##       mean          sd    
##   0.59528901   0.34220679 
##  (0.10821529) (0.07651556)
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
## [1] -0.6459573 -1.4410339 -0.5879511  0.3267801  1.3135050  0.2546404
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
## [1] 0.0152
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8960982
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
##     original     bias    std. error
## t1*      4.5 0.01861862     0.92819
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 5 6 8 9 
## 2 2 1 2 1 2
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
## [1] 0.0258
```

```r
se.boot
```

```
## [1] 0.9303762
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

