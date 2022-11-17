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
## 1 3 5 6 7 8 9 
## 1 1 1 3 1 2 1
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
## [1] 0.0426
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
## [1] 2.721182
```

```r
UL.boot
```

```
## [1] 6.364018
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
##    [1] 4.4 4.3 4.9 4.8 4.3 5.6 3.9 5.7 2.9 3.8 4.5 3.5 3.9 5.2 6.3 4.8 3.0 5.2
##   [19] 2.8 3.8 2.4 4.5 3.7 4.9 5.7 3.4 2.9 4.2 4.7 3.5 4.1 4.5 3.7 3.6 4.8 3.8
##   [37] 4.4 4.4 3.0 4.9 5.9 3.8 3.4 3.7 3.8 5.8 4.5 5.4 5.2 4.1 2.7 4.6 4.5 3.6
##   [55] 4.7 5.6 4.0 3.0 4.4 5.9 4.2 3.7 3.6 4.4 5.0 3.7 3.7 4.8 3.6 4.9 4.1 4.9
##   [73] 4.5 5.0 2.7 3.1 4.8 5.9 4.9 3.9 5.5 4.5 3.8 3.9 5.4 2.3 3.6 3.3 4.7 4.5
##   [91] 5.1 5.5 5.6 7.0 5.9 5.1 4.7 3.4 5.3 3.6 5.8 5.7 3.6 3.6 3.4 5.0 4.2 3.1
##  [109] 4.5 3.8 4.3 4.1 3.2 5.1 3.4 3.0 3.9 4.0 4.9 4.4 4.3 3.7 3.5 6.6 5.3 3.1
##  [127] 5.1 5.9 2.6 3.1 4.8 4.8 3.4 5.4 3.8 6.5 6.5 5.2 6.0 4.6 5.1 4.0 3.9 3.3
##  [145] 4.6 4.4 3.0 5.7 3.4 5.6 5.5 3.7 5.2 5.4 3.8 4.4 4.3 5.6 4.3 5.7 3.2 5.3
##  [163] 4.4 5.4 4.5 4.7 3.7 3.0 3.9 5.6 5.3 4.1 4.9 5.6 4.0 4.3 4.5 5.6 3.9 2.5
##  [181] 3.9 5.0 3.1 5.2 5.1 4.0 4.4 3.0 3.9 5.0 4.9 3.8 6.5 4.2 4.5 4.6 5.2 4.9
##  [199] 4.6 3.8 3.3 4.0 3.2 3.9 5.6 3.8 5.0 5.7 4.5 4.6 5.6 5.1 4.2 4.1 4.7 4.1
##  [217] 5.7 5.7 4.7 3.5 4.7 3.8 6.9 4.4 5.0 4.2 6.2 5.2 6.2 4.0 3.3 4.2 5.4 5.1
##  [235] 4.3 3.8 4.8 4.8 5.1 2.9 6.5 4.6 4.8 4.8 3.5 4.2 4.0 4.1 5.3 4.5 5.2 5.2
##  [253] 5.1 5.7 4.4 4.8 4.9 5.8 4.4 3.7 5.0 5.4 4.1 3.3 3.5 4.5 4.7 4.5 5.3 5.4
##  [271] 3.7 5.4 4.2 3.1 5.5 2.7 5.3 5.2 3.4 4.5 3.6 4.5 4.9 4.1 3.6 3.6 3.2 4.2
##  [289] 4.8 4.2 5.1 3.7 5.9 3.6 4.4 3.3 3.4 3.3 4.8 4.9 5.8 3.5 4.7 3.8 2.8 5.4
##  [307] 4.5 3.6 3.9 4.5 4.2 5.4 4.4 5.8 5.1 4.6 4.0 4.0 4.1 4.3 4.3 5.7 4.4 3.2
##  [325] 6.3 5.9 4.3 4.6 4.4 4.9 4.0 4.2 3.4 4.9 4.7 3.8 4.3 5.0 3.8 3.4 5.4 5.2
##  [343] 5.0 4.6 5.2 4.5 5.1 4.4 3.7 5.1 4.8 5.0 3.6 5.2 5.8 2.1 4.5 2.5 4.3 4.5
##  [361] 3.9 3.7 4.3 5.8 4.0 4.3 4.4 4.4 4.3 4.9 6.7 5.5 4.4 3.5 4.1 4.7 5.3 3.7
##  [379] 3.9 3.3 5.9 4.1 5.1 5.1 4.8 4.8 4.5 3.2 5.3 3.8 4.5 4.2 3.7 3.2 4.9 4.3
##  [397] 4.4 4.6 4.8 2.6 3.3 5.4 4.9 5.2 5.6 2.7 4.5 6.1 3.0 5.4 5.6 5.1 3.0 4.1
##  [415] 5.4 6.5 3.5 5.7 4.5 4.5 4.5 4.3 3.2 5.3 4.7 5.8 4.0 4.9 3.2 4.1 3.7 5.3
##  [433] 5.4 4.0 4.8 4.2 5.7 3.6 5.2 4.0 6.1 4.0 4.3 5.1 4.4 2.3 4.7 3.2 4.7 3.6
##  [451] 4.5 3.1 3.0 4.9 5.5 3.9 3.3 2.6 3.8 4.3 3.5 3.8 4.8 5.7 3.4 4.7 6.1 4.5
##  [469] 4.0 6.3 5.5 3.4 2.5 3.7 4.1 4.1 3.6 3.5 3.9 3.7 3.5 4.9 3.8 4.0 4.1 2.8
##  [487] 6.2 3.7 5.2 3.9 5.7 3.3 5.9 4.2 5.9 4.9 6.6 5.5 5.5 4.4 3.2 5.3 4.9 3.2
##  [505] 4.5 4.5 5.0 4.2 5.1 4.9 4.7 3.8 3.9 6.4 5.1 3.4 4.6 5.4 5.3 4.7 7.0 2.3
##  [523] 4.3 4.3 3.9 4.3 4.5 3.7 4.9 5.9 4.1 2.9 3.4 2.4 4.1 3.2 4.7 5.0 5.4 3.3
##  [541] 3.3 4.5 3.7 3.1 5.6 5.6 4.2 4.7 3.6 3.2 3.5 5.6 5.5 5.3 4.5 4.6 5.1 3.0
##  [559] 5.4 3.9 4.8 4.8 5.1 4.6 3.9 5.7 2.4 2.9 3.9 4.2 5.2 2.9 2.5 5.3 4.1 4.2
##  [577] 3.7 3.7 4.3 3.7 4.6 4.4 3.6 5.6 5.3 5.2 4.2 6.8 3.6 4.7 3.5 4.8 3.8 6.5
##  [595] 4.3 5.6 4.2 4.1 4.3 3.6 5.1 5.1 5.8 5.9 2.9 3.8 4.8 4.5 5.1 3.5 4.3 2.9
##  [613] 4.6 4.1 5.2 5.1 4.0 5.2 4.1 3.9 5.0 4.2 5.1 3.6 4.3 3.9 5.0 4.2 4.0 4.0
##  [631] 4.0 4.9 4.4 4.5 4.2 4.5 5.7 5.4 4.8 5.2 3.7 4.2 4.5 5.9 4.6 3.7 4.9 5.3
##  [649] 2.6 5.2 5.6 4.1 5.0 3.9 2.8 4.1 2.6 3.1 5.1 4.4 3.6 4.9 4.7 4.3 4.0 2.4
##  [667] 4.6 3.7 4.9 4.5 6.4 2.7 4.2 3.5 5.3 5.6 4.9 4.3 3.8 4.7 5.0 4.2 4.0 5.4
##  [685] 4.4 4.5 3.6 4.6 5.3 4.4 5.6 3.7 3.6 5.3 4.5 4.6 4.2 4.5 4.8 4.4 5.2 3.8
##  [703] 4.5 3.7 5.2 4.7 5.5 6.2 4.3 4.5 3.5 4.6 3.7 5.1 4.8 4.6 3.6 5.0 5.4 4.4
##  [721] 5.0 4.4 4.0 4.6 5.8 3.7 6.1 4.6 4.5 5.3 4.0 5.8 3.3 2.7 5.3 4.9 4.9 4.3
##  [739] 5.7 4.5 4.3 4.8 3.4 4.2 5.1 5.4 4.3 3.8 3.5 4.1 4.2 4.8 4.4 4.8 5.9 4.2
##  [757] 5.1 4.4 4.0 5.7 3.6 4.3 5.3 4.2 4.3 4.7 4.2 3.9 5.0 5.5 4.9 4.9 3.3 4.0
##  [775] 4.8 5.3 4.1 3.4 5.0 4.5 4.0 3.8 5.0 4.4 4.8 3.7 3.3 4.1 4.4 3.8 4.0 4.9
##  [793] 6.3 3.6 4.2 3.3 5.3 3.4 3.0 6.5 4.2 3.8 5.2 6.3 4.8 5.4 2.8 6.7 3.9 3.2
##  [811] 4.6 4.0 2.3 4.1 4.4 5.5 3.4 5.8 4.0 5.7 4.5 4.8 4.9 4.1 5.5 2.6 5.0 4.3
##  [829] 5.5 4.1 4.8 4.8 4.2 5.7 5.3 4.5 4.7 4.1 4.0 4.5 4.0 4.9 4.3 4.6 2.0 5.0
##  [847] 5.6 4.2 4.5 4.3 3.3 4.2 5.4 6.2 6.8 6.0 4.4 5.2 3.9 4.5 4.4 3.3 3.6 5.3
##  [865] 4.5 4.6 3.4 4.8 4.9 4.2 5.6 5.6 4.7 4.3 3.8 4.3 3.6 4.1 4.9 4.4 3.5 5.9
##  [883] 3.8 4.9 5.7 4.8 5.7 3.6 4.1 4.3 5.1 3.2 4.1 2.1 4.7 4.8 4.0 4.4 5.3 5.3
##  [901] 5.0 5.3 5.0 5.3 4.6 3.7 4.9 4.3 3.5 4.8 4.6 4.8 5.4 3.5 4.3 5.2 4.8 5.1
##  [919] 3.1 4.8 4.3 4.9 4.9 4.5 2.3 4.8 4.8 5.0 5.6 4.8 4.2 3.7 4.3 3.0 4.6 5.1
##  [937] 3.8 5.9 4.7 4.1 6.0 5.0 3.8 4.9 4.1 2.8 5.1 3.0 5.5 5.1 3.7 3.6 4.2 3.5
##  [955] 3.8 3.2 4.8 5.0 4.7 4.1 4.1 4.2 4.8 6.7 3.0 5.5 5.2 4.9 4.7 4.0 5.0 4.8
##  [973] 4.1 3.1 6.0 3.8 5.2 5.2 5.9 5.2 3.1 4.7 3.0 4.9 3.1 4.5 5.0 4.9 4.7 4.9
##  [991] 4.6 5.8 6.2 4.0 4.6 3.5 5.3 4.8 6.1 4.7
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
##   2.7   6.2
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
##    [1] 5.7 6.2 5.5 3.6 3.8 6.2 5.4 4.2 3.6 4.3 5.7 2.4 5.3 2.9 4.6 4.8 5.5 4.3
##   [19] 4.5 3.1 2.7 3.1 3.7 5.8 6.1 3.7 5.0 2.2 5.2 5.1 5.4 4.2 4.7 3.0 4.3 4.6
##   [37] 4.8 3.8 5.3 4.6 5.1 3.7 5.6 5.3 5.0 4.3 3.2 4.0 3.5 3.3 5.0 4.6 3.7 3.7
##   [55] 4.4 3.2 4.4 4.5 4.2 3.6 4.5 4.1 4.6 5.3 4.9 4.5 5.1 5.7 4.9 4.4 5.8 2.4
##   [73] 4.6 3.5 4.2 5.9 3.5 3.6 5.5 5.8 4.0 4.7 4.4 2.4 4.2 2.8 4.2 5.8 4.4 6.2
##   [91] 4.7 3.4 4.7 3.8 5.5 4.0 5.5 3.8 5.4 3.6 5.0 4.4 4.2 3.2 3.5 5.6 3.7 4.9
##  [109] 5.5 4.3 4.0 4.2 5.0 5.5 5.1 3.4 5.3 3.6 4.3 4.3 3.0 3.7 4.2 5.0 4.4 4.8
##  [127] 2.2 5.2 4.7 5.2 4.0 3.4 3.3 5.2 3.7 5.5 3.6 5.1 3.2 5.7 3.1 4.1 4.0 6.2
##  [145] 5.2 6.6 5.6 3.9 5.7 3.9 5.9 4.5 4.3 3.4 3.8 4.3 5.1 3.6 5.5 3.3 4.3 3.9
##  [163] 3.7 4.9 4.8 3.8 5.2 3.6 4.7 6.6 5.0 2.8 3.4 5.0 5.8 5.4 5.0 2.9 5.8 6.3
##  [181] 6.2 4.6 4.3 5.6 1.0 4.1 5.0 3.6 3.7 4.8 4.9 5.3 4.9 5.2 3.0 4.0 3.7 3.3
##  [199] 4.6 5.1 5.2 4.2 4.3 4.1 4.8 4.4 4.1 3.9 5.1 5.1 5.1 3.3 4.5 6.2 3.3 4.0
##  [217] 4.3 4.9 3.7 3.7 4.6 4.9 5.0 4.1 5.2 6.6 4.0 5.2 4.0 2.0 4.8 4.4 5.5 6.0
##  [235] 4.2 3.7 5.6 4.5 4.7 4.2 5.4 4.0 4.1 5.1 3.5 4.0 4.1 4.0 4.5 4.8 4.5 5.1
##  [253] 3.5 4.3 4.2 2.9 4.0 5.2 4.1 4.6 5.4 4.9 5.0 3.5 5.5 6.1 5.0 5.3 4.2 4.4
##  [271] 4.3 4.7 3.4 4.8 5.7 5.0 3.9 5.5 5.5 5.5 5.5 3.4 5.3 4.3 4.0 3.8 3.7 4.7
##  [289] 5.3 6.3 5.1 4.5 5.1 3.5 5.5 3.9 2.9 4.7 5.8 3.6 4.6 6.1 3.7 5.6 4.0 4.4
##  [307] 2.6 3.9 6.0 4.1 4.8 5.5 3.9 4.6 4.5 5.2 4.9 4.4 3.4 4.9 3.7 4.0 4.6 5.5
##  [325] 3.2 4.2 5.5 4.8 3.8 5.3 3.8 4.6 5.5 2.1 2.9 5.7 4.2 4.1 3.7 4.1 4.4 4.3
##  [343] 4.8 5.3 5.1 6.0 6.4 4.8 4.4 2.8 3.6 5.1 4.3 5.0 4.9 4.3 4.3 3.8 3.9 5.0
##  [361] 5.6 4.5 3.2 4.7 2.7 4.6 6.1 5.5 4.6 4.9 5.4 5.1 5.7 5.0 3.8 5.6 4.5 3.5
##  [379] 3.9 4.4 3.7 4.9 5.8 4.0 3.8 5.2 5.7 4.3 4.0 5.3 4.3 5.5 3.4 5.9 5.6 6.7
##  [397] 4.6 1.7 4.3 3.7 4.4 4.6 5.6 4.6 3.8 3.5 4.9 4.5 4.5 5.1 4.4 5.5 4.8 3.0
##  [415] 3.2 5.6 3.9 5.1 4.6 4.2 4.8 2.6 4.9 3.6 4.4 4.6 4.8 6.3 3.8 5.1 3.1 6.2
##  [433] 4.2 4.7 5.0 4.6 5.4 5.4 3.1 5.2 5.9 4.8 4.9 4.7 5.4 3.4 4.7 3.7 5.3 3.9
##  [451] 4.4 3.9 5.4 3.9 4.4 3.2 4.6 4.6 3.6 2.3 6.4 6.6 4.5 3.6 2.5 5.4 7.2 5.1
##  [469] 3.3 3.7 6.3 4.4 4.9 2.9 3.5 4.1 3.8 5.3 3.4 5.4 4.6 4.2 4.0 4.5 4.7 5.6
##  [487] 4.5 5.3 3.6 5.9 4.6 4.5 3.5 4.5 5.6 4.0 4.9 5.0 4.8 4.7 3.3 3.7 4.3 6.3
##  [505] 4.2 4.8 4.3 3.6 3.9 3.5 3.8 2.1 4.8 6.4 4.9 5.5 5.7 4.3 4.3 4.6 6.8 4.4
##  [523] 5.3 2.9 4.1 4.1 4.5 6.7 5.3 2.5 5.6 3.8 5.4 5.0 4.0 4.0 3.1 4.5 3.4 4.7
##  [541] 4.0 4.2 3.4 3.3 5.6 3.6 4.6 3.3 2.7 5.7 5.0 4.8 3.3 3.4 4.9 4.9 4.6 3.5
##  [559] 5.2 4.8 3.6 3.2 5.2 5.0 5.4 5.5 3.3 4.8 3.5 6.5 4.6 4.9 5.5 4.9 4.3 3.4
##  [577] 5.0 4.5 4.2 3.9 4.9 6.2 3.9 5.2 4.5 4.1 5.7 3.7 4.8 4.6 3.7 4.5 3.3 3.4
##  [595] 4.1 5.0 4.1 5.4 5.1 5.6 3.4 6.2 6.2 4.0 5.1 6.2 3.4 4.3 5.1 6.2 4.8 3.1
##  [613] 4.6 4.8 3.8 4.4 4.5 2.8 3.3 4.9 4.7 5.3 5.0 5.5 3.4 4.0 5.2 4.1 4.5 4.1
##  [631] 3.3 5.3 4.9 5.1 4.3 4.7 4.5 4.4 6.4 4.7 6.0 3.5 3.7 3.1 5.3 5.2 4.0 5.7
##  [649] 3.4 4.1 5.2 5.3 4.9 4.4 3.4 4.8 5.5 4.3 3.1 3.6 3.8 4.1 5.0 3.8 3.6 5.7
##  [667] 5.4 5.0 5.5 4.2 5.9 5.9 4.7 3.9 4.0 4.7 3.4 3.8 4.5 5.8 3.9 4.0 3.6 4.0
##  [685] 5.0 3.8 3.4 3.5 5.4 5.4 4.6 4.9 3.5 5.6 4.9 3.8 4.5 3.9 5.1 4.5 5.6 5.4
##  [703] 4.8 5.6 3.3 5.2 4.0 3.7 4.4 5.8 4.9 4.5 4.6 4.7 4.7 5.2 4.5 2.8 4.5 4.2
##  [721] 4.3 5.7 4.8 4.0 5.2 4.6 5.2 5.7 3.1 6.5 4.6 5.6 4.6 3.8 4.9 4.2 5.1 6.3
##  [739] 5.4 3.5 3.7 4.6 3.9 5.2 6.0 3.4 5.0 4.4 5.2 6.0 4.5 2.7 4.8 5.2 5.1 4.6
##  [757] 4.3 5.1 5.0 5.2 5.2 4.5 4.6 5.9 4.5 4.5 3.6 4.7 5.9 5.9 3.2 5.8 4.3 6.0
##  [775] 4.7 4.3 4.2 5.5 3.8 4.8 5.0 3.2 3.7 4.9 4.1 4.1 2.2 5.0 5.1 3.5 5.1 5.2
##  [793] 4.3 4.7 4.6 5.6 4.3 4.9 3.8 4.0 4.8 4.0 3.5 4.5 4.3 4.8 4.9 4.9 5.2 4.3
##  [811] 4.3 6.3 5.0 3.9 4.2 4.5 4.6 3.8 4.5 4.4 5.9 4.0 4.1 6.0 4.4 3.7 5.8 4.3
##  [829] 4.7 4.5 5.3 3.5 5.2 4.8 4.0 3.3 4.3 3.3 4.2 5.0 3.0 2.1 4.0 4.2 5.7 5.1
##  [847] 5.1 4.0 5.0 4.4 3.7 4.5 2.7 3.0 6.1 3.3 2.8 4.3 4.5 4.3 4.6 4.1 4.9 6.1
##  [865] 4.1 6.0 4.9 5.1 4.3 3.7 4.5 5.1 4.2 5.1 4.3 3.2 3.2 4.6 3.2 3.1 4.8 3.6
##  [883] 4.6 5.2 3.9 4.1 5.1 6.2 4.3 3.4 2.5 4.0 4.6 5.2 5.7 4.0 4.8 4.2 3.3 5.7
##  [901] 5.2 4.7 5.1 4.1 5.4 3.4 3.6 3.1 2.9 7.0 4.4 4.6 4.7 4.0 3.7 5.8 5.3 4.2
##  [919] 3.4 5.2 4.0 3.5 5.4 3.4 4.4 6.2 3.8 4.3 3.2 3.8 3.2 5.7 4.6 5.9 4.5 6.7
##  [937] 3.4 5.6 5.0 4.6 4.0 4.9 4.7 4.6 4.6 6.1 3.4 3.5 5.0 4.7 6.2 6.1 4.0 6.6
##  [955] 4.6 4.7 3.2 4.3 3.6 5.1 4.2 2.9 4.6 4.1 5.0 5.8 3.7 5.7 3.8 4.2 5.0 3.7
##  [973] 3.9 3.5 4.8 4.8 5.4 3.5 4.2 4.8 3.2 3.9 4.4 3.4 3.9 5.5 4.8 4.6 4.6 5.7
##  [991] 4.3 3.5 4.1 3.0 4.2 5.2 4.2 5.1 5.1 5.0
## 
## $func.thetastar
## [1] 0.01
## 
## $jack.boot.val
##  [1]  0.49969880  0.37768595  0.25490196  0.11624650  0.17298137 -0.05820106
##  [7] -0.14133739 -0.24590643 -0.37896175 -0.50260116
## 
## $jack.boot.se
## [1] 0.9391087
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
##    [1] 4.8 5.6 4.2 5.2 4.0 4.7 3.1 5.0 6.0 4.0 4.3 4.4 5.7 4.0 3.8 3.8 4.4 4.8
##   [19] 4.6 3.2 3.9 2.9 5.3 4.0 5.0 3.5 4.8 5.6 5.1 4.3 3.1 5.5 4.7 4.6 4.9 5.4
##   [37] 5.3 3.4 5.1 4.6 2.6 5.3 5.2 4.1 3.7 3.0 4.5 5.1 5.6 3.9 4.1 5.0 3.8 4.7
##   [55] 5.2 5.2 4.3 3.2 4.7 5.7 3.2 5.4 5.4 3.9 4.8 3.4 4.7 3.8 3.7 5.1 3.3 3.7
##   [73] 3.7 5.8 4.8 3.1 4.2 4.6 4.1 4.5 6.5 6.8 4.6 4.1 4.7 4.1 3.2 4.3 4.6 3.5
##   [91] 3.7 4.1 2.8 3.9 5.7 5.7 4.3 4.3 4.0 4.8 4.5 2.9 6.1 4.1 3.6 4.5 4.9 4.4
##  [109] 3.6 3.4 5.7 5.7 6.4 3.6 4.8 4.5 4.1 3.5 5.0 5.8 4.2 3.6 4.8 3.7 3.2 4.6
##  [127] 4.2 4.8 5.3 5.4 4.9 5.2 2.6 4.2 3.7 4.8 4.0 4.8 3.6 3.9 4.8 4.4 5.4 4.9
##  [145] 3.7 4.1 5.4 3.8 5.2 4.7 4.8 4.3 4.0 6.3 3.6 3.7 3.8 4.5 4.2 2.2 4.7 3.5
##  [163] 4.4 5.8 5.3 4.4 3.9 3.7 4.2 4.2 3.5 2.9 3.3 4.8 3.5 4.0 5.9 3.3 3.5 5.0
##  [181] 4.2 5.4 5.0 5.1 2.5 3.1 4.5 3.0 5.6 3.5 5.1 5.6 5.7 5.5 4.5 4.9 4.5 3.6
##  [199] 5.2 5.9 5.7 4.9 4.8 3.9 3.0 6.0 4.7 5.2 6.2 3.7 3.8 3.3 2.3 4.3 5.3 5.5
##  [217] 5.1 4.5 5.0 3.0 3.5 5.7 3.0 4.3 6.1 5.1 3.8 5.8 3.7 4.6 4.4 4.8 5.1 3.7
##  [235] 4.9 4.9 6.0 5.1 3.3 4.3 4.6 2.5 5.0 5.0 6.0 3.7 3.3 4.2 5.7 3.5 4.7 3.6
##  [253] 5.8 4.5 4.0 4.6 4.6 3.7 6.7 5.5 5.0 4.5 4.5 4.6 4.9 5.4 3.3 5.0 5.4 5.8
##  [271] 5.6 4.3 4.5 4.3 2.9 5.7 4.0 3.9 4.5 5.2 4.7 3.3 2.8 3.3 3.8 4.4 4.6 3.4
##  [289] 5.0 3.6 4.7 5.0 4.4 5.2 4.4 5.8 4.0 4.3 3.4 4.7 5.0 5.4 3.1 4.5 3.0 4.5
##  [307] 5.4 4.8 3.8 3.3 5.7 4.9 6.5 3.4 4.3 4.3 3.3 4.5 3.6 4.6 4.7 4.5 4.8 4.4
##  [325] 3.5 3.9 6.5 3.5 3.5 3.2 4.8 4.3 3.9 6.2 3.1 4.0 5.6 5.0 5.3 4.7 5.3 4.7
##  [343] 6.2 4.8 5.8 5.0 3.8 4.8 5.0 3.7 3.3 3.0 3.7 4.6 4.3 3.6 4.1 3.4 5.1 5.4
##  [361] 4.1 5.0 4.5 5.4 3.8 5.6 3.5 4.3 4.4 5.4 5.3 5.0 4.0 4.6 3.0 4.0 4.8 3.9
##  [379] 4.4 4.0 4.1 3.8 4.5 4.0 4.1 4.1 4.3 3.8 2.9 5.9 4.4 5.7 4.9 4.1 4.3 4.4
##  [397] 4.7 5.2 3.9 4.2 3.4 4.5 3.6 4.0 5.3 5.2 3.4 4.7 3.2 5.3 2.6 5.6 4.5 4.1
##  [415] 4.2 4.2 4.2 2.7 4.1 5.5 3.6 4.1 4.4 4.8 3.7 5.3 3.8 4.7 6.1 4.3 4.9 3.4
##  [433] 3.4 6.5 6.6 2.6 4.6 4.4 2.9 6.4 5.7 5.2 5.8 4.6 4.9 4.5 6.4 6.5 3.4 4.8
##  [451] 4.3 4.5 4.5 3.9 4.0 5.2 5.2 3.7 5.9 2.1 6.1 3.8 4.0 5.9 3.9 4.8 4.7 5.1
##  [469] 4.8 6.4 4.9 4.9 4.6 2.3 4.6 4.8 5.0 3.4 3.8 3.8 5.5 3.1 4.0 4.0 3.9 6.5
##  [487] 3.2 3.3 4.4 4.6 4.5 4.3 5.5 5.0 3.2 4.0 5.5 4.5 3.0 3.8 5.6 4.5 4.2 4.8
##  [505] 4.5 5.4 4.2 5.4 5.8 5.1 4.5 4.3 5.0 3.5 5.6 6.7 5.3 4.3 4.8 3.4 3.8 4.8
##  [523] 2.8 6.1 3.2 4.3 4.4 3.5 4.4 5.1 2.6 5.5 3.8 4.9 3.7 6.4 4.3 4.3 3.4 5.2
##  [541] 2.9 3.9 1.9 4.5 5.6 4.9 4.2 5.2 3.1 5.7 5.4 6.0 3.0 6.4 6.3 4.5 3.6 5.6
##  [559] 4.6 4.4 3.9 5.6 3.5 5.1 4.8 4.4 5.4 5.3 4.9 5.0 4.1 4.7 5.3 6.1 4.4 4.9
##  [577] 6.4 2.7 5.1 2.9 5.2 3.2 3.6 4.4 5.2 6.0 4.5 4.8 4.4 4.1 3.4 4.1 3.7 3.4
##  [595] 3.6 3.7 5.2 4.2 5.1 4.4 5.1 5.5 5.3 4.1 5.9 3.8 5.1 3.3 5.4 4.3 5.7 5.2
##  [613] 5.2 4.3 4.9 6.3 4.2 4.1 5.6 5.0 5.1 3.3 3.8 5.5 5.4 4.9 4.9 4.5 5.1 3.8
##  [631] 4.2 5.3 3.2 3.2 4.9 4.1 3.8 5.1 3.8 4.1 6.3 4.5 4.2 4.6 5.5 4.8 5.0 5.3
##  [649] 3.1 5.7 5.3 4.3 5.4 6.9 2.8 4.5 5.8 3.3 6.0 3.9 3.8 5.3 5.1 4.5 5.3 4.4
##  [667] 3.4 6.2 3.7 5.7 2.8 5.7 5.2 5.2 6.5 5.3 4.9 5.2 5.0 4.6 3.9 3.6 3.9 3.9
##  [685] 4.4 4.3 3.7 4.5 5.6 3.4 4.1 5.1 5.3 4.4 4.3 4.3 3.7 4.9 5.2 4.8 3.8 4.2
##  [703] 2.8 3.5 6.4 4.4 5.6 4.6 3.4 3.8 3.9 3.7 4.1 4.5 3.9 3.2 5.3 4.8 4.5 3.5
##  [721] 2.8 4.6 5.5 4.8 4.6 4.7 3.2 4.7 4.8 4.3 3.8 3.6 4.5 4.4 5.8 4.2 4.3 3.9
##  [739] 5.3 4.2 4.6 3.7 6.4 5.6 4.6 3.1 5.9 4.7 3.9 5.7 4.8 4.7 4.2 4.9 4.2 4.5
##  [757] 5.1 5.0 3.3 3.7 4.3 3.1 5.4 3.1 4.1 4.6 4.4 4.7 3.1 5.5 4.8 4.6 5.2 3.8
##  [775] 5.9 4.8 5.2 5.9 4.7 4.2 3.7 4.2 5.5 3.1 4.3 5.2 3.7 4.4 5.5 3.5 4.8 4.8
##  [793] 2.6 3.6 3.8 3.9 5.2 3.8 4.6 4.7 6.2 2.9 4.1 3.0 5.6 2.5 3.9 4.9 4.5 3.8
##  [811] 3.0 5.5 4.3 4.6 3.9 3.7 3.8 5.0 5.2 3.8 4.0 4.3 6.0 4.2 3.6 3.6 3.5 3.7
##  [829] 4.4 4.6 2.9 4.2 5.0 4.4 3.6 3.9 3.3 3.1 4.6 4.7 4.8 3.9 4.3 3.4 5.6 4.9
##  [847] 5.4 5.2 3.7 5.3 4.1 4.1 6.4 4.6 6.0 4.2 4.0 3.6 4.0 5.2 4.4 4.7 5.1 3.6
##  [865] 4.9 5.0 4.7 4.5 4.8 3.1 4.2 3.3 4.6 4.3 4.7 4.0 4.7 4.1 4.6 5.7 5.0 6.0
##  [883] 4.6 4.3 7.3 4.6 5.5 3.6 4.1 5.1 7.4 4.8 2.2 4.1 2.9 4.4 5.4 2.6 5.5 3.8
##  [901] 3.2 2.6 5.4 4.9 4.9 4.6 3.9 6.6 4.1 3.6 4.9 5.3 5.1 3.4 4.1 3.5 2.6 5.7
##  [919] 5.6 4.4 4.3 3.9 4.2 5.4 5.2 3.1 3.9 3.1 3.6 4.7 4.0 4.6 4.8 3.9 5.2 5.1
##  [937] 4.3 3.9 5.6 4.0 3.9 5.6 5.5 4.8 3.6 3.7 5.7 4.5 4.6 5.8 3.7 4.7 4.7 3.3
##  [955] 4.7 3.1 4.2 4.8 2.9 3.5 3.9 3.4 5.2 4.8 6.2 4.5 5.1 5.0 5.2 3.8 5.3 3.6
##  [973] 4.4 7.4 4.5 3.3 5.9 4.9 5.3 3.0 3.5 3.3 5.4 3.9 4.7 5.1 5.5 5.0 4.9 5.0
##  [991] 4.9 4.6 4.4 4.3 4.1 4.6 5.1 5.0 5.8 4.4
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.3 5.2 5.2 5.0 4.9 4.7 4.6 4.4
## 
## $jack.boot.se
## [1] 1.037497
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
## [1] -0.03138034
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
##   2.1975728   3.7232371 
##  (0.9179927) (1.7463661)
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
## [1] 0.417950 0.563413 1.402148 1.371187 1.151121 1.612278
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
##    [1]  0.8563583989 -0.3080745567  0.0884861483 -0.2063938044 -0.3699204466
##    [6] -0.1608560453  0.1394099611 -0.9499432067  0.5203263699  0.2886520533
##   [11] -0.3577613215  0.9168814983 -0.1858236730  0.0954787354  0.6160131014
##   [16] -0.4086284775 -0.4387375084  0.0471429946  0.3260330976 -0.2534672695
##   [21] -0.7064722679  0.0795898631 -0.6447466842  0.5826357040 -0.0469963824
##   [26] -0.1465128350 -1.1343777559 -0.4647274314  0.1656607743  0.4796292160
##   [31]  0.1857261547  0.3441012083  0.5499089307  0.0257197805  0.5552476235
##   [36] -0.7818276932 -0.0131638650  0.5260297177  0.6673232512  0.3487444598
##   [41]  0.3182670995  0.8037023211  0.4721260239 -0.2888347596 -0.4532860927
##   [46] -0.9147319080  0.2638251249 -1.2345855683  0.3109587489  0.1521543493
##   [51]  0.0041097913 -0.8144970867  0.1213660180  0.9476935100  0.2216839631
##   [56] -0.7651259279 -0.2071929861  0.4014500547  0.1893494691 -0.0751068329
##   [61]  0.1869982407 -0.1895611051  0.9051473356 -0.1500880853 -0.8643237159
##   [66]  0.7384933463  0.1154401661 -0.1234101946 -0.2675016860  0.1674303631
##   [71]  0.0733936616 -0.1582878523  0.4511048607 -0.5360702900 -0.6657802957
##   [76] -0.3759728636 -0.1249763629 -1.8257590217  0.1040983491  0.5755524731
##   [81] -0.5027968932  0.3597630878  0.3260485440  1.1116807420 -0.2466534121
##   [86] -0.1156129167  0.2206407586 -0.1342843837  0.3293821817  0.1619990463
##   [91]  0.3912274574 -0.1554710452  0.7327791526 -0.2459013776 -0.6006801439
##   [96] -0.5712183502  0.7454470344 -0.7225281505 -1.0531301026 -0.5444355516
##  [101] -0.6544519559  0.0430188942 -0.3025664504 -0.7382161378 -0.4983110553
##  [106] -0.1374291943 -0.4309461439 -0.3813229776 -0.6163309339  0.1062572048
##  [111]  0.5594272463  0.1235501256  0.1820004609  0.2592975717  0.0543896854
##  [116]  0.3486424887  0.5129760150 -0.5423033025 -0.1367980798 -0.1428257559
##  [121] -0.3534044238  0.1904009925 -0.2382256912 -0.3631416703  0.1320554748
##  [126] -0.3629044760 -0.2177087071  0.1820299337 -0.3152497280 -0.7513717303
##  [131] -0.1670340601  0.2744589332 -0.0071511029 -0.2401837347  0.0897453210
##  [136]  0.0829524946  0.0779176227  1.0236770767  0.1259537541  0.3887565480
##  [141] -0.5549753837  1.0498211604  0.0745118146 -0.9242854481  0.9360456414
##  [146] -0.1044089311 -0.4603577175  0.5321899713  0.6181474415 -0.4026617956
##  [151]  0.7849706742 -0.3946702668  0.0386869166 -0.4365494447 -0.0793559810
##  [156] -0.3423700940  0.8309446059  0.4900713325  0.3302542203  0.2846900651
##  [161]  0.2874491338 -0.7383987624  0.2475544201  0.3279472628 -0.5256049718
##  [166]  0.7266587017 -0.3852812985  0.1229294904  0.3247755279 -0.1786160348
##  [171] -0.1252356992  0.0616396318 -0.0592975615 -0.3425931193  0.3367333596
##  [176]  1.5436964503  0.0629309567 -0.2160621442 -0.0852186599  0.3478222470
##  [181]  0.4075812577 -0.0297646037  0.2034023564  0.0218960617  0.1930994899
##  [186]  0.1414690320  0.3206905074  0.4291248284 -0.1322987068  0.1153013176
##  [191]  0.8807289199 -0.0908540403  0.0644931588 -0.3005094605  0.5497484103
##  [196]  0.5374725562 -1.5079740284  0.5838802213 -0.4100793145 -0.0443438532
##  [201] -0.2067688356  0.4464657590  0.3887361714 -1.7784761957  0.1712048019
##  [206]  0.4170743749  0.3087400592  0.8386155155 -0.9402435276  0.0161458376
##  [211]  0.0188677755  0.1571723141  0.7135395863  0.2270940048 -0.3104306707
##  [216]  0.0253295751 -0.1677908020 -0.6758886651 -0.4334058057  0.3572763550
##  [221]  0.5549947785  0.3239906229 -0.0175034587 -0.4381786600 -0.0654883149
##  [226]  0.1533026959  0.1341934076  0.2479699196 -0.2089372495  0.3905174974
##  [231] -0.4227457224 -0.5763951665 -0.5894911053 -0.2569387194  0.1763746238
##  [236] -0.2459508556 -0.0347080717  0.4909514679 -0.3858666114  0.2157148250
##  [241]  0.3174020010  0.3459875832  1.2368979408  0.3907350634 -0.2756703072
##  [246]  0.2122839290 -0.9566160818 -0.9188707701 -0.3828019729 -0.9084247676
##  [251] -0.2727553709 -0.0533443837 -0.5721860491 -0.1012882311  0.3473630866
##  [256] -1.3121080673  0.5336366587  0.4050580827 -0.2680380428  0.5966533258
##  [261]  0.5596306931  0.4823428509 -0.2652023404  0.0689655160  0.4876216554
##  [266]  0.5606539072 -0.5267968282 -0.3171583501 -0.2569646328 -0.2117567611
##  [271] -0.5787364205 -0.0867441573  0.8162014949 -0.1786073717 -0.2475588135
##  [276] -0.4754135442  0.1306755100  0.7977227963  0.3035902733 -0.4199018304
##  [281]  0.4653512794 -0.3321954747  0.5347364633  0.1532839474  0.1965589258
##  [286] -0.6121409596  0.6913429084  0.3255336280 -0.4798081063 -0.4417669377
##  [291]  0.4435643216 -0.0560816779 -0.2865652996  0.3002040593  0.7175306084
##  [296] -0.5347488656  0.3693397080  0.8023303585  0.3377881018 -0.7217466766
##  [301] -0.5161780238  0.2469965247  0.5458933224  0.8772140357  0.5636665942
##  [306] -0.0066298936  0.3591778859  0.8225103719  0.4277664413 -0.3961201791
##  [311]  0.3917521745  0.5730689822 -0.2404285565  0.5917623380  0.4357046330
##  [316] -0.4678809449  0.8445411904  0.6467315857 -1.1863262536 -0.3592251340
##  [321]  0.6522397838 -0.5773176642 -0.3546857438  0.2498817853  0.4450762516
##  [326] -0.6989077760  0.2044173506 -0.9729356447  0.2110496339  0.0380005988
##  [331]  1.0384397882  0.5182848943 -0.1350265533  0.0432518370 -0.2112325134
##  [336] -0.0111848190 -0.5698715029  0.0999686260  0.8716056757  0.1407168779
##  [341]  0.0961728910 -0.4391910263 -0.0232582458 -0.7297698876 -0.2292177185
##  [346]  0.2134324664  0.8402484950  0.2356545412 -0.2297104747 -0.1993317895
##  [351] -0.5301856797 -0.5250469088 -0.1033269736 -0.1906248332  0.2684279476
##  [356]  0.3862428929 -0.1519688543 -0.6339508547  0.3481428689  0.3730614817
##  [361] -0.6221156089  0.1127413982 -0.0266730921  1.1489660202 -0.2811381837
##  [366]  0.1672973910  0.0638974760 -0.3922687933 -0.1619796553  0.2624782753
##  [371] -0.0643689731  0.4283286782 -0.1319032773 -0.1801561374  0.0734984796
##  [376]  0.1143415823  0.3107334258 -0.6007468563 -0.1764633855  0.7782567834
##  [381] -0.2160621442  1.4051489008  0.3480584378  0.4640766764 -0.0995642932
##  [386]  0.5947451271 -1.1709272754 -0.3253494948  0.5654981047  0.0483227966
##  [391]  0.1539013015  0.3034487005 -0.8652665740  0.8096472235  0.4157777070
##  [396]  0.3444924699  1.0077758138  0.8762713857  0.0373575048  0.1483802688
##  [401]  0.2287935836  0.4095674414  0.2139199421  0.1503471574 -0.0883250234
##  [406]  0.2201540591 -0.8578943999 -0.3067370306  0.1780460011  0.0857175041
##  [411]  0.8984903187 -0.6719452473 -0.3786287210 -0.0065555697 -0.2529698650
##  [416]  0.1665338681  0.6987308213  0.3486010995 -0.1906248332 -0.4170170334
##  [421] -0.1217971389 -0.5910992420 -0.4281992349  0.8590690322  0.0220135255
##  [426]  0.4364824481 -0.2938038028 -0.2209183164 -0.3429147725  0.6313245009
##  [431]  0.1056673251 -0.0945300534  0.3666024874  0.3344926916 -0.4532860927
##  [436] -0.1277123299 -0.3219869595  0.2967744368 -0.5696397242 -0.3618941681
##  [441] -0.0504122382  0.8841749828 -0.1952271587 -0.0421101121  0.1713318243
##  [446]  0.3758562048 -0.3325340924 -0.2255918583 -0.1679247481 -0.6460451426
##  [451]  0.5565836461  0.1597992697 -1.0350902324 -0.0812932582  0.8154961122
##  [456]  0.0704429120 -0.3569352275 -0.9765234183 -0.6932630967 -0.5149412658
##  [461]  0.0349076867  0.7841661933  0.1170593084  0.0831019889  0.1350074827
##  [466]  1.2715831896 -0.5140384216 -0.1429333234  0.1013272154 -0.2044798458
##  [471]  0.1945707112  0.4485329544  1.2884777959  0.4261624946  0.2278384568
##  [476] -0.9583214150 -0.7166687395 -0.2246610906 -0.2266617558 -0.1013486031
##  [481]  0.2783110065  0.4718348385  0.1039908703  0.5637190647 -0.6257425847
##  [486]  0.7617374660  0.3667475077  0.3304550751  0.0559926434 -0.0715194337
##  [491] -0.1914971015 -0.6365466978  0.3509306896 -0.1928549511  0.1749289029
##  [496]  0.7990968112  0.5220503164 -0.1917117343 -0.0770450833 -0.0301139608
##  [501]  0.0950685252  0.0331407608 -0.0256671664  0.7228234725  1.1663347230
##  [506]  1.0147483475 -0.7865892989 -0.0423573608 -0.0732149433  0.8870732817
##  [511]  0.1647995929 -0.3000422877  0.4855812848  0.2049917361  0.0891181097
##  [516] -0.4874331139 -0.1265458065 -0.3312497716  0.0616172986 -0.5142102465
##  [521]  0.1061933720 -0.8088389374 -0.3920188153  0.4178110957 -0.2354921574
##  [526]  0.0884861483 -0.5742530534 -0.3404780127 -0.0618418593  0.4285122778
##  [531] -0.7086006448  0.5960292040 -0.0243338893  0.5804085816 -0.0845388046
##  [536]  0.0894233366  0.1524883364 -0.4953414459 -0.6332055735 -0.7577168110
##  [541]  0.5736106252 -0.2199682809 -0.2360682311 -0.3990340148 -0.3257126073
##  [546] -0.7484480715 -0.2253658397 -0.0009297990 -0.2255918583 -0.1893956527
##  [551]  0.1606403014 -0.2591122906  0.9574879734  0.1841837086 -0.7438197874
##  [556]  0.4636684133 -0.2441089485 -0.0762977622  0.1255544319  0.4344855091
##  [561] -0.2713429551  0.4530635817 -0.0245437343 -0.3241944207  0.2299642442
##  [566] -0.5246239479  0.0652636512 -0.3554738175  0.3090658639 -1.1710857794
##  [571] -0.4922255110 -0.2796464891 -0.8471928392 -0.3132023787  0.2815188784
##  [576] -0.1904962806 -0.4187231506 -0.2766932432 -0.5935031498 -0.1570319549
##  [581]  0.6674229559 -0.4514116819  0.3233728412 -0.4780246557 -0.0106688954
##  [586]  0.1120818536  0.6130467451  0.0917428929 -0.1061966631 -0.2087545983
##  [591] -0.0316815946 -0.1635578199  0.3838583966  0.4071975003 -0.0955886099
##  [596] -0.1837308738 -0.7165982835 -0.3934118147 -0.2962519934 -0.1438682013
##  [601]  0.0099148455  0.1255163515 -0.0266497084  0.1800050144 -0.2439331540
##  [606] -0.5893535172  0.4362155637 -0.5488690894 -0.7913895127  0.5928341364
##  [611] -0.4983975191 -0.7067668313  1.9607173959  0.8949684891 -0.0579143146
##  [616]  0.3560306544 -0.0807324393  0.3460395203  0.7228234725 -0.1799137773
##  [621]  0.2714034673  0.1211173354  0.4159934243  0.0471374384  0.3539901105
##  [626] -0.5256429293  0.1108341865  0.5449302436  0.1445803205  0.2456522443
##  [631]  0.1001406713  0.0285908293  0.2591376910  0.8762313830  0.0006839965
##  [636]  0.3236043924 -0.2534672695 -0.9604211312  0.7449765001 -0.1171586584
##  [641]  0.3674911300  0.0053341899  0.0133065441 -0.0007456001  0.9873990531
##  [646]  0.1189598746 -0.3524199187  0.6941381747  0.5280085076  0.3028568417
##  [651]  0.1023425868 -0.9503495983  0.9507389519  0.1450352997 -0.8114069285
##  [656] -0.4081436065  0.4499686486  1.2492112455 -0.7225338127  0.2816307296
##  [661] -0.3154852658 -0.9838329480 -0.0056008328 -0.1453187219  0.2522786603
##  [666] -0.2100110997  1.0135426222 -0.5416314460  0.3492261194 -0.2840391585
##  [671] -0.7806950639  0.1766381339 -0.8952298164 -0.1086436531 -0.5641847803
##  [676]  0.0712057389 -0.2925775103 -0.1619796553 -0.2882208932 -0.4536646343
##  [681]  0.2778344007 -0.4733100924  1.4465907172  1.0691940433  0.7455967919
##  [686]  0.4150512884 -0.1117399247 -0.3160852779  0.0365734525  0.6601337028
##  [691] -0.4289911651  0.0924188327 -0.2224904073  0.2193713403  0.7232427058
##  [696] -0.5476566756  1.4892445416 -0.3413237460  0.3414605681 -0.0953356083
##  [701] -0.2434585450  0.7083940140  0.3025363719  0.6146972477  0.6505291836
##  [706]  0.0237816434  0.3039720968 -0.0807482637  0.2324201000  0.5027783925
##  [711]  0.6631955141  0.0258395481 -0.5199002607 -0.0390549349 -0.0129765614
##  [716]  0.3561778796  0.2059711859  0.5760290755  0.3206433092  0.0797657295
##  [721] -1.2113641187  0.3858515842  0.6699346759  0.4413103635 -0.4219535007
##  [726] -0.1930191554 -0.4423828526  0.5386486848  0.4113681573  0.4465976098
##  [731] -0.1809490030  0.6052291715  0.3160164111  0.2962355459 -0.2928631910
##  [736] -0.1408611449  0.3232382044 -0.6723670944 -0.4715269841 -0.5143393048
##  [741] -0.6247855611  0.0241095537  0.4164386464  0.9155050830 -0.9194375285
##  [746]  0.4890756225 -0.1670429335 -0.4168281601 -0.8079878605 -0.2003231935
##  [751]  1.0036497206 -0.6680755670 -0.0513203783 -0.1359496831 -0.4715269841
##  [756] -0.7971912883  0.6187716530  0.1360114145  0.0555764839  0.0228734355
##  [761] -0.1917972366  0.8267512852 -0.4809808641  0.4522536406  0.5042832985
##  [766] -0.4731858392 -0.4007951646 -0.4430227064 -0.3171386770 -0.4114860087
##  [771] -1.2198753572  0.2154572195 -0.5983473930  0.2855186445  0.1053111263
##  [776] -0.9966628135  0.6333162744  0.6668148383  0.9245258887  1.0124699005
##  [781]  0.9182180628  0.8545352996 -0.4355067759  0.8220257326 -0.5051313823
##  [786]  0.0748523854  0.6672749444  0.4885883606 -0.0433202237  0.4807302118
##  [791]  0.9911005314  0.7730641178  0.6780888332 -0.5228348977 -0.4254317767
##  [796] -0.0868601135 -0.2778995793 -0.3444848394  0.1110762101 -0.4911051997
##  [801] -0.6350834114 -0.8213207226  0.9934974068 -0.0588211988  0.5669566953
##  [806] -0.0427502494 -2.3795126750  0.1851248982  0.1387708576 -0.2414486909
##  [811]  0.8109904649  0.4820179953 -0.1716946624  1.3293329644  0.0537366186
##  [816]  0.3827274837 -0.5829474950 -0.4550446305 -0.7354829950 -0.1437453502
##  [821] -0.3890511372  0.1752637715 -1.7376869253  0.4677736241  0.9388363731
##  [826] -0.2294684165 -0.5900921723  0.4162605055 -0.0639348595  0.0689956975
##  [831]  0.4564590117  0.7097858206 -0.7601443822  1.4522238041 -0.1867757456
##  [836] -0.0615484297  1.5081575104 -0.6717586542  0.0724406446  0.2373216844
##  [841] -0.4965168259  0.4669631095  0.6947111694  0.4782614550  0.0963546128
##  [846] -0.3222799931 -0.0839090609 -0.3473529616 -1.2639092421 -0.7971912883
##  [851] -0.7806257085 -0.3896868692  0.0256941360  0.0350347085  0.8234940619
##  [856] -0.4662932206  0.0675065377  0.1573506421 -0.2144012721  0.6357201836
##  [861]  0.0175202090  0.3184214076  0.4878111080  0.1412774899  1.0182223704
##  [866]  0.4594830040 -0.1640711018  0.2997853282 -0.9028546009 -1.0840789595
##  [871]  0.1033529385 -0.1042039993  0.2175498472  0.1189404048  0.7204893914
##  [876] -0.3306367335 -0.1930389391  0.5226616500  0.4816578347  0.6148303533
##  [881]  0.2923330134  1.3478536359 -0.3126910083 -0.1574638406  0.4228808126
##  [886] -0.1554446719 -0.1090947352  0.2656234353  0.5314690339  0.2625043490
##  [891] -0.2457226657 -0.2827172914 -0.4224352396  1.1085540633  0.0293141479
##  [896] -0.8817950453 -0.3583441947  0.0305192012 -0.7558149735  1.1489660202
##  [901] -0.8563148164 -1.0925815371 -0.0199798501 -0.3764641208 -0.0336378438
##  [906] -0.3627872477  0.8574347483  0.0215953441 -0.2320523203  0.2601352364
##  [911]  0.1983788336  0.8066540248 -0.3220125798 -0.0395181664 -0.3464405014
##  [916]  0.2561795137  0.1185671699  0.5440668072 -0.2205887347  0.0702546249
##  [921]  0.2886520533  0.3133614172  0.4409947352  0.5190533990  0.2098930897
##  [926]  0.3590776179 -0.9193726168  0.2529871903 -0.2226514299 -0.3936004522
##  [931] -0.0192203698 -0.3603627442  0.8929123157  0.4725227224 -0.3404780127
##  [936]  0.2715624924  0.3072269212 -0.5802597792  0.5360309362  0.2429950905
##  [941] -0.2485715995  0.0830124883  0.3104267269  0.2759330125  0.7714430873
##  [946] -0.0566846341 -0.2402368597  0.1283459231  0.0684124846 -0.4162195752
##  [951]  0.1173636507 -0.2106793155 -0.1692008847 -0.6609424139 -0.1889317677
##  [956]  0.2644466992  0.0120645328  0.5464038144  0.5165956465 -0.2332625185
##  [961]  0.3871309796 -0.1774622600 -0.2070897383 -0.2833185198 -0.0191164470
##  [966] -0.2513233018  0.3692204565  0.0758435682  0.1973655916  0.6270738825
##  [971]  0.4887685901  0.8351035658  0.4036906133 -0.1483779517 -1.2306951442
##  [976]  0.2997455291 -0.6066526477 -0.2171781004 -0.5436998451  0.5050349763
##  [981]  0.4398129038 -0.0360379619 -0.6437706543 -0.1129534156 -0.0461324157
##  [986] -0.3546857438  0.2898790133 -0.0449383666 -0.0067122853  0.1561887532
##  [991]  0.1938994766  0.4596755908  0.1265663634 -0.0265900949  0.2450902951
##  [996] -0.0136310054 -0.3537666164  0.3867394840  0.1538279303  0.1235501256
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
##   0.5902328   0.3163504 
##  (0.1000388) (0.0707341)
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
## [1] -1.230842656 -0.310829090 -0.007790308 -0.405613206  0.400559602
## [6]  0.803287058
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
## [1] -0.0624
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9197457
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
## t1*      4.5 0.04994995    0.893159
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 5 6 7 8 9 
## 1 1 2 2 1 1 2
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
## [1] 0.0277
```

```r
se.boot
```

```
## [1] 0.9180226
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

