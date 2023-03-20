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
## 0 2 3 4 6 7 
## 1 3 3 1 1 1
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
## [1] -0.015
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
## [1] 2.693738
```

```r
UL.boot
```

```
## [1] 6.276262
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.1
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
##    [1] 4.1 3.7 4.3 4.1 3.3 5.7 4.1 4.8 3.2 6.3 3.2 4.4 3.0 3.8 4.9 2.7 6.1 4.3
##   [19] 3.7 5.0 2.9 4.3 3.2 4.0 5.4 3.4 3.5 4.9 2.8 4.6 5.6 4.5 4.6 4.1 5.9 2.6
##   [37] 4.4 3.7 4.8 2.9 3.4 4.9 5.0 5.8 4.7 4.5 3.0 2.1 4.9 5.0 3.6 5.0 5.5 4.7
##   [55] 4.9 5.1 4.5 4.3 5.6 5.4 5.2 4.4 5.5 4.3 5.4 4.8 3.4 4.3 5.4 3.9 4.6 5.4
##   [73] 4.4 2.9 6.1 4.2 5.3 3.8 3.9 5.7 3.6 3.9 3.1 6.2 3.7 4.2 4.7 3.3 5.8 4.5
##   [91] 4.3 4.8 3.7 7.2 3.5 4.9 3.3 5.2 4.2 3.2 3.6 5.9 3.3 4.3 4.3 5.3 4.6 4.4
##  [109] 4.1 5.2 4.3 4.7 5.0 5.5 4.0 4.3 5.0 4.0 3.7 5.3 7.2 4.9 5.3 3.7 3.4 5.0
##  [127] 5.3 4.4 4.0 4.7 5.8 5.8 5.9 4.8 5.4 4.8 3.5 3.7 5.2 2.9 4.7 3.8 3.1 2.9
##  [145] 5.6 4.4 3.3 4.2 4.2 4.5 3.1 4.7 4.4 3.7 4.7 6.6 4.6 4.6 3.5 5.2 3.5 5.5
##  [163] 4.3 5.2 4.8 5.0 4.4 3.2 2.4 3.8 4.2 6.0 5.8 3.7 3.7 4.6 5.5 5.7 3.5 3.4
##  [181] 4.0 3.5 3.8 3.9 7.0 5.5 3.1 5.7 3.9 6.4 4.5 4.7 5.8 5.9 3.5 5.5 4.5 4.2
##  [199] 4.9 4.8 4.2 4.1 5.1 5.5 5.6 3.9 3.4 4.6 4.1 4.2 4.1 5.1 5.1 5.2 6.4 5.0
##  [217] 7.1 3.9 5.8 3.5 5.6 3.6 4.0 3.7 3.0 4.3 4.5 5.5 3.9 4.8 4.6 4.2 4.3 6.3
##  [235] 4.5 5.1 5.4 3.4 3.8 6.5 4.0 6.3 4.7 4.3 4.2 3.8 5.7 4.6 3.7 2.2 5.1 5.3
##  [253] 4.1 4.7 4.8 3.6 5.1 3.9 6.0 2.4 5.0 4.6 5.9 4.2 4.9 5.0 5.4 2.2 4.5 5.9
##  [271] 3.9 2.4 5.0 3.7 3.6 4.1 5.5 5.1 4.6 4.2 6.3 4.7 4.2 6.4 4.4 3.5 4.0 5.6
##  [289] 2.8 5.4 4.2 4.0 5.0 3.0 4.4 3.5 5.5 4.6 4.4 3.9 5.5 4.1 5.5 3.7 2.4 3.7
##  [307] 2.9 5.2 4.5 3.0 5.1 4.9 5.2 5.3 4.8 2.9 4.6 4.4 4.1 3.3 4.2 5.2 5.8 4.0
##  [325] 4.6 2.3 4.6 3.7 5.2 3.7 5.7 3.7 4.5 3.8 4.0 6.8 4.9 5.6 3.9 5.0 6.3 3.6
##  [343] 5.4 5.0 4.9 5.1 4.1 4.3 4.0 3.8 2.3 5.7 3.6 6.3 4.9 5.9 4.9 5.0 3.4 5.2
##  [361] 2.4 4.5 5.1 4.3 6.7 5.4 5.0 4.6 5.1 6.0 4.8 3.3 3.5 4.7 5.2 4.5 3.2 3.3
##  [379] 4.3 5.5 3.5 3.7 3.3 5.4 5.0 3.9 4.3 3.6 4.5 5.2 5.1 4.7 4.1 3.8 5.6 5.1
##  [397] 5.2 3.7 4.1 4.5 5.2 5.0 4.0 4.4 5.7 3.6 4.5 4.0 3.1 3.3 5.2 3.1 4.7 5.1
##  [415] 5.1 4.1 4.9 5.9 5.7 4.7 4.3 3.6 4.7 4.3 5.6 4.6 4.6 4.6 4.1 3.7 3.0 4.9
##  [433] 4.3 3.8 4.6 4.1 4.8 3.3 4.1 4.4 3.8 4.5 5.0 3.2 4.2 3.5 5.3 4.8 4.3 4.0
##  [451] 4.6 5.3 3.5 5.1 4.7 4.9 3.9 3.2 4.9 4.1 3.6 3.8 5.6 4.7 6.3 3.1 3.8 4.3
##  [469] 4.3 5.2 5.3 3.5 5.7 4.0 4.2 4.5 4.4 4.6 5.1 5.0 5.2 5.4 4.5 4.2 4.2 4.4
##  [487] 4.7 4.1 3.3 4.8 4.4 5.0 5.9 4.1 5.4 5.4 4.9 4.6 3.3 4.0 4.9 4.3 5.1 5.1
##  [505] 4.2 5.0 5.2 5.9 4.2 4.6 3.9 4.2 3.1 4.9 2.9 5.6 5.9 5.0 5.0 5.6 3.9 3.0
##  [523] 5.1 3.7 5.7 4.5 6.0 2.5 4.8 3.4 3.2 4.9 5.0 4.7 3.0 6.8 5.1 5.6 6.3 3.9
##  [541] 3.3 3.7 5.0 5.0 4.2 6.0 4.2 6.4 5.3 4.1 4.1 3.9 4.1 5.2 4.1 4.9 4.7 4.4
##  [559] 3.7 3.3 5.6 6.4 3.8 4.0 5.5 3.5 3.5 2.9 5.7 4.6 3.1 5.1 4.2 3.7 4.0 4.0
##  [577] 5.2 3.9 5.2 5.3 4.5 5.6 5.3 3.9 4.6 4.6 5.2 4.0 5.9 6.8 4.1 4.5 5.0 4.1
##  [595] 4.9 4.6 5.3 4.2 6.5 5.5 4.6 5.4 6.4 5.0 3.1 5.2 3.2 4.3 4.6 4.3 5.6 3.9
##  [613] 5.2 5.2 5.5 5.0 3.5 4.1 3.7 4.6 4.9 5.0 4.8 2.3 3.3 2.6 4.4 4.2 3.9 3.0
##  [631] 5.2 5.2 5.8 3.5 5.4 3.6 4.1 4.6 5.0 6.3 5.5 2.8 4.2 5.6 6.0 3.7 3.7 5.2
##  [649] 4.0 5.6 5.9 4.2 4.4 5.3 4.4 4.7 4.4 4.6 4.3 4.6 5.1 3.0 5.0 4.4 5.2 4.5
##  [667] 4.2 3.8 4.9 6.2 4.1 4.1 5.2 4.2 3.3 4.2 5.3 4.9 4.2 5.1 4.3 3.0 4.8 4.5
##  [685] 6.2 3.4 4.0 6.1 6.0 4.9 3.5 3.9 5.3 5.5 4.7 5.1 5.9 4.2 4.0 4.4 5.1 5.1
##  [703] 4.2 5.5 4.2 4.7 3.3 5.5 3.3 3.5 5.2 3.6 5.8 5.4 5.9 4.5 3.3 3.4 2.5 4.7
##  [721] 5.0 4.4 4.2 5.9 5.5 2.6 5.5 4.7 3.5 4.6 4.3 4.1 2.5 3.1 5.3 3.6 5.5 3.4
##  [739] 5.2 3.6 5.4 4.8 5.0 4.5 5.9 3.4 4.5 5.2 5.1 5.2 4.5 3.7 3.9 2.9 4.4 5.2
##  [757] 4.5 4.5 3.7 3.4 5.3 5.5 5.3 4.4 6.7 3.0 5.3 4.2 4.4 4.4 5.1 4.5 4.0 5.0
##  [775] 5.0 3.9 3.9 5.6 3.8 5.1 3.8 4.8 6.7 3.3 4.5 3.7 5.0 4.3 5.2 4.7 4.0 3.5
##  [793] 4.0 4.5 4.3 5.5 3.7 4.9 5.2 4.5 5.2 2.9 3.7 3.9 5.0 4.2 4.2 3.7 5.2 4.3
##  [811] 5.4 2.8 5.9 4.7 3.5 3.4 5.3 4.5 2.6 4.4 4.0 3.7 3.8 4.8 4.1 5.0 4.0 4.7
##  [829] 3.4 4.2 3.6 4.2 4.4 4.2 4.6 3.8 3.0 6.0 5.8 3.8 4.7 3.4 3.1 5.8 3.9 5.5
##  [847] 5.1 4.0 4.2 5.0 3.8 5.7 3.8 3.8 5.4 5.2 3.5 5.0 3.1 3.8 3.8 4.0 3.6 5.0
##  [865] 4.9 3.3 5.2 5.8 4.6 3.6 4.8 4.2 4.0 4.0 3.5 5.8 4.3 4.1 4.7 5.5 3.4 4.4
##  [883] 5.7 5.1 4.3 3.5 5.7 5.4 4.5 3.7 3.2 4.3 4.5 5.0 4.0 1.8 3.4 5.5 4.0 6.6
##  [901] 4.1 3.5 3.8 3.1 4.2 5.9 4.9 5.4 4.2 4.2 5.6 6.2 3.4 5.9 5.4 4.9 4.5 5.2
##  [919] 5.2 4.6 3.5 4.5 4.2 3.6 3.7 5.1 5.2 3.6 3.7 5.7 5.6 5.2 3.9 4.4 4.0 4.6
##  [937] 5.6 5.0 3.6 4.9 3.9 5.9 4.9 5.5 3.9 3.9 3.4 3.8 4.8 5.4 4.4 4.3 4.7 4.6
##  [955] 4.6 4.9 5.0 4.5 3.9 3.8 5.3 6.5 3.4 4.5 3.6 3.1 2.7 3.3 5.0 5.8 4.6 4.1
##  [973] 4.2 4.4 4.7 5.1 3.0 5.7 3.9 4.9 3.5 6.1 4.2 5.3 4.5 4.4 4.8 5.8 3.2 3.7
##  [991] 4.1 4.3 5.0 4.0 5.1 4.9 4.6 5.1 4.2 4.1
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
## 2.8975 6.3000
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
##    [1] 4.0 3.4 6.2 4.8 5.0 4.3 3.5 3.6 3.0 5.0 4.4 4.5 4.5 3.8 4.4 4.4 5.0 4.6
##   [19] 4.3 4.0 5.4 3.8 3.9 4.8 5.9 3.0 4.0 3.8 5.0 4.0 4.2 4.9 4.3 5.3 4.7 4.2
##   [37] 3.0 6.4 2.2 4.5 4.3 5.0 5.2 5.1 3.2 4.3 4.4 3.8 4.4 4.4 6.4 4.9 4.7 4.9
##   [55] 5.3 4.2 5.8 3.7 5.4 4.8 3.8 4.3 5.0 4.4 3.5 3.9 3.8 4.5 5.3 5.7 2.8 3.2
##   [73] 4.6 4.3 3.6 4.7 4.5 4.2 3.4 3.2 4.8 3.1 3.1 5.9 4.6 5.5 3.9 3.4 4.7 3.2
##   [91] 5.3 3.8 5.7 4.2 3.0 4.3 5.0 2.7 5.3 5.4 4.6 4.1 5.7 4.3 6.3 4.7 4.7 4.9
##  [109] 3.3 3.8 5.9 5.0 4.4 4.1 4.6 4.6 3.9 3.8 5.6 4.9 3.9 5.2 4.5 3.9 4.0 5.4
##  [127] 4.0 3.9 4.2 3.6 4.7 4.9 5.5 4.1 5.2 3.6 4.3 4.1 2.8 4.6 4.7 4.6 5.3 4.3
##  [145] 5.1 4.2 4.3 3.8 4.2 5.2 5.4 5.1 3.7 5.4 4.4 5.5 4.9 5.1 4.3 6.4 5.1 5.4
##  [163] 5.2 5.0 5.3 5.1 3.7 4.0 4.9 4.7 4.6 6.4 3.7 3.7 4.4 5.0 4.1 3.0 3.8 4.1
##  [181] 4.6 4.5 4.2 5.6 4.7 3.8 3.5 5.4 3.6 3.5 5.6 4.8 4.8 4.6 4.1 4.3 5.1 3.8
##  [199] 5.2 4.7 4.2 6.4 5.7 4.2 4.4 5.5 4.3 5.1 3.9 4.9 5.2 4.3 3.8 4.7 4.7 3.9
##  [217] 5.6 4.4 3.9 5.1 6.8 6.0 2.9 5.5 2.7 3.8 3.4 3.7 3.3 4.1 4.6 4.6 3.6 5.9
##  [235] 5.8 3.8 3.4 4.9 6.1 6.0 5.5 4.9 5.0 4.5 4.4 4.4 4.9 3.8 4.9 3.0 4.0 5.5
##  [253] 2.6 4.5 5.7 4.6 5.2 4.5 3.0 5.1 6.6 4.5 4.8 4.8 6.8 4.5 4.3 2.9 4.8 4.8
##  [271] 3.5 5.2 4.1 3.7 4.5 3.6 5.1 5.0 3.1 4.7 3.8 3.3 3.5 4.7 4.0 3.5 6.1 3.7
##  [289] 4.8 5.6 2.8 4.2 3.6 5.3 5.5 3.3 4.4 3.6 4.7 4.8 3.8 4.3 4.1 5.1 5.4 3.1
##  [307] 4.0 5.8 4.7 6.0 4.4 2.3 4.0 5.9 4.1 3.7 4.7 4.6 4.4 3.7 3.6 3.7 4.7 5.4
##  [325] 4.0 5.3 3.3 4.1 3.0 5.0 3.0 5.5 3.8 2.9 4.1 4.6 4.3 6.2 4.9 4.3 6.0 4.1
##  [343] 3.3 4.0 4.7 5.8 4.2 2.9 3.7 3.1 6.0 5.5 3.6 4.9 4.2 5.0 4.8 4.5 4.3 5.0
##  [361] 6.4 4.2 4.6 3.6 6.5 5.0 4.2 3.9 3.6 3.1 4.5 4.6 3.6 6.3 2.7 4.3 4.1 2.7
##  [379] 5.7 3.9 4.0 4.7 6.3 5.4 5.5 4.0 4.3 5.6 4.9 5.1 4.7 3.9 5.1 2.3 5.6 4.3
##  [397] 3.4 6.5 3.7 5.2 5.1 4.4 4.7 4.2 5.9 3.5 4.6 5.1 3.7 4.8 6.5 3.1 5.0 4.3
##  [415] 5.4 5.2 6.0 4.6 4.8 5.4 3.7 5.5 3.5 6.2 2.8 4.8 4.6 3.8 3.4 4.3 3.0 4.1
##  [433] 4.6 5.3 4.5 4.5 4.3 4.9 4.6 4.7 5.6 3.7 6.0 5.0 3.9 4.1 5.4 5.6 3.8 5.9
##  [451] 3.9 3.2 5.6 5.0 5.2 4.9 4.2 5.0 4.1 4.0 4.2 5.6 4.5 5.7 4.2 4.9 5.2 5.8
##  [469] 3.4 4.8 5.4 4.8 5.3 3.6 6.3 5.7 5.1 4.4 3.5 5.1 2.8 4.4 5.2 5.0 3.3 4.4
##  [487] 3.7 4.7 5.2 2.2 4.5 6.0 4.0 5.2 4.2 6.0 3.3 4.9 3.7 6.0 5.2 4.6 3.9 4.0
##  [505] 5.3 7.2 4.3 4.2 3.4 4.8 5.3 6.0 4.6 4.0 2.8 4.3 4.6 5.7 3.8 4.5 3.2 3.5
##  [523] 4.6 3.7 4.9 1.9 2.0 2.2 4.9 3.6 5.0 5.5 3.3 2.9 4.8 4.5 5.4 4.4 3.8 3.5
##  [541] 4.0 4.1 5.3 3.8 3.8 4.7 5.2 4.4 2.7 5.2 4.5 5.3 3.5 4.8 5.1 5.3 5.9 4.2
##  [559] 4.1 6.0 3.9 3.9 5.2 5.7 4.3 4.6 4.9 6.4 4.0 6.0 4.4 5.4 4.7 4.7 3.1 5.1
##  [577] 4.5 2.3 4.5 5.4 4.6 4.7 6.8 4.5 4.8 5.6 2.6 4.1 4.5 3.4 6.5 4.6 5.8 5.9
##  [595] 3.2 5.3 4.9 6.0 3.2 5.9 5.4 5.0 5.3 4.3 4.8 5.2 5.5 4.6 3.9 2.9 4.2 5.3
##  [613] 7.0 3.7 4.0 3.2 5.7 3.7 5.9 5.4 4.8 6.0 3.6 2.8 4.3 5.2 4.0 4.3 3.7 4.1
##  [631] 3.8 3.7 4.6 4.5 3.3 5.5 5.7 4.6 5.0 5.6 5.3 5.4 4.1 4.3 5.7 4.1 3.5 5.1
##  [649] 5.1 5.8 6.6 5.4 4.5 4.3 3.6 4.8 4.4 3.1 3.3 3.7 5.4 3.7 4.1 6.0 4.4 3.8
##  [667] 4.8 4.6 4.2 4.0 4.3 4.2 6.6 4.9 3.4 4.1 5.2 5.4 3.7 3.5 5.5 2.9 4.3 5.0
##  [685] 4.1 3.5 4.1 5.2 3.5 4.0 3.3 5.0 4.7 4.3 3.8 4.4 4.0 3.9 4.8 4.6 4.0 4.8
##  [703] 4.0 3.9 5.1 4.8 4.5 4.0 3.5 3.6 5.7 2.9 4.9 4.8 3.5 4.4 4.5 3.9 4.8 5.4
##  [721] 3.1 5.5 5.1 5.3 4.0 3.4 5.2 6.2 5.1 5.3 4.0 4.0 5.4 3.8 5.6 5.2 5.2 4.5
##  [739] 1.9 4.5 3.6 4.3 4.4 3.6 4.3 4.7 5.0 4.9 3.1 4.7 4.5 5.2 4.4 6.3 4.4 4.4
##  [757] 4.6 4.5 4.7 4.9 4.5 4.6 4.8 5.1 5.5 5.4 3.8 5.0 5.2 4.1 6.1 5.2 4.6 5.0
##  [775] 4.1 5.9 3.6 5.3 3.9 3.9 3.0 4.9 5.1 4.9 4.2 5.1 5.1 4.4 6.6 3.3 5.3 4.6
##  [793] 5.1 5.7 5.4 4.6 4.7 4.7 3.4 4.7 4.7 3.2 4.8 5.5 4.3 4.8 4.1 4.2 5.2 4.0
##  [811] 5.8 4.5 4.9 4.6 4.5 4.7 6.1 3.8 3.2 3.5 4.1 6.4 4.7 5.2 3.9 3.7 5.6 5.7
##  [829] 3.6 4.7 4.3 4.8 5.6 3.4 4.3 6.2 3.9 6.1 4.1 3.9 4.9 3.8 4.3 4.3 3.4 3.4
##  [847] 4.2 5.2 5.1 4.8 4.8 3.2 5.9 5.3 3.9 7.4 5.4 2.9 4.9 4.9 4.2 6.2 3.9 4.7
##  [865] 4.7 4.0 3.2 5.5 5.2 4.5 4.8 3.7 2.6 4.3 4.6 4.2 3.5 2.8 4.7 3.1 3.0 4.3
##  [883] 5.0 3.4 5.5 4.4 2.9 5.1 3.9 4.6 4.6 3.9 3.4 4.4 4.3 2.9 2.9 3.6 3.1 5.7
##  [901] 4.7 4.9 4.5 3.2 4.5 3.0 2.8 4.4 5.6 3.9 3.9 5.8 3.9 3.9 4.3 4.0 5.3 3.1
##  [919] 5.6 2.8 5.3 4.1 3.1 6.4 3.3 4.9 4.9 4.9 3.7 5.0 3.3 5.2 3.7 3.9 6.2 3.3
##  [937] 3.0 3.2 5.0 4.1 5.0 4.9 5.5 5.5 5.0 4.2 4.3 3.8 3.4 5.3 4.4 4.1 4.9 4.1
##  [955] 3.5 4.2 4.2 6.2 3.5 4.6 4.5 3.1 4.6 4.5 3.2 3.4 3.4 4.4 5.5 4.2 4.0 5.0
##  [973] 4.1 4.2 4.0 5.8 4.2 4.2 5.2 4.1 5.4 6.0 2.0 5.3 5.8 6.2 5.1 5.8 3.9 4.2
##  [991] 4.8 4.1 5.3 3.1 4.2 5.0 4.7 3.9 4.5 3.2
## 
## $func.thetastar
## [1] 3e-04
## 
## $jack.boot.val
##  [1]  0.43418803  0.41189802  0.26023055  0.20088757  0.05994318 -0.13519553
##  [7] -0.12132565 -0.30783133 -0.39106628 -0.48811594
## 
## $jack.boot.se
## [1] 0.9430754
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
##    [1] 5.3 5.9 4.4 5.2 5.7 4.1 3.8 4.2 5.1 4.1 5.6 5.7 3.6 4.4 5.1 4.7 6.8 3.1
##   [19] 5.7 5.9 4.9 4.4 3.1 5.5 5.0 5.4 4.8 4.3 3.8 3.9 4.3 2.9 4.4 4.8 4.5 4.2
##   [37] 5.8 5.2 4.6 5.1 3.1 5.1 4.9 4.3 5.5 6.0 6.6 6.0 5.5 5.1 6.4 3.9 4.5 5.7
##   [55] 4.6 4.6 4.4 3.9 4.8 5.3 3.6 6.1 4.8 3.1 4.7 5.2 5.4 3.9 4.5 5.9 4.9 4.8
##   [73] 5.8 3.7 3.6 5.5 4.6 5.0 4.8 5.2 4.3 5.9 3.3 5.6 4.6 5.6 4.2 4.2 4.2 3.8
##   [91] 5.1 3.1 2.7 5.3 5.0 3.3 5.5 4.3 3.0 4.2 3.9 3.3 5.1 3.8 3.0 4.5 6.0 5.1
##  [109] 1.5 4.1 5.4 4.4 5.1 3.4 4.8 4.8 5.3 4.5 5.2 4.0 4.8 3.0 4.2 4.9 3.8 5.2
##  [127] 4.7 3.8 4.1 5.1 4.5 5.1 5.3 4.6 5.4 4.6 3.7 5.2 3.6 6.0 3.5 4.7 5.5 5.0
##  [145] 5.1 4.1 5.0 5.8 4.6 4.9 4.5 5.4 3.0 4.6 4.5 4.2 3.7 5.2 5.9 4.2 3.9 4.0
##  [163] 4.5 5.3 4.6 5.3 4.3 4.7 4.5 4.1 6.3 5.5 4.0 4.5 4.6 5.6 5.6 5.2 5.3 6.2
##  [181] 3.4 4.8 4.0 4.6 4.5 4.9 4.8 3.8 4.1 3.1 3.7 4.3 5.8 3.9 3.4 4.7 4.9 5.4
##  [199] 5.2 4.1 2.7 5.4 3.9 2.8 4.7 5.1 3.9 3.7 4.4 5.5 3.6 4.1 4.4 3.2 4.6 3.9
##  [217] 3.6 4.7 2.2 4.9 5.2 4.2 3.8 3.0 4.1 5.4 4.6 3.9 4.1 2.8 3.5 5.4 4.7 4.9
##  [235] 4.8 4.3 5.9 4.4 3.7 2.7 5.7 5.1 5.1 4.3 4.1 4.5 5.2 4.9 4.1 3.7 4.4 4.6
##  [253] 4.8 5.0 3.8 3.2 5.4 4.1 5.5 3.4 5.6 5.1 5.1 5.2 4.9 4.2 3.6 3.1 5.3 4.0
##  [271] 5.3 4.8 5.0 4.2 2.0 2.2 3.7 4.4 5.1 3.1 5.3 4.4 5.2 1.8 4.6 4.2 5.1 3.9
##  [289] 5.8 5.0 4.7 6.0 4.5 4.3 4.7 5.1 4.5 4.4 3.6 5.6 4.6 3.8 5.8 3.6 3.7 3.3
##  [307] 4.5 5.5 3.6 3.1 3.8 4.4 4.8 4.1 3.9 5.0 4.2 3.8 4.8 5.2 3.7 5.8 2.5 3.7
##  [325] 4.4 3.3 3.8 4.4 4.0 4.8 5.7 4.7 5.1 4.1 5.1 5.9 5.3 4.6 4.0 5.4 3.9 4.3
##  [343] 4.7 4.9 4.1 6.4 5.3 6.0 3.2 4.3 3.8 5.2 4.9 5.1 4.2 1.9 3.8 4.2 3.7 4.5
##  [361] 3.9 5.5 3.5 3.8 4.3 3.5 4.5 5.2 6.1 5.6 5.2 4.5 6.2 4.1 4.2 3.9 3.4 4.3
##  [379] 5.1 3.6 2.2 4.0 3.8 4.5 5.8 6.1 5.0 4.6 4.2 5.2 4.6 4.4 3.6 3.8 4.7 5.2
##  [397] 5.1 5.3 3.5 4.8 4.5 4.7 5.3 4.3 4.2 4.4 5.4 4.8 5.4 4.6 4.6 4.3 4.6 3.7
##  [415] 3.7 4.2 4.7 4.0 6.0 5.5 5.3 3.9 4.3 6.6 3.4 5.2 3.4 4.7 4.9 4.7 3.3 5.1
##  [433] 4.9 3.3 3.4 4.4 4.4 3.5 4.3 4.1 5.6 5.0 4.7 3.1 2.6 5.4 6.7 4.7 3.9 3.2
##  [451] 4.3 4.4 2.9 3.4 7.3 4.4 5.2 4.2 4.9 3.7 4.2 5.5 3.9 4.8 3.0 3.1 6.2 4.3
##  [469] 4.0 5.0 5.6 5.1 4.0 3.4 3.4 3.5 6.2 4.9 3.9 6.1 5.6 5.4 4.0 4.2 4.0 4.3
##  [487] 6.6 3.5 4.5 5.6 5.0 4.7 5.8 4.7 3.3 4.4 4.2 3.7 5.1 4.8 4.5 5.9 4.2 3.2
##  [505] 4.8 3.8 3.9 4.7 4.3 5.7 5.2 4.7 4.2 4.8 4.3 4.5 2.6 5.1 4.5 5.2 3.3 5.5
##  [523] 4.0 4.3 3.5 3.7 3.5 4.6 3.6 2.8 3.8 2.9 3.9 3.2 7.0 4.6 4.5 4.1 3.9 5.4
##  [541] 3.8 6.3 5.5 4.3 3.4 4.9 3.5 4.9 5.1 3.0 7.0 4.6 5.4 3.0 4.2 5.4 4.6 4.2
##  [559] 3.4 5.0 5.4 5.2 5.8 6.1 4.9 2.8 5.9 5.8 5.3 3.5 6.4 4.8 5.5 5.2 5.4 3.4
##  [577] 3.9 4.4 5.0 2.5 3.7 4.3 4.1 5.6 4.2 6.8 4.6 2.7 4.0 5.0 4.4 4.5 4.9 3.3
##  [595] 4.1 3.9 3.5 3.7 3.2 4.2 4.5 4.7 5.7 4.7 5.2 3.9 4.0 3.1 5.0 5.5 3.0 4.8
##  [613] 5.6 2.7 3.8 4.7 5.5 4.3 2.4 5.0 5.7 3.9 4.3 4.7 5.7 4.2 5.0 3.7 3.6 5.0
##  [631] 3.7 3.6 5.6 5.2 5.2 4.9 4.7 4.3 4.3 4.0 2.7 5.4 5.1 4.2 3.0 4.2 4.5 5.2
##  [649] 3.6 4.5 3.9 3.7 4.5 4.1 3.6 3.6 5.1 4.1 5.4 5.2 4.4 4.3 4.6 4.4 5.8 4.0
##  [667] 4.4 5.3 6.0 3.2 4.3 4.5 5.0 4.7 5.0 3.9 4.6 4.2 4.0 4.1 5.2 5.1 5.4 4.2
##  [685] 4.4 3.6 4.8 4.0 4.7 4.8 6.2 3.4 4.8 3.6 3.8 3.4 5.8 4.3 4.7 4.0 3.4 4.6
##  [703] 3.9 4.0 2.8 5.9 3.6 4.0 3.1 4.8 3.4 4.6 5.5 4.6 5.4 5.5 5.1 5.2 5.3 4.3
##  [721] 4.3 3.6 3.6 4.4 3.9 5.4 4.1 5.1 5.2 5.7 5.3 3.5 3.6 5.1 3.9 2.8 5.5 5.3
##  [739] 6.1 4.4 4.8 5.4 4.2 4.7 4.0 3.8 4.5 4.9 3.9 5.6 4.6 5.0 6.0 4.4 4.7 5.2
##  [757] 4.0 3.9 5.5 4.8 3.3 5.4 5.3 4.2 4.8 6.6 2.9 4.3 3.0 5.2 4.4 5.2 5.1 4.5
##  [775] 5.6 3.8 4.4 4.8 4.6 4.9 4.8 4.0 4.6 4.4 5.3 2.5 4.2 4.5 5.1 4.2 3.4 7.1
##  [793] 4.8 4.5 4.7 5.1 4.2 5.3 5.9 3.6 3.4 4.4 4.8 5.6 2.8 4.0 4.4 5.4 4.5 6.2
##  [811] 2.4 4.6 4.5 5.3 2.7 5.0 4.2 4.7 4.7 5.5 4.3 4.2 4.8 4.6 3.6 2.3 4.8 3.5
##  [829] 5.4 3.5 3.9 3.9 5.1 5.9 5.3 5.2 2.6 3.8 4.3 4.1 4.4 4.4 4.4 5.0 4.5 3.6
##  [847] 6.0 4.4 4.8 3.4 5.7 5.1 4.8 4.3 4.3 4.0 4.8 4.0 3.6 5.3 5.9 4.1 4.6 5.7
##  [865] 5.1 3.7 6.6 3.2 5.7 3.8 6.4 4.7 5.1 6.7 6.2 4.5 5.1 2.9 2.8 5.2 3.4 4.8
##  [883] 4.9 5.4 2.7 4.7 4.6 5.4 5.4 3.8 4.6 6.1 5.3 4.4 4.3 4.0 5.7 5.6 3.8 5.5
##  [901] 4.0 4.9 3.8 5.4 4.4 4.4 5.5 5.7 3.7 5.1 6.3 4.7 4.1 3.6 4.8 5.4 3.8 4.4
##  [919] 3.8 6.0 4.4 3.2 4.6 5.7 3.6 4.4 4.6 4.6 4.4 5.0 3.4 5.1 6.0 5.9 4.6 5.1
##  [937] 5.0 5.0 4.0 4.5 4.6 6.0 5.1 5.6 5.1 5.6 4.5 5.3 2.3 4.0 3.9 4.2 4.0 3.6
##  [955] 5.4 3.2 4.1 4.3 3.6 5.6 5.3 5.3 5.8 3.5 5.7 4.7 5.2 3.7 3.7 5.0 5.3 5.8
##  [973] 5.1 6.4 5.1 4.0 4.6 4.7 4.5 4.3 4.7 4.1 4.5 5.2 4.3 4.2 3.6 5.6 6.4 3.5
##  [991] 5.7 3.4 5.0 4.3 3.9 3.9 4.5 4.7 3.2 3.6
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.4 5.4 5.3 5.2 5.2 5.0 5.0 4.8 4.7 4.5
## 
## $jack.boot.se
## [1] 0.8720665
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
## [1] 0.6314138
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
##   3.231157   3.910066 
##  (1.376952) (1.802668)
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
## [1] -0.02388295  0.09503674 -0.17987893  0.25442420  0.68881298 -0.17048465
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
##    [1]  2.1792409439  0.2688036944 -0.3611821912 -0.1069440917  0.4115380944
##    [6]  0.6492466471  0.3123788200 -0.3748148061 -0.2071683373  1.0182934850
##   [11]  1.5742421331  2.4074138471  0.7055269963  0.5401775805  0.6813493365
##   [16]  0.6185107013  0.6726075391  1.5085098508  0.6285126175  0.2548161764
##   [21] -0.7187853190 -0.1127783281  0.0725682798  0.3216688813  0.0255703790
##   [26]  0.6069762547  0.2207065590  1.4753590742  1.4496151646  0.5036807654
##   [31]  0.1968177672  0.1942125436  0.6246553236  0.1961290902  0.7638493523
##   [36]  1.4753419033  0.0677885155 -0.4751421856  0.9432309487  1.3310653203
##   [41]  1.3311026520  1.2237536357 -0.5263379630  0.9010102550  0.4447786256
##   [46]  1.2703063683  0.1784486258  0.7089778751  0.5893927778  0.5162752100
##   [51]  0.2159428107  0.2505460291  0.6926207089 -0.2648799893  0.2825889029
##   [56]  0.7470216324  0.2860190562  0.0620463397  0.6503618388  0.7194391144
##   [61]  0.6778730447  1.0620629290  0.2492700550  1.0005313037  0.6383813601
##   [66]  1.9844289042  0.5342311828  1.3492103820  0.5120061478  0.3982671737
##   [71]  0.7549699031  1.0640014035  0.0200452749  0.3884209385  0.7026547304
##   [76]  1.0512845354  0.7467014319  0.8284276170 -0.3761811480  0.9321430149
##   [81]  0.2945410608 -0.3378161797  0.0770383122 -0.1458622278  0.4759517073
##   [86]  0.5216380058  1.5255027884  0.8122819554 -0.4147338143  0.4005876128
##   [91]  0.7700041908  0.1908510309 -0.0009615900  0.2411878871  1.0510188741
##   [96] -0.7979181772  1.4149025564 -0.5019170445  0.2224898040  0.6618357497
##  [101]  0.8746821718  0.1030247171  1.1298001382  0.9415900340  1.0946576024
##  [106]  0.5357411270  0.6672391030  0.6447933452  0.7170448474  0.2401065088
##  [111] -0.5314628480  1.0745752596  0.8314417448  0.5549953737  2.4151552935
##  [116]  0.5218951202  1.6525635169  0.0766340337 -0.0511156166  0.8410035930
##  [121]  1.0228048218  0.3290166850  0.7300191232 -0.4382599486  2.2716228599
##  [126]  0.5189429712  0.2658934303  0.7932165053  0.1739775003  0.0319326176
##  [131]  1.1008878208 -0.0015806311  0.9263486992  0.1365502393  0.7869419529
##  [136]  1.0275308653  0.5445410982  0.9238067804  0.2690016826  0.6643805844
##  [141]  0.7637093557  1.2194271284 -1.4215367536  0.7001664937  2.2401986339
##  [146]  0.7697958518  1.6363249636  0.1621122392  1.1821251723 -0.5906923934
##  [151]  0.9869035963  0.6723553244  1.1436619589 -0.1494681446 -0.4786959646
##  [156]  0.2736831880  0.8781452315  0.2667822798  0.3000861080  0.6274124912
##  [161]  1.0732567968 -0.0308543060  0.4989756984  0.3841599006  0.2073194538
##  [166]  1.4557078711 -0.2916748438  0.2166514546  0.1850596285  0.3878116713
##  [171]  0.5543412449  0.6720343040  0.8167031860  0.5365303411  0.0606050096
##  [176]  0.9687047524  0.6701429177  0.0592874207  0.1353339697  1.5193046540
##  [181]  0.0869346640  0.5459875807  0.6969761341  0.1157571697  1.0897167045
##  [186]  0.5928106650  0.2950671381  0.4848677990  1.3979548922  1.2986973794
##  [191]  0.3576911404  0.9073104156  0.0568412294  0.6254719761 -0.5716709601
##  [196]  0.3710623154  0.7456983986  0.4144354503  0.3365117071  0.3651847855
##  [201]  1.1091101944 -0.0824076050  1.0450957525 -0.2120165616  0.5311538302
##  [206]  1.4139846797  0.5684330841  0.0743822340 -0.0632230620 -0.2401581347
##  [211]  0.7173584035  0.2491101830  0.8563442490  1.3124465684 -0.1253593661
##  [216]  1.1576623185  1.5356072372  0.7585679874  0.2733705589  0.6422862330
##  [221]  0.1204820503  0.7237242526  1.5580813414  0.3098129839  0.5637753964
##  [226]  0.5633826690  0.1476927941  0.8789963207  0.2874911757  0.2461311255
##  [231]  0.8377485961 -0.5325494034  0.4177337268  0.3376710919  0.2008789497
##  [236]  0.6007145205 -0.3107920687  0.6720302992  1.0670598176  0.3862500177
##  [241]  1.1738669122  0.3495012701  0.5098571668  2.1111204140  0.5633826690
##  [246]  0.2433699426  0.2699476777  0.9151295715  0.1700267973  0.3430218815
##  [251] -0.1880571631  0.9339153371  0.9549844953  0.1148865374  0.6890384223
##  [256]  0.6581829676 -0.1983972301  0.6629592345 -0.3761892996  0.6723553244
##  [261]  1.0034420387  0.2230941354 -0.3866383047  0.4992338638  1.1042213503
##  [266]  0.3022473314  0.4298787841  1.3466708457  0.3239213238 -0.1346822781
##  [271]  1.3141193370  0.2354804830  2.4023988790  0.4293414979  1.6387864526
##  [276]  2.0926999782  0.2284632674  0.4089440883  1.3429895019  0.6482138279
##  [281]  1.6588550354  0.0298776564  1.1130601978 -0.4365130858  0.9214204571
##  [286]  1.2324324675  0.5877931424  0.9511221073  0.1889325621 -0.1754356703
##  [291]  0.8626950271  0.7237216421  0.4260035497  0.2806408157  0.7455694011
##  [296]  0.9175680254  0.0658853959  0.1952777718 -0.2891830149  0.2140982107
##  [301]  0.0922856838  1.0645755351  0.6598104384  1.0565562270 -0.2736534897
##  [306]  0.5586199167  0.3268673744  0.3855235094  0.2802282472  1.6073385367
##  [311]  0.0718926947  0.2555213354  1.0801097165  1.1009752068  1.1593515339
##  [316]  1.4141840962  0.2575937154  0.9288235454 -0.3427276279  0.4088115341
##  [321] -0.3123052215  0.8422151524  1.3629011455  0.2053784362  1.3406918543
##  [326]  0.4430060314 -0.5265764354  0.5014011191  1.7792264486  1.3374569036
##  [331]  0.4391140025  0.8458992282  0.5957319518  0.3456524429  1.1376347253
##  [336]  0.9093829048  1.2001166654 -0.1156132177  0.1651235810  0.7877498283
##  [341] -0.1297586726  0.2918778955  1.1212716118  0.1184259152  0.6512177843
##  [346]  0.7875615605  0.2327349088  0.2236343252 -0.0310060577  0.9868838060
##  [351]  0.4113525736  0.9171014901  0.0124072909  1.0819700915  1.1194305868
##  [356]  1.1824412893 -0.1497589418  0.5248283606  0.2532052430 -0.2301332435
##  [361]  1.0593476557  0.1647689126  0.1582405534  0.5372128301  0.3235444712
##  [366]  0.3754996866  1.0507747619  0.3414296124 -0.5384236700  2.3743361575
##  [371]  0.3657038508  0.9147838113  0.7374750399  1.1747339207  0.2545258185
##  [376]  0.0313821287  1.0091990089  0.3675276445  0.4987870888  0.8634938657
##  [381]  0.5555276288  0.4139365165  0.1806513696  0.6201497102  0.9583972782
##  [386]  0.6362438347  0.9697138670  1.5253410035  0.6554415273  0.1204474310
##  [391]  0.1590199218  0.7326052619  0.2631311318  0.6740155730  1.5270075203
##  [396]  0.7055944159  0.3495012701  0.5531631591  0.1957785253  0.5311643962
##  [401]  0.4384074911  1.4303163895  0.4959753563  0.4333001621  0.4918491622
##  [406]  0.3117610716  0.6513497735  0.1653517150  1.1193447949  0.2446341120
##  [411]  0.7728465690  1.1566283311  0.6326429611 -0.5171940221  0.6361090700
##  [416]  0.7467121557 -0.1538755622  1.0503109821  0.2391376772 -0.3428358534
##  [421]  0.2544194623  0.6320908796  0.9234396838  0.9936366022 -0.2765810313
##  [426]  0.3247004373  0.8543272489  0.3764494032  0.9602713240  1.2997211345
##  [431]  0.5667743625  1.0171787771 -0.3930233233  1.0741709951  0.1099234536
##  [436]  0.0567012355  0.9715808637  0.3863544541  0.8127855993  1.0449441354
##  [441]  0.9931226969  2.1142937910  1.3018044205  0.5395038174  0.6832667586
##  [446]  0.5824928140  0.1310868627  0.6213578400  0.0768683728  0.6284908395
##  [451]  0.5683349590  0.5571451081  0.4090115313 -0.1594019526  0.4145389018
##  [456]  0.6756783807  0.4564961389  0.3273057827  0.0726954527  0.3647855523
##  [461] -0.2828135081  0.8082326190  1.8140148785  1.4805074785  0.6300373957
##  [466]  0.2301699554  0.5718152114  0.6400990685  0.1882019241  0.9707580171
##  [471]  0.4293513462  0.5686247467  0.0031603323  0.5207376803  0.9728765583
##  [476]  0.2411801434  0.5683986882  0.6619374207  0.8851499188  0.7098143374
##  [481]  0.5999871947  0.3817247472  0.2034820490  0.9550507836  0.9196422984
##  [486]  1.5588154892  1.3202941883  0.7611374516  0.1667150047 -0.0931751849
##  [491]  0.8092090525  0.5206874806  0.9262444764  0.2182361613  0.2598332867
##  [496]  0.8692840944  0.0298731370  2.2234299564  0.3384441562  0.8756502591
##  [501]  0.2494957470  0.2455026441 -0.1731130628  0.8101447429  0.7068365371
##  [506]  0.9023221492  1.1083899552  0.1741967407  0.4270477865  1.2571381664
##  [511] -0.0448764176  0.4263178130 -0.2051025198  0.2947012795  1.1572309844
##  [516] -0.0688759049 -0.2659108186  0.6200613566  0.6474073046  0.8146543231
##  [521]  0.4222952986  1.3013335798  0.3901289127 -0.4019646361  1.2161111942
##  [526]  0.1133437791  0.4591814835  0.6172857845 -0.0983964803  1.1071400277
##  [531]  1.9770333153  0.3713277326  0.9328501957 -0.2314799087  0.6191519580
##  [536] -0.0062115702  0.6223973129  0.5902030070  0.2173704749  0.9698007019
##  [541]  0.2655123754 -0.3574800916  1.0619490570  0.0731428150  0.5952709918
##  [546]  0.5116092780  0.3642157237  0.8071993879 -0.3843087114  0.5463994196
##  [551]  1.0628640432  0.9024819324  1.5141542619  0.0045859701  0.7772630449
##  [556]  0.1731482952  0.2703984053  0.5038391126 -0.0878138288  0.4645419877
##  [561]  0.2424892680 -0.0277754950  1.3670827069  0.6133968008 -0.0034085042
##  [566]  1.0859298380  0.7868756346  0.1555705303  0.0275787112  0.0719761649
##  [571]  0.3444584936  0.6376198935  0.5457462386  0.1743878198  0.0023446404
##  [576]  1.0244666726  0.0035396637  0.9834280741 -0.5672703185  0.3909836805
##  [581]  0.2188363524  0.7931848732  0.5379845650  0.0360939850 -0.5826483573
##  [586]  0.5384363935  0.3272718639 -0.1557355321  0.8123927983  0.3021759709
##  [591] -0.3304933271  0.6440355859 -0.8460106513  0.7374780174  0.4170610720
##  [596]  1.1935412615  1.3651235040  0.9550838751  0.6357804870  0.6944443471
##  [601]  0.5534531175  1.9643854676 -0.0178519473  1.0473585035 -0.6289632664
##  [606]  0.5367000740  0.5121807708  0.6600979178 -0.0036678022  0.5248166595
##  [611]  0.9550507836  0.2733705589  0.1121344630  0.1881139778  0.3660203268
##  [616]  1.2984560939  1.9353440947  1.2477648639  0.7759060356  0.8448547239
##  [621] -0.3877675938 -0.5280903039  1.0484100546  0.4633604362  0.9775632781
##  [626] -0.2717370543  0.0725682798  1.1538819347  0.8261391196 -0.0452780074
##  [631]  0.3468172198  2.3501156024  0.4958307543  0.0554020238  1.0437451063
##  [636]  0.5569566879  0.7249319818  0.7894102913  1.0776767654  1.2492298361
##  [641]  1.4051406120  0.7870880171  0.4916452165  0.3071593840  0.6711992755
##  [646]  0.0726954527  0.8308936937  1.8358887883  0.8151685087  0.1614892217
##  [651] -0.1185869460  0.3694506020  0.5662904933  0.6762116421 -0.4025608399
##  [656]  0.2495383335 -0.3524899631  1.0510417536  0.4565370250  0.3338504957
##  [661]  0.0828048643  0.6258999104  0.0294287436 -0.0152453261  1.2696990476
##  [666]  1.0628640432  1.6497527585  1.8118561878  1.2272934596  0.6130081826
##  [671]  0.1873011610  1.0932120513  0.3893570737 -0.2908217693  0.6613289791
##  [676]  0.5333273923  0.8972597401  1.1249665707  0.2973064021 -0.1878653854
##  [681]  1.0546766793  1.2182746132  0.7580001250  0.2800246616 -0.0872991408
##  [686]  0.6850291200  1.4737402948  0.4446474466  0.2110281061  1.5104711048
##  [691]  0.3290497985  0.1853230385  0.1473371251  1.6742670335  0.7286803389
##  [696] -0.0031530032  0.2474645102  0.1692187552  0.5124660827  0.4218070894
##  [701]  0.2645865820  0.3472282684 -0.5761369449  1.0206365003 -0.2484211133
##  [706]  0.7456928419  0.2693360588  0.1311743448  1.8908967862  0.6208060822
##  [711]  0.7127572806  0.7923145217  0.0798711770  0.8017536165  0.8165762794
##  [716]  0.9502777912  0.8823013304 -0.2072456136  1.0012349804  0.4199539258
##  [721]  0.8922172217  2.0435360357  0.2624796105  0.2543601459  0.5037215416
##  [726]  0.2645865820 -0.0999999656  0.3169841701  0.1314001822  1.0034420387
##  [731]  0.3645217562  0.4908261743  0.6810814844  1.0994944037  1.1158141888
##  [736]  0.9134651278  1.0774483612  0.8537425372  0.2191916579  0.9553076639
##  [741]  0.1787097993 -0.0289098760  0.5244284043  0.2687248154 -1.1002398263
##  [746]  1.1313436121  0.8356526056  0.9782714009  0.3046372312 -0.0740008071
##  [751]  0.8808845095 -0.0758628546  0.3408905647 -0.0178519473  0.3370684365
##  [756]  0.6380586565  1.1707808312  1.8450495125  1.0284883850  0.8720513257
##  [761]  0.6529974039  0.3387599461  0.6827666945  0.3821601523 -0.4155677411
##  [766]  0.9326287174  1.3822738727  0.7311957505  0.2700877064 -0.0795151501
##  [771] -0.0662328966  0.5323295142 -0.1886244957  0.6788452130 -1.6964458213
##  [776]  0.8197039140  0.4455702718 -0.0299615133  0.6616063771 -0.6219955905
##  [781]  0.4469230856 -2.0069915263  1.0115801072  0.8641435986  0.2996238224
##  [786]  0.2332135582  0.3189410123  1.1530363915  0.4163194934  0.6580394633
##  [791]  0.5544953895  0.2348085991  0.9667079110  0.8247451905  0.0344252032
##  [796]  0.2564603510  0.1148461254  1.0016815481  0.3841599006  0.8406875613
##  [801]  1.4336225738  0.2679647601  0.7333295549  1.6083667115  1.0811583803
##  [806]  0.3923787120  0.3360988523  0.0223933485  0.5907656027 -0.0878484012
##  [811]  1.1824732070  0.6527676223  0.7768337677 -0.0642206264  0.0319101098
##  [816] -0.4796358569  0.4910100976  0.8851499188  1.2597605997  0.0125844700
##  [821]  0.5280298534  1.3406918543  1.1272345134  0.3108213933  1.0506939559
##  [826]  0.3500985601 -0.3047448669  0.7418865608  0.9750867704  0.6980130810
##  [831]  0.6862219844  0.9306802315  0.4314467359 -0.0634627747  0.6052706763
##  [836]  0.0005224225  1.1540607195  0.2708902315  0.5618844187  0.0803703829
##  [841]  0.6780989722  0.0405042853  0.8246151302  2.3190611762  0.3679562229
##  [846]  0.2149763451 -0.3262845818  0.6030612451  1.4653644452 -0.0717231157
##  [851]  0.2571194369  0.0227089129  0.1577342501  0.6449106001  0.5910745578
##  [856] -0.8780034055  0.4153812210  1.5390699531 -0.1226204427  0.7593400443
##  [861]  1.5284806191  0.8455396143  0.7621079167  1.5410294948 -0.7202921248
##  [866]  0.8455396143  1.0500470917  0.0906031714  0.7699996096  1.7291255801
##  [871]  1.1193447949  0.6303539747 -0.3205289123  1.0419086324  0.8091609313
##  [876] -0.0118098500  1.0179902297 -0.0871652052  1.0157167785  1.1875098580
##  [881]  0.4313898941  0.5246950858  0.6684532760  0.2638591599  0.2730538096
##  [886]  0.7592394439  0.6747107547 -0.4452328708  0.0377084843 -0.0898706446
##  [891]  0.5282933683  0.6705156155  1.1564680916  0.1786971257  0.5180857424
##  [896] -0.5095227343 -0.0720994934  0.6726745642 -0.1526971403  0.9765876836
##  [901]  0.2945410608  0.6128410736 -0.1518413127  0.3991167548  1.4599989056
##  [906]  0.8243471131  1.1379591083  0.3735512865 -0.0825487851  0.6289707076
##  [911]  0.3060705283  0.8131411484  0.5693922738  0.5113104632 -0.2152889650
##  [916]  0.1400231081  0.8056354020  0.2718215629  0.6964275026  0.4388595328
##  [921] -0.0264776276  1.0907156484  0.7150271235  0.0794825523  0.3771050851
##  [926]  0.5324190749  0.3183196676  0.5333853567 -0.3316006922  0.8626278967
##  [931]  0.4863919227  0.6629592345  2.1211272564  0.0639736741  0.9869035963
##  [936]  1.0851480146  1.0106458965 -0.1600383207  1.8207463571  0.8332708452
##  [941]  0.2860997086  0.4250441870 -0.3014027105  0.7204235862  0.0395160964
##  [946]  0.5957319518  0.3584775406  0.8448935313  0.3697162916  0.0789725921
##  [951]  1.0024000550  2.2251432546  0.5417208875  0.1667650098  0.4539283755
##  [956]  0.2594166644  0.4938348569 -0.1258147729  0.9509609785  0.8958035937
##  [961]  1.1442045745  1.5108813865  0.1526102447  0.1473127228  0.0380422190
##  [966]  0.2540848924  0.0626263631 -0.1184222969  0.2897420439  0.5613111456
##  [971]  1.1293264348  0.2919411673  2.1626931676  0.9375698228  0.1001569619
##  [976]  1.2743258500  0.7917366919  0.7780278543  0.2408079389  0.5869589169
##  [981]  1.3260732754  0.6984127577  1.1313436121  0.4017714898 -0.3757534154
##  [986]  0.3379090533 -0.2644532097  1.0723288864  0.5700507479  1.6387302495
##  [991] -0.2266004779  0.0922522221  0.8622788784  0.1647547432  0.3409020795
##  [996]  0.3189410123  0.9176612884  1.1730521810  1.1322406246  0.0802716847
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
##   0.8263688   0.4587874 
##  (0.1450813) (0.1025858)
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
## [1] -0.35754129 -0.28229870  0.63464789 -0.08105841 -0.33688693 -0.20720518
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
## [1] -0.0148
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9353694
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
## t1*      4.5 0.003203203   0.9143313
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 2 3 4 5 6 7 8 
## 3 1 1 1 1 1 2
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
## [1] 0.0072
```

```r
se.boot
```

```
## [1] 0.8895051
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

