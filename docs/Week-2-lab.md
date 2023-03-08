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
## 1 2 3 9 
## 1 4 3 2
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
## [1] 0.0053
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
## [1] 2.801545
```

```r
UL.boot
```

```
## [1] 6.209055
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
##    [1] 3.1 4.3 5.3 4.6 4.9 3.1 4.7 3.5 3.8 5.2 4.4 3.2 5.2 6.1 4.6 4.6 5.6 5.4
##   [19] 4.0 5.2 3.0 5.8 5.2 5.6 4.2 3.4 4.1 4.2 6.1 5.7 4.6 6.2 3.8 2.8 5.2 5.3
##   [37] 4.3 5.0 3.9 3.8 4.8 4.2 5.1 5.6 7.0 2.0 4.2 4.2 4.2 4.7 4.3 4.2 5.7 4.2
##   [55] 3.6 6.4 3.9 6.7 4.6 3.8 4.2 4.6 2.9 4.9 4.6 4.8 3.3 4.6 5.3 5.5 4.8 4.8
##   [73] 4.7 3.9 5.0 4.7 4.9 5.4 3.5 3.6 5.0 4.1 4.8 4.9 3.6 3.7 5.1 5.4 3.3 3.9
##   [91] 4.1 5.9 3.5 3.4 4.8 2.7 5.0 5.3 4.9 4.1 3.3 3.2 4.3 6.1 4.1 4.7 6.9 4.7
##  [109] 3.2 4.7 4.3 2.9 3.8 6.4 4.2 5.8 4.6 4.1 5.4 3.9 5.0 5.1 3.8 5.1 5.0 5.8
##  [127] 5.4 5.0 3.7 5.5 4.1 3.5 6.4 5.5 4.6 4.4 5.1 5.6 4.3 4.3 4.1 4.1 4.2 5.3
##  [145] 4.4 4.8 4.3 4.4 6.2 3.6 5.0 4.2 4.5 5.3 4.2 4.2 3.5 4.1 5.7 4.0 6.0 4.5
##  [163] 3.6 2.5 4.7 4.0 4.8 4.3 6.6 4.1 3.6 4.7 4.6 4.3 4.9 3.6 3.4 4.7 4.9 4.5
##  [181] 2.0 5.6 4.0 4.5 5.2 4.7 4.4 3.9 4.3 3.6 5.1 4.8 6.7 3.9 2.4 4.1 4.7 4.9
##  [199] 5.7 3.1 4.7 5.0 4.8 3.2 4.3 4.0 5.4 4.2 2.7 5.0 4.5 4.5 4.6 4.3 4.7 5.0
##  [217] 4.2 3.1 3.9 3.5 5.0 2.6 4.4 4.4 5.0 4.4 5.1 4.6 4.5 4.1 5.5 3.2 5.1 4.8
##  [235] 4.7 4.8 3.8 4.7 2.5 5.0 3.9 3.3 5.9 3.7 5.1 3.8 4.8 4.7 4.3 4.1 5.0 5.2
##  [253] 5.6 2.9 5.3 4.1 4.5 6.3 5.9 5.1 3.6 5.1 4.5 5.6 5.1 5.0 5.7 6.4 4.6 3.6
##  [271] 4.3 5.6 4.9 5.2 6.9 3.4 6.2 5.9 5.4 4.3 4.4 5.0 3.2 4.4 3.7 5.7 4.9 4.5
##  [289] 2.9 5.7 4.3 5.1 4.2 5.2 4.7 5.8 4.1 2.8 3.4 3.2 4.6 4.5 4.6 5.2 5.8 5.1
##  [307] 4.2 4.1 4.9 4.5 4.5 4.2 3.8 5.8 4.6 6.8 5.2 5.8 3.7 4.2 5.1 5.9 4.5 5.7
##  [325] 2.9 4.6 3.7 4.1 4.4 4.4 4.5 6.1 4.5 4.2 3.9 5.2 3.1 4.8 6.3 5.2 3.5 5.3
##  [343] 4.7 3.7 6.0 3.8 2.6 5.3 3.7 5.7 4.4 3.5 6.0 5.3 4.6 7.3 2.8 4.1 4.3 5.2
##  [361] 5.3 4.0 4.9 4.6 5.3 3.4 4.5 5.0 2.9 3.7 4.6 5.1 5.1 4.9 4.4 6.1 5.6 4.8
##  [379] 4.0 4.4 4.4 3.2 3.7 4.1 4.8 5.2 3.2 7.4 4.7 4.5 4.0 4.6 5.1 4.3 4.0 5.5
##  [397] 4.0 4.1 5.2 3.9 3.4 5.9 5.4 4.4 4.6 4.1 4.6 3.3 6.9 6.3 2.9 3.1 4.6 6.0
##  [415] 3.6 5.6 5.2 5.0 6.0 2.3 3.1 4.1 5.1 4.0 4.5 6.4 3.8 3.8 3.7 6.0 3.9 5.2
##  [433] 2.9 4.9 3.6 5.1 4.0 4.7 2.6 4.2 5.2 4.7 5.4 3.9 4.4 4.9 4.0 4.9 3.2 6.4
##  [451] 3.9 5.3 6.0 4.5 3.7 5.4 4.1 4.9 4.7 5.4 5.0 3.9 3.3 5.9 5.0 5.2 4.7 5.1
##  [469] 5.4 4.3 3.9 3.6 5.0 4.3 5.6 6.0 4.0 5.7 3.9 4.4 4.9 3.7 3.7 4.4 3.4 4.4
##  [487] 4.3 3.7 5.8 4.8 2.3 4.7 3.9 3.6 3.8 5.1 3.9 4.3 2.5 4.5 4.9 3.6 4.4 3.0
##  [505] 5.1 5.4 4.6 4.4 3.9 5.0 4.4 5.5 3.3 3.9 4.6 2.5 4.3 3.8 4.2 5.6 4.6 5.3
##  [523] 3.2 4.2 3.2 4.8 2.8 6.2 6.2 5.2 4.8 5.1 5.2 4.9 4.7 4.5 5.7 4.2 5.6 4.3
##  [541] 4.8 4.1 5.0 5.7 5.3 3.3 4.0 4.3 5.1 3.6 4.3 3.6 5.5 3.5 4.2 3.5 4.1 3.6
##  [559] 3.6 4.2 3.8 4.3 6.0 5.9 4.5 4.9 5.3 3.8 3.7 3.9 5.2 5.2 4.8 2.4 4.6 3.1
##  [577] 4.6 4.7 5.4 5.4 2.9 3.5 3.3 4.5 4.9 5.7 5.9 2.9 4.6 3.6 5.2 4.3 6.3 2.6
##  [595] 4.1 3.9 4.5 3.6 5.0 6.6 3.8 4.4 5.5 3.0 4.0 3.2 5.1 5.4 4.2 2.8 3.8 4.7
##  [613] 5.8 4.1 5.0 2.9 4.3 5.2 6.2 4.8 4.2 3.9 3.4 3.6 4.0 3.6 4.7 4.9 5.1 3.9
##  [631] 5.5 4.2 4.3 4.2 4.0 4.6 4.1 5.2 4.5 5.3 4.0 5.0 4.0 4.3 4.9 4.9 4.0 5.3
##  [649] 6.1 4.2 4.6 3.6 4.1 5.9 5.2 3.7 4.7 3.9 4.6 5.3 3.8 3.9 4.3 4.3 4.4 4.0
##  [667] 5.7 4.8 4.1 3.9 5.5 3.1 4.5 5.1 3.1 6.1 5.0 3.6 5.5 3.2 5.2 5.1 4.8 3.6
##  [685] 2.9 6.4 4.8 4.7 3.4 5.2 4.8 4.1 4.9 4.8 4.2 4.3 3.5 4.5 4.0 3.8 3.5 6.3
##  [703] 4.0 4.6 4.2 5.5 5.0 3.0 4.9 4.5 4.0 7.1 5.2 3.5 5.4 4.9 4.2 5.3 5.3 5.8
##  [721] 5.1 6.3 3.9 3.6 3.2 7.5 4.0 5.4 5.9 5.7 4.2 4.2 4.2 3.9 4.1 3.7 3.2 5.1
##  [739] 4.5 5.0 4.5 3.0 3.0 4.3 5.5 4.7 4.2 3.4 3.5 3.9 6.1 5.0 3.1 3.3 5.3 5.1
##  [757] 5.0 4.0 4.3 4.8 3.8 5.4 3.5 5.6 4.6 3.9 4.2 5.1 3.5 2.4 5.0 4.0 4.0 4.5
##  [775] 4.9 5.0 4.7 3.5 6.0 5.6 4.7 5.0 5.8 6.2 4.3 3.0 3.7 3.9 3.9 3.5 4.0 5.6
##  [793] 4.2 4.7 5.5 4.4 4.3 3.7 3.6 3.5 2.7 3.8 3.8 4.2 4.7 4.9 5.3 3.1 5.2 5.4
##  [811] 3.5 4.4 4.6 4.6 4.3 4.7 3.4 2.9 2.8 5.0 4.6 4.2 4.9 4.8 4.4 2.5 3.6 3.9
##  [829] 4.7 4.8 5.6 3.2 6.1 4.6 3.6 3.9 5.3 4.2 3.2 3.8 4.8 5.7 3.6 6.0 3.9 4.1
##  [847] 5.5 5.4 5.1 4.8 4.0 3.5 3.5 2.9 3.3 4.6 3.0 4.1 5.1 5.3 3.9 5.3 6.3 4.3
##  [865] 5.4 6.2 5.4 5.2 5.0 3.8 5.0 4.5 6.3 5.1 4.0 2.4 6.1 4.3 3.0 3.4 4.4 5.3
##  [883] 4.5 4.1 4.7 6.1 4.5 3.5 3.6 4.6 3.5 5.0 4.7 5.5 2.8 4.0 5.1 3.6 4.5 4.3
##  [901] 3.4 3.4 4.7 4.2 5.3 3.8 4.8 5.0 5.9 3.7 4.0 5.0 3.4 5.9 5.1 5.6 4.0 6.5
##  [919] 4.1 4.1 5.2 4.9 5.7 5.6 3.7 5.1 4.9 4.8 5.5 4.6 4.3 5.1 4.2 4.5 4.4 5.5
##  [937] 4.2 5.3 5.0 4.1 2.8 3.9 3.1 3.8 3.8 4.7 5.5 6.3 3.5 3.6 5.2 4.9 4.9 4.9
##  [955] 5.9 3.8 4.9 5.6 4.7 3.0 4.1 3.6 4.3 4.2 3.9 4.4 5.0 5.7 3.6 5.7 2.7 4.3
##  [973] 3.5 3.4 4.5 5.2 5.1 2.9 4.8 4.1 4.3 3.9 5.0 4.3 5.2 3.3 4.6 4.3 3.0 4.3
##  [991] 4.3 5.0 4.5 3.8 6.3 3.4 2.6 3.0 4.8 3.1
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
##    [1] 5.3 4.4 4.8 4.9 4.7 4.0 4.5 5.6 4.0 5.4 3.8 4.5 5.7 5.0 5.8 5.7 5.7 4.1
##   [19] 4.3 4.1 6.9 5.2 4.4 4.8 4.2 4.2 3.4 5.1 5.1 5.8 4.3 4.7 4.4 4.9 3.9 4.7
##   [37] 4.7 3.6 4.4 5.7 4.0 3.0 4.7 4.3 3.4 5.3 5.0 4.7 3.7 5.9 5.1 3.3 4.3 5.3
##   [55] 4.4 4.8 3.5 5.3 5.9 4.6 4.9 4.5 4.8 5.0 4.0 2.9 3.6 3.8 4.5 4.0 5.5 4.5
##   [73] 4.2 4.7 4.0 6.1 4.4 2.6 5.0 3.6 5.0 5.9 3.8 3.9 5.1 5.4 5.4 4.9 3.6 3.5
##   [91] 4.0 6.3 3.9 3.9 5.2 4.3 3.8 2.9 5.2 6.4 4.7 5.0 5.4 5.8 4.8 4.0 4.8 4.6
##  [109] 5.2 3.9 6.4 3.9 3.8 3.7 4.9 4.0 5.2 3.2 5.8 3.5 4.4 4.9 4.0 3.4 3.5 5.1
##  [127] 4.3 3.1 5.7 3.8 3.6 5.5 5.7 5.2 4.2 4.6 4.3 4.0 4.3 4.2 4.0 4.3 3.6 4.4
##  [145] 3.5 4.6 6.0 5.3 5.2 3.3 3.8 5.8 4.2 3.6 4.5 5.4 2.2 5.3 3.2 4.8 2.7 5.8
##  [163] 5.1 2.8 4.1 3.3 5.8 5.3 4.2 4.9 6.1 4.5 5.6 4.3 5.3 3.1 5.9 4.1 4.1 4.2
##  [181] 3.1 4.8 2.8 5.6 4.8 3.5 4.9 4.0 4.5 3.8 5.6 5.3 5.5 5.1 4.5 3.9 3.5 4.1
##  [199] 5.7 4.0 5.0 2.3 4.9 3.6 5.1 5.0 5.9 4.1 4.7 4.7 4.4 5.4 4.5 4.5 3.3 4.6
##  [217] 4.5 5.0 3.1 4.0 5.5 5.5 3.3 4.0 4.5 3.5 4.0 3.9 5.4 5.3 3.2 5.6 5.7 3.2
##  [235] 4.9 7.2 3.7 4.8 5.0 3.8 4.1 3.6 4.3 3.9 4.2 4.7 4.9 4.8 4.4 4.8 5.0 4.0
##  [253] 4.9 5.3 4.3 5.8 4.4 5.3 5.4 5.2 3.7 5.3 3.4 5.3 4.9 5.6 3.4 5.2 5.0 3.8
##  [271] 5.7 3.3 4.1 3.9 4.6 5.8 3.5 5.5 5.9 4.3 4.0 3.4 4.9 5.0 2.5 3.7 3.0 5.5
##  [289] 4.8 5.9 4.3 4.5 3.9 5.8 5.6 5.9 4.7 5.0 3.7 4.3 5.9 4.2 2.5 4.9 5.1 4.9
##  [307] 4.8 5.4 3.4 5.4 3.8 5.3 3.9 5.3 5.1 5.9 5.2 3.0 3.8 7.0 6.1 4.6 4.0 4.4
##  [325] 4.9 3.9 4.7 4.8 3.6 5.5 4.9 5.8 4.8 4.6 3.6 4.8 2.1 4.7 4.1 4.0 5.4 4.7
##  [343] 4.7 6.1 5.1 4.5 6.4 5.1 3.2 4.5 4.2 3.1 3.5 4.1 5.1 4.6 2.5 2.5 4.7 3.0
##  [361] 5.4 4.2 5.4 4.6 5.0 5.8 5.4 2.5 3.6 6.1 4.9 4.0 3.3 4.0 4.0 5.1 1.5 3.9
##  [379] 7.0 4.8 4.4 4.2 5.6 5.4 3.9 2.7 3.8 4.6 4.4 3.1 2.3 4.0 4.7 6.1 4.2 5.0
##  [397] 4.1 3.4 3.5 4.2 4.3 5.3 3.7 3.5 5.9 5.2 4.5 5.5 4.8 4.9 4.0 4.7 4.8 2.6
##  [415] 4.4 4.0 4.0 5.6 4.7 4.8 6.0 3.4 3.7 4.1 5.0 4.3 3.3 4.5 4.5 5.8 4.7 4.6
##  [433] 5.1 4.3 5.8 4.5 4.8 4.5 4.3 4.6 3.5 3.8 6.1 4.9 4.5 5.5 3.1 3.1 4.0 5.1
##  [451] 4.3 7.1 4.2 4.5 4.7 6.0 6.2 5.7 4.6 3.3 3.7 5.8 5.3 3.5 3.7 4.8 4.4 5.9
##  [469] 4.6 4.1 5.1 5.5 3.5 3.7 4.4 4.5 5.1 3.4 3.3 5.2 3.9 4.0 3.9 5.9 5.0 5.0
##  [487] 5.6 4.6 3.9 3.9 4.8 2.9 4.6 4.6 4.4 4.9 3.3 5.3 3.2 2.1 5.3 4.1 5.3 5.7
##  [505] 4.6 3.5 3.1 3.3 5.4 3.7 4.4 5.2 4.3 5.2 4.2 4.2 3.9 4.2 4.0 5.0 5.3 3.2
##  [523] 5.1 3.0 4.0 4.5 5.4 5.6 4.9 3.9 4.7 5.0 5.0 4.1 4.2 4.2 5.0 4.7 3.2 3.4
##  [541] 4.5 5.1 4.1 6.3 3.4 3.9 5.0 3.8 6.3 4.4 5.6 3.8 5.7 5.7 5.3 5.4 4.1 5.9
##  [559] 3.8 6.2 3.8 4.1 6.6 3.3 4.6 3.8 6.3 4.7 5.5 5.0 5.2 3.1 5.3 5.3 4.9 3.7
##  [577] 3.0 5.1 6.2 3.9 5.3 4.3 3.9 5.7 4.5 5.0 3.2 5.3 4.1 4.5 3.2 3.8 5.0 5.9
##  [595] 4.4 5.4 5.2 5.7 3.9 5.8 4.1 5.1 4.1 4.4 4.6 3.8 5.0 3.6 3.1 4.9 5.5 5.1
##  [613] 4.0 3.5 5.2 4.0 6.2 4.8 5.1 4.4 4.7 2.8 4.2 5.4 4.6 3.6 4.8 6.0 5.1 3.9
##  [631] 4.5 3.2 2.7 2.9 4.6 6.2 4.9 5.2 5.3 3.9 5.0 5.7 3.7 4.1 3.7 3.3 4.5 5.2
##  [649] 3.6 5.1 4.1 3.8 4.0 4.4 6.3 3.2 4.2 4.7 5.0 5.7 4.3 5.1 5.3 3.5 4.5 6.0
##  [667] 5.3 5.4 4.3 4.8 6.7 3.5 5.5 4.4 4.5 3.6 3.8 3.4 3.5 3.9 3.1 3.9 4.3 4.3
##  [685] 5.0 4.1 5.8 3.6 3.9 4.9 4.9 4.9 5.6 5.6 4.5 5.0 4.0 5.7 5.5 5.5 5.5 6.4
##  [703] 3.7 5.4 4.5 4.9 4.0 4.7 4.5 4.7 3.6 4.4 4.6 4.9 3.6 4.5 4.9 4.6 4.4 2.8
##  [721] 3.8 5.0 4.6 4.5 4.0 4.0 4.0 3.0 4.8 3.9 4.4 6.2 5.7 5.5 4.5 4.6 4.5 5.1
##  [739] 4.1 5.4 3.6 3.7 5.2 4.6 4.6 5.8 4.0 3.9 5.5 5.4 3.8 4.8 2.7 3.8 5.0 4.2
##  [757] 5.3 6.6 6.0 4.3 3.7 4.8 3.7 4.3 5.8 4.4 4.3 4.0 6.2 4.6 3.5 4.0 3.3 5.4
##  [775] 4.0 4.4 5.1 5.2 3.7 4.5 4.5 4.8 5.3 3.9 5.6 4.1 5.6 4.7 3.8 3.2 2.6 4.9
##  [793] 4.1 4.5 4.2 5.1 2.6 4.4 4.7 3.1 5.4 5.1 3.1 4.7 5.1 4.3 5.8 4.5 5.1 3.6
##  [811] 3.8 5.0 4.4 4.4 3.9 3.7 5.7 2.6 3.7 5.8 4.4 5.5 3.5 3.8 3.8 4.8 5.5 4.5
##  [829] 4.4 4.7 6.0 3.5 5.7 6.2 4.2 5.6 5.1 4.2 5.6 4.2 4.8 5.0 4.9 5.2 4.0 2.7
##  [847] 3.5 3.8 4.2 5.3 4.8 4.5 3.8 5.5 3.6 3.2 4.4 4.6 4.2 5.3 4.1 3.0 4.2 4.1
##  [865] 5.0 4.8 6.8 4.6 6.5 4.9 4.4 4.0 4.8 6.7 5.9 6.0 6.1 5.3 5.7 5.8 5.8 5.3
##  [883] 2.9 3.5 5.8 4.2 3.0 4.1 5.6 5.2 6.2 5.1 4.6 4.5 3.3 4.0 4.2 4.4 4.7 5.1
##  [901] 2.2 4.3 4.4 3.5 4.6 4.8 5.1 3.0 4.9 4.8 3.9 5.4 5.5 4.3 3.9 5.6 3.8 7.4
##  [919] 4.0 5.5 4.4 4.0 5.0 4.5 4.9 3.1 4.6 4.5 4.8 4.9 5.0 4.0 6.5 4.9 5.4 6.1
##  [937] 4.1 4.3 4.6 5.4 4.5 4.3 3.9 4.9 4.6 4.4 4.6 3.4 2.3 4.8 3.8 4.7 3.8 5.4
##  [955] 5.1 3.2 4.8 4.5 5.7 4.5 5.0 4.1 4.8 4.7 5.0 4.2 5.1 5.1 3.6 3.7 3.5 5.1
##  [973] 5.0 5.5 5.5 3.9 4.2 2.5 4.0 4.1 4.0 6.2 5.8 5.0 4.6 4.5 5.7 3.0 5.0 4.3
##  [991] 5.0 4.5 5.3 4.4 5.7 3.0 5.7 6.3 5.0 2.6
## 
## $func.thetastar
## [1] 0.0524
## 
## $jack.boot.val
##  [1]  0.54202899  0.46106195  0.38551913  0.22028986  0.17351190 -0.03818182
##  [7] -0.05060606 -0.25140187 -0.34340176 -0.45373134
## 
## $jack.boot.se
## [1] 0.9872859
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
##    [1] 4.7 4.3 4.9 3.2 4.7 3.6 3.7 6.2 3.9 5.5 5.0 4.3 5.0 3.8 3.6 4.4 4.0 4.9
##   [19] 3.1 5.1 3.1 4.5 4.5 5.1 5.3 5.2 3.9 5.6 4.8 4.6 4.9 3.3 4.7 3.5 4.6 3.9
##   [37] 5.2 3.3 4.3 3.8 3.3 3.7 5.3 3.6 4.8 3.6 4.7 5.2 4.6 5.4 5.4 2.8 2.8 5.4
##   [55] 5.5 3.9 5.6 5.0 3.7 3.7 3.8 4.1 3.8 6.7 4.7 5.3 4.5 5.0 4.4 3.6 3.4 4.2
##   [73] 3.6 4.4 4.4 3.5 4.8 3.4 4.2 4.3 4.2 5.9 4.2 5.1 4.9 3.6 4.8 5.2 2.8 7.0
##   [91] 4.7 6.2 2.1 3.7 4.1 5.1 4.8 3.5 4.7 5.0 4.2 6.2 4.8 4.0 5.8 3.7 4.4 5.2
##  [109] 4.8 4.4 2.1 3.8 4.3 4.2 3.9 5.2 4.4 6.3 5.9 4.2 5.0 5.1 5.3 4.5 5.0 3.6
##  [127] 3.3 3.2 4.9 4.4 6.6 4.3 6.2 5.9 5.3 4.8 4.7 5.8 4.2 4.0 3.6 4.2 4.9 5.1
##  [145] 4.3 3.6 3.5 3.6 4.9 4.7 4.4 4.8 4.2 4.3 4.8 4.7 6.0 5.1 5.2 4.3 5.2 4.4
##  [163] 4.7 5.1 3.8 3.1 5.1 5.8 4.4 5.4 4.5 4.6 5.0 4.9 4.8 6.6 4.4 5.6 5.3 2.4
##  [181] 4.3 5.8 5.4 3.6 5.8 5.5 5.5 5.0 5.1 5.7 4.8 3.1 5.0 4.8 5.3 4.7 5.1 5.2
##  [199] 4.5 4.5 1.6 2.9 5.3 3.8 4.8 4.0 4.6 5.4 6.0 4.6 3.9 4.4 3.3 4.1 5.9 4.6
##  [217] 3.9 2.1 3.3 4.4 4.3 5.9 3.0 5.2 6.1 4.7 4.7 5.1 4.4 4.4 5.4 4.0 3.0 5.1
##  [235] 4.0 4.1 3.9 5.8 4.2 2.9 4.4 3.4 5.8 4.1 5.5 3.7 3.5 5.8 2.7 5.9 3.8 4.6
##  [253] 4.7 5.2 4.3 5.1 3.7 4.1 6.0 4.4 6.1 3.9 3.9 3.1 3.6 4.9 4.2 3.8 5.2 3.2
##  [271] 4.6 3.6 4.2 5.2 3.7 6.0 6.9 2.3 4.7 5.4 4.3 3.0 5.5 5.5 5.0 4.8 4.2 4.7
##  [289] 4.8 5.2 3.7 5.0 3.8 4.2 4.9 4.1 3.8 3.5 4.0 6.1 3.6 3.7 4.3 5.6 4.9 5.5
##  [307] 4.3 2.7 4.7 5.6 4.8 3.9 4.4 4.4 3.8 4.7 3.6 3.5 5.0 2.9 4.7 5.3 3.9 4.3
##  [325] 4.7 5.9 4.9 5.4 5.9 4.9 4.6 3.7 2.9 4.3 3.1 4.8 3.9 3.9 4.3 3.8 4.6 5.6
##  [343] 4.6 4.5 3.7 3.2 3.8 4.5 5.3 6.3 4.6 3.6 5.1 5.5 3.3 5.7 4.6 5.6 5.7 4.8
##  [361] 3.8 5.5 4.1 3.3 5.4 3.1 5.6 4.8 4.5 4.9 3.3 5.8 2.7 4.9 4.9 4.9 4.4 3.9
##  [379] 4.6 3.2 3.8 3.9 4.9 5.5 4.9 4.3 3.8 4.1 5.8 2.5 3.9 3.9 4.4 5.1 4.4 5.2
##  [397] 5.8 5.5 3.5 2.6 4.2 4.6 4.1 5.5 4.9 4.1 3.7 5.6 3.9 4.8 3.7 3.8 3.9 4.5
##  [415] 4.6 5.6 4.3 4.0 4.3 3.2 4.8 4.9 3.0 4.3 4.3 4.0 5.8 5.0 4.8 4.1 7.2 4.3
##  [433] 4.6 4.4 5.7 3.8 5.6 4.2 4.8 3.6 3.6 4.1 5.3 3.6 4.7 3.4 3.5 6.1 4.9 4.9
##  [451] 5.0 4.5 3.2 5.3 4.5 4.2 5.4 5.3 3.7 4.1 5.6 4.2 5.1 4.9 5.0 4.5 3.2 3.6
##  [469] 4.2 4.5 4.9 3.5 4.1 5.9 3.9 4.5 4.9 5.8 3.3 5.4 4.4 4.0 2.9 3.6 4.5 5.8
##  [487] 5.3 4.0 4.4 3.0 2.7 3.8 4.2 4.1 5.1 5.0 5.1 2.7 4.2 4.0 5.1 4.6 1.6 5.2
##  [505] 4.0 4.7 5.4 4.2 4.0 3.3 4.3 5.0 4.5 4.2 4.9 5.3 4.9 4.6 5.6 5.4 3.3 4.7
##  [523] 2.5 3.8 3.9 3.9 4.9 3.5 4.7 4.3 2.5 4.7 5.4 5.3 4.6 5.1 4.7 4.3 3.6 3.8
##  [541] 4.9 5.4 5.6 4.1 3.6 4.4 4.8 5.2 4.8 4.4 4.0 4.9 3.0 3.7 5.3 2.6 4.6 4.7
##  [559] 3.6 4.1 4.2 5.4 6.4 4.8 3.3 5.5 4.3 3.2 4.7 5.2 4.1 4.1 2.7 4.1 5.3 4.0
##  [577] 4.3 5.4 5.5 3.9 3.4 5.9 4.0 3.9 4.1 4.0 2.2 5.4 4.1 5.5 6.3 4.2 5.6 5.4
##  [595] 5.3 4.9 4.4 4.6 3.4 3.9 5.9 4.6 3.8 3.6 3.9 5.5 5.2 4.0 5.6 4.1 5.4 4.1
##  [613] 5.4 6.2 3.7 5.4 5.1 4.9 3.6 4.2 5.2 5.7 3.6 4.7 3.3 4.5 4.7 4.8 5.3 4.2
##  [631] 4.3 4.0 5.1 6.3 4.8 5.0 4.6 5.4 5.5 4.6 6.3 4.3 4.6 4.9 5.8 5.5 5.6 4.7
##  [649] 3.6 5.7 4.1 4.8 4.5 4.2 5.2 5.3 5.5 5.8 5.1 4.6 4.6 5.2 3.6 5.8 5.6 3.6
##  [667] 5.4 5.6 4.5 5.2 4.6 5.0 3.9 5.8 5.0 3.9 5.0 4.3 3.5 3.3 5.1 5.1 3.1 4.0
##  [685] 5.5 5.4 6.3 4.3 6.5 5.4 5.0 4.6 4.4 5.8 2.8 4.4 4.4 4.0 3.2 4.3 3.5 4.2
##  [703] 4.6 7.4 6.0 3.9 5.0 4.4 4.5 4.2 4.5 4.3 6.3 5.2 4.0 4.4 6.0 4.5 3.3 4.8
##  [721] 4.2 4.6 4.9 4.8 6.2 4.8 5.2 4.2 3.5 4.6 6.2 4.5 3.9 4.8 6.6 4.7 4.8 5.2
##  [739] 4.9 5.0 4.0 3.9 6.2 4.9 5.4 3.8 4.1 6.3 3.8 4.1 5.7 4.1 4.6 3.6 4.5 5.5
##  [757] 5.1 5.5 4.5 4.0 4.1 3.5 5.8 3.4 5.0 5.5 3.8 3.7 2.7 3.2 4.7 5.4 5.9 3.2
##  [775] 4.2 5.1 3.8 5.0 4.3 4.4 3.3 3.8 2.9 3.1 5.2 4.6 3.6 3.9 3.8 4.1 4.0 4.1
##  [793] 3.7 3.9 2.8 4.0 6.1 6.2 4.0 5.1 4.5 7.1 4.9 4.8 5.2 4.8 3.3 4.4 5.1 4.2
##  [811] 4.6 4.7 4.8 4.8 3.8 4.8 3.9 3.4 3.9 2.1 4.9 3.4 3.4 4.6 5.9 4.3 4.4 3.3
##  [829] 6.1 5.4 3.9 4.3 4.5 4.8 4.0 4.3 4.4 3.3 5.3 3.4 4.2 3.5 2.9 4.8 5.4 4.9
##  [847] 4.1 5.8 4.9 3.3 3.7 4.0 4.5 4.1 4.5 6.4 4.6 5.1 3.3 4.2 3.3 4.1 5.0 2.4
##  [865] 4.2 4.4 3.2 4.6 4.0 4.9 5.4 4.0 4.1 3.7 4.7 3.0 4.8 4.8 3.9 6.2 6.1 4.6
##  [883] 3.6 2.8 3.3 4.7 4.6 4.6 3.9 5.2 4.4 4.9 4.7 3.3 3.1 5.0 5.2 4.0 4.6 5.1
##  [901] 5.5 4.5 4.6 4.5 3.9 5.2 3.5 6.4 4.0 3.1 4.5 5.5 4.4 4.8 4.7 5.4 4.9 4.2
##  [919] 2.7 3.7 4.1 5.6 5.8 5.1 4.3 3.7 4.3 4.7 5.2 5.3 2.1 5.4 3.8 3.7 3.2 4.5
##  [937] 4.2 5.1 3.4 5.7 5.1 3.8 6.2 4.5 5.5 3.4 4.2 3.1 3.2 5.0 5.7 5.4 6.0 5.1
##  [955] 5.4 4.6 4.3 5.0 4.2 4.3 4.0 4.3 4.6 4.5 6.1 6.6 4.9 5.3 6.8 4.2 6.1 5.9
##  [973] 6.5 4.1 5.1 4.5 5.4 5.7 6.0 4.8 6.0 3.9 3.7 5.7 5.3 4.4 2.8 4.1 5.0 3.6
##  [991] 4.6 5.5 6.1 4.9 4.8 3.8 4.4 4.4 4.0 3.4
## 
## $func.thetastar
##   72% 
## 5.028 
## 
## $jack.boot.val
##  [1] 5.448 5.400 5.400 5.200 5.200 5.100 4.800 4.800 4.700 4.404
## 
## $jack.boot.se
## [1] 1.001968
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
## [1] 1.063339
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
##   3.111751   5.822050 
##  (1.323818) (2.687764)
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
## [1] 0.03713863 0.89728557 0.48359278 0.84305812 0.70137684 0.26759624
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
##    [1]  1.0370176028  1.0477299093  0.2868199212  1.6192072592  1.5214928898
##    [6]  1.1063629216  1.6807797612  1.9128990330  1.9390289826  1.0475415244
##   [11]  0.6686258213 -0.4901134777  1.8345394051  1.0975220551  1.6236202870
##   [16]  0.7001453465  0.9068855251  1.1277652002  0.6594991605  1.5549791314
##   [21] -0.1415739414  0.1674145393  1.1883968018  0.6025208236  1.1023845408
##   [26]  2.5306974517  0.3557442934  1.8671328012  1.2320171065  1.0169282212
##   [31]  1.8210409619  0.6413154790  1.4301828787  1.1129800533  0.3759553407
##   [36]  1.1406860290  1.1075374689  1.7233660073  1.1901714849  0.8753412219
##   [41]  0.5541230832  0.6077665727  1.1513375338  1.7583571730  1.7353674660
##   [46]  1.0925293402  0.7220879113  1.7982633113  1.1351794234  1.7048072002
##   [51]  0.0106534245 -0.0775218087  1.1633447099  0.6783517092  0.2083304909
##   [56]  1.8348959919  1.9781327454  0.4027222375  1.2141294389  0.3250338655
##   [61]  0.8705206262  0.7040180455  1.7489487971  0.7261424748  1.6853365614
##   [66]  0.3087728457  1.1445379384  1.2792048017  0.3626984453  0.7418571828
##   [71]  1.2169020930  0.4069223944  1.1893110625  0.7430444675  1.1283145053
##   [76]  1.2273091194  0.6320556815  1.1851620858  0.3703179665  0.9919750398
##   [81] -1.0897043092  1.0489693422  1.5927471674  0.6646395103  1.3842224797
##   [86]  0.2732322091  0.5357025923  1.2964284098  0.5449343780  1.1521134570
##   [91]  1.1487176907  0.9001934579  1.9794700197 -0.9362159268  1.7667670115
##   [96]  1.9117493006  0.6339560220  0.8418396935  0.9483446602  0.1864573676
##  [101]  0.5899061705  1.1176678954  0.7418571828  2.3474376403 -1.2734719250
##  [106]  1.8767112174  1.7228492113  1.1706400448  1.7599999436  0.3160306102
##  [111]  1.9849381030  0.5417465056  1.5868324139 -0.4951781885  1.1305995050
##  [116] -0.4774826279 -0.6579622664  1.1586789799  1.1235929186  0.2334211679
##  [121]  0.5995821463  1.8537023648  1.3130705187  1.2716970582  2.3889863801
##  [126]  0.9632028258  1.7160972278  1.1061756371  1.5549791314  1.5871492229
##  [131]  1.3966842579 -0.0448040499  0.2392267533  1.1762547367  1.5389128647
##  [136] -0.0409923056  0.2059399703 -0.1178010344  1.1154050873  2.0800225770
##  [141]  1.0608270566  1.6102999610  1.1923136818  0.5350040141  1.0128587356
##  [146]  1.6741409161  0.2229025661  1.6493426753  1.2085754855  0.7145860460
##  [151]  1.0280825198  1.5034339885  0.3662526194 -1.0508637147  1.6551699309
##  [156]  1.8749910643  0.9104883930  0.5783790569  0.2731335978  1.3083245060
##  [161] -0.1617433556  2.3700436028  0.6118913101  0.6023259005  1.2559418876
##  [166]  0.2454935648  1.0247363775  1.0645920464  1.7865125892  1.0088542432
##  [171]  1.4169007964  0.2510772117  0.9592871439 -0.0310144023  1.1726797546
##  [176]  1.2276587271  1.0387038797  1.0530477081  1.6095867635  0.6469685579
##  [181] -0.5014156486  0.3045243359  0.6936978258  1.4264149478  0.8708347196
##  [186]  1.7435280308  0.7660702806  1.1945480174  1.8369887540 -1.0983632205
##  [191]  0.2964209936  1.9697368120  0.5006161256  1.9519340384  0.7517165567
##  [196]  0.6473490606  0.9119145850  1.2009940906  0.9397184135 -0.1259772265
##  [201]  1.7991696429  0.6214090067  1.1168867004  1.7526968096  1.8321766972
##  [206] -0.7969233430  1.0850948376  1.0818597396  0.7405838455  0.2101763109
##  [211]  0.4036912885  1.7118040923  0.9647827401 -0.1565855165  0.6184726464
##  [216]  1.0732935013  1.5104078635 -2.5436511298  1.0101820515  0.2451767646
##  [221]  1.2321070915  2.0984503516  2.0779336516  1.1742431499  2.0607663547
##  [226]  0.7998794042  1.1461493123  1.1858972287  1.7477437236  2.2738820502
##  [231]  1.6853365614  1.1898826735  1.7202312849  0.0007795825  1.6346609895
##  [236]  0.1620091753  0.6169702430  1.1971778686 -0.1143430943  0.6083849969
##  [241]  1.7740210524  1.0612227238  0.6049339224  0.2983076992  1.6892238292
##  [246]  1.2716970582  1.0776036939  0.7023468802  0.2669650529  0.6679056898
##  [251]  1.8558609380  1.5631852623  1.1521421891  0.6513761988  0.8315301147
##  [256]  1.0392521034  0.0845066677  1.5231634611  0.1736790740  1.0165633979
##  [261]  2.0127494538  2.1052167706  1.2884988579  0.6559583450  0.6795632665
##  [266]  0.9060227592  1.0337947996  0.6497675472  1.6068457052  1.0748484373
##  [271]  0.7359331705  0.7237655651  1.2107013439  0.8312651050  0.2819141808
##  [276]  1.1582717880  1.2288013413  1.8950201560  0.9991583270  1.2749229027
##  [281] -0.3600635957  0.1307219333  1.0258727949  0.5611921506  0.3034503887
##  [286]  0.3789056283  0.1880350123  1.6767816762  0.7033298382  1.2344400214
##  [291]  0.2286691997  0.6858688705  0.1478362310  1.7617387331  1.1153393009
##  [296]  0.5454030976  1.3481278545  1.0472213900  1.1170211174  0.3308278063
##  [301]  0.5967126193  1.1829948742  1.0207544606  1.9050169530  0.1994492010
##  [306]  1.6605661887  1.0208794928  1.9591905086  1.5558892260  0.2378079658
##  [311]  0.2810661647  1.2429611990 -0.9981050091  0.5513730905  1.9288954904
##  [316]  1.7094969673  0.6011189832  0.2857035412  1.3647861603  1.7707117321
##  [321]  1.1588354662  0.7015465781  1.1972165065  0.6358697295  0.5347258699
##  [326]  1.9644641970 -1.8890546957  1.9193767870 -1.2273574840  1.7227404479
##  [331]  0.6466282990  0.4079621736  1.9609087240 -0.1543495378  1.1940611491
##  [336]  0.5850343950  0.6857867954  1.0767990103  1.3200261328 -0.8621456714
##  [341]  0.0151453224  1.0870689218  1.6242359771  0.8104767620  0.6436582070
##  [346]  2.0046572048  1.7970807112  0.6699901104  0.6538405424  1.0078370530
##  [351]  0.7709154708  0.2650652741  0.6806084750  2.0812044256  0.5742228570
##  [356]  0.8291434383  2.0876539472  0.6219535054  1.7203534760  1.9998860726
##  [361]  0.6972269870  1.1495524613  1.1851589887 -0.0787651029  1.7327808309
##  [366]  1.0643329346  1.0202993098  1.9703326246  1.0422164571  1.6068457052
##  [371]  1.2292959302  0.3936556560  1.4955942501 -0.1540797961  0.6587304405
##  [376]  0.8815047482  1.2272852082 -0.1456560578 -0.3984682168  1.1750106808
##  [381]  0.6562403277  1.2501840512  1.7458626110  0.5918270137  0.9931348643
##  [386]  1.0363512204  1.8032495910  0.9419809574  1.9030943416  0.6628725818
##  [391]  1.7130271650  1.4897066701  1.7457674018  0.6893677239  0.6552913226
##  [396]  1.0492818568  1.1258644641  0.6039970779  0.2507504850  1.2994110564
##  [401]  1.1444970366  1.4623179418  0.6904992521  0.6378653785  1.2107404192
##  [406]  1.8356356189  0.6541265664  0.9898515649  1.7407530494  0.3030338322
##  [411]  1.0631027113  1.7245060838  1.8634053768  1.0744972425  1.0840120398
##  [416]  0.3068963557  1.6827081411  1.1283913505  0.9455729996  1.7619813379
##  [421]  1.7779753652  0.5553286731  0.4099696551 -0.0811964814  1.7394362912
##  [426]  1.1406207993  1.4491062309  1.1318773213  0.9180391611  0.6565918419
##  [431]  0.1589302833  1.6121607742  0.2487420822  0.1960938634  1.8508912390
##  [436] -0.1145799323  1.9067292877  1.6577951385  1.2469641941  0.2488895004
##  [441]  1.6526121110  0.7761309136  1.9075900000  0.6407273248  0.6490779165
##  [446]  1.2792652088  1.7568824839  1.2541162112 -0.4266597616  1.1915809755
##  [451]  1.2245315788  0.7549503047  1.6975588936  1.2189765021  0.5630692614
##  [456]  1.5004741536 -0.1245420873 -0.8265484639  0.6436012683  0.2440556720
##  [461]  1.4755633288  0.5590032176  1.8823308696  1.5918528975  0.5847789055
##  [466]  0.6904902586  1.0539186122  1.2809376953  1.0747145464  1.1613132007
##  [471]  0.6627099568  1.1478847116  0.2381789762  0.5641645931  0.6128611849
##  [476]  0.3403181956  1.1789045353 -0.4198412385  1.2646010603  1.1638881973
##  [481]  1.0175290191 -0.2152293287  1.9534395669  1.4742654143  0.2199576395
##  [486] -1.2723380464  1.1380135605  1.7039798996  1.1127776311  0.5755700521
##  [491]  0.1732465118 -0.7902787243  0.6461124634 -0.3381040645  1.7504045591
##  [496]  0.6658397446  1.8459228291  1.1915023949  1.4258364442  1.9609281167
##  [501]  0.6325744151  1.1147011584  0.6461082332  1.4828357501  0.6900017476
##  [506]  1.0044246818  0.6812818907  0.8328796173  1.1202868090  1.2092776035
##  [511]  0.6197569481  0.6242699537  1.6386219237  1.2311990152  1.7861275524
##  [516]  0.1357657695  1.7071041436  0.6481536122  1.0864400821  1.0616367785
##  [521]  0.6848527204  1.0630502238  1.6340917452 -0.1836879524  1.5416253701
##  [526] -0.1270955190  0.9570809194  2.5232792307  0.5645390091  0.7666548029
##  [531] -1.0787229821  1.9191245369 -0.0818437498  0.1678817245  1.0523920324
##  [536] -0.2152293287  1.9733273539  1.7371062704  0.6442141595  1.0906705220
##  [541] -0.2133176948  1.6091400486  0.6355435752  0.2761954891 -0.1852191446
##  [546] -0.0783567781  1.7150897051  1.1649756375  1.4632482692  0.0217796054
##  [551]  0.7319496959  1.6079642383  1.8165218050 -0.9679412686  1.0374393368
##  [556]  0.6905048955  1.7803705531  1.1434695464  1.8612640066  1.2368131806
##  [561] -0.1732970817  1.5758343508  1.0329726978  0.3642897863  1.8437468241
##  [566]  1.4852888472  1.1752260298  1.2640099842  1.0862237971  1.8887388163
##  [571]  1.4633045706  1.4951829633  0.5198068206 -1.3103626788  0.6908577969
##  [576]  1.1737444038  0.5462756755  0.6542585953  0.1970356945 -0.2247027039
##  [581]  1.6625801230  0.5614851062  0.9932642128  1.1334880174  0.3793646520
##  [586]  0.6122691695  1.7539028328  1.6238679272  1.2123283540  0.2305746281
##  [591] -0.7272271647  1.1660572078  1.9542394561  1.0597205908 -0.6805228795
##  [596]  1.0649568835  1.0920245703  0.5710767093  1.2105042991  0.6184726464
##  [601]  1.0280892512  1.4307638528  0.6435717597  1.6692823434  1.8506969233
##  [606]  1.0487297008  0.6619868015  0.6618071086  0.6023879694  0.1247831557
##  [611]  1.5212446448  1.6877440793  0.9950728936  2.0136834829  1.1147429625
##  [616]  1.0274737868 -0.1516364564  1.1803213647 -0.0951164537 -0.0449133753
##  [621]  1.5670180383  1.5110326168  1.1131417998  1.6227423539  0.9972793249
##  [626]  1.0154063699  0.1573452653  0.2944262154  1.4665978669  1.0656394985
##  [631]  0.2859129257  1.0913151795  0.3584855829  2.2332451384  1.6031197952
##  [636]  0.6383162523  0.1964619816  0.5451286138  0.6348072295  1.5514513191
##  [641]  0.6378673812  0.5613184772  0.6839475047  1.1985133275  0.5762465094
##  [646]  1.8537023648  1.1874284344  1.0627248476  1.5680952695  0.3712095985
##  [651]  0.7311460419  1.6746320214 -0.9047241773  1.0011372764  0.8314841123
##  [656]  1.7884728225  0.5147978565  0.1747404460  1.6486224505  1.5374517400
##  [661]  1.4801974362  0.2277956058 -0.6149163353  1.4171874910  1.1354856568
##  [666]  0.9641545344  0.6732058446  1.0876664228  1.7376390745  1.6438310056
##  [671]  1.7189387709  1.5615785514  1.6805806882  0.9769495631  1.6770369889
##  [676] -0.5548409702  0.6858613718  1.8537023648  2.1810339850  1.1104357698
##  [681]  0.2299172655  0.6913996586  0.2790941993  1.1544973370  0.6237455244
##  [686]  2.0139734656 -0.4760888055  0.2305732011  1.0332584522  0.3825453206
##  [691]  1.1496823883  1.5843822913  0.9926513044  0.0001747511  1.1633447099
##  [696]  1.5136106980  1.2119389666  1.4393409299  0.7149456074 -0.1072419793
##  [701]  2.1231029965  1.1464185945  0.7039708318  0.2447173016  1.1380733535
##  [706]  2.0039359679  0.2044744205  0.7487997496  1.1058973445  1.7009291928
##  [711]  1.7697467126  0.1817666131  1.8491194816  2.0270171409  1.6490587661
##  [716]  1.0023094450  1.4478495533  1.8378196672  1.2366859435  0.6972212135
##  [721]  0.4734530810  1.1633991756  1.5639169766  0.5549675460  1.1066053149
##  [726]  1.8028698440  1.4979538007  0.0002233092  1.1385399428  0.5870210343
##  [731]  1.7616249045  0.9642967026  1.9313336919  0.7165762656  1.0919681836
##  [736] -0.3655511162  0.7686068527  1.2654716222  0.3450074058  1.0920954680
##  [741]  1.4921910651  1.6713049019  0.2628031317  2.1900994129  1.5114497622
##  [746]  1.8489412129  1.9306632241  0.4989898801  0.6881713371  1.8673360183
##  [751]  1.6318676047  1.2508506097  1.1638007047  1.7445686539  2.0786073693
##  [756]  1.9728312050  0.7224935340 -0.4309657605  1.7697467126  1.5629468606
##  [761] -0.0331889285  2.0113697492  2.0487234654  0.7072509686  0.6246843297
##  [766]  0.6297069012  2.0592506359  0.3152333636  0.5885594009  0.5880874904
##  [771]  0.6368425906  0.0110234017  1.7168841501  0.7096643881  1.8417085730
##  [776] -0.1587274602  1.7246350955  0.6740687163 -0.2651537112  1.5821125277
##  [781]  0.8083570869  1.2197389602  0.2368864343  0.2289787996  2.0590705537
##  [786]  0.8512235553  1.0333594075  0.6074499611  1.7030393426  1.8504228335
##  [791]  1.1929313550  1.9517144492  0.5960808330  0.2811812892  1.8215068675
##  [796]  0.6525373669  0.6327569402  1.0752601878  0.7993498101  0.1717464701
##  [801] -1.1367852635  1.8028714967  1.0430585263  1.3669473309  2.1258885653
##  [806]  1.5123158368  1.8550585686  0.1852582742  0.6872534934  1.1835575718
##  [811]  1.5945745390  0.3267955430  1.6825940678  1.4943250990  1.0332077886
##  [816]  0.2689125972  1.2957235471  1.9937449045  1.0688648213  0.6755578410
##  [821]  1.6208077784  1.0815140750  0.6021684211  2.4248365673  0.6851166873
##  [826] -0.3733683746  0.5684389091  0.9817995500  0.6449652469  1.2422713391
##  [831]  1.1881389533  1.3241809770  1.1016444140 -0.4045887667  0.9888216310
##  [836]  1.8485189834  1.1759212896  0.6261826534  1.6901674621  0.6156753538
##  [841]  1.1975549149  0.6960613262  1.2449535064 -0.5840650741  0.7491545079
##  [846]  0.9960512634  0.6994801672  1.1780609304  1.8567560795  1.0566726626
##  [851] -0.1145125380  2.1621937853  1.0297251791  1.8050270603  2.0017256134
##  [856]  0.6544434642  1.3570853838  1.2017046462  0.0202332260  0.6614243988
##  [861]  1.3951272873  1.1295206135  0.6429689418  1.3868289964 -0.2285292106
##  [866]  0.5671823400  1.0837971505  1.5499383032  0.6579075035 -0.5925455813
##  [871] -1.1690202672  1.5529063032  0.6032789561 -0.1959686764  1.1586789799
##  [876]  1.6370660461  1.3946160727  1.6608264782  1.7987667500 -0.2017839972
##  [881]  1.7102999139  2.0060247547 -1.0902917389  0.2806376061  1.1357748083
##  [886]  0.1456828389  1.5254937410  0.2216289207  0.3337005865  1.2289689749
##  [891]  0.6603459927  1.1431611696  0.2635436103 -0.1444545289  0.5781224123
##  [896]  1.0408540577 -0.0640968849  1.1283082482  0.2698840411  1.7436248154
##  [901]  1.2803811468  0.2428764544  0.1280011084  0.3121128218  0.1860799857
##  [906] -0.1490791961  1.1031659687  0.7355342036  1.6406394578  0.5557068838
##  [911]  1.4715357044  0.0181922995  1.0408516675  1.9750583417  1.2911787256
##  [916] -0.4513545357 -0.2711717170  0.6092579863  0.6598693304 -0.5454344468
##  [921]  0.5125168314 -0.0407318126  1.6545014291  1.2607437259  0.5701314072
##  [926]  1.1371140567  1.1367797456  1.5830738443 -1.9326094336  0.5404115572
##  [931]  1.3416331661  1.5564093039  1.2773748891  1.5589250844  0.2225653608
##  [936]  0.9793120724  0.2012916589  1.6905972874  0.5339510961  0.9528055019
##  [941]  1.7934179231  0.5630692614 -0.6717325849  0.4744744001  1.5318406278
##  [946]  0.2297339951  1.9609459249  1.2179095258 -0.7438740803 -0.1998744448
##  [951]  0.6348295470  1.1074411355  1.1147709407  0.7441720337  0.2117623699
##  [956]  0.9137237684  0.6527542938 -0.1216418274  1.2354597086  1.1157475334
##  [961]  1.5114497622  0.2272925041  0.3094935746  0.5184348983  1.6775040953
##  [966]  1.6474361017  1.0933002625  1.0498954414  0.7048493725  1.1815573370
##  [971]  0.5941981954  1.0460414494  1.0814997400  1.4906389052  1.1019970599
##  [976]  1.6592139528  0.6266649363  0.1050832141  0.2166150957  0.6477804936
##  [981] -0.1950090732  1.2164414401  1.9543927295  1.6495103846  1.2066858773
##  [986]  0.0017561236  1.6105831486  1.1815573370  0.7545211737 -1.1553220558
##  [991]  1.1561258135  0.5475736181  0.7048179242  1.4411603444  1.1327013513
##  [996]  0.7059549770  1.1970132991  1.8221191065  1.2424986855  1.1805968429
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
```

```r
fit2
```

```
##       mean          sd    
##   0.53447670   0.32569793 
##  (0.10299473) (0.07282495)
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
## [1]  0.09594934  0.69388527 -0.26641220 -0.03347931  0.28533248 -0.39603776
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
## [1] -0.0273
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8931083
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
## t1*      4.5 0.008808809   0.8879862
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 4 9 
## 1 1 1 3 2 2
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
## [1] -0.0284
```

```r
se.boot
```

```
## [1] 0.8765168
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

