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
## 1 2 3 4 6 7 8 9 
## 1 1 2 1 2 1 1 1
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
## [1] 0.0015
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
## [1] 2.75536
```

```r
UL.boot
```

```
## [1] 6.24764
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.2
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
##    [1] 4.4 4.4 3.1 2.5 6.6 4.4 4.7 4.7 5.0 2.6 3.4 3.5 2.1 3.5 4.2 3.8 4.5 3.1
##   [19] 4.6 4.1 2.6 4.2 3.9 3.8 4.4 3.3 4.4 3.8 5.6 5.1 4.3 5.7 2.7 3.4 3.5 3.8
##   [37] 4.4 4.2 2.5 4.9 5.2 6.4 4.1 2.6 4.2 3.0 5.3 4.6 4.9 4.2 4.6 3.2 3.8 4.2
##   [55] 3.8 3.5 2.8 3.4 4.3 4.8 4.6 3.8 3.2 2.6 3.9 3.6 3.6 3.9 3.3 3.2 4.6 4.2
##   [73] 2.6 5.2 3.9 5.7 5.1 4.8 4.1 5.4 3.3 3.9 5.3 5.4 5.1 4.5 4.6 4.8 6.3 4.0
##   [91] 5.5 6.4 4.2 3.0 3.5 4.2 5.1 5.6 5.9 4.8 5.0 4.5 4.4 5.4 5.7 3.6 4.4 4.0
##  [109] 3.4 5.1 3.7 4.0 5.3 4.4 4.3 5.8 4.0 4.4 4.7 4.8 3.0 3.5 4.8 5.3 3.6 4.9
##  [127] 3.7 4.4 4.1 4.3 3.0 5.1 3.4 3.7 4.8 5.1 5.2 4.8 4.1 4.1 4.9 4.9 4.9 3.8
##  [145] 5.2 6.0 3.2 5.3 5.0 4.4 5.6 5.1 4.7 4.9 6.1 4.0 3.9 5.1 3.5 4.8 4.0 4.8
##  [163] 5.1 3.5 3.3 4.0 4.3 4.8 4.3 5.5 4.3 3.4 3.9 6.1 4.6 3.7 3.6 4.0 6.9 5.2
##  [181] 4.6 4.6 2.6 4.1 5.1 3.9 4.1 4.2 5.2 4.7 6.2 5.2 5.2 5.7 3.8 4.1 4.8 3.7
##  [199] 5.5 4.2 5.0 3.8 4.5 3.9 4.1 4.0 5.5 3.1 4.1 4.0 3.1 5.2 6.1 4.3 5.6 5.0
##  [217] 4.8 4.4 4.3 5.3 4.4 3.5 4.4 3.7 5.4 4.2 5.2 3.0 3.3 6.8 4.3 3.9 4.5 4.2
##  [235] 4.5 4.2 5.8 4.0 4.6 4.5 5.6 4.4 5.1 4.9 3.6 4.6 5.0 4.5 4.1 4.1 6.5 4.1
##  [253] 5.6 5.3 5.0 4.7 3.5 4.9 4.3 5.3 5.9 5.3 5.5 5.6 3.9 3.9 5.4 4.4 4.0 3.5
##  [271] 5.4 5.4 5.3 4.9 4.0 5.0 4.3 4.1 3.0 4.7 4.0 3.3 5.7 5.0 6.2 3.6 5.5 3.9
##  [289] 6.1 4.6 4.7 5.0 5.5 2.7 5.4 3.1 4.4 4.9 4.0 4.9 4.3 7.7 4.7 5.1 5.1 4.3
##  [307] 2.9 4.4 3.8 5.0 4.1 3.9 4.8 6.1 4.2 4.4 4.6 4.9 3.4 5.6 3.7 5.9 4.1 4.9
##  [325] 3.9 4.6 4.6 4.4 4.7 5.8 3.2 5.0 4.3 5.6 5.8 3.7 5.2 4.2 4.8 5.8 5.3 3.8
##  [343] 2.7 3.9 3.9 5.9 3.7 4.2 4.0 3.6 2.9 3.7 4.7 4.9 4.8 4.7 3.0 4.5 4.3 3.9
##  [361] 5.4 5.7 4.8 2.5 6.0 4.0 5.4 3.4 3.8 5.7 5.5 6.0 6.7 4.5 4.3 4.4 5.3 3.4
##  [379] 4.1 3.8 4.1 4.1 5.1 4.2 4.3 4.5 2.9 4.5 4.9 4.2 4.4 4.3 5.6 3.9 2.6 3.1
##  [397] 3.5 4.8 4.0 4.8 3.9 4.2 4.3 5.2 5.7 6.3 5.0 4.6 2.3 5.4 4.8 4.0 5.3 5.3
##  [415] 5.2 4.8 4.3 4.5 5.0 4.7 2.8 5.3 4.0 4.7 4.6 4.7 5.0 4.5 4.7 4.5 4.4 3.2
##  [433] 5.6 4.3 4.6 4.6 4.1 4.0 4.5 4.1 5.2 2.8 5.9 2.3 6.0 5.4 3.3 3.6 4.5 3.5
##  [451] 4.7 4.9 4.2 3.9 4.3 3.8 2.4 4.0 4.6 5.2 6.6 4.9 4.6 5.2 5.8 5.3 3.3 4.1
##  [469] 4.7 4.7 4.5 6.2 5.3 5.9 4.3 4.2 2.2 5.1 5.1 6.3 3.8 5.4 2.9 3.9 3.8 3.7
##  [487] 4.1 3.5 3.2 5.9 5.0 5.2 5.8 4.7 5.5 2.7 4.4 4.5 4.7 5.3 3.6 4.7 4.9 3.6
##  [505] 4.2 4.1 5.2 4.1 5.1 5.4 6.4 2.0 2.9 3.6 5.2 3.2 3.8 4.9 3.0 5.3 4.8 3.9
##  [523] 4.9 4.7 3.3 5.8 6.2 5.1 5.7 3.9 5.2 6.2 3.7 5.4 4.8 4.8 4.9 4.7 3.4 3.3
##  [541] 3.6 4.2 5.5 5.2 3.7 2.7 4.6 4.7 3.1 4.7 6.0 5.4 4.8 4.7 4.7 5.4 3.5 3.8
##  [559] 4.3 4.6 4.6 4.1 3.5 4.7 5.3 5.9 5.1 4.6 5.5 4.6 4.2 4.6 6.3 4.0 5.2 5.7
##  [577] 4.9 4.9 4.9 2.8 3.9 4.8 5.1 4.1 2.6 4.4 4.2 3.4 3.8 4.0 5.0 4.0 4.8 3.7
##  [595] 4.1 5.6 3.4 3.6 4.8 6.4 5.5 4.7 4.1 5.6 4.4 3.0 4.0 5.2 4.6 4.2 4.9 5.1
##  [613] 4.6 3.4 5.2 4.7 4.6 5.1 5.6 4.7 5.8 3.3 4.2 4.7 5.2 6.0 5.4 6.6 4.0 5.7
##  [631] 4.6 3.6 5.2 4.9 4.2 4.4 5.7 4.0 3.3 3.9 5.8 4.9 4.0 6.3 2.4 4.0 5.0 5.2
##  [649] 4.2 5.8 4.7 4.6 3.8 4.3 5.2 5.7 5.6 6.2 4.1 5.7 3.7 5.6 4.4 5.0 3.2 3.0
##  [667] 4.4 5.3 2.7 5.7 3.1 4.1 4.3 2.9 4.7 3.6 4.9 3.4 4.5 4.5 5.6 4.8 4.3 5.4
##  [685] 3.5 3.6 5.3 4.1 3.8 3.5 5.5 4.8 3.4 4.1 3.1 5.2 3.8 5.4 4.2 4.2 5.0 4.9
##  [703] 6.2 5.2 5.5 6.2 4.5 4.7 4.0 5.0 4.3 2.2 5.7 2.6 4.8 3.7 5.3 4.9 4.5 6.6
##  [721] 5.4 5.1 4.1 4.3 4.2 5.9 5.4 3.6 5.4 4.0 5.3 2.8 4.8 2.7 3.0 5.4 5.3 3.3
##  [739] 6.1 4.9 4.3 4.8 3.1 3.7 5.4 4.7 4.6 3.5 5.5 5.4 5.8 4.6 5.2 3.6 4.1 3.9
##  [757] 5.1 4.3 3.5 2.5 4.3 5.0 3.0 3.7 5.8 5.0 5.0 4.1 4.4 3.9 6.3 3.1 5.6 2.8
##  [775] 5.8 4.5 6.7 4.4 6.0 4.1 3.7 5.0 4.8 4.6 5.2 3.3 3.1 4.8 4.5 3.7 5.3 4.2
##  [793] 4.8 3.7 4.6 4.9 5.4 4.9 5.8 3.1 3.8 2.6 4.1 5.2 4.3 4.9 2.5 5.0 3.3 4.8
##  [811] 3.9 3.9 5.4 4.9 5.1 5.3 3.7 5.3 4.2 4.4 5.2 4.2 4.2 2.2 6.3 3.5 4.1 5.5
##  [829] 4.3 5.1 4.5 3.8 2.6 4.8 3.6 4.7 3.4 4.4 2.8 3.9 5.2 5.5 6.7 3.3 5.0 4.0
##  [847] 5.0 3.8 4.4 4.6 4.4 4.0 5.8 4.8 4.3 4.2 5.5 3.9 4.0 5.5 5.9 2.2 3.0 4.2
##  [865] 4.2 4.7 4.7 6.1 2.6 4.6 3.4 5.9 5.0 4.0 3.2 4.8 4.4 4.8 3.1 5.0 3.8 4.8
##  [883] 4.2 5.5 5.6 5.1 4.2 3.0 4.0 4.3 4.2 4.5 5.4 4.7 5.8 5.0 4.3 5.7 5.1 3.7
##  [901] 6.6 3.9 4.7 3.6 6.0 5.7 5.1 3.6 4.3 4.6 3.2 5.6 4.9 3.2 3.5 3.7 4.4 4.3
##  [919] 4.3 4.0 5.3 5.5 5.5 3.3 3.5 4.0 4.8 3.9 4.0 5.9 5.7 4.3 3.9 6.9 3.0 6.2
##  [937] 5.5 3.5 4.4 3.3 4.2 5.1 5.9 5.2 4.8 6.0 5.7 4.5 4.3 6.9 3.4 3.5 3.4 3.8
##  [955] 3.5 2.4 5.3 3.4 4.5 4.9 5.3 3.6 4.5 2.7 3.6 5.2 5.5 5.1 4.1 5.9 5.1 3.2
##  [973] 5.2 3.9 5.2 4.9 3.7 5.1 3.7 4.2 3.9 0.9 4.1 4.9 4.6 4.6 3.0 4.4 4.0 6.2
##  [991] 6.1 4.9 5.4 5.3 3.8 5.0 3.5 4.5 4.1 5.2
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
##    [1] 6.4 5.3 3.8 4.1 4.4 5.9 5.0 5.5 6.3 3.5 2.8 3.9 4.4 4.6 3.6 4.2 4.6 4.7
##   [19] 5.4 3.7 5.1 5.7 6.0 5.7 3.9 5.2 5.2 3.6 3.8 3.1 5.1 5.7 3.0 6.0 3.9 4.8
##   [37] 5.0 4.0 5.0 4.2 4.1 4.4 4.0 4.1 4.5 4.2 5.9 6.8 4.5 4.4 3.6 4.7 5.9 5.2
##   [55] 4.6 3.8 5.3 5.2 4.8 3.0 5.8 4.0 2.8 4.0 3.1 5.0 4.1 4.1 4.8 3.4 3.9 4.0
##   [73] 4.5 4.0 4.4 5.2 5.9 4.2 5.5 6.2 3.9 4.5 3.6 4.5 5.1 3.0 4.7 5.6 4.8 3.2
##   [91] 5.2 5.4 4.8 4.2 4.6 4.3 5.0 4.6 4.4 6.0 4.2 4.1 4.4 4.3 4.1 5.8 4.6 3.7
##  [109] 5.2 5.0 6.3 6.5 3.3 5.3 5.0 5.5 6.2 1.3 5.4 5.9 2.9 6.0 2.7 4.4 4.7 4.1
##  [127] 4.6 5.0 5.1 4.4 5.8 4.4 5.6 5.0 4.8 4.1 3.2 3.8 5.1 5.1 4.3 5.8 4.1 2.7
##  [145] 4.3 4.6 3.8 5.8 5.2 2.9 5.5 4.7 4.8 5.7 4.0 4.7 5.2 4.4 3.9 4.4 4.1 3.5
##  [163] 5.5 6.2 5.8 4.2 4.0 3.6 4.5 3.4 5.3 3.9 4.9 4.9 5.4 4.3 6.3 4.2 5.0 3.3
##  [181] 5.6 5.0 5.9 3.0 4.4 4.0 4.3 3.5 5.8 4.2 4.4 4.3 3.2 5.3 5.2 3.3 4.7 5.0
##  [199] 3.4 3.5 4.2 6.1 5.5 4.3 5.1 5.6 4.3 4.3 2.8 2.5 4.2 5.5 5.6 4.5 3.3 5.4
##  [217] 6.2 4.0 5.0 5.6 4.7 3.9 5.4 5.5 5.8 3.2 6.0 5.8 3.7 5.0 3.4 3.6 4.2 3.7
##  [235] 5.7 4.8 2.8 4.3 4.6 6.5 2.7 3.7 4.5 4.3 5.1 3.3 5.9 5.6 4.1 3.8 3.7 3.3
##  [253] 5.1 5.1 2.6 5.2 4.0 3.6 3.7 2.7 4.3 3.5 5.2 3.0 3.4 5.1 5.3 3.9 4.6 2.8
##  [271] 4.8 2.8 3.9 5.6 3.9 5.5 3.8 3.9 4.3 4.7 4.9 5.2 5.6 3.6 4.6 4.5 3.8 5.5
##  [289] 5.4 3.9 3.2 4.3 4.1 3.8 3.4 3.5 4.5 3.7 3.4 5.8 2.3 4.8 4.3 4.8 4.4 3.3
##  [307] 5.3 3.8 5.1 4.8 5.6 4.3 3.3 5.5 5.5 5.3 6.1 5.1 5.7 3.7 3.3 6.3 5.6 3.4
##  [325] 4.4 3.5 6.8 6.0 5.5 4.8 4.0 5.2 4.7 3.8 4.5 3.9 3.8 5.5 5.2 4.9 4.6 5.3
##  [343] 4.3 5.7 3.7 5.1 4.2 4.1 4.0 3.4 4.7 3.9 4.9 2.9 3.7 3.7 5.1 3.9 4.9 4.3
##  [361] 5.4 3.9 4.4 4.8 3.4 4.2 4.9 3.8 7.2 4.4 4.4 4.1 5.7 4.3 4.3 3.4 5.4 3.1
##  [379] 3.8 3.7 4.6 2.8 5.3 6.2 3.6 5.2 6.4 3.3 6.2 6.4 3.8 4.0 3.9 2.9 3.5 4.8
##  [397] 4.3 5.8 3.0 4.1 2.9 3.9 4.1 4.7 4.7 5.3 4.2 3.9 5.9 3.6 3.7 3.8 3.9 4.7
##  [415] 4.3 3.9 5.1 6.2 3.6 3.9 3.4 3.4 5.4 3.2 5.1 5.0 4.2 3.9 4.4 4.7 3.0 4.1
##  [433] 4.4 3.7 3.5 4.1 4.8 6.3 4.2 5.6 3.7 5.5 4.4 4.9 5.5 5.8 3.5 3.2 5.1 3.3
##  [451] 3.9 4.4 4.7 5.4 6.9 3.8 3.8 5.7 4.6 4.8 4.9 5.7 4.5 3.7 4.0 4.8 3.1 4.8
##  [469] 5.5 4.2 5.2 4.5 3.6 4.1 4.9 5.3 6.0 3.7 5.7 5.2 4.6 5.6 4.0 3.5 3.6 5.8
##  [487] 4.2 4.8 4.2 3.3 3.5 4.3 5.4 3.4 3.2 5.1 4.9 4.6 5.5 3.6 3.3 6.8 4.2 6.2
##  [505] 3.1 4.3 4.6 5.9 4.5 4.5 3.3 5.6 4.2 5.8 3.5 3.2 4.8 4.7 4.7 5.0 4.5 3.6
##  [523] 3.8 5.3 3.0 4.1 2.8 3.6 3.4 2.8 3.9 4.0 4.8 4.0 5.7 3.2 5.3 3.8 6.5 3.6
##  [541] 5.0 4.7 4.7 5.5 5.5 4.1 2.9 4.8 3.5 4.5 3.0 3.8 4.9 4.1 3.5 4.2 5.6 4.3
##  [559] 3.2 3.8 4.9 6.1 2.9 4.9 4.6 3.1 5.7 5.6 3.3 6.4 4.6 5.2 5.2 5.2 4.2 5.3
##  [577] 4.1 4.4 6.2 6.2 4.1 5.0 5.1 3.3 3.2 4.3 4.9 5.8 2.5 4.5 6.1 4.6 3.7 3.2
##  [595] 3.7 2.7 4.5 4.3 5.6 4.6 6.4 4.3 4.5 2.5 4.2 4.7 3.7 3.2 4.4 3.5 6.1 5.2
##  [613] 4.1 3.5 6.1 3.6 3.9 5.9 4.5 4.5 3.7 5.3 5.0 4.9 4.3 5.4 3.8 4.7 4.2 4.8
##  [631] 3.9 4.8 6.1 5.3 4.2 5.3 4.0 3.4 4.6 4.3 4.4 3.5 4.3 5.3 4.9 5.1 3.9 5.5
##  [649] 4.5 4.2 5.4 4.4 5.4 5.2 3.6 4.5 3.9 6.2 6.5 4.2 3.3 4.5 3.8 4.5 3.6 4.4
##  [667] 6.0 2.3 5.7 4.5 4.8 4.1 3.0 3.4 5.7 5.4 3.1 7.1 5.3 4.5 4.2 4.0 4.3 3.2
##  [685] 3.0 4.6 5.6 5.8 2.7 3.2 5.6 6.6 5.1 4.4 4.6 3.8 5.3 4.5 4.1 4.0 5.4 4.5
##  [703] 4.6 4.2 5.5 4.8 4.5 5.2 5.4 3.3 6.4 2.1 5.0 3.9 3.8 4.2 5.1 5.2 4.5 4.2
##  [721] 5.2 2.6 3.8 4.8 6.0 2.6 4.9 5.5 4.3 5.9 5.0 5.6 4.9 5.0 4.7 5.4 3.4 3.8
##  [739] 3.4 3.9 3.1 5.9 4.5 3.8 3.4 3.5 4.2 5.4 3.5 4.6 4.4 4.2 5.3 4.8 3.2 3.0
##  [757] 5.2 3.8 4.5 3.4 5.0 3.0 4.6 5.2 4.8 6.2 2.8 5.5 5.2 6.4 3.1 4.7 3.5 2.8
##  [775] 2.7 4.1 4.7 3.9 4.1 5.5 4.3 6.4 2.8 4.4 6.3 5.2 3.4 4.0 3.5 4.8 4.2 4.2
##  [793] 5.8 5.7 2.9 6.0 3.8 4.5 5.9 2.8 4.5 3.7 5.1 4.7 4.5 4.0 5.1 5.3 4.4 4.1
##  [811] 3.4 6.0 5.2 5.0 2.4 3.2 4.3 5.6 4.0 2.5 4.5 3.7 2.4 4.7 5.6 3.6 4.6 4.4
##  [829] 4.3 5.5 4.8 5.2 4.4 3.8 3.1 4.5 5.9 2.9 4.4 3.8 4.5 5.8 3.0 4.7 5.0 3.4
##  [847] 4.2 6.6 6.0 4.9 5.1 4.7 4.3 6.1 3.3 4.8 3.4 4.8 4.1 3.7 5.5 4.9 3.9 4.6
##  [865] 5.7 4.4 5.6 3.6 4.9 3.5 4.3 3.9 3.4 5.9 5.5 4.4 3.5 4.0 4.9 4.5 4.7 4.5
##  [883] 5.1 6.6 6.0 4.9 5.2 2.9 5.7 5.9 4.7 4.2 4.2 4.7 5.4 4.7 4.9 5.0 3.3 2.9
##  [901] 5.5 3.6 4.3 5.1 3.9 5.4 4.9 5.7 6.1 4.6 4.1 3.9 5.3 2.8 2.6 5.0 4.1 3.9
##  [919] 5.2 3.5 3.5 5.1 4.0 4.2 5.5 2.5 5.0 3.7 4.2 6.9 4.3 4.2 3.5 4.1 5.1 4.1
##  [937] 4.8 4.6 2.9 5.4 5.5 5.0 5.4 4.7 4.1 4.8 4.9 5.5 6.7 3.5 3.6 2.2 5.8 4.7
##  [955] 3.6 3.8 5.0 5.1 5.9 5.8 5.2 3.2 3.2 5.7 4.9 5.0 4.3 3.4 4.4 3.2 3.7 5.7
##  [973] 5.6 3.9 3.4 3.6 3.4 6.6 5.3 3.6 5.6 3.9 4.5 6.5 4.0 5.6 4.7 5.8 3.2 4.3
##  [991] 4.4 3.9 5.9 4.3 4.3 5.2 4.2 4.7 5.5 3.9
## 
## $func.thetastar
## [1] 0.0041
## 
## $jack.boot.val
##  [1]  0.55382436  0.42028986  0.36320225  0.15233918  0.08800000  0.03457143
##  [7] -0.16372240 -0.28207283 -0.41142857 -0.55983827
## 
## $jack.boot.se
## [1] 1.053287
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
##    [1] 5.4 5.3 4.9 3.5 6.1 4.9 7.2 3.2 4.8 4.5 3.7 3.9 5.2 3.1 3.8 4.4 4.4 2.7
##   [19] 3.2 5.3 5.2 4.8 5.3 4.2 6.2 4.9 3.7 6.3 4.7 3.9 4.4 5.7 5.1 3.6 4.5 4.2
##   [37] 4.6 4.9 2.1 4.4 5.2 4.4 4.0 3.9 4.5 4.8 4.1 5.5 3.5 5.0 5.8 3.5 3.8 5.1
##   [55] 4.8 5.5 4.0 4.1 5.6 5.8 3.9 4.9 5.4 5.3 5.5 4.9 5.4 4.5 4.5 4.2 5.3 3.2
##   [73] 3.9 4.1 3.9 4.8 3.7 5.1 5.2 4.5 3.9 5.2 4.4 3.5 4.1 3.9 3.6 5.9 4.5 4.3
##   [91] 5.2 7.0 4.9 3.5 4.2 3.3 4.4 4.3 5.7 6.1 3.6 6.5 6.0 2.9 5.6 6.1 5.1 4.6
##  [109] 3.6 5.2 6.1 4.1 3.4 5.5 5.6 3.6 4.7 4.5 5.8 2.5 5.8 4.2 3.0 3.7 3.6 5.2
##  [127] 2.9 5.3 4.7 4.7 5.0 6.3 5.5 3.6 3.9 4.6 5.1 3.9 3.9 4.5 4.4 4.9 3.8 4.4
##  [145] 6.0 5.4 5.9 4.4 4.1 5.5 3.2 3.7 4.2 2.8 3.2 4.8 3.8 4.5 4.4 4.7 4.2 4.9
##  [163] 4.2 4.0 3.7 4.6 2.7 4.1 3.8 3.8 4.5 5.3 4.5 3.1 3.4 4.4 3.8 5.7 3.8 4.1
##  [181] 5.7 5.2 3.7 4.2 3.7 5.4 3.8 4.1 2.4 3.4 5.3 3.7 4.9 4.1 3.2 5.2 5.0 3.8
##  [199] 3.8 6.0 5.0 5.4 4.8 4.3 4.7 3.5 5.3 4.9 6.9 4.9 5.6 4.3 4.3 5.2 5.3 5.5
##  [217] 5.5 4.5 4.0 4.2 2.8 4.9 5.3 4.5 3.8 3.2 4.8 5.8 3.0 4.1 4.7 3.6 4.1 4.1
##  [235] 3.5 5.9 5.9 4.3 4.4 5.1 4.1 3.7 3.7 4.5 4.3 1.9 4.2 5.6 3.9 5.5 4.7 3.7
##  [253] 5.5 6.0 3.9 5.3 4.8 5.7 3.4 4.5 4.3 4.8 5.7 6.0 5.9 5.4 4.3 4.7 4.3 5.3
##  [271] 3.7 3.3 6.4 4.7 5.7 3.5 2.4 5.0 5.4 5.2 3.6 3.5 3.5 3.2 3.4 5.3 3.2 5.1
##  [289] 3.9 3.9 5.1 3.9 4.8 5.6 3.1 5.3 4.3 6.1 5.6 6.1 3.7 4.4 3.6 7.5 6.7 4.4
##  [307] 4.1 4.6 4.1 3.5 5.3 5.8 3.8 3.8 4.3 4.6 3.4 4.0 4.0 3.6 4.7 4.2 3.2 4.9
##  [325] 3.3 5.2 5.7 5.2 4.4 5.2 4.3 4.6 3.3 5.2 6.0 4.7 5.1 4.8 5.7 5.6 5.6 3.8
##  [343] 4.2 2.6 5.8 4.8 3.6 5.1 3.1 3.2 3.4 5.4 5.4 3.9 3.9 4.0 5.9 5.3 4.8 5.1
##  [361] 5.0 5.2 4.1 5.1 6.2 4.4 4.5 5.4 4.7 4.0 4.5 4.1 4.6 4.9 2.8 4.0 3.7 3.3
##  [379] 5.1 4.6 3.4 4.4 3.7 4.8 4.4 5.1 4.5 5.2 4.7 4.6 4.2 5.9 3.9 5.1 3.8 4.6
##  [397] 6.6 4.1 6.0 4.9 5.3 3.5 4.0 3.6 4.8 3.6 4.8 6.2 6.3 3.9 3.6 3.8 4.6 3.9
##  [415] 5.8 5.4 5.6 3.1 2.5 6.2 4.1 5.0 4.5 4.5 5.1 3.5 4.9 5.1 3.5 5.5 5.1 4.4
##  [433] 3.7 3.2 3.9 5.9 4.1 4.5 5.4 3.2 3.5 3.2 5.5 4.0 4.0 4.2 6.3 3.6 2.4 4.9
##  [451] 4.3 5.0 4.1 4.4 3.3 4.1 2.7 3.3 2.9 2.9 5.8 4.2 4.2 4.4 5.4 3.9 4.3 3.7
##  [469] 4.7 5.2 5.0 4.7 5.2 3.0 3.4 3.5 4.5 3.2 4.7 5.1 4.9 6.6 5.2 4.3 3.9 4.1
##  [487] 3.5 5.0 4.7 5.3 3.9 5.2 3.3 4.6 4.5 4.2 5.4 5.7 5.7 2.3 3.6 4.9 3.4 5.3
##  [505] 6.1 4.1 7.1 6.5 3.7 3.5 4.5 4.6 4.2 3.9 4.0 4.1 4.7 3.7 3.6 4.5 4.9 3.5
##  [523] 4.7 3.9 3.3 4.3 5.2 3.8 3.2 2.7 3.7 4.3 4.6 3.6 4.1 4.9 4.8 5.2 1.8 5.8
##  [541] 3.9 3.2 4.9 3.0 4.2 3.4 2.1 3.4 3.6 5.7 3.9 5.1 5.0 4.5 4.1 4.0 4.3 5.4
##  [559] 4.8 4.3 4.0 3.0 5.5 5.4 4.7 2.2 4.7 6.6 3.6 5.5 3.9 5.0 4.2 4.1 6.3 4.7
##  [577] 3.2 4.1 4.4 3.9 2.8 2.6 5.1 4.7 4.4 4.5 4.5 2.7 3.5 5.3 2.9 5.2 4.4 3.7
##  [595] 4.2 3.7 4.0 5.1 2.8 4.5 4.3 3.9 5.7 3.9 5.3 4.7 4.3 5.0 4.1 4.4 4.8 5.6
##  [613] 4.6 4.2 4.1 4.9 3.1 5.1 5.1 2.4 3.3 3.7 4.8 3.4 4.1 3.3 4.3 3.9 4.9 3.7
##  [631] 4.9 4.9 3.8 4.6 4.4 5.7 3.9 3.7 4.9 5.5 4.9 4.3 2.9 4.3 4.3 6.6 2.9 4.1
##  [649] 5.0 4.8 3.6 4.9 4.6 4.9 3.7 3.7 5.9 3.8 4.0 5.6 7.0 3.7 3.7 5.2 3.9 4.6
##  [667] 5.3 3.7 6.6 5.2 3.1 4.2 4.2 3.7 3.4 4.8 5.5 3.5 4.3 5.8 4.2 3.8 4.8 4.8
##  [685] 5.0 3.4 3.6 2.9 5.8 3.8 4.8 3.0 4.6 4.9 5.8 4.2 3.4 5.3 4.8 5.0 4.8 3.7
##  [703] 5.8 2.9 4.8 3.3 4.7 4.3 4.3 4.7 3.5 5.6 4.5 5.1 4.0 5.3 2.7 4.3 4.8 2.8
##  [721] 3.3 5.6 3.5 3.5 3.8 3.7 2.7 4.7 3.4 4.4 3.5 4.9 4.8 4.2 3.9 3.9 4.4 3.4
##  [739] 4.2 4.9 5.8 3.1 5.3 3.5 3.8 5.6 4.5 3.0 5.4 3.9 4.9 3.6 4.4 5.9 4.0 5.2
##  [757] 4.4 3.1 4.6 4.1 3.3 4.8 4.6 6.0 4.4 4.7 4.1 5.6 4.5 3.6 4.7 4.6 5.3 4.2
##  [775] 4.3 4.5 3.1 5.3 4.2 5.0 2.5 2.5 4.6 5.9 3.4 5.4 5.2 3.9 2.9 3.3 2.7 3.8
##  [793] 4.2 5.1 3.4 4.9 5.0 4.5 4.9 4.2 3.4 5.1 3.7 3.7 4.8 4.5 4.7 5.9 4.9 4.6
##  [811] 4.3 4.5 5.6 4.2 2.6 4.7 4.8 4.0 3.9 4.1 4.0 5.5 4.4 4.5 3.2 4.4 5.3 4.8
##  [829] 4.6 3.4 6.4 4.9 4.6 5.5 3.2 5.4 4.2 4.1 6.3 5.7 5.5 6.6 4.8 3.7 5.6 4.5
##  [847] 5.1 4.6 6.5 4.2 4.6 4.4 6.0 5.2 4.7 3.0 4.2 7.3 2.6 3.3 3.9 5.0 4.3 4.6
##  [865] 3.6 4.8 4.1 4.9 5.0 2.7 4.2 4.4 5.3 3.3 4.2 3.2 6.6 3.4 3.8 4.3 3.4 3.7
##  [883] 3.6 3.5 4.4 3.5 4.6 2.8 4.6 3.6 4.1 2.0 3.2 4.0 4.3 4.6 5.1 4.4 4.9 3.5
##  [901] 2.9 4.7 2.8 3.0 6.0 4.5 6.1 3.9 5.6 4.6 3.5 3.8 1.8 4.6 4.1 3.3 4.3 4.7
##  [919] 3.2 3.6 3.3 2.8 3.3 5.2 5.1 4.5 3.7 3.9 4.8 2.9 2.9 3.8 3.3 4.4 3.5 3.0
##  [937] 4.7 4.8 4.2 5.3 2.5 4.2 6.8 4.9 5.5 3.9 4.0 4.7 5.1 2.6 4.7 6.7 4.7 3.7
##  [955] 3.4 4.8 4.8 4.7 3.7 5.9 3.5 5.8 4.0 3.9 5.5 4.7 5.2 3.9 4.5 4.9 4.3 6.0
##  [973] 3.5 5.2 4.3 4.5 3.3 3.2 3.4 4.3 5.0 4.7 4.4 6.0 3.2 4.1 3.2 5.1 4.4 4.2
##  [991] 3.5 5.8 3.7 5.8 4.6 4.8 5.1 3.9 3.7 4.2
## 
## $func.thetastar
## 72% 
## 4.9 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.300 5.200 4.900 4.900 4.888 4.700 4.500 4.300
## 
## $jack.boot.se
## [1] 1.116697
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
## [1] 0.2415266
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
##   4.344192   7.124690 
##  (1.872960) (3.256396)
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
## [1]  0.07821719 -0.12391976  0.49581177 -0.22425539  0.40580946  1.87819084
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
##    [1]  0.5704661157  0.4790086109  1.5180422826  1.2409334973  0.3747954261
##    [6]  0.0255913101  0.7252914586 -0.0504659111  0.5439072343 -0.1677869046
##   [11] -0.6903698767 -0.0424066250  0.5869081592 -0.6042043342  0.5877883874
##   [16]  0.0573305241  0.5053722687  1.1762780732  0.2497119854  0.6781074612
##   [21]  0.9695873091 -0.4097651200  0.7234194061 -0.3991311805 -0.3403403503
##   [26] -0.0664028466  0.6843029549 -0.2490746727 -0.4531623701  0.1767399653
##   [31]  0.7025936047  0.3202194478  0.2941838881 -0.4440620718  1.1892890212
##   [36]  0.7031737887  0.8885164616  0.2241751639 -0.1624685711  0.1764047241
##   [41] -0.5470642977  0.1859502745 -0.4870153596 -0.1032722582  0.1987719860
##   [46]  0.3284675254  0.0038182616  0.2783526101  1.0142708816  0.9073043464
##   [51] -0.6411404390 -0.0174575040 -0.2758733048 -0.0730704903  0.7702745894
##   [56]  0.2327151990  0.3166328477  1.1821425121  0.5036836640 -0.0135844114
##   [61] -0.0018066242  0.7513945057  0.1780636797  0.6690108221 -0.6442431284
##   [66] -1.2655039647  0.5858894819  1.1236673833 -0.2352610082 -0.0523363056
##   [71]  0.6615422889  0.1557264409  0.5569402263  1.8248657342 -0.4134746053
##   [76]  0.3091290079 -0.2755809772 -0.1435050312  0.2843346023  0.7352497571
##   [81] -0.1160580681 -0.0718511229  0.6037499491  0.9506604441  1.3652155015
##   [86]  0.9705169479 -0.5228106996  0.3195229550  0.3888966678 -0.3710497674
##   [91]  0.1270719984  0.5902511144  0.4640640948 -0.0237212826 -0.1663459609
##   [96] -0.0212343342  0.2503758539 -0.1941112959 -0.5034418477 -0.1003906058
##  [101]  0.4937295774  0.7421054391 -0.3633523796 -0.1200644241  0.6943129756
##  [106] -0.1645650702 -0.1900000664 -1.1766372588  0.3236098025  0.0210544131
##  [111]  0.5763594807 -0.3787529050  0.5295399201 -0.2057816318 -0.3507921227
##  [116]  0.9939354886  1.6107464290  0.0850097689  1.1369614107  0.6281378198
##  [121]  0.7143843863 -0.0178558802 -0.2690418667 -1.1615521915  0.3127071286
##  [126]  0.0659282695  0.4819941985  0.6196905600  0.7661400565  0.7526179275
##  [131] -0.1883532171  0.3804740890  0.5829767586  0.2187572109  0.2644957588
##  [136] -0.4182111434  0.3303483913  1.2632313802  0.0958468437 -0.0571598812
##  [141] -0.2863010550  0.2505660240  0.1884609325 -0.0735866049 -0.4733253113
##  [146] -0.6138917259 -0.2929103763  0.3529722515 -0.0129996974  0.5445177961
##  [151]  0.1530903237  0.0713163008 -0.7733691663 -0.3882386362 -1.1739918012
##  [156] -0.3262814549  0.3507259703 -0.6606373588  0.0491370275  1.4424404166
##  [161]  0.0692890676 -0.1032722582 -0.0030492111  0.5139359335  1.0157087503
##  [166]  0.2458987298 -0.9906617409  1.0972930233 -0.3725113783  0.1162499436
##  [171]  0.1318698864 -0.0676952884  0.1100397736  0.1664494264 -0.8725707104
##  [176] -0.4882900629  0.7044518524  0.4621993467 -0.8584624773  0.2150767296
##  [181]  0.5249592458  0.5047704866 -0.3971818756  0.3698936957  0.4753581084
##  [186]  0.0432176116  0.5700603985 -0.0356447733  0.6577533142  0.2946894891
##  [191]  0.5325012389  0.4741187845 -0.0803879201 -0.3276368440  0.0808835092
##  [196]  0.2387590131  0.4487481086  0.7836125586  0.5188322623 -0.8495204787
##  [201]  0.5631351888  0.5276473232  0.5584898900  0.3566034123  0.4291539447
##  [206]  0.5562221848  0.1273457653  0.2297985280  0.5992421181 -0.1099996707
##  [211]  1.0766692929  0.2712400079  0.7301587703  0.7792961639 -1.1243954326
##  [216]  0.8344136727 -0.4491769581  0.7815791222  0.8330687120 -0.5411229846
##  [221]  0.5718088951  0.2803967428 -0.1460258470  0.0098469541  0.1947591949
##  [226] -0.6838363792  0.9753551736  0.3803600423  0.9005666114  0.2232229680
##  [231]  1.0688254066 -0.0661565995  0.9944714224 -0.2963630397  0.5741831214
##  [236] -0.3907529809 -0.6112647757  0.9888111734  1.1682662151 -0.4970426646
##  [241]  0.0663062762 -0.4811929914  0.0656110580 -0.5088943424  0.9575349217
##  [246] -0.8893150577  0.8600286751  0.3517639348  0.4482834140  1.0073918906
##  [251] -0.0236880532  0.2611047195 -0.1144058525  1.4185013773  0.0779703111
##  [256] -0.5471098255 -0.8062480268  0.7474287163  0.6459121779 -0.8357316559
##  [261]  0.0194800301  1.2120530012  0.1062185257 -0.0600511246  0.6541462481
##  [266]  0.2931470278  0.7324379648 -0.6199375102 -0.1593128735 -0.1855433792
##  [271]  0.1626078492  0.4153713570  0.3380508200 -0.3015536475  0.3400359958
##  [276] -0.0042666905  0.0521544879 -0.0720695543 -0.2977995217  0.2870418296
##  [281]  0.1298438754  0.3826171513  1.0299803913  0.0423020407  0.3759766719
##  [286] -0.4906925920 -0.4545151257 -0.2340175034  0.4458870950 -0.7105972445
##  [291] -0.1989159155  0.3787434528  0.8186406225  0.4627076309  0.6120733993
##  [296] -1.2571276832  0.1437641064  0.2245188579  0.8587895904 -0.8931784846
##  [301] -0.0292750219 -0.7817094071  0.1377878858 -0.6485462507  0.2083317849
##  [306] -0.1275285077  1.0766685771  0.1658104366 -1.2475115221 -0.3500186248
##  [311]  0.4708268374  0.7071173556 -0.5521448614  1.0956881246  0.2062635078
##  [316] -0.0649811121  0.9525998090 -0.1803732811  0.8388617177  0.0010587970
##  [321] -0.1320683347  2.4941801637  0.1357247244  0.6826779194 -0.4051078541
##  [326]  0.6465158234  0.1122338295  0.0978868187  0.4308533393  0.7746545479
##  [331]  0.4015698083  0.7787640335  0.3007670178  0.7276771621  0.1811812584
##  [336]  0.5343058550 -0.3353515221  0.4175918512  0.3070875922  1.6259643606
##  [341] -0.3760099178  0.1824020366  0.3297774012  0.4598330470  0.6808153038
##  [346]  0.4070681119  0.7822517020 -0.1662700627 -0.3355864753 -0.3650989400
##  [351]  1.0637919003 -0.1620797125  0.6658661601  0.2065495181  0.0150478952
##  [356]  0.7522875770  0.4368930309  0.7963025634  0.0255479982  1.1333397530
##  [361]  0.5499756772  0.0101081707  1.2284976936  1.0582659916  0.8274500931
##  [366] -0.6249857917 -0.1603566871  0.2432205311  0.4355849606 -0.1657131249
##  [371]  0.2968165232  0.6436757980  0.4829725608  0.1212801459  1.0644770545
##  [376] -0.5699573623  0.4380009371 -0.6773466844 -0.5050639056  0.6572763234
##  [381] -0.0731404016  0.6266377235  1.0702311268 -0.0401385238  1.0732224733
##  [386]  0.3922623946  1.2509436813  0.1197212469 -0.0909532973  0.1878765296
##  [391]  0.7775342498  0.1188695164  0.2462626862  1.0374712247  0.2577866679
##  [396] -0.8365933751 -0.4372318711 -0.1500837533 -0.9048442456 -0.7817094071
##  [401] -0.1703019764  0.2676539378  0.7483662778  0.1341160971 -1.1448593810
##  [406]  0.0258564164  0.7137144906 -0.3425023163  0.3073283346  0.7077715771
##  [411]  1.2862291618  0.6850522852 -0.2498940754 -1.4201338417  0.1216791353
##  [416] -0.2040971362 -0.5083706704  1.1496780527  1.1311731070  0.7288851818
##  [421] -0.5663106598  0.0695805249 -0.1968074222  0.0320668597 -0.8672368910
##  [426]  0.7730459448  0.1774136295  0.8110706987  0.3093597614 -0.2621615045
##  [431]  0.1911147981  0.9256939166 -0.5766061443 -0.5173910084 -0.2738682355
##  [436]  0.8040026309  0.0364232516  0.4657225769  0.5358947230  0.0767667866
##  [441]  0.5623393049  0.9581567577 -0.1296608289 -0.1756569952 -0.2240067611
##  [446]  0.1452173411 -0.0009206162  1.0807274265 -0.3635549157 -1.0294535768
##  [451]  0.1207403244 -0.1091662241 -0.1116014461 -0.1694531817  0.3810384718
##  [456]  0.0663062762 -0.4440620718 -0.0841343386  0.6875559786  0.0103801943
##  [461]  0.3338673507  0.0265698143 -0.7047683803  0.0930568211 -0.0335159652
##  [466]  0.6984120049  0.8954729408 -0.2056896172  1.0170884213  0.4284258881
##  [471]  0.1951551657 -0.2211440884 -0.2618824234 -0.0799875344  0.8153664549
##  [476] -0.0380551310  0.0747727895  0.1870716627 -0.3940076655  0.7912118204
##  [481] -0.4953413568 -0.3088602339 -0.4350686612  0.0771763881  0.8876541432
##  [486]  0.4811862456  1.1820972814  0.6155047492  0.3507259703 -0.0470761117
##  [491] -0.0409175200  1.1502252117  0.0914485073 -0.2850719572  0.6357732858
##  [496]  0.7019405603  0.4383629114 -0.6872972197 -0.0859202738  0.3536315837
##  [501]  1.8472895898  1.0632488344 -0.5486191836  0.2737454302  1.1669239880
##  [506] -0.1639840463  0.4228715923  0.0726244191  0.0147813605 -0.2760004690
##  [511]  0.5225730550 -0.1121060879  0.0784512240  0.3258325908 -0.1219973713
##  [516] -0.0406263380  2.3014198646 -0.8611188205  0.4804412561 -0.1250076498
##  [521] -0.1599464174  0.0569748784 -0.0491986786  0.8164144933 -0.0841962812
##  [526]  0.9851253554 -0.9597867669  0.5472851109  0.4021977000  0.0096032595
##  [531]  0.1164037743 -0.2814550385  1.6071992086 -0.4384836293  0.2232700515
##  [536]  1.2010884421  0.9145433477  0.2501048295  0.2369613151 -0.7386299763
##  [541]  0.3386273638 -0.1183195728  0.8782766385 -0.0326236145  0.8061994550
##  [546]  0.1095184064  0.0616651055 -0.9572997502  1.0868628338  0.7120042249
##  [551] -0.0041521124  0.1699245571  0.0245290431  0.5450077233  0.2189402906
##  [556]  0.0115444800 -0.0494710769  0.5937168455 -0.2580331035  0.6273094722
##  [561] -0.0607902918 -0.0055902855  0.5573250671  0.7971370847  0.4427901856
##  [566]  0.5305537405  0.5264262360  1.4515098032  0.0962518759  2.0295026404
##  [571] -0.5696855900  0.3747954261  0.5195717519  0.2449404997  0.1349345438
##  [576] -0.7552800260  0.3553064708  0.5830140663 -0.4267607645 -0.3385216522
##  [581]  0.5273365483 -0.6670463090 -0.0254692539 -0.2540948643 -0.3023488183
##  [586]  0.5180138818 -0.6528615443  0.0408441756 -0.4923305265  0.5011337292
##  [591] -0.1602375360  0.2689201348  0.0393561848  1.2659901034  0.8061994550
##  [596] -0.0129996974 -0.5251123953  0.3247438656  1.2678585155  0.0814211563
##  [601] -0.3335572725  0.2727701521 -0.1736870582  0.1592848473  0.0780540902
##  [606]  1.1947289565  0.4261015934  0.4017901463  0.7000943611  0.4441509990
##  [611] -1.1801937900 -0.2010735938 -0.3772625939  0.3551452297  0.2562798650
##  [616]  1.1377425554 -0.2540948643  0.0362408241 -0.0243420458 -0.1891625424
##  [621] -0.2238479612 -0.1146760624  0.7136720308  0.4472171968 -0.1612231807
##  [626]  0.1826685441  0.2419693988  0.7792042859  0.8500872934  0.7176679677
##  [631] -0.5569637090  0.2996037462  0.4363251288  0.2271250630 -0.9834491071
##  [636]  0.9012016526  0.1549587298  0.0489627432  0.0716901098 -0.5864665503
##  [641]  0.4541920637  0.0526753744 -0.1968074222  0.1653329081 -0.3231425982
##  [646]  0.8662209081 -0.1417894463  0.3302861248  0.4246017241 -0.1416233602
##  [651]  0.3983469984  0.1571419304 -0.4170425429  0.1713535827  0.1512575995
##  [656]  1.9816451974 -0.2256161026  0.1105250028 -0.1320683347 -0.3943551057
##  [661]  0.1060327807  0.0565105633  0.5014993908  0.1632155491  0.1378437909
##  [666] -0.2460542488  0.4341763963  0.7453585818 -0.1970931812  0.8151581139
##  [671] -0.3119561763  0.5177363602 -0.0881744672 -0.1274276171  1.4555178875
##  [676]  0.3039897163  0.5080430666  0.1473300148  0.3608068407 -0.3458670556
##  [681] -0.0854059821  1.0441210028  0.6913767781  0.5215675240 -0.3428237618
##  [686]  0.4590332229  0.6163269213  0.7899205534  1.2082992454 -0.5989972357
##  [691] -0.3511991902  1.0182025704  0.2008249716 -0.4686007251 -0.0167858437
##  [696]  1.4314654107  0.0238980144 -0.0467391322  0.7149406652  0.0126375485
##  [701]  0.0536674463  0.9963268557 -0.3699010537  0.1355551469 -0.1071065739
##  [706]  0.2948180505  0.5217859491 -0.2983631619 -0.0628599558  1.3849512933
##  [711] -0.0070469863  0.1348529622 -0.0740968003 -0.1286373093 -0.3823921204
##  [716] -0.0779408871  0.1675586419  0.5553748510  0.1440480163  0.3196179767
##  [721] -0.1111796447  0.6157683934 -0.1861130064 -0.2953737061  0.1802039991
##  [726]  0.5680076557 -0.9875040771 -0.2951730395  0.4762458451  0.6771889500
##  [731] -0.4087680232 -0.7730123124  1.2712325615  0.4374784423  0.2373173673
##  [736] -0.3371689470  0.0622919736  0.6338208360  1.0269842938  1.2581345711
##  [741]  1.3816229138  0.6815979427  0.5461587756 -0.0877631248  0.2041780674
##  [746]  0.1573540293  0.4432277727 -0.4518888769  0.6384523701 -0.3176781604
##  [751] -0.3856600082  0.0658685628 -0.3749860703  0.1426918551 -0.8165531884
##  [756] -1.4603016911  0.7132158361  0.0455688830 -0.1379430802 -0.5510776864
##  [761]  0.0116072222  0.7305705703 -0.0153560724  0.7574374049 -0.6471099138
##  [766]  0.5060999015 -0.0536260348  0.3620516246  0.4834117973  0.0642483787
##  [771]  0.6794273198  0.3760113066  0.0685174628  0.0316502890  0.8025713166
##  [776] -0.0490254645  0.4044316286  0.3802792894  0.9349219471  0.2609714824
##  [781]  0.3927598393 -0.0372088471  0.3418846268 -0.6394265668  0.1743239284
##  [786]  1.2621280290 -0.2924214813 -0.0490892211  0.0417862906 -0.6025524845
##  [791] -0.1623129799  0.0765336838  0.0792765502 -0.2962918557 -0.2477713936
##  [796] -0.1883840218  0.8814201960  0.6188130375 -0.4462014572 -0.0351026935
##  [801]  0.3537705555 -0.0886349255 -0.5392116468  0.2707527043  0.4432493680
##  [806]  0.0774116731 -0.3024108376 -0.0790326639  0.1737360514  0.2701431211
##  [811]  0.2415878974 -0.1486718964  0.0575611467  0.4833749769  0.2018653350
##  [816] -0.0815780392 -0.1932621041  0.8705879398  0.5770576045  0.6209292701
##  [821]  0.3059056529 -0.7016282145 -0.6289804511 -0.3229177206 -0.1466326853
##  [826]  1.2727310909  0.4265296345  0.5577098206 -0.1417894463  0.7555376149
##  [831] -0.0111769400  0.3254555380 -0.6043044561 -0.0033680411  0.6875574470
##  [836]  0.7400214644  0.6400061415 -0.1041467284 -0.1075926549  0.2308440477
##  [841] -0.1055165715 -0.2850719572  0.3858802472  0.0199576256  0.7578043589
##  [846]  0.0458125026  0.6049495317  1.9837652102 -0.3110133546  0.3521763161
##  [851] -0.0350446230  0.2062045097 -0.6033599780 -0.0366438842  0.2732933733
##  [856]  0.3652957363  1.0182025704 -0.5136195160 -0.4582981044 -0.5653065409
##  [861]  0.5873442420  0.1959741179  0.2612402961  0.2451211156  0.1597325444
##  [866]  0.3123009936  0.6949063942  0.2316043398 -0.0325948138  0.0379198114
##  [871]  0.5141446118  0.5491475165  0.3968915689  0.5387871261 -0.0033708095
##  [876]  0.5973639716 -0.8481284920 -0.0538956092  0.5330968047 -0.0178558802
##  [881] -0.0013244444 -0.6685568483 -0.8806966576  0.3314769413 -0.4962951508
##  [886] -0.5480026767  0.4667204861  0.2179669649  0.1349537865  0.5971785092
##  [891]  0.6922721122 -1.0272807566  0.0787262706  0.0943255598 -0.4624000542
##  [896]  0.3542140655  1.0619980243  1.8023677861  0.6294430086  0.1375592613
##  [901]  0.6538724632 -0.1201627675 -0.5629522891 -0.4310926253  0.2190585142
##  [906] -0.3951401379 -0.1335155319  0.1821312232  0.1570648394  0.0319688199
##  [911] -0.8017804627  1.1496495948  0.1363263227  0.5832442202 -0.1464900650
##  [916]  1.7230186182  0.5678630681 -0.7331659806  0.3444039653  0.6101657217
##  [921] -0.2032635391 -0.0498438901  0.3538603670 -0.0458015699  0.3007272982
##  [926] -0.8029326298  0.6442411198  0.4797430162  0.1592803645  0.4867407789
##  [931]  0.0183801991 -0.6016590751 -0.2179996725 -0.2333013608  0.3987228784
##  [936] -0.5841874786  0.0557656447 -0.3249476818  0.7342284996  0.2591189844
##  [941]  0.5518112239 -0.2019666614  0.9216842521  0.0216114426 -0.1961968907
##  [946] -0.0379384503  1.2424916126  0.0436295325 -0.2315673832 -0.9122441760
##  [951] -0.0254063459  0.2846580755  0.1918115965  0.5317201038  0.1452814095
##  [956] -0.2667035507 -1.2677365107 -0.5152491278  0.3485104023  0.1422676017
##  [961]  0.2645711447 -0.2153834909 -0.3128690337  0.4656055549 -0.5819709202
##  [966]  0.6779272081 -0.1950396658 -0.0047954843  0.3584975188  0.4951426007
##  [971]  1.2326357224 -0.0662414376 -0.0466586665 -0.3636465567  0.5806545869
##  [976]  0.4611946289  0.9022894606  0.2731281359  0.0021536059 -0.5254762442
##  [981] -0.9906617409  0.3725957398  0.2415265614 -1.5574812081 -0.4933491935
##  [986]  2.3526682362 -0.2926698096 -0.3745294385  0.1566942742  0.2033656154
##  [991] -0.7492551984 -0.3725113783 -0.7707558404  0.1436372945  0.5363480276
##  [996] -0.5122656078 -0.7057709463 -0.0640751051  0.0770599664  0.5039700547
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
```

```r
fit2
```

```
##       mean          sd    
##   0.60973681   0.26571184 
##  (0.08402546) (0.05941224)
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
## [1] -0.3321936 -0.3293802  0.1063685  0.3693328 -0.9710797 -0.6494067
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
## [1] 0.0064
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9194588
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
## t1*      4.5 -0.007107107   0.8805853
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 3 4 5 7 8 
## 1 1 3 1 2 2
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
## [1] -0.0016
```

```r
se.boot
```

```
## [1] 0.9093758
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

