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
## 0 1 2 3 5 6 7 9 
## 2 1 2 1 1 1 1 1
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
## [1] 0.0047
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
## [1] 2.778048
```

```r
UL.boot
```

```
## [1] 6.231352
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
##    [1] 5.4 4.1 3.9 3.5 5.5 4.6 4.8 5.7 4.8 4.9 3.8 5.1 5.9 5.3 4.5 5.2 3.5 2.3
##   [19] 4.1 4.5 5.6 5.4 3.1 5.0 3.2 4.5 3.7 3.5 5.3 4.5 5.2 3.7 3.4 5.8 3.8 3.0
##   [37] 2.3 3.8 5.3 4.1 3.5 5.1 5.0 4.8 5.3 6.0 2.2 5.9 4.8 4.3 4.6 3.7 3.6 3.5
##   [55] 5.2 4.3 4.7 5.3 4.7 2.0 4.2 3.6 5.0 4.6 5.2 2.9 4.0 3.7 4.8 3.7 5.0 4.5
##   [73] 4.5 3.6 5.6 4.8 3.7 5.3 4.7 4.3 4.5 4.5 4.2 5.1 5.8 4.8 4.4 4.3 3.4 5.6
##   [91] 4.4 5.5 5.7 3.9 3.5 4.3 5.1 4.2 4.7 3.0 6.7 4.7 3.6 4.7 4.8 2.9 4.3 3.7
##  [109] 4.7 4.7 5.2 5.7 3.9 3.1 3.6 4.3 4.3 4.5 5.4 4.1 4.0 5.5 2.8 5.1 5.3 4.4
##  [127] 4.2 4.9 2.4 4.3 5.1 5.6 4.9 3.8 4.0 4.6 4.9 5.8 4.9 4.4 5.7 4.4 6.2 4.3
##  [145] 4.8 5.6 4.3 4.9 3.5 4.4 3.8 4.5 5.7 4.5 5.2 5.4 3.2 4.9 3.9 5.5 3.5 5.4
##  [163] 4.4 4.8 4.5 5.9 5.6 5.4 3.8 3.4 4.2 3.6 5.7 4.2 4.7 5.0 4.8 3.7 5.6 3.7
##  [181] 2.9 5.7 2.8 4.0 3.4 5.5 2.9 4.7 4.6 4.5 4.8 3.9 4.8 4.8 5.1 6.8 5.4 5.0
##  [199] 5.3 4.6 4.2 5.8 3.5 5.5 5.2 3.9 4.5 3.6 5.4 3.0 2.7 6.4 5.4 5.1 2.7 5.1
##  [217] 4.3 3.2 4.1 3.6 4.7 7.0 5.0 4.7 5.5 5.6 4.5 6.0 4.0 3.3 4.2 5.7 5.5 4.6
##  [235] 3.5 6.0 6.4 5.2 4.5 4.2 4.7 5.6 4.6 6.2 6.3 4.4 4.4 4.9 3.9 4.9 3.8 3.9
##  [253] 3.1 4.0 5.9 4.6 4.7 4.1 6.2 6.2 4.6 5.1 4.0 4.1 4.1 4.3 6.8 4.1 6.1 2.5
##  [271] 3.8 4.5 4.5 5.1 3.9 3.4 4.0 4.3 5.1 3.7 3.5 3.9 5.7 4.3 3.7 6.2 5.1 5.6
##  [289] 4.0 5.2 4.5 3.7 5.0 3.2 5.8 4.2 5.2 3.8 4.8 4.6 6.9 4.0 4.0 5.9 3.7 6.4
##  [307] 7.3 4.2 2.7 4.3 3.5 5.6 5.3 3.8 4.5 4.8 4.3 5.3 4.8 4.9 5.9 4.1 4.0 4.1
##  [325] 5.5 5.6 3.8 5.3 4.6 6.1 3.4 6.4 3.9 4.7 4.4 3.8 3.7 5.0 2.4 4.4 4.9 6.1
##  [343] 5.3 4.1 3.5 5.0 3.6 3.1 4.4 4.3 4.3 5.0 4.5 5.8 3.6 4.9 4.3 4.2 5.4 4.1
##  [361] 4.1 5.5 3.5 2.8 4.0 3.6 4.9 5.6 5.0 5.4 2.9 4.6 4.3 5.5 3.5 4.0 5.0 4.6
##  [379] 6.0 4.9 3.6 4.1 3.0 4.8 5.1 5.5 4.6 5.2 5.1 2.6 3.5 4.8 3.6 5.2 3.0 5.3
##  [397] 4.7 4.1 3.1 5.3 6.4 4.9 5.7 5.8 5.0 5.2 5.3 4.6 5.4 4.1 5.4 4.0 4.4 4.7
##  [415] 5.0 4.2 4.6 5.6 3.9 4.4 3.5 3.5 4.1 4.8 4.3 4.8 5.5 5.8 5.4 4.9 5.3 3.1
##  [433] 4.1 5.8 4.3 4.6 3.1 3.1 2.7 4.1 4.5 4.9 6.1 4.6 3.8 4.6 4.3 3.9 4.9 3.8
##  [451] 4.1 5.6 4.2 4.2 5.4 4.7 5.2 6.1 3.8 4.6 3.9 5.6 5.1 4.8 4.4 3.6 3.9 4.6
##  [469] 7.2 4.0 5.0 5.2 4.6 5.8 3.7 5.0 2.5 5.5 4.3 4.2 4.0 3.8 4.8 4.0 4.6 4.0
##  [487] 4.8 5.6 3.5 4.4 4.9 3.7 5.6 3.8 4.1 3.1 3.8 3.9 3.2 3.7 3.2 4.6 5.5 5.3
##  [505] 6.0 4.1 4.4 3.2 4.1 3.9 4.4 4.0 4.6 4.1 5.3 4.1 3.8 5.2 5.7 4.5 5.4 6.0
##  [523] 4.6 4.5 5.0 4.0 6.0 5.4 6.1 4.6 4.3 5.4 2.9 4.0 4.4 4.9 4.2 5.2 5.6 5.0
##  [541] 2.8 2.9 5.8 4.1 4.1 4.3 3.3 3.8 5.0 2.9 3.6 3.8 4.1 5.0 4.4 5.6 4.5 4.4
##  [559] 4.8 4.3 3.5 6.6 4.3 5.5 4.1 5.0 3.7 5.8 3.1 4.9 5.5 4.9 3.8 5.0 3.6 3.6
##  [577] 5.4 5.3 5.6 4.4 3.8 5.2 4.1 3.9 4.2 3.7 5.2 5.3 3.8 4.2 3.0 5.2 4.6 4.5
##  [595] 4.1 4.1 4.8 4.1 4.4 6.4 4.3 5.7 4.3 5.4 4.7 5.4 4.3 4.5 3.9 5.6 4.5 4.9
##  [613] 4.2 3.7 3.8 4.5 3.6 4.4 3.1 5.1 3.9 4.8 4.5 5.2 3.9 4.7 4.7 4.0 3.1 4.8
##  [631] 6.0 6.6 5.2 5.8 4.3 5.4 4.4 4.6 3.4 2.7 4.6 5.1 4.1 4.8 5.0 4.8 3.4 4.4
##  [649] 4.9 5.0 3.4 5.4 4.5 3.6 3.2 3.5 5.7 4.4 3.6 3.3 5.2 6.6 4.8 5.2 5.4 4.5
##  [667] 4.6 5.6 4.4 4.4 5.1 4.1 3.7 5.1 3.7 6.3 3.2 6.5 4.5 5.1 6.9 4.6 4.8 6.4
##  [685] 3.5 5.9 3.8 4.2 4.0 4.1 4.1 5.4 5.0 5.4 4.7 4.6 5.6 3.0 5.6 3.9 4.3 2.4
##  [703] 5.2 4.8 4.9 3.7 3.7 5.9 5.8 3.1 4.2 4.4 5.2 4.3 5.5 3.4 5.2 4.6 5.3 5.0
##  [721] 4.5 5.5 4.6 4.2 4.0 5.0 4.9 5.2 5.1 4.3 4.3 1.6 3.9 5.3 5.0 5.5 4.7 4.5
##  [739] 4.7 6.1 4.5 2.5 2.8 3.9 3.5 3.1 5.5 3.9 3.7 4.9 5.1 3.9 5.2 3.8 3.8 4.7
##  [757] 4.2 6.6 3.8 5.7 6.0 4.7 4.2 5.0 4.2 4.4 5.3 5.6 3.2 3.6 5.0 4.6 5.2 5.0
##  [775] 5.1 3.7 3.7 6.1 2.8 6.7 5.0 3.5 4.8 4.0 5.3 5.6 2.9 5.8 4.2 4.2 4.0 4.2
##  [793] 4.7 5.9 5.4 4.7 4.3 4.9 5.8 5.6 5.7 3.2 4.7 5.6 4.4 3.6 3.7 3.3 3.2 3.6
##  [811] 4.2 4.6 4.6 5.2 4.7 5.0 3.8 4.1 4.4 4.6 4.5 6.0 4.4 4.8 4.4 4.9 4.6 5.8
##  [829] 3.8 2.6 5.5 4.2 4.2 4.5 4.5 5.4 4.9 2.6 3.6 4.9 5.6 3.2 3.0 3.6 4.5 6.1
##  [847] 3.9 3.3 3.8 3.7 3.9 4.2 4.9 3.2 4.6 3.2 4.1 6.5 4.8 4.5 5.0 4.1 5.7 6.2
##  [865] 5.3 3.6 3.4 3.7 6.4 4.8 6.0 3.2 5.2 5.4 4.0 3.8 4.6 3.5 5.2 3.4 4.3 5.8
##  [883] 4.1 4.3 5.2 5.9 3.3 3.7 4.4 3.4 5.6 3.8 3.6 4.7 3.7 3.3 4.5 4.0 4.4 4.8
##  [901] 5.3 4.5 4.8 5.0 4.4 4.8 4.5 4.9 3.6 5.5 5.7 4.2 4.4 3.3 2.3 5.4 4.8 4.1
##  [919] 5.1 4.6 5.0 5.0 3.7 4.8 4.6 3.5 4.0 4.5 6.0 4.8 4.3 5.2 5.5 2.9 3.9 5.6
##  [937] 4.5 4.9 4.9 4.9 5.7 3.3 4.4 3.9 4.9 4.2 4.1 3.9 4.8 6.0 2.3 4.9 4.6 3.7
##  [955] 3.9 4.6 4.8 4.2 5.2 5.8 4.6 4.9 4.6 4.3 3.7 5.7 4.9 4.3 5.0 4.7 4.3 1.9
##  [973] 2.5 4.2 5.1 3.8 3.7 4.0 5.0 3.8 5.4 4.5 3.6 4.0 3.5 3.1 4.8 3.6 5.1 3.5
##  [991] 4.5 3.6 4.9 5.7 5.7 4.4 4.0 3.7 5.2 3.2
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
## 2.8000 6.2025
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
##    [1] 5.3 4.9 4.7 6.0 4.4 4.7 5.4 4.4 4.9 4.6 4.4 4.8 4.1 5.7 3.3 4.9 3.6 5.7
##   [19] 4.9 4.5 5.6 3.5 7.3 4.4 4.7 5.4 4.5 2.9 5.2 5.2 5.9 4.0 5.7 4.2 3.7 3.5
##   [37] 5.6 4.1 4.4 5.3 4.0 5.2 4.6 5.9 4.7 4.3 4.7 3.5 3.8 5.2 6.6 4.5 5.3 6.8
##   [55] 3.8 4.1 4.9 5.1 5.1 4.4 3.3 4.5 6.2 3.8 4.9 4.0 3.2 3.9 4.0 4.4 4.6 5.7
##   [73] 4.8 3.9 5.5 5.3 5.0 2.6 2.7 4.3 4.9 5.1 5.4 4.6 4.5 3.4 3.6 3.8 5.0 3.6
##   [91] 5.1 5.6 3.7 4.6 3.4 3.2 4.5 6.4 6.1 4.1 5.4 4.3 5.0 4.5 3.7 2.8 4.2 3.5
##  [109] 4.6 5.6 3.7 4.6 6.1 4.0 4.6 4.4 4.5 4.8 4.4 4.0 4.5 3.5 3.8 4.8 4.6 4.0
##  [127] 4.7 4.6 4.8 6.1 5.5 4.2 4.3 4.2 4.5 5.4 5.3 4.8 5.0 2.8 4.5 3.9 4.0 2.9
##  [145] 3.6 3.9 3.4 4.6 4.6 5.8 3.3 4.9 5.2 2.2 2.9 5.0 5.6 5.2 3.3 4.7 4.5 4.6
##  [163] 4.1 4.6 4.7 5.2 4.3 4.4 5.2 2.4 3.3 4.5 4.5 4.6 4.2 4.6 6.7 4.8 4.4 6.4
##  [181] 3.2 3.3 5.4 4.7 5.5 5.7 4.7 5.1 5.4 4.8 4.0 4.2 5.1 3.1 4.1 4.3 3.6 5.2
##  [199] 2.4 3.2 4.1 3.3 5.2 3.8 3.4 4.3 4.6 3.4 4.1 4.5 4.0 5.4 5.3 5.9 5.2 5.6
##  [217] 4.4 3.1 3.9 4.5 6.2 3.9 3.2 6.3 3.3 4.8 4.7 3.3 4.1 4.9 4.3 3.4 4.2 4.8
##  [235] 4.6 5.2 4.5 6.0 3.1 4.6 5.7 4.4 5.1 4.8 3.8 2.8 4.9 5.4 4.7 3.8 3.4 3.3
##  [253] 3.0 4.5 4.3 6.6 5.3 6.0 6.1 5.2 3.5 4.9 4.6 3.9 4.5 4.9 2.6 5.7 4.5 5.0
##  [271] 5.7 3.4 3.8 2.5 4.0 5.1 3.6 4.0 3.2 2.9 4.9 5.4 4.3 4.4 4.9 4.1 5.2 5.2
##  [289] 4.3 4.9 3.4 4.3 3.2 4.7 4.4 5.1 4.7 3.7 4.6 3.8 5.0 3.1 4.1 2.5 4.3 6.2
##  [307] 4.0 5.0 5.4 3.8 5.1 5.1 4.2 4.9 3.9 5.4 4.7 5.7 3.8 5.0 5.2 4.7 4.6 4.3
##  [325] 2.6 3.3 4.2 3.3 4.9 4.8 4.0 5.4 4.4 5.5 4.4 4.0 3.9 4.3 5.7 4.7 4.1 3.9
##  [343] 4.3 4.5 5.5 5.7 5.0 5.2 4.7 4.0 4.5 3.2 5.4 5.2 3.9 4.3 3.3 5.6 3.7 3.7
##  [361] 6.0 4.8 3.9 4.3 5.2 4.4 4.4 5.4 3.3 4.0 5.7 4.7 4.8 4.6 5.0 4.1 5.3 4.6
##  [379] 6.3 3.8 4.9 5.2 5.4 4.5 5.6 5.3 4.8 1.3 5.6 4.4 5.0 4.6 4.4 4.3 4.7 5.0
##  [397] 4.9 4.9 4.9 3.8 4.9 4.5 4.9 4.5 4.6 4.6 5.2 5.1 4.5 4.3 4.1 5.6 4.3 4.6
##  [415] 5.5 3.5 2.0 5.4 4.8 6.5 4.3 2.6 2.9 3.6 3.7 3.9 5.0 5.1 5.4 4.5 3.9 5.7
##  [433] 3.5 4.9 4.2 4.4 4.2 4.3 4.1 4.0 3.9 4.3 5.3 5.7 5.2 5.3 3.9 4.6 4.2 5.0
##  [451] 5.0 5.5 5.2 4.6 3.8 4.5 6.1 5.4 4.5 4.7 4.6 4.1 4.5 4.6 2.6 5.8 4.1 5.4
##  [469] 4.4 5.0 2.5 4.6 4.3 3.9 5.1 2.5 3.1 4.7 5.8 6.5 3.7 4.3 4.6 6.0 3.7 3.1
##  [487] 4.2 4.6 4.1 4.9 4.1 4.9 3.7 3.1 5.6 5.4 3.8 4.2 4.8 3.3 4.1 3.8 6.0 3.8
##  [505] 5.1 3.6 4.0 3.3 4.7 4.4 3.6 3.7 4.5 5.7 6.0 2.8 5.8 4.5 6.6 4.0 5.2 5.3
##  [523] 4.2 4.3 3.6 5.9 5.2 4.2 4.2 5.0 3.5 4.9 4.3 5.3 5.5 4.0 5.4 3.6 4.9 4.3
##  [541] 4.4 5.4 3.3 3.2 4.7 4.7 4.9 4.8 3.7 6.5 3.8 5.1 4.6 5.2 4.3 4.8 6.5 3.3
##  [559] 2.7 3.3 5.8 3.3 5.6 5.1 3.2 3.9 3.4 5.4 4.6 4.5 6.1 6.0 5.0 4.6 3.8 4.4
##  [577] 4.1 4.3 4.2 7.0 5.1 2.5 3.8 4.8 3.5 6.0 3.1 4.8 4.2 4.4 3.8 4.7 4.9 2.2
##  [595] 3.7 4.0 5.6 5.1 3.8 2.9 5.3 4.8 3.4 3.7 3.7 3.7 2.2 6.1 3.7 3.9 1.7 5.8
##  [613] 4.6 4.6 4.4 5.1 2.7 4.5 6.0 4.1 3.4 3.0 5.3 3.4 4.6 3.5 4.2 5.0 5.0 5.1
##  [631] 4.7 4.9 4.3 3.5 5.4 4.6 3.9 4.4 4.5 4.9 5.0 4.2 6.2 3.8 4.8 3.2 3.7 6.9
##  [649] 5.8 4.7 5.7 3.9 4.2 4.3 4.2 6.5 4.4 4.1 4.4 4.8 4.7 4.7 5.0 3.3 3.6 3.8
##  [667] 4.2 3.1 5.4 3.8 5.8 3.5 6.7 6.1 2.4 3.2 5.7 3.6 4.8 4.8 4.4 4.7 4.2 4.0
##  [685] 3.2 3.6 4.2 3.2 6.6 4.2 5.7 4.7 3.5 5.1 5.0 3.9 3.6 4.1 3.8 3.6 4.1 4.2
##  [703] 4.7 3.5 4.5 4.1 4.5 4.5 3.9 4.7 4.9 3.3 4.4 4.5 3.3 4.2 3.4 4.6 5.6 3.9
##  [721] 4.4 3.5 4.8 4.0 3.5 3.8 5.7 4.7 3.4 4.4 4.5 3.4 4.5 4.3 4.7 4.1 4.8 4.7
##  [739] 3.3 4.7 5.0 5.7 5.4 4.8 3.7 4.0 6.0 4.7 4.6 5.0 4.9 4.9 5.0 4.5 3.4 3.3
##  [757] 5.2 5.0 3.8 5.3 5.6 3.9 3.6 4.2 4.2 4.8 5.2 4.3 3.9 5.9 5.3 5.4 4.3 4.4
##  [775] 4.5 5.5 4.6 5.3 4.0 5.4 4.8 4.0 5.2 5.2 5.2 4.1 4.3 3.5 3.2 4.2 4.1 5.0
##  [793] 5.5 4.7 4.9 3.2 3.4 4.9 4.7 4.0 4.4 4.8 4.8 4.2 3.7 4.0 5.7 4.2 3.7 5.2
##  [811] 2.9 4.2 5.5 4.8 5.1 4.4 3.2 3.3 4.5 5.5 4.3 6.2 5.0 5.1 4.7 3.5 5.2 4.8
##  [829] 3.6 5.7 6.2 2.8 3.7 3.8 3.7 4.6 3.3 2.6 4.1 5.1 5.8 4.1 5.5 4.1 4.2 5.3
##  [847] 5.9 5.6 5.1 5.0 4.2 4.1 3.4 3.9 5.2 4.6 3.4 4.7 5.3 3.5 4.6 3.9 6.0 4.3
##  [865] 3.6 5.4 4.2 5.7 5.9 5.4 4.2 4.5 3.9 5.3 2.7 6.3 4.4 4.8 2.3 4.5 4.6 4.7
##  [883] 4.6 4.5 5.5 5.2 4.4 5.5 4.5 3.6 4.3 2.8 3.6 5.9 5.0 5.8 4.8 3.6 4.9 4.9
##  [901] 4.4 3.1 4.5 5.3 3.8 5.1 3.0 3.4 4.4 4.0 5.0 4.8 6.8 3.4 4.3 3.1 4.3 4.4
##  [919] 2.9 3.0 5.1 5.0 4.7 4.6 4.3 4.9 6.3 4.5 4.5 3.2 4.2 3.3 4.5 5.4 4.6 5.7
##  [937] 2.7 3.2 3.9 5.8 5.5 4.3 5.5 3.0 3.4 2.3 5.1 3.8 4.2 3.5 5.3 3.8 4.2 5.4
##  [955] 4.5 3.4 4.2 4.9 5.2 5.1 4.3 5.7 5.1 5.4 5.9 4.5 5.0 4.0 5.3 5.2 4.9 4.5
##  [973] 4.2 5.3 4.2 4.6 3.7 4.5 4.4 4.4 4.8 5.4 3.2 6.4 3.9 6.4 6.4 6.1 4.0 3.8
##  [991] 3.6 3.7 4.2 4.1 4.1 5.3 5.1 4.7 3.0 4.3
## 
## $func.thetastar
## [1] -0.0085
## 
## $jack.boot.val
##  [1]  0.46381766  0.35625000  0.18409091  0.20823171  0.03994638 -0.03536232
##  [7] -0.20000000 -0.26224784 -0.43284024 -0.37514793
## 
## $jack.boot.se
## [1] 0.8791146
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
##    [1] 4.8 7.1 3.1 5.7 5.2 3.8 4.5 5.4 4.2 3.4 4.3 3.7 5.5 3.7 4.6 4.8 4.9 6.5
##   [19] 5.1 5.2 5.9 5.5 2.9 5.3 3.3 4.8 2.9 4.1 5.5 5.9 5.0 5.4 3.3 4.0 4.8 4.8
##   [37] 3.8 5.4 4.3 4.7 5.0 3.5 3.4 4.4 4.0 4.1 4.3 3.7 3.4 4.4 5.2 4.6 4.7 5.8
##   [55] 5.2 3.8 5.6 5.0 5.7 4.5 4.7 3.6 5.8 4.6 4.7 3.0 5.7 4.5 5.0 4.2 4.0 3.8
##   [73] 4.4 4.1 5.4 4.4 5.4 5.0 5.5 4.5 4.2 5.3 4.5 4.5 4.3 4.9 2.3 5.3 3.1 5.8
##   [91] 4.8 3.7 4.2 2.9 6.3 5.3 3.5 3.9 4.9 2.9 4.6 5.1 4.7 3.4 3.6 4.4 4.4 4.8
##  [109] 3.0 5.5 2.5 4.2 4.7 4.9 2.8 4.7 4.8 4.7 4.7 4.0 4.5 4.0 3.6 3.8 4.3 6.1
##  [127] 5.4 3.7 4.0 5.2 5.8 3.3 5.3 3.5 4.7 4.8 3.5 3.6 4.9 4.3 5.0 4.0 3.5 5.6
##  [145] 5.8 6.5 3.3 3.2 5.8 3.6 4.7 5.0 5.7 4.8 4.2 4.3 4.2 4.2 4.4 6.4 4.9 3.7
##  [163] 6.1 6.4 4.9 2.8 3.5 3.1 5.6 4.7 3.5 3.8 5.9 4.5 4.8 3.4 5.7 3.4 3.4 5.1
##  [181] 4.9 3.5 3.4 3.9 4.5 6.5 4.3 5.6 5.0 5.4 4.2 4.2 6.7 4.5 5.0 4.9 5.2 5.3
##  [199] 3.9 3.1 4.8 4.6 4.5 5.0 3.3 4.4 1.9 4.7 3.7 4.1 3.6 5.3 6.0 6.0 4.5 4.9
##  [217] 4.2 6.0 4.0 4.4 4.4 5.7 5.1 3.7 5.4 3.2 3.4 3.9 5.0 4.3 5.6 2.4 4.2 5.5
##  [235] 3.0 4.8 5.5 3.3 4.5 2.8 5.7 4.5 4.9 4.2 5.7 3.3 3.1 4.9 4.4 3.8 4.3 5.0
##  [253] 4.7 3.0 4.4 4.5 5.7 4.3 5.8 5.3 5.1 3.2 4.1 6.4 3.4 4.7 4.1 4.1 5.1 4.6
##  [271] 4.3 4.2 5.1 3.7 5.1 3.9 6.0 3.5 3.3 3.8 3.9 4.5 4.5 4.3 5.3 4.5 5.0 3.9
##  [289] 6.6 4.5 4.9 3.2 3.8 4.2 4.2 3.9 4.8 5.6 4.6 4.8 5.9 4.2 3.9 3.6 4.5 5.3
##  [307] 4.6 4.3 4.4 3.5 3.7 3.3 3.8 4.5 4.9 3.3 4.2 3.9 6.5 3.5 4.0 4.7 5.7 5.2
##  [325] 3.5 4.7 3.8 4.2 4.9 4.3 6.1 4.7 2.9 4.1 3.7 4.7 4.2 5.0 3.6 4.3 5.9 4.0
##  [343] 3.9 5.5 3.3 4.9 4.4 5.2 4.7 4.7 4.9 5.6 5.2 4.0 5.2 5.1 4.7 4.4 4.2 4.3
##  [361] 5.0 3.5 4.8 4.6 4.8 4.4 5.5 5.8 3.0 4.9 4.3 4.7 3.6 6.5 4.1 4.8 3.7 4.7
##  [379] 4.3 3.1 5.2 4.6 5.1 4.9 4.1 4.3 4.4 4.3 4.6 4.5 4.1 4.9 4.2 5.3 5.4 3.6
##  [397] 4.7 3.4 4.3 4.2 3.9 4.5 4.8 5.3 5.0 4.5 4.0 5.6 3.6 4.1 5.0 4.6 3.6 3.1
##  [415] 4.4 4.4 5.4 4.8 4.1 4.9 4.5 5.4 5.5 3.6 4.6 4.7 5.2 4.4 3.8 4.1 4.3 6.2
##  [433] 5.4 6.6 4.7 4.0 5.5 3.9 4.5 5.1 4.4 4.7 4.9 5.1 5.2 5.4 4.2 3.1 4.9 5.0
##  [451] 4.5 5.8 4.5 3.3 4.6 6.5 3.6 3.4 4.7 4.3 4.9 5.8 5.8 3.1 3.5 4.9 3.8 5.0
##  [469] 4.6 4.8 5.3 4.6 5.1 4.6 5.1 3.4 3.9 5.5 4.0 6.0 4.0 4.0 6.2 5.1 3.9 5.3
##  [487] 3.4 5.3 4.4 4.4 4.0 4.3 5.6 3.7 5.1 5.0 5.1 4.2 3.2 4.1 5.9 5.5 2.8 3.6
##  [505] 2.6 4.4 5.0 4.9 4.9 4.2 4.8 4.8 5.4 4.5 3.5 2.9 4.9 3.9 6.3 4.4 4.7 4.3
##  [523] 3.7 5.7 4.1 3.6 5.6 4.0 4.9 4.2 3.8 4.8 4.3 3.2 5.8 4.6 4.9 3.7 3.9 4.3
##  [541] 5.6 5.0 4.8 5.7 3.9 4.3 4.8 3.9 3.3 5.6 4.2 2.2 5.5 3.5 3.5 2.9 3.6 4.2
##  [559] 4.3 3.5 4.8 5.6 3.3 4.9 3.8 4.0 4.3 5.0 3.9 5.8 5.9 3.3 6.0 5.2 5.2 4.0
##  [577] 5.0 4.7 3.6 4.2 5.4 5.4 3.4 3.6 4.6 2.8 4.7 5.6 3.8 6.6 5.0 3.3 4.4 6.4
##  [595] 3.4 3.1 5.1 5.1 5.8 5.2 4.3 4.3 3.5 6.5 3.5 5.3 4.4 5.0 6.9 4.4 3.5 6.5
##  [613] 4.9 4.8 4.5 6.8 5.9 4.9 4.2 4.9 5.5 4.8 4.6 5.7 4.7 4.8 6.5 4.3 4.7 4.1
##  [631] 3.7 4.6 4.2 5.6 5.3 4.5 5.4 4.2 4.6 6.1 3.0 5.3 3.8 3.8 5.4 5.5 4.9 4.8
##  [649] 4.2 5.8 3.9 3.2 4.3 5.5 4.8 5.4 3.1 4.4 4.1 5.6 3.5 3.4 4.5 4.5 4.4 3.8
##  [667] 3.5 4.9 4.9 2.8 5.0 4.1 2.6 3.4 4.8 4.8 4.7 5.1 3.4 4.5 4.9 4.3 4.6 4.6
##  [685] 3.4 3.4 6.6 5.6 4.6 4.0 5.0 6.2 5.7 4.6 4.6 4.8 5.4 5.8 4.6 3.9 4.4 2.9
##  [703] 4.9 4.4 3.6 4.3 5.0 4.2 4.8 2.7 5.5 5.4 5.1 4.0 4.4 5.6 4.9 6.1 4.3 3.8
##  [721] 4.1 3.7 4.7 3.9 4.4 4.0 4.9 4.6 5.4 3.2 4.0 3.7 3.3 4.7 5.6 5.1 3.1 3.9
##  [739] 5.3 4.0 5.1 6.1 4.4 2.3 3.6 3.8 3.2 3.6 5.4 5.7 5.2 2.7 5.0 3.8 5.2 4.3
##  [757] 5.6 4.7 2.8 4.4 4.0 4.4 4.6 5.8 3.5 4.0 4.6 4.9 6.2 4.1 3.9 3.5 4.8 3.8
##  [775] 4.5 4.8 5.4 4.0 4.9 3.3 3.4 4.9 4.6 3.2 3.3 4.0 6.5 5.5 4.2 3.3 4.4 3.8
##  [793] 5.3 4.0 4.0 5.0 5.6 4.2 4.5 4.1 3.4 5.9 5.9 4.7 5.0 5.4 6.2 3.0 3.4 5.8
##  [811] 3.9 4.6 4.9 4.1 5.1 3.9 3.4 5.2 5.9 6.6 3.6 3.5 5.1 3.8 4.8 3.8 4.1 4.1
##  [829] 1.8 4.7 3.4 3.5 3.2 5.4 3.5 4.0 4.4 4.5 4.3 2.9 4.4 4.8 5.4 4.5 4.6 6.0
##  [847] 4.8 4.0 3.9 4.7 5.7 3.5 3.9 4.2 4.7 3.7 5.6 3.0 4.6 3.4 4.6 4.4 4.6 3.6
##  [865] 4.8 5.0 4.6 5.3 4.7 4.3 4.0 3.4 5.8 3.2 2.9 4.6 4.9 4.0 4.9 4.3 4.5 5.0
##  [883] 4.6 4.6 4.8 4.8 5.2 4.1 4.4 4.9 4.1 5.5 4.0 7.0 5.6 5.3 4.0 4.0 4.4 5.7
##  [901] 4.6 4.3 4.1 3.7 4.3 4.0 5.1 4.8 4.0 4.6 4.9 4.3 4.4 4.6 4.1 3.7 3.0 4.6
##  [919] 4.5 4.1 3.0 5.3 5.0 3.1 4.5 3.8 4.5 2.9 4.5 4.6 5.0 4.5 5.2 5.0 5.5 5.6
##  [937] 4.5 4.7 4.0 4.1 4.9 3.9 5.1 3.9 3.7 4.6 5.8 4.1 3.5 4.2 4.0 4.3 5.6 4.4
##  [955] 4.6 1.7 4.5 5.1 3.9 5.8 3.1 3.9 4.4 5.0 5.7 4.9 5.4 5.7 5.1 4.3 3.9 3.1
##  [973] 3.6 5.0 4.4 5.1 4.6 4.8 4.2 3.6 4.2 7.0 5.0 5.6 5.1 2.7 3.3 3.2 4.0 4.0
##  [991] 4.5 4.8 3.5 6.0 3.3 4.7 2.6 5.1 6.2 6.0
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.4 5.4 5.4 5.1 5.1 4.9 4.9 4.8 4.7 4.4
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
## [1] 0.9722232
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
##    6.255234   13.385076 
##  ( 2.726089) ( 6.073879)
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
## [1]  0.3363515  0.2971515  0.5262113 -0.6282595  0.2633687  0.1920948
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
##    [1]  1.743633403  1.126709131  0.874914648  1.381921085  0.692958971
##    [6]  0.133879053  0.751902985  0.456686569  1.193401483  1.296711405
##   [11]  1.476512473  0.198149082  2.464796145  0.392130835  1.311609698
##   [16] -0.077147009  1.822492088  0.194470169 -0.421964944  0.831194557
##   [21]  0.571001040  1.885988102  0.610130291  1.451373178  1.011744783
##   [26]  0.450369212  0.887103973  2.510517691  0.384244997 -0.028393996
##   [31]  1.187014328 -0.016183101 -0.381644883  0.877420854  0.792430829
##   [36]  0.062879656  0.678506300 -0.359665978  2.037138533  0.351430915
##   [41]  1.136159586  0.579651806  0.489401178  0.242961257  1.312258644
##   [46]  1.561215587  1.083137048 -2.106077934  1.398170406  0.240720247
##   [51]  0.461098401  1.581177394 -0.454634284  0.323234003 -0.076796708
##   [56]  0.310333799  0.368839733  1.448937326  1.319925472  0.350874005
##   [61]  0.442502422  0.794394471 -1.942696156  1.059763168  0.861709755
##   [66]  0.047483747  0.124250687  1.291680234  0.488264463  0.891350464
##   [71]  0.282073351  0.323871576  0.722382668  1.864946488  0.768691175
##   [76]  0.269580360  1.395276161  0.251034421  1.979150726  1.219196380
##   [81]  0.830514716  2.568230572  0.190577983  0.831489024  1.957956415
##   [86] -0.185390213  1.188699187  1.219859774  1.294700379  1.804361579
##   [91]  1.400568514  0.978147041  1.000340380  1.441593889  0.111396836
##   [96]  0.852103187  1.273740558  1.233214654  0.354152581  0.318500168
##  [101]  1.019853495  0.228619331  0.646180570  0.208521290  0.222426566
##  [106]  1.047751126  0.459823380  1.780897589  0.741958762  1.400545624
##  [111]  1.492675200  0.080629986  0.880642593  0.614345933  0.630789177
##  [116]  0.192794134  0.526190697  0.660102713  0.714736931  2.569773820
##  [121]  0.880770376  0.674120606  1.350719528  1.378344499  0.751570370
##  [126]  0.408493340  0.686269324 -0.095062256  1.416479776  1.432714028
##  [131] -0.331865333  1.276732492  0.263016811  0.561574056  0.764657413
##  [136]  0.836696071  0.939934718  0.884735195  0.262732852  0.686053879
##  [141]  0.146698053  1.377680102  0.032316238  1.433278731 -0.294751296
##  [146]  1.229807683  0.379001727  2.511379684  1.413454596  0.846126376
##  [151] -1.196618561  0.826877206 -0.378496557  0.558558510  1.345937055
##  [156]  1.173137905  1.155208833  0.847835582  0.127305184  0.871246126
##  [161]  0.695043004  0.950062422  0.371498829  0.518312765  0.562713879
##  [166]  1.052411332  0.959835604  0.598177350  0.305057500  0.467337589
##  [171]  0.660575624  0.773034124  0.389263791  1.417148750  0.642647108
##  [176]  0.465931187  0.273746644  0.470479516  1.220212143  1.421681911
##  [181]  1.449807615  0.112966471  1.119465300  0.297053069 -0.160381347
##  [186]  0.509831017  0.252117249  0.318025729  1.080735474  1.359615701
##  [191]  0.265932946  0.904675459  0.321625669  1.460100676  0.611936386
##  [196]  0.639904287  0.380041140  0.957436813  1.103234372  0.892444344
##  [201]  0.624855888  0.268280078  0.900645752  0.367559878  2.598297655
##  [206]  0.617341767  0.564398304  0.867219879  1.689230795  0.863477683
##  [211]  0.831187171  0.921155104  0.325749346  0.429207731  0.842642757
##  [216]  0.828694286  0.929155597  0.622601305  0.829353728  1.515372818
##  [221]  1.018456025  0.941172275  1.434630953  1.010556253  0.626260242
##  [226]  0.843504458  0.540445930 -0.732552765  1.448576430  0.119604584
##  [231]  0.238590036  0.575374898  1.424462047  0.879724780 -0.871868259
##  [236]  0.619930529  0.253878538  0.463744680 -0.136506833  0.703899817
##  [241]  1.666909216  0.638442007  0.449234871  1.338429771  0.760594096
##  [246]  0.530815470  0.560460622  1.229779328  0.999582217  1.068709389
##  [251]  0.872718121  1.286035396  0.866119590  1.990776744  0.847031702
##  [256]  0.988380170  1.424261171  0.182182512  1.413729804 -0.681330252
##  [261]  1.441415472  1.413908282  0.792294427  0.683151595  0.766036062
##  [266]  0.687786495  0.686063056  0.913029447  0.192664320  0.806212867
##  [271]  1.437910481  1.383882891  0.545851286  1.357909531  1.736247232
##  [276]  1.387295168  1.378435574  0.993286170  1.434328755  0.822875347
##  [281]  0.236610490  0.764657413  0.882741988  0.901812153  1.195801391
##  [286]  0.666037191  0.673344963  0.434819768  0.877373855  0.612540363
##  [291]  0.526699778  1.298538164  0.333705807  0.560512397  0.652550765
##  [296]  0.604694947  0.533534079  0.160755400  1.751119612  1.349865276
##  [301]  0.507386711 -0.066313089  0.690447595  0.464604779  1.130145028
##  [306]  0.331843893  1.422562882  0.645323962  1.435005039  0.703208203
##  [311]  0.578065679  1.339921351  0.771570160  0.134616735  1.258328810
##  [316]  1.036192272  0.031607826  0.515602202  0.994648919  1.380663723
##  [321]  0.328953765  0.681613562  0.300431347  0.634316840  0.334141657
##  [326]  1.040676864  0.012739311  0.428315087  0.591015221  0.874377981
##  [331]  0.823862738  0.898515360  0.197556444  1.081221248  0.898759398
##  [336]  0.228784807  1.387616704  0.665869542  1.481159063  1.750846174
##  [341]  0.697052979  1.323724473  1.365562072  1.009239287  0.652714027
##  [346]  1.832352377  0.354819349  0.835261226  0.754210638  0.842036791
##  [351] -0.041320100  0.427286422  0.420828920  0.025411592  1.340669182
##  [356]  2.353793644  0.438024948  1.002414327  0.796799462  0.015510253
##  [361] -0.066570020  0.178633405  0.792410740  0.672238178  1.976754439
##  [366]  0.607825895 -0.629056037  0.481392978  0.669026399 -0.146153389
##  [371]  1.265343357  0.691291500  1.394851049  0.743498754  0.686169988
##  [376]  0.134262437  0.843872779  0.061596376  0.785559576  1.090305073
##  [381]  0.951621237  0.889341926  0.413152719  0.453428116  0.351175060
##  [386]  0.822764500  0.385897456  1.356117139  0.641769156  0.732684561
##  [391]  1.841971484  0.578717537  1.424462047  1.474744002  0.959643209
##  [396]  0.284216308  0.930589292  0.401212785  1.312382668  0.775076182
##  [401]  0.421641821  1.328729588  1.070580120  2.012764162  0.414663885
##  [406]  0.651802798  0.107731915  0.473721167  0.085037582  0.328953765
##  [411]  0.898152092  0.468500288  0.394219891  0.793769643  0.526182841
##  [416] -0.652395364  0.904260172  0.757193901  1.302485888  0.412708008
##  [421] -0.186670841  0.576547088  1.116969026  0.559073104  0.826870872
##  [426]  1.224778598 -0.086916997  0.228765329  1.826618339  0.198823435
##  [431]  0.610877218  1.468490205  0.407231787  0.792722280  0.502543543
##  [436]  1.971130801  0.428868201  0.302656620 -0.028676412  0.306981612
##  [441]  1.398338115  0.472485289  1.360098036  0.339320343  0.596979471
##  [446]  0.656915913  0.654319640  1.459056054  1.959438147  1.062265462
##  [451]  0.511864244  0.288128341  0.205222055  1.223217268  0.135616141
##  [456]  0.537147947  1.042753086  0.431258178  2.391330877  0.829860152
##  [461]  0.530051925  1.369222179  0.810675839  0.321766709 -0.335640904
##  [466]  0.720238621  0.927917450  0.499319859  0.993632215  0.991523747
##  [471]  0.944512032  0.932076278  0.412880738  1.280449717  1.428789663
##  [476]  0.945548754  0.243278104  0.335191999  1.043691019  0.259766531
##  [481]  0.542455605  0.376908487  0.847558575 -0.053015066  0.857916517
##  [486]  0.617929774  0.423047559  0.187559774  0.636792681  2.401962034
##  [491] -1.090887302  2.584909141  1.031309137  1.299848705  1.250207596
##  [496]  1.969699047  1.386355478  1.001403860  1.258334828  0.540563857
##  [501]  0.525062871  1.347185072  1.324451303  1.939886201 -0.273842276
##  [506]  0.583331635  0.191112306  1.100543659  0.802562213  2.370037848
##  [511]  0.210681716  0.148614875  0.459268949  1.958545687  0.852910297
##  [516]  0.891828977  0.573742914  0.523606323  0.164549605  1.193757563
##  [521]  1.458750704  1.713078825  1.458930247  1.912926615  0.512045934
##  [526]  0.831187171  0.144953483  0.955174414  0.991916618  0.277273291
##  [531]  0.369477771  1.305105142  1.194363445  2.352243530  0.315098593
##  [536]  1.228005583  1.339183295  0.982385787  0.810735772  0.892540589
##  [541]  1.099046157  0.527765629  0.286175099  0.598523947  0.571812861
##  [546]  0.914032874  0.786476156  0.616698306  0.347925638  1.207084375
##  [551]  1.098610041  0.809059354  0.874699980  0.392689986  0.403890525
##  [556]  1.421131321  1.071389659  2.541158029  0.793987134  1.214841066
##  [561]  1.465319430  0.160269810  0.246426271  0.905780282  0.635394061
##  [566]  0.427782346  0.599056003  0.292275052  0.180026421  0.840064974
##  [571]  0.944082024  0.417684663  0.663226537  0.829090498  0.458231979
##  [576] -0.025571580  1.370691820  0.359182567  0.726043238  0.279298148
##  [581]  1.181061788  0.301339484 -1.279836240  0.752399776  0.283017935
##  [586] -0.393693042  2.644457647 -0.018465619  1.782477228  2.254532183
##  [591]  0.226283058  1.389120147  0.729554556  2.037145363  2.458010942
##  [596]  0.833922713  0.766423751  1.085181723  0.406821617  0.297995363
##  [601]  0.563778765  0.505883170  0.269619499  0.154608609  0.384161030
##  [606]  1.546890164  0.587504682  0.499080044  0.396029517  0.659068487
##  [611]  1.481532532  0.997072185  0.699676870  0.224071555  0.245008701
##  [616]  0.534085035  1.083177557 -0.866752272  1.204186945  0.836844306
##  [621]  0.386586141  0.663584649  1.950327660  1.659940812  2.598620995
##  [626]  0.687017416  0.413208577  0.690194021  0.035102903  1.434470595
##  [631]  0.869344566  0.631128793  0.173352413  1.080724617  0.396192366
##  [636]  1.406192447  1.362530439 -0.370174182  0.599274729  1.024647813
##  [641]  1.002920853  1.551642033  0.394388300  0.672373311  0.462429790
##  [646]  1.108221618  1.218976110  0.902069498  1.383882891  1.366122361
##  [651]  1.428124010  1.387602084  1.374246477  0.653338768  1.028609858
##  [656]  0.540271758  1.453361056  1.151831421  0.423535199  0.447736275
##  [661]  0.763449710  0.567627412  0.884154361  1.310061423  0.506135588
##  [666]  0.831897467  0.251977775  0.426488881 -0.351184602  0.838092228
##  [671]  0.370667290  0.641769156  1.911687409  0.835489807  0.610130291
##  [676]  1.448937326  0.983284370  1.272119809  2.554596722  0.584986372
##  [681]  0.146193176  1.145663017  0.884485336  0.148026590  0.577978141
##  [686]  0.562181940  0.643933623  0.592902512  1.793240394  2.047244847
##  [691]  0.160705222  1.066200340  0.641044550  1.390286997  1.848360021
##  [696] -0.787512764  1.495107557  1.342247453  0.217536890  0.751860016
##  [701]  0.661091910  1.214714305  0.964173272  0.835874851  1.320087977
##  [706]  1.961207287  0.563747060  0.712832077  0.349670947  1.794368878
##  [711]  1.128423714  1.656577803  0.403641892  2.612520279  0.523682305
##  [716]  0.922155851  0.638666333  1.374364887  1.178907516  0.420287107
##  [721]  1.772435834  1.766150694  1.023651089  0.402603401  0.552624703
##  [726]  2.297492840  0.637566075  1.170886988  1.211319015  0.486246115
##  [731]  1.449495184  0.618453092  1.077418605  1.070834624  2.083728270
##  [736] -1.878866663  0.708631838 -0.001115256  1.264742197  1.244264533
##  [741]  0.526789058  1.740651034  0.762263752  0.545673403  0.830617170
##  [746]  2.554788569  0.573658333  0.949870945  0.822764500  0.067083670
##  [751]  0.089572714  0.484875347  1.079961627  0.503595602  1.967601597
##  [756]  0.880109870  1.352596842  0.577250455 -2.266342857  0.008047109
##  [761] -0.644760197  0.613443036  1.185715878 -0.358236846  0.240820080
##  [766]  0.404948313  0.832446287  1.366704261  0.758122643  0.412708008
##  [771] -1.712742699  0.817331775  1.162600978  2.029509526  0.541576376
##  [776]  0.863639543 -1.198469561  1.757141301  0.453676237  2.526746224
##  [781]  1.978565339  0.953047702  0.804376911  1.379680594  0.237758075
##  [786]  0.955815134  0.467750211  1.196712937  0.751591237  0.577164731
##  [791]  0.631129901  0.893475178  1.903304558  1.579582545 -0.138172579
##  [796] -1.240266695  0.460192335  0.117857388  0.815056354  0.737797066
##  [801]  1.154896502  0.465085409  0.218807458  2.160346964  1.376449889
##  [806]  0.098188880  0.273746644 -0.364803747  1.467256593  1.375979579
##  [811]  0.337538818  0.547011229  0.608041282  0.162368518  0.871460522
##  [816]  1.840966307  1.255337389 -1.035616404  0.828618497  1.962888979
##  [821]  0.749475019 -0.059455819  0.571267359  0.336764541  0.326107033
##  [826]  0.667569037  1.012934601  0.276275497  1.206369750 -0.149160659
##  [831]  1.470337191  0.907373298  0.466320501  1.368389021  0.581714860
##  [836]  0.320674993  0.482830106  1.322121086  0.393274292  1.453275862
##  [841]  0.743261412  0.859516255  0.933056883  1.396758341  0.237060970
##  [846]  0.467597058  0.861117687  2.593394876  0.683161736  0.849915404
##  [851]  0.595469784  0.350806479  0.753846125  0.004603648  0.578349922
##  [856]  2.012902199  1.016097473 -0.315116052  1.227284905  0.709591879
##  [861]  0.574856254 -0.337750742  0.964071746  0.519041723  0.273523408
##  [866]  0.259491289  1.439551019  2.030705782 -0.068571165  0.534085035
##  [871]  2.628995919  1.991601522 -1.195986796  0.278642609  0.336635758
##  [876] -1.811443312  1.258221124  0.226339255 -0.442196529  1.103575454
##  [881]  0.627911051  1.265700402 -0.455295080  0.606691382  0.276371583
##  [886]  0.894684411  1.106920824  1.841462050  0.723112665  0.745937963
##  [891]  0.834927821  1.842042275  2.010794916  1.304507173  0.264369820
##  [896]  0.225021653  0.521807137  0.515393796  1.182133837  0.218865510
##  [901]  0.831194557  0.095381366  0.317852639  1.286624263  0.909085458
##  [906]  0.664288392  0.677071326  0.180686196  0.656915913 -0.159872495
##  [911]  0.726524853  1.250512223  1.337277341  0.918829942  0.814190155
##  [916]  0.241689586  0.805426497  1.765263673  1.142276705  0.953013476
##  [921]  0.304548217  1.368389021  0.394388300 -0.519792695  0.759420245
##  [926]  0.609509226 -0.341449785  1.130145028  1.335948032 -1.758186786
##  [931]  1.459056054  1.901144290  2.020368742 -0.177853044  0.575020843
##  [936]  0.578441533  2.004723783 -0.289669908  0.846126376  1.144593700
##  [941]  1.259017691  1.353330577  0.861991661  0.411093636  0.846254455
##  [946]  2.569087287  1.815518906  0.810091824  0.398069843  0.789141464
##  [951]  1.235083058  0.764623810  0.309236650  0.608686494  1.751616798
##  [956]  1.174380305  0.348644296  0.522642384  0.757267693 -0.159373426
##  [961]  0.602687297  0.027311907 -0.031312821  1.746844876  0.606819464
##  [966]  1.421681911  0.650526801  0.592840304  0.473482949  2.032091766
##  [971]  0.570683379  0.458049133  0.936894534  0.511546237  0.328621159
##  [976]  1.350938392  0.578959850  1.119153172  0.803000105  0.048612395
##  [981]  0.423722522  2.005050071 -1.134243401  1.480292439  0.201456478
##  [986]  0.197044209  1.163043142  0.488289649  0.202372508  0.529138246
##  [991]  0.331683014  1.777236960  1.244589484 -0.371708300  0.567607830
##  [996]  1.490781302  0.444032918  0.586726001  0.507822082 -0.410467510
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
##   0.46731451   0.19506885 
##  (0.06168619) (0.04361410)
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
## [1]  0.09062331 -0.28388824  0.10728542 -0.13849592 -0.85670100 -0.52046626
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
## [1] 0.0214
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9187743
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
## t1*      4.5 -0.01301301   0.8884309
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 4 5 6 9 
## 1 1 3 3 1 1
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
## [1] -0.0246
```

```r
se.boot
```

```
## [1] 0.9131093
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

