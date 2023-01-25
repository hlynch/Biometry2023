Week 2 Lab
=============

Confidence intervals
-----------------------

Before getting too far, we need to formally define a confidence interval. 

A 95th percentile confidence interval say “If I repeat this procedure 100 times using 100 different datasets, 95% of the time my confidence intervals will capture the true parameter”. It does NOT say that there is a 95% chance that the parameter is in the interval.

**Quiz time! (Don't worry, not a real quiz)**

*Important note*: This is an area where Aho is **WRONG**. Aho is correct on only one point. It is true that *once the 95th CI has been constructed*, it is no longer possible to assign a $\%$ to the probability that that CI contains the true value or not. Because that CI, once created, either DOES or DOES NOT contain the true value. However, we often talk about the interval in the abstract. **<span style="color: orangered;">When we say "There is a 95$\%$ chance that the interval contains the true value" what we mean is that there is a 95$\%$ probability that a CI created using that methodology would contain the true value.</span>**

Do not let Week 2 pass by without fundamentally understanding the interpretation of a confidence interval. 

So now we know the general idea behind confidence intervals but we don't yet know how to calculate them. To do that, we'll actually walk through an example of bootstrap using pennies. Each of you should have gathered the ages of ten pennies. (If a penny was made in 2021, that penny would be 1 year old, etc.)

*Data*: 10 pennies that the students have

*Test statistic*: Median

Lets say we are trying to find the median age of all pennies in circulation. We can't figure this out exactly, because we can't collect all the pennies in circulation, but we each have a sample of 10 pennies. The median age of the pennies in our sample is a reasonable estimate for the median age of all pennies in circulation. 

What is our uncertainty about that number? How far might our estimate of the median age be from the true median age? In this case, we don't know the underlying distribution of penny ages. (Let's brainstorm this for a bit. Do we have any guesses what this distribution might look like? What might be a reasonable distribution to describe the shape of penny age?) 

Let’s use bootstrapped samples to calculate the s.e. associated with that estimate.

