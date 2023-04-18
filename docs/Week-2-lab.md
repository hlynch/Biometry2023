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
## 1 2 3 5 6 7 8 9 
## 1 3 1 1 1 1 1 1
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
## [1] -0.0357
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
## [1] 2.668535
```

```r
UL.boot
```

```
## [1] 6.260065
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
##    [1] 4.7 6.3 6.0 3.8 2.8 4.6 3.4 4.2 4.4 3.8 5.6 3.6 3.6 2.5 5.8 4.9 4.9 5.6
##   [19] 4.4 5.6 4.8 5.7 5.2 3.2 5.0 3.9 5.0 5.8 4.9 5.4 4.2 3.9 3.3 6.0 5.4 4.3
##   [37] 4.7 3.7 6.8 5.5 3.7 6.3 4.7 5.5 4.9 3.6 5.1 5.3 3.8 3.2 4.7 4.8 5.6 4.6
##   [55] 4.8 4.2 3.6 6.0 4.5 6.4 4.5 5.6 5.9 3.4 4.9 6.6 5.1 4.8 6.1 4.0 4.4 4.8
##   [73] 4.6 4.9 5.3 3.3 4.5 5.9 3.5 3.7 6.4 4.5 6.3 4.6 4.8 5.4 6.0 5.2 3.7 3.5
##   [91] 4.6 4.2 4.6 2.8 3.1 4.9 4.1 4.9 4.1 4.6 4.5 4.4 5.8 3.8 3.2 4.4 5.0 4.5
##  [109] 5.0 6.4 4.1 3.1 3.8 6.0 4.1 4.6 3.4 5.3 6.0 6.1 4.5 5.1 4.1 4.2 4.3 4.3
##  [127] 5.2 3.3 4.4 3.6 4.8 3.5 6.5 4.2 4.1 4.1 4.0 4.5 4.9 4.8 4.3 4.7 5.3 4.1
##  [145] 2.3 4.1 3.4 5.5 3.8 4.4 2.7 4.6 4.0 3.6 3.0 3.4 3.1 3.6 3.7 3.7 5.0 4.9
##  [163] 5.4 3.6 4.5 3.0 6.2 5.6 3.6 4.9 3.7 5.9 5.3 4.7 4.0 4.4 4.7 2.7 5.0 6.2
##  [181] 4.8 3.1 4.7 4.1 3.8 4.0 4.8 6.3 5.2 4.8 4.1 5.0 4.1 3.0 4.4 5.2 2.7 2.9
##  [199] 5.9 5.0 4.8 4.1 4.5 3.4 5.7 5.1 4.4 3.5 4.7 5.1 4.8 3.5 5.7 5.0 4.3 4.5
##  [217] 4.8 3.9 3.4 5.3 4.8 5.5 3.8 3.8 5.4 3.5 4.5 3.0 3.3 4.9 5.2 3.0 5.6 6.0
##  [235] 4.8 4.2 3.8 3.4 5.2 5.0 5.3 4.5 5.6 4.0 6.5 4.6 3.8 5.6 4.5 4.5 3.6 5.1
##  [253] 5.3 4.7 3.9 4.4 2.8 5.1 3.2 3.6 3.7 4.7 5.3 5.5 4.2 4.6 4.7 5.6 3.6 4.3
##  [271] 4.0 4.4 4.6 5.9 4.5 4.9 5.9 3.3 6.6 6.0 4.6 3.4 4.5 3.3 3.6 5.8 3.8 5.3
##  [289] 4.1 3.1 3.3 5.8 4.2 5.1 3.5 4.8 4.0 4.2 4.9 4.9 4.2 5.2 4.2 4.4 3.9 4.0
##  [307] 5.7 3.5 3.5 4.5 4.5 5.6 3.0 4.7 4.5 5.2 4.5 4.4 5.3 4.7 4.6 4.5 4.6 4.3
##  [325] 3.8 3.9 4.2 2.7 4.8 4.6 4.8 3.8 3.7 5.5 5.3 4.0 5.6 6.0 5.7 3.0 4.3 4.4
##  [343] 5.5 5.2 4.1 4.1 5.5 6.8 2.4 4.4 4.4 5.2 4.0 4.0 4.6 3.7 4.2 5.5 4.5 5.0
##  [361] 4.7 4.7 4.3 5.0 4.3 4.9 4.0 4.3 5.9 4.7 3.1 4.8 4.8 5.2 5.6 3.1 4.5 5.6
##  [379] 5.6 5.5 3.3 5.0 5.4 5.2 4.1 5.0 4.4 4.9 2.6 3.6 3.4 6.2 4.0 5.3 6.5 4.5
##  [397] 4.7 5.9 3.4 3.6 3.0 5.0 5.4 4.1 5.5 3.7 5.3 5.0 5.2 4.2 5.7 4.6 3.1 4.2
##  [415] 3.8 4.0 4.4 5.6 4.3 6.3 6.0 5.9 4.1 6.5 3.4 5.3 4.6 5.7 4.4 3.2 3.6 7.1
##  [433] 3.3 3.9 4.1 4.2 4.9 3.9 4.3 5.8 4.5 4.1 4.0 3.2 3.8 3.0 2.8 3.5 3.9 3.8
##  [451] 3.6 3.8 5.3 4.2 5.4 3.4 4.0 4.7 2.8 4.1 4.5 5.8 3.0 4.7 5.5 4.3 4.2 7.3
##  [469] 4.8 5.0 4.6 4.4 3.7 4.5 2.5 4.7 4.6 3.4 3.0 3.9 4.9 4.5 4.9 5.6 4.5 3.7
##  [487] 4.1 4.1 3.6 4.8 4.6 5.4 5.8 4.9 5.3 5.6 5.4 4.6 3.9 6.2 6.5 5.1 3.6 3.8
##  [505] 3.7 5.1 5.6 3.2 4.2 3.9 3.9 4.7 3.0 5.1 4.9 5.3 3.8 5.4 3.1 4.7 2.6 4.2
##  [523] 3.4 4.2 4.4 3.3 4.5 4.2 4.2 5.1 3.4 3.4 4.9 5.3 5.4 5.2 5.2 4.4 4.5 2.6
##  [541] 4.9 4.6 4.0 3.9 5.4 4.0 4.7 4.3 4.5 6.5 2.8 4.8 4.8 3.8 6.1 3.3 5.2 5.1
##  [559] 5.2 5.3 3.3 4.2 5.2 4.5 4.2 2.3 6.1 3.9 3.8 4.5 4.9 4.5 3.8 4.0 4.7 4.4
##  [577] 6.2 6.2 4.6 4.3 4.2 4.5 4.4 4.4 4.0 3.3 4.2 4.5 3.3 3.5 4.6 4.5 3.9 5.1
##  [595] 3.6 4.5 4.0 3.3 5.8 4.1 5.4 5.5 5.0 4.5 3.8 6.0 4.0 5.7 4.8 5.8 3.7 5.3
##  [613] 4.1 4.5 4.3 5.0 4.5 4.4 2.7 4.8 5.5 4.5 5.1 5.0 4.4 4.4 4.0 4.2 3.7 2.4
##  [631] 4.8 3.3 4.6 4.6 4.4 3.9 5.3 3.8 4.4 3.2 4.0 3.6 5.8 5.9 5.1 5.3 3.6 3.2
##  [649] 4.7 3.9 4.8 3.4 4.3 5.5 5.9 3.2 3.1 4.9 2.9 4.0 3.2 4.1 5.7 4.2 4.9 5.0
##  [667] 2.9 3.7 5.3 3.8 4.8 3.0 5.5 4.7 5.4 2.8 6.0 6.0 4.2 3.9 4.7 4.9 5.5 2.6
##  [685] 3.9 4.6 6.4 3.3 3.7 4.2 5.3 5.4 4.4 3.7 3.6 3.6 4.1 2.9 3.0 4.5 5.1 5.3
##  [703] 3.7 4.7 7.0 3.2 4.3 3.8 5.3 5.9 4.6 2.1 4.0 4.9 4.4 4.8 2.8 6.3 5.7 3.4
##  [721] 3.4 5.0 2.7 4.1 3.1 4.9 4.7 4.8 3.0 4.7 5.1 4.5 3.0 5.2 5.2 5.0 6.3 3.1
##  [739] 5.0 4.6 4.7 6.9 2.6 3.6 2.8 3.2 4.8 5.6 4.2 4.4 4.9 3.6 4.1 4.4 5.0 5.1
##  [757] 2.5 3.3 3.1 3.1 3.0 4.7 5.6 3.9 5.2 5.1 4.4 4.9 4.4 5.9 5.8 5.6 4.1 4.2
##  [775] 4.3 4.8 3.5 2.4 3.3 5.7 4.8 6.5 5.1 6.4 3.7 4.4 3.9 5.1 5.1 4.8 4.7 3.4
##  [793] 6.3 5.5 4.8 4.7 5.5 6.1 4.5 4.2 2.8 4.9 5.7 5.5 5.7 3.3 5.4 4.3 3.7 4.1
##  [811] 3.0 5.6 3.6 4.7 4.4 3.9 4.7 3.0 5.4 4.7 4.3 4.6 1.5 5.0 4.3 3.6 3.4 4.7
##  [829] 4.0 4.9 4.6 6.4 2.1 5.7 5.0 3.6 4.7 5.5 3.0 3.9 4.6 6.6 3.9 3.9 4.3 4.5
##  [847] 4.6 3.6 5.0 5.7 3.5 3.6 4.7 3.4 3.6 5.0 5.1 5.3 3.7 5.0 3.5 3.2 4.1 4.3
##  [865] 3.1 3.2 6.4 5.8 4.3 4.7 3.4 4.2 5.1 3.1 3.6 4.0 3.9 4.4 4.8 4.4 4.4 4.5
##  [883] 5.6 5.0 5.1 2.8 3.7 3.8 4.6 3.6 4.8 2.7 5.5 5.2 5.1 4.9 3.4 4.8 4.2 4.0
##  [901] 4.2 3.8 3.3 5.2 3.7 4.8 4.4 4.3 3.9 5.3 3.4 3.6 3.1 3.0 5.9 5.5 4.1 3.8
##  [919] 5.5 3.9 4.9 4.0 5.9 5.5 3.5 3.3 3.2 3.7 6.0 3.8 3.6 6.4 4.6 4.2 4.0 3.3
##  [937] 4.3 4.0 4.0 4.2 3.7 3.3 4.8 3.9 6.0 4.9 5.0 4.8 4.8 3.7 4.2 3.4 2.9 4.8
##  [955] 4.6 4.6 2.5 4.3 6.0 5.5 4.9 5.9 2.6 5.8 6.1 3.5 3.5 4.0 3.3 4.9 4.2 3.2
##  [973] 5.1 4.4 5.6 4.7 4.2 4.3 5.0 4.0 5.1 4.1 5.1 6.3 4.9 6.3 3.4 4.0 4.5 3.7
##  [991] 6.5 5.2 3.9 5.0 4.1 4.5 3.4 3.8 5.8 3.5
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
## 2.7975 6.3025
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
##    [1] 4.5 6.2 6.5 4.1 4.0 4.2 3.6 4.7 5.7 4.6 2.9 5.3 5.8 4.3 2.5 4.5 4.6 3.9
##   [19] 5.5 4.8 4.8 3.1 4.5 4.1 4.6 5.2 5.0 3.9 6.4 5.8 3.4 5.6 4.3 4.4 5.2 5.1
##   [37] 4.5 4.5 5.2 5.1 4.9 5.1 3.2 4.2 6.8 4.4 3.2 4.3 5.0 4.5 5.1 4.5 4.4 3.9
##   [55] 4.2 4.1 6.3 5.1 5.3 3.2 5.6 5.1 5.3 3.6 5.5 5.2 5.1 4.2 5.1 4.1 4.4 4.1
##   [73] 3.9 4.9 5.7 3.5 3.4 4.7 3.7 5.2 4.5 4.3 5.0 5.9 4.1 4.0 5.0 5.1 5.1 3.9
##   [91] 3.8 5.7 4.8 3.8 5.0 4.9 3.5 5.3 5.3 6.1 5.4 3.8 5.2 4.6 4.0 4.6 3.5 5.2
##  [109] 4.1 4.0 4.4 6.2 4.3 4.1 4.2 3.6 1.8 4.4 4.8 4.1 4.1 3.5 5.1 4.3 3.6 5.8
##  [127] 5.5 3.0 6.1 3.8 4.9 3.4 4.3 6.6 4.6 5.2 5.0 3.2 4.9 4.6 3.7 6.4 4.6 5.7
##  [145] 4.7 5.3 4.3 4.3 4.1 4.5 5.5 3.8 4.1 4.4 4.3 4.0 4.8 3.3 4.2 4.4 3.3 3.6
##  [163] 4.7 3.5 5.0 5.5 3.5 5.4 5.1 4.6 4.0 5.4 4.1 4.0 3.5 3.8 4.9 4.3 4.5 4.9
##  [181] 4.8 3.3 4.6 5.5 5.6 5.8 4.8 5.7 5.9 4.1 2.9 2.4 3.8 3.8 5.7 3.4 5.2 3.8
##  [199] 5.7 5.9 4.9 5.3 4.8 5.1 4.2 4.1 5.2 4.7 4.7 5.1 5.2 5.2 4.1 4.8 5.4 2.9
##  [217] 4.4 3.6 4.4 4.5 3.1 4.9 3.8 3.4 5.5 5.6 2.9 2.8 5.7 4.7 5.2 4.3 4.5 4.8
##  [235] 5.1 4.9 4.0 5.0 5.2 3.1 4.6 2.9 4.8 4.0 3.8 4.8 3.7 5.0 4.4 5.0 4.3 3.2
##  [253] 5.2 3.2 3.8 5.8 4.7 4.2 3.4 3.6 4.6 5.5 4.1 4.0 4.8 4.3 3.3 4.8 4.0 4.0
##  [271] 4.6 4.0 4.9 5.5 3.6 6.3 5.4 5.7 3.8 5.1 4.4 4.4 3.7 4.4 5.4 4.3 6.4 4.1
##  [289] 5.2 5.2 4.2 4.1 5.4 4.6 4.1 3.7 4.0 4.8 4.7 3.9 4.6 4.2 3.7 4.7 4.6 5.1
##  [307] 3.7 3.9 4.1 5.3 2.7 3.6 4.1 5.0 4.7 4.1 6.4 5.5 3.8 4.4 5.7 4.0 3.1 4.5
##  [325] 4.9 3.9 5.8 5.3 5.9 4.5 4.2 3.5 5.1 3.5 4.9 5.3 3.8 4.5 5.1 3.3 5.2 4.6
##  [343] 4.4 3.1 6.9 3.3 5.5 5.2 3.9 3.4 3.1 4.5 4.1 4.6 3.6 4.4 4.6 3.3 3.1 6.0
##  [361] 4.2 4.4 4.5 6.1 5.0 2.0 3.8 4.3 4.2 5.2 4.5 3.5 3.8 3.8 4.0 4.0 3.8 5.8
##  [379] 5.2 5.6 4.3 5.5 5.5 2.0 4.4 4.3 5.0 4.8 4.1 4.9 3.4 5.8 5.8 4.6 4.3 4.6
##  [397] 3.6 3.9 4.7 6.8 5.4 4.1 4.0 4.6 3.2 4.2 6.2 5.3 4.1 3.9 4.9 3.0 4.7 5.7
##  [415] 4.9 5.1 5.9 4.2 5.1 4.9 4.4 5.1 4.3 4.9 4.1 5.1 4.1 6.1 5.7 5.0 4.9 3.9
##  [433] 4.4 5.6 5.0 4.7 3.9 4.8 4.3 4.4 3.2 5.1 4.7 4.0 5.8 4.3 3.7 4.3 5.3 5.3
##  [451] 4.2 4.6 4.2 4.6 5.2 5.2 3.8 4.0 3.9 5.2 5.0 3.5 4.2 4.6 4.8 3.9 3.7 4.9
##  [469] 5.8 4.8 3.5 5.0 4.8 4.3 5.1 4.5 4.1 2.4 4.8 5.4 3.2 5.3 5.0 4.1 4.5 6.4
##  [487] 4.9 2.7 4.0 5.1 4.1 3.6 4.9 3.7 4.6 6.2 4.7 5.7 5.9 2.9 4.3 4.2 3.6 4.3
##  [505] 3.4 5.8 6.7 3.7 4.1 4.1 3.5 4.9 4.0 4.4 4.3 4.0 5.9 4.3 6.0 3.7 5.1 3.7
##  [523] 3.4 3.7 4.0 5.2 3.6 4.4 5.5 4.6 3.8 5.2 6.4 4.3 4.0 4.4 4.0 5.2 5.0 4.1
##  [541] 2.2 5.2 4.6 5.6 4.0 2.6 5.5 6.1 3.4 5.6 6.5 3.8 5.1 5.6 3.8 5.6 6.4 5.2
##  [559] 4.0 4.2 5.2 4.3 3.2 4.9 4.1 1.6 4.2 5.1 4.2 4.3 4.3 6.0 4.4 4.9 3.7 4.3
##  [577] 5.0 4.6 4.9 3.8 4.3 3.9 4.8 3.8 4.6 5.1 3.9 4.7 3.1 3.2 5.2 5.0 3.5 4.4
##  [595] 2.8 5.2 6.5 4.6 1.9 4.4 5.0 4.4 4.3 4.7 4.2 4.4 4.6 3.4 3.9 4.9 6.6 3.6
##  [613] 5.1 4.5 3.3 4.2 5.5 4.8 3.6 6.1 4.7 3.3 5.6 4.9 4.1 6.4 3.8 4.9 5.4 5.6
##  [631] 4.4 4.5 4.4 4.2 4.6 4.0 5.9 3.7 3.7 3.0 4.3 3.6 4.0 2.6 4.2 4.5 4.3 5.1
##  [649] 5.9 6.5 4.7 3.9 2.9 6.3 4.0 5.5 3.5 5.6 5.2 3.9 4.1 5.6 5.2 2.8 4.7 2.7
##  [667] 4.3 4.1 3.8 3.8 3.2 5.3 3.3 3.4 5.3 2.4 4.5 4.4 4.3 2.8 4.1 5.4 4.0 4.8
##  [685] 4.0 4.5 4.5 3.7 6.6 5.2 4.2 4.0 4.8 2.3 4.4 5.8 5.5 4.5 4.3 3.8 3.5 2.2
##  [703] 3.4 5.3 3.3 4.4 5.1 3.3 5.2 5.6 4.9 6.4 4.0 3.0 3.6 4.2 5.0 3.4 5.0 4.7
##  [721] 4.8 6.1 4.4 4.6 5.0 4.1 4.0 3.4 4.9 6.1 3.6 5.0 4.5 4.4 4.8 5.6 4.4 3.7
##  [739] 4.7 4.2 5.4 3.6 5.3 3.3 3.6 4.7 5.5 3.7 5.5 4.5 4.7 3.6 3.7 5.0 3.8 4.3
##  [757] 4.3 6.9 4.0 5.0 4.7 3.9 5.7 3.1 4.3 5.1 6.3 3.6 4.0 3.7 5.6 4.9 4.5 3.7
##  [775] 3.5 4.1 4.8 4.3 3.9 4.2 5.4 5.1 3.6 4.5 6.3 4.1 4.5 3.9 6.1 5.5 3.7 3.7
##  [793] 4.1 5.6 5.4 4.5 5.5 5.1 4.9 3.4 4.9 3.7 5.1 3.3 4.7 6.1 4.5 5.5 3.2 3.9
##  [811] 4.5 5.0 3.7 5.5 5.4 3.3 5.4 3.5 5.3 4.1 4.6 3.7 4.7 3.7 4.5 3.5 3.3 3.5
##  [829] 3.3 3.9 3.2 4.9 5.2 4.7 5.2 4.2 5.6 4.3 4.4 4.5 5.0 5.0 6.1 4.6 3.8 4.7
##  [847] 4.3 3.0 4.7 4.0 5.1 2.4 3.8 6.5 3.5 4.8 3.5 5.2 5.4 5.7 4.5 6.9 3.6 4.7
##  [865] 3.8 4.3 3.6 5.2 5.7 5.7 5.4 4.7 5.7 3.1 4.2 4.7 3.3 5.1 4.2 4.7 5.3 5.5
##  [883] 4.5 5.4 4.7 4.9 5.6 3.3 5.5 5.0 4.3 4.4 4.0 4.9 4.9 5.9 5.0 4.7 4.3 4.1
##  [901] 4.1 4.9 5.2 4.6 4.9 5.0 4.0 5.4 4.7 3.1 5.1 5.2 4.7 3.6 4.9 5.5 3.7 4.2
##  [919] 4.6 4.5 4.3 3.7 4.7 3.7 6.3 3.7 4.2 3.6 5.2 5.9 4.3 4.3 3.8 3.7 6.5 5.5
##  [937] 5.8 5.1 3.2 4.3 3.9 4.5 5.2 4.2 4.6 6.2 2.9 4.8 4.4 5.2 3.2 4.9 3.2 5.4
##  [955] 4.3 6.6 4.7 4.6 6.4 4.1 3.8 5.2 5.8 3.0 4.5 2.3 5.3 4.4 3.9 4.2 5.9 4.8
##  [973] 5.5 4.2 2.8 3.9 4.2 4.6 3.9 6.1 3.5 3.5 5.8 3.8 5.2 4.2 4.1 4.5 4.4 5.3
##  [991] 4.2 4.9 4.4 3.9 5.0 4.7 5.1 3.4 4.0 3.3
## 
## $func.thetastar
## [1] 0.0166
## 
## $jack.boot.val
##  [1]  0.47130435  0.33261456  0.35264798  0.18288043  0.05457143  0.01243094
##  [7] -0.10501475 -0.23074627 -0.28186813 -0.49729730
## 
## $jack.boot.se
## [1] 0.8880336
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
##    [1] 3.6 3.2 4.6 2.3 3.4 4.2 4.7 4.2 5.7 5.6 5.2 5.3 3.3 6.4 4.9 3.9 3.7 3.3
##   [19] 4.2 5.9 5.7 5.1 5.1 5.5 5.5 4.2 4.7 6.6 4.6 2.7 3.9 3.7 3.4 3.2 4.1 5.0
##   [37] 5.1 3.2 4.5 4.5 4.6 3.1 3.9 4.3 6.4 6.0 5.8 5.1 5.7 3.4 4.2 5.4 4.9 3.6
##   [55] 5.6 4.8 5.3 4.3 4.8 4.9 5.8 5.1 4.9 5.1 3.7 4.2 3.7 4.9 4.6 4.2 2.4 6.3
##   [73] 4.8 4.2 5.7 5.5 5.6 3.5 4.4 4.5 4.5 4.8 4.1 4.6 5.6 4.9 4.1 4.5 5.1 4.5
##   [91] 4.1 4.1 3.8 4.0 6.3 5.8 4.1 6.2 5.2 3.4 3.9 3.7 2.8 3.6 4.2 4.3 2.4 4.3
##  [109] 5.4 5.9 4.9 3.9 3.9 5.4 3.4 4.8 4.6 4.1 3.1 4.4 4.8 5.3 3.7 3.8 4.7 4.5
##  [127] 5.3 5.4 4.7 6.0 4.7 4.9 4.6 4.3 6.1 4.9 2.9 5.0 4.0 5.8 4.3 5.7 6.5 4.7
##  [145] 5.9 4.3 4.2 6.0 5.3 5.4 4.9 5.0 4.4 4.4 5.8 4.7 3.4 6.0 5.7 4.2 4.4 4.4
##  [163] 3.2 4.3 4.5 2.4 6.0 3.8 4.8 3.4 3.3 4.4 4.4 4.3 5.2 5.9 4.5 6.0 5.4 3.8
##  [181] 3.7 6.2 5.0 3.8 5.1 3.8 4.5 4.6 4.6 4.5 4.7 4.0 4.8 2.6 4.0 3.7 5.2 4.1
##  [199] 5.0 3.0 3.3 3.3 3.5 2.5 3.0 3.8 5.3 3.2 5.1 3.8 4.2 4.8 5.4 4.8 3.4 4.8
##  [217] 4.7 4.3 5.4 3.8 5.5 4.7 5.1 3.6 4.7 2.9 5.0 1.3 3.7 4.9 4.9 5.6 3.2 4.8
##  [235] 3.4 4.3 4.7 4.1 3.6 4.2 3.0 3.1 3.6 4.8 4.0 4.3 4.1 4.1 3.7 3.1 4.3 4.8
##  [253] 4.4 4.0 2.7 4.2 4.4 4.7 5.1 2.9 5.2 4.0 2.4 4.4 4.2 5.4 3.7 4.1 4.6 4.2
##  [271] 4.1 2.8 4.4 5.3 5.4 4.7 4.5 4.5 3.4 4.2 4.0 5.4 7.3 3.3 4.1 4.9 6.4 4.9
##  [289] 4.2 3.0 4.9 3.9 3.7 5.0 4.7 6.3 4.4 4.0 4.4 5.4 6.4 5.3 4.1 5.2 3.5 4.9
##  [307] 2.8 5.7 3.8 6.0 4.2 3.2 5.2 3.4 5.4 4.4 6.0 4.3 4.6 4.4 3.5 5.7 3.9 4.4
##  [325] 3.7 4.2 5.5 4.0 5.1 3.6 3.7 4.7 3.7 4.2 4.7 4.3 3.5 4.2 4.3 5.8 5.1 3.4
##  [343] 3.0 4.9 3.8 4.6 5.3 3.2 4.8 4.3 5.5 4.4 4.8 2.8 4.6 3.9 6.3 4.3 4.6 5.5
##  [361] 3.9 3.8 5.0 3.2 4.6 4.2 3.6 5.0 5.1 5.2 6.0 7.1 5.5 3.3 5.0 6.3 4.8 4.3
##  [379] 3.2 5.1 5.5 4.6 4.9 4.1 3.3 4.0 3.8 4.3 4.2 3.5 3.5 6.2 5.5 5.1 4.5 4.4
##  [397] 4.2 3.5 2.8 5.3 6.1 3.5 5.1 4.5 4.9 2.7 3.5 4.9 5.1 3.9 3.5 5.4 4.3 4.9
##  [415] 2.5 4.0 2.4 6.1 4.3 5.3 5.4 4.3 5.2 4.2 5.5 4.7 2.7 6.4 3.0 4.7 4.9 3.4
##  [433] 5.1 3.0 5.4 4.6 6.1 5.9 4.5 6.7 3.6 3.2 4.3 5.1 6.0 4.1 3.9 5.2 5.4 5.0
##  [451] 4.4 5.3 4.1 3.5 4.9 4.9 2.8 5.3 4.9 4.7 4.3 5.6 3.6 5.6 5.2 5.1 6.0 3.8
##  [469] 6.2 3.7 4.0 5.1 4.6 5.3 5.5 6.2 4.4 3.1 4.5 4.0 3.5 4.2 4.3 5.6 4.4 4.1
##  [487] 5.8 3.7 4.8 4.5 3.5 4.1 4.8 4.5 6.2 2.8 5.5 5.8 3.8 5.7 4.7 4.7 3.8 4.9
##  [505] 3.4 5.1 6.0 3.8 4.9 5.1 3.6 4.9 3.8 6.4 4.3 4.3 6.1 3.5 6.1 5.1 4.4 2.6
##  [523] 4.3 5.7 5.1 3.1 5.3 3.8 3.5 5.4 4.1 4.6 3.2 5.8 5.2 4.0 4.4 4.3 2.8 4.5
##  [541] 4.5 4.1 5.5 5.3 3.9 4.4 5.3 6.6 5.7 3.9 4.2 5.6 4.1 4.3 3.8 6.2 4.0 6.2
##  [559] 4.3 4.7 5.6 5.0 5.1 4.7 5.0 4.9 3.8 3.9 4.9 5.5 5.3 3.7 4.6 4.2 4.1 4.6
##  [577] 4.3 4.2 4.7 4.2 2.8 4.6 3.6 5.6 4.0 3.0 3.8 4.4 3.9 3.8 3.5 3.6 4.5 7.0
##  [595] 3.7 4.4 4.8 3.5 4.1 5.8 4.8 5.3 4.5 3.3 3.6 4.3 4.0 3.8 4.5 3.4 5.2 4.3
##  [613] 4.1 5.0 5.5 6.3 4.8 4.3 3.7 5.4 4.4 5.5 5.6 6.4 4.1 4.5 3.8 4.8 5.2 3.3
##  [631] 5.5 5.5 5.1 3.6 4.1 5.8 5.7 3.9 4.8 7.3 3.4 4.4 5.6 3.6 6.3 3.8 4.8 4.5
##  [649] 3.4 4.4 4.7 3.6 1.9 3.6 3.3 3.7 3.6 3.6 4.3 3.6 5.1 3.8 4.4 6.3 3.4 6.0
##  [667] 4.7 3.9 6.0 5.1 3.4 4.8 4.4 5.4 4.3 6.0 3.9 5.8 5.2 3.2 5.1 5.5 5.4 2.8
##  [685] 4.8 4.3 6.2 4.0 4.3 4.5 4.6 2.2 3.7 2.9 3.9 2.2 4.7 3.5 5.4 5.1 5.4 4.8
##  [703] 4.6 6.0 3.1 2.9 5.7 4.9 4.2 4.9 4.8 4.1 3.6 6.5 4.2 5.0 4.2 4.3 3.5 4.5
##  [721] 5.5 5.6 5.4 6.1 4.4 4.3 3.7 4.2 3.0 6.7 4.8 4.1 4.0 3.7 3.6 4.0 4.5 2.8
##  [739] 4.3 4.4 5.0 4.3 4.6 4.5 3.3 4.3 1.9 5.3 5.1 4.4 6.0 5.3 4.2 3.6 4.6 4.3
##  [757] 5.1 3.8 6.7 4.3 5.1 3.8 4.7 3.3 5.3 5.9 4.6 3.7 4.3 3.1 4.1 4.1 3.4 3.9
##  [775] 3.8 4.0 3.9 6.6 6.4 5.6 5.1 3.7 5.0 5.8 4.8 4.8 5.4 3.7 4.2 3.5 4.5 3.9
##  [793] 2.7 4.1 4.6 5.0 5.8 4.4 4.6 5.8 4.5 3.6 3.3 4.4 3.6 5.8 4.7 3.9 4.4 4.9
##  [811] 5.2 5.8 4.0 4.7 4.6 4.2 3.0 4.4 4.1 6.6 5.5 4.3 4.3 3.5 3.5 4.7 4.4 3.6
##  [829] 3.6 3.8 4.0 4.7 4.7 5.1 5.2 2.8 3.9 4.3 5.7 4.7 7.0 2.9 5.1 2.7 5.1 5.3
##  [847] 3.5 4.5 3.9 6.0 5.8 2.9 3.3 4.9 2.7 5.5 4.1 4.0 5.0 4.0 5.2 4.7 3.7 6.2
##  [865] 4.5 4.1 5.2 5.4 5.6 2.8 4.0 3.1 4.3 3.7 3.5 5.0 3.9 4.4 5.9 4.7 3.4 3.1
##  [883] 3.9 5.1 4.4 6.7 2.9 4.2 4.9 3.9 5.8 5.2 3.6 5.1 2.7 4.0 4.0 4.0 6.7 5.2
##  [901] 6.4 6.4 4.5 3.3 3.1 4.8 4.0 4.1 4.7 4.4 3.3 4.7 3.8 3.7 4.0 5.1 6.5 4.5
##  [919] 4.0 4.4 5.0 4.4 6.0 4.9 4.3 3.9 3.7 5.0 5.2 4.9 3.7 6.5 3.3 6.3 5.1 3.6
##  [937] 3.7 3.9 4.8 5.2 4.3 4.3 4.1 4.1 3.9 4.6 4.0 3.7 4.9 6.0 4.5 3.9 3.9 4.4
##  [955] 3.9 5.9 3.6 3.4 4.6 6.5 3.8 4.7 4.3 5.7 4.9 3.5 3.8 5.5 4.5 5.1 3.5 4.2
##  [973] 3.1 4.4 4.2 4.8 4.7 3.5 3.7 4.4 3.7 4.7 4.9 4.7 6.3 4.1 3.1 5.5 3.6 4.2
##  [991] 5.1 4.7 4.1 3.4 5.0 5.1 3.1 5.0 5.3 3.4
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.4 5.1 5.1 4.9 4.8 4.8 4.6 4.4
## 
## $jack.boot.se
## [1] 1.077079
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
## [1] 0.7467629
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
##   2.967912   6.777060 
##  (1.259841) (3.134220)
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
## [1]  0.9645730  0.5803760  0.5747048  0.3768488  1.1968261 -0.2388477
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
##    [1]  7.713062e-01  9.189390e-01  7.735589e-01  8.231479e-02  7.584577e-01
##    [6]  1.220667e+00  9.075833e-01  7.570349e-01  3.507772e-01  1.184047e+00
##   [11]  5.849666e-02  1.792085e+00  7.480617e-01  1.122643e+00  5.922199e-01
##   [16]  5.134880e-01  8.181659e-01  1.325163e+00  8.980794e-01  7.357025e-01
##   [21]  2.420845e+00  3.455187e-01  1.234477e+00  3.786609e-01  1.211371e+00
##   [26]  7.070606e-01  7.852777e-01  8.095042e-02  5.806509e-01 -7.637976e-01
##   [31]  2.754293e-01  2.720630e-01  2.061258e+00  1.209698e+00 -2.401743e-01
##   [36]  6.939314e-01  6.195236e-01  1.085675e+00  1.173113e+00  1.509125e+00
##   [41]  8.609699e-01  1.337283e+00  4.414422e-01  4.274688e-01  1.161822e+00
##   [46] -2.983425e-01  1.182149e+00  1.231185e+00  1.615257e+00  1.040070e-01
##   [51]  3.470857e-01  1.309254e+00  2.997438e-01  7.825678e-01  1.559919e+00
##   [56]  6.335274e-01  1.542602e+00  2.993716e-01  4.504032e-01  8.641012e-01
##   [61]  4.696958e-02  4.928101e-01  7.132795e-01  1.235767e+00  1.885058e+00
##   [66]  6.730839e-01  1.352874e+00  8.956088e-01  7.289853e-01  1.391894e+00
##   [71]  9.937190e-01  1.220698e+00  2.248924e+00  3.246627e-01  1.537062e+00
##   [76]  4.890817e-01  6.534136e-02  6.936450e-01  3.139201e-01  1.922748e+00
##   [81] -7.431356e-02  1.230470e+00  1.778655e+00  8.167371e-01  6.142298e-01
##   [86]  8.774562e-01  1.372055e+00  6.932838e-01  2.164247e-01  6.599237e-01
##   [91]  2.304606e+00  7.703852e-01  5.084621e-01  1.414655e+00  8.259869e-01
##   [96]  3.038533e-01  1.201688e+00  1.238314e+00  9.746042e-01 -1.115301e-02
##  [101]  1.402188e+00  7.634039e-01  1.125760e+00  1.995261e+00  1.231409e+00
##  [106]  1.164418e+00  9.260665e-01  6.387997e-01  1.075069e+00  7.504231e-01
##  [111]  6.686214e-01  3.221053e-02 -3.620395e-02  1.601264e+00  1.263453e+00
##  [116]  3.556906e-01 -1.231099e-01  4.550214e-01  1.473809e+00  1.206718e+00
##  [121]  1.522517e+00  7.743779e-01  8.478298e-01  2.394753e+00  1.460705e+00
##  [126]  1.998478e-01  1.094956e+00  7.739397e-01  8.016841e-01  1.385353e+00
##  [131]  1.247640e+00  1.186358e+00  3.968280e-01  1.826445e+00  2.730155e-01
##  [136]  4.100585e-01  5.990303e-01 -9.183449e-01  8.136763e-01  6.297861e-01
##  [141]  2.555595e+00  1.464305e+00  1.763745e+00  1.202068e+00  1.154542e+00
##  [146]  7.926019e-01 -3.734776e-01  6.497260e-01  6.630465e-01  8.379661e-01
##  [151]  2.565141e-01  1.238681e+00  4.643414e-01  3.455779e-01  7.158613e-01
##  [156]  2.041877e+00  4.427896e-02  1.053970e+00  2.260161e+00  7.158463e-01
##  [161]  3.435320e-02  8.186989e-01  4.286815e-01 -1.019885e-01  1.664932e+00
##  [166]  2.353976e+00  6.652581e-01  3.917631e-01 -1.642749e+00  6.053823e-01
##  [171]  1.260834e+00  3.929408e-01 -8.478447e-02  3.968469e-01  1.992165e+00
##  [176]  4.471667e-01  7.817964e-01  1.140197e+00  6.861789e-01  4.506521e-01
##  [181]  1.183873e+00  2.031693e+00  1.433027e+00  1.203225e+00  1.146475e+00
##  [186]  2.262011e+00  7.019064e-01  8.002542e-01  1.345437e+00  1.384411e+00
##  [191]  7.574029e-02  6.980825e-01  8.722942e-01  1.373909e+00 -3.110411e-01
##  [196]  1.932492e+00  3.681246e-02  7.110055e-01 -4.444931e-02  2.568426e-01
##  [201]  6.692549e-01 -3.659925e-01 -2.203117e-01  9.114872e-01 -6.547137e-01
##  [206]  4.430729e-01  1.151988e+00  2.021249e+00  2.109771e-02  1.372530e+00
##  [211]  2.501536e+00  4.150043e-01  1.383303e+00  7.668386e-01  1.304173e+00
##  [216]  4.992043e-01  6.361383e-01  1.694494e+00  1.212822e+00  5.011652e-01
##  [221]  2.950943e-01 -3.246868e-01  2.959609e-01  1.431492e+00  1.962456e+00
##  [226]  7.327293e-01  1.329214e+00  1.841685e+00  2.689234e-01  3.257699e-01
##  [231]  6.681276e-01  3.468273e-01 -4.270796e-01  4.678038e-02 -2.990518e-01
##  [236]  6.079796e-01  7.268533e-01  8.055334e-01  3.672750e-01  1.271743e+00
##  [241]  3.466808e-01  1.207398e+00  2.009639e+00  4.890817e-01  4.248028e-01
##  [246]  3.430810e-01  7.475241e-01  1.223881e+00  8.153707e-01  1.216046e+00
##  [251]  1.317639e+00  2.034243e+00  9.934479e-01  3.049480e-01  3.156801e-01
##  [256]  5.349713e-01  1.511238e+00  2.711922e-01  1.753520e+00  4.873000e-01
##  [261]  3.578166e-01  7.271827e-01  1.236827e+00  1.320686e+00  6.390169e-01
##  [266]  9.074140e-01  1.290224e+00  1.354734e+00  4.780424e-01  3.125370e-01
##  [271]  1.157879e+00  1.384196e+00  4.309542e-02 -5.188633e-01  1.239388e+00
##  [276] -2.786257e-01  1.178985e-01  6.633613e-01  6.226260e-02  3.176988e-01
##  [281]  2.300594e+00  9.165650e-01  7.324905e-01  1.232060e-01  3.298304e-01
##  [286] -3.401068e-01  9.273373e-01  7.034951e-01  1.183873e+00  7.451255e-01
##  [291]  1.433598e+00  7.520650e-01  8.138330e-01  1.230579e+00  6.230881e-01
##  [296]  5.730652e-02  1.657340e+00  1.939664e+00  2.093273e-01  1.341726e+00
##  [301]  1.813112e+00  1.350348e+00  2.851812e-01 -2.930236e-01  4.930640e-01
##  [306]  7.689151e-01  7.018959e-01  2.260098e-01  5.015770e-01  1.290086e+00
##  [311]  1.255306e+00 -6.945126e-01  2.184797e+00  6.378502e-01 -6.236464e-01
##  [316]  9.149944e-01  1.331093e+00  1.720003e+00  1.068371e+00 -3.169534e-01
##  [321]  2.306783e+00  3.162187e-01  8.584383e-01  4.556767e-01  2.536897e-01
##  [326]  1.622916e+00  1.405439e+00  1.377111e+00  7.617226e-01  4.547981e-01
##  [331]  7.546207e-01  4.089776e-01  6.680216e-01  1.146231e+00  1.491762e+00
##  [336]  1.237099e+00  9.341037e-01  8.578485e-01  1.109136e+00  9.092740e-01
##  [341]  2.287985e+00  1.528163e+00  1.323950e+00  8.399316e-01  1.837688e-02
##  [346]  5.048820e-01  3.597174e-01  1.373795e+00  6.062423e-01  6.814581e-01
##  [351]  7.317261e-01  7.628834e-01  7.086389e-01  4.532990e-01  5.078932e-01
##  [356] -3.480814e-01  7.792302e-01  6.956824e-01  1.122682e+00  7.908480e-01
##  [361]  1.593852e+00  5.325877e-01  7.855092e-01  1.152945e+00  1.620031e+00
##  [366]  9.044874e-01  4.269266e-01 -7.958021e-01  1.897288e+00  7.292368e-01
##  [371]  6.715294e-01  1.389313e+00  5.077848e-01  1.055020e+00  7.067719e-01
##  [376]  6.954318e-01  6.215491e-01  1.118852e+00  1.407860e+00  5.146567e-01
##  [381]  4.483475e-01  7.181211e-01  2.000121e+00  5.809857e-02  2.747252e-01
##  [386]  4.287362e-01  2.066300e+00  8.727920e-02 -3.874617e-01 -2.215006e-02
##  [391]  9.277655e-01  1.191950e+00  1.918505e+00  1.824644e+00  4.409355e-01
##  [396]  1.185678e+00  9.114872e-01  8.451387e-01  1.238101e+00  2.919753e-01
##  [401]  1.008797e+00  8.582568e-01  3.941117e-01  7.636626e-01  1.358050e+00
##  [406]  1.168418e+00  7.946336e-01  4.502138e-01  1.386665e+00  4.184902e-01
##  [411]  6.113916e-01  2.292648e+00  6.704723e-01  2.343000e-02  1.110929e+00
##  [416]  1.467999e+00  1.081768e+00 -5.020193e-01 -1.782714e-02  7.261147e-01
##  [421]  6.156606e-01  1.985215e+00  5.009775e-01  8.870727e-01  1.958958e+00
##  [426]  9.387410e-01 -5.969963e-02  2.631415e-01  1.225099e+00  8.169119e-01
##  [431]  7.654459e-01  4.532384e-02  5.631998e-01  9.465025e-01 -1.212203e-01
##  [436]  3.554667e-01  7.988255e-01  2.439215e+00 -7.457623e-01  2.040561e-02
##  [441]  2.353994e+00  4.590039e-01  3.565705e-01  6.030061e-01 -1.483414e-02
##  [446]  4.929548e-01  1.164103e+00  1.448460e+00 -3.801941e-01 -4.471396e-02
##  [451]  4.786520e-02 -3.498534e-02  1.447059e+00  1.295497e+00  8.310967e-01
##  [456]  2.133771e+00  3.356435e-01 -7.148148e-02  8.842759e-01  8.283595e-01
##  [461]  1.203225e+00  2.885796e-01  5.988263e-01  1.775476e+00  1.143652e+00
##  [466]  1.352268e+00 -5.042865e-02  4.228132e-01  4.711047e-01  7.545409e-01
##  [471]  6.489929e-01  3.637663e-01 -6.866714e-01  1.043943e+00  1.631947e+00
##  [476]  1.490992e-01  6.390070e-01  1.950564e+00  1.223046e+00  5.708368e-02
##  [481]  1.480278e+00  1.051006e+00  2.086928e+00  1.327367e-01  4.296592e-01
##  [486]  3.321803e-01  7.802942e-01  1.536953e+00  3.448259e-01  1.621709e+00
##  [491] -2.050407e-02  1.657943e-03  1.927145e+00 -2.571754e-02  7.230534e-01
##  [496]  3.635240e-01  1.768894e+00  6.381097e-02  1.073443e+00  6.697191e-01
##  [501]  1.845363e+00  8.597565e-01  7.386513e-01  6.675176e-01  8.971331e-01
##  [506]  2.331195e+00  1.018405e-01  9.222693e-01  1.465615e+00  1.148159e+00
##  [511]  6.596359e-01  9.134054e-02  7.564384e-01  3.417802e-01  2.406622e+00
##  [516] -2.967344e-02  2.444771e+00  1.531798e+00  1.182679e-01  1.127730e+00
##  [521]  8.895595e-01  6.915948e-01  5.676951e-01  1.307464e+00  7.848526e-01
##  [526]  1.085675e+00  1.345470e+00  1.442145e+00  1.555632e+00  4.589915e-01
##  [531]  1.147901e+00  2.260578e+00  7.469671e-01  8.153157e-01  1.484241e+00
##  [536]  2.138647e+00  1.146278e-01  8.992881e-01  6.127533e-01  1.373182e+00
##  [541] -8.498099e-01  3.770092e-01  7.825610e-01  1.402336e+00  1.423019e+00
##  [546]  5.378300e-01 -1.411115e+00  6.836528e-01 -5.094220e-01  2.096261e-02
##  [551]  1.242430e+00  2.582012e-01  3.379969e-01  1.234350e+00  8.191482e-01
##  [556]  3.718218e-02 -9.143633e-05  3.221053e-02  5.002033e-01  1.215305e+00
##  [561]  7.116502e-01  2.914778e-01 -2.596139e-01  1.654769e-01 -4.317736e-02
##  [566]  4.552589e-01  9.285592e-02 -3.090372e-01  3.623676e-01 -1.321595e+00
##  [571]  9.399203e-01  4.332678e-02 -6.517325e-02 -3.664902e-01  7.490857e-01
##  [576]  1.191121e+00  7.271744e-01  6.975984e-01  1.197621e+00  1.204978e+00
##  [581]  7.672778e-01  2.176959e+00 -4.720183e-01  3.838445e-01  8.576663e-01
##  [586]  8.946148e-01  4.577097e-01  7.299596e-01  1.393645e+00 -6.077051e-02
##  [591]  7.586839e-01  1.181958e+00  2.909397e-01  2.053060e+00  6.439217e-01
##  [596] -4.091312e-01  4.868697e-01 -4.875380e-02  3.901557e-02  2.187437e+00
##  [601]  8.067999e-01  1.486762e+00  3.306763e-01  1.198381e+00  3.568228e-01
##  [606]  3.344099e-01  4.433073e-01  1.290107e+00  8.873536e-01  7.885211e-01
##  [611]  6.908829e-01  1.277424e+00 -9.143633e-05  3.476422e-01  1.997555e+00
##  [616]  1.199633e+00  1.363811e+00  1.877969e+00  8.090141e-01  1.335617e+00
##  [621]  1.303859e+00  2.930383e-01  1.169136e+00  1.898453e+00  1.188412e+00
##  [626]  1.893080e+00  1.206208e+00  7.121492e-01  1.273822e+00  1.338408e+00
##  [631]  3.553464e-01  1.141219e+00  9.077049e-01  1.608898e+00 -3.917367e-01
##  [636]  1.229093e+00  1.334731e+00  1.762635e-02  7.895594e-01  7.142278e-01
##  [641]  1.798153e+00  3.622550e-01  8.885086e-01  1.322827e-01  3.385891e-01
##  [646]  2.744500e-01  7.014410e-01  1.384282e+00  3.678292e-01  9.002678e-01
##  [651]  1.914754e+00  1.530674e+00  7.363572e-01  3.232414e-01  2.837041e-01
##  [656]  1.520807e+00  5.849762e-02  1.520755e+00 -1.238153e-01  1.338413e+00
##  [661]  1.256634e+00  9.196210e-01  4.289983e-01  7.623226e-01  7.099364e-01
##  [666]  1.137287e+00  1.348178e+00  6.720124e-01  1.383979e+00  7.220903e-01
##  [671]  1.875496e+00  2.035882e+00  7.133787e-01  7.904767e-01  1.469992e+00
##  [676]  1.551239e+00  5.133045e-02  1.486115e+00 -2.638146e-01  7.459709e-01
##  [681]  1.275986e+00  1.234477e+00  3.178465e-01  2.737094e-01  3.829660e-01
##  [686]  3.133500e-01  9.761933e-01  2.796002e-01  1.138814e+00  7.214254e-01
##  [691]  1.056354e+00  1.379902e+00 -9.148551e-02  1.204378e+00 -8.271364e-02
##  [696]  1.057411e+00  8.614896e-01  1.245044e+00  1.500144e+00  6.780468e-01
##  [701] -5.320046e-01  1.337021e+00  1.163755e+00  4.473920e-01 -2.444452e-01
##  [706]  3.302927e-02 -9.154152e-02  1.422749e-01  2.901141e-02  1.263329e+00
##  [711] -3.796512e-01  2.006216e+00  1.200434e+00  6.495328e-01  8.368949e-01
##  [716]  5.933806e-01  7.519667e-01  7.688796e-01  1.820906e+00  2.389807e-01
##  [721]  4.101110e-01  5.927993e-02  1.236512e+00  8.156837e-01  1.168720e-01
##  [726]  1.173066e+00  3.340485e-01  6.838284e-01  1.160913e+00  7.244275e-02
##  [731]  6.943523e-01  1.175729e+00  3.324371e-01  4.635776e-01  7.993240e-01
##  [736]  7.870467e-01 -3.984334e-02  2.291616e+00  8.367485e-01  2.306363e-01
##  [741]  1.137451e+00 -3.678198e-01  4.974958e-02 -3.830633e-01 -1.444158e-02
##  [746]  2.753371e-01  2.495512e+00  3.808376e-01  8.253117e-01  1.202753e+00
##  [751]  1.181430e+00  8.553494e-01  2.022309e+00  8.665693e-01  4.018273e-01
##  [756]  4.427921e-01  1.002244e-01  3.100815e-01  7.510120e-01  3.926555e-01
##  [761] -6.251046e-01  3.794357e-01  1.173167e+00  6.682986e-01  1.969665e+00
##  [766]  7.671458e-01  9.084883e-01  1.726478e+00  1.488781e+00  2.093825e+00
##  [771]  2.267935e+00  2.005944e+00  1.155343e+00  8.958926e-01  1.140047e+00
##  [776]  7.172203e-01  1.530413e+00  1.765902e+00  7.988954e-01  1.463898e-01
##  [781]  1.118051e+00 -1.092884e-01  1.288535e+00  9.082036e-01  7.491604e-01
##  [786]  2.710690e-01  1.911915e+00  8.234314e-01  4.312636e-01  2.932280e-01
##  [791]  6.615227e-01  1.381728e+00  3.789121e-01  8.579659e-02  1.370121e+00
##  [796]  4.678728e-01  6.747333e-01  1.448460e+00  1.303614e+00  6.241397e-01
##  [801]  1.388306e+00  1.848323e+00  1.447040e+00  3.983627e-01  1.387046e-02
##  [806]  4.405109e-01  1.221914e+00 -2.923446e-01  4.443310e-01  8.770251e-01
##  [811]  7.803594e-01  5.129277e-01  2.138280e-01  2.307602e-01  4.892780e-01
##  [816]  1.892707e+00  6.540048e-01  1.107420e-01  1.802444e+00  6.990023e-01
##  [821]  1.390871e+00 -3.990350e-01  1.220796e+00 -6.568965e-02  1.160286e+00
##  [826]  3.102448e-01  1.329214e+00  7.202973e-01  1.823892e+00  2.962022e-01
##  [831] -2.812588e-01  8.839684e-01  6.215389e-01  2.230847e-02  6.841970e-01
##  [836]  6.944874e-01  1.137451e+00  1.268623e+00  9.099371e-01  2.725681e-01
##  [841]  6.540708e-01  2.095506e+00 -3.109832e-02  2.661325e-01  8.793569e-01
##  [846]  6.167897e-01  8.612616e-01  4.666528e-01 -4.662532e-01  8.775263e-01
##  [851]  1.157088e-01  7.663283e-01  1.465865e+00  2.199736e+00  3.262049e-01
##  [856]  2.261909e-01  6.654207e-02  7.799370e-01  1.160932e+00  3.464399e-01
##  [861]  4.288455e-01  7.886660e-01  1.377111e+00  6.775785e-01  4.466291e-01
##  [866]  5.683073e-01  8.738389e-01  6.892134e-01  1.217649e+00  2.394529e+00
##  [871]  3.324371e-01 -5.953262e-01 -3.722933e-01  1.305043e+00  6.613910e-01
##  [876]  1.357783e+00  1.148980e+00  1.372575e+00  3.225497e-01  1.040070e-01
##  [881]  3.614839e-01  9.077389e-01 -4.440316e-03  7.962339e-01  1.277695e+00
##  [886]  8.450869e-01 -7.453266e-02 -3.760625e-01 -8.789746e-01  2.304345e+00
##  [891]  8.089553e-01 -2.936401e-01  3.479402e-01  3.182920e-01  3.335396e-01
##  [896]  1.496159e+00  1.262662e+00 -4.173943e-01  3.485790e-01 -8.976557e-01
##  [901] -4.397160e-02  1.191622e+00  1.278592e+00  1.212588e+00 -2.310609e-01
##  [906]  4.552622e-01  9.196266e-01  5.098850e-01  6.592440e-02  7.467629e-01
##  [911]  7.331513e-01  3.420976e-01  2.646393e-01  7.314957e-01  9.208309e-01
##  [916]  6.411990e-01  1.242493e+00 -1.310123e-02  7.318636e-01  2.560918e-01
##  [921]  3.283167e-01  9.138068e-01  1.455183e+00 -3.834845e-01  4.687337e-01
##  [926]  1.233686e+00 -3.736816e-01  2.357827e+00  4.617069e-01  6.692549e-01
##  [931]  2.025808e+00  6.644525e-01  8.189147e-01  9.527893e-01  6.954706e-01
##  [936]  2.285110e+00  6.898292e-01  4.654385e-03  8.798324e-01  4.021431e-01
##  [941]  6.961111e-01  3.344099e-01  5.225222e-02  8.559447e-01  3.510419e-01
##  [946]  3.591130e-01  1.190664e+00 -1.045683e-01  7.226267e-01  7.017850e-01
##  [951]  2.025632e+00  1.515837e+00  1.451273e+00  1.855446e+00  6.915509e-01
##  [956]  1.140225e+00  7.874086e-01  1.086220e-02  1.846497e+00  1.146647e+00
##  [961]  7.078546e-01  1.154203e+00  2.634952e-01  1.458038e+00 -2.630636e-02
##  [966]  1.433532e+00  3.320111e-01  8.763353e-01  8.087688e-01  4.517650e-01
##  [971]  7.036506e-01  5.069535e-01  2.080701e+00  4.249919e-02 -2.490267e-02
##  [976]  9.135283e-01  6.117645e-01  6.604651e-01  6.598456e-01  1.883778e+00
##  [981]  3.383619e-01  1.426605e+00  1.539148e+00  6.037634e-01  2.071619e+00
##  [986]  9.626153e-01  1.257984e+00  1.116108e-01  3.797739e-01  4.413231e-01
##  [991]  8.789112e-01  1.463862e-02  2.006633e+00  6.326090e-01  6.147597e-01
##  [996]  7.340347e-01 -1.374808e-01  1.994534e+00  7.570704e-01  1.488442e+00
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
##   0.43793399   0.26207571 
##  (0.08287562) (0.05859908)
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
## [1]  0.42615836  0.11211764  0.80950604  0.64903157 -0.02557578  0.87074055
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
## [1] 0.0111
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9044694
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
## t1*      4.5 -0.05005005   0.9139958
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 4 6 8 9 
## 2 1 1 1 2 2 1
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
## [1] 0.0225
```

```r
se.boot
```

```
## [1] 0.931564
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

