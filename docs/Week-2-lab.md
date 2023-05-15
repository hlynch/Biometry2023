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
## 0 1 3 4 5 7 9 
## 2 1 1 1 2 1 2
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
## [1] -0.0221
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
## [1] 2.686051
```

```r
UL.boot
```

```
## [1] 6.269749
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.6   6.2
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
##    [1] 2.8 5.9 3.0 3.0 4.0 4.9 5.6 4.0 3.6 4.1 4.4 4.2 3.6 3.6 3.9 4.8 4.6 4.9
##   [19] 5.0 4.9 4.5 4.0 5.3 4.2 5.2 3.5 4.3 5.5 4.3 5.0 5.1 4.1 4.5 4.6 4.4 5.2
##   [37] 2.5 5.8 4.6 6.7 4.5 5.9 6.3 4.2 4.4 4.8 4.2 4.3 4.3 3.5 3.2 4.6 5.0 6.1
##   [55] 5.4 4.5 6.2 2.7 5.4 4.8 5.2 3.2 5.5 5.9 4.7 5.2 4.1 5.1 6.6 4.0 2.7 4.5
##   [73] 3.1 3.8 3.1 3.4 6.1 4.0 2.7 5.6 4.5 7.0 5.0 3.6 3.3 3.9 6.5 5.9 4.8 5.0
##   [91] 7.1 5.3 3.9 5.2 5.1 4.1 4.2 5.4 5.0 4.8 4.3 3.8 5.0 3.7 3.0 5.3 4.6 4.3
##  [109] 5.0 4.9 3.7 3.2 6.2 3.3 4.7 4.2 5.1 3.9 2.4 3.5 6.8 3.1 5.0 4.5 3.6 2.7
##  [127] 4.4 7.8 4.2 5.2 4.8 4.0 5.2 5.3 4.4 3.3 4.5 4.2 5.8 2.5 5.1 3.8 3.8 4.4
##  [145] 3.4 3.5 3.6 5.0 3.8 4.7 4.8 6.4 3.9 3.8 4.1 4.1 4.1 5.0 3.9 3.3 5.6 5.9
##  [163] 5.3 4.3 4.8 4.3 5.2 4.8 5.6 3.8 4.2 5.6 5.3 4.1 4.9 3.9 3.9 4.1 3.4 4.9
##  [181] 5.0 3.0 3.5 4.5 2.5 5.7 3.7 3.9 4.5 4.3 4.6 5.0 5.2 3.5 5.4 3.5 4.6 4.3
##  [199] 4.6 4.3 6.0 5.0 5.3 3.9 5.8 4.7 3.3 5.6 4.1 6.3 2.8 4.5 3.2 3.2 5.4 3.9
##  [217] 5.2 6.3 4.3 5.1 4.1 5.1 4.4 5.9 4.8 4.7 4.8 4.7 4.8 5.3 5.1 5.0 4.5 3.8
##  [235] 4.5 5.7 3.9 5.4 4.0 4.5 4.1 5.6 5.1 4.8 5.2 5.6 3.1 5.0 2.9 4.4 4.0 6.0
##  [253] 4.7 3.3 2.5 4.5 5.9 4.1 4.7 3.5 4.4 4.1 3.7 5.0 4.1 4.7 4.2 4.9 4.4 4.5
##  [271] 3.8 4.1 6.2 5.0 2.6 4.2 3.6 4.3 3.4 5.5 4.3 3.5 5.5 6.0 5.1 4.7 5.8 2.7
##  [289] 3.7 4.9 4.4 2.7 4.9 4.5 2.6 5.2 4.1 5.1 4.5 6.0 5.7 4.1 3.3 4.9 4.4 4.8
##  [307] 5.2 4.4 4.4 3.4 5.1 3.0 5.0 4.7 3.7 4.2 4.6 3.6 6.4 5.2 2.7 3.7 5.4 4.4
##  [325] 5.1 3.6 4.2 4.8 3.8 5.3 3.2 3.7 3.2 2.6 3.5 5.2 6.0 3.9 4.4 4.9 3.2 4.2
##  [343] 4.9 3.7 4.0 3.8 4.3 4.2 5.8 4.1 4.0 3.0 3.3 6.4 3.5 4.8 4.7 3.9 4.7 5.4
##  [361] 5.7 4.7 4.2 5.3 4.7 4.1 4.1 3.8 5.3 4.4 5.1 5.2 4.8 4.3 4.2 6.1 3.8 4.0
##  [379] 6.4 4.8 4.9 3.0 4.4 4.8 5.7 5.0 4.3 4.7 6.7 3.7 5.4 5.3 5.3 3.9 4.2 6.1
##  [397] 3.0 4.5 4.1 4.4 5.8 5.2 3.8 3.5 4.3 5.2 4.2 5.2 5.2 4.4 4.9 5.4 3.8 3.0
##  [415] 3.9 4.6 5.0 4.8 4.2 7.6 3.8 4.7 5.4 4.3 3.1 6.3 3.9 6.1 4.9 3.9 3.6 4.1
##  [433] 4.5 5.8 4.6 3.8 2.9 4.0 5.3 2.2 4.7 4.1 3.8 5.5 4.2 5.1 4.4 5.6 4.7 5.1
##  [451] 5.7 5.3 3.8 6.2 4.3 4.4 4.2 5.2 5.2 4.7 3.9 4.8 5.5 4.3 3.2 5.2 5.1 4.3
##  [469] 6.7 5.1 5.0 6.5 4.0 4.1 3.1 4.2 2.8 4.2 5.2 3.5 3.2 4.6 3.7 4.3 6.3 3.5
##  [487] 4.6 4.8 4.7 4.7 3.6 3.6 5.7 4.4 5.2 4.1 6.2 4.4 4.9 4.0 5.7 3.1 5.6 3.8
##  [505] 4.5 3.9 4.0 4.4 4.3 4.9 3.2 3.5 4.5 4.5 4.0 6.2 3.9 4.6 3.6 4.9 5.2 3.8
##  [523] 4.6 4.6 4.3 3.9 3.6 4.1 4.3 4.9 5.1 5.5 3.6 5.6 4.3 3.3 3.6 4.5 3.1 5.0
##  [541] 5.0 5.5 4.0 4.0 5.3 3.5 4.2 5.2 4.0 5.4 3.4 4.0 3.3 2.9 3.9 5.0 3.3 4.7
##  [559] 4.3 6.2 2.9 2.6 3.8 5.1 5.5 2.9 3.5 2.3 3.2 2.5 5.5 4.8 4.4 4.7 5.6 3.7
##  [577] 4.0 5.4 4.8 4.0 4.1 4.0 3.4 3.2 4.5 3.7 4.1 3.6 3.9 4.1 3.5 4.4 4.1 3.8
##  [595] 5.1 3.0 4.2 4.7 5.6 3.8 5.0 3.3 3.5 4.2 4.3 4.7 5.3 5.8 3.9 3.9 3.7 4.5
##  [613] 3.9 5.5 5.2 3.5 3.3 2.4 4.5 3.9 5.0 5.7 6.1 3.5 2.9 3.9 3.8 4.3 3.7 2.0
##  [631] 5.6 4.8 5.0 4.6 2.8 5.1 3.9 4.8 3.8 3.1 3.7 5.0 4.0 6.0 3.9 4.4 4.4 4.4
##  [649] 4.6 5.0 5.5 4.9 4.9 6.0 3.8 4.5 3.5 3.3 5.5 5.5 2.9 5.3 5.8 4.4 6.1 5.4
##  [667] 3.6 4.9 3.2 5.1 4.1 3.8 5.7 2.9 4.4 5.3 3.0 5.0 5.0 3.1 4.4 4.8 3.5 5.0
##  [685] 4.7 4.4 4.9 3.4 5.9 5.7 5.0 5.0 4.2 4.1 3.5 3.4 3.7 5.0 3.0 4.8 3.9 4.6
##  [703] 4.2 5.4 4.2 3.8 4.1 6.4 6.2 4.8 4.3 4.2 2.7 5.1 4.5 3.3 5.6 4.4 5.0 4.6
##  [721] 2.8 4.1 4.0 5.2 5.2 3.5 4.9 5.1 5.9 3.1 4.5 4.1 4.6 3.4 3.2 5.1 3.9 5.1
##  [739] 5.3 3.4 3.5 4.3 3.3 4.8 4.5 3.3 2.6 5.0 5.2 4.7 3.3 4.9 4.6 5.8 3.2 5.1
##  [757] 5.7 5.5 3.0 4.0 4.0 4.9 4.1 5.2 2.3 4.4 4.3 4.9 5.5 3.7 4.4 5.1 5.0 3.8
##  [775] 5.6 4.6 4.3 5.4 4.6 5.2 4.0 2.5 4.4 4.6 3.9 4.1 4.8 5.9 5.3 4.5 4.7 6.2
##  [793] 3.8 4.1 3.7 5.5 4.1 4.3 3.2 3.8 5.5 3.9 4.8 4.6 3.1 2.5 4.8 6.6 4.8 4.7
##  [811] 6.2 4.6 4.0 4.8 4.0 5.0 5.1 4.4 3.6 4.7 5.3 6.0 5.0 6.6 5.6 3.8 5.1 6.0
##  [829] 5.1 5.0 3.3 4.8 3.8 4.4 4.0 2.4 4.6 6.5 5.9 4.0 5.8 4.3 5.8 5.8 5.5 3.1
##  [847] 5.8 5.0 4.4 4.4 4.7 4.5 4.0 3.8 5.2 4.2 4.6 5.9 4.0 6.2 3.5 3.8 5.3 3.1
##  [865] 4.6 3.1 5.0 3.9 4.5 4.4 4.0 4.8 6.3 4.1 4.3 5.0 4.4 5.9 4.2 4.7 4.1 5.9
##  [883] 6.3 5.4 5.6 3.6 3.8 4.9 4.1 4.3 5.8 5.8 4.1 4.3 6.2 4.2 6.3 5.1 4.5 4.0
##  [901] 6.2 3.2 4.3 3.0 4.6 5.1 4.2 2.9 6.2 3.4 5.0 3.6 4.1 3.3 4.2 3.8 3.0 5.8
##  [919] 3.9 3.2 6.2 3.7 5.1 6.9 4.0 6.1 4.2 5.1 4.1 3.9 4.4 3.9 6.5 4.0 3.9 6.8
##  [937] 4.2 4.4 4.5 4.2 3.7 4.8 3.8 4.3 4.3 5.6 4.6 5.3 3.2 4.7 5.3 4.4 5.5 3.4
##  [955] 5.4 3.8 4.1 5.5 3.7 4.0 3.7 4.5 4.0 3.9 5.4 4.4 4.8 3.9 5.4 3.6 3.6 3.4
##  [973] 4.3 3.1 5.2 5.3 3.9 3.9 3.2 5.3 4.3 3.9 6.0 5.4 5.4 4.6 5.6 5.5 2.6 4.6
##  [991] 5.0 5.4 4.9 4.7 2.8 4.4 3.9 4.2 4.1 4.8
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
##    [1] 3.9 6.9 5.0 3.3 4.4 5.7 4.8 3.7 4.3 4.9 4.6 3.8 5.4 4.8 5.0 5.2 3.4 5.6
##   [19] 5.5 6.0 4.4 4.4 4.7 4.6 4.9 2.3 3.8 4.1 4.6 3.0 4.5 5.0 4.7 4.0 5.5 3.7
##   [37] 5.1 4.3 4.3 4.6 4.8 5.7 3.2 4.5 2.5 3.1 5.2 2.6 3.3 4.7 4.6 3.9 6.5 5.2
##   [55] 4.9 3.6 3.0 3.8 4.5 4.5 2.1 5.1 3.4 5.0 4.0 3.4 4.1 5.6 4.6 4.4 5.3 4.9
##   [73] 4.4 4.8 5.5 4.6 4.5 4.3 5.2 4.9 4.1 5.7 4.1 3.8 3.8 3.4 4.6 5.1 4.4 5.0
##   [91] 5.0 3.6 5.6 3.6 2.9 3.5 3.6 4.1 3.6 6.4 3.5 4.3 5.0 3.6 4.7 2.9 3.3 6.1
##  [109] 4.6 3.9 5.1 5.5 3.5 4.5 4.3 4.0 5.6 4.6 4.6 2.6 3.5 5.4 4.3 4.2 4.6 3.8
##  [127] 4.1 4.7 4.2 4.9 3.5 5.9 3.5 4.8 3.9 3.7 3.2 4.6 5.4 5.1 4.3 5.4 2.5 4.3
##  [145] 4.0 3.4 5.7 3.4 4.2 5.1 4.4 5.0 5.8 4.4 3.9 5.1 4.7 3.4 4.5 2.4 5.3 4.8
##  [163] 4.9 5.1 4.4 4.1 3.1 4.8 5.1 5.4 4.5 6.3 5.7 3.6 5.0 4.8 4.6 5.3 5.0 3.6
##  [181] 4.2 4.3 5.1 4.5 2.7 5.1 5.2 3.7 2.8 5.6 5.3 4.0 4.7 5.5 4.6 4.2 4.9 2.6
##  [199] 4.7 3.8 2.7 6.6 5.4 4.9 5.0 5.6 5.5 3.1 3.8 2.1 2.5 4.0 3.8 3.1 4.8 4.5
##  [217] 6.5 5.2 3.9 4.0 5.0 5.1 4.1 5.6 4.9 3.7 3.8 4.2 4.3 5.3 3.6 5.5 4.1 4.7
##  [235] 3.5 4.9 3.6 4.4 4.4 3.7 4.4 4.1 3.9 6.2 5.0 4.5 4.9 4.4 4.9 4.7 3.1 4.6
##  [253] 4.3 4.9 2.8 4.5 5.8 3.8 4.0 4.1 5.2 3.7 4.7 4.7 4.6 4.6 5.5 4.5 7.3 5.1
##  [271] 3.3 5.0 3.6 3.7 5.4 5.4 4.9 6.4 4.4 5.6 5.1 4.6 5.8 4.3 4.5 4.0 2.7 3.7
##  [289] 4.0 4.1 5.5 5.5 5.6 4.3 4.8 4.3 6.0 3.6 4.4 4.7 3.4 4.6 4.4 5.2 2.4 4.7
##  [307] 3.8 4.7 6.3 4.6 5.3 5.2 4.7 5.3 2.7 4.4 4.9 4.7 5.5 4.0 4.1 3.7 3.9 3.6
##  [325] 4.5 6.0 3.3 4.5 5.0 4.1 4.0 3.6 5.4 5.0 2.9 4.0 3.7 4.9 3.8 3.5 5.6 3.0
##  [343] 5.3 3.3 3.8 4.3 4.1 4.8 6.4 3.8 6.3 5.2 4.6 4.1 5.2 5.7 4.7 5.8 4.2 3.5
##  [361] 2.6 4.9 3.0 4.0 3.4 6.4 5.0 4.4 3.8 4.2 5.3 5.9 3.5 3.7 3.3 4.7 4.6 5.3
##  [379] 4.3 4.2 4.1 5.0 3.9 4.6 4.4 3.9 5.3 4.0 4.2 6.2 4.3 4.8 5.0 3.4 5.0 4.1
##  [397] 5.2 5.0 5.8 4.7 4.7 4.1 6.1 5.8 3.2 5.2 3.0 5.4 3.6 3.9 5.1 4.2 5.6 4.0
##  [415] 3.8 5.2 5.2 4.3 5.5 3.8 5.6 5.7 3.4 5.0 4.5 4.7 3.2 3.2 2.7 4.9 3.9 3.7
##  [433] 4.9 4.5 3.6 4.7 4.1 5.6 6.6 6.0 4.3 3.9 4.4 2.9 5.4 5.0 5.8 3.6 5.4 3.9
##  [451] 5.0 4.8 4.2 6.1 5.2 4.9 4.1 4.3 4.8 3.9 4.7 5.2 5.0 4.2 4.1 5.4 3.5 4.6
##  [469] 5.2 4.1 5.7 4.7 4.4 3.0 3.8 4.7 4.9 5.7 3.7 3.8 4.3 5.1 3.5 2.3 3.4 5.7
##  [487] 3.9 5.9 5.0 4.6 4.3 5.3 5.1 5.3 4.3 5.3 4.3 5.4 3.4 4.1 5.9 3.8 1.6 5.9
##  [505] 5.1 3.4 3.5 5.6 3.6 5.1 3.4 6.1 7.1 2.8 4.3 5.7 3.4 4.9 4.4 4.2 4.6 5.0
##  [523] 4.4 5.3 4.8 3.4 4.0 3.0 3.7 3.8 3.9 2.6 5.5 5.8 3.7 5.3 3.8 3.6 4.3 4.4
##  [541] 5.3 5.5 4.0 4.2 4.6 2.8 5.0 4.1 5.5 4.1 7.0 3.6 3.7 4.9 4.2 4.8 4.1 3.5
##  [559] 5.2 3.5 3.6 5.5 3.7 4.4 4.0 6.1 4.1 3.0 4.4 3.0 4.9 4.2 5.3 3.6 5.1 5.5
##  [577] 5.9 2.5 3.5 4.4 3.2 5.0 5.5 5.1 4.8 4.1 4.9 3.1 3.8 4.7 3.9 4.0 3.7 5.1
##  [595] 5.9 6.6 4.6 5.5 3.3 5.3 2.9 3.0 4.0 5.4 4.8 3.8 4.8 4.2 5.1 5.0 4.1 3.9
##  [613] 3.9 6.2 6.5 5.5 4.1 4.6 5.4 4.8 4.5 4.2 3.7 4.2 5.7 4.0 4.6 5.2 4.2 5.1
##  [631] 4.2 4.8 5.2 4.3 4.1 4.6 4.7 4.2 5.3 6.1 3.5 4.3 4.1 5.2 5.1 2.4 3.4 3.8
##  [649] 2.6 4.5 3.9 4.9 2.3 6.4 2.3 3.6 5.3 3.2 2.9 4.0 2.8 4.3 6.5 4.7 4.2 2.0
##  [667] 5.7 3.2 5.8 5.3 4.8 3.1 5.2 4.1 5.6 5.9 5.4 3.9 5.2 3.7 5.5 5.6 4.8 5.0
##  [685] 5.8 5.1 4.8 4.3 4.7 4.7 4.2 3.7 6.0 5.6 3.4 4.7 4.2 4.6 4.4 4.3 4.9 3.0
##  [703] 3.9 4.1 4.4 4.3 3.9 4.0 4.8 2.8 4.9 5.0 4.7 6.0 4.1 5.5 4.6 5.6 3.4 5.2
##  [721] 6.3 4.0 4.3 3.7 5.2 3.9 3.9 3.2 4.7 6.1 4.2 3.4 5.8 3.3 4.9 4.7 5.3 2.3
##  [739] 4.8 5.3 5.6 4.7 4.2 5.6 4.1 3.4 3.4 3.5 5.3 5.0 4.4 4.0 5.9 3.7 4.2 3.8
##  [757] 4.8 4.7 4.8 5.0 5.6 4.9 5.4 6.1 4.6 4.3 4.7 4.3 4.1 4.5 3.7 5.5 4.7 5.9
##  [775] 3.7 3.8 2.9 3.4 3.5 4.8 4.8 4.6 4.4 4.5 5.0 5.5 4.3 5.7 6.5 3.3 3.5 3.4
##  [793] 2.9 4.0 4.4 4.9 2.9 3.6 4.2 3.7 4.3 5.1 4.2 4.5 5.4 3.5 3.7 4.7 4.0 4.7
##  [811] 4.4 4.8 6.0 2.9 3.0 5.1 5.3 4.3 5.1 4.7 4.8 3.4 3.7 2.2 5.1 4.1 4.6 2.1
##  [829] 5.4 4.9 4.8 5.8 4.4 5.2 4.0 3.2 3.9 4.7 4.2 5.2 4.8 4.8 4.4 3.5 4.5 3.8
##  [847] 3.7 5.1 6.0 3.9 6.4 3.4 2.0 4.7 4.8 5.5 4.1 4.9 5.0 3.9 3.2 5.4 4.3 3.5
##  [865] 4.3 3.8 4.2 5.0 4.0 5.5 6.4 4.5 3.4 5.0 4.5 4.3 4.5 3.6 5.3 3.3 5.2 5.9
##  [883] 5.2 4.0 5.6 4.6 4.2 2.9 4.0 4.7 5.5 3.3 5.2 5.4 2.5 5.1 4.4 5.8 5.7 4.3
##  [901] 4.9 2.2 5.0 4.9 4.8 5.1 4.7 4.2 3.6 4.0 3.6 4.1 5.3 5.4 5.0 4.4 4.3 4.8
##  [919] 5.7 5.4 5.1 5.5 3.7 6.0 4.7 4.9 3.0 3.7 3.8 3.5 4.3 3.4 5.5 4.8 4.2 3.9
##  [937] 4.4 4.2 6.9 6.1 6.1 4.7 4.7 3.8 4.8 4.8 4.9 3.1 5.5 5.3 4.5 5.7 4.7 4.7
##  [955] 5.0 4.1 5.4 4.3 2.1 6.1 4.2 4.6 4.9 4.5 4.9 4.6 4.2 4.9 4.5 4.0 4.2 4.2
##  [973] 2.5 3.0 4.9 4.9 4.4 5.4 4.0 4.4 5.1 4.7 3.6 5.0 4.4 3.2 4.5 4.3 5.2 4.7
##  [991] 4.3 3.4 3.5 4.1 4.3 5.2 4.7 6.2 5.2 4.8
## 
## $func.thetastar
## [1] -0.0206
## 
## $jack.boot.val
##  [1]  0.47939394  0.37941176  0.23463855  0.11871345  0.02401216 -0.05567867
##  [7] -0.12053571 -0.32228412 -0.40943953 -0.50322581
## 
## $jack.boot.se
## [1] 0.9404927
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
##    [1] 3.4 5.0 3.0 4.3 4.4 3.5 4.8 5.7 5.6 4.1 4.7 5.1 2.6 4.4 3.9 5.5 2.8 5.0
##   [19] 4.2 4.6 4.8 5.1 6.1 5.4 4.9 4.1 4.1 2.3 3.4 3.8 2.8 4.1 4.8 6.4 5.3 4.0
##   [37] 4.8 5.4 5.2 5.9 5.6 5.1 4.6 4.8 4.7 5.7 4.4 5.6 6.1 5.3 5.8 3.5 4.7 4.0
##   [55] 4.9 6.2 4.2 4.7 4.3 4.8 4.4 3.9 5.0 2.9 5.7 4.8 4.7 4.7 3.8 4.9 4.4 6.1
##   [73] 3.2 5.0 3.9 4.8 3.4 6.3 3.7 5.0 6.2 4.7 5.9 4.4 4.1 4.3 3.7 4.5 5.3 4.3
##   [91] 5.4 5.9 5.4 3.0 4.8 4.6 4.8 6.2 3.3 5.0 4.6 4.7 4.4 3.8 4.1 3.6 4.7 3.8
##  [109] 4.5 4.8 3.3 3.7 4.1 5.1 4.5 4.6 4.2 5.0 3.9 3.2 4.6 4.1 4.7 3.6 5.8 4.7
##  [127] 5.0 4.0 4.5 3.4 3.7 2.9 6.2 4.8 5.6 4.8 5.8 4.7 5.6 4.3 4.3 3.7 4.1 3.9
##  [145] 4.5 5.2 4.1 3.0 3.6 4.4 4.2 3.4 4.7 5.3 2.8 5.3 4.6 5.2 4.7 3.8 3.0 3.7
##  [163] 4.1 5.6 5.6 4.8 4.9 3.6 5.6 2.9 5.7 3.5 4.2 4.6 4.3 2.9 3.4 4.5 4.0 3.3
##  [181] 4.7 4.0 4.0 4.6 4.2 4.1 4.6 3.7 3.4 3.4 3.4 4.8 4.4 3.9 4.6 3.7 5.0 1.6
##  [199] 5.4 3.5 5.9 4.7 4.7 5.1 5.2 4.5 3.4 3.0 2.6 4.9 4.1 4.7 5.3 5.8 4.8 4.0
##  [217] 4.9 4.0 4.5 3.3 4.4 5.0 4.7 4.7 5.9 3.8 4.8 5.4 4.2 4.7 4.7 4.6 2.9 5.4
##  [235] 4.2 6.0 5.0 5.7 5.9 4.7 2.4 3.3 4.0 5.8 3.5 5.5 4.3 4.3 5.8 4.8 4.7 3.9
##  [253] 4.0 5.2 3.9 5.2 3.9 4.0 5.9 4.7 3.9 4.6 5.9 3.9 3.4 3.9 5.2 4.4 6.1 4.3
##  [271] 3.1 4.7 7.3 5.5 3.6 3.3 4.9 4.9 4.1 3.3 5.0 5.6 5.5 3.2 4.8 4.1 5.7 4.5
##  [289] 3.8 5.6 4.4 3.6 5.9 3.7 5.6 4.4 4.2 3.8 4.9 4.7 5.0 5.0 3.3 4.8 4.3 4.7
##  [307] 3.5 4.7 4.5 4.5 4.7 5.0 3.0 4.5 4.3 4.4 5.0 3.3 4.2 3.1 4.0 3.8 4.8 4.8
##  [325] 6.2 3.2 4.0 4.8 6.0 3.4 6.3 3.7 5.6 6.1 4.5 4.7 3.6 5.4 3.7 5.3 5.9 5.4
##  [343] 5.1 3.6 5.3 4.8 5.6 4.8 5.7 5.4 4.6 3.4 3.7 5.0 3.5 6.6 5.0 5.7 5.0 3.5
##  [361] 4.2 5.4 3.6 5.4 3.0 4.0 4.0 4.4 3.5 5.2 5.6 5.0 4.6 4.8 3.3 5.8 3.8 2.9
##  [379] 4.5 3.7 4.1 3.5 3.9 3.9 4.9 5.0 5.9 3.9 4.6 5.6 3.9 3.6 4.9 3.9 4.1 4.8
##  [397] 4.8 4.8 4.2 5.6 5.5 4.0 4.0 6.1 5.9 3.6 3.4 4.2 4.9 5.7 4.9 5.5 4.5 4.9
##  [415] 4.4 5.2 4.6 4.6 5.8 4.3 3.8 3.8 3.7 5.7 3.3 2.8 3.8 4.2 4.6 5.6 3.8 4.8
##  [433] 5.1 4.3 5.2 3.8 4.9 3.4 5.3 4.9 4.5 5.7 5.4 5.1 3.5 3.5 3.2 4.4 4.3 5.3
##  [451] 3.9 2.9 5.3 3.3 4.0 3.4 3.5 3.4 5.0 4.0 5.1 4.2 4.0 1.8 4.6 5.0 3.4 3.7
##  [469] 4.1 4.3 4.1 4.0 6.0 4.8 5.6 3.0 4.5 4.2 4.9 4.8 5.6 4.8 5.1 4.3 4.5 4.8
##  [487] 4.9 3.1 3.4 4.6 5.2 3.3 5.1 5.7 3.6 4.8 5.0 6.0 2.6 3.4 3.4 5.3 3.7 5.2
##  [505] 4.5 5.4 4.3 4.5 3.7 3.7 2.7 5.8 4.1 5.5 4.6 3.7 4.5 5.3 5.6 2.8 4.4 5.1
##  [523] 4.3 3.6 4.2 5.1 3.4 4.5 5.5 4.7 4.7 4.6 4.5 5.0 6.4 3.8 4.1 5.6 4.1 4.3
##  [541] 4.8 3.1 4.6 5.0 5.0 5.6 4.7 4.5 4.5 4.1 6.4 5.5 3.0 3.9 3.2 3.5 3.7 6.3
##  [559] 6.7 6.4 4.2 4.8 4.3 3.6 4.4 4.8 6.7 5.6 6.0 5.1 4.3 5.1 3.9 4.9 5.0 5.5
##  [577] 4.7 4.0 3.6 4.8 5.1 5.3 3.2 6.0 4.5 3.6 2.4 3.7 5.0 4.1 3.7 4.1 6.2 3.3
##  [595] 4.2 5.1 3.2 3.5 5.0 3.9 3.8 5.4 4.7 3.2 4.3 4.1 4.7 5.8 3.5 4.1 4.3 5.0
##  [613] 5.5 4.9 3.2 5.0 4.7 3.8 4.1 1.9 5.5 5.9 5.6 5.6 4.7 6.0 3.9 5.1 4.2 3.9
##  [631] 2.1 6.9 3.2 3.2 3.1 3.8 3.8 3.6 4.2 4.3 4.0 3.6 4.7 5.4 4.3 3.3 3.4 3.6
##  [649] 5.3 5.0 4.7 4.9 5.0 4.0 5.0 5.0 4.4 2.6 4.0 5.2 4.2 4.9 5.1 5.0 5.1 3.9
##  [667] 4.2 4.6 4.3 5.1 4.7 4.2 3.4 2.6 4.6 5.5 3.7 3.8 3.0 3.9 4.3 3.8 5.9 6.2
##  [685] 5.3 3.8 3.6 6.2 4.3 3.4 4.1 5.4 5.6 4.4 4.4 4.2 5.5 4.1 3.9 4.3 2.5 4.2
##  [703] 4.1 4.6 5.0 4.3 3.1 3.5 4.0 4.6 6.6 4.5 5.5 5.9 5.0 5.4 4.4 4.6 6.3 4.6
##  [721] 4.9 4.7 4.7 3.9 4.7 4.0 5.6 6.1 3.7 6.8 3.8 4.0 4.3 6.2 4.1 3.5 4.5 4.9
##  [739] 5.5 4.3 4.3 5.5 3.0 4.2 3.7 5.4 2.9 4.9 4.6 6.0 5.3 4.3 4.3 4.4 4.3 2.9
##  [757] 4.3 4.7 4.4 6.3 3.6 3.8 4.0 3.8 4.2 4.5 6.2 5.0 4.1 4.2 5.6 4.2 6.3 4.2
##  [775] 3.3 5.0 3.9 5.0 4.6 4.1 2.4 5.1 4.5 4.2 4.8 6.1 3.5 3.7 5.0 4.3 4.9 3.6
##  [793] 4.7 2.7 3.4 4.2 4.2 4.4 4.4 6.9 4.3 4.8 2.8 5.6 3.5 6.2 3.5 5.2 4.8 4.4
##  [811] 2.1 4.1 4.8 3.9 4.8 6.1 5.3 4.6 3.5 4.7 2.5 4.8 4.8 4.6 5.0 6.4 4.2 6.3
##  [829] 4.9 4.8 4.6 2.5 7.2 4.5 2.8 5.3 6.8 6.2 3.7 5.2 2.5 3.4 3.2 3.6 5.1 4.1
##  [847] 4.4 5.2 4.0 4.7 3.6 5.5 6.5 2.6 5.2 4.9 4.0 4.6 5.1 4.0 4.0 4.8 5.8 4.8
##  [865] 4.7 5.6 4.5 4.4 3.6 4.3 4.5 2.8 3.7 2.9 5.1 5.0 5.7 4.5 4.6 4.2 3.5 5.5
##  [883] 5.6 4.2 4.3 3.5 3.3 6.6 4.0 4.2 4.8 4.9 4.5 4.0 5.0 4.5 4.5 3.6 4.0 5.5
##  [901] 4.0 4.3 4.0 3.1 5.5 5.1 4.1 4.8 2.8 3.1 3.5 2.0 4.8 4.5 4.8 3.3 3.9 4.8
##  [919] 5.6 4.3 5.3 4.6 3.9 5.8 4.5 4.2 3.7 4.5 4.2 5.2 4.9 3.8 3.5 5.4 5.1 4.5
##  [937] 3.6 5.0 5.2 3.6 4.8 4.5 5.3 4.2 4.0 2.8 5.2 4.9 5.6 4.2 4.9 6.0 4.8 5.2
##  [955] 4.2 5.5 3.2 6.8 5.0 4.0 5.0 3.4 2.0 4.4 4.3 5.1 5.0 3.6 3.3 3.4 4.7 3.7
##  [973] 4.8 3.4 4.8 5.3 4.8 3.2 4.1 4.0 4.8 4.1 3.9 3.9 3.0 5.1 4.0 3.5 3.5 4.4
##  [991] 3.7 4.0 4.7 4.9 4.1 4.8 6.2 4.9 4.2 5.3
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.6 5.5 5.3 5.2 5.1 5.0 4.8 4.8 4.7 4.4
## 
## $jack.boot.se
## [1] 1.066583
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
## [1] 0.5789895
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
##   3.001617   4.952917 
##  (1.274829) (2.289605)
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
## [1] -0.25748948  1.20871893  1.11317658  0.87931011 -0.08855171 -0.12862243
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
##    [1] -0.3627152240  0.2448764802  0.0838698846  0.5255767122  0.9034334982
##    [6] -0.7151338543 -0.1166163915  0.7322377377 -0.1624136299  1.8547951339
##   [11]  0.4214051669  0.6567783350  0.6921667886 -0.1288916624  0.2031406890
##   [16]  0.6686764404  0.4073975516 -0.1759862722  0.5077192369  1.0236825372
##   [21]  0.9784021586  0.8721079384  0.8558323333  0.1532464998  0.3704882942
##   [26]  1.5402543283  0.4669059746  1.0374050423  0.5781543199  1.0929460229
##   [31]  0.6843423876  1.0635137821  0.0412574808  1.4347370138  0.8684793892
##   [36]  0.1708663798 -0.8089131648  0.5860126587 -0.2772728640  0.1860076813
##   [41]  0.6347350498  0.7937087662  1.1032690247  0.7013101682  0.0598297972
##   [46]  1.3941701852  0.0815142672  1.9788963744  0.8839252247 -0.4146372849
##   [51]  0.4397956800  1.6479335480  0.1580262123  0.9545232485  0.6406915140
##   [56]  0.8702661442  0.0515835475  1.1950850160 -0.4895479181  0.5713048087
##   [61]  0.6716977766 -0.0750965939  0.7693011039  2.2374540360  0.1872566764
##   [66]  0.6754681814  0.7378062019  0.7829031311  1.6172506163  1.4190196685
##   [71]  1.1056610567 -1.1226439237  0.6216540038  1.9427967087  1.1205882599
##   [76]  1.0736967093  0.4824984422  0.2987762219  0.1312010093  0.0314243335
##   [81]  0.3010910900  0.4152477559  0.4715659530  1.0787851398  0.9854358148
##   [86]  0.3586124979  1.0762455945  0.8138931659  0.9991625006  0.6648506039
##   [91]  0.2824195307  1.4443463313  0.6939260960  1.0217321564 -0.2266315419
##   [96]  0.1871565831  0.0408713835  0.7681411251 -0.1493853577  0.2785074890
##  [101]  0.3691989198  1.1095356881  0.6165793296  0.6619752661  0.1286790907
##  [106]  2.0303959503  0.1950543146  0.2389433752  0.2348882722  0.2300224381
##  [111]  0.5744158683  0.6695698896  1.0296347361  0.4184348506  0.2847873370
##  [116]  0.3916974407  1.0568540149  0.6327660523 -0.1569413527  0.3705342174
##  [121] -0.0042072363  1.0499533924  0.6203630455  1.0120475010  1.7916754073
##  [126]  0.3079158481  0.6347350498 -0.0088192731  0.5622478401  1.2083097739
##  [131]  0.7506266548  1.7568195447  0.4549672857  1.8671185222  0.5489703490
##  [136]  0.5950119963  0.6392589147  0.7307215258 -0.0186668441  0.6945780384
##  [141]  0.1211429422  0.5070189100  0.3072774593  0.2369143940  1.7299254350
##  [146]  0.5478674657  1.2585669582  1.1629192151  2.0608334525 -0.1766359685
##  [151]  0.8436262755  1.1293980230  0.3733885817  0.7876379224 -0.0784203569
##  [156]  0.8147480788  0.6364954523  1.4289340383  1.1502648581  1.4913658147
##  [161] -0.0447589957  0.2369739493  0.0908375286 -0.4403584038  1.6751871029
##  [166]  0.5163706136 -0.0397209243  0.3001441599  0.8928363216 -0.0238692889
##  [171]  0.7714542120 -0.1498084330  0.9327266901 -0.0633071686  0.7278141460
##  [176]  1.1985592999  0.7667071796  0.6969672067 -0.4906548216 -1.1850995918
##  [181]  1.0988461115 -0.3082248528  0.6021374512  0.9363961071  0.8542673464
##  [186]  1.1701903509  1.0679474096  1.8496531180 -0.1006290305  1.2176852945
##  [191] -0.0837621055  0.5051814516  0.5157587737 -0.1159824362  0.9710612790
##  [196] -0.3368533551 -0.0969106610 -1.4602992278  0.8691164924  0.3123367473
##  [201] -0.3779823380  0.6881443389  0.5842230012  1.0546865131  0.4946613203
##  [206]  0.9080123441 -0.5309758769  1.1772347091  0.8255108263  0.1519314763
##  [211]  0.5866836847 -1.6411138198  0.7314572816  0.9011198237  0.8273699095
##  [216]  1.4262614440  0.1097081078  0.0770892434 -0.2969422628  0.3187439045
##  [221]  0.5643236584  0.4602851794  0.5828098349 -0.5265353873  0.6642644347
##  [226]  0.9073424286  1.9043991644  0.5694978952  0.1208860183  0.3446968483
##  [231] -0.3118767277  0.5214436300 -0.0711243735  0.5197009854 -0.0665624163
##  [236]  0.3406345166 -0.1111524136  0.0235922769  0.4053178737 -1.1751148804
##  [241]  0.3519052801  0.5962922605  1.0826808957  0.1754534243  0.6653395854
##  [246] -0.1007322484  0.9500120815  0.3164987446 -0.0777286303  0.7358692512
##  [251]  1.9930415597  0.3853542652  0.5223036443 -0.0283346927  1.0173768714
##  [256]  0.2397558803  0.7988029721  1.2707345267  0.8077322534  0.8879790926
##  [261]  1.0764976766  0.8159362305  0.0827309685  0.1401769662  0.7283641914
##  [266]  1.6570181843  1.7627073175  0.5751877746  0.6640044553  0.5404466209
##  [271]  0.6214464526  0.8250798762  0.6941615424  0.5975438540  0.5707223173
##  [276]  0.6186043479  0.2952151368  0.8307476573  0.8501294357  1.1107477075
##  [281]  0.0128789751  0.8302669928  0.2823203081 -0.0443567070  0.7564868802
##  [286]  1.0447997714  0.9377929692 -0.1573412463  0.7656522740  0.6501846909
##  [291]  0.8873103146  0.6153064367  0.2599572295  0.0317258257 -0.1580893687
##  [296] -0.0783815859  0.2164153711  0.0779915222  0.2446160555  0.7183295657
##  [301]  0.5860202590  0.2915184495  0.6404484266  0.6284566185  0.0582029664
##  [306]  1.3367019966  0.9725044347 -0.3875364357  1.1807552863  1.0134400438
##  [311]  0.8327297916  0.6410287722 -0.0071000371  0.8765755250  1.0167315988
##  [316]  0.0622896662  0.9640297268  0.3365464848  1.2958466425  0.2196235293
##  [321]  0.3351286339  0.0406152253  0.6975336291  0.2502423579 -0.3528450893
##  [326]  0.5247592335  0.8983800536  1.5256659254  1.1232696790  0.7456880493
##  [331]  0.5423180089  0.7011617878  0.9179824418  0.3836725751  0.7615793936
##  [336]  0.6858506381  1.2602914292  1.1360066068  0.2272036738  1.9775203134
##  [341]  1.1554831979  0.4524761347  0.2797946855  0.6897921753 -0.0721864509
##  [346]  0.4191677635  0.4753618357  1.3634734764  0.3868014477  0.6253032178
##  [351]  1.1676729897  1.0406199137  0.8604740165  0.1706125491 -0.4231493254
##  [356]  1.1377176199  0.8343969178  0.8684461221  1.0686331598  0.1122326544
##  [361]  0.3995907633  0.9966140972 -0.2683508463  0.3786279045  0.4025467262
##  [366]  1.0801136293  1.2983852073 -1.3609970199  1.3846267988  0.2726192858
##  [371]  0.0122216625 -0.7955578181 -0.6811727583  0.2946908196  0.3100217841
##  [376]  0.6438635551  1.2408066867  0.1835188445  0.2067304740  0.6428298166
##  [381]  1.0656137681  0.5604192923  1.4195191270  0.4715236980  0.5342590696
##  [386]  0.6684709396  1.0587487015  0.2412936334 -0.0966042142  0.0377912970
##  [391]  1.3512994954  0.2685905325  0.9256371176  0.7238050110  0.7616266420
##  [396]  1.2824519837 -0.2421368415  0.7374013064  0.8043501637 -0.0515227725
##  [401]  0.8989370106  0.5971596472  0.6045050018 -0.0176059757  0.1327820040
##  [406]  0.6837263040  1.9395892701  0.7716207627  1.4704691247  0.6078081943
##  [411]  0.3496741941  0.6341590856  0.2125645915  0.3338121818  0.6486011157
##  [416]  0.7352668290  1.1308467867  0.5916332282  0.5950119963  1.2348341352
##  [421]  0.6055141633  0.2810097477  0.6684131567  0.4883444923  0.2972050578
##  [426]  0.1321843640  0.9233665568  1.4043628712  0.9002631956  0.8552505646
##  [431]  0.8276121406  0.4201232577 -0.8651123965  0.3061908623  0.6678399695
##  [436]  0.0014331062  0.5637471327  0.4093024944  0.7310526322  0.4142039755
##  [441]  1.3391220066 -0.1090171240  0.8685348347  1.5715448076  0.6325367499
##  [446]  0.3087266432  0.9950632226  0.4451992243  0.6085057379  1.1985632748
##  [451]  0.6058500119  1.0616490056  0.2447178738  0.6654873876  0.6182871176
##  [456]  0.7443332383  0.0968496416  0.7298928353  0.6332720008  0.7425739197
##  [461]  0.3085783467  0.5010707602  0.6114146052  1.1498195845  0.2259720966
##  [466]  0.6903960337  0.6825173543  0.6467903162  0.4230404845  1.1237376203
##  [471]  1.2611134946 -0.4228285562  0.4511696751  0.4844475991  1.8535745529
##  [476] -0.3406708236  0.7923478946  0.6668837673  1.1639930568  0.8551368438
##  [481]  1.0614083467 -0.0241893776  0.2687248421  1.1862148674  0.1062963522
##  [486]  0.1981089392 -0.1704710383  0.4936645115 -0.2154368301  0.0135449846
##  [491]  1.7625871694  1.7078101567  0.3157763179  0.5130124530  0.1039718821
##  [496]  0.6027610408  1.4527619283 -0.3954470489  0.7359737095  0.5460781721
##  [501]  1.0328166920  0.8548088875  0.1342842595  1.4959260620 -0.6994041242
##  [506]  1.2781440389  0.7841166802 -0.2983073880  2.2712641342 -0.9453003536
##  [511]  0.3494255746  0.5474048224  0.3117902786  0.4112036818  0.4453708885
##  [516] -0.4272510965  1.1791291663  1.0346931367  1.0630399355 -0.1305357771
##  [521]  1.2683844946  0.7778315050  1.1308733086  0.8385535652  0.2037666607
##  [526] -0.6501644941  0.5915094859  1.3706617203  0.7722079567  0.5710177399
##  [531]  0.8682434594  0.9415706660  0.6688571996  0.7859640960  1.1726639380
##  [536]  1.3904965392  0.7447908627  0.1308164758  0.4778673765  1.6600952809
##  [541]  0.7844816097  1.0950184570  0.9027144323  0.7422706237  0.1938740671
##  [546]  1.9178551033  0.9306455755  0.5736660449  0.9536322888  0.3442913065
##  [551]  0.6537709680  0.2314641338 -0.0489899095  1.7666082611  0.8240315505
##  [556]  0.5587326526  0.4004828030  0.4259500915 -0.1940362501  0.3187326192
##  [561]  0.7020637915  1.0533527323  1.0536283606  0.4633047981  0.9639582162
##  [566]  0.1838383212  1.1871321596 -0.3490202887  0.3471567384  0.9948351646
##  [571] -0.4676300155  0.0865512830  1.7634622543  1.1268268881  1.3780767541
##  [576]  1.3942297820  1.3824471932  0.4202237995  0.0688766752 -0.1079783618
##  [581]  1.3901948148 -0.0370342935  0.6180764884  1.1148597226 -0.0497758597
##  [586]  0.7243313043  0.2474708626  1.8319818827  0.2127687948  1.5977687567
##  [591]  0.6214365162  0.6362396958  0.0365642798  1.6719460219  0.1232435767
##  [596]  1.0757242816  0.5350642448 -0.1899158346 -0.1734192878  0.2520737002
##  [601]  1.2772931261  0.6487750279  0.9082561731  0.3880329250  0.3629199745
##  [606]  0.0065318040  1.3558623902  0.2608081087  1.4605969498  0.1938676614
##  [611]  0.3362077303  0.6485019850  0.0601180747  0.7284662034  1.1338161322
##  [616]  0.5776722262  2.0852892561  1.0967615754  0.8575603762  0.6707264853
##  [621]  0.9747207213  0.9110590373  0.4941788060  1.1615248058  0.6993706883
##  [626]  0.4685811521  0.7089933904  1.7709206680  0.9711031675  0.4585076600
##  [631] -0.7619169849  1.8800070185  0.7203656218 -0.1909072137 -0.6277785561
##  [636] -0.0010318835 -0.4957316758  0.9915313248  0.1907215925  0.6438635551
##  [641]  0.4313660034  0.9536069608  1.2614365606  0.1498504354  0.5014123020
##  [646]  0.6981245309  0.0263467336  1.1067300127 -1.2502597918 -0.4736724826
##  [651]  0.1790500960  0.5735770163  0.5687158997 -0.1510234070  1.2282163388
##  [656] -0.4355829076  0.4058306953  0.5739719302  0.0002276818  1.8967009782
##  [661]  0.9367655793  0.6034188893  0.7744827513  0.5488237665  0.9803711091
##  [666]  0.1000379119  0.3105461054  0.7259551063  0.8448139609  0.1524266876
##  [671]  0.5023343655  1.3673966441 -0.0509774336  0.2965288068  0.9786005647
##  [676]  0.2621306810  0.6318857187  1.0048632851  0.3818324135  0.4371481610
##  [681]  0.9764343804  1.2120388300  0.2692503397  0.4464447032  1.0072837722
##  [686]  0.6119102100  0.4903008790  1.2960406072  1.1456517910 -0.0813841622
##  [691]  0.7087142611  0.5378593197  0.2612620033 -0.2433859600  0.2549054007
##  [696]  1.3355842476  0.7028438313 -0.1603494460  1.2925060372  0.2379834481
##  [701]  1.0381820468  0.6068242797  0.3072595627  0.7348300034  0.9304984198
##  [706] -0.0173179944  0.5891768815 -0.7544221568  0.4951265116  0.2173172612
##  [711]  1.5605372764  0.7011617878  0.2201705464  0.8940206740  0.5884790037
##  [716]  0.8582585079  1.1060119002  0.2495009565  1.0289405654  0.4318736284
##  [721]  0.2253905592  0.7052498430  0.5804688078  0.6682847088  0.3781541368
##  [726]  0.2823735550  1.4168978294  0.7428357987  0.2864009777  0.9761465957
##  [731]  1.0355774653 -0.0271537538  0.2166723183  2.2245335403  0.6942358081
##  [736] -0.5504565256 -0.0120350320 -0.5950795480  1.2917294014  0.3896176902
##  [741]  0.6240226328  1.0067975935  1.2619861727  0.4857688996  0.5821723776
##  [746]  1.0956304398  0.7814500617 -0.1028676705  0.5639478845  0.8798172306
##  [751]  0.4771060792  1.0538742924  0.7531089191  0.6528023060  1.1284970835
##  [756]  0.1488887512  1.0518749453  1.2247874612 -0.8073002924  0.5776183657
##  [761]  1.3239676226 -0.1139470250  0.2395524653  0.9944280952  0.8484502812
##  [766]  1.1768542452  0.9136699178  1.0218308609  0.5078637415  0.7140012695
##  [771]  0.1483561334  1.7127123610  0.6069552987 -0.3389942386  0.3164660357
##  [776]  1.7629937093  1.3980704405 -0.3625386468  0.4226037396  1.3381295610
##  [781]  0.7768223115  0.3776964400 -0.1901738445 -0.1732257832  0.2211112221
##  [786]  0.4140586878 -0.4886243783  0.3978355901  0.1432789066  1.2837032516
##  [791]  0.7006518553  0.8898890869  0.9894280008  0.4005191149  1.0279108123
##  [796]  0.7099188978 -0.0252528609  0.9931826930  0.3778646901  0.7053802931
##  [801] -0.3211160183  0.6000749100  0.8300300692  1.0145881291  1.1076639597
##  [806]  0.2612186161 -0.1572134755  0.7241109718  0.8017161779  0.1540424906
##  [811] -0.0856884407  0.3251602740  2.0461048422  0.8305318264  0.1838499418
##  [816]  1.2905374364 -0.4200754821  0.3328693943  0.8056662551  0.9213976799
##  [821]  1.8139184089  1.3019404036  0.0137022424  0.8024618114  0.5492792344
##  [826]  0.0290800959  0.6419106522  1.8037388845  1.3257772494  1.5122360704
##  [831]  0.4229941558  1.2550818948  0.4384797293  0.6020925850 -0.2084318708
##  [836] -0.5812901079  1.0935978331  0.4287944410  0.0492059299  1.1664869779
##  [841]  0.4414454114  0.4680198536  0.0721833029 -1.1078665672  0.8326459804
##  [846] -0.4743913413  0.8428973212  0.3240666402  0.0520688178 -0.2132835433
##  [851]  0.7528857833 -0.2069712486 -0.9740544222  1.0482437276  1.1430724190
##  [856]  0.7912534071  0.6901521489  0.3857315690  0.5527554708  0.1925184364
##  [861]  1.8654744172 -0.1229543950  0.7308922692  0.1907918850  0.1593703012
##  [866]  1.2390547652  0.8257240725  1.1557280298  0.7304422654  1.0980176974
##  [871]  2.0318556960  0.2522389579  0.1062025065  0.8478429000  0.4271123789
##  [876]  0.6104037429  1.7559639572  0.4984826532  1.0941421556  0.1694028478
##  [881]  1.6051023005  0.6128069828  0.9057251199  1.1082820724  0.7479240426
##  [886]  0.5587326526  0.3570443273  0.8939907721  0.6652974130 -0.1150937146
##  [891]  0.5812100840  1.0155450814  0.8163828358 -0.4181350000  0.6562208706
##  [896]  0.9707568969 -0.3014611741 -0.2346066605  0.4039537556  0.0230258021
##  [901]  1.2204985489  0.6239663568  0.4004132273  0.6857709534  1.1170690306
##  [906]  1.1748586746  0.2038801083  0.0101694468  0.9696132192  1.1356973428
##  [911]  0.5445974679  1.0017143972  0.7988107809  0.7769137792  0.3373502922
##  [916]  1.0770178463  0.2855983501 -0.1341210487 -0.0158659395  0.3465315400
##  [921]  1.1500472586  0.5575291105 -0.4468823664  0.2795004206  0.8066578719
##  [926]  0.5216609299  1.1321143994  0.5791060545 -0.1533370706  0.9780937801
##  [931]  0.8474994108 -0.3187816663  0.7230561370  0.2984183584  0.3726379754
##  [936]  0.1210141860  0.8360747528  0.2280018385  1.1082688861  1.0355051752
##  [941]  0.9224190766  1.2000786982  1.1306521303  1.0007593645 -0.3471664441
##  [946]  1.3884119377  0.4136803420  1.3760257176  0.7199971672  1.0954109309
##  [951] -0.1455425612  1.3370565283 -0.1532202210  0.4004981062  0.4751332398
##  [956]  0.2268833081  1.8260741003  0.9073424286  0.6868227391  0.5815189604
##  [961]  0.2632550036 -0.1127151522  0.1564872144  1.0823851065  0.1403161036
##  [966]  0.8792920673  0.4702152661  1.8148239428  1.3195946981 -0.0044242528
##  [971]  0.6518261732  0.5762539687  1.1574618563 -0.0442141193  0.6187836266
##  [976]  0.0754226797 -0.2339244481 -0.8134535386  2.0962573309  0.5381675152
##  [981]  0.5713048087  0.8350356231 -0.0415297774  0.5693084186  0.5499571463
##  [986]  1.4717380094  1.8626916143  0.5257296644  1.3327632680  0.7464909835
##  [991] -0.0713401813  0.0194195936  0.0908222271  0.8301447060 -0.2177516718
##  [996]  0.2717232104  1.4202055202  0.1953280657  0.7265495519  0.4941788060
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
##   0.60603453   0.34372572 
##  (0.10869562) (0.07685928)
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
## [1]  0.6786864 -0.7205066 -0.3098466 -0.8644542 -0.8022145  0.8860062
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
## [1] -0.0417
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8974389
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
## t1*      4.5 0.01101101   0.9386698
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 6 7 8 
## 3 2 1 4
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
## [1] -0.0053
```

```r
se.boot
```

```
## [1] 0.8927143
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

