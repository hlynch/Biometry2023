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
## 2 4 6 7 8 9 
## 2 1 2 2 2 1
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
## [1] -0.0523
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
## [1] 2.654957
```

```r
UL.boot
```

```
## [1] 6.240443
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.6   6.3
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
##    [1] 5.3 4.5 3.9 6.3 4.8 4.3 4.8 5.4 5.5 3.3 4.6 4.9 4.7 4.7 4.7 4.8 4.3 4.7
##   [19] 5.0 5.1 5.8 4.9 4.0 4.1 4.3 5.1 4.7 5.6 4.4 4.7 5.1 5.4 4.8 3.4 5.4 3.6
##   [37] 4.0 4.2 4.5 4.3 5.0 6.5 3.5 4.4 4.4 5.7 4.1 4.7 5.9 4.2 4.9 4.0 4.9 4.3
##   [55] 4.3 5.1 4.0 4.5 5.1 4.6 5.4 5.2 2.6 3.2 5.6 3.6 5.8 4.7 4.5 2.7 4.5 4.3
##   [73] 5.2 4.4 5.2 3.3 4.4 2.5 3.4 5.5 5.7 5.3 6.4 3.5 6.7 4.8 5.9 4.7 5.7 3.9
##   [91] 4.5 3.9 5.2 2.6 5.5 4.4 3.6 5.0 4.5 3.9 4.9 3.7 6.0 6.3 4.7 4.8 4.1 4.6
##  [109] 5.0 5.6 6.2 5.2 5.3 3.6 4.7 4.8 5.2 5.1 5.2 4.8 4.9 3.7 3.8 3.6 5.0 2.9
##  [127] 3.8 3.8 6.3 4.3 5.4 4.9 4.2 3.8 3.9 5.3 2.4 3.7 2.2 4.0 4.5 3.8 3.2 4.2
##  [145] 4.5 5.2 5.3 5.7 3.8 5.2 3.5 4.8 5.5 2.7 5.3 4.7 6.6 4.0 5.4 4.0 4.1 3.4
##  [163] 5.3 2.6 4.1 4.2 4.7 3.8 2.9 4.2 5.1 4.3 5.9 5.8 3.3 4.5 6.8 4.8 5.1 4.2
##  [181] 4.4 3.9 4.3 4.6 4.5 5.7 6.0 4.5 4.9 2.8 3.2 3.5 4.8 3.2 3.8 4.5 3.9 1.6
##  [199] 6.0 2.7 5.7 3.7 4.7 5.3 4.5 4.8 5.3 4.7 4.0 4.0 4.6 4.7 4.5 4.4 4.8 4.2
##  [217] 5.9 5.8 5.7 5.6 5.6 4.7 5.3 4.4 3.8 5.4 5.4 4.0 3.9 4.4 4.7 2.8 6.5 4.5
##  [235] 4.7 4.9 4.5 3.9 5.2 5.7 4.5 6.1 5.9 5.4 5.5 4.3 4.9 5.2 4.6 5.1 3.8 4.5
##  [253] 4.9 5.1 4.6 5.0 3.9 4.9 3.0 5.1 6.3 4.1 5.6 3.3 2.9 5.2 3.2 4.2 5.2 4.6
##  [271] 5.5 5.1 3.0 6.1 5.2 4.0 6.6 5.8 5.7 4.3 4.9 4.7 2.0 4.1 4.0 4.7 4.3 3.7
##  [289] 3.7 6.0 3.7 5.2 5.3 4.5 5.0 5.1 2.0 3.5 4.9 3.5 3.9 4.4 4.9 5.5 4.9 4.3
##  [307] 5.0 4.6 5.4 4.3 5.2 4.4 4.6 4.8 3.9 3.2 3.3 3.7 5.7 4.2 4.5 4.0 4.4 5.2
##  [325] 3.4 3.4 4.0 4.0 3.7 3.8 6.0 3.9 5.7 5.1 4.8 4.3 4.5 4.6 5.2 4.3 4.6 2.5
##  [343] 3.0 3.8 4.4 5.1 3.5 5.5 5.2 6.2 4.3 4.2 6.3 5.6 4.5 2.2 3.4 5.0 3.6 4.6
##  [361] 4.8 5.4 4.1 4.7 4.3 6.1 3.9 4.5 4.9 4.0 3.4 4.9 4.4 5.5 5.0 5.8 5.0 4.0
##  [379] 3.9 4.0 3.9 4.8 4.5 5.8 3.9 4.8 4.6 3.2 4.6 3.1 5.4 3.9 4.1 4.5 4.8 5.1
##  [397] 2.4 3.3 3.8 6.3 5.6 2.7 4.2 4.8 4.6 3.8 2.3 5.5 3.7 3.6 4.9 3.7 4.4 3.2
##  [415] 5.3 5.7 4.9 4.8 3.9 5.0 4.1 4.9 6.3 6.2 3.9 3.5 4.7 2.9 4.9 4.5 5.5 3.5
##  [433] 4.9 3.7 4.5 5.2 4.7 2.9 3.1 4.3 3.5 4.3 3.5 3.8 4.6 3.6 4.5 4.1 5.3 4.2
##  [451] 6.0 4.4 3.2 5.5 4.0 4.8 5.7 4.7 4.5 4.7 4.2 4.2 2.6 3.0 4.2 4.0 4.4 4.4
##  [469] 4.0 4.2 4.5 4.3 4.4 4.7 6.0 4.4 6.1 3.9 5.4 3.9 3.2 5.4 4.9 5.3 4.4 4.5
##  [487] 4.7 3.8 4.2 5.3 4.4 4.9 4.2 4.5 4.1 5.8 5.7 3.3 4.2 4.1 4.1 3.4 5.4 4.6
##  [505] 5.3 6.7 6.5 5.0 5.3 4.3 3.5 3.9 2.9 4.0 5.9 4.6 5.5 4.4 4.5 2.2 4.2 4.5
##  [523] 5.1 3.8 3.6 4.6 4.3 3.3 4.4 3.7 4.1 3.6 4.6 3.5 5.5 3.6 4.3 4.9 4.2 4.1
##  [541] 4.7 5.3 4.6 4.1 3.9 5.4 3.2 2.4 3.9 5.3 5.6 3.9 4.7 4.2 4.9 3.7 3.8 4.2
##  [559] 3.7 3.1 4.4 5.8 4.6 3.6 3.6 4.2 3.4 5.9 3.5 4.4 4.6 5.1 5.7 2.6 4.9 5.0
##  [577] 4.9 3.6 3.4 5.3 2.9 4.5 3.3 5.2 5.2 6.4 5.3 3.2 5.5 4.3 3.7 5.5 5.7 4.2
##  [595] 4.1 5.1 3.7 4.9 2.6 4.4 4.3 5.5 4.0 5.4 4.0 4.8 4.3 3.7 5.1 4.1 5.4 5.1
##  [613] 4.8 2.9 5.9 5.0 5.1 2.9 4.5 6.7 4.8 4.7 4.8 4.6 4.2 4.6 4.3 3.4 3.5 5.2
##  [631] 4.2 5.2 3.2 2.4 2.9 3.8 4.6 4.6 4.4 2.8 4.2 5.4 3.8 3.8 3.2 6.2 4.1 6.2
##  [649] 5.0 4.6 4.8 3.3 3.6 3.4 4.9 6.1 4.7 4.7 6.1 4.2 2.9 4.8 4.1 4.7 3.2 5.4
##  [667] 2.4 6.3 3.8 4.1 4.4 5.9 4.2 3.8 5.0 5.1 5.7 5.1 3.5 3.8 5.2 4.0 5.3 3.7
##  [685] 2.9 3.8 4.0 5.5 6.1 3.6 4.9 5.2 3.2 6.1 4.2 3.4 4.8 4.0 4.1 5.3 5.1 4.4
##  [703] 4.1 3.9 4.8 3.4 5.1 5.6 5.7 3.5 6.4 6.4 3.9 4.4 3.5 5.4 6.3 4.1 4.2 4.5
##  [721] 4.7 5.0 4.1 5.2 4.4 4.7 3.2 5.8 6.3 3.6 3.8 4.3 3.9 4.9 4.7 4.5 3.8 5.7
##  [739] 3.4 3.4 6.2 4.5 4.0 4.6 4.9 3.7 5.0 3.9 5.5 4.2 4.4 4.3 4.7 5.0 4.0 6.4
##  [757] 4.6 4.3 4.5 5.7 5.8 4.6 4.3 5.1 4.5 5.5 2.8 2.9 3.4 3.9 4.5 5.6 6.2 5.2
##  [775] 5.3 3.5 5.0 5.1 4.1 3.7 6.2 4.7 4.1 3.7 6.1 5.0 4.4 4.1 4.8 4.0 4.1 4.7
##  [793] 3.4 4.1 6.1 5.3 4.9 6.2 2.6 2.7 4.9 4.3 3.9 5.6 3.5 3.5 5.0 7.0 4.5 5.3
##  [811] 6.2 3.0 4.2 3.7 4.3 5.1 3.4 3.6 3.8 3.9 4.6 2.9 4.4 4.0 4.9 3.7 3.3 4.6
##  [829] 4.9 4.1 4.3 4.7 5.0 4.4 5.3 4.6 4.3 3.4 3.7 3.5 4.2 5.8 5.1 2.5 6.1 4.6
##  [847] 4.2 4.6 4.2 5.3 3.0 3.9 5.2 2.9 5.1 3.9 4.1 3.3 5.1 4.8 5.0 4.3 5.0 4.9
##  [865] 2.8 5.5 4.1 4.0 3.8 3.8 3.5 4.8 3.9 6.9 3.9 4.2 5.0 4.2 4.2 3.6 5.1 3.5
##  [883] 4.2 4.7 3.8 4.1 3.4 5.3 4.0 4.5 4.1 4.2 3.9 5.3 4.0 4.3 7.2 5.8 3.9 6.7
##  [901] 4.1 5.1 4.4 4.1 5.5 2.4 5.7 4.3 4.9 3.9 5.8 4.3 4.3 5.1 4.6 5.1 4.1 4.5
##  [919] 5.3 2.3 3.2 5.4 2.9 3.4 5.4 4.6 4.8 5.6 2.8 5.3 4.3 5.5 5.1 4.7 5.2 5.2
##  [937] 5.0 6.1 5.8 4.3 3.5 6.3 5.9 3.5 3.8 5.8 5.2 5.3 4.6 5.1 3.6 5.5 6.1 3.8
##  [955] 5.2 6.3 3.3 2.7 3.6 4.3 4.6 6.3 4.8 4.0 5.8 4.8 5.3 4.6 3.8 3.4 5.3 4.2
##  [973] 4.9 5.2 5.2 6.7 3.6 3.2 4.8 3.5 6.1 4.2 4.0 4.9 5.8 3.9 5.0 4.5 4.5 5.2
##  [991] 3.7 3.4 4.9 2.5 3.8 4.5 5.4 4.3 5.8 4.1
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
## 2.6975 6.3000
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
##    [1] 4.3 4.5 3.7 4.5 5.4 4.2 3.6 4.1 5.4 5.1 5.7 3.9 5.2 5.8 4.5 4.6 5.2 5.2
##   [19] 4.1 6.2 3.2 4.8 6.9 5.6 5.7 4.5 4.7 4.4 4.0 4.6 5.3 4.1 3.3 4.7 4.4 4.8
##   [37] 3.6 5.9 4.2 4.8 4.9 3.4 3.4 5.5 4.0 4.5 4.2 4.1 4.0 4.5 5.0 6.2 5.4 5.7
##   [55] 5.1 5.2 3.4 5.8 5.8 5.3 2.7 5.4 3.3 5.4 5.4 4.1 4.4 4.6 6.6 5.4 4.9 5.4
##   [73] 5.4 4.0 3.2 3.8 4.7 4.3 4.2 5.2 4.3 4.0 4.1 6.4 4.4 4.8 4.5 4.5 5.4 4.4
##   [91] 4.8 5.7 3.5 2.6 4.2 4.9 5.0 3.6 5.5 5.9 4.4 5.4 6.3 5.8 3.3 4.6 4.4 3.7
##  [109] 5.8 3.4 5.2 5.1 3.5 4.0 4.9 4.2 5.0 5.5 4.8 4.4 4.7 4.8 3.5 3.9 2.9 3.6
##  [127] 6.2 4.5 3.7 5.0 4.6 4.9 4.2 4.6 4.3 4.0 4.6 4.8 4.6 7.3 5.0 3.4 5.7 4.1
##  [145] 4.7 5.7 4.6 5.6 6.0 5.3 4.4 4.5 4.4 3.8 5.6 5.7 4.4 5.6 5.5 4.2 3.8 2.8
##  [163] 4.2 3.7 2.8 5.1 4.5 4.4 4.6 4.8 2.5 5.8 2.9 4.5 3.9 4.5 3.0 5.7 3.1 5.4
##  [181] 3.6 4.8 4.6 3.3 5.1 4.8 5.0 5.0 4.0 4.1 4.9 5.5 3.6 4.6 3.8 4.8 3.5 5.0
##  [199] 4.1 5.5 3.3 4.8 4.1 4.6 5.4 3.9 4.0 5.5 3.7 4.9 4.3 3.9 4.7 4.9 4.0 6.6
##  [217] 4.4 4.5 3.5 3.6 4.6 3.8 4.6 5.0 3.7 4.3 3.9 4.6 3.7 4.2 4.0 5.1 4.6 4.4
##  [235] 4.7 4.4 5.3 6.0 3.4 3.0 3.6 4.4 4.2 4.9 3.9 4.5 4.6 5.5 5.1 6.3 4.9 3.9
##  [253] 4.5 5.6 5.2 4.4 4.0 5.4 5.2 5.9 4.1 4.9 4.6 4.2 3.5 5.0 3.0 4.2 3.4 5.6
##  [271] 3.3 4.9 6.2 3.6 4.0 5.8 3.5 3.7 5.3 5.1 3.3 4.9 5.1 4.4 5.8 4.0 3.6 6.1
##  [289] 4.8 5.9 3.9 3.3 3.9 4.8 3.4 3.3 4.3 4.8 4.1 4.1 3.6 3.4 4.6 3.1 2.9 4.5
##  [307] 4.1 5.5 4.2 4.1 5.1 4.6 3.1 3.9 2.9 5.6 5.7 3.4 5.5 6.3 5.3 4.7 6.3 4.3
##  [325] 3.9 5.3 4.7 3.8 4.6 6.1 4.6 4.7 3.0 3.4 2.9 5.0 5.8 4.1 4.5 3.4 5.1 6.0
##  [343] 3.9 4.3 5.2 3.4 4.8 5.3 4.6 4.5 3.0 4.1 4.4 4.1 3.7 2.4 4.3 4.7 4.2 4.6
##  [361] 5.0 2.8 3.7 3.5 4.4 5.5 3.7 3.2 5.2 4.5 3.3 2.8 4.2 4.3 6.5 4.0 3.4 3.4
##  [379] 5.7 3.4 5.1 3.8 3.4 4.4 3.9 4.9 3.3 6.1 5.5 4.7 4.3 3.7 2.3 4.1 5.7 5.5
##  [397] 4.0 3.0 5.3 3.5 4.0 4.6 6.3 4.8 4.0 4.5 4.5 5.1 5.1 3.8 5.7 4.0 4.6 4.5
##  [415] 1.6 4.8 3.7 3.8 4.5 5.2 5.1 3.1 5.6 3.3 5.4 4.7 5.1 5.3 5.4 5.3 5.3 2.3
##  [433] 3.8 4.0 4.5 4.5 6.0 4.3 4.2 4.9 4.8 2.1 5.6 2.5 4.7 4.4 4.6 2.2 5.6 3.9
##  [451] 3.3 3.6 4.9 5.1 3.6 4.3 4.3 3.4 4.3 4.3 3.3 4.7 4.1 3.5 4.3 4.2 3.9 4.5
##  [469] 4.8 3.8 4.2 3.3 4.4 3.8 4.8 6.3 3.7 5.4 3.7 2.7 3.8 3.1 4.6 5.1 4.0 5.3
##  [487] 4.5 5.9 4.4 4.4 4.0 4.8 4.7 5.9 3.9 3.7 6.2 4.7 4.1 4.1 4.4 4.5 3.6 4.9
##  [505] 3.4 4.2 4.0 4.8 5.0 5.3 4.6 4.2 3.3 3.4 4.9 3.4 4.9 3.8 4.4 4.6 4.1 6.7
##  [523] 3.7 3.8 4.4 6.7 5.2 5.0 6.2 4.2 4.8 5.2 3.8 6.3 4.2 4.1 3.6 5.5 4.3 4.0
##  [541] 4.7 4.6 5.7 3.3 3.3 5.2 5.7 4.0 5.6 4.4 5.9 3.8 4.0 4.8 5.3 3.3 3.5 5.2
##  [559] 4.8 5.8 5.7 3.8 5.4 2.9 4.8 5.0 3.7 6.0 4.0 5.2 4.0 3.3 4.3 4.9 4.9 4.2
##  [577] 5.8 4.3 6.7 4.2 3.8 5.1 4.0 5.4 3.7 4.5 4.3 3.0 4.4 4.2 4.4 3.4 5.3 2.6
##  [595] 4.0 4.5 4.6 5.4 4.7 5.2 4.6 6.2 4.5 4.8 4.1 5.5 3.6 4.2 4.0 5.9 4.3 4.6
##  [613] 3.3 5.6 4.8 1.8 5.7 4.5 3.5 4.4 6.1 3.7 5.6 2.5 4.2 4.3 4.9 4.4 4.7 4.0
##  [631] 5.0 5.1 4.8 4.1 4.1 5.9 4.3 4.4 3.5 2.7 6.0 5.9 4.4 4.1 4.9 3.4 4.7 2.9
##  [649] 6.0 4.2 3.3 4.3 5.3 4.3 4.2 4.9 3.4 2.5 5.3 6.0 3.6 3.7 4.3 5.2 4.6 4.8
##  [667] 5.4 4.7 5.7 4.1 4.2 4.8 5.1 4.5 4.3 3.6 3.5 5.3 6.0 6.4 6.4 4.2 5.8 3.6
##  [685] 5.3 5.2 3.6 4.1 4.8 5.6 5.0 4.4 5.7 3.8 3.7 3.3 2.9 3.8 4.8 5.1 4.8 6.3
##  [703] 6.6 5.8 3.5 3.8 6.3 4.5 4.8 5.1 3.1 5.0 4.4 3.4 5.1 3.1 4.6 4.3 4.1 3.9
##  [721] 5.2 3.9 2.6 4.2 4.9 5.2 4.6 4.7 4.3 5.4 3.6 3.8 4.2 3.8 3.5 4.1 3.7 2.1
##  [739] 4.6 6.7 4.4 4.2 4.6 3.4 3.9 6.1 4.8 4.5 2.8 3.5 5.2 4.5 3.7 5.7 4.2 3.3
##  [757] 3.2 4.2 4.3 3.4 2.4 2.9 4.2 4.9 4.7 4.4 2.9 5.4 6.8 5.3 5.0 3.5 4.0 3.3
##  [775] 5.4 3.6 5.6 3.3 5.4 3.6 4.5 2.1 5.8 5.2 3.3 5.3 5.3 4.8 3.9 4.6 4.9 4.0
##  [793] 4.1 5.4 5.1 5.1 2.7 5.0 5.7 3.8 4.4 3.1 4.0 5.3 3.0 5.7 5.8 4.6 4.0 2.9
##  [811] 5.0 4.3 3.6 3.6 5.1 3.4 4.7 4.6 4.9 5.8 5.0 6.0 4.8 5.1 4.2 2.9 5.4 3.8
##  [829] 5.9 4.5 3.1 5.5 4.5 3.9 4.0 5.3 3.2 4.6 4.0 4.3 4.3 4.5 5.8 4.4 5.4 4.5
##  [847] 5.3 4.1 5.3 3.7 4.8 5.9 4.1 5.9 4.3 2.7 4.6 3.1 5.5 4.5 3.4 5.6 5.8 4.5
##  [865] 5.5 4.5 3.2 4.0 4.4 4.9 4.0 3.1 5.0 4.6 5.2 4.4 5.1 3.2 4.2 4.5 5.4 4.6
##  [883] 4.7 5.2 4.3 4.8 5.8 3.9 5.0 6.1 4.8 4.7 3.6 4.6 5.0 4.3 5.3 4.1 4.8 3.5
##  [901] 5.0 3.8 5.0 4.4 3.4 4.1 5.2 4.4 5.7 5.1 4.9 5.1 6.8 3.2 4.4 5.9 5.7 3.8
##  [919] 3.3 4.2 3.5 3.6 5.2 3.1 5.1 4.9 3.9 5.7 5.8 4.5 5.8 3.0 4.8 3.9 5.8 4.2
##  [937] 4.8 3.9 4.8 4.9 5.7 4.0 3.8 4.8 5.0 4.3 2.8 5.3 3.6 4.4 5.5 5.3 5.3 5.2
##  [955] 4.2 5.2 4.5 5.6 5.0 5.3 6.3 4.3 3.7 4.1 5.1 5.8 5.8 4.3 5.2 5.9 4.2 4.0
##  [973] 3.8 4.6 4.7 4.5 3.9 3.9 2.8 2.9 4.7 3.4 6.5 4.6 4.4 3.2 4.8 4.2 3.4 4.5
##  [991] 3.9 3.9 4.4 3.0 4.0 4.7 5.3 3.4 3.8 4.7
## 
## $func.thetastar
## [1] -0.006
## 
## $jack.boot.val
##  [1]  0.45432836  0.32296512  0.31565217  0.17286136 -0.01728723 -0.01388889
##  [7] -0.12917847 -0.25335196 -0.47134503 -0.49375000
## 
## $jack.boot.se
## [1] 0.9421541
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
##    [1] 4.6 4.4 4.2 3.1 4.6 5.0 4.4 3.3 4.6 5.4 4.6 3.1 3.1 5.7 3.8 5.6 3.9 4.2
##   [19] 2.7 4.4 4.7 4.7 4.3 6.0 4.0 4.4 5.9 4.2 4.7 4.9 4.5 5.3 5.2 2.9 5.6 3.5
##   [37] 2.8 4.2 5.6 4.9 3.0 5.5 3.6 1.7 5.6 4.5 5.1 3.9 4.4 6.4 4.2 4.9 5.4 4.8
##   [55] 4.8 2.8 5.2 5.2 5.5 3.5 5.7 4.5 6.1 5.4 4.9 4.5 5.2 4.6 6.0 3.7 5.7 4.5
##   [73] 3.0 6.7 3.5 5.3 6.4 4.1 2.7 5.4 4.3 4.2 5.3 4.1 5.7 4.7 4.6 4.3 4.4 4.5
##   [91] 4.2 4.1 3.7 6.4 5.8 3.4 4.5 3.3 4.7 3.4 3.9 4.4 4.0 3.3 5.8 5.8 5.0 4.6
##  [109] 4.1 4.0 3.9 4.4 4.9 5.2 4.7 4.2 5.1 4.6 3.2 4.0 5.3 3.9 4.6 4.3 5.0 3.7
##  [127] 4.1 5.3 6.8 5.8 5.6 3.2 4.6 5.9 5.2 4.2 5.7 4.6 5.5 5.3 4.2 3.7 4.2 5.9
##  [145] 4.4 4.6 4.4 4.1 6.3 4.8 4.4 5.5 4.2 6.1 6.1 4.1 2.0 4.0 4.0 5.7 4.1 4.4
##  [163] 3.8 4.5 4.6 4.2 4.8 5.8 3.9 2.9 3.5 5.7 4.3 4.1 4.7 5.5 6.0 5.1 4.7 5.3
##  [181] 6.2 3.1 5.1 2.6 5.4 4.3 6.0 4.0 3.5 5.6 4.5 5.3 3.6 5.3 3.2 5.1 6.0 2.9
##  [199] 5.1 6.0 3.9 5.3 3.1 4.6 5.3 3.9 3.4 2.5 6.1 4.7 2.5 6.3 4.7 5.2 3.8 5.5
##  [217] 4.3 5.3 4.2 6.5 4.8 3.7 5.0 6.0 5.4 6.3 2.0 5.0 5.0 3.9 4.8 4.2 4.1 2.9
##  [235] 4.4 4.9 4.8 5.6 5.4 4.9 4.8 5.1 4.4 4.4 4.4 6.4 4.4 4.7 3.0 3.2 5.2 4.2
##  [253] 4.4 4.9 4.8 5.2 4.1 5.8 3.4 5.1 4.8 3.7 5.7 4.1 3.9 3.4 4.1 5.3 4.2 3.3
##  [271] 6.1 5.6 4.8 5.3 3.2 5.0 4.6 4.4 3.6 4.5 3.2 6.0 3.3 5.5 3.2 3.2 2.7 5.5
##  [289] 4.0 5.7 3.4 5.9 4.4 4.5 5.9 4.7 4.2 2.9 3.1 5.0 4.1 4.3 3.8 4.9 5.9 5.7
##  [307] 5.2 6.1 5.1 3.6 3.6 5.4 4.2 3.3 4.6 4.2 3.6 5.0 3.2 3.4 3.5 3.2 6.1 5.5
##  [325] 3.9 4.5 4.8 3.1 5.9 4.2 4.3 2.8 6.0 4.3 3.1 4.0 4.8 5.0 5.4 5.0 3.8 4.8
##  [343] 4.9 4.4 5.0 3.7 3.1 4.1 4.4 4.8 6.5 4.7 3.7 3.1 4.3 4.2 5.0 4.6 4.1 6.2
##  [361] 3.4 3.4 4.3 5.3 3.6 4.6 5.8 4.3 3.9 4.7 3.6 4.4 4.2 5.9 3.8 4.8 3.8 6.7
##  [379] 5.4 5.0 5.0 4.1 5.8 4.4 5.1 3.9 5.6 4.5 4.8 3.6 4.7 5.4 3.9 4.5 3.0 3.2
##  [397] 4.7 6.3 3.1 3.9 5.0 4.2 4.0 5.1 4.4 4.4 4.6 6.2 4.3 4.7 4.9 5.5 4.4 5.0
##  [415] 3.6 4.4 3.8 4.1 5.0 4.9 4.9 4.4 4.8 3.7 3.9 5.3 4.2 3.6 2.4 4.0 5.7 4.0
##  [433] 6.0 4.8 5.2 3.6 4.0 3.1 3.6 3.9 5.8 4.6 4.2 3.7 3.4 3.6 5.1 3.4 5.4 4.0
##  [451] 5.0 5.0 4.4 3.4 5.0 3.5 5.4 4.8 3.8 5.0 4.2 3.9 3.9 5.4 1.9 2.1 4.9 5.0
##  [469] 5.4 3.8 3.4 3.6 4.2 6.0 3.4 6.7 4.4 3.7 4.4 4.0 3.8 3.2 5.2 4.6 5.1 3.8
##  [487] 4.0 5.4 3.5 3.8 4.0 3.7 5.5 3.5 3.8 6.0 4.7 3.6 4.7 3.9 5.2 6.3 5.6 5.7
##  [505] 2.7 3.9 5.1 4.2 6.1 5.5 2.1 4.7 3.6 4.1 4.3 5.1 5.2 5.4 5.8 4.3 4.4 3.8
##  [523] 6.1 4.6 4.7 5.8 4.9 4.6 5.7 4.3 4.5 5.2 4.0 5.4 4.4 3.9 6.9 4.3 5.0 4.6
##  [541] 5.4 6.1 5.3 4.7 4.5 4.7 4.5 4.9 3.4 3.7 5.4 4.6 5.6 3.6 4.9 4.6 4.0 4.0
##  [559] 3.3 4.3 5.3 5.0 3.9 4.6 3.5 3.2 4.6 2.7 3.9 4.2 6.4 4.2 3.7 5.5 3.9 4.1
##  [577] 5.5 4.2 5.3 5.0 4.7 5.4 3.2 3.3 2.9 3.1 4.4 5.4 4.5 3.9 4.9 3.6 5.8 4.4
##  [595] 5.0 4.9 4.3 5.8 5.9 5.0 3.8 5.3 3.4 2.9 5.4 6.2 4.9 5.0 5.6 3.9 4.3 4.7
##  [613] 2.8 3.7 5.0 4.8 3.4 5.5 5.2 4.1 4.6 3.4 5.6 5.6 3.7 3.9 3.1 5.5 4.2 5.1
##  [631] 5.7 4.7 3.2 5.8 4.3 6.5 3.4 4.7 5.4 4.7 5.2 3.9 4.0 5.3 4.2 5.7 4.9 1.9
##  [649] 4.2 4.0 2.7 4.2 5.3 4.6 4.9 4.6 3.7 5.1 6.8 4.7 4.3 5.2 4.4 3.0 3.4 4.7
##  [667] 4.9 5.2 4.4 5.6 6.5 5.3 5.2 6.2 5.4 5.4 4.2 4.3 3.3 3.4 5.0 4.3 3.5 5.0
##  [685] 3.9 4.0 3.8 3.8 5.3 5.0 4.5 4.7 4.0 4.6 4.2 5.6 4.8 4.5 3.6 5.2 5.1 4.0
##  [703] 4.5 2.8 4.9 5.0 5.1 3.8 3.2 5.1 3.7 6.1 3.3 4.1 3.6 4.5 4.2 3.5 3.9 6.2
##  [721] 5.4 5.5 5.4 3.0 5.2 4.2 5.0 5.2 3.3 4.1 3.5 5.6 3.9 4.4 4.0 4.1 3.8 4.3
##  [739] 5.8 4.0 4.8 4.4 3.3 3.5 4.1 5.0 5.6 5.2 5.8 4.5 3.2 3.1 3.6 5.1 4.4 4.7
##  [757] 2.7 5.1 4.8 5.4 5.0 4.9 6.1 5.8 6.0 5.5 5.6 3.8 6.3 4.5 4.8 5.1 4.4 3.3
##  [775] 4.9 4.6 4.8 4.4 3.7 5.0 5.1 4.4 4.7 6.2 6.4 5.7 5.1 4.1 4.7 5.8 3.4 3.4
##  [793] 4.8 5.5 4.8 4.4 5.5 4.7 4.6 4.6 5.4 2.6 3.2 5.0 4.0 5.3 5.1 4.2 4.6 4.5
##  [811] 4.9 3.3 4.2 4.4 4.7 4.8 4.2 3.8 4.7 4.1 3.6 5.1 3.2 3.9 4.2 4.5 5.2 3.5
##  [829] 3.0 4.8 3.1 4.9 5.0 4.9 4.8 3.5 3.7 3.3 5.4 3.8 4.1 5.7 4.7 5.2 4.8 4.8
##  [847] 5.2 2.1 5.2 4.9 6.7 3.9 4.4 4.1 4.1 4.8 3.9 4.4 4.1 4.0 4.9 5.3 4.7 5.5
##  [865] 3.7 5.6 3.7 4.1 4.4 4.0 3.6 1.9 2.3 5.5 4.1 6.3 3.7 5.0 3.7 4.0 4.5 4.7
##  [883] 4.5 4.8 3.2 3.7 4.3 4.0 4.0 5.2 6.6 4.8 3.4 4.4 4.1 4.2 3.9 3.8 2.5 4.3
##  [901] 4.6 5.7 5.1 5.0 5.7 3.8 4.9 4.8 4.5 5.2 2.3 5.1 5.8 4.5 4.3 5.0 3.6 3.6
##  [919] 5.1 4.9 4.8 4.8 4.8 4.0 5.7 6.1 3.7 4.1 4.5 3.7 4.6 5.9 4.1 5.7 5.7 4.2
##  [937] 6.1 5.4 6.0 3.6 5.3 4.0 3.2 5.0 4.2 4.2 4.0 5.3 4.9 4.5 3.6 4.4 5.7 5.1
##  [955] 3.6 4.9 3.7 4.8 4.4 3.8 4.3 4.6 3.9 5.4 6.1 4.5 4.0 5.9 6.5 4.1 5.3 4.5
##  [973] 4.1 4.5 3.3 5.6 3.2 3.5 3.6 4.6 3.5 4.7 4.5 5.6 4.4 4.0 5.1 3.5 3.6 3.6
##  [991] 5.5 4.1 4.3 4.0 3.9 4.2 4.6 3.7 4.0 3.7
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.600 5.400 5.400 5.336 5.300 5.000 4.800 4.800 4.600 4.400
## 
## $jack.boot.se
## [1] 1.139387
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
## [1] 1.136757
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
##   4.067315   5.757864 
##  (1.749481) (2.636067)
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
## [1]  0.5142153 -0.1966482  0.1249742  0.1297291  0.6818177 -0.4850347
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
##    [1] -0.385457558 -0.264937336  0.738432720  1.226766737 -1.186476959
##    [6]  0.989083927  0.176112069  2.032218040  1.244170769  1.023280388
##   [11] -1.056544997  1.472006561  1.111172130 -0.118253067  1.215312196
##   [16]  1.142604840  1.183529093  0.982327290  1.320619843  1.244357161
##   [21]  1.291564453 -0.205785396  0.211848141 -1.348740132  0.780477178
##   [26]  0.818840564 -0.344879083 -0.505307836  0.800383804  0.998406370
##   [31]  0.928031734  0.321477098  0.003806689  1.392987757 -0.011910597
##   [36]  0.784425442  0.418663159 -0.576152516  0.826424271 -0.201325979
##   [41] -0.749688630 -0.504525525 -0.591960331  0.308749758  0.968014108
##   [46]  0.327411165  0.627789039  1.439653605  1.387357622  1.183774075
##   [51]  1.504259127  0.437539037  1.324671509  0.474175620 -0.051681300
##   [56]  0.829992257 -0.546193406 -0.859300269  1.157837754  0.260416412
##   [61]  0.134422161  0.094195404  0.927218152 -0.008145608 -0.842799544
##   [66] -1.371199830  0.017338704  1.397581857  1.034001762 -0.338087494
##   [71]  0.675820851  0.372675085  1.341555467  1.460641300  1.263916073
##   [76]  0.044799592 -0.721064584 -0.488353413  0.270217526 -0.816863659
##   [81]  0.711577802 -0.649654143 -0.792685629 -0.051324143  0.428390598
##   [86]  1.357409321  1.436438063 -0.305953555  1.675171825  0.351480722
##   [91] -0.286213460  1.355334835  0.926661312 -0.746522398  1.004375790
##   [96]  1.160393034  0.639910356 -0.289719380 -0.412624837  0.280333848
##  [101]  0.115257151  1.133226557 -0.187819432  0.527919050 -0.360602958
##  [106] -0.296500083  0.915663377  0.531460546  0.841560067 -0.472620995
##  [111]  1.849123537 -0.689735196  1.299254647  1.334825982  0.208177752
##  [116] -0.929764176  0.783449138  0.591726087  1.003896323  0.145271503
##  [121]  1.206604442  1.357051685  0.852134524  0.205677115 -0.309894790
##  [126]  1.274632943  1.294517501  0.545928295 -0.292629062 -0.364105427
##  [131]  0.984691655  0.995790386  1.597897960  0.141523516  1.604177453
##  [136]  1.396113587  0.872077618 -0.164100835 -0.637852291  0.633518014
##  [141]  0.960629655  1.205872316  1.011126133  0.661746089  1.141569653
##  [146]  0.869505787  0.470944442  0.956850101  0.924137070 -0.741790984
##  [151]  0.128407544  0.331839486  1.436702793 -0.693118339  0.391128007
##  [156] -0.249646223  0.171052822  0.903303072  1.387357622 -0.773538559
##  [161]  0.092949227  0.448907177  0.896537745 -0.639496641  0.550162574
##  [166]  1.541015016 -0.546193406  0.840197096  1.282094061  1.257124248
##  [171]  1.214988207 -0.155716722 -0.553870081  1.074374636 -0.236643040
##  [176]  1.650893965  1.735273003  1.162902375 -1.108767446 -0.004031240
##  [181]  1.378823589 -0.416267930 -0.457752186  0.920997145 -0.236336575
##  [186]  0.952847929  0.716354469  0.658012992 -0.956906511  1.061704086
##  [191] -0.069574619  0.180785354 -0.679031419 -0.425600306  0.983300816
##  [196]  0.520733702 -0.589014445  0.794306478  0.520981053  1.227042360
##  [201]  1.007273406  1.479370413  0.549041619  1.241042831  0.605684349
##  [206]  0.222551429  0.851730375  0.672700935  1.041631630  1.439562388
##  [211]  1.381075809  0.744577863  0.922656664 -0.847593961  0.951437656
##  [216]  0.040669123  1.024204170  1.219104932 -0.573899980 -0.119105050
##  [221]  0.921732723 -0.600179661 -0.771157862  0.772210259  0.601919480
##  [226] -0.649999625  0.926465034  0.981753696  1.258213701  1.816185055
##  [231] -0.357479547 -0.129106458 -1.337723906 -0.441698992  1.605307841
##  [236]  0.666070616  0.688584166  0.846325554  0.967629544  1.072081207
##  [241]  1.557421650 -0.446823069  0.001237786  1.048670152  1.491882046
##  [246]  1.176528410  0.477884614 -0.440459664  1.197656571  1.587716274
##  [251] -0.538730269 -0.009771875  1.153733556  0.704904126  0.804595491
##  [256]  1.695055555 -0.452286891  0.072799277  0.949390139  1.156799740
##  [261]  0.929340837 -0.531375301  0.972091984  0.901258270  1.035804030
##  [266] -0.200879967  0.456109490  0.828563853 -0.049448842  1.293423719
##  [271]  0.930338750  0.205014434 -0.691946346 -0.218912497 -0.133391411
##  [276] -0.683267830 -0.443985523 -0.729253665  0.030982183  0.898367041
##  [281]  1.229267955 -0.060628763  0.679241992 -0.310468203  0.799761057
##  [286]  1.158438331  0.358725265 -0.522273780 -0.666256368  1.348866000
##  [291]  0.771667447  1.161865054 -0.490840585 -0.364337748  0.973965527
##  [296]  2.048102422  1.286989137 -0.006354323  1.656709956  0.213689416
##  [301]  0.841322343  0.604517404  1.199682264  0.185214119  1.273179568
##  [306]  1.190024688 -0.461258997 -0.038002852 -0.586527319  1.366433290
##  [311] -1.302031654  1.170626110 -0.188711669  0.240151885  0.979384535
##  [316]  0.940416883  0.939983594  1.444416856 -0.389515714 -0.395021839
##  [321]  0.887747787  0.678256484  1.418590707 -0.384444176  0.429612095
##  [326]  0.837419018  1.025305481 -0.630986727  0.809727138 -1.036035281
##  [331]  1.273165965  0.968548198  1.215494002  0.361807516  0.480474306
##  [336]  0.592209459  0.473002629 -0.613212823  0.728729893 -0.381708639
##  [341]  0.893944521 -1.021191952  0.957186300  1.499174952 -0.787375869
##  [346]  0.785201240  1.444166529 -0.259950685  0.477400161 -0.416519259
##  [351]  1.191970499  1.373964827  1.331767457  0.598530751  1.542372249
##  [356]  0.319293518  0.559509256  2.117271012  1.489377692  1.261977665
##  [361] -0.465767213 -0.291590094  1.543499600  1.108992316  1.541775204
##  [366]  0.893567059  0.249589008  0.857369390  1.809943806  0.783469610
##  [371]  0.930976807 -0.046289524  0.891994625  1.126523151  0.608883388
##  [376]  0.437127317  1.404959208  0.955393516  1.014342790  0.890227577
##  [381] -0.414411275  0.004094097  0.686987686  1.215494002  0.478748316
##  [386]  0.590389670  1.000298114  1.478852112  1.035688917 -0.803280228
##  [391]  0.995397665 -0.116736832  0.872364666  1.407535792  2.153477236
##  [396]  1.095506394  1.273179568  0.991840194  1.754200195  1.195294749
##  [401] -0.842071007  0.135691310  1.041809447 -0.007507291  0.169833667
##  [406] -1.719551275  1.116760585  0.856313780  0.363778656  1.114678741
##  [411]  0.134311707  0.425701200  0.742295566  0.211848141  1.142016607
##  [416]  1.057413785 -0.887012739  0.642516595  0.548006336 -0.721125087
##  [421]  0.914457539  0.854449247  1.385176581  0.700265539 -0.151328484
##  [426] -0.214952545  0.819790085  0.926656209  1.214213661  0.245903042
##  [431] -0.505751429  0.337868127  1.912776023 -0.642707734  1.397547344
##  [436]  0.710879148  0.584436983  0.905442509  0.408800727 -0.247017637
##  [441]  1.500374711  1.006714403  0.872796915  1.379329072 -0.699586365
##  [446]  0.946913895  0.008166092  0.964400913 -0.959305805  1.535366871
##  [451]  0.692638424 -0.171700966  0.941545852 -0.959757157 -0.337557788
##  [456]  1.207130634  0.909089833  1.196957337 -0.888833199  0.780587947
##  [461]  0.583700854  0.983305702  0.914017544 -0.072165177 -0.302942998
##  [466] -0.348299243  0.566793393 -0.411443148 -1.462758928  0.670301072
##  [471] -0.598435771  0.983133347  0.195240958 -0.026244118  0.627587670
##  [476] -0.229690872  1.436428395  0.852579788  0.717809687  0.948861606
##  [481]  2.178754197  1.042846580  0.992965594  1.341773766 -0.359864882
##  [486]  1.518713105  1.214123344 -0.608100252  0.570352860  0.977202808
##  [491] -1.238338003  1.370592906  0.900290293  0.952089094 -0.129563857
##  [496]  1.405972667  1.050509313 -0.654012483  0.732358288  0.970239365
##  [501]  1.101017199  0.600162696 -0.817743159  1.097789026  0.430047652
##  [506]  0.779961163 -1.195976908 -0.165323217  0.898788361  1.145442284
##  [511]  0.842779900  1.006192311  1.082132425 -0.308445235  0.607317622
##  [516]  1.313606214  0.251069795  0.674953374  1.025641122 -0.402271039
##  [521] -1.570174223 -0.964162921  0.783192545  0.795989878  1.438968671
##  [526]  1.245984878  0.910820010  1.106333936  0.749309036  1.019459248
##  [531]  1.394675478  1.662344228 -0.566657109 -1.024982981  1.693564350
##  [536]  1.111240366  0.979068919  1.040016736  1.378608990  1.175886988
##  [541] -0.289693680  1.285811895  1.613386808  0.816627068  1.377888506
##  [546]  0.485295859  1.448688808  0.550194321  1.549500987  0.954281915
##  [551]  1.189665390  1.258761624  1.375478402  0.799882332  0.208583616
##  [556] -0.407900225  0.792874211  0.811519222  1.344568057  0.270162480
##  [561]  0.804692912  0.728929355  0.547798046 -0.381426902 -1.194366242
##  [566]  1.059617322 -1.201361151 -1.210922518  1.013732711  0.755481100
##  [571] -0.241721577  1.213461124  1.164954132 -0.535128204  1.496110030
##  [576] -0.176066158  0.486843165  0.126864060  1.226647626 -0.410210548
##  [581]  0.388144258  1.172397527  0.936522114  1.443482982  1.077188394
##  [586] -0.104700076  1.024094646 -0.409223295  0.620562808  1.388106968
##  [591]  0.513411312  1.517991410  0.336383381  1.511748685 -0.076388529
##  [596]  0.976650389  1.346931239  0.316146264  1.105089807  0.774348377
##  [601]  1.116562781 -0.933567481 -0.846606999  1.147486367 -0.138971929
##  [606]  0.750048609  1.092545695 -0.842799544  1.444166529  0.025768340
##  [611] -0.749652390  1.493083644  1.275509438  0.814479117  0.678146661
##  [616]  0.847581447 -0.086004104 -1.184807586  1.155786376  0.587210899
##  [621] -0.064087692  0.084394355  1.503851600 -0.173445367 -0.803280228
##  [626]  0.680793087  1.486829983  0.970635564  0.369967195  0.098563393
##  [631] -0.410100240 -0.602670958  1.189060116 -0.125587936  1.243642404
##  [636]  1.258721704 -0.340591578  1.239511102  0.444430472  1.797592102
##  [641]  0.902689199 -1.880461460  1.043607980 -0.739817003 -0.359864882
##  [646]  1.594298000  0.994096500  1.207355273  1.231934974  0.832891031
##  [651] -0.734850284  1.451102959  0.452907495 -0.187833976  1.335440840
##  [656] -1.807471784  1.089038154  1.639288395  0.773995395  0.857986581
##  [661]  0.985177407  0.428295185  0.003437403 -0.508739345  2.044508320
##  [666]  0.802130204  0.897079555  0.419024473  1.638877229 -0.357148786
##  [671]  1.508306649 -0.160890214  1.341760102 -0.595774432  0.087776278
##  [676]  0.832884929 -0.287671427  0.489678263  1.689246537  0.980562696
##  [681]  0.716367573  0.009748464  0.685502160 -0.846783075  1.607034390
##  [686]  1.876146478  0.685349821  1.004999566  0.262207939  0.398169033
##  [691]  0.816218107  0.058009200 -0.355227375  0.280076269  1.311138911
##  [696] -0.068369556  0.841920470  0.882437738  0.762476305  0.388136048
##  [701]  1.162086022 -1.156820513 -1.042468049  1.201428443  1.681834303
##  [706]  1.077529203 -0.760265893 -1.772034867  0.789089044  1.271819769
##  [711]  1.064202466  0.140317488  0.246988953  1.312430468  1.241757324
##  [716]  1.340082200  1.539480039 -0.032645536 -0.227809374  1.446023091
##  [721]  1.216875590 -0.351909355  0.928823656 -0.521269939  0.789946691
##  [726]  1.075315229  1.207130634  0.629788451  1.090892682  1.601869142
##  [731] -0.737380465  1.394645727  1.512980605 -0.795203708 -0.670077879
##  [736]  1.309000458 -0.632420666  0.884941081 -0.005430188  1.303671074
##  [741] -0.461258997  1.030553132  0.611725122 -0.705668541  1.263259547
##  [746] -1.221422176  1.220456438  0.372617072  1.246152268 -0.475355814
##  [751]  1.110500899 -0.749569690  1.446997129 -1.208486340  1.779598537
##  [756]  0.787558578  1.365994437  1.196303714  1.179718787  1.151223145
##  [761] -1.990643870  0.774557328  1.155786376 -0.558016573  0.949581934
##  [766]  1.039947977  0.852104205 -0.125963290  0.777179157  0.052261454
##  [771]  0.858216877  1.414034550  1.020890164 -1.149224742 -0.083709476
##  [776]  1.531006887  0.707973400  1.106130244  0.153896431  0.199757775
##  [781]  1.238934646 -0.296574077 -0.173445367  0.829073520  0.875902562
##  [786]  1.327578780 -0.242045294 -0.648517047 -0.164181086  1.140912572
##  [791]  0.897074053  0.970790724  0.957587081  0.797367002  1.351579078
##  [796]  0.777223172 -0.274623589  1.517214556  1.444166529  1.106589173
##  [801]  0.596163876 -1.033748383  2.049100007  0.871719216  1.459042897
##  [806]  0.406634191 -0.318894065  0.982327290  1.346255480  0.798752019
##  [811]  0.249902831  0.931691432  0.911528462  0.747263924  0.826147022
##  [816]  0.547487456 -0.106253853 -0.592880255  0.828089736  1.334716936
##  [821]  0.952754652  0.411413108  1.336938506 -0.676827130  0.999098556
##  [826]  0.421492300  1.394448091 -0.540903138  1.263916073  1.103314841
##  [831]  0.578723052  1.340163868 -0.137175162  0.132433673  1.501816886
##  [836]  1.204805553  0.723774150  1.315674722  0.898984685  1.466190609
##  [841]  0.834465359  0.909513245  1.191591961  0.373715216  0.616861034
##  [846] -0.369403577  0.384998243 -0.523116047 -0.311746826  1.127188877
##  [851] -0.406228017  0.543710387  1.465407065  1.151907237  0.711204313
##  [856]  0.810431532  0.763654947  0.591064524  0.561623486  0.057003912
##  [861]  0.490255222  0.119123715  0.866187673  1.048975533  0.543293613
##  [866]  0.965285914  1.062480776  1.169347183  1.522828590 -1.042158769
##  [871]  0.904231036  1.316308897  0.870014014  1.278010575  1.147666801
##  [876]  0.841048194  1.572134803 -0.513721030 -0.969944278 -0.283263159
##  [881]  1.039952365 -0.812902649  1.398606679  1.662364581  0.693557208
##  [886]  1.382569239  0.066981373 -0.575100024 -0.417364376  0.149321205
##  [891]  0.888581747  0.174552704  1.473351187 -0.290278066  1.127852913
##  [896]  1.286113232  1.512703182  0.942885588 -0.798712782  0.714853259
##  [901]  1.107235403  0.699919704  0.299231679  0.310415909 -0.016266953
##  [906]  1.073491540 -0.563004458  0.166637277  0.135775058  0.978204663
##  [911] -0.355466519  1.062144795  0.531765327  0.628392540  1.476100041
##  [916]  0.738445813  1.424019144 -0.787663231  1.360850851  1.075246088
##  [921]  0.923890205  0.261241390  0.785312136 -0.472522427  1.409253756
##  [926] -0.383450667  0.368437172  0.500364382  1.191385920  0.773746426
##  [931]  0.522142626  0.898327294 -0.446976445 -0.727860630  0.296137534
##  [936]  1.347442755  1.198861675  0.986215878 -0.907411309 -0.291949154
##  [941]  1.224252577  1.048771382  1.334659716 -0.257660707  0.742848505
##  [946]  1.006714403 -0.188290952 -2.187094520 -0.040932608  1.559073314
##  [951]  1.420165145  1.173763384  0.240178805  1.456439226 -0.141519518
##  [956]  0.904417446 -0.999618402  0.175289565 -0.414695414 -0.387754898
##  [961] -0.540019027  1.536069812  1.059447772  0.809421699  1.150828157
##  [966]  0.688560427 -0.315785541  0.412746007  1.401912069  0.958473130
##  [971]  1.384238269  0.131126344 -0.342203453 -0.043459854  1.107424060
##  [976]  1.186055259  1.007273406  1.026245624  0.891994625  0.544687750
##  [981]  0.903216814  0.987185592 -0.331982132  1.100693027 -0.232863519
##  [986]  0.728980084 -0.717652820  0.380998619 -0.368130277 -1.385971183
##  [991]  1.323939304 -0.221675135  0.822783534 -1.263077590 -0.955293552
##  [996] -0.026244118  1.287109118 -0.731362291  0.772479365 -0.841923820
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
##   0.70639309   0.36710576 
##  (0.11608903) (0.08208598)
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
## [1] -0.58185525 -0.08106040  0.05861303 -0.43446164 -1.13216535  0.30710888
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
## [1] -0.0064
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9167439
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
## t1*      4.5 0.06876877   0.9230002
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 4 5 6 7 8 
## 1 1 2 1 1 1 3
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
## [1] 0.0049
```

```r
se.boot
```

```
## [1] 0.8938036
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

