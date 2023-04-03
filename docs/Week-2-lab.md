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
## 0 4 5 6 7 8 9 
## 1 1 1 1 3 2 1
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
## [1] 0.0427
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
## [1] 2.782282
```

```r
UL.boot
```

```
## [1] 6.303118
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.3
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
##    [1] 4.1 4.2 3.1 4.0 3.2 2.8 4.0 3.4 4.1 4.7 4.6 5.1 4.3 3.8 5.1 3.8 4.2 5.9
##   [19] 4.5 3.6 4.3 5.6 4.1 4.8 4.0 3.8 4.6 3.5 2.8 4.0 5.9 4.0 4.7 4.9 4.1 5.6
##   [37] 4.5 4.5 4.8 2.5 4.1 4.2 5.8 5.7 3.2 4.2 4.6 4.6 4.2 5.1 4.9 3.6 2.9 2.6
##   [55] 4.2 2.8 5.2 4.5 5.3 3.7 3.8 5.1 3.2 5.5 4.1 4.6 4.5 4.2 2.8 6.0 3.0 4.9
##   [73] 5.6 3.5 4.1 4.7 5.4 4.8 4.1 4.3 3.8 3.5 3.5 5.5 4.1 3.8 3.6 3.0 3.7 4.6
##   [91] 4.3 5.1 6.2 4.2 5.7 4.2 5.1 3.3 3.2 4.2 4.6 4.2 3.9 6.4 4.9 5.1 5.1 4.7
##  [109] 5.8 5.2 3.9 5.6 3.6 4.9 3.6 3.1 4.7 5.3 2.9 5.2 5.9 4.4 5.1 4.1 4.8 6.5
##  [127] 5.1 4.5 2.8 4.7 3.9 4.8 4.6 5.5 3.8 3.6 4.7 2.9 4.7 5.5 4.2 3.5 5.7 3.3
##  [145] 4.6 6.2 4.0 5.9 4.0 5.4 5.7 5.4 5.0 6.1 4.4 3.9 4.7 3.8 4.9 4.0 4.2 4.3
##  [163] 4.3 5.7 4.1 4.6 3.9 6.6 5.4 4.5 3.3 3.5 3.8 4.3 4.0 3.3 3.8 4.2 5.0 3.9
##  [181] 4.3 5.1 3.9 3.5 5.1 2.8 4.1 4.6 3.2 5.9 5.0 4.1 4.0 3.5 4.0 5.3 2.9 5.2
##  [199] 4.7 4.2 2.9 3.8 4.3 5.4 4.7 4.0 5.3 3.2 5.2 5.5 4.9 4.6 2.4 4.5 4.6 4.7
##  [217] 4.1 5.0 1.9 4.6 3.3 4.3 3.9 5.5 6.3 4.2 5.0 2.5 5.1 5.4 3.7 3.1 4.3 4.1
##  [235] 3.2 5.2 3.5 4.1 2.9 5.3 4.5 5.5 4.6 4.4 5.7 3.5 3.1 3.3 4.7 5.2 4.0 5.1
##  [253] 4.6 5.3 4.2 4.1 5.3 4.2 6.8 4.0 4.6 6.3 4.7 4.1 5.4 4.5 4.9 3.5 5.7 5.3
##  [271] 4.0 5.3 3.5 2.9 4.2 3.4 4.6 4.0 5.4 5.5 2.9 4.3 3.4 4.6 3.2 4.2 4.0 4.6
##  [289] 3.7 4.4 5.2 4.2 4.0 5.4 4.1 5.0 6.1 4.3 5.9 4.6 4.4 2.9 3.6 4.6 4.5 4.6
##  [307] 5.0 5.5 4.5 4.2 4.5 2.6 3.8 5.2 4.6 6.3 4.2 5.1 5.2 5.1 4.7 4.4 4.6 4.8
##  [325] 2.9 3.9 3.9 5.6 4.0 6.4 5.3 3.6 2.8 3.7 4.1 4.7 3.9 3.4 4.6 6.7 4.2 3.6
##  [343] 4.6 5.4 4.0 3.7 3.1 5.9 4.9 3.3 4.3 5.0 3.9 4.5 4.3 3.3 5.0 5.2 4.9 4.5
##  [361] 6.1 3.2 6.2 4.9 3.4 5.6 4.4 6.5 3.6 4.9 4.5 4.3 4.3 5.3 4.1 3.4 3.5 5.6
##  [379] 4.4 4.1 2.9 4.0 6.1 4.4 4.4 4.3 5.3 6.7 6.6 4.6 2.8 4.9 4.7 5.5 4.6 4.9
##  [397] 4.1 6.1 3.3 4.0 5.4 5.5 4.7 3.9 3.7 5.1 5.2 4.8 4.5 4.1 5.2 4.6 5.1 3.5
##  [415] 4.1 5.0 3.9 4.4 4.3 6.8 5.7 4.5 4.9 4.0 4.3 3.7 5.4 5.5 4.7 7.5 4.3 4.5
##  [433] 3.0 4.5 4.5 4.4 6.2 5.1 5.3 5.3 4.3 3.9 6.0 5.7 5.5 3.9 5.7 4.1 4.2 3.9
##  [451] 5.0 4.5 3.6 5.5 3.1 4.8 4.2 4.4 4.6 4.2 3.4 3.6 5.0 2.9 4.6 4.6 5.5 4.8
##  [469] 4.0 3.4 4.6 4.4 4.1 4.1 5.3 4.5 5.4 4.6 4.1 4.2 4.9 4.8 5.6 3.4 6.3 4.0
##  [487] 4.4 4.7 5.7 3.6 4.4 5.0 4.6 2.7 4.5 4.0 3.6 5.2 3.9 3.6 2.9 5.6 2.6 5.8
##  [505] 4.0 5.1 4.5 4.2 4.6 3.6 5.7 5.6 3.3 4.8 4.6 4.1 4.5 6.8 4.4 5.3 5.7 4.9
##  [523] 5.1 5.1 4.8 4.4 3.8 4.2 4.3 4.9 4.2 4.6 5.2 5.1 2.9 5.5 4.1 4.8 4.7 3.6
##  [541] 4.0 5.4 5.2 2.9 2.6 5.8 4.0 4.7 4.9 3.5 4.2 4.7 4.4 5.3 5.0 3.9 5.2 4.4
##  [559] 4.4 5.5 3.9 5.0 4.8 5.8 4.1 3.9 3.8 4.3 3.2 5.6 4.6 3.8 3.5 5.3 5.6 6.2
##  [577] 3.0 5.3 4.5 4.0 5.1 2.9 6.0 4.2 4.9 5.1 4.6 5.0 4.4 4.5 5.0 4.1 4.8 5.6
##  [595] 3.0 3.7 5.3 4.7 4.3 3.9 5.1 5.3 4.1 5.3 4.7 3.4 5.8 4.9 4.2 5.6 6.3 4.4
##  [613] 5.2 5.4 4.9 3.0 4.4 2.2 5.6 5.8 5.5 3.8 5.8 4.4 3.7 5.1 3.0 4.3 4.6 4.4
##  [631] 5.0 4.8 6.8 4.2 2.0 5.0 5.5 5.4 5.1 3.5 5.0 2.8 2.8 3.1 3.9 4.7 2.4 4.5
##  [649] 4.6 3.6 4.6 5.8 4.6 3.5 4.2 4.8 5.5 5.0 6.1 4.2 5.6 4.1 5.6 3.9 2.5 3.8
##  [667] 4.0 5.7 5.4 6.0 4.5 2.8 3.5 3.8 1.2 4.5 4.9 4.1 5.7 4.7 4.9 5.8 4.6 5.1
##  [685] 4.7 4.4 4.0 4.5 4.7 4.7 5.4 3.3 5.1 4.6 6.0 5.8 4.4 6.3 3.8 3.8 2.7 3.7
##  [703] 5.6 5.6 3.4 5.3 4.2 3.8 3.8 5.0 3.7 3.4 3.7 5.1 3.4 4.3 4.2 4.2 6.2 3.6
##  [721] 3.6 4.8 4.4 5.0 4.2 5.2 4.5 4.0 4.0 5.2 5.4 5.9 4.9 3.3 4.3 5.7 4.6 4.8
##  [739] 4.6 4.6 5.2 3.8 4.7 5.9 4.6 5.8 3.8 5.3 5.2 2.7 5.2 3.9 5.1 3.9 6.2 5.8
##  [757] 3.3 4.2 3.9 4.8 4.3 4.7 5.0 7.1 5.5 4.0 3.1 3.2 5.8 5.2 5.2 2.8 3.7 4.1
##  [775] 3.4 5.5 4.0 5.3 4.1 3.9 4.3 4.7 4.5 4.5 3.4 4.3 3.8 2.8 5.2 4.2 3.4 5.0
##  [793] 5.3 4.3 5.3 3.7 4.8 3.9 3.7 4.5 5.7 4.4 5.8 3.9 4.1 2.9 3.7 4.4 6.0 3.4
##  [811] 4.2 3.1 5.3 6.0 3.9 4.4 4.5 2.8 6.0 4.9 2.9 5.0 3.9 3.2 5.8 5.4 4.4 4.5
##  [829] 6.0 3.8 4.6 3.1 4.4 2.5 4.4 4.5 6.4 3.6 4.5 3.6 6.1 5.5 4.2 5.0 4.2 3.5
##  [847] 5.3 6.1 6.0 3.9 4.1 3.6 3.9 4.9 3.8 4.2 5.2 3.8 4.0 4.4 4.5 5.5 5.4 6.6
##  [865] 5.6 5.0 4.6 6.0 4.6 4.8 3.5 4.1 5.4 3.5 3.8 4.6 4.1 4.3 4.8 4.8 4.1 4.0
##  [883] 4.1 3.4 5.5 2.9 5.0 2.9 3.5 2.5 3.0 4.9 5.0 6.2 3.5 6.1 4.9 5.5 4.8 5.4
##  [901] 5.0 4.9 2.6 3.9 4.6 5.2 5.0 3.8 4.6 5.3 5.1 4.7 4.8 4.7 4.0 4.2 5.3 3.4
##  [919] 5.6 4.8 5.1 4.8 3.0 4.4 4.1 3.7 4.4 5.3 4.5 5.1 3.1 2.6 5.0 3.6 6.1 4.6
##  [937] 5.5 5.1 4.0 5.4 5.7 5.4 4.6 4.2 4.7 4.1 5.4 4.8 5.2 4.6 6.6 5.3 5.2 5.1
##  [955] 5.4 4.9 4.1 4.8 4.7 4.7 4.4 4.8 4.3 5.6 3.8 3.3 5.3 6.0 5.3 5.2 5.3 4.4
##  [973] 4.6 4.6 3.7 4.8 2.8 5.1 2.5 3.6 4.7 5.1 3.5 4.8 4.7 5.8 3.8 5.4 4.5 2.5
##  [991] 4.0 4.4 4.4 4.1 4.3 3.2 3.4 4.4 4.4 3.8
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
##   2.8   6.2
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
##    [1] 4.3 4.2 5.0 3.9 3.5 3.7 5.0 4.1 5.1 4.4 4.2 4.5 5.7 4.8 4.4 3.5 3.6 5.5
##   [19] 3.6 2.9 3.2 5.3 3.5 3.3 5.6 4.1 3.5 5.5 4.6 5.1 3.2 6.2 5.1 5.0 5.8 5.6
##   [37] 5.9 4.0 3.8 5.0 3.9 4.2 6.5 4.3 5.2 3.5 3.4 5.3 2.3 3.7 5.0 3.5 4.0 5.7
##   [55] 4.2 6.6 4.5 4.7 4.0 4.6 4.8 2.8 4.6 4.0 3.9 2.7 3.8 3.8 4.9 3.8 4.5 3.4
##   [73] 4.0 6.1 5.5 4.1 4.6 5.8 4.2 6.8 4.4 2.6 4.6 5.2 4.9 4.3 4.8 4.2 3.0 4.1
##   [91] 3.6 4.3 3.7 6.3 5.3 1.9 5.1 3.6 4.6 5.2 4.8 5.8 5.5 5.4 6.0 5.0 4.3 2.2
##  [109] 4.7 6.0 4.5 2.7 5.5 1.9 5.7 5.1 2.9 4.2 3.9 4.8 5.1 3.9 4.5 5.5 3.8 4.4
##  [127] 4.5 4.2 5.7 5.2 4.1 3.3 5.0 4.0 6.0 4.2 3.3 3.4 5.4 4.7 3.9 3.8 2.9 4.2
##  [145] 4.0 5.3 5.0 4.5 6.2 4.3 4.9 2.8 5.9 3.0 5.1 3.1 3.9 5.2 3.6 4.7 3.9 5.2
##  [163] 5.5 4.0 4.4 4.4 3.9 4.8 5.4 6.4 4.6 4.3 4.4 5.0 4.9 4.1 4.2 4.1 5.5 5.4
##  [181] 4.6 5.1 2.8 4.5 3.7 5.6 5.5 3.9 4.8 6.2 4.7 3.4 4.3 4.0 3.6 4.2 3.7 5.3
##  [199] 3.5 4.8 5.1 5.3 5.1 5.4 5.5 3.0 3.2 3.9 3.8 3.9 6.0 4.5 3.3 3.2 4.3 5.7
##  [217] 6.9 4.9 3.8 3.9 3.5 4.7 4.8 3.5 4.7 5.1 2.9 5.1 4.2 4.0 4.5 4.2 3.3 3.2
##  [235] 4.9 4.5 3.1 3.2 3.9 5.1 4.1 3.4 4.6 4.8 5.8 4.8 3.8 5.6 5.4 4.6 4.6 4.6
##  [253] 4.1 5.2 4.5 4.2 5.0 4.3 5.3 3.5 3.5 5.3 2.8 5.0 5.3 4.7 4.4 5.0 2.3 3.8
##  [271] 4.5 3.6 4.5 2.2 3.9 5.5 6.1 4.1 5.3 3.9 3.6 5.4 4.5 5.4 5.6 3.8 4.3 4.1
##  [289] 3.8 3.9 3.1 5.4 4.5 2.8 5.0 4.8 6.1 3.4 3.8 4.1 4.9 2.5 3.8 4.7 5.4 4.6
##  [307] 4.1 3.5 4.0 6.1 4.4 4.0 4.8 3.3 5.7 4.6 5.0 4.3 2.5 4.2 4.8 4.1 4.0 4.9
##  [325] 3.8 5.0 5.5 3.3 4.2 5.7 3.9 4.3 3.3 5.1 4.6 4.6 5.1 4.5 4.1 4.7 5.1 5.5
##  [343] 4.8 3.5 3.6 5.6 4.0 3.7 3.1 5.1 3.9 6.2 3.6 4.0 5.0 6.5 4.7 4.3 5.7 4.5
##  [361] 3.9 4.2 4.7 5.6 3.9 5.1 5.8 3.4 5.0 4.2 5.1 5.9 3.8 3.4 4.7 3.2 5.8 6.2
##  [379] 3.1 3.7 3.4 4.0 4.4 5.1 3.4 3.9 5.1 3.3 4.6 2.6 3.2 4.8 3.5 5.4 5.4 4.3
##  [397] 3.9 4.3 4.0 4.3 4.2 4.8 4.2 4.3 5.7 4.3 5.3 4.9 5.0 3.1 4.6 4.0 4.6 5.0
##  [415] 6.1 5.0 4.2 6.0 3.6 4.6 3.9 4.8 3.1 5.6 4.3 6.1 5.3 3.5 5.2 4.6 3.2 5.8
##  [433] 4.6 4.3 4.3 3.8 4.7 4.0 4.6 4.3 5.2 4.3 4.2 4.4 5.1 4.4 3.1 4.5 6.6 3.3
##  [451] 4.9 4.4 4.0 3.3 3.6 5.9 5.8 5.3 4.0 3.7 3.8 3.4 2.6 6.2 3.2 4.3 4.8 3.9
##  [469] 5.8 4.1 4.4 5.1 3.0 6.0 4.1 4.3 6.5 5.2 4.4 3.4 6.0 6.9 3.7 3.8 5.0 5.2
##  [487] 5.1 3.4 3.6 5.3 4.9 3.7 4.8 5.7 5.1 5.2 5.3 4.4 4.1 4.3 5.6 4.6 4.3 3.6
##  [505] 4.6 5.5 4.6 5.3 4.0 3.4 5.3 5.4 6.5 5.0 3.3 3.6 3.4 4.9 5.2 4.0 4.7 4.5
##  [523] 5.3 4.4 4.5 3.7 4.0 4.9 4.1 4.3 3.9 2.8 4.1 3.6 4.3 4.2 4.5 5.0 4.2 3.4
##  [541] 4.5 3.8 4.8 4.7 5.9 4.5 4.2 4.8 4.7 3.8 5.5 4.5 6.0 4.2 3.8 5.0 4.8 5.9
##  [559] 3.4 4.1 3.1 3.5 4.3 2.4 5.5 4.2 5.1 3.2 4.1 3.3 4.6 3.2 4.1 4.0 3.5 4.5
##  [577] 4.9 5.0 5.2 6.0 2.6 3.8 5.9 4.6 3.5 3.0 2.8 6.0 4.0 4.1 4.8 4.5 4.8 4.4
##  [595] 5.3 3.1 3.2 5.7 3.5 4.0 5.9 4.7 4.1 4.2 5.9 4.3 3.8 4.0 5.2 2.9 4.9 3.4
##  [613] 5.3 4.9 6.6 4.2 4.5 5.3 3.9 4.5 4.7 4.8 4.9 7.0 2.7 3.9 4.2 4.2 5.6 4.7
##  [631] 4.8 5.0 4.5 2.9 4.4 3.3 5.8 4.0 5.7 4.4 4.5 5.7 4.1 4.1 4.1 5.0 4.3 5.1
##  [649] 4.4 5.0 4.6 5.2 4.1 4.4 4.0 5.0 4.9 5.6 4.5 5.4 4.5 3.3 3.1 3.7 5.3 5.0
##  [667] 5.0 4.6 4.4 4.3 4.0 5.2 3.8 4.5 4.2 5.1 4.3 5.6 3.2 4.5 3.9 4.7 4.9 1.5
##  [685] 5.1 4.3 6.1 2.5 4.1 3.2 4.9 4.4 5.7 5.3 5.3 3.3 3.3 4.7 4.0 4.0 4.0 5.7
##  [703] 4.5 3.5 6.9 6.4 3.7 4.8 4.8 5.8 4.7 4.7 6.9 4.3 4.7 3.7 4.0 3.4 3.5 4.8
##  [721] 5.1 4.4 4.1 6.9 4.3 4.4 6.9 4.1 5.0 5.3 5.4 2.8 5.3 3.7 4.3 3.0 4.2 3.5
##  [739] 5.6 3.2 4.9 2.6 3.9 3.7 4.6 3.0 4.6 6.1 3.7 4.5 3.1 5.0 3.0 2.5 3.8 4.7
##  [757] 4.1 3.4 3.7 4.6 5.1 4.7 2.5 3.8 4.8 5.7 5.1 5.4 3.5 4.2 5.1 4.4 4.6 4.7
##  [775] 3.8 5.3 4.4 4.2 5.0 6.0 6.4 5.3 4.0 5.3 4.4 3.3 4.8 3.7 4.0 5.4 4.6 5.1
##  [793] 5.3 3.6 5.1 5.1 4.6 2.5 5.6 4.9 6.0 4.6 4.3 4.5 4.1 3.4 4.2 5.2 4.0 5.5
##  [811] 6.3 3.2 3.1 5.1 4.6 5.1 5.1 5.7 6.1 3.2 4.9 4.1 5.4 5.0 2.8 5.3 3.5 3.4
##  [829] 4.1 5.0 4.5 4.1 4.7 4.9 4.3 4.8 3.6 3.7 3.8 4.3 5.4 4.4 4.2 4.8 3.7 5.2
##  [847] 3.4 6.2 5.5 3.5 2.8 5.6 4.4 2.7 4.4 5.6 3.8 4.3 2.5 4.3 5.0 5.2 6.1 3.8
##  [865] 4.3 4.1 5.1 4.9 4.5 4.7 4.9 4.6 4.2 5.0 6.0 4.2 5.6 5.9 4.9 4.4 3.9 6.3
##  [883] 3.7 4.5 5.0 4.2 2.6 3.6 4.5 5.3 4.2 4.3 4.2 4.6 3.6 4.5 3.9 5.1 3.0 5.3
##  [901] 4.9 4.3 5.4 5.3 4.8 4.7 4.3 5.5 6.8 4.8 4.6 5.5 4.3 5.8 5.6 5.2 3.5 2.5
##  [919] 4.7 5.1 4.3 3.3 3.1 3.4 5.7 5.3 4.3 3.7 5.4 4.4 4.8 4.6 4.0 5.0 4.4 4.5
##  [937] 3.9 4.4 3.9 5.4 6.0 5.3 4.7 4.3 5.7 4.6 5.6 4.2 5.0 4.7 5.5 4.0 4.3 5.4
##  [955] 3.9 3.0 5.1 4.1 5.3 5.3 3.8 6.0 3.9 4.3 4.9 5.1 5.4 3.8 4.0 4.8 4.7 4.6
##  [973] 4.4 4.3 4.4 3.9 3.7 6.0 3.6 3.8 3.4 4.8 4.0 4.7 5.2 3.6 5.5 4.3 4.0 3.4
##  [991] 4.0 6.6 3.0 4.8 5.7 3.6 4.3 5.6 4.8 3.4
## 
## $func.thetastar
## [1] -0.0221
## 
## $jack.boot.val
##  [1]  0.52500000  0.40518732  0.19945205  0.15773810  0.04775281 -0.12831325
##  [7] -0.23398329 -0.25278515 -0.42320917 -0.49620596
## 
## $jack.boot.se
## [1] 0.9782066
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
##    [1] 6.1 4.7 4.2 3.9 4.7 4.7 4.6 3.9 4.4 4.7 4.3 5.3 5.6 5.1 4.4 4.2 4.3 3.2
##   [19] 3.5 3.0 4.3 4.7 4.4 4.3 4.3 5.1 5.5 5.0 5.1 4.1 5.0 6.0 4.0 3.3 5.5 4.7
##   [37] 5.3 3.2 4.3 4.3 6.2 4.0 4.4 3.3 4.6 3.9 5.2 3.0 4.8 4.6 5.2 6.0 5.9 3.5
##   [55] 4.0 5.8 4.9 3.5 3.4 2.9 4.3 3.9 3.5 4.7 3.8 3.9 5.6 5.2 4.5 4.0 3.5 6.1
##   [73] 3.5 4.6 4.6 4.0 2.5 5.9 3.7 4.2 4.5 4.7 4.6 5.3 3.1 3.4 3.8 4.1 3.7 4.8
##   [91] 3.4 3.7 4.3 4.5 3.6 3.5 5.0 4.4 4.0 5.0 3.2 5.2 4.5 5.8 5.6 5.2 5.1 5.5
##  [109] 3.4 2.6 3.9 4.8 6.3 4.1 5.0 4.1 5.0 3.8 6.3 4.9 4.3 3.2 6.0 5.8 3.6 4.0
##  [127] 4.3 3.1 4.6 5.3 3.9 4.0 2.6 5.7 4.7 4.6 4.2 5.7 4.1 5.4 3.4 3.3 4.3 4.8
##  [145] 3.6 5.7 4.1 4.1 3.6 5.7 5.3 6.0 5.5 4.2 4.0 4.4 4.1 4.9 4.6 4.5 3.8 5.3
##  [163] 4.3 3.0 4.7 4.1 4.1 4.6 5.1 5.5 5.1 2.8 2.9 4.8 5.0 5.8 2.6 4.1 3.2 4.8
##  [181] 3.0 3.4 4.7 3.4 3.1 4.9 4.7 4.8 3.4 3.6 4.2 5.8 5.0 3.8 5.0 4.2 4.0 4.1
##  [199] 3.7 6.0 4.1 4.3 3.7 5.1 3.7 4.0 3.6 5.0 4.0 5.7 3.4 5.6 2.7 4.9 4.1 3.9
##  [217] 3.6 5.3 5.6 4.5 3.5 3.6 5.6 5.2 4.1 3.6 4.1 5.1 4.5 5.6 6.2 3.5 5.4 2.9
##  [235] 4.8 5.1 5.2 4.7 4.2 5.3 4.8 3.0 4.4 3.8 4.4 3.6 4.4 4.4 5.8 3.2 3.4 5.3
##  [253] 4.1 3.0 6.1 5.4 4.5 3.7 5.9 5.1 3.7 4.7 4.2 4.3 4.4 5.4 4.2 3.8 6.1 4.4
##  [271] 3.9 5.4 2.9 2.9 3.9 4.9 4.0 3.3 6.6 4.1 3.1 6.1 4.8 4.2 3.6 5.3 4.2 5.4
##  [289] 4.7 4.4 3.4 2.9 4.1 4.0 5.3 4.5 5.9 4.7 5.4 4.9 4.5 5.4 3.8 4.2 4.9 4.9
##  [307] 4.4 5.4 4.2 5.0 4.3 2.7 1.8 3.3 6.4 3.7 3.9 4.7 5.1 5.3 4.3 4.4 4.6 3.3
##  [325] 5.4 4.1 5.2 4.5 4.5 6.2 4.0 4.2 1.8 4.5 4.3 4.9 5.6 5.0 5.0 3.3 4.6 5.9
##  [343] 2.8 6.3 4.8 4.3 4.4 5.1 5.3 3.1 5.4 4.4 4.7 3.6 3.8 5.2 4.7 5.1 5.3 4.8
##  [361] 2.8 4.1 3.7 5.8 4.5 3.5 4.5 4.1 5.2 3.9 6.0 4.5 2.6 2.7 5.0 6.2 5.0 6.1
##  [379] 4.5 4.3 4.0 3.8 5.8 5.9 4.1 5.0 6.0 5.6 4.2 4.5 4.0 5.1 5.4 3.5 4.1 4.7
##  [397] 5.8 3.7 4.6 4.6 4.7 5.9 5.0 5.2 4.1 4.0 5.8 3.3 4.0 2.4 4.5 4.2 3.8 4.0
##  [415] 5.0 3.3 4.5 5.1 3.7 3.9 3.4 3.7 3.6 4.3 6.3 3.2 3.7 4.4 5.5 4.2 3.7 4.4
##  [433] 4.5 4.2 3.1 4.7 5.2 4.5 3.9 4.1 4.0 6.7 3.4 5.3 3.0 5.8 5.4 4.4 4.1 4.5
##  [451] 4.2 4.3 5.8 7.0 4.4 5.3 3.3 4.7 6.6 5.6 4.8 6.0 5.0 3.6 4.3 5.5 3.7 4.3
##  [469] 3.3 6.8 4.7 2.8 3.3 4.7 5.0 5.1 5.7 6.2 5.4 3.8 6.0 3.8 4.0 4.9 3.8 5.1
##  [487] 5.4 3.0 5.1 3.0 6.3 5.1 5.3 4.5 4.2 4.3 3.0 6.0 3.2 4.8 3.8 3.8 3.7 4.1
##  [505] 3.5 4.4 4.4 5.4 3.1 4.8 4.2 3.1 3.9 4.2 4.5 5.4 5.0 3.7 4.8 3.1 5.2 5.2
##  [523] 4.6 4.7 2.7 2.6 5.4 2.7 4.1 4.1 6.1 3.0 4.7 3.6 3.4 4.7 4.3 4.0 4.6 4.8
##  [541] 5.1 4.1 4.9 5.7 4.1 3.4 3.4 6.4 5.2 4.9 5.1 4.4 3.7 5.1 3.5 5.7 3.8 5.8
##  [559] 4.1 3.6 5.1 5.2 5.2 3.9 4.2 5.3 3.3 4.2 4.8 3.1 4.6 5.5 4.0 5.4 5.5 3.9
##  [577] 4.0 4.3 5.5 4.9 5.1 3.3 4.3 4.0 6.2 6.0 3.7 4.6 3.8 4.9 4.5 5.2 3.9 4.1
##  [595] 4.1 4.5 4.4 4.0 5.1 4.5 3.1 4.1 4.1 5.8 2.9 5.5 4.0 4.3 3.5 4.1 3.2 4.7
##  [613] 5.3 3.4 5.1 4.9 3.6 4.4 4.0 3.2 3.6 3.3 3.8 4.0 3.6 4.7 4.3 5.1 3.8 4.2
##  [631] 4.2 5.8 5.4 4.7 4.6 4.7 6.3 3.5 4.4 4.0 3.6 4.3 4.4 5.3 4.2 5.1 5.1 4.5
##  [649] 3.0 4.4 4.7 4.9 6.6 4.0 4.3 3.6 4.5 4.1 5.5 3.3 3.4 5.4 5.4 5.5 5.5 4.6
##  [667] 4.5 3.7 3.5 4.8 4.3 4.5 3.9 4.4 4.4 4.7 3.5 4.2 4.2 4.7 5.0 5.3 4.4 6.2
##  [685] 2.9 5.6 5.4 3.5 5.1 3.2 6.8 5.3 4.8 5.5 5.2 3.9 4.2 3.6 4.0 4.6 5.9 3.4
##  [703] 4.6 3.4 4.4 5.5 4.2 4.2 4.6 5.2 5.7 3.8 4.9 5.6 4.9 3.2 5.0 5.6 4.9 5.1
##  [721] 5.2 4.8 6.2 4.1 4.6 5.1 5.5 5.2 3.3 4.8 2.9 3.1 4.4 3.5 5.9 5.4 4.0 4.5
##  [739] 2.5 3.2 5.3 4.9 4.8 4.0 4.4 2.7 5.1 3.7 4.2 4.5 4.9 5.7 3.5 6.4 5.4 3.9
##  [757] 5.0 4.6 3.8 4.9 5.1 5.0 3.0 5.6 4.0 3.4 6.5 3.1 6.3 3.3 5.6 5.3 3.0 3.4
##  [775] 4.0 4.3 4.5 4.4 4.4 5.3 4.0 5.1 7.2 5.6 4.8 5.6 4.6 5.5 5.1 2.5 5.1 5.5
##  [793] 3.3 3.9 4.7 5.1 4.6 5.4 3.8 6.0 1.9 4.3 3.3 5.4 5.9 4.3 3.5 5.1 3.8 3.9
##  [811] 3.3 4.9 5.1 3.8 3.6 4.7 4.2 5.3 4.4 6.0 4.1 4.2 6.3 3.8 4.3 4.2 4.4 5.7
##  [829] 3.7 3.9 4.1 5.4 4.1 5.4 3.7 4.9 2.6 4.6 5.7 4.1 3.6 4.7 4.3 5.4 3.0 3.5
##  [847] 3.6 5.8 5.6 3.5 6.1 4.2 5.2 4.0 4.1 4.7 3.3 5.6 5.9 4.1 4.2 4.4 5.3 2.7
##  [865] 2.9 5.8 5.5 4.5 3.5 5.1 4.1 3.8 4.9 5.1 4.0 3.1 4.5 6.0 4.1 4.7 5.1 5.3
##  [883] 3.0 4.3 6.7 4.3 3.3 5.9 5.5 3.7 4.5 4.7 5.0 4.8 4.1 5.6 4.9 5.1 4.6 3.8
##  [901] 5.3 3.8 3.6 2.7 2.5 4.4 3.9 4.3 4.5 6.2 4.7 5.5 4.2 3.6 3.1 5.2 6.2 4.8
##  [919] 6.4 6.1 4.6 4.9 3.6 4.4 4.7 4.5 5.4 4.0 2.7 4.7 4.7 5.4 4.6 4.2 5.7 5.1
##  [937] 3.7 5.7 3.4 5.0 4.8 6.1 3.5 4.7 5.1 4.0 3.9 4.7 3.3 5.3 3.8 3.1 4.5 3.5
##  [955] 4.2 3.3 4.6 4.7 2.8 3.8 4.6 3.7 5.2 3.4 3.7 4.0 4.3 4.9 4.5 5.9 4.4 4.0
##  [973] 5.1 5.4 4.4 4.6 3.7 3.5 3.9 4.6 3.9 1.8 6.0 4.0 3.2 3.7 6.4 3.6 4.8 3.6
##  [991] 5.8 5.2 3.8 5.7 6.2 4.5 4.6 5.0 4.0 5.2
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.4 5.2 5.1 4.9 4.9 4.9 4.5 4.4
## 
## $jack.boot.se
## [1] 1.063203
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
## [1] 1.713642
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
##   3.473721   5.529690 
##  (1.484948) (2.543219)
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
## [1]  0.4141451  0.1844198 -0.7145077  0.8925966  1.8185487  0.4755179
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
##    [1]  2.427038911  1.596151515  1.319962295  1.229705561  0.760330608
##    [6]  1.642438271  1.482305006  1.433158097  1.972322842  1.356096215
##   [11]  1.121916268  2.258950139  0.399269377  1.910919312  1.142530666
##   [16]  0.702046566  1.573877831  1.849498605  2.323256029  0.526645671
##   [21]  1.405953869  0.256669435  2.209071313  2.153134983  1.408330776
##   [26]  1.838897117  1.490315497  1.816231561  1.190645954  0.634376947
##   [31]  0.609610742  1.575061133  2.546596136  1.952709788  1.707845466
##   [36] -0.027529950  0.618588664  1.251527752  1.125673112  0.960252877
##   [41]  2.289766574  1.438460499  2.285334211  1.731441842  1.394723953
##   [46]  1.300943419 -0.276214187  0.465154256  0.261252808  0.944408974
##   [51]  2.133057846  0.169363081  2.026708799  2.005362011 -0.608016433
##   [56]  1.168143712  0.060057438  0.728638908  1.810597727  0.908315824
##   [61]  1.015960759  1.470021590  1.782004413  1.138070691  0.598560448
##   [66]  1.474165269  2.008918221  0.918767130  1.512227616  1.269806759
##   [71]  0.617989225  0.141325919  1.953229117  2.158584826  1.651808348
##   [76] -0.298195973  0.226585649  2.033647639  2.180412344  1.608703550
##   [81]  0.894115119  0.260140419  2.024895344  0.248161152  2.047530440
##   [86]  0.333441716  1.825192015  1.156067096  0.134052329 -0.138696637
##   [91]  1.283484419 -0.080104710  1.087608615  1.962190364  2.295344240
##   [96] -0.152509819  1.258289420  0.744436496  0.554576187  1.427157972
##  [101]  0.766989136  1.968616728  0.383038932  0.096994713  0.325483852
##  [106]  1.951942593 -0.356475359  0.642606793  1.230345097  1.901623367
##  [111]  0.930962260  2.049006067  2.477520516  2.121542137  2.034051529
##  [116]  2.042795811  1.174705583  0.352411872  0.411870308  0.976094294
##  [121]  1.740888883  1.038712502  2.030328583  0.240174310  0.624751595
##  [126]  1.908762157  0.451540732  0.725961842  1.949072591  0.293292600
##  [131]  0.681683288  0.719029532  0.603778932  2.233929107  1.072463966
##  [136] -0.404641208  0.726195361 -0.109688787  1.820658103 -0.285378500
##  [141]  1.425673898  2.380347931  1.534468563  0.617220922  1.461660120
##  [146]  0.253069332  1.345610331  0.039655006  1.554847705  1.590669799
##  [151]  1.603375075 -0.062057086 -0.261505370  0.087203330  1.117208503
##  [156] -0.064341787  1.553733596  0.965262493 -0.139242500  1.752513072
##  [161]  0.856062214 -0.026521246  0.689571940  0.134481759  0.726807132
##  [166]  1.455478668  0.397173066  1.757832201 -0.016887196 -0.504414280
##  [171] -0.167247588  0.627553695  1.734164122 -0.143972332  2.120997539
##  [176]  0.940156915  1.171160645  1.443277184  1.163901740  0.703749719
##  [181]  0.296400221  1.700883513  0.985823104  1.165491261  0.017336026
##  [186]  1.236035785  1.804681881  1.179556300  1.498167538  1.346985512
##  [191]  1.949193569  0.552535449  0.252984523  1.530843410  1.091428244
##  [196]  1.190645954  1.475898666  2.237786907  1.946976472  1.259457141
##  [201] -1.552931952  0.664784854  1.606446143  1.976201735  1.067668955
##  [206]  1.108640182 -0.323276454 -1.261282953 -0.208332255  0.828324521
##  [211]  1.988539836  1.700895852  1.176140952  1.171428992  0.873143428
##  [216]  1.301261985  1.009978566  0.672140233 -0.118038627  1.117806661
##  [221]  1.445849100 -0.295707253  0.820925232  1.170415159  0.588126596
##  [226]  1.498990799  1.548132407  1.209859061  0.610882114  1.059689535
##  [231]  1.811739862  1.320956556  1.667377635 -1.058841554  1.880220933
##  [236]  1.315091210  0.352514312  1.121552774  1.973572223  0.239468796
##  [241] -0.119582665  2.167839180  0.442148443  1.315088952  0.385190570
##  [246]  1.178473065 -0.016344985  1.226723444  1.443771674  1.693997478
##  [251]  1.918212092  0.636999485  0.699041566  1.049063520  0.417956943
##  [256]  1.742200990  2.287632346  1.544959354  1.764428680  0.090089818
##  [261]  1.110783817  1.105546480  1.987573239  2.329581130  0.069393691
##  [266]  2.004770957  0.389447804  1.738063628  0.831198685  2.145064990
##  [271]  1.984323078 -0.149978274  1.089714829  2.235608898  1.290296052
##  [276]  2.071633569  1.451204595  1.094073526  0.059707930 -0.246500182
##  [281]  1.125753380  0.987543780  0.318183214  1.640437764  0.431592032
##  [286]  1.296466951  1.760874669  0.626374567  0.182287060  1.649096086
##  [291]  1.062244484  2.325123286  0.384576630  2.341553655  0.450245423
##  [296]  1.007295974  1.879841546  1.614421095  1.705376235  1.728515549
##  [301]  0.533744788  1.029156899  1.012990688  0.258672019  1.456781359
##  [306]  0.102077738  0.623710414  0.867551868  1.830955644  0.962626519
##  [311]  1.726519932  1.198734332  2.122547611  1.132737293  1.527751480
##  [316]  1.934629601  1.170691209 -0.329349239  0.390674544  1.240247146
##  [321] -0.063044946  0.834258692  1.135005458  0.811767150  0.699561478
##  [326]  1.601214088 -0.549637721  1.915006496  1.073770466  0.582593124
##  [331] -0.036055288  0.574772006 -0.339951794 -0.266895186  1.242179811
##  [336]  0.062598985  0.779111410  2.015543155  1.103190807  1.236513552
##  [341]  1.679997085  1.016891097  0.443649831  1.322876401  1.834504832
##  [346]  1.676995331  1.188616130  0.738257033  1.246798798  1.767054181
##  [351]  1.357448173  0.610873522  1.189960077  1.692217609 -0.803629247
##  [356]  1.544709862  1.191835380  2.285900813  1.655908657  2.151868979
##  [361]  1.744722743  1.056155056  0.957811086  1.008353417  1.236938535
##  [366]  0.308317325  2.100448488  1.230286187  1.358812644  0.186592341
##  [371]  1.823583271  0.661556284 -0.047704855  1.977005945  0.328836860
##  [376]  1.753990539  0.635287538  1.704433127  1.538139553 -0.086121476
##  [381]  1.793458767  1.225260222  0.537344598  1.522916161  1.863602307
##  [386]  1.515645353  1.007370239  0.574125211  1.188519819  0.568834263
##  [391]  0.684627884  1.581702273  1.577166141  1.086353751  2.282550535
##  [396]  0.020062120  1.162413647  1.608639370 -0.119844896  0.166629069
##  [401]  2.093966520  1.719811222  2.206920351  1.988803194  0.481560805
##  [406]  0.583053803  1.403605872  1.704858343  1.588857549  1.953946874
##  [411]  1.654768344  0.411266034  0.657109044  1.998979745  1.172137564
##  [416]  1.887432295  0.413930423  0.293147055  0.234674359  0.673704274
##  [421]  0.640264497 -0.176477874  1.364864722  1.265681389  1.921006292
##  [426]  0.350695682  1.154096373  1.297989580 -0.008325818  1.394723953
##  [431]  0.179237983  0.585885394  1.685904711  0.786266102 -0.336576436
##  [436]  0.722681775  0.220262759  0.964338544  0.449754505  1.542411294
##  [441]  0.809708989  1.863602307  0.305713921  1.811942819  0.126219576
##  [446]  0.233038774  0.417884928  2.184819402  1.540586490  1.904705213
##  [451]  1.895741040  2.101869815  1.249415472  1.974628006  0.645782345
##  [456]  1.912545930  2.288443291  0.121648520  0.682368224  2.184878098
##  [461]  0.902566831 -0.424457033  2.449580556  1.785447129  1.945479024
##  [466]  1.167376109  0.218385835 -0.018013483  0.342359655  1.660890371
##  [471]  1.794517452  1.169986788  0.520170323  1.238690155  1.504252525
##  [476]  0.972561362  0.338346311  1.170502903  1.140658918  0.069393691
##  [481]  0.846139533  0.712378452  1.870694627  0.023143254  1.196610012
##  [486]  0.318401185  1.177073739  1.097961462  1.206411926  1.527282680
##  [491]  2.119286741  0.375800763  1.329737845  2.106987046  0.330967594
##  [496]  2.060946347  0.434509601 -0.413289899  1.810777422  2.204709679
##  [501]  1.405197959  2.093828202  1.393503210  0.454972867  1.698551839
##  [506]  1.324666256  1.276595185  1.995627350  0.993723805  1.181154684
##  [511]  0.970407450  1.080141338  1.324818836  0.200595867  1.964822525
##  [516]  2.051814530  0.569015765  1.134823887  1.831561276  1.899078942
##  [521]  0.463838605  1.334244580  0.126811103 -0.114590906  1.782386170
##  [526]  0.321673264  1.919058848  0.790650017  1.638231386  1.903978043
##  [531]  0.238207163  0.730052997  1.367606513  0.815157046  1.422048219
##  [536]  1.069382881  1.351827012  2.028632610  2.188170376  1.258977105
##  [541]  0.338308403  0.730504904  1.478225739  1.170706340  1.440387941
##  [546]  1.612522558  1.721701967  1.323132647  0.911074771  1.572837006
##  [551]  1.051989066  2.055471453 -0.237079271  1.156792296  2.144261516
##  [556]  1.400714049  2.009487796  0.721848625  1.232827356  0.848734155
##  [561]  0.999506842  1.391959035  1.330385246  0.750276194  2.234507204
##  [566]  0.950129981  1.333746744 -0.514938447  1.289780297  1.909312675
##  [571]  0.521675979  1.615165856  1.330451423  1.097315073  1.528849104
##  [576]  1.488085399  1.991375219 -0.206020838 -0.120934063  1.200746073
##  [581]  1.605319267  1.690894328  1.487713425  1.729456171  1.704354147
##  [586]  1.390873020  1.950071306  1.769713922  1.097943340  0.881220640
##  [591]  1.962795435  1.939864841  1.129076905  0.326236655  2.125982749
##  [596]  1.840617859  2.239797162 -0.882226256  0.656557127  0.544525824
##  [601]  1.783929595 -0.315622676  1.390145910  1.974787434  0.337313308
##  [606]  2.634341424  1.347449968  0.964658784 -0.290260767 -0.185215592
##  [611]  0.691027167  1.994022290  0.329362583  0.744503087  1.627379389
##  [616]  1.059105829  2.096076576  0.181627786  1.735174034  0.570336667
##  [621]  0.783032285  1.023581381  1.853437545  2.206570817 -0.595789328
##  [626] -0.358782236  2.135135202  1.084673537  1.398845830  1.014229274
##  [631]  1.809165154  1.258055684  2.182106364  1.078527630  1.171871973
##  [636]  0.077524129  1.056860673  1.177828687  0.964669100  1.361228672
##  [641] -0.094500942  0.152662978  1.819929290  1.009695482  1.966157666
##  [646]  1.034048457  1.862408455  0.494386687  1.285231501 -0.583871198
##  [651] -0.180506938 -0.203239193 -0.142678733  1.827927985  1.087974531
##  [656]  0.115977145  1.898141121  2.076983722  1.422683379  1.328494004
##  [661]  2.056755751  1.660305191  2.247330992 -0.126477182  1.966302969
##  [666]  0.301068054  2.415592003  2.013939423  1.223338346 -0.072773387
##  [671]  1.230792930  0.587217430  1.144881939  1.440203012  0.626947985
##  [676] -0.418620717  0.659180838 -0.150254863  0.480919908  1.231436967
##  [681]  0.752762956  0.693316338 -0.136345821  0.672628519  1.054615610
##  [686]  1.678310291  1.194703707  0.986401059  1.391959035 -0.328214307
##  [691] -0.123546781  1.880307585 -0.795599242  0.915279211 -1.226597141
##  [696] -0.566101556  1.402194673  2.333322650  1.115612375  1.580059310
##  [701]  0.400190336  2.229987299  0.341757883  1.304107165  0.617827007
##  [706] -0.785019562  1.291174127  1.116283479  0.758275728  0.382002247
##  [711]  1.136369549  2.074360562 -0.621829078  1.827545122  1.490741098
##  [716]  0.691763960 -0.327587549  1.987798160  1.714117630  1.259689236
##  [721]  0.256311961  1.855177073  0.136461050  0.054157137  1.619192718
##  [726]  0.936632687  0.669654450  1.269806759  0.244179359  0.406392667
##  [731] -0.235254671  1.072228476  1.309604550  0.741842761 -0.045405468
##  [736]  0.945815285  0.469358875  0.850651506  1.622656543  0.027803166
##  [741]  1.576567340  2.040995909  2.197306984  1.275949464  0.039317562
##  [746]  1.326669786 -0.154583071  1.966355548  1.571666668  0.762256938
##  [751]  1.726869157 -0.585907765  1.613565628 -0.829500738  1.085604059
##  [756]  0.561697776  1.230063156 -0.377565322 -0.074344591  1.343613676
##  [761]  1.744552812  0.204856737  2.391187481 -0.132510469  2.160565146
##  [766]  0.893109468  1.056331670  2.289766574  1.171769678  1.573804467
##  [771]  0.548387902  0.748477753  2.495330027 -0.222431496  1.798450882
##  [776]  1.952813769  0.893207836  0.133499977  1.367360887 -0.355910149
##  [781]  1.576857991  0.784714541  0.107298982  0.791097904  0.264128862
##  [786]  1.273178803  1.301734822  0.416485343  2.369480532  2.492428723
##  [791]  0.783975632  1.149646279  0.569178508 -0.108936566  1.403327662
##  [796]  2.419644598  1.080236990  1.973691697  0.100139670  1.873638071
##  [801]  0.551361517  0.658702697 -0.245007073  2.167290161  2.273645968
##  [806]  1.434674432 -0.365573714  1.538577331  1.360294398  0.488173025
##  [811]  1.122851230 -0.917714485  1.975127289  0.431659543  2.228700073
##  [816] -0.104203172  0.992854468  0.193672720  0.834401729  1.980026592
##  [821]  1.408483511  0.675258964  0.617159257  1.950727165  0.248573173
##  [826]  0.395287436 -0.432185043  0.241895816  2.269612284 -0.180376664
##  [831]  1.117225258  1.078497172  0.076491107 -0.495734864  2.237105969
##  [836]  0.380836825  2.322673292  1.416978090  0.710695322  2.381464447
##  [841]  1.349313501  1.067116612  0.715549937 -0.313334129  1.714695280
##  [846] -0.019523918  0.674514296  1.988573430  0.943841804 -0.327627421
##  [851]  1.253343564 -0.377184654  0.808577807  1.382761675 -0.164204541
##  [856]  1.274705005  1.069399679  0.035585368  1.095934616  0.161126179
##  [861]  1.241399732  0.898000558  0.924109115  0.358259068  1.486277211
##  [866]  1.085295137  0.995695077 -0.198291477  1.944729517  0.174774455
##  [871]  1.676970317  0.993316597  0.292507835  1.843418043  2.159213234
##  [876]  2.037325915  1.864555115  0.569973031  0.599735826  1.190736345
##  [881] -0.129937704  1.497271616  0.846638968  1.778200199  0.777748663
##  [886] -0.125614265  0.386989507  0.806524935  1.743370494  2.300884036
##  [891]  1.938668983  0.633260699  0.376434202  0.686465562  1.882304073
##  [896]  0.166598572  1.231615953  1.208612717  0.261252808  0.680353930
##  [901]  0.411677988  0.010618601  1.726677161 -0.065777982 -0.058795195
##  [906]  1.129287860  1.728260590  1.033023226  1.393234622  2.116399749
##  [911]  0.182150020  1.355047320  0.243106801  0.506665931  0.722565391
##  [916] -0.119488028  1.330264178  1.302999960 -0.023686083  1.951521168
##  [921]  1.257681659  0.974047696  1.665833655  2.003200071  0.430478045
##  [926] -0.358662079 -0.330188894  1.790681343  1.324818836  2.338469657
##  [931]  1.184851967  1.740645403  1.497271616  0.683663252  0.947610520
##  [936]  2.039803157  1.970077559  1.796129799  0.431592032  2.074972534
##  [941]  1.215138372 -0.168161510  0.306498968  2.003793206  1.600484124
##  [946]  0.043653757  1.716583944 -0.380410847  1.655265149  2.242229147
##  [951]  1.149924424  0.296658488  0.909749593  0.282516182  0.213941944
##  [956]  2.028446130  1.816918234  1.677367521  2.012605365  1.041290455
##  [961]  1.508455006  1.754286568  2.024544133 -0.245431629 -0.075114817
##  [966] -0.165112436  1.459971666  1.792943556  0.450088260  0.635876382
##  [971]  2.148834403  0.829063234  1.870856498  2.034800875  1.573920391
##  [976]  0.644959915 -0.223759096  1.996809154  1.252102937  0.521374629
##  [981]  0.830744117  1.153477256  1.531823215  0.357774153  2.469612465
##  [986]  0.092057546  0.369834345  0.576304287  2.073166517  1.936533657
##  [991]  1.453326899  1.998372543  1.925057165  1.106303526  1.813775389
##  [996]  1.421619012  1.818888867  1.648967780  1.824293569  1.352945430
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
##   0.62819212   0.38824853 
##  (0.12277496) (0.08681248)
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
## [1] -0.3901197  0.2821354 -0.3219229 -0.1039566  0.4815363 -0.6404165
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
## [1] 0.027
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9174381
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
##     original       bias    std. error
## t1*      4.5 0.0005005005   0.9015183
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 5 6 8 
## 1 2 1 2 3 1
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
## [1] 0.0093
```

```r
se.boot
```

```
## [1] 0.8950892
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

