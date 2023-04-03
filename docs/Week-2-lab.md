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
## 0 1 2 4 5 6 7 8 
## 2 1 1 1 1 2 1 1
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
## [1] 0.0161
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
## [1] 2.733385
```

```r
UL.boot
```

```
## [1] 6.298815
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.7000 6.3025
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
##    [1] 4.0 4.4 3.7 5.0 4.2 6.0 5.2 3.8 4.3 4.6 3.3 3.5 4.9 5.2 6.6 5.3 4.0 4.8
##   [19] 3.2 5.0 4.7 3.8 4.8 4.3 4.7 5.3 4.7 5.9 2.9 5.5 2.4 3.8 4.9 4.3 5.8 5.1
##   [37] 6.0 3.8 4.7 4.6 5.1 4.7 4.8 4.0 5.6 4.0 4.4 4.2 3.4 5.2 3.7 4.1 5.4 3.2
##   [55] 4.2 3.8 4.0 4.3 4.6 4.4 2.6 3.5 7.2 3.2 4.3 4.2 6.5 4.3 5.2 3.8 4.3 4.7
##   [73] 4.1 4.6 5.2 4.0 5.0 4.0 3.2 4.0 5.4 4.4 4.6 4.8 4.6 3.6 5.4 5.4 5.0 5.1
##   [91] 4.5 3.1 4.7 4.1 5.4 5.3 5.1 4.2 5.1 3.6 3.6 4.8 4.5 4.2 5.6 5.8 4.8 4.8
##  [109] 5.5 3.4 4.3 4.0 4.6 4.0 5.1 5.9 3.0 4.6 4.5 5.1 5.6 5.1 4.0 5.2 6.5 6.0
##  [127] 3.4 5.3 4.1 5.1 3.9 4.8 6.3 2.4 3.9 5.3 4.5 3.5 4.9 3.8 4.2 5.1 3.3 3.4
##  [145] 4.5 5.8 5.8 4.1 4.9 3.9 4.3 3.5 3.5 4.6 4.2 5.0 3.8 3.4 4.2 3.5 3.0 4.7
##  [163] 4.0 4.5 3.9 3.4 6.3 3.9 4.1 5.1 3.8 4.7 5.3 4.5 6.3 3.9 5.3 3.6 5.2 4.8
##  [181] 4.4 4.9 4.3 4.5 4.4 4.2 5.1 3.4 3.4 4.4 5.0 4.8 3.7 3.5 4.3 5.6 6.0 4.8
##  [199] 4.4 6.1 3.0 3.8 5.3 5.8 5.8 4.0 3.3 4.9 4.6 6.1 4.1 5.1 3.4 5.1 4.7 4.8
##  [217] 4.0 4.5 4.9 5.7 4.7 4.6 4.8 4.3 4.1 3.8 5.5 4.8 5.7 3.9 5.9 4.0 4.5 6.2
##  [235] 3.7 4.7 4.8 5.5 3.9 3.2 4.4 2.6 5.0 4.1 4.4 4.2 6.2 5.4 5.4 5.4 3.8 4.7
##  [253] 3.2 5.1 5.2 3.7 6.0 4.2 5.9 5.3 5.1 4.1 4.0 5.0 3.5 3.9 2.6 5.6 2.7 3.9
##  [271] 5.5 4.0 4.6 4.9 4.1 4.2 5.0 3.5 4.1 3.7 4.7 3.7 6.1 5.1 3.5 6.2 4.6 3.9
##  [289] 5.5 3.7 4.4 3.6 5.1 5.0 3.5 5.5 5.3 4.2 5.3 3.8 3.7 3.6 5.9 4.5 4.3 4.1
##  [307] 3.5 2.8 5.1 5.3 4.9 5.4 5.1 4.7 4.2 4.8 2.4 4.8 3.5 3.7 4.8 4.1 4.6 5.5
##  [325] 3.5 3.6 4.1 4.0 4.6 1.9 3.7 4.1 5.7 7.5 4.3 3.4 4.6 3.4 4.7 3.2 3.6 2.8
##  [343] 4.1 5.2 3.6 5.5 3.6 3.8 4.6 4.3 5.8 5.1 5.3 5.0 4.7 6.3 3.8 3.4 4.4 4.2
##  [361] 5.4 3.9 5.2 3.6 5.4 5.2 5.8 2.7 3.9 5.4 3.8 4.9 2.8 5.0 3.7 3.8 4.5 3.9
##  [379] 4.7 6.0 5.7 5.8 4.5 3.8 3.5 6.6 6.5 3.7 6.3 4.5 4.1 3.5 5.7 4.8 4.1 4.7
##  [397] 4.7 4.3 3.1 3.8 3.8 3.7 6.3 4.7 3.6 4.0 3.8 3.1 4.5 4.8 4.4 3.0 4.9 3.9
##  [415] 3.9 6.7 3.8 5.2 4.6 4.5 3.1 4.3 4.0 4.1 3.4 2.4 3.4 4.8 4.4 3.6 4.5 7.2
##  [433] 5.1 5.3 2.7 3.8 3.1 5.0 6.4 4.7 3.6 4.6 4.8 4.3 6.4 5.2 4.8 5.4 4.3 6.9
##  [451] 5.3 4.3 4.8 4.4 5.4 5.8 4.7 4.2 3.9 5.5 5.3 5.4 3.6 4.4 4.0 6.8 4.1 6.2
##  [469] 3.6 4.0 4.3 4.0 2.8 4.3 4.9 4.4 4.9 3.6 4.1 3.3 4.3 4.9 4.0 4.5 4.7 5.4
##  [487] 4.7 5.9 4.1 5.8 3.2 5.8 4.8 5.5 4.9 2.8 5.3 4.9 4.8 4.3 3.6 3.4 4.8 3.7
##  [505] 4.6 4.0 4.7 4.1 4.7 4.9 5.7 5.2 4.0 5.5 6.0 6.7 2.6 4.7 3.8 4.3 4.9 4.8
##  [523] 5.9 2.8 4.2 5.0 4.3 3.9 3.2 4.7 3.2 5.1 4.0 5.4 3.6 4.8 4.4 4.3 5.1 4.3
##  [541] 5.2 3.8 3.7 6.2 6.2 3.6 2.9 2.3 5.7 3.1 3.0 4.8 4.9 2.8 4.5 3.5 3.7 3.4
##  [559] 5.3 5.6 4.1 4.4 5.0 3.8 5.3 4.9 4.1 4.3 3.2 2.8 3.9 4.9 5.3 3.9 4.1 6.0
##  [577] 5.8 4.1 4.2 2.7 4.0 5.4 6.0 4.6 4.3 4.0 4.8 3.7 3.1 3.9 3.5 4.2 4.2 4.2
##  [595] 5.0 4.6 3.8 5.2 4.8 4.2 3.7 3.8 3.7 3.3 5.6 6.5 5.3 3.8 6.6 3.5 5.0 3.2
##  [613] 3.7 3.6 4.0 3.7 5.0 4.6 3.4 4.9 4.2 4.9 6.4 5.7 4.4 4.1 6.1 5.0 3.5 4.4
##  [631] 6.0 3.8 5.5 5.0 5.3 3.1 4.9 4.4 5.2 2.9 5.6 4.0 5.6 5.4 5.1 4.4 6.1 4.8
##  [649] 5.7 4.5 3.2 5.7 5.3 5.0 4.5 4.9 4.8 4.2 3.8 4.8 5.6 5.8 4.9 3.5 6.2 5.0
##  [667] 5.1 5.3 5.2 3.9 5.7 5.4 4.8 4.0 2.9 3.4 5.0 4.4 5.2 4.2 4.3 4.9 2.7 4.9
##  [685] 4.6 5.0 3.8 3.1 4.3 4.3 5.6 3.8 3.3 4.2 5.3 4.5 3.8 4.2 4.9 5.5 5.8 3.7
##  [703] 2.0 3.1 5.9 3.1 4.5 5.3 4.3 6.3 3.9 3.7 4.0 4.9 2.4 5.2 5.3 4.2 3.5 6.1
##  [721] 4.5 5.3 4.0 5.2 5.0 5.6 5.4 4.0 4.0 4.2 3.1 2.3 3.5 4.7 4.4 4.9 3.9 5.0
##  [739] 4.8 5.0 5.7 4.2 4.7 4.6 3.3 3.8 4.5 4.8 3.7 5.4 4.2 5.0 4.2 4.2 5.8 5.0
##  [757] 5.0 5.1 3.9 2.2 4.9 4.7 3.3 3.9 2.7 3.7 4.1 4.7 5.6 4.7 5.6 4.0 3.4 3.9
##  [775] 4.2 5.9 4.6 4.5 3.7 4.7 5.6 4.3 4.4 4.5 3.8 4.8 5.8 3.9 5.2 4.1 6.2 4.2
##  [793] 3.6 4.5 2.4 3.7 5.4 3.9 4.1 5.2 6.5 6.0 4.8 4.6 4.5 3.8 4.9 5.9 4.0 4.3
##  [811] 4.8 4.2 5.6 3.3 4.4 4.0 5.9 5.5 6.3 3.9 4.6 4.0 5.1 4.1 4.8 3.6 4.6 4.9
##  [829] 5.3 5.2 3.8 4.2 3.7 6.7 4.4 6.0 4.4 5.5 5.8 5.3 4.2 3.2 4.9 4.3 5.6 4.7
##  [847] 4.0 4.4 3.4 5.6 3.9 5.3 2.9 5.3 3.4 2.5 3.6 4.5 4.5 5.4 4.9 6.1 4.4 5.0
##  [865] 4.1 4.3 5.8 6.4 4.6 5.3 6.3 5.6 4.7 5.0 3.8 3.4 4.0 4.2 4.8 6.3 5.7 5.2
##  [883] 4.4 4.9 4.3 4.2 5.8 5.2 3.1 3.7 2.2 5.2 4.4 2.7 3.9 4.7 6.6 4.6 4.6 5.2
##  [901] 5.3 3.7 4.6 3.2 4.5 4.1 3.5 6.0 4.1 4.2 5.0 5.0 3.9 4.1 4.4 5.9 5.9 4.4
##  [919] 6.2 3.4 4.3 3.5 5.8 5.0 3.9 3.3 3.5 6.0 6.1 3.7 5.4 5.1 4.5 5.3 2.7 3.1
##  [937] 4.6 3.5 3.8 4.3 4.8 4.3 4.2 5.2 5.3 4.0 4.0 5.1 4.5 4.0 4.4 6.2 3.6 4.9
##  [955] 4.2 4.4 4.0 4.4 5.0 4.6 4.1 4.0 4.3 6.1 6.4 4.0 3.3 3.8 2.9 5.1 4.3 5.3
##  [973] 3.6 6.5 5.0 4.4 5.7 5.7 4.7 5.2 3.7 4.6 5.1 3.4 3.9 3.3 5.2 3.9 3.6 4.7
##  [991] 2.6 3.3 5.4 4.0 4.3 4.7 4.5 4.2 5.3 5.4
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
##   2.7   6.3
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
##    [1] 5.1 3.9 3.6 3.7 5.4 4.2 4.7 4.0 5.5 4.4 3.5 3.6 4.6 5.6 4.4 4.7 5.2 3.5
##   [19] 4.0 4.2 6.2 4.0 5.3 3.1 4.2 6.5 4.9 4.4 3.0 5.1 4.0 5.3 3.1 6.4 4.4 3.3
##   [37] 4.0 3.0 5.2 4.7 4.1 5.7 3.5 3.9 3.8 3.9 3.3 5.0 5.5 4.3 3.9 5.4 5.2 4.7
##   [55] 2.8 4.3 3.5 4.7 4.8 3.1 2.2 3.6 6.6 3.4 5.0 4.6 3.7 4.8 5.6 3.8 5.9 4.2
##   [73] 4.5 3.8 4.7 4.3 5.5 5.3 3.5 5.3 3.7 3.3 3.6 4.3 2.5 5.7 5.0 5.2 3.9 5.5
##   [91] 4.9 3.2 4.5 4.2 3.0 5.1 6.2 4.1 6.1 4.1 4.3 4.0 5.0 6.2 5.6 3.3 4.0 5.6
##  [109] 4.8 3.6 3.7 5.2 3.5 5.0 5.6 5.4 4.6 3.4 5.1 4.8 3.9 4.7 4.4 4.9 3.4 4.9
##  [127] 4.2 4.3 5.4 4.8 4.2 4.0 5.7 3.8 4.2 3.2 4.2 2.8 4.3 5.8 5.3 5.0 3.2 4.5
##  [145] 5.1 4.0 3.9 4.0 4.0 4.5 4.3 6.2 5.0 6.1 4.7 4.4 5.2 4.6 4.3 4.4 5.5 6.1
##  [163] 4.2 4.8 4.0 4.5 3.6 2.4 5.6 4.6 6.0 3.5 3.7 3.6 5.0 5.0 4.0 5.0 3.9 3.5
##  [181] 4.0 3.5 3.8 3.1 4.7 4.8 4.5 4.4 4.3 4.2 5.8 4.5 4.4 3.3 4.3 4.6 5.3 4.2
##  [199] 4.1 3.7 2.3 4.9 5.8 4.7 4.6 3.2 5.1 4.4 3.1 3.5 5.1 3.9 3.6 3.7 4.2 5.7
##  [217] 6.2 4.4 6.0 4.3 6.6 4.4 6.5 5.4 3.9 4.7 4.6 4.7 5.5 3.7 3.4 3.6 2.8 4.7
##  [235] 4.6 5.1 3.4 3.9 4.0 4.1 6.8 4.2 3.3 3.7 5.6 4.1 4.8 3.6 4.3 4.6 4.6 3.9
##  [253] 5.8 3.9 5.6 5.2 5.4 4.8 4.2 3.8 3.8 3.3 5.9 4.2 4.4 5.7 5.0 5.3 3.9 3.1
##  [271] 3.4 5.0 4.3 2.6 4.9 5.1 5.4 5.3 6.3 5.9 5.5 4.7 6.4 4.4 6.3 5.3 4.8 5.1
##  [289] 6.1 5.5 3.2 4.5 4.6 4.9 5.0 5.1 4.5 4.9 4.7 4.3 5.3 3.3 4.7 2.8 5.4 4.8
##  [307] 3.6 4.3 5.1 3.8 5.3 6.0 4.5 5.2 3.2 4.9 5.5 5.3 5.4 4.4 3.2 4.9 4.3 6.4
##  [325] 4.6 4.3 4.1 5.2 5.1 3.3 3.7 3.5 5.4 5.0 5.2 6.1 4.3 4.3 2.9 2.8 5.4 3.9
##  [343] 3.3 5.4 5.1 3.0 3.9 5.4 5.6 2.3 6.0 4.9 5.4 3.9 5.7 5.8 4.3 3.4 4.7 5.9
##  [361] 4.0 4.0 3.3 4.1 5.6 2.8 5.0 4.6 4.3 4.7 4.6 6.1 5.1 3.9 5.1 4.9 5.9 4.4
##  [379] 4.5 5.6 3.6 4.6 3.7 4.0 5.3 4.9 5.6 3.0 3.8 2.7 5.4 6.4 6.9 4.6 5.0 5.4
##  [397] 5.5 3.5 3.4 4.7 5.3 5.0 5.5 4.3 5.9 4.4 3.3 3.8 4.9 5.7 5.0 3.4 5.2 5.1
##  [415] 4.6 4.5 3.9 5.2 3.2 3.6 5.3 4.0 4.5 4.4 4.8 4.6 3.8 4.2 3.6 3.7 2.5 4.8
##  [433] 3.7 5.4 3.8 5.4 4.2 3.2 5.4 5.2 6.4 5.0 5.6 5.3 5.0 4.4 4.9 4.3 6.0 4.7
##  [451] 4.6 4.3 4.8 4.3 5.9 4.7 3.8 5.5 5.1 3.1 4.1 4.3 3.1 5.3 4.3 5.2 4.4 4.9
##  [469] 4.3 4.3 4.6 3.8 5.6 4.5 3.1 3.2 5.2 5.3 5.2 2.5 5.3 5.7 4.3 6.0 5.2 3.4
##  [487] 5.1 6.1 3.9 4.6 3.8 4.8 4.7 5.4 2.6 3.4 4.3 4.1 3.3 4.8 3.7 3.9 3.8 4.7
##  [505] 5.5 4.8 4.1 3.5 4.2 5.3 6.4 4.8 3.5 5.8 4.8 4.8 4.2 3.6 3.8 3.9 4.5 3.6
##  [523] 3.9 3.4 3.5 5.0 4.0 4.8 3.1 5.3 5.0 6.1 4.5 6.3 4.0 4.1 4.0 3.2 4.5 3.8
##  [541] 3.6 4.5 4.8 4.8 5.3 5.8 5.1 6.1 4.9 3.3 3.9 4.6 3.9 4.7 3.7 5.4 5.4 5.2
##  [559] 3.0 4.0 3.0 4.9 6.2 5.9 4.9 3.4 4.2 2.7 5.2 3.5 3.4 4.5 4.4 5.0 3.6 4.4
##  [577] 5.2 3.5 4.0 5.2 5.2 3.7 5.6 4.9 4.9 6.7 5.2 5.5 3.6 5.7 3.9 5.5 4.5 4.9
##  [595] 4.1 4.9 4.8 3.1 5.1 5.1 4.7 3.1 5.7 3.3 5.1 4.6 3.3 6.2 5.6 3.8 3.8 3.9
##  [613] 3.2 5.5 5.1 6.2 4.1 3.6 5.1 5.3 3.1 4.9 5.2 5.2 4.2 5.3 5.4 3.5 5.5 3.5
##  [631] 5.1 4.2 4.1 4.1 5.3 3.6 3.3 5.9 4.5 6.9 3.9 6.3 3.7 5.4 3.7 5.4 5.9 4.7
##  [649] 5.0 3.8 3.0 3.7 4.2 3.4 4.0 4.0 5.5 5.6 5.3 4.0 5.8 4.1 5.1 4.9 3.6 3.8
##  [667] 4.7 5.7 3.9 5.5 3.4 4.8 4.3 4.5 5.5 4.4 3.3 4.1 4.4 5.1 4.1 7.0 4.8 4.8
##  [685] 4.0 3.6 3.1 3.3 2.9 4.1 3.5 5.6 5.0 3.3 5.3 2.9 4.9 3.3 4.7 4.4 3.5 7.1
##  [703] 4.1 3.7 5.2 1.8 4.2 3.2 5.9 3.9 5.8 4.9 4.9 3.6 4.2 5.4 4.4 5.6 4.2 4.3
##  [721] 3.9 4.2 5.4 5.0 4.4 3.7 4.7 6.1 4.0 4.3 4.9 4.7 6.0 5.7 5.5 4.3 4.2 4.8
##  [739] 3.6 4.2 4.0 5.0 4.7 4.6 3.3 4.9 4.6 3.1 5.2 4.7 6.0 4.3 4.4 3.7 4.0 5.4
##  [757] 4.9 3.6 5.0 3.9 5.1 4.8 3.2 4.1 5.4 5.7 4.1 6.8 5.4 4.6 3.6 4.8 3.9 3.8
##  [775] 6.2 3.1 5.2 4.4 3.9 5.5 3.4 5.2 4.8 3.1 4.0 3.7 4.5 3.8 4.6 4.5 3.7 7.4
##  [793] 5.1 4.9 5.1 3.5 5.0 5.3 5.6 3.4 3.6 5.2 5.1 4.2 6.1 3.6 3.6 4.6 4.1 4.8
##  [811] 5.6 5.7 6.0 5.4 4.2 3.5 3.7 4.9 5.1 5.3 6.1 3.8 5.0 4.8 3.4 4.4 4.4 5.9
##  [829] 5.4 5.3 5.1 4.1 4.8 3.4 5.7 4.7 5.8 4.1 6.4 2.7 3.4 3.8 5.4 3.5 5.3 3.7
##  [847] 4.0 4.1 4.0 3.9 4.8 5.8 4.7 5.0 5.9 5.8 4.9 5.5 3.9 5.5 3.5 4.6 4.5 3.8
##  [865] 3.7 4.6 5.1 3.9 5.6 4.7 3.9 6.0 4.9 4.4 6.3 5.3 3.4 3.3 4.5 5.3 4.9 5.6
##  [883] 4.4 4.1 3.5 4.6 5.5 2.5 3.9 5.1 4.7 4.4 5.2 4.3 4.7 4.3 4.6 3.3 5.8 5.1
##  [901] 4.9 6.7 5.4 4.3 4.0 4.0 5.5 5.6 5.4 4.1 3.2 5.0 5.2 4.5 2.8 3.6 3.6 4.5
##  [919] 6.6 4.6 4.3 3.4 1.9 4.3 5.0 5.4 3.0 5.3 5.8 3.4 4.6 3.9 5.5 4.3 3.2 4.5
##  [937] 5.0 3.5 4.0 5.1 5.2 3.6 4.8 4.8 5.0 4.6 4.2 4.1 5.3 3.4 6.7 3.9 6.1 6.0
##  [955] 3.4 4.6 3.1 5.8 4.3 4.8 4.0 4.7 3.7 5.5 6.1 4.9 3.9 4.6 4.7 3.6 5.2 4.1
##  [973] 4.2 5.3 4.7 3.4 3.5 4.7 4.6 4.7 5.7 4.8 2.8 3.2 3.1 4.2 3.9 4.0 4.8 3.5
##  [991] 3.7 5.2 5.0 4.6 5.5 5.8 4.5 3.2 4.3 5.3
## 
## $func.thetastar
## [1] 0.0373
## 
## $jack.boot.val
##  [1]  0.54378378  0.46106443  0.27369942  0.24672365  0.09000000 -0.05770393
##  [7] -0.16137072 -0.21608187 -0.32422535 -0.48425926
## 
## $jack.boot.se
## [1] 0.9739818
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
##    [1] 4.0 3.0 5.2 4.1 4.6 4.8 3.6 6.4 5.5 2.1 4.2 4.8 4.4 3.6 4.3 3.9 4.0 5.9
##   [19] 4.9 5.2 4.5 3.9 4.1 4.6 4.4 4.0 4.2 4.9 4.8 3.7 3.8 5.9 4.9 4.4 5.8 4.1
##   [37] 4.0 5.4 4.8 4.0 5.3 5.0 4.6 7.0 5.2 5.5 5.2 4.6 4.2 3.9 4.0 5.4 5.5 5.0
##   [55] 5.0 5.8 4.5 4.5 5.1 4.2 5.1 4.4 3.2 3.2 2.1 2.9 3.2 4.1 5.5 5.6 3.7 6.1
##   [73] 5.2 5.3 4.1 3.9 5.6 3.8 4.9 5.2 6.6 3.3 3.8 6.0 3.8 4.1 2.7 5.7 4.4 4.5
##   [91] 5.6 6.3 4.6 3.6 5.9 3.8 4.8 5.1 5.2 3.0 4.4 5.7 4.9 3.4 3.7 4.3 4.4 3.8
##  [109] 3.3 4.3 3.9 5.5 3.6 4.6 4.4 5.9 3.3 4.4 5.5 4.5 3.5 5.7 4.8 6.5 4.8 5.0
##  [127] 5.5 3.3 6.2 3.3 3.3 4.5 6.0 4.9 3.4 3.7 5.5 5.1 4.2 5.2 6.4 2.8 5.3 3.5
##  [145] 3.4 5.1 5.5 5.7 4.7 4.3 5.0 5.5 5.1 5.2 5.1 3.7 4.1 3.7 6.0 4.7 5.1 5.1
##  [163] 4.8 4.2 6.3 4.0 3.2 4.3 4.7 4.4 4.3 4.8 4.4 4.2 5.4 4.9 3.9 3.5 4.7 3.6
##  [181] 3.8 4.3 3.9 4.3 2.7 4.8 6.4 4.5 4.9 3.3 6.0 3.7 3.4 4.3 3.8 5.3 5.6 3.9
##  [199] 4.2 4.7 5.0 5.0 6.3 4.3 4.9 5.0 5.0 4.3 3.8 3.3 5.2 3.7 4.2 3.3 4.7 3.6
##  [217] 5.0 5.2 3.7 5.1 4.9 5.7 6.1 4.2 4.0 4.8 4.2 4.2 5.6 3.2 4.1 4.0 4.8 4.3
##  [235] 5.1 4.4 3.9 6.1 4.0 4.6 4.3 5.0 5.4 2.9 3.7 6.2 3.6 3.9 3.9 5.6 3.3 2.4
##  [253] 2.4 3.4 4.4 4.9 4.3 2.9 5.3 4.7 5.3 4.5 5.8 3.3 3.4 5.3 5.0 3.2 3.9 5.3
##  [271] 4.0 4.4 2.9 4.8 6.0 4.0 4.0 4.2 4.3 4.4 5.1 4.1 4.4 4.1 5.6 5.9 5.8 3.6
##  [289] 4.2 6.3 2.9 4.6 4.2 4.9 5.6 5.4 3.6 3.6 4.9 4.3 4.5 3.8 3.4 4.5 5.5 5.5
##  [307] 4.2 3.2 4.8 3.4 4.8 4.7 4.1 4.7 3.9 5.1 4.6 3.7 2.6 4.4 4.3 3.4 4.6 4.7
##  [325] 4.9 2.8 4.7 4.1 4.0 4.9 4.5 3.5 3.7 5.2 4.4 3.8 4.8 4.4 5.1 3.9 3.4 6.0
##  [343] 4.8 3.8 3.6 2.1 3.5 5.3 4.2 4.5 3.7 5.7 5.4 4.7 3.6 4.1 4.4 6.4 5.1 5.9
##  [361] 3.6 5.4 3.6 4.9 5.3 4.4 4.3 3.0 3.7 5.5 5.5 3.6 4.9 4.3 5.3 5.7 4.5 5.2
##  [379] 3.5 4.1 6.4 5.0 4.2 2.9 5.0 3.1 3.4 4.8 3.9 2.4 3.4 3.2 4.0 5.4 5.3 4.6
##  [397] 3.7 4.3 4.0 4.7 4.5 3.7 5.6 4.0 4.0 3.3 4.8 4.1 3.4 3.6 6.5 4.8 5.4 2.9
##  [415] 5.2 3.8 4.7 5.6 4.5 4.4 4.7 5.2 3.2 4.9 4.6 4.5 2.9 5.3 5.4 3.5 3.3 4.4
##  [433] 3.7 5.3 3.1 3.7 4.3 5.1 3.3 3.7 4.7 5.8 4.0 4.8 4.4 3.7 5.4 5.4 5.1 4.5
##  [451] 5.4 3.5 4.7 1.9 3.6 4.6 5.2 5.5 4.3 5.3 4.7 4.2 5.0 3.8 3.6 3.2 5.8 4.8
##  [469] 7.2 3.6 4.9 5.3 5.2 4.4 4.3 4.3 4.1 5.7 5.6 4.0 5.0 4.5 4.7 4.1 4.0 4.0
##  [487] 4.7 4.6 4.6 3.5 4.0 3.1 4.0 5.3 4.6 4.9 5.3 4.9 5.7 4.1 4.1 5.7 3.6 2.6
##  [505] 4.5 4.4 4.5 4.5 3.2 4.4 5.1 3.7 4.2 3.6 3.3 4.9 5.4 3.9 5.0 5.6 3.0 4.5
##  [523] 4.7 4.5 4.2 4.2 5.1 4.3 4.9 5.0 3.1 5.6 4.3 4.2 2.5 3.2 5.9 4.5 4.5 5.9
##  [541] 3.5 5.1 4.2 3.8 3.1 5.1 3.2 4.1 4.4 3.8 4.1 5.0 6.0 3.6 5.1 4.7 5.4 4.4
##  [559] 4.0 5.4 2.8 3.6 4.9 5.5 5.0 3.2 2.8 5.5 5.1 4.8 6.2 5.4 3.3 2.7 3.5 4.1
##  [577] 5.7 3.9 3.3 5.8 4.2 4.8 5.4 3.7 4.6 5.1 4.8 5.2 5.7 5.7 4.4 4.1 3.1 5.0
##  [595] 4.9 4.7 4.3 3.1 4.0 3.5 5.4 4.3 3.4 4.0 4.0 3.6 4.2 4.9 4.1 4.1 4.0 3.5
##  [613] 6.0 4.3 5.3 4.0 4.6 3.1 3.4 5.5 2.9 3.9 5.2 5.1 3.1 3.9 5.3 4.3 4.2 3.6
##  [631] 3.8 4.3 6.4 3.2 4.9 4.5 4.5 4.1 5.9 5.5 5.5 4.7 4.1 4.7 4.5 6.3 3.7 5.4
##  [649] 4.5 4.0 2.2 3.6 5.3 3.9 3.1 4.4 5.3 3.9 5.7 3.4 5.7 4.7 4.2 3.5 4.5 2.9
##  [667] 3.2 5.1 4.6 3.8 4.5 6.4 5.8 4.3 5.1 6.0 5.6 4.7 2.9 3.2 4.0 2.7 6.1 4.7
##  [685] 5.7 4.4 5.7 4.7 5.1 3.8 4.6 5.8 4.1 5.6 5.4 3.7 4.7 3.7 6.6 4.1 5.5 3.6
##  [703] 5.1 6.4 3.7 4.7 3.7 2.9 3.9 3.4 4.9 4.5 5.3 4.9 5.8 4.9 3.1 5.5 4.7 5.3
##  [721] 4.1 4.8 5.2 4.1 3.9 2.7 5.8 3.4 6.5 3.5 4.5 4.4 5.5 3.4 3.5 5.4 5.0 4.3
##  [739] 4.6 4.6 4.0 3.9 4.2 4.7 3.9 4.9 3.3 4.4 4.8 5.5 5.0 3.1 4.9 5.2 4.7 2.5
##  [757] 5.0 4.8 5.0 4.9 5.2 4.7 3.9 3.3 4.4 4.4 4.1 3.5 5.1 4.9 5.2 3.9 3.6 4.8
##  [775] 3.1 5.6 3.6 4.3 3.7 3.1 6.4 5.4 4.7 4.6 4.8 4.6 3.1 4.7 4.7 4.4 3.4 4.4
##  [793] 4.2 5.0 4.7 4.7 3.9 4.7 6.9 4.7 3.3 5.6 4.5 3.2 4.4 3.7 4.4 5.3 6.0 4.5
##  [811] 4.9 2.4 5.9 4.5 4.8 5.0 5.7 6.1 4.7 3.2 4.6 2.8 3.8 4.4 3.7 4.4 3.6 5.1
##  [829] 3.3 4.9 3.7 4.2 3.6 4.0 6.1 4.3 4.9 5.3 5.3 5.0 4.1 3.3 5.1 4.1 5.1 4.0
##  [847] 5.0 4.7 4.8 4.3 5.1 4.6 3.9 3.2 4.0 4.7 4.7 5.7 3.8 2.5 4.7 4.3 4.6 4.6
##  [865] 3.7 3.1 4.0 5.5 4.5 6.0 4.5 4.4 4.1 3.6 3.1 3.3 4.5 3.4 5.2 4.8 4.7 3.4
##  [883] 4.3 5.0 5.7 5.2 5.0 4.5 3.7 4.8 5.2 4.0 4.6 6.1 4.3 5.8 4.7 4.4 5.1 3.5
##  [901] 5.1 4.6 4.1 6.1 6.0 4.6 4.3 4.7 5.2 3.4 3.7 3.8 2.3 2.8 3.8 6.6 4.9 6.0
##  [919] 4.9 4.2 3.9 5.3 4.4 5.3 4.0 3.8 5.1 3.7 7.0 5.0 2.4 5.8 4.3 4.9 4.4 5.7
##  [937] 3.5 4.3 4.2 5.0 3.5 4.8 3.5 4.9 3.7 5.8 3.9 3.6 5.4 5.4 3.4 4.1 4.7 5.3
##  [955] 5.4 3.4 5.8 4.8 4.6 4.4 5.1 5.7 6.1 4.3 3.9 4.1 4.2 4.7 4.7 4.6 5.1 3.1
##  [973] 4.9 3.9 6.2 5.3 3.0 4.3 4.5 6.5 4.2 5.1 4.4 4.8 3.6 4.5 5.5 3.2 6.1 4.3
##  [991] 4.2 4.9 3.0 5.0 6.3 4.5 4.6 4.4 4.4 4.4
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.3 5.3 5.1 5.0 5.0 4.9 4.8 4.6 4.4
## 
## $jack.boot.se
## [1] 0.9529428
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
## [1] 0.1532458
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
##     shape       rate  
##   3.535602   7.968114 
##  (1.512510) (3.662660)
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
## [1]  1.2944147  0.9959197 -0.1444918  0.4275530  0.7463772  0.6528607
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
##    [1] -0.100568852  0.931063322  0.634495812  0.461469474 -0.075981063
##    [6]  1.661388961 -0.091586721  0.632849920 -0.210658851 -0.473761811
##   [11]  0.726403025 -0.076961380  0.437699129  0.338444739  0.207964236
##   [16] -0.876578782  0.543816142  0.292193942  0.148347923 -0.524774153
##   [21] -0.038275166 -0.491887524  0.307329181  0.544084536  0.211088588
##   [26]  1.074224223  0.274930622  0.617299668  0.400058512  0.472794183
##   [31] -0.349999191 -0.481156495 -0.013819978 -0.409306997  0.422351132
##   [36]  0.403439316  0.310268587  0.232682451  0.505904042  0.564173016
##   [41]  0.769749006  0.385881027 -0.593526437 -0.229604688  0.190453905
##   [46]  0.089113799 -0.429227276  0.112807495  0.396061477 -0.307755670
##   [51] -0.785566077  0.556498834  0.208021840 -0.207409819  0.023132508
##   [56]  0.171886743  0.346000263  0.018952869  0.165036180  0.286885362
##   [61]  0.018673443  0.017407327 -0.304662954 -0.158763723  0.173520739
##   [66] -0.160686736  0.157356069  0.538185935  0.156097144  0.654269242
##   [71]  0.007039716  0.083617669  0.159459795  0.482477176  0.522460935
##   [76]  0.309796428 -1.038417861  0.395872431  0.057361627  0.088808880
##   [81]  1.280445416  0.671472720  0.160023929  0.605629239  0.272215512
##   [86] -0.205595015  0.164607051  0.268349021  0.495307377 -0.313454919
##   [91] -0.098575867 -0.999545203 -0.446381291  0.198183754 -0.341550177
##   [96]  0.572669198  0.421955949 -0.651655779  0.129301552  0.625644667
##  [101] -0.069276051  0.357255547  0.396085844 -0.783368143  0.364446972
##  [106]  0.326815919  0.890475869  0.215344223  0.905713260  0.133471756
##  [111] -0.822732957  0.444562076  0.196680507  0.686298186  0.234067995
##  [116]  0.523596367  0.193612186  0.434772086  0.644535500 -1.005985831
##  [121]  0.744274755 -0.191937873  0.206156668  0.424409554 -0.565920604
##  [126]  0.163565349  0.760485371 -0.029352669  0.341358728  0.003494840
##  [131]  0.423163067  0.918612136 -0.213100378  0.314161304  0.726488334
##  [136]  0.055363623  0.567495077 -0.447128091  0.334720592 -0.082554228
##  [141] -0.217255589  0.126444947  0.123056994 -0.538263096 -0.479429660
##  [146] -0.008331347  0.973614022  0.559324205  0.364842251 -0.913928915
##  [151] -0.108108009  0.262409383  1.142814007 -0.180197810 -0.330690430
##  [156]  0.259880004  0.360353726  0.257015645 -1.142011012 -0.198359019
##  [161] -0.565920604  0.120663178 -0.525036808  0.058278979  0.027727933
##  [166] -0.562516279  0.456307904  1.002647189  0.605473151  0.144506906
##  [171]  0.712092015  0.187414508 -0.882158289 -0.386740603  0.119380159
##  [176]  0.286798064 -0.135544964  0.211563266  0.210727425  0.325414690
##  [181]  0.086829168 -0.427256904 -0.061447011  0.806133789  0.852549266
##  [186]  0.332420329 -0.703548869  0.216111459 -0.138665561  0.079648859
##  [191] -0.104118820  0.336242562 -1.479088857 -0.626654753  0.524232843
##  [196]  0.318083865 -0.168357964  0.758910651  0.680220399  0.015915369
##  [201] -0.458039570  1.103009847  0.961183007 -0.132054764 -0.042501204
##  [206]  0.172614698  0.449449634 -0.602138421  0.173950984  0.909296766
##  [211] -0.101326996  1.403564628 -0.171523832 -1.389385004  0.528135354
##  [216] -0.455518016 -0.776678765 -0.012532642  0.444794193 -0.046063746
##  [221]  0.273206547  0.373165106  0.275264451 -0.104221381  0.217207674
##  [226]  0.375882225  1.103419628 -0.072928457  0.620148869  0.467350671
##  [231]  0.796082043  0.049223740  0.469856208  0.092612473 -1.155902340
##  [236]  0.300114524 -0.208360026 -0.206559663  0.345454562  1.159942593
##  [241]  0.926876247  0.995416366  0.145251934 -0.579315638 -0.291870697
##  [246] -0.114503960 -0.208886030  0.498297832  0.917643794  0.905939619
##  [251]  0.004237792  0.678436365  0.947486468  0.486170465  0.201316337
##  [256]  0.560586712  0.314462675  0.318760031  0.089326470  0.300817149
##  [261]  1.653551209 -0.196869346  0.927718637  0.422701059  0.120638131
##  [266]  0.454707463  0.472310475 -0.150374707 -0.152719638 -0.386089257
##  [271] -0.440092036 -0.461875782  0.225185856  1.085557247  0.427107662
##  [276] -1.473774820 -0.204794131  0.112668875  0.596297276  0.902716325
##  [281]  0.260456610  0.325660481  0.915064958  0.299425472  0.847375024
##  [286]  0.217777358  0.776098804  1.348139692  0.292909960  0.619199536
##  [291]  0.434044855  1.613335765 -0.219388831  0.353360392 -0.520273361
##  [296]  0.403199407  0.501651574  0.852080512 -0.528592193 -0.992347986
##  [301]  0.395697877  0.174826253 -0.275208851  0.295987591 -0.502789906
##  [306] -0.346979886  0.212154645  0.126693783  0.234889395  0.162909168
##  [311]  0.630295023  0.095410463 -0.069534558 -0.461447465 -0.134956809
##  [316] -0.037014810  0.676688994  0.333806452  0.040535548  1.356283730
##  [321]  2.121910309  0.161385018  0.562369440 -1.191338898 -0.136662877
##  [326]  0.616366924  0.269284326  0.178331220 -0.308578479  0.190071085
##  [331]  0.347395172 -0.593848277 -0.246074249 -0.219902989  0.223033698
##  [336] -0.186451920  0.714139750 -0.101495502  0.190448450 -0.441607071
##  [341]  0.240510121 -0.305912375 -0.549304096 -0.036359355 -0.350715538
##  [346] -0.001635624  0.093554739  0.886778883  0.607994128  0.863087003
##  [351]  0.434919369  0.453970082  0.211296434 -0.136618193  0.106313748
##  [356]  0.847273774 -0.428406402 -0.357290714  0.803554895  0.874843243
##  [361]  0.391273678  0.301537095 -0.422036642  0.333004038 -0.501021367
##  [366] -0.470475056  0.102929447  1.261530346 -0.031344282  0.447740285
##  [371]  0.070013263  0.334580262  0.207741817 -0.472191625  0.377307608
##  [376]  0.201050915  0.616432708  1.029569316 -0.153388021  1.307742259
##  [381] -0.019480667 -0.432440372  0.633912687  0.015390879 -0.190919577
##  [386]  0.233871039 -1.324799913  0.946749527  0.212816094  0.558497609
##  [391]  0.611253785  0.912796945  0.522502965  1.324744371  0.550423233
##  [396] -0.010237948  0.003047612  0.026575201 -0.304466489  0.348853432
##  [401]  1.482729958 -0.078140619 -0.507911413 -0.094105225  0.471745210
##  [406]  1.002821268  0.153871935  0.168010508  0.943161992  0.564833281
##  [411]  0.600642929  0.503685915  1.160382402  0.433221270 -0.247070323
##  [416]  0.492269495 -0.023572791 -0.197754307 -0.795389963 -0.149874539
##  [421] -0.061825581  0.315316504  1.484643167 -0.492763643  0.227746009
##  [426]  0.080122160 -0.513170774  0.485572603 -0.056353934 -0.026540372
##  [431] -0.781993554  0.557940026  0.776037865 -0.519306796 -0.119735664
##  [436] -0.170100435  1.373601278 -0.172386362  0.272735316  0.790501704
##  [441]  0.150081726 -0.114574530  0.367537598  0.269211642  0.314634074
##  [446]  0.446099442  0.445304746  0.065893458  0.145722455  0.433411394
##  [451] -0.116249775  0.568081540  0.242920662 -0.435592015  0.495966199
##  [456] -0.124259842  0.452628524  0.380385052  0.721094129  0.160590011
##  [461]  0.193942617 -0.680674996  0.929334743  0.264297682  0.055740144
##  [466]  0.142188636 -0.590960472  0.348502679  0.167715288  0.638502541
##  [471]  0.478234776  0.261232035  0.446934743  1.173216246 -0.231994958
##  [476] -0.141248130  0.100032280 -0.267592263  0.255937025 -0.140625566
##  [481]  0.170190304  0.605763245  0.103379626  0.010481625 -0.273202003
##  [486] -0.173450169  0.190587367 -0.819241000  0.197222139 -0.400499628
##  [491]  1.838238612  0.204182988  0.105609075 -0.133501239 -0.302022482
##  [496]  0.207855503  0.466687623 -0.276027240  0.721086619  0.507718050
##  [501]  0.579871072  0.173009408 -0.493724713  0.601805540 -0.090062708
##  [506]  0.166660067 -0.024569189  0.096761313 -0.180737560  0.118158594
##  [511]  0.346243593  0.312868166 -0.517823643 -0.112662528  0.528493210
##  [516]  0.748618829  0.183852283  0.670342100  1.090870305 -0.548807349
##  [521]  0.414727631  0.162313603 -0.321831334  0.602139861 -0.046580115
##  [526]  0.735553278  0.593579729 -0.215929186  0.559745235  0.396556671
##  [531] -0.290238854  0.554448672  0.160588128  0.852174787 -0.475636533
##  [536]  0.557885167  0.614266853  0.029920364  0.312496424  0.401337756
##  [541]  0.753666875 -0.394239838  0.367584936 -0.210635108  0.757060016
##  [546] -0.270836846 -0.183191787  0.185531037 -0.175773751  0.242188536
##  [551]  0.691453540 -0.795416914 -0.961399493  0.212037817  0.750897364
##  [556]  0.590862060  0.353560170  0.009933606 -0.996169990 -0.162639947
##  [561]  0.275385144 -0.782802358  0.577523469 -0.310087310  0.655510700
##  [566]  0.680143613 -0.238447828 -0.201529145 -0.006000313  0.246633195
##  [571]  1.387031643  0.211563266  1.435769870  0.208789024 -0.061607004
##  [576]  0.166633155 -0.167632324  0.163911766  0.799527150  1.240672080
##  [581]  0.173686706 -0.507870000  0.636271906  0.178588432  0.425106820
##  [586]  0.131046472 -0.417311162  0.365693177 -0.017623714  0.251323810
##  [591]  0.719703822  0.043333905  0.990199738  0.313224847  0.863835942
##  [596]  1.626820524 -0.034940987 -0.927156538 -0.252175249  0.154599852
##  [601]  0.283383782  0.705607508 -0.685695851  0.333841397 -0.600738764
##  [606]  0.598112601 -0.050041600  0.268847252  0.323325143  0.528426756
##  [611] -0.393588371 -0.143054377  0.150427734  0.627273756  0.599735234
##  [616]  0.130292402  0.346677202  0.059916120 -0.065353015  0.130634584
##  [621]  0.256978064  1.051177449 -0.077659566 -0.213013945  0.716768341
##  [626]  0.083013095  0.152717815 -0.870848097  0.134839198 -0.195560098
##  [631]  0.418773362  0.314601617  0.715035145  0.689396187  0.650478877
##  [636]  0.289244286  0.120663178 -0.070992617 -0.128363677  0.116501206
##  [641]  0.812398746  0.427794984  0.443885959  0.468659099  0.267753415
##  [646]  0.099527854  0.145254694 -0.104221381  0.347961576 -0.318654490
##  [651] -0.502789906  0.158074870 -0.183851655  0.259090773 -0.816328520
##  [656] -0.507242431  0.453356981 -0.357051757  0.172085127  0.589960881
##  [661]  0.496727848  0.137438124  0.175832650 -1.202744568 -0.261909657
##  [666]  0.064740186 -0.121849528  1.197808458  0.111903759  0.477306598
##  [671]  0.138109587  0.930359150  1.662411315  1.031090958  0.479937180
##  [676] -0.774666585  0.697363511 -0.327809467  0.208538213 -0.883921292
##  [681]  0.339104173  1.125258809  0.620723573 -0.543258593 -0.325913804
##  [686]  0.584871678  0.424214531  0.350287093  0.382603585  0.515316356
##  [691]  0.083509971 -0.169269668  0.006356908 -0.205203393 -0.070156144
##  [696] -0.153292237  0.868001166  0.949288312 -0.213013945 -0.027634112
##  [701]  0.916363289  0.475434551  1.027823662  0.506746409 -0.178520239
##  [706]  0.003260493  0.531964147  0.343080994  0.242280599  0.035723443
##  [711]  0.427185239  0.445304746  0.187695248 -0.167929294  0.158368052
##  [716] -0.078913900 -0.065821018  0.794042506  0.649029735 -0.207050770
##  [721]  0.111938488  0.564173016 -0.857862543  0.483047719 -0.225915081
##  [726] -0.340300455 -0.891087822  0.420042866  0.603170452  0.239819908
##  [731]  0.271791896 -0.894141664  0.368827732  0.492030951 -0.157597803
##  [736]  1.414689002 -0.417344183  0.450803273  0.233625089  0.529414804
##  [741]  0.365893838  0.565932187  0.470710388 -0.741592296 -0.513253837
##  [746]  1.072013910  0.711952480 -0.286932704  0.288190819  0.049143226
##  [751]  0.112537292  0.469613544  0.586810010 -0.253915878 -0.499828204
##  [756]  0.154121647  0.729713463  0.983721628 -0.502178201  0.142002915
##  [761] -0.551378778  0.204408499  0.259215475 -0.431308671  0.362614207
##  [766]  0.721060443  0.194551482 -0.028582694  0.182708201  0.544790283
##  [771]  1.319964716  0.520463732  0.262361593  0.146572062 -0.032965282
##  [776] -0.544297064  0.549167466  0.112587058  0.944315109  0.747375603
##  [781]  0.122254156 -0.265998287  0.319422298  0.558354109 -0.902686777
##  [786]  0.226564229 -0.009673668  0.285534522  0.364281055  0.520564893
##  [791] -0.823668873 -0.260237508 -0.432498047  0.623662492 -0.548165225
##  [796]  0.474948644  0.256151950  0.515618931 -0.431169687  1.084993628
##  [801]  1.047926224  0.557446574 -0.311402560 -0.795283425 -0.166841028
##  [806] -0.249704901  0.504415688  0.130047206  0.188378522 -0.047174596
##  [811]  0.944904615  0.479170560 -0.088094180  0.225658877  0.813693959
##  [816] -0.206734873  0.053531133  0.178210294  0.479271917 -0.110414458
##  [821] -0.485317047  0.141576913  0.183879208  0.526113957 -0.427014185
##  [826]  0.182708201  0.544055296 -0.441349777  0.180832317  0.024392410
##  [831]  0.504398088  0.441742356  0.349874883 -0.658060142  0.186641497
##  [836]  0.544734058  0.260883432 -0.105811020 -0.116249775  0.536127701
##  [841]  0.509859397  0.376670484  0.526902786  0.145085192  0.546126260
##  [846] -0.367977403 -0.194176524  0.561042113  0.345244852  0.673891900
##  [851]  0.446115055 -0.161007024  0.571636973  0.148195875  0.554057704
##  [856]  0.533958075 -0.073032570 -0.130597849 -0.296283360 -0.874455936
##  [861] -0.007866765 -0.381295299 -0.876707092 -0.877320446 -0.279914729
##  [866]  0.472020574 -0.152625868  0.590862060  0.596735788 -0.044796021
##  [871]  0.700615675 -0.289109802  0.478586084  0.251323810  0.278209049
##  [876]  0.545575082 -0.133274872  0.329462942  1.314542226 -0.496639745
##  [881]  0.027072628  0.731215965  0.475303256 -0.470558632  0.357365527
##  [886]  0.365427649  0.034643048  0.585676730  0.238587139  0.092036728
##  [891]  0.199370749  0.201276742  0.461469474 -0.875970886  0.402187775
##  [896]  0.185382193  0.748618829  0.937652012 -0.427229306  0.402792430
##  [901]  0.066660632  0.306924370  0.975852540  0.525278516  0.566236195
##  [906] -0.817948321 -0.007968089  0.240662695  0.195242042  0.140818751
##  [911] -0.560205785 -0.394202670  0.463794128 -0.503118158 -0.412823654
##  [916]  0.779281224 -0.522369193  0.209149315  1.090415044 -1.601331653
##  [921] -0.069763659  0.043500752  0.049143226 -0.125869752  0.325564729
##  [926]  0.398712219  0.803960288 -0.243568747 -0.177224382  0.488283451
##  [931] -0.412516732  0.188585078  0.121899848  0.175598416  1.052723247
##  [936] -0.111823150 -0.290089382 -0.238248031  0.796915561  0.091871457
##  [941]  0.422061073  0.029767663  0.503343954  0.940099659  0.353289846
##  [946]  0.663064002  0.911496371  0.469716941  0.108751953  0.535295992
##  [951]  0.002834631  0.161524572  0.698638889 -0.272507345 -0.427778410
##  [956]  0.534164599 -0.074662906  0.376063081  0.344293751  0.870150051
##  [961] -0.220187119  0.451254707  0.277066272  0.428218811  0.659117794
##  [966]  0.312665007 -0.704798950 -0.344173020  0.045166239 -0.142555701
##  [971] -0.126417372  0.601059812  0.802027962  0.799664300  0.191501831
##  [976]  0.159676238 -0.151318140 -0.442467239  0.015876595 -0.560110575
##  [981]  1.028323975  0.131341277  0.526939295  0.022013632  0.249990159
##  [986] -0.212158169  0.940257153  0.990199738 -0.729676147  0.958052650
##  [991]  1.619788116  0.996787539 -0.036054967 -0.608895431 -0.155775209
##  [996] -0.002758516  1.084558016  0.243386441  0.535920852  0.825089599
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

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.44372161   0.21861150 
##  (0.06913102) (0.04887850)
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
## [1] -0.4135178 -0.3448382 -1.0167435 -0.4427503 -0.3884158  0.4676421
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
## [1] -0.0398
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9061993
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
## t1*      4.5 0.02192192   0.8981666
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 3 4 5 7 9 
## 3 1 1 1 2 1 1
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
## [1] -0.0259
```

```r
se.boot
```

```
## [1] 0.9084407
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