Procedure: 
1. Sample WITH REPLACEMENT a group of 10 pennies. (To sample with replacement you will have to sample one penny, write down the age, and repeat that 10 times.)
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
## 0 1 3 4 5 6 8 9 
## 2 1 1 2 1 1 1 1
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
## [1] -0.052
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
## [1] 2.686983
```

```r
UL.boot
```

```
## [1] 6.209017
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.6975 6.2000
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
##    [1] 5.4 3.4 2.9 5.7 3.7 5.3 4.8 2.9 4.1 5.8 6.2 4.9 6.4 3.6 4.1 4.0 5.0 4.1
##   [19] 3.6 6.7 4.7 5.5 5.3 5.5 7.1 4.5 5.8 5.1 4.0 5.7 3.6 4.8 3.7 5.7 3.4 6.7
##   [37] 5.5 5.1 3.7 5.5 4.5 5.3 4.2 4.2 4.3 5.0 4.6 4.6 4.0 5.1 5.0 4.4 3.8 4.5
##   [55] 4.4 4.2 2.3 4.7 2.7 4.2 3.4 4.4 5.1 3.9 4.1 4.0 4.5 3.3 5.3 5.9 5.0 6.4
##   [73] 3.6 4.2 4.4 6.3 4.5 4.2 4.5 3.3 6.6 3.6 4.4 4.4 4.5 4.7 4.3 3.1 5.8 5.2
##   [91] 4.5 3.9 5.1 4.7 4.7 4.0 3.6 5.8 4.2 4.6 5.0 4.8 4.2 4.9 2.8 4.2 4.3 3.7
##  [109] 5.4 5.3 4.2 5.1 4.9 4.5 5.8 5.0 3.4 6.2 4.5 5.4 6.4 4.6 3.5 3.5 4.0 4.7
##  [127] 5.3 4.7 5.4 5.2 4.9 5.3 5.2 5.0 5.6 5.2 5.7 4.2 4.7 4.1 4.7 5.3 4.5 5.4
##  [145] 5.6 4.4 5.0 4.6 5.2 4.4 3.9 2.7 5.4 3.3 4.1 3.5 3.5 4.9 4.8 4.0 4.5 4.1
##  [163] 2.6 4.8 2.2 4.1 2.7 6.1 5.2 6.9 6.0 3.2 3.5 4.4 6.2 5.3 4.4 4.0 4.0 3.5
##  [181] 4.7 5.3 4.6 4.8 4.5 3.7 5.7 4.6 3.8 4.7 3.6 4.1 4.5 5.5 4.6 4.3 5.0 4.2
##  [199] 3.0 3.6 4.0 3.4 4.2 3.6 5.1 3.2 4.4 5.6 4.2 4.7 4.5 3.6 2.7 5.0 4.8 4.7
##  [217] 3.5 4.7 3.2 4.9 3.7 4.8 5.2 2.8 4.0 4.4 3.3 4.9 4.4 4.8 3.6 5.5 4.4 4.7
##  [235] 3.2 4.4 4.2 4.0 5.8 6.8 4.5 6.4 4.3 5.7 3.2 4.0 4.6 4.1 3.5 4.0 3.2 3.6
##  [253] 4.3 3.9 5.8 4.8 5.8 4.2 3.9 5.2 5.1 4.5 4.6 6.4 3.6 3.7 4.7 4.4 2.9 4.4
##  [271] 3.7 4.4 5.9 3.6 5.0 5.9 4.3 5.1 3.7 3.5 4.8 5.4 3.4 5.1 5.2 4.7 5.4 4.6
##  [289] 5.6 4.5 4.2 4.1 4.9 5.4 4.1 5.1 3.6 6.3 5.7 4.8 5.0 4.1 4.2 5.1 3.9 6.5
##  [307] 3.0 4.0 3.6 6.2 4.4 4.9 4.0 4.8 4.5 6.1 3.4 5.0 4.3 4.7 6.1 5.4 6.0 4.9
##  [325] 3.4 6.1 4.4 4.3 4.1 6.2 4.3 4.6 5.7 6.0 5.0 3.8 3.9 3.6 5.6 4.7 4.2 5.4
##  [343] 3.7 5.7 3.1 3.1 5.4 4.6 3.6 3.6 3.9 5.0 5.7 3.6 5.1 4.4 5.6 5.2 5.1 6.0
##  [361] 4.5 3.6 5.9 4.4 5.5 4.7 4.1 5.1 7.8 4.8 4.7 3.5 4.8 5.2 5.2 4.9 4.2 3.7
##  [379] 5.7 4.5 2.6 3.5 5.8 4.7 3.8 4.5 3.7 3.9 5.1 4.0 4.3 3.5 6.0 5.7 5.4 3.5
##  [397] 4.6 4.9 3.7 4.0 4.8 5.2 3.3 3.4 5.0 5.1 5.3 5.7 5.1 4.9 5.3 6.0 5.8 4.9
##  [415] 2.3 4.1 5.9 4.7 4.4 5.7 5.3 5.0 3.5 3.7 5.6 2.3 6.2 4.0 3.1 4.2 4.7 4.1
##  [433] 6.3 4.5 4.3 4.6 5.3 4.2 5.2 5.2 4.2 5.1 5.4 4.1 4.3 5.7 4.4 3.5 3.7 4.4
##  [451] 4.4 5.3 3.9 5.1 3.9 5.1 2.1 3.7 5.1 3.5 3.6 5.3 5.7 3.3 6.0 4.1 4.9 3.4
##  [469] 5.8 4.1 4.6 3.7 5.2 6.4 3.2 5.5 5.5 5.4 5.3 4.2 6.2 4.4 3.5 5.9 4.6 4.7
##  [487] 4.5 3.2 2.8 3.3 3.6 3.9 5.5 3.7 5.7 5.5 3.7 4.8 3.0 4.8 4.9 4.6 4.7 6.0
##  [505] 3.6 5.0 4.0 4.9 4.4 5.5 5.3 1.8 5.0 4.3 3.5 6.0 5.6 5.7 2.2 4.7 4.1 4.6
##  [523] 5.2 6.0 4.4 4.6 5.5 5.0 4.7 2.8 3.3 4.9 4.7 4.4 5.5 5.0 3.5 3.3 5.0 4.2
##  [541] 3.6 5.9 4.8 3.8 3.7 4.6 5.1 6.2 4.7 3.1 6.5 5.5 6.1 5.1 4.8 5.9 3.3 3.8
##  [559] 4.4 2.4 4.2 4.4 3.1 5.8 3.2 3.6 2.6 5.7 3.4 3.9 5.2 5.6 4.0 4.1 4.0 5.0
##  [577] 5.4 5.3 4.0 4.9 5.0 4.6 4.4 5.3 3.3 4.5 4.4 4.1 5.3 5.4 4.8 3.4 4.6 5.9
##  [595] 2.7 2.6 4.5 2.8 4.2 4.8 3.6 4.1 4.3 6.2 3.1 4.0 5.2 5.0 3.4 3.8 4.5 4.9
##  [613] 4.0 3.9 4.8 4.6 4.6 5.4 3.7 3.7 5.5 3.3 4.3 4.4 4.8 4.2 4.6 4.2 4.3 3.0
##  [631] 4.3 3.8 4.3 5.6 5.4 4.0 4.2 3.9 3.2 5.3 5.0 2.8 3.9 4.7 5.2 5.8 5.6 5.9
##  [649] 3.3 5.3 4.7 4.0 4.6 4.6 4.2 2.6 5.5 4.0 6.0 4.5 5.8 4.3 4.0 6.1 4.8 3.5
##  [667] 5.2 5.4 2.1 5.0 6.0 3.4 5.8 3.5 2.6 4.7 5.7 4.1 6.3 5.2 4.4 3.7 3.1 4.1
##  [685] 4.5 4.5 4.5 3.0 4.2 4.7 4.5 3.6 5.1 5.7 4.0 4.5 4.1 3.6 6.0 6.0 4.4 5.5
##  [703] 2.8 3.4 4.8 3.7 4.0 4.5 5.8 5.0 5.3 3.6 4.8 5.1 4.4 2.7 5.1 3.9 6.3 4.2
##  [721] 5.3 3.5 3.6 4.6 4.7 3.9 5.1 4.5 3.6 5.3 5.7 5.7 4.3 4.9 4.5 4.0 4.9 4.4
##  [739] 4.8 4.7 5.7 4.0 4.7 5.4 2.9 3.0 5.5 3.8 4.6 3.5 4.2 4.9 3.4 5.9 4.2 5.8
##  [757] 5.5 5.0 4.7 3.8 4.0 4.4 5.3 4.0 5.4 4.8 4.1 3.1 5.5 5.3 5.8 3.8 4.9 4.5
##  [775] 3.6 4.2 5.6 3.7 4.9 4.2 4.2 5.0 4.8 3.1 5.3 6.4 4.6 5.1 6.4 4.8 4.5 4.9
##  [793] 5.5 4.0 5.3 5.6 5.0 4.3 3.2 5.4 2.9 4.3 3.5 4.4 3.9 4.8 3.0 4.4 5.2 5.2
##  [811] 4.7 4.2 4.7 6.1 3.6 3.7 2.7 5.1 2.9 4.1 3.3 4.9 4.4 4.8 3.3 3.7 3.0 4.4
##  [829] 5.4 4.4 5.4 5.1 5.0 5.2 3.4 4.8 4.8 3.4 3.9 4.2 5.2 5.3 4.6 3.1 5.6 3.8
##  [847] 5.3 6.0 4.1 4.1 3.7 6.0 3.9 4.3 5.2 6.2 4.5 3.3 3.8 5.0 4.9 4.3 3.9 4.9
##  [865] 6.2 5.3 5.7 4.8 6.4 5.4 5.1 5.3 6.1 4.3 4.6 4.2 5.5 4.4 6.4 4.1 5.0 4.5
##  [883] 4.6 5.4 3.6 4.3 4.2 5.6 7.1 4.8 5.3 3.6 3.8 5.5 5.7 4.5 6.2 3.8 5.7 5.3
##  [901] 4.9 3.5 4.5 3.9 3.8 3.3 4.1 5.3 4.3 2.3 3.8 4.5 4.8 4.6 4.4 4.4 4.9 4.0
##  [919] 4.7 2.9 4.3 5.1 3.1 4.7 3.7 5.0 5.1 3.5 5.8 5.5 4.5 6.3 6.7 4.2 4.0 5.6
##  [937] 6.2 4.4 4.4 4.5 5.7 4.4 4.2 4.9 4.3 3.7 4.1 4.3 3.0 4.6 3.2 4.1 5.9 4.4
##  [955] 5.2 4.9 4.3 4.9 3.4 3.9 5.7 4.2 3.9 3.9 4.3 3.9 5.3 4.3 3.7 5.6 5.4 4.8
##  [973] 4.9 4.8 4.5 3.9 4.4 5.3 5.0 3.2 4.4 4.2 4.9 3.6 4.8 4.0 5.4 5.7 5.3 6.9
##  [991] 4.0 4.2 6.2 4.6 4.4 3.6 4.0 4.5 4.5 3.5
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
##  2.5% 97.5% 
##   2.8   6.3
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
##    [1] 3.6 5.5 4.0 5.1 6.0 4.2 5.4 6.0 3.5 3.9 4.2 3.7 4.9 3.6 4.5 6.0 4.9 4.5
##   [19] 4.6 5.0 3.9 3.6 3.6 5.1 3.9 3.4 6.2 4.4 4.8 5.1 3.8 4.5 3.4 4.5 4.1 3.0
##   [37] 4.4 3.9 5.3 4.5 4.4 4.1 3.7 5.4 4.4 6.2 4.3 5.0 3.5 4.4 3.9 4.9 3.6 3.6
##   [55] 3.7 4.7 4.5 4.2 4.6 4.4 5.5 2.9 3.2 4.8 2.7 5.5 5.0 3.4 3.1 3.8 4.1 4.3
##   [73] 5.3 4.0 3.8 5.2 4.0 5.8 3.2 5.7 2.4 5.1 4.6 4.5 5.7 5.6 4.1 3.9 4.3 5.5
##   [91] 5.5 4.0 4.7 4.9 5.1 4.7 3.2 5.3 4.7 4.1 6.0 5.2 5.4 5.4 5.2 5.9 4.0 5.1
##  [109] 4.0 4.6 4.6 5.3 4.1 5.6 5.2 4.8 4.9 5.0 5.2 5.2 5.1 2.8 5.5 5.5 4.7 5.2
##  [127] 4.7 4.8 4.4 4.2 6.0 4.3 5.3 3.5 4.7 5.7 4.3 4.7 4.2 2.6 2.4 5.7 5.6 5.5
##  [145] 4.1 4.8 3.2 4.9 4.5 4.4 4.3 3.2 4.6 4.4 3.6 4.9 5.7 6.8 3.9 5.7 3.7 3.9
##  [163] 5.3 3.6 2.8 3.1 4.8 5.2 4.1 5.0 4.7 5.1 5.7 2.9 7.4 3.9 4.3 5.9 5.2 5.8
##  [181] 3.0 4.6 4.8 4.4 2.6 5.3 6.3 5.2 5.4 4.8 5.1 3.9 4.7 4.8 3.8 4.4 4.3 3.4
##  [199] 3.5 4.1 5.0 3.7 3.6 5.0 4.6 4.0 6.1 4.3 6.2 4.7 4.9 4.1 5.7 3.9 3.6 3.6
##  [217] 3.9 2.9 4.0 5.4 5.1 5.2 4.1 4.5 5.4 4.0 4.2 4.8 3.0 4.6 4.8 3.7 3.4 2.5
##  [235] 4.2 3.8 3.5 4.9 3.3 6.7 4.8 4.8 3.6 6.2 3.9 3.6 4.3 4.1 2.4 3.1 5.2 4.2
##  [253] 4.7 6.4 2.6 3.7 4.8 4.4 5.5 3.5 3.9 4.8 6.4 4.3 3.5 6.4 4.6 5.8 4.2 2.8
##  [271] 4.6 5.2 5.9 5.3 6.1 5.4 4.5 3.3 5.4 3.9 4.5 5.5 5.0 4.1 4.3 5.3 4.6 5.5
##  [289] 4.3 4.9 4.5 2.4 4.4 5.9 5.6 6.1 4.3 4.0 5.4 4.2 5.7 5.5 6.4 3.2 4.3 4.3
##  [307] 3.1 5.2 5.7 2.6 3.5 5.0 3.8 4.8 3.8 5.7 3.4 5.5 4.3 3.7 4.0 4.7 4.4 4.9
##  [325] 5.2 4.2 5.5 6.8 4.1 4.3 3.0 5.0 6.1 5.4 3.4 3.6 2.8 3.9 3.6 4.4 4.5 4.0
##  [343] 4.4 2.2 7.3 5.5 3.8 5.0 5.1 3.9 6.0 3.2 4.2 4.4 3.8 4.2 4.8 4.9 5.5 4.2
##  [361] 5.1 4.9 4.8 4.1 5.4 4.6 3.6 5.2 4.1 5.1 4.5 5.9 4.3 4.9 4.0 5.1 4.2 5.2
##  [379] 5.2 6.1 5.1 4.6 2.9 5.4 4.4 3.9 3.5 4.5 4.5 4.9 4.4 4.0 5.1 4.7 6.0 5.6
##  [397] 4.0 3.6 4.7 6.2 3.6 4.6 4.4 5.1 3.8 4.1 4.9 4.5 4.2 6.3 3.4 5.9 4.8 4.3
##  [415] 3.2 6.3 5.1 5.3 2.6 5.6 4.3 5.2 4.7 5.8 4.2 3.2 3.9 3.9 5.1 2.1 3.2 4.4
##  [433] 6.6 4.9 5.3 4.2 5.2 3.6 2.2 3.6 4.2 3.5 4.0 3.5 4.0 5.1 4.9 4.2 5.1 3.8
##  [451] 4.1 4.0 5.0 4.2 5.0 4.3 4.1 4.5 4.3 6.0 5.9 4.5 4.4 4.6 5.1 4.9 3.9 4.1
##  [469] 5.9 5.3 3.6 4.4 4.4 3.9 4.6 5.4 3.3 4.9 3.9 3.5 4.6 4.3 4.1 4.5 4.4 5.0
##  [487] 2.9 3.6 6.5 4.0 4.6 5.8 3.2 3.9 3.6 5.6 4.3 4.3 6.0 5.1 3.8 3.9 4.7 4.7
##  [505] 5.5 3.8 5.0 3.7 5.3 3.4 4.8 3.6 5.5 5.4 4.5 3.9 6.2 3.7 3.7 5.8 5.2 3.7
##  [523] 3.4 4.2 4.6 3.5 3.6 4.4 5.3 4.9 3.9 4.5 4.6 2.9 5.3 3.6 5.0 4.9 4.9 4.8
##  [541] 4.7 3.3 4.1 4.8 4.2 5.2 4.7 5.1 2.9 4.2 3.6 5.5 4.5 3.8 5.6 5.2 4.6 4.8
##  [559] 3.3 4.7 5.4 4.5 4.2 5.9 5.7 4.1 4.5 4.1 7.1 4.2 4.5 4.0 4.1 4.6 4.5 5.4
##  [577] 4.3 4.0 3.4 4.0 4.9 5.3 5.2 3.0 4.2 5.4 4.9 3.7 5.4 3.8 5.9 4.1 6.5 4.0
##  [595] 2.5 6.7 4.0 5.2 4.2 5.5 2.8 4.9 3.6 4.3 4.1 3.9 5.4 4.1 4.1 1.5 5.0 4.2
##  [613] 5.4 5.8 4.0 5.1 5.4 5.5 4.8 5.0 3.9 4.0 4.1 5.4 4.7 3.6 3.3 4.0 4.2 3.3
##  [631] 5.0 3.9 5.0 3.2 5.2 5.0 4.2 2.7 3.5 5.7 4.4 5.4 3.0 4.4 5.7 4.2 5.8 5.7
##  [649] 4.2 4.6 3.1 4.3 4.5 4.3 4.3 4.8 4.2 3.7 5.1 3.6 3.5 5.8 5.1 4.6 5.3 5.0
##  [667] 5.3 4.8 4.7 5.8 3.6 5.5 4.4 5.0 5.5 4.3 4.8 6.0 5.6 3.7 4.3 4.1 3.2 4.0
##  [685] 4.0 6.2 5.6 4.3 4.1 5.1 5.4 4.2 4.0 3.8 4.4 4.1 4.9 4.0 3.4 4.7 4.2 3.9
##  [703] 4.4 4.7 4.0 4.6 4.2 4.4 5.3 3.3 3.1 5.4 4.1 4.6 5.6 4.8 4.0 5.9 5.3 5.1
##  [721] 4.8 5.1 4.6 6.3 4.5 2.9 5.7 5.0 4.0 6.0 4.6 3.7 4.2 4.7 5.3 4.4 5.6 3.6
##  [739] 5.4 4.6 4.0 4.6 4.7 4.9 3.4 2.8 4.0 3.3 5.0 5.2 4.8 4.4 5.0 3.9 4.7 4.3
##  [757] 4.3 2.5 5.4 4.7 3.7 5.3 5.3 3.6 6.1 4.8 5.0 3.3 4.2 4.4 5.0 5.0 4.3 6.2
##  [775] 4.0 4.7 4.8 5.2 2.5 3.1 3.9 3.1 2.9 4.0 5.0 5.2 4.5 4.9 6.1 3.8 4.0 4.2
##  [793] 2.4 4.3 4.1 4.2 5.6 5.8 3.5 3.2 4.8 5.4 5.6 4.7 4.5 5.1 5.2 4.4 4.5 5.8
##  [811] 4.8 4.0 4.9 4.9 3.3 4.5 6.4 3.7 4.0 6.2 4.6 4.0 5.4 4.3 3.2 6.5 3.7 4.7
##  [829] 4.0 2.2 4.0 5.9 3.5 3.8 5.6 4.3 4.0 4.8 5.6 5.6 1.6 4.1 4.2 3.0 5.6 4.7
##  [847] 4.7 4.6 5.9 6.5 5.5 5.1 3.9 4.2 5.0 3.7 5.7 5.6 4.8 7.2 5.9 6.7 4.0 2.4
##  [865] 3.4 4.8 4.2 5.7 6.0 5.2 3.2 3.7 4.7 5.3 6.2 4.6 3.9 5.3 5.0 5.3 4.5 4.9
##  [883] 5.3 5.6 5.4 4.2 4.0 6.1 5.3 4.7 4.4 4.5 4.4 4.0 4.8 5.5 3.0 4.9 4.7 3.6
##  [901] 4.3 4.5 5.4 5.4 4.8 4.8 3.8 5.4 4.6 5.4 5.1 5.4 5.4 5.8 4.6 2.3 6.5 4.3
##  [919] 3.9 5.1 4.1 3.1 4.2 4.8 3.5 5.4 5.5 4.1 4.0 4.6 5.9 4.0 4.3 4.5 4.0 5.9
##  [937] 4.0 3.8 3.6 5.1 3.4 7.4 4.2 2.4 4.0 4.6 4.3 5.0 4.0 4.7 4.8 4.8 4.2 4.5
##  [955] 3.1 5.1 4.8 4.6 4.1 5.8 5.7 5.9 3.4 4.4 5.1 2.6 4.1 6.6 6.1 6.8 3.1 4.4
##  [973] 3.9 3.8 5.5 3.2 4.1 3.4 4.7 5.8 2.0 4.5 5.7 5.9 2.9 5.2 4.2 3.9 5.5 3.3
##  [991] 6.2 5.1 3.9 4.7 5.6 4.7 5.8 5.8 6.3 2.8
## 
## $func.thetastar
## [1] 0.047
## 
## $jack.boot.val
##  [1]  0.52815249  0.49456522  0.36353591  0.16115942  0.16840580 -0.02479784
##  [7] -0.07288630 -0.22557471 -0.33484419 -0.42256098
## 
## $jack.boot.se
## [1] 0.9567971
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
##    [1] 4.3 3.7 3.5 4.2 5.0 4.2 4.9 4.8 4.6 4.7 5.4 5.4 3.3 5.6 4.9 4.3 4.8 4.4
##   [19] 3.9 3.7 3.2 4.3 4.4 3.4 4.7 4.4 2.5 3.5 7.0 5.1 3.9 4.1 6.3 4.3 6.1 3.0
##   [37] 4.7 4.0 3.9 5.0 5.0 6.4 5.5 3.3 5.4 4.2 3.9 5.2 5.2 5.4 5.0 3.0 6.1 3.5
##   [55] 3.8 4.0 5.6 5.8 5.4 4.4 6.2 5.5 3.2 4.7 4.1 5.8 4.6 4.8 3.0 5.5 4.9 5.1
##   [73] 4.8 5.5 3.3 4.0 4.5 4.5 4.6 5.5 5.0 3.4 3.5 5.6 3.5 3.3 5.3 5.7 3.3 4.1
##   [91] 4.3 4.1 3.6 4.1 3.8 5.4 4.6 4.5 4.7 4.7 4.2 3.2 3.2 4.4 4.1 5.3 3.0 6.6
##  [109] 4.6 5.4 4.7 3.9 3.4 5.4 4.2 4.0 5.5 4.0 2.1 4.8 4.3 4.7 3.1 3.0 3.4 6.2
##  [127] 5.7 4.2 3.2 4.2 6.1 4.2 5.2 6.4 4.9 5.2 5.4 2.1 3.3 4.6 4.4 5.0 4.9 2.6
##  [145] 3.9 3.8 2.8 4.2 4.7 5.9 3.7 4.8 5.3 3.8 6.6 6.0 4.7 5.0 5.4 4.9 4.5 3.7
##  [163] 3.0 5.5 3.5 3.8 5.5 4.0 3.0 5.1 5.1 2.6 4.0 4.6 3.3 5.7 4.3 2.6 4.4 4.6
##  [181] 3.9 4.0 4.2 3.6 4.1 4.4 4.7 5.2 4.1 2.3 3.7 5.6 5.5 4.2 3.8 5.4 5.6 4.7
##  [199] 4.6 5.5 4.7 5.4 4.2 2.5 6.2 4.7 5.7 4.3 4.2 4.0 4.3 3.8 5.9 4.1 3.8 3.8
##  [217] 5.0 4.3 4.4 4.4 4.1 6.5 4.4 4.5 6.8 3.7 6.6 4.5 5.2 4.3 3.8 4.2 3.2 4.0
##  [235] 4.2 5.9 4.9 5.2 3.4 4.0 4.8 4.5 4.3 4.8 4.6 4.2 3.3 4.1 5.6 3.5 5.3 4.6
##  [253] 1.9 4.5 5.3 5.4 4.7 4.6 4.0 3.4 3.8 5.8 6.2 4.0 4.1 6.7 4.9 3.6 4.3 6.0
##  [271] 4.5 4.6 4.7 4.9 5.0 4.7 5.8 4.5 4.8 5.3 4.6 5.0 3.7 3.7 3.7 2.9 3.0 4.4
##  [289] 2.5 4.5 4.6 4.6 3.7 4.7 4.7 5.2 5.1 2.9 3.6 5.5 4.7 5.5 5.5 5.2 4.7 5.5
##  [307] 6.2 4.6 4.4 5.2 4.8 4.4 3.6 3.7 3.8 4.2 2.8 3.6 5.1 4.2 4.6 4.7 4.0 4.9
##  [325] 5.6 3.2 3.9 4.5 3.9 3.3 3.7 5.5 5.0 6.2 2.8 4.7 3.2 5.5 4.4 5.7 3.7 5.2
##  [343] 5.2 4.0 4.5 4.1 4.0 3.5 3.3 5.7 5.8 3.8 5.6 4.2 4.6 2.8 4.1 4.7 5.8 5.4
##  [361] 5.1 4.5 4.0 2.5 4.1 4.7 3.8 4.8 4.0 4.4 4.9 3.8 5.2 4.5 3.2 4.3 5.6 3.8
##  [379] 4.2 4.4 4.0 4.3 3.6 4.9 5.2 4.0 5.7 5.2 5.7 3.2 3.3 4.2 3.8 3.0 5.7 4.5
##  [397] 2.5 4.6 4.4 3.8 5.5 4.8 4.9 5.8 6.0 5.2 5.0 4.9 4.0 4.1 4.6 4.9 5.0 3.7
##  [415] 3.5 3.4 3.8 4.9 4.8 5.3 4.6 3.0 5.0 5.1 2.3 5.9 4.2 4.2 4.8 5.8 4.6 5.3
##  [433] 4.3 6.7 4.4 3.6 4.1 5.0 4.8 2.9 3.2 3.9 3.3 5.1 5.3 4.4 4.7 5.8 4.8 5.9
##  [451] 4.2 5.6 3.6 4.5 5.0 5.5 3.7 3.9 3.8 3.7 4.9 5.7 5.2 4.9 5.4 4.0 4.7 4.3
##  [469] 5.4 3.3 3.7 5.1 4.8 4.3 4.5 3.3 4.4 4.3 4.3 5.2 4.6 4.1 4.5 4.2 5.0 5.3
##  [487] 2.7 5.5 6.6 5.0 5.8 3.2 3.8 5.1 4.9 5.6 5.1 4.6 4.7 5.0 6.1 2.4 2.6 4.8
##  [505] 5.2 4.3 4.2 5.3 4.5 5.9 5.9 5.6 5.2 6.2 6.1 3.9 3.3 3.5 3.9 5.2 5.7 3.9
##  [523] 3.9 3.9 5.1 4.2 5.2 3.6 2.7 4.5 6.3 4.9 3.9 3.9 3.2 3.5 5.1 5.1 4.7 5.1
##  [541] 2.9 5.1 4.0 4.2 5.4 6.3 4.4 4.1 5.4 4.8 5.3 5.6 5.3 5.1 4.1 3.2 4.2 4.1
##  [559] 4.9 4.0 4.8 4.9 4.5 4.1 4.9 3.9 4.3 3.7 3.3 4.8 4.3 5.0 5.5 3.5 4.0 5.6
##  [577] 5.6 3.5 5.5 4.3 4.1 6.2 4.2 3.7 4.9 5.7 5.9 5.1 5.3 5.1 3.1 5.4 5.2 5.5
##  [595] 4.9 4.0 4.9 5.1 5.7 5.0 4.8 5.0 5.0 4.6 4.3 4.5 5.5 3.4 3.0 4.5 5.2 4.6
##  [613] 1.9 5.4 3.9 4.6 2.7 5.3 4.0 5.0 4.3 4.9 4.7 4.1 4.2 5.1 4.5 4.9 4.5 4.1
##  [631] 4.5 3.9 4.4 3.2 4.8 3.2 3.6 4.5 4.9 3.9 4.3 4.1 6.3 6.0 4.0 4.1 5.6 5.6
##  [649] 4.3 4.6 4.3 3.9 3.9 2.6 5.7 5.1 3.8 5.0 3.7 3.6 4.4 5.0 6.0 5.2 3.5 5.3
##  [667] 4.9 4.0 3.3 4.8 4.4 3.4 4.6 3.4 3.3 4.7 3.4 4.6 6.2 3.1 2.8 2.9 4.7 2.6
##  [685] 4.5 3.9 4.3 4.7 3.5 7.2 5.1 2.6 4.6 3.6 4.7 3.7 3.5 4.6 3.3 4.2 3.7 4.3
##  [703] 5.7 5.0 3.5 6.2 3.8 5.1 3.0 4.3 4.9 4.2 5.2 4.7 4.4 4.4 2.1 4.8 5.0 5.3
##  [721] 4.8 4.6 2.7 3.6 3.4 4.8 5.1 4.7 3.0 4.1 5.0 3.9 4.0 4.0 4.6 4.6 6.4 3.6
##  [739] 5.7 4.2 4.5 4.5 5.1 3.9 5.0 5.1 3.0 4.9 4.9 4.1 5.0 4.0 6.4 3.0 3.9 5.7
##  [757] 4.7 5.7 3.2 4.7 3.7 4.5 3.7 5.8 4.6 4.2 6.1 4.3 6.8 3.9 4.2 4.7 4.2 5.2
##  [775] 4.9 5.1 5.1 6.9 3.4 3.7 5.7 4.9 4.1 3.5 3.5 5.7 3.8 4.4 3.0 5.2 3.8 2.6
##  [793] 2.2 4.9 3.4 3.0 4.3 6.7 5.9 4.8 3.8 3.8 4.2 4.1 5.4 2.8 6.8 3.7 2.3 3.5
##  [811] 5.5 4.4 3.7 4.5 4.2 5.1 5.1 5.3 4.7 3.9 5.1 6.3 4.6 4.4 5.5 6.6 5.6 4.5
##  [829] 4.3 5.3 4.0 4.8 4.7 4.0 4.6 4.3 4.0 4.2 3.0 5.1 4.2 4.1 4.0 3.6 4.5 4.7
##  [847] 3.6 3.8 3.1 5.1 4.3 5.0 4.3 4.2 4.2 5.0 4.6 4.8 2.9 3.9 5.0 3.2 6.5 3.9
##  [865] 4.6 5.3 5.8 4.0 4.5 3.4 5.4 5.5 3.4 4.9 3.7 5.2 4.2 4.7 4.4 5.0 4.3 4.7
##  [883] 3.1 3.6 3.8 5.5 5.5 3.8 2.0 5.7 4.1 4.0 3.4 4.3 3.6 5.1 4.5 3.5 3.2 3.4
##  [901] 3.7 5.2 3.9 6.2 4.8 3.3 5.5 3.5 5.0 4.1 4.4 3.8 5.3 4.6 3.9 4.0 4.0 4.3
##  [919] 4.2 3.9 5.3 4.0 3.3 4.1 4.0 5.4 5.6 6.1 3.9 3.7 2.9 4.4 3.4 4.0 5.5 4.4
##  [937] 4.5 5.8 5.1 4.7 3.1 4.5 4.0 3.4 4.6 3.7 4.1 5.1 4.3 5.5 3.5 4.1 5.2 3.3
##  [955] 3.6 4.0 4.9 4.0 4.2 6.0 3.7 3.5 4.8 4.5 5.5 4.3 4.7 5.0 3.4 4.3 5.8 3.9
##  [973] 3.6 4.8 3.7 4.7 4.0 4.6 5.3 3.8 2.8 6.0 5.1 5.1 4.2 4.6 6.0 5.8 5.6 2.6
##  [991] 5.2 3.9 5.4 3.3 4.9 4.7 3.2 4.5 4.2 4.7
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.4 5.4 5.3 5.1 5.1 4.9 4.8 4.8 4.5 4.5
## 
## $jack.boot.se
## [1] 0.9562426
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
## [1] 0.8599888
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
##     shape       rate  
##   3.030037   4.423967 
##  (1.287469) (2.044354)
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
## [1] 0.6038748 0.2235446 0.2818220 1.4701298 0.1978242 1.3791906
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
##    [1]  1.0673979057  0.7852335511  1.1241564143  1.2565422268 -0.2149757415
##    [6] -0.2572131920  1.1012913127  0.0775094673  1.4718084072  0.6783798025
##   [11]  0.9574164505  1.2103249321  0.6258113848  1.1232689171 -0.4098209877
##   [16]  1.3014465419  0.5515949922  0.3578653749  0.3749320931 -0.0801486470
##   [21]  1.2136710985 -0.7746756912  1.3917994311  0.4029762711 -0.0500271642
##   [26]  0.9515677668  1.2682666831  0.0873659116  0.7883174407  0.6893192303
##   [31]  1.1865357284  0.5009854834 -0.3167697018  1.2111565456  0.4969534075
##   [36]  0.6974766103  0.8554261298  0.6073449408 -0.0027703391  0.7024953188
##   [41]  0.2666725722 -0.4269515156  0.8931771883  1.1756389225 -0.0224702725
##   [46]  0.3367992505  0.9476644130  0.6559605116  0.6913614521  0.7686189003
##   [51]  1.0712019203  0.7234115898 -0.0156300253  0.2471651683  0.1583044892
##   [56]  0.1928736678  0.8164423919  0.2106613762  0.9964530511 -0.0632475093
##   [61]  0.4599146054  0.2346664525  0.6548663591  0.8786154486  1.3557556190
##   [66]  0.1568784737  0.8124927483  0.5580207558  0.3101389618  1.4024251427
##   [71] -0.8213658365  0.6817666538  1.0444998970  0.4034824038  0.2494125664
##   [76]  1.1136912443  1.0640253217  1.0509128650  0.6040794259 -0.1202405886
##   [81]  1.2802976828  0.0434369695  1.3967875997  0.4767855631  0.7756111522
##   [86]  0.8933806770  0.6888543098  0.5053161455  0.8602133317  0.7225662649
##   [91]  0.4734186924  0.5967651526  0.5338409438  0.6688856883  0.1182134620
##   [96]  0.3548972562  0.7691900452  0.6203946629  0.7523219450  0.2474631731
##  [101]  0.3737243321  0.4586531287  1.1386694293  0.6271793299 -0.0801486470
##  [106]  0.1046607842 -0.2771312526  0.1421609862  0.7963602362  0.3702838202
##  [111]  0.3441062573  0.6242458559  0.6392695170  0.7458775891  0.8019791708
##  [116]  0.5066453189  0.7585048331  0.1187767057  0.8656171790  1.3178703504
##  [121]  0.7824345550  1.1764290780  0.3218593537  0.1522056372  1.3586179972
##  [126]  0.6183325939  0.4862004834  0.9353482117  0.8250162904  0.9800704561
##  [131]  1.5505368445  0.9184090607  0.5917766931  0.4468633543  0.7583143075
##  [136] -0.0886531270  0.4004588875  0.7054286944  2.1894067905  0.9159112028
##  [141]  0.2153750888  0.6245352528  0.7401992381 -0.0547517981  0.8937377146
##  [146]  0.4350931235  0.7557232179  0.4065682932  0.1600523783 -0.3569131783
##  [151] -0.2039175832 -0.1282020634  0.7239839184  0.2237104518  0.8764318128
##  [156]  0.4246744231  0.2214983048  0.9829299464 -1.5477366354  0.5387621192
##  [161]  1.2598703148  1.0628001426 -0.1221243145 -0.2495006458  1.4808464681
##  [166]  0.1914048759  0.5479943594  0.1016951879  0.4394728362  0.6756244370
##  [171]  0.2230263854  1.4935055282  0.8899879112  0.7831558169  1.2372309170
##  [176] -0.1265570156  0.9626752261 -1.2718498619 -0.0078630614  0.9479668449
##  [181]  1.1117524128  1.4565186095  1.3597458516  1.0157048421 -0.3659625427
##  [186]  1.0244996833  0.4641499859  0.7474579314  1.2481609550  1.4001741093
##  [191]  1.7475387590  0.6489576436  1.2368827958  0.7572993408 -0.1088849313
##  [196]  1.6129686722  0.4181823774  1.3003622592  0.8689504076  1.4241702149
##  [201]  0.1559142709  0.8616472741  1.4263777386  0.2061659064  0.1284205113
##  [206]  0.4030667807  1.2215684272  0.9057717118  1.0510708096  1.7641986926
##  [211]  1.3443291005  0.4865398637  1.7781511325  0.7688053158  0.4197505808
##  [216]  0.0778315201 -0.1120653232 -0.4490086714  1.4398870562  0.3089504778
##  [221]  1.3145380152  0.1699646686  0.4744787402  0.2377778810  0.7184671552
##  [226] -0.6529353820  0.7312984675  1.3905026792  0.9224333244  0.4543301099
##  [231]  0.4410092921  0.9378759309  0.0508240625  0.7242134976  0.4798766310
##  [236] -0.1699426384  1.0885430189  0.7997926733 -0.3718827163  0.5522727897
##  [241]  1.5147965857  0.8068779563  0.2307083499 -0.2651766845  0.2219999696
##  [246]  0.9856163555  1.6045453574  0.9519034180  1.1697376665  0.9676036268
##  [251]  1.0773279727  0.2414259764  0.6582226236 -0.1633480193  0.9180729029
##  [256]  0.1682713263  1.3172189859 -0.0375630333  1.6069639984  0.3360854616
##  [261]  1.2234776667  1.0172207406  0.4760268140  1.5027351267  0.3322334762
##  [266]  0.9605722393  1.2301421256  0.1609044712  1.3145380152 -0.0233031435
##  [271]  0.2278795760  1.3575675658  1.4429270549  0.8137867287  0.3251932224
##  [276]  0.2995371247  0.6087415113  1.1048544252  1.2026234587  0.0661162964
##  [281]  1.3420169010  0.6547301857  0.6305790600  0.5053220349  1.3752987706
##  [286]  0.6716328681  0.7619228356  0.5030158072  0.9025126010  0.8718331008
##  [291]  0.7334505838 -0.1029156644 -0.4359228313  1.0678076815  0.8154453551
##  [296]  0.9471492077 -0.3023647837  0.8599888042  0.7922921160  0.7231156764
##  [301]  1.6971324833  0.5673487003  0.4210532543  0.0265883447  0.2626355075
##  [306]  1.1310639195 -0.1424977552  0.7814946913  0.2621074672  1.0388197551
##  [311]  0.6132704257  0.5056515311  0.9238098342  0.3360994283  0.7181327916
##  [316]  0.8301960082  0.7471609268  0.5262205373  1.0209888408  0.8363806602
##  [321]  0.8138216938  0.9363912655  0.3272196388  1.0471226816  0.1304622523
##  [326]  0.4135325176  1.1614049145  0.4546953798  1.0863676790  1.4541087675
##  [331]  0.3375100881  0.2270595046  0.9421512133  0.4066114942  1.0248808699
##  [336] -0.0617461345  1.2011149714  1.4310163975  0.8885368638  0.1546545489
##  [341]  0.2344636914  0.4977598055  1.9090643608  1.0133185906  0.2070379139
##  [346]  0.2249534998 -0.0224177582  0.8479033398  1.0775733917  0.6994333703
##  [351]  0.3900286005  0.8821413827  0.4317835529  1.5576938984  0.4481989301
##  [356]  1.7829212167  0.2504060393  1.2948369202  0.9734526488  0.1651076427
##  [361]  0.8658859642 -0.4408459681  0.5481375670  0.7483338113  0.1664796286
##  [366]  1.0026644304  1.0333826136  0.0659335676  0.8312553296  0.5944961514
##  [371]  0.0597803440  1.1261733465 -0.7352458814 -0.0349234505  0.9315288731
##  [376] -0.4989812098  2.2201823533  1.3684013561  0.8231057557  1.5529315781
##  [381]  1.0553504793  1.1109590988 -0.1127877331  0.9350306827  0.9705651689
##  [386]  1.5303463920 -0.1183986858  1.2962339851  0.7358193646  1.8070294981
##  [391]  1.2634433842  0.9637108734  0.2273102286  0.1067073373  1.0637276794
##  [396]  0.7909422807  1.2893728234  1.0179339741  1.1284152265  1.2748699420
##  [401]  0.6157635852  0.7203564082  0.4882910591  1.1914851646  1.0233283641
##  [406]  0.7653646766  0.9406602526  0.2754059797  0.0384761667  0.8451797646
##  [411] -0.1696138825  0.6627221354  0.5610891894  2.1336863177  0.1475446980
##  [416]  0.9180326493  1.5669005535  0.5945455300  0.9561272781  1.1262658996
##  [421]  1.1716644352  0.4604036794  0.7260402746 -0.0916333427  0.5416972342
##  [426]  1.4824871427  0.9395531697 -0.0485634500  0.4466055676  1.1208863072
##  [431]  1.7312363628  0.4965956485  0.0016326981 -0.2157002848 -0.7930753845
##  [436]  1.0998917670  0.8003166124  0.6404756128  1.1020154796  0.5532156124
##  [441]  0.0228560428  0.4414859746  0.5458705138  0.9992638116  1.0053898995
##  [446]  0.7115674515  0.7825752937 -0.1435191453  0.2907812200  0.2648935628
##  [451]  0.8101012825  0.4830705013  0.1668129508  0.6235537054  0.2555487530
##  [456]  1.4982064229  0.2670550087  1.3928594543 -0.0227597817  0.3488744265
##  [461]  2.0751858185  1.2184520664  0.3306537003  0.1397006514  0.7430532181
##  [466]  0.0254893991  0.8377299205  0.5220167237  0.3252985600 -0.4590529509
##  [471]  1.1349140596  1.9226402291  0.9948715293 -0.0021927153  1.1911123785
##  [476]  0.6100978381  1.7508350385  1.9901529797  1.3975398761  1.1407879464
##  [481]  1.0148887873  1.2238286423  1.1068862970  0.8927853889  0.0622000492
##  [486]  0.0808250454  0.5790277998  0.9036027976  0.7390380745  1.2808034728
##  [491] -0.2764007884  1.0365207254  1.0369643982  0.4640981207  0.5500282503
##  [496] -0.1320646741  1.0951756716  0.3406554938  0.9251786667  1.1523144169
##  [501] -1.2058311488  0.3194195682  0.5317651279 -0.5656187117  0.5591687315
##  [506]  0.7896804116  0.0243591522  1.1759621882 -0.2513749637  0.6759610877
##  [511] -0.2178510547  0.1806216272  0.8654229944  1.1356293423  0.8692285982
##  [516]  0.3460625738  0.9570074549  1.0933569393  0.3149421117  0.0686080748
##  [521] -0.5732442955  0.1576205352  0.1552538819  0.3744381801  1.4372786348
##  [526]  0.4235819845  0.9420138942  0.4488392374  0.1938129372  1.0790050795
##  [531]  1.7635288264 -0.1806687712  0.2007687483  1.4685790742  0.7355060497
##  [536]  0.3131146659  1.6990739789 -0.0451932478  1.2145448574  1.3310047089
##  [541]  1.3678405286  1.0145813416  0.9092588561  0.7357055036  0.5412968211
##  [546]  0.3462771047 -0.1120432729  0.5542457966  1.2850756597 -0.0594865667
##  [551]  1.3445432346  0.8369262015  0.3740057330  0.6980038933 -0.3857445072
##  [556]  1.8720672743  0.1686503198  1.5578117997  1.6846070416  0.4066985073
##  [561]  1.2488904414  1.6116769183  0.9348034531  0.2559081845  1.5305970915
##  [566] -0.8317702261  1.1891638324  0.3090384084  0.0100418726  1.4404883799
##  [571]  0.1672280881  0.8114931071  0.2829591939  0.7382086180  0.6697033429
##  [576] -0.1975131107 -0.0895500715  0.5155000835  0.1004318735  0.7890953992
##  [581]  0.9972125838 -1.0056621383  0.3268054266  0.4505451580  0.0470544216
##  [586]  0.4424429896 -0.6388013464  0.3195550093 -0.3145435225  0.5720534935
##  [591]  1.0505720255  0.5054502569  0.3132219712 -0.5916597822 -0.0476350581
##  [596]  0.1849468435  1.2109010294  0.5988598673  1.0610767673  1.0616633646
##  [601] -0.0003635699  0.3786249047  0.1587343923  0.3782020335 -0.3431867137
##  [606]  0.4714459237  0.4208484853  0.3382874207 -0.2718645953  1.2995272928
##  [611] -0.0770141781  0.2107478713  1.4544584519  0.5323752248  0.9321036123
##  [616]  0.8302496121  0.5058768037 -1.3446240209  0.0761794458  0.9785201689
##  [621]  1.1314835073  0.4592647285  1.3636922157  0.5758724933  1.1953810778
##  [626] -0.5642793015  0.5550482060  0.4893950207  0.8226975928  0.4255212257
##  [631]  1.0719106388 -0.9230343430 -0.6346291547  0.0851326385  0.3702892205
##  [636]  0.0559905684  0.3067448688  1.2679775971  1.2153814270  1.4274119311
##  [641]  0.6190504710  1.4914319656  1.2128435856  0.1995181061  0.9940978535
##  [646]  0.8599888042  0.8932692476  0.5545044146 -0.0006101196  0.0765764629
##  [651]  0.6164202619  0.8802716479  1.1262012439  0.4641499859  0.8112303364
##  [656]  1.4054711618  0.0699087948  1.5147706763  1.0233195081 -1.0741983152
##  [661]  0.9324700474  1.3145380152  0.1621489126  0.5445490638  0.7507827238
##  [666]  0.4008342248  0.8015719130  0.1437973725  1.0572986838  1.4112770343
##  [671]  0.3203127212  1.4173849450  0.8580055200  0.8424361258  0.6888671425
##  [676]  0.0040484842 -0.0928645764  0.5825085836  0.4777049782  0.2061644957
##  [681] -0.6233322217  1.6982205104  0.7026479925  0.2074239560  1.1166031743
##  [686]  1.4853448674  1.4126066761 -0.3269269563  1.9699215436  1.3550099467
##  [691]  0.3863569373  0.0612534945  0.6650339058 -0.6748097551  0.9095160092
##  [696]  0.5338505722  0.7236842198  0.9608715802  0.6197359360  0.7379383058
##  [701]  0.6233109966  0.3871604334  0.5983542074 -0.0094519155  1.4266841764
##  [706]  1.3120288298  1.5428137268  0.9124490341  0.4573054912  0.7862205472
##  [711]  0.5558796344  0.3265151217  1.0216398486  0.5341925334  1.5613737238
##  [716]  1.2527228602  0.0672172322 -0.1499096971  0.7514622385  0.0856006800
##  [721]  1.1555509582  0.2932270868  0.7345965842  0.0958987767  0.6938282659
##  [726]  0.0743913635  1.4518976098  0.7728439095 -0.7755110206  0.3849390423
##  [731]  0.8154453551  0.7012235751  0.5212533599  0.9900405757  0.8692285982
##  [736]  0.7073683841  0.3287298975  1.1863117876 -0.0676306911  0.6528048537
##  [741]  0.4937458243  0.1710942712  0.0437415671  1.1839477247  0.5346599920
##  [746]  0.7706604432  0.8122659038  0.7694863980  0.5560457013  0.1562584440
##  [751]  1.6191043682  0.3027122572  0.7168597521  1.2867453255  1.0184072424
##  [756]  0.7200424594  1.2871746715 -0.8609036184  1.4094717594  1.1787966994
##  [761]  0.5507902739  0.8124847056  0.6944168233 -0.0069740181  1.1242687124
##  [766]  1.9742806888  0.5311223563  1.1305740150  0.2449409258  0.5994680252
##  [771]  0.1640785895  1.3818701891  0.9151134433  0.4411039319  0.5284771289
##  [776]  1.2067926049  0.1075792827  0.5407774685  1.6925612780  0.1364386934
##  [781]  0.7257172974  0.4513971778 -0.0009178326  0.5378561175 -0.6345096549
##  [786] -0.0295691201  0.2494160082  0.5631979266  1.2910488472  0.7955153048
##  [791]  1.0599345799  0.9564210739  0.0367540700  0.4300310573  0.6117413228
##  [796]  1.1505676574  1.0942119459 -0.1244920378  0.8325451516  0.7813061040
##  [801] -0.1401717202  0.8684170775  0.4520062152  0.4150922585  0.6449065270
##  [806]  0.2773563054  0.1929984614  1.0807330190  0.1730059870  0.6585211650
##  [811]  0.9068913831  0.7911434068  0.5562518465  1.9762249664  1.0768432676
##  [816]  0.2400149970  0.8723367200  0.6182908890  0.6715551373  0.1855062410
##  [821]  0.4278468934  1.7870427310  0.9573561344  1.0098784644 -0.1001015696
##  [826]  0.4906737515  1.0981213048  0.5891957349  0.7122780228  0.6539986827
##  [831]  0.3470355997  0.1322283951  0.3167303302  1.8982778784 -0.0149899774
##  [836]  0.1708377905  0.4049287242 -0.4485894303  0.5151646436 -0.1449116819
##  [841]  0.0728353981 -0.3679208442  0.8582258143  1.1824142100  0.6223583717
##  [846]  0.8466063472  0.5500282503  0.5374528906  0.4463774954  0.7483772275
##  [851]  0.4203462175  0.1063908412 -0.7315706701  0.8292856227  0.4733092293
##  [856]  0.2993351919  1.8820962557  0.4293629882  1.1081331407  1.2816021428
##  [861] -0.2481589968  1.0548256232  0.7964408009  0.4411777882  1.1868743989
##  [866]  0.1366977806 -0.8598390354  0.4892774400  0.5289365380  0.3030844514
##  [871]  0.9157976586  0.6984994963  0.3219117616  0.9848806152  0.8302496121
##  [876]  0.4266453988  0.2682215747  0.8528737238  1.1844182778  0.5408600444
##  [881]  1.2815081586  0.5506863375  0.2272661911  0.9596498091  1.2254138700
##  [886]  0.2326409492  0.7484767939  0.0336957443  1.2089930817  0.8774688250
##  [891] -0.2027326808  0.6247062725  1.3999420067  0.8349052606  1.7528835851
##  [896]  0.3540096643 -0.2153721821  0.1489442653  1.1555157811  0.2005974509
##  [901] -0.7190550608  0.0466260494  1.7793439764  0.8881427112  0.3348666939
##  [906]  1.0102311719  1.0598728968  1.0616633646  0.8063243449  0.0573321589
##  [911]  0.8151354857  1.1522783585  1.4784708062  0.5980601958  0.5019491740
##  [916]  1.0877488216  1.3434321709  1.5370754420  0.5723516689  0.2258110672
##  [921]  1.0616633646 -0.7569329158  0.4477759911  0.5770702078  0.0877761701
##  [926]  0.2125702015  0.6046603830  0.3467953771  1.2123908070  0.7576611501
##  [931]  0.6663247412 -0.2071163436  0.2186599083 -0.1833553854  0.3075889598
##  [936]  0.9499040107  0.8771355637 -0.3870439347  0.8538760422  0.5927810973
##  [941]  0.1140159887  0.1991568299  1.1995685803  1.4057773926 -0.0377538231
##  [946]  0.6068864298  0.4509873818  1.0939324541  0.4061341884  0.3923256283
##  [951]  0.0738050922  0.0803559760  0.5959198954  0.6826664946  0.0200378779
##  [956]  0.0861998630  0.8377299205 -1.1636937069  0.3949237052  0.9842220524
##  [961]  0.9485348111  1.1650287809  1.5949478902  1.4164272617  0.6853269045
##  [966]  0.5216362997  0.2792511193 -0.7737357657  0.3549843652  0.7730210297
##  [971]  1.1066071634  1.5039546492  0.8240400718  0.3918941382  0.6888786539
##  [976]  0.9460969945  1.9476256998  1.1740957184  0.2768012835  0.9747656360
##  [981]  0.5315391848  1.1302917349  0.6638755529  0.4441817482  1.2112803260
##  [986]  0.4038880193  0.9140852207  0.4064799366  0.9998535484  1.4363935547
##  [991]  0.8502007400 -0.2361441160  0.3640601096  1.1196020531 -0.6021434083
##  [996]  1.2717907981  0.7478935958 -0.2182357957  0.2380389338  0.3892885177
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
##   0.68491070   0.38537643 
##  (0.12186673) (0.08617006)
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
## [1] -0.3709122  0.4534612  0.1905138  0.2563948 -0.3700746  0.4661843
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
## [1] -0.0507
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9287638
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
## t1*      4.5 -0.01581582   0.9596207
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 4 7 8 9 
## 2 1 3 1 1 2
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
## [1] -0.0687
```

```r
se.boot
```

```
## [1] 0.9277991
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

