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
## 0 1 4 5 6 7 9 
## 3 1 1 1 1 1 2
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
## [1] 0.0284
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
## [1] 2.755069
```

```r
UL.boot
```

```
## [1] 6.301731
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
##    [1] 4.0 2.9 4.0 5.2 5.6 3.5 5.3 4.2 4.6 5.0 5.5 5.9 5.5 5.1 5.4 4.6 5.2 4.5
##   [19] 3.2 4.3 5.3 5.4 3.9 6.3 3.9 3.7 3.1 4.1 3.9 4.8 3.9 5.4 3.7 5.2 6.4 4.3
##   [37] 4.3 4.4 4.7 4.1 4.9 5.2 5.2 4.3 5.5 4.4 4.4 5.4 4.1 6.3 4.2 5.8 5.5 3.3
##   [55] 3.9 4.7 4.7 4.1 5.3 6.6 4.6 4.3 3.8 5.9 5.0 3.1 5.7 5.1 5.0 3.4 5.6 5.3
##   [73] 5.6 5.1 3.6 5.0 4.2 4.8 1.9 4.1 3.7 4.0 2.9 3.6 4.1 4.1 4.9 5.4 4.2 4.7
##   [91] 5.8 4.1 3.7 3.6 4.3 3.8 5.0 2.9 3.9 3.5 3.8 5.4 3.3 3.5 3.9 4.0 4.2 3.5
##  [109] 4.5 3.1 5.0 3.8 2.6 5.7 4.8 5.0 4.6 3.9 5.8 3.8 5.1 5.2 5.4 3.5 3.4 4.1
##  [127] 4.5 5.2 4.2 4.9 4.6 4.4 4.5 3.9 5.4 6.0 3.8 5.2 4.8 3.6 5.7 5.1 4.0 3.5
##  [145] 4.7 4.5 2.9 4.2 4.3 5.1 6.1 3.0 4.2 3.9 2.8 5.2 4.3 3.6 4.5 5.3 4.0 4.7
##  [163] 5.0 4.0 2.7 5.2 4.4 6.1 5.0 5.7 4.6 5.3 3.9 3.6 3.9 3.7 5.1 4.9 5.1 4.2
##  [181] 5.8 5.2 5.2 4.8 4.7 2.3 4.1 4.7 3.0 5.1 3.8 4.5 5.7 4.3 3.8 6.6 4.1 4.8
##  [199] 4.1 5.4 3.5 4.9 4.9 4.4 5.3 5.5 5.9 4.5 3.5 2.9 6.1 4.3 4.6 4.1 5.4 4.5
##  [217] 3.9 4.1 5.1 4.9 4.2 4.3 5.2 6.4 2.8 6.4 3.2 6.1 4.7 6.2 4.6 5.5 5.0 4.8
##  [235] 3.8 4.4 4.0 4.2 4.1 4.6 4.0 4.4 4.7 4.4 4.1 3.9 4.7 5.5 2.1 4.6 6.7 3.7
##  [253] 5.0 2.8 2.2 4.8 3.5 3.1 6.3 3.4 3.9 6.0 5.4 5.2 4.4 5.0 3.6 4.0 3.4 2.7
##  [271] 4.8 4.7 3.9 4.7 5.1 3.8 4.3 5.4 3.6 5.1 4.3 5.2 5.9 3.9 5.4 3.6 2.3 4.7
##  [289] 4.7 4.3 4.1 5.0 4.2 4.1 4.3 3.5 3.4 4.2 4.2 5.0 4.0 4.1 4.0 3.9 4.7 4.6
##  [307] 4.0 3.9 5.0 3.5 5.4 4.8 5.0 3.8 3.8 5.4 3.2 3.5 5.0 5.4 4.5 6.2 4.2 3.5
##  [325] 5.4 3.9 4.8 3.4 3.4 5.6 3.2 5.5 3.4 4.0 2.7 6.4 4.1 4.4 4.9 4.3 4.2 3.1
##  [343] 4.5 4.2 5.6 5.9 3.4 4.2 4.6 4.5 5.7 5.0 4.3 6.0 5.1 3.7 5.0 5.1 2.7 3.3
##  [361] 3.9 5.3 4.6 4.6 4.2 6.2 6.4 6.0 4.4 4.1 5.6 4.3 3.4 4.8 5.4 3.5 5.1 5.7
##  [379] 4.5 5.3 5.3 2.1 4.5 4.5 4.8 3.2 4.1 3.7 4.6 4.6 4.8 4.5 5.5 3.7 5.2 3.4
##  [397] 4.8 5.8 5.1 5.1 4.7 3.1 5.0 2.2 4.9 4.5 3.3 3.3 5.3 4.6 5.8 3.6 5.2 5.4
##  [415] 4.7 2.9 5.2 2.4 4.1 4.8 4.2 4.1 4.5 3.6 4.9 4.9 3.8 4.7 5.4 2.0 4.4 3.1
##  [433] 4.5 4.2 5.2 3.8 5.4 4.1 4.8 3.5 4.7 5.7 3.9 5.4 4.8 2.9 3.1 2.2 4.7 4.7
##  [451] 4.4 3.9 5.7 3.6 5.1 4.3 4.4 4.1 5.3 5.5 4.5 3.9 5.0 3.9 5.7 5.9 3.1 5.5
##  [469] 3.6 5.8 5.1 6.0 4.2 3.2 3.7 5.6 3.9 5.5 3.5 5.3 3.2 4.2 4.6 3.9 3.3 4.6
##  [487] 4.0 3.3 4.4 2.8 5.3 4.6 2.5 4.8 4.2 6.1 4.5 4.8 4.3 3.6 4.6 5.6 4.0 6.1
##  [505] 2.5 3.4 3.8 2.8 5.5 5.5 4.8 2.3 5.2 4.1 4.0 5.7 4.8 3.5 3.0 5.5 4.4 5.2
##  [523] 4.8 4.9 4.0 5.7 3.9 3.7 3.8 5.4 5.6 4.8 3.5 4.9 4.6 4.4 2.2 4.0 5.0 6.6
##  [541] 5.8 3.6 4.6 4.7 5.7 2.9 5.9 6.2 4.8 4.6 3.9 5.7 3.5 2.6 5.7 4.4 3.7 3.8
##  [559] 4.4 4.5 6.4 3.4 3.9 6.0 4.2 4.0 4.8 3.9 5.3 3.5 5.9 5.2 3.4 3.4 3.4 5.4
##  [577] 2.7 4.2 4.8 3.4 6.0 4.6 4.6 4.0 4.6 4.2 5.0 4.8 4.3 4.6 3.9 4.9 4.1 4.8
##  [595] 4.0 5.1 4.8 3.8 4.4 5.2 4.4 4.8 4.5 4.3 5.1 5.4 3.5 3.9 5.4 4.1 3.8 5.7
##  [613] 2.9 4.0 4.7 5.5 5.3 4.2 2.3 3.6 6.2 2.6 4.3 5.2 2.8 5.7 6.4 5.6 4.5 4.6
##  [631] 4.4 5.9 4.2 4.1 4.5 5.2 5.3 4.4 2.9 5.5 3.1 4.7 6.1 5.5 5.2 4.4 4.8 4.2
##  [649] 5.3 4.3 4.1 3.2 2.7 4.4 4.4 5.5 3.9 3.4 4.2 3.1 5.8 4.3 3.9 4.8 3.3 5.2
##  [667] 4.3 4.7 3.5 4.2 4.2 3.7 3.1 3.7 5.6 2.3 4.0 5.1 5.7 4.8 4.4 5.3 4.5 4.8
##  [685] 4.2 4.3 4.3 5.6 4.1 5.3 4.6 3.9 5.0 4.7 5.4 4.7 4.6 4.7 4.3 5.7 4.1 5.0
##  [703] 2.9 4.8 4.5 3.8 4.1 4.9 5.2 3.7 3.6 5.3 4.9 5.5 3.3 4.3 4.4 4.9 4.1 5.3
##  [721] 4.8 4.5 4.6 4.8 6.2 6.4 5.1 5.6 4.2 4.5 6.0 3.1 3.2 2.0 4.5 4.9 3.9 3.7
##  [739] 4.0 4.3 4.6 3.8 4.0 3.9 4.1 3.7 5.3 3.7 4.7 6.1 5.7 4.5 3.4 5.8 5.3 6.2
##  [757] 3.5 4.1 3.5 3.8 5.2 5.0 4.9 3.7 5.1 6.0 4.8 5.4 4.0 4.3 4.4 6.6 4.4 4.2
##  [775] 4.2 6.4 5.1 5.6 5.4 4.1 5.3 3.9 4.8 4.5 6.6 2.4 4.8 4.6 4.4 3.6 5.5 4.5
##  [793] 5.3 5.7 4.6 4.4 4.5 5.0 4.6 4.8 4.9 5.5 4.3 4.5 2.9 4.6 4.8 3.3 5.0 5.3
##  [811] 5.1 2.5 2.9 3.5 6.0 5.2 3.7 3.9 4.8 4.5 3.8 5.2 5.1 3.8 5.6 3.6 3.2 4.0
##  [829] 5.6 5.5 4.5 3.9 3.4 4.7 5.0 4.6 5.1 4.9 5.1 6.5 5.1 3.5 4.1 5.2 3.1 4.3
##  [847] 3.7 5.5 6.5 3.5 4.8 3.7 3.9 3.2 5.2 4.9 4.2 3.5 3.5 4.2 4.3 3.2 3.8 4.8
##  [865] 3.3 3.5 5.2 5.2 4.5 6.1 5.1 5.0 5.2 3.7 4.4 6.1 3.0 4.7 5.3 3.8 4.3 4.0
##  [883] 3.1 5.2 4.9 5.7 5.0 3.2 4.5 2.5 3.0 3.5 4.0 4.2 3.4 5.2 5.3 4.1 3.5 3.7
##  [901] 5.0 4.9 5.6 3.6 5.4 3.5 4.4 3.9 4.0 4.8 5.3 3.1 4.7 3.1 3.4 4.0 3.9 6.2
##  [919] 4.6 4.7 4.7 5.0 4.7 4.3 5.8 5.1 4.8 3.8 3.7 5.6 4.2 4.4 4.8 5.3 5.8 6.2
##  [937] 3.9 4.9 3.2 3.0 6.3 4.3 5.4 3.1 6.7 4.2 5.4 4.0 5.6 3.1 5.4 4.0 3.5 3.5
##  [955] 5.6 5.0 5.3 2.3 4.1 4.3 4.4 3.8 4.2 4.6 4.2 5.1 4.4 4.3 4.9 3.9 4.5 4.3
##  [973] 5.5 5.1 4.3 4.9 3.4 2.7 4.5 4.2 4.5 5.1 4.9 4.8 5.3 5.3 5.4 3.3 4.2 2.9
##  [991] 5.2 4.1 3.0 5.5 3.4 4.4 4.2 4.0 3.2 5.8
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
##    [1] 4.3 3.6 6.1 4.1 4.9 5.8 4.5 7.0 5.5 5.3 3.7 4.6 4.2 4.0 3.3 3.8 5.0 3.7
##   [19] 5.5 5.9 4.1 5.1 3.7 3.9 4.2 4.5 4.6 3.5 3.9 5.3 3.8 6.1 5.6 4.5 4.9 4.5
##   [37] 3.2 3.8 4.3 3.8 5.2 4.0 5.2 5.3 4.4 5.2 4.3 5.6 4.6 3.1 4.8 4.7 4.2 4.4
##   [55] 3.5 4.5 4.7 4.3 4.0 3.7 4.1 3.1 5.8 2.3 4.8 3.4 2.5 6.0 5.5 4.8 4.8 4.1
##   [73] 5.0 5.7 4.1 5.5 2.6 6.8 6.0 3.1 4.8 3.8 4.2 4.5 3.3 4.3 4.8 3.9 4.2 3.8
##   [91] 3.2 2.6 3.3 4.0 3.6 4.4 3.0 4.9 5.2 4.5 5.9 4.9 4.5 4.5 4.2 4.0 3.2 3.8
##  [109] 3.5 5.6 3.1 6.0 3.9 3.9 6.5 5.1 5.5 3.9 4.2 2.8 3.4 4.7 5.3 4.1 6.0 5.4
##  [127] 3.6 2.9 4.6 4.1 5.1 4.8 4.0 5.2 3.0 5.6 4.0 3.7 3.1 4.2 6.0 3.0 6.2 4.2
##  [145] 3.9 4.1 4.5 4.8 3.7 4.3 4.4 4.2 4.3 4.6 4.3 4.9 5.5 4.3 3.9 3.8 4.6 4.6
##  [163] 5.0 4.2 5.8 4.7 4.4 5.6 4.5 4.3 4.9 4.5 5.3 4.7 3.4 5.8 4.3 3.5 4.7 3.6
##  [181] 3.1 4.2 5.6 4.4 5.3 4.0 3.9 3.8 5.0 4.7 3.7 3.6 4.1 5.4 3.7 4.8 5.3 4.8
##  [199] 5.9 3.8 3.8 4.2 4.5 5.7 5.1 3.6 4.1 3.7 5.1 3.8 3.8 4.4 4.8 5.2 3.0 4.3
##  [217] 4.9 3.1 4.4 4.1 4.4 4.8 3.8 4.5 3.1 4.6 4.9 5.0 3.9 5.5 4.4 5.0 5.2 3.2
##  [235] 4.2 5.6 5.5 3.8 2.8 2.9 5.1 4.7 4.8 5.0 2.2 5.1 4.8 4.8 5.7 3.3 5.0 4.3
##  [253] 2.9 4.8 5.3 5.3 4.8 4.2 4.4 4.9 4.3 4.2 5.9 5.2 3.0 4.7 3.9 6.2 5.2 2.6
##  [271] 3.9 4.7 5.6 3.7 2.6 3.7 4.6 4.1 5.0 5.0 6.0 4.5 3.6 5.1 4.3 3.1 4.4 4.1
##  [289] 4.0 6.0 4.5 4.1 3.8 6.3 4.3 4.0 4.3 5.0 5.4 4.3 4.7 4.6 5.6 2.4 2.7 4.1
##  [307] 3.6 3.9 6.2 4.5 4.8 3.6 5.5 3.3 4.4 3.8 3.8 5.5 4.7 5.7 4.0 5.2 5.1 4.5
##  [325] 3.4 4.9 3.1 4.2 3.6 5.2 3.9 3.2 5.9 4.3 5.6 3.5 3.7 3.9 5.5 5.4 5.0 3.6
##  [343] 4.8 5.4 3.5 5.3 6.0 3.8 5.7 4.7 5.6 3.5 5.7 4.5 4.6 4.4 3.7 4.8 5.7 6.3
##  [361] 5.9 5.8 3.7 4.3 3.4 4.3 4.9 5.2 3.2 4.3 4.0 3.0 6.7 3.3 4.7 3.9 4.4 4.7
##  [379] 3.8 4.1 4.7 5.5 4.9 4.2 4.0 4.2 5.6 4.5 4.5 6.0 4.5 4.6 4.4 2.7 5.0 3.9
##  [397] 4.5 5.5 3.3 2.6 3.4 4.3 5.1 5.9 3.3 4.1 2.2 4.0 5.6 5.3 5.3 4.3 4.5 3.8
##  [415] 4.2 3.6 3.8 4.7 5.3 5.2 6.6 3.5 4.7 3.9 3.7 5.2 4.1 5.2 3.2 4.9 3.9 3.1
##  [433] 4.5 7.0 5.8 4.1 3.6 4.7 5.1 4.7 4.5 4.4 3.0 3.4 3.3 6.6 2.9 4.6 5.5 4.8
##  [451] 5.3 3.8 4.0 3.7 5.2 5.2 4.5 5.3 5.0 5.5 4.1 5.1 4.6 4.7 3.3 3.2 5.3 4.0
##  [469] 3.3 5.4 4.9 5.7 4.7 3.9 4.0 5.5 5.5 4.4 4.9 3.9 5.1 4.1 5.7 4.1 3.8 4.4
##  [487] 3.9 4.0 5.3 5.0 3.8 4.5 4.0 5.6 4.5 3.6 4.0 3.4 4.0 3.6 4.4 5.0 5.6 4.6
##  [505] 3.0 5.2 5.0 6.1 4.6 4.2 4.7 3.2 6.0 4.3 3.4 3.5 4.9 3.7 5.1 5.5 5.9 4.7
##  [523] 4.0 6.3 4.5 4.8 3.4 5.3 4.6 4.8 5.2 4.9 4.1 3.6 3.9 4.4 5.7 4.4 3.5 6.4
##  [541] 4.3 5.6 4.8 3.5 4.9 4.6 4.8 3.8 3.7 2.9 4.7 3.0 2.2 4.4 5.0 4.7 4.3 5.2
##  [559] 5.4 5.4 4.3 3.1 3.4 6.9 4.5 4.8 5.0 4.9 3.5 5.4 3.5 4.8 4.5 4.5 4.1 3.5
##  [577] 3.7 4.9 4.7 4.8 3.8 5.3 4.2 4.6 4.4 3.8 3.3 5.4 5.3 4.0 6.5 2.7 4.1 4.5
##  [595] 4.5 5.6 2.3 3.8 5.1 4.1 3.5 3.1 5.3 5.4 3.1 5.6 5.2 3.3 5.3 4.9 4.3 4.0
##  [613] 3.3 4.7 5.6 2.5 4.8 5.3 4.5 4.3 4.3 3.7 5.2 4.0 4.9 5.7 4.7 5.0 4.3 4.9
##  [631] 3.4 4.5 4.5 3.3 4.0 4.4 4.6 4.5 3.9 4.1 5.0 5.2 5.3 4.2 3.9 5.3 3.2 4.6
##  [649] 4.3 5.2 2.8 3.9 4.7 4.7 3.7 3.9 3.4 4.3 4.0 4.6 3.5 6.2 5.4 3.7 5.8 4.8
##  [667] 5.3 5.0 5.1 4.4 5.2 4.0 4.1 6.2 3.8 3.7 4.4 4.7 6.1 4.5 5.0 3.6 5.4 5.8
##  [685] 3.4 4.2 4.9 3.8 3.9 5.9 4.7 3.1 6.6 6.0 3.2 3.5 5.3 4.9 4.9 4.6 4.3 4.8
##  [703] 4.4 3.3 3.4 4.8 2.7 5.4 4.2 4.9 5.5 3.3 5.6 3.8 4.8 5.2 5.1 5.0 5.0 3.3
##  [721] 4.0 4.4 3.5 4.3 3.4 3.6 5.4 3.8 3.3 5.0 3.8 3.7 5.1 3.1 3.9 3.9 4.9 3.2
##  [739] 4.3 3.6 5.2 4.6 4.1 3.0 4.6 4.6 4.2 3.7 4.9 4.8 5.9 4.1 5.0 5.3 3.9 3.9
##  [757] 4.6 2.0 3.5 3.5 4.9 2.5 3.6 4.7 4.3 3.7 4.2 5.0 4.8 6.3 4.4 5.2 5.8 5.9
##  [775] 4.8 5.1 5.4 3.6 4.6 4.8 4.6 4.8 4.4 4.3 4.0 5.7 3.5 2.7 5.0 6.7 4.7 5.2
##  [793] 4.1 4.4 5.9 4.5 3.8 3.5 3.5 4.6 4.8 5.3 3.7 5.6 2.9 6.9 4.8 3.8 5.9 4.0
##  [811] 3.8 6.2 3.9 4.2 4.5 5.2 5.0 3.4 3.9 5.8 4.6 3.1 4.8 4.1 3.7 5.2 4.8 3.7
##  [829] 4.3 5.0 4.3 4.0 4.8 4.9 3.4 5.8 5.5 4.9 5.5 5.7 4.4 2.8 4.5 4.6 4.4 5.7
##  [847] 4.4 3.3 4.7 5.2 3.8 3.3 3.2 5.7 5.4 5.2 3.3 5.1 5.9 5.6 3.6 5.9 4.3 4.8
##  [865] 6.4 4.7 5.0 3.7 5.6 2.7 4.8 4.8 4.6 3.5 3.2 4.5 3.2 3.7 3.8 4.7 3.9 5.7
##  [883] 3.9 3.8 4.4 4.2 4.1 5.6 4.4 3.6 4.6 5.0 4.3 5.1 3.8 4.0 5.8 2.8 3.6 3.8
##  [901] 6.2 3.3 4.2 6.0 2.0 3.3 5.6 5.2 4.0 4.7 6.0 5.0 5.4 3.7 4.3 3.2 5.9 3.3
##  [919] 5.4 6.0 4.0 5.3 2.3 3.5 4.0 4.5 5.2 3.6 5.4 6.3 4.1 5.9 4.6 3.0 4.8 5.3
##  [937] 4.0 4.0 4.3 5.1 5.4 4.7 5.5 5.2 5.1 3.4 4.5 4.8 4.0 5.1 4.1 3.1 3.7 3.5
##  [955] 4.3 3.4 2.0 4.7 5.3 4.7 4.6 2.4 4.9 4.6 6.6 5.0 5.1 5.2 2.7 5.2 4.9 4.8
##  [973] 4.0 5.6 3.5 5.2 4.8 4.8 5.6 6.5 3.5 3.9 5.8 5.2 4.3 5.4 4.9 5.6 4.3 3.4
##  [991] 5.6 3.4 4.4 4.2 5.2 4.4 2.0 4.8 4.4 4.7
## 
## $func.thetastar
## [1] -0.0307
## 
## $jack.boot.val
##  [1]  0.44146982  0.34281690  0.21726190  0.11616766  0.06480447 -0.08821839
##  [7] -0.24568966 -0.26890756 -0.40840580 -0.55824176
## 
## $jack.boot.se
## [1] 0.9397378
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
##    [1] 4.5 5.2 5.3 3.2 4.6 4.4 5.0 5.3 4.0 4.8 4.7 7.0 5.1 3.5 5.3 4.1 4.1 4.7
##   [19] 4.0 4.5 4.4 5.6 4.0 6.1 3.4 6.2 5.6 3.9 4.7 4.3 4.5 4.0 4.9 4.5 5.7 4.4
##   [37] 4.3 4.9 5.6 3.4 3.4 3.8 5.4 5.5 5.0 5.9 3.9 3.6 5.1 7.2 4.7 4.4 2.0 4.4
##   [55] 4.3 4.8 4.4 4.3 4.9 3.2 5.2 4.3 4.1 5.9 4.9 4.7 4.1 3.8 5.4 3.6 3.7 4.6
##   [73] 4.8 3.7 6.3 4.7 2.8 4.1 4.6 5.7 5.4 4.4 6.1 3.5 4.3 4.4 5.0 4.1 3.8 5.6
##   [91] 3.5 4.3 3.8 5.1 5.6 4.9 6.2 4.7 5.5 4.3 4.9 5.6 4.2 2.9 3.2 6.4 4.1 4.0
##  [109] 4.1 5.7 5.0 4.4 4.2 6.9 4.5 6.3 4.9 5.0 2.9 5.6 4.6 3.8 4.6 5.5 4.6 6.0
##  [127] 4.6 5.5 4.4 3.4 4.2 6.9 4.9 4.3 4.9 4.1 4.4 5.3 3.7 5.1 4.1 5.6 4.7 5.1
##  [145] 3.4 4.8 3.7 5.8 5.9 5.3 3.0 3.7 5.3 4.5 5.1 4.7 4.2 3.8 5.7 4.6 5.0 4.1
##  [163] 4.1 1.8 5.4 4.1 3.4 5.0 3.5 3.5 4.3 3.8 4.0 5.2 4.7 5.3 5.2 5.3 3.6 4.7
##  [181] 3.8 4.9 4.0 5.4 3.9 6.3 5.3 4.1 5.5 4.6 5.1 3.7 4.4 5.4 4.9 4.5 4.2 4.6
##  [199] 4.9 6.3 4.6 6.2 3.9 4.7 4.9 6.0 4.0 5.8 6.0 4.5 4.0 7.0 3.5 3.5 4.0 4.7
##  [217] 5.9 4.3 4.5 3.0 3.4 5.0 6.8 5.6 5.3 4.0 6.0 4.9 4.6 4.9 5.6 3.9 3.9 5.7
##  [235] 5.4 5.0 6.4 2.8 4.6 4.9 5.4 4.4 5.0 6.5 3.2 3.3 3.5 5.0 2.8 5.6 5.9 5.6
##  [253] 3.2 2.7 5.4 3.5 4.7 5.9 2.0 2.7 5.1 4.8 5.1 5.3 3.6 4.5 4.8 5.6 3.5 3.8
##  [271] 5.3 5.5 4.6 5.6 4.4 5.1 3.1 5.0 4.3 5.6 2.6 4.7 6.0 3.3 5.1 5.2 4.1 4.8
##  [289] 4.6 5.8 4.4 4.9 4.8 4.4 4.8 4.1 4.5 4.3 3.6 4.0 5.0 5.8 5.4 3.1 3.7 5.7
##  [307] 4.6 5.6 4.3 3.2 4.7 6.1 5.8 5.3 4.4 5.0 2.9 2.6 4.8 3.8 2.5 4.8 5.4 4.1
##  [325] 3.6 3.3 5.0 5.1 3.3 4.1 3.4 5.5 4.1 4.7 5.4 5.6 3.4 5.0 3.8 3.2 4.9 4.9
##  [343] 3.0 5.0 3.8 5.5 3.5 4.4 4.3 3.5 3.9 4.0 4.5 4.0 6.4 3.6 5.5 4.4 3.7 4.0
##  [361] 4.6 4.4 4.4 4.7 4.7 3.8 4.3 5.4 4.5 5.5 4.2 5.6 4.3 4.4 3.7 2.8 4.3 4.6
##  [379] 4.5 5.7 4.3 6.0 3.7 3.6 4.0 4.3 5.1 4.1 3.8 2.6 3.7 5.2 5.4 4.5 3.8 3.5
##  [397] 4.6 4.3 4.3 6.3 4.2 5.5 4.1 3.7 4.9 5.7 3.5 4.8 5.2 4.5 2.6 6.0 4.6 2.7
##  [415] 4.3 4.4 2.9 4.9 3.9 3.9 5.8 3.8 6.1 3.0 3.8 4.4 5.2 3.8 3.0 4.2 6.4 3.9
##  [433] 4.6 5.2 5.1 4.9 2.9 4.6 5.4 3.9 2.6 6.7 4.1 5.7 4.8 5.7 4.7 3.1 5.7 4.0
##  [451] 4.4 3.9 2.9 4.7 5.3 6.2 3.5 4.9 5.8 4.7 5.4 3.9 5.7 4.4 5.5 4.1 4.8 4.4
##  [469] 4.3 5.5 6.1 5.0 5.2 4.7 2.2 3.0 3.7 5.5 5.0 4.9 4.4 5.2 5.4 3.8 5.2 6.0
##  [487] 4.6 5.5 4.7 6.1 4.9 4.8 4.9 4.2 4.9 4.4 5.1 6.4 3.5 5.1 2.8 3.8 4.0 5.4
##  [505] 4.6 3.3 4.4 4.5 3.7 5.0 6.1 4.9 3.4 4.8 5.1 4.9 4.4 3.3 4.7 4.4 2.7 3.6
##  [523] 4.1 4.7 5.3 4.5 6.3 3.0 5.2 4.4 4.4 2.2 3.9 4.1 4.5 4.2 4.2 4.0 5.7 3.9
##  [541] 3.1 4.7 5.4 4.3 3.7 6.3 5.8 3.2 4.9 3.9 4.3 5.1 5.0 5.1 4.0 5.4 4.4 3.4
##  [559] 6.2 4.4 4.7 5.2 4.8 5.0 2.5 3.7 4.0 4.0 4.5 5.0 5.2 5.5 3.7 5.4 4.6 4.5
##  [577] 5.0 3.9 5.1 4.6 4.5 5.2 5.2 5.1 3.6 3.1 3.3 5.1 4.4 3.9 4.0 4.2 4.7 4.1
##  [595] 4.2 4.4 3.2 4.7 2.3 4.7 4.7 4.4 3.7 5.0 4.8 5.1 3.5 3.3 5.3 7.2 5.8 2.5
##  [613] 5.2 5.8 4.3 4.9 4.5 6.3 5.0 4.4 4.5 4.5 4.2 6.3 4.6 5.1 4.7 3.1 3.5 4.7
##  [631] 5.8 4.2 3.2 4.4 3.3 4.6 4.7 4.8 4.5 4.9 4.5 3.7 3.2 5.6 3.4 5.0 5.0 4.5
##  [649] 5.6 2.8 3.6 4.3 5.3 4.1 5.1 3.2 6.0 3.8 3.3 5.1 3.6 3.7 3.9 4.9 4.9 4.2
##  [667] 4.2 3.9 3.4 5.0 2.1 4.8 6.5 3.5 4.9 5.4 5.4 5.1 2.7 3.4 4.6 3.9 5.1 4.8
##  [685] 5.1 6.1 4.1 4.8 2.6 4.4 4.0 3.6 4.6 4.9 5.8 2.7 4.2 4.6 4.1 5.2 4.8 5.3
##  [703] 5.7 3.7 4.1 4.0 5.9 4.9 4.7 5.4 4.6 5.4 4.2 4.1 3.6 3.0 4.0 5.2 4.4 4.9
##  [721] 3.6 4.7 4.4 3.9 4.6 5.4 2.8 5.5 3.8 5.7 4.0 5.1 4.6 4.5 4.3 4.6 3.5 5.0
##  [739] 4.3 5.2 3.7 3.2 4.9 4.8 2.7 4.0 5.3 6.1 4.8 2.3 4.1 3.3 3.8 4.3 3.8 4.9
##  [757] 4.5 4.6 5.0 4.8 3.3 4.8 4.5 2.8 3.4 4.9 4.9 4.8 4.9 4.0 4.7 5.7 4.6 3.6
##  [775] 3.9 2.7 4.9 4.0 5.9 5.5 5.1 4.6 3.7 4.9 4.5 5.7 4.5 5.7 4.9 2.9 4.3 4.3
##  [793] 3.1 4.1 4.4 3.8 2.7 3.4 6.2 5.1 4.1 5.4 5.4 5.0 2.6 3.5 6.2 3.8 3.3 4.2
##  [811] 3.2 4.6 5.3 4.3 3.2 4.0 5.0 4.4 4.1 4.0 4.9 3.8 5.2 4.4 4.2 4.8 5.2 4.5
##  [829] 5.4 5.2 4.5 5.6 3.2 5.1 5.7 2.4 3.8 4.7 5.5 4.1 4.4 3.2 3.0 5.5 3.5 3.5
##  [847] 5.0 4.7 4.9 3.5 4.0 4.7 3.4 5.7 5.5 3.8 4.8 5.0 3.5 4.9 4.5 5.2 5.7 5.2
##  [865] 5.2 5.2 5.4 5.2 4.9 3.1 4.4 3.9 2.3 3.1 4.8 4.4 5.5 3.3 4.8 4.7 5.1 3.5
##  [883] 4.3 2.6 4.2 3.8 6.6 4.4 5.3 5.6 3.6 4.8 4.4 5.5 4.3 5.2 5.1 3.7 4.9 5.2
##  [901] 2.2 5.1 4.3 4.4 5.5 4.1 4.4 3.3 5.0 3.1 4.5 3.7 3.5 4.8 3.2 4.6 2.4 4.5
##  [919] 5.0 5.3 5.0 3.2 6.2 3.6 5.5 4.7 3.0 4.6 4.5 4.6 4.6 5.6 3.2 4.5 5.3 4.0
##  [937] 5.6 4.0 4.6 4.7 3.4 5.0 3.4 4.2 3.6 6.4 4.8 4.8 3.9 4.1 5.2 4.9 3.6 3.9
##  [955] 4.9 5.8 5.0 4.2 4.0 4.2 3.6 5.1 3.8 4.7 3.9 4.2 3.5 5.0 5.6 5.8 4.2 4.3
##  [973] 4.3 4.9 4.4 5.5 4.3 4.4 5.5 6.1 5.1 5.0 2.5 3.4 4.1 4.5 4.8 4.5 5.1 5.2
##  [991] 5.2 4.2 5.4 4.7 4.0 4.9 3.9 4.4 5.5 4.9
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.300 5.204 5.192 5.100 4.900 4.900 4.624 4.600
## 
## $jack.boot.se
## [1] 0.8787573
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
## [1] 1.596471
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
##   2.1410544   3.8887167 
##  (0.8929867) (1.8267037)
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
## [1] -0.3153722  0.5742268  0.7835729  0.7446198  1.0550224  0.4124881
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
##    [1]  2.164787207  1.180092821  1.577281822  2.063161537  1.340237364
##    [6]  0.941627508  0.282079615  0.233327335  1.949247815  1.886015689
##   [11] -0.053774869 -0.921038572 -0.106532707  0.971290153  1.493235383
##   [16]  1.615048678  1.232622391  0.734508552 -0.216483361  0.146520780
##   [21]  1.954633775  2.059759156  2.143903799  0.846940089  0.020969737
##   [26]  0.714416391  1.825884246  0.152723424  2.012418740  1.198845309
##   [31]  0.165031271  1.489382134  1.654092332  1.434998563  1.276163128
##   [36]  1.266905020  0.101413269  0.548200272  1.248844755  1.142393881
##   [41]  2.005061282 -0.159980593  0.191880922  0.015891556  1.370063818
##   [46]  1.973912305  1.386800974  1.799673404  0.011240364 -0.267891837
##   [51] -0.559957548  0.198468858  2.010169669  1.025995328  0.553042416
##   [56]  1.813737300  1.817618328  0.687096565  1.091838100  0.247661338
##   [61]  1.151757431  1.706814707  0.293630158  1.369717636  1.007510885
##   [66]  0.485731923 -0.578846967 -0.631988246  1.001221351  1.063993848
##   [71]  0.983926393 -0.502390364  1.575606371  0.016432336  0.644528166
##   [76] -0.229667167  0.455690841  1.727119192  2.227050818  1.126961895
##   [81]  0.524474169  1.753492857 -0.491569521  1.475155444  0.969776724
##   [86]  1.422574940  2.059136257  0.504294811  0.214010070  0.509559229
##   [91] -0.274564924  0.378096097  1.811768774  0.237092301  1.106748665
##   [96] -0.475458666  0.713486933  1.671147143  0.553680732  0.302873915
##  [101] -0.006154741  2.014664050  0.208395984  1.929421735  1.815376598
##  [106]  1.602970092  1.071747441  0.629611900  0.997010200  1.007635482
##  [111]  1.816617348  0.975859776 -0.012598240 -0.315813305  0.916346788
##  [116]  1.192454677  1.819725921  1.669935680 -0.329755563  1.334611267
##  [121]  2.030176929  0.557463571  0.677115004  1.627215521  1.056223267
##  [126]  0.230019517  1.436883207  1.803064866  0.541950459  1.230911160
##  [131]  1.659932524  0.633799800  0.295382027  1.632275469  1.870466134
##  [136]  1.497226112  1.755697026  1.013749159  0.284458714  1.148560815
##  [141]  1.256171421  1.362042571  1.758408525  0.749741749  1.845601111
##  [146]  1.049911884  1.819318336  1.847444335  0.258140571  2.049265310
##  [151]  1.803044374  0.141271879 -0.217176449  0.764501134  1.927857464
##  [156]  1.115631167  1.660242648 -1.033387637  0.341955227  1.241898941
##  [161]  2.095479824  1.197338499  1.182513721  1.609404228  1.388479874
##  [166]  1.493944325  0.914504090  1.014155373  2.094918952 -0.060906302
##  [171]  0.336704831  0.963232028  1.481991088  1.215008837  0.662440375
##  [176] -1.034504754 -0.487274494  1.774635563  0.809774099  2.060429840
##  [181]  1.819329910 -0.249911402  0.558094748  0.767101004  1.721421642
##  [186]  1.846763927  1.627978436 -0.063723468  0.663966226  0.601319732
##  [191]  1.328748277  0.902011914  1.189420415  0.326801093  1.418411819
##  [196]  0.269535899  1.181025758  1.650934583  2.131956401  1.576796400
##  [201] -0.699267045 -0.375409851  1.025401701  0.997270807  1.645633398
##  [206]  1.849941092  1.561350892 -0.813767622  1.436755602  1.286351745
##  [211]  0.169956725  2.016992236  0.216748151  1.081181567  0.965512402
##  [216]  1.533270801  1.882165915  0.904735626  0.886027679  1.792929718
##  [221]  1.658373426  1.241175702 -0.781238453  1.948916881  1.590117700
##  [226]  1.842672842 -0.157399607  0.528137415  1.428062934  1.475982996
##  [231] -0.002812212  1.768027280  1.641100736  1.943741262  0.992790531
##  [236] -0.050319148 -0.788618045  2.005379181  0.863021671  1.005737664
##  [241]  0.519316166 -0.507323156 -1.482786175  1.132359469  1.687954608
##  [246]  0.704766690  1.411269200  1.651202990  1.833625183 -0.154433720
##  [251]  0.741216315  2.344874249 -1.319265590  1.548994707  1.106091157
##  [256]  2.063288230  1.216936008  1.026605883  1.440646518  0.448058870
##  [261]  0.597113957  0.780183419  0.995078802  1.537611559  1.236265971
##  [266]  1.165640306  2.085493625  0.697673255  0.903000230  1.069559204
##  [271]  1.440646518  1.478532439  1.683349522 -0.133505945  1.762010492
##  [276]  1.065404096  0.538641724  1.662872538  1.340288488  0.614630965
##  [281]  1.179337159  0.656632595  1.713106154 -0.413290548 -0.344675155
##  [286] -0.138394755  0.326919311  1.784727250  1.074569829 -0.098137166
##  [291]  1.315994672  0.531249026  0.467200798  1.610018236  1.249750300
##  [296]  1.968633094  0.082580008  0.600398046  1.274828833  1.574060949
##  [301]  1.078965223  1.178290832  1.125448489  1.721725447  0.181918348
##  [306]  0.534475852  1.193143426  2.034060246  0.271862699  0.059230378
##  [311] -0.550609132  1.480682439  0.105957259  1.561830527  1.048913207
##  [316] -0.459108102 -0.116538985 -0.060158034  2.078872591  0.768810013
##  [321]  1.722862697  0.685458650  2.058321931  0.062990329 -0.082134865
##  [326]  1.350670066  1.456591911  2.234555583  1.840772048  1.091072136
##  [331]  1.130635048  1.425469672  0.447500844  0.579380472  1.213451859
##  [336]  1.316825096  1.658045410  0.695554162  2.017388848  1.465094452
##  [341]  1.104803582  0.083177699  1.548917024  0.028100479  2.192840387
##  [346]  0.186223430  0.034996930  1.154608535 -0.594926469  1.781593814
##  [351]  0.220220374  1.178885368  1.303893791  1.088647334 -0.154219146
##  [356]  0.206942615  0.854272836  0.877360972  1.135339040  1.076906344
##  [361]  1.412450067  0.930597867  1.089446459  1.052369272  0.330478999
##  [366]  0.852839356  0.476977737  1.897507774  0.773108829  0.708058664
##  [371]  0.260052784  0.480140642  0.538578170 -0.030544197  1.002753881
##  [376]  1.254827339  0.534124357  2.269612592  1.060434592  1.445920591
##  [381]  0.533402096  0.650528923  1.399249504  1.875719654  1.238371240
##  [386]  0.327774517  1.221346612 -0.341775360  2.120956187  0.503657747
##  [391]  0.624153484 -0.369767257  1.240209621  1.845385053  1.119782220
##  [396]  1.567429653  1.614645541  1.432525657  0.950483398 -0.161781312
##  [401]  0.776056837  1.215085437  0.835436604  0.163485017 -0.109318207
##  [406] -0.063495894  0.975380673  1.647351071  0.614722938  0.088530130
##  [411]  1.085795587  1.433230987  1.804134490  0.884885870  0.546453076
##  [416]  1.144790565  0.362379631  1.481905562  0.659312423  1.125035409
##  [421]  0.839764783  1.115856618  1.039801842  1.273339039  1.669225967
##  [426]  0.036816838  1.112229913  1.730529605  1.098147540  1.676389428
##  [431]  1.172944879 -0.100833452  1.306689428  1.899119302  0.693678778
##  [436]  1.304477988  0.797862451  1.348956321  1.141514007  1.786710514
##  [441]  1.297798175  1.664936029  0.730515272  0.718889752  0.595766386
##  [446]  2.030925195  0.288751678  1.023481568  1.103854501  0.264105465
##  [451]  1.297253922  1.500958150  1.186870684  0.951225522  0.913866886
##  [456]  2.020490384 -0.276869240  0.540435796  0.589568980  0.664034334
##  [461]  0.050566544  1.236422429  1.738130899  2.212947114  0.750939068
##  [466]  0.589076366  1.778018636  1.686182989  1.475982996 -0.475496771
##  [471]  0.725330624  1.627646941  1.784441966  1.098316750  1.342117193
##  [476]  1.099307842  0.064190036  0.432747937  0.607237740  1.778016670
##  [481]  0.961303609  1.259663422  1.622359491  1.184509450  1.718187796
##  [486] -0.610250147  1.264740092 -0.271371988  1.208474583  0.161059821
##  [491]  0.643236180 -0.836144823  0.982038549 -0.785609943  1.917739495
##  [496]  0.546086598  0.313977998  1.625328896  0.628493704  1.566559172
##  [501]  0.952609554  1.202067948 -0.729767076  0.267498635  1.063282392
##  [506]  1.202337986 -0.097112589  1.344070511  0.380991596 -0.692077232
##  [511] -0.166833006  0.662317893  0.349204239 -0.081820166  1.147154268
##  [516]  1.692385634  1.347757814  2.038596165 -0.686779486  0.220276246
##  [521]  0.533874811  0.040465874  1.256333612  1.031106778  1.226446072
##  [526]  0.923750458  2.023942813  1.802557014  0.753751567  1.882809111
##  [531] -0.130954412  1.117821457 -0.343506192  0.597892625  1.351865988
##  [536]  1.336782893  1.214051227  0.282342246  1.098396553  1.669044711
##  [541]  0.992159228  2.054211353  0.719442170  2.110177079 -0.556833438
##  [546] -0.134433363  0.730005888  0.704094762  0.050417554  0.773132092
##  [551]  1.369610459  0.179949096  1.015420198  1.494742553  1.706217924
##  [556]  1.151743935  0.360745844  1.464585578  1.619107507  1.405497410
##  [561] -0.454363006  1.973062921  0.422988839  0.631033757  0.822351937
##  [566]  1.415170913  0.703994970  0.757882178 -0.031901801  1.584878798
##  [571]  1.701151356  1.418611919  0.012145948  0.587712805  1.043600026
##  [576]  1.408616458  1.985228596 -0.652351291  0.485960367 -0.665467720
##  [581]  2.158202865  1.188663088  0.314057077 -0.162510383  1.071429252
##  [586] -0.176610782  1.025540953  0.602419533  1.781593814  1.101934999
##  [591]  1.172573572 -0.138449129  0.746000834  0.608793460  0.839099289
##  [596] -0.069761738 -0.101688709  1.856918864  1.254230134  0.147624278
##  [601]  0.813806499  2.077198285  1.216626088  0.114902909 -0.264081887
##  [606]  1.226670529  1.623083446  1.592290997  1.017629105  0.163557234
##  [611] -1.046714740  0.795251969  0.427311459 -0.160562758  1.264546772
##  [616] -0.045039562  2.085622762  1.627646941 -0.773861849  1.458551568
##  [621]  0.255237838  0.586601294  1.661589864  0.942794098  2.064563754
##  [626]  0.499206947  0.998147205  1.621887292  1.527436737  0.526251034
##  [631]  1.419173542  1.250706756  1.215017446  0.281668371  1.433230987
##  [636]  1.555134835  0.571845607  0.054711814  1.796367503  1.830071922
##  [641]  1.078903591  1.086412080 -0.239854530  0.700524666  1.059042258
##  [646]  1.930548174  2.311874449  1.735390529  1.825543723  2.284308528
##  [651] -0.378539604  1.832006251  1.162081685  1.166080060  0.535404954
##  [656]  0.135299760  0.318924294  1.472534880  0.228444784  1.613266597
##  [661]  0.835996763  0.525123695  0.088903425  0.517729843  1.867070148
##  [666] -0.402808353 -0.105045420 -0.871742936  0.546723542  0.107005630
##  [671]  0.917399745  0.614924303  1.159697028  1.545053081 -1.648958798
##  [676] -0.081616500  1.236057729  1.612292871  2.175036392  1.004970377
##  [681]  0.077302589  0.222303337  1.603789748 -0.979733312  1.151668906
##  [686]  1.233546216  1.198572905  0.587467311 -0.195548891 -0.341096824
##  [691]  1.888086363  1.914216740 -0.785434091  1.302562733 -0.015766882
##  [696]  1.328081756  1.827532585  1.058211254  1.093066816  0.889646160
##  [701]  1.657054150 -0.173647986  0.286942149  1.685934980  1.546801665
##  [706]  0.789499871  0.324252921  1.262500742 -0.381129961  1.217311928
##  [711] -0.497747153  0.246431201  0.679301325  0.343946951  1.245500549
##  [716]  1.471161428  1.798502680  2.080197247  1.735609380  1.717935989
##  [721]  0.166241712  1.720180690  1.895499487  1.321693596  1.001141585
##  [726] -0.948834758 -0.388488048 -0.533291690  1.819725921  0.918088591
##  [731]  1.161462545  1.297798175  1.143494252  0.505241009  0.435996568
##  [736]  0.739605944  0.601753450  1.047292727  1.124984740  1.576581748
##  [741] -1.421255021  0.981079286  2.012361571  1.388186751  1.265751754
##  [746]  0.848167871  1.481829517 -0.271927133  1.388565590  2.075436811
##  [751]  1.074236326  0.430792317  0.593344610  1.441378959 -0.093552931
##  [756]  1.598177605  1.489382134  1.444540839  1.261763861  2.008897347
##  [761]  0.913178900 -0.132090710  1.577491246  2.542605345  1.827532585
##  [766]  1.150217133  0.169956725  1.219241728 -0.266092938  0.153627289
##  [771]  1.904981456 -0.151618976 -0.252442082  1.639954801  0.544342629
##  [776]  1.687124344  1.920673059  0.925102002 -0.697271075 -0.391154464
##  [781]  1.898050188  0.609009240  1.897444447 -0.135666315  0.471105911
##  [786]  1.116065059  0.614640068 -0.855355077  1.456835700  1.274272899
##  [791]  1.174463499  1.669655894  0.252693961  0.212884282  1.559348781
##  [796]  0.623188911 -0.402997010 -0.152957214  1.914425099 -0.183719766
##  [801]  0.536418242  2.025276929  0.981179757  0.407893463  1.300892841
##  [806]  0.483616241  0.637787527  0.555217810  0.593105190  1.181269083
##  [811]  1.542697584  1.993056893  0.550736902  1.308449316  1.060434592
##  [816]  1.402335527  0.612146434  1.491089188  1.815003861  1.328926015
##  [821]  1.608821304 -0.497850690 -0.167839353 -0.087623782  2.010173533
##  [826]  1.102023009  0.103213626  1.210600634  1.098768756  1.544325201
##  [831]  0.813787302 -0.142730996  0.639191529  0.541253443  1.202989982
##  [836]  0.612856889  1.826975470 -0.777418583  0.695000284  0.678850857
##  [841]  0.453620828  0.979289570  1.182302889  1.411351659  1.153929903
##  [846]  1.791042629 -1.099968360  1.664318333  1.819746842  1.123435746
##  [851]  1.146391934  1.108756442  1.826566051  1.697775197  1.752399296
##  [856]  1.009335087  0.671319001  2.069392596  1.469476272  1.551199061
##  [861]  0.625519076  2.377827232  0.590117194  0.588910766  0.835796822
##  [866] -0.173684290  1.110801613 -0.725879411 -0.473821559  1.218298404
##  [871] -0.063698785  1.315544189  0.384493659  1.929365368 -0.537879125
##  [876]  1.536834286  1.140033069  0.819348478  0.399726668  0.314675199
##  [881]  1.330200833  1.613357763  1.662932876  1.578454314  0.146463925
##  [886]  0.280185621  1.828755204  0.484633201  0.084876577  1.360690590
##  [891]  1.827626265 -0.701879912  1.126927723  1.710488943  1.110325988
##  [896]  1.963851575  2.170721434  1.741623985  2.412557073  1.243507192
##  [901]  1.358516154  1.139063463  0.280954278  1.031332198  0.987327910
##  [906] -0.205948866  1.278604594 -0.171563603  1.798074383  0.924243725
##  [911]  1.434277215  1.875137645  1.669248163 -0.338582235 -0.023068594
##  [916]  1.146880572  1.148096828  1.551478671  1.351924664  0.015641312
##  [921]  1.648177183  1.702510999  0.564805229  0.886451897  1.896677505
##  [926]  0.606438068  1.839405451  1.874343671  1.695075841 -0.318176862
##  [931]  1.222851327  0.014957821  1.194983381  1.335650013  0.297478122
##  [936]  1.394857877  1.633325733  1.202251614  0.909369380  0.955488017
##  [941]  1.847206312  1.190860075  0.034863962 -0.017866104  1.642705697
##  [946]  0.880892905  0.614097677  1.107206418  1.921539644  0.399280320
##  [951]  1.042813345  0.978558786  1.137051893  0.883619238 -0.361335335
##  [956]  1.614412074  1.845683909  1.028150241  0.105672672  1.237858974
##  [961]  1.107800675  0.850221098  1.436657417 -0.161781312 -0.038586111
##  [966]  0.033017630  1.847033181  0.735144360  0.116021971  1.712399670
##  [971]  1.008911358  1.829619964  1.737227721  0.088796647  1.592510004
##  [976]  0.748940178  1.216626088 -0.715388404  0.228601076  2.364039409
##  [981]  0.619912502  0.669452253  1.695866987  0.153627289  1.442300658
##  [986]  0.080710585  2.229337681  1.144276452 -0.030787444  1.878338867
##  [991]  1.103574338  1.188884387  0.678246868  0.513013515  1.167772985
##  [996]  0.449893708  0.194643489  1.304124622  0.737940258  0.987481656
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
##   0.55058143   0.40812905 
##  (0.12906174) (0.09125855)
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
## [1] -0.2882878 -0.9149848 -0.1176331 -1.3586700  0.1573168 -1.2417392
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
## [1] 0.0208
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9169013
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
## t1*      4.5 -0.02452452   0.8756155
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 4 5 7 8 9 
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
## [1] 0.0477
```

```r
se.boot
```

```
## [1] 0.9162938
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

