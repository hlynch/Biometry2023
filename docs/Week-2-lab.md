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
## 0 1 2 3 6 7 8 9 
## 1 1 2 1 1 1 2 1
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
## [1] 0.0064
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
## [1] 2.778244
```

```r
UL.boot
```

```
## [1] 6.234556
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
##    [1] 4.4 2.8 5.5 6.2 4.7 4.2 3.9 6.1 4.9 4.6 2.2 3.3 4.2 5.0 3.5 3.5 5.4 4.9
##   [19] 4.7 5.2 4.6 5.6 4.2 4.6 5.4 2.0 2.8 3.4 4.1 2.6 3.3 3.5 4.2 4.7 4.2 2.9
##   [37] 3.8 3.5 4.6 4.0 3.7 5.8 3.7 5.3 2.6 4.4 4.4 3.7 6.1 3.1 5.6 4.6 5.7 5.0
##   [55] 5.5 3.9 4.1 4.4 3.8 5.3 4.7 3.5 5.5 4.7 5.0 5.8 5.2 5.1 3.8 3.6 4.1 5.2
##   [73] 4.1 4.6 4.0 3.1 4.1 4.9 4.4 4.3 5.4 5.7 3.0 4.8 4.6 5.6 6.4 4.4 4.1 3.6
##   [91] 4.0 3.1 4.7 5.2 3.5 4.8 2.7 4.0 5.0 5.4 4.7 3.5 3.0 4.0 5.6 3.8 4.2 3.8
##  [109] 4.6 4.4 3.5 5.6 5.4 4.1 4.6 5.5 5.5 3.7 5.1 4.7 3.9 2.8 5.5 4.5 3.9 4.3
##  [127] 4.9 5.0 5.2 3.4 5.1 3.5 4.5 4.7 3.7 4.9 4.6 4.2 3.6 4.3 4.2 5.4 4.6 5.0
##  [145] 5.4 4.1 3.6 4.4 4.0 4.0 4.4 4.0 4.3 4.6 3.7 4.7 5.1 4.5 3.3 6.0 5.2 4.9
##  [163] 3.3 4.2 5.5 6.0 3.9 4.5 5.3 4.0 5.8 4.3 4.3 4.4 3.8 5.3 5.5 3.3 4.5 5.6
##  [181] 2.5 4.3 5.1 5.1 4.0 1.4 4.8 3.6 4.4 3.3 4.4 4.5 3.4 5.5 5.6 4.5 4.3 6.1
##  [199] 5.6 4.4 5.3 5.1 4.6 3.9 3.8 3.5 5.8 4.3 3.5 4.2 3.3 5.4 6.0 5.4 5.2 4.8
##  [217] 4.0 5.1 3.3 3.7 4.8 3.9 3.8 5.1 4.3 5.4 6.6 5.4 5.1 4.0 4.9 5.4 4.0 4.4
##  [235] 5.6 4.5 5.9 3.5 5.7 4.3 4.8 6.8 4.3 5.1 4.0 4.1 3.2 5.2 4.6 3.8 5.2 3.9
##  [253] 4.2 5.0 3.8 3.5 4.7 5.4 2.8 3.6 5.9 3.7 5.3 5.3 5.9 4.4 4.2 4.1 2.7 4.5
##  [271] 4.2 5.1 4.1 3.6 6.5 2.5 4.3 4.8 6.2 3.1 3.8 3.5 4.0 5.1 4.4 3.6 5.3 3.9
##  [289] 3.0 4.2 4.9 4.5 4.6 3.3 5.6 4.7 5.4 4.2 4.9 5.0 3.2 5.3 4.0 4.4 5.6 3.7
##  [307] 4.3 3.7 5.4 3.2 3.4 5.0 5.3 5.2 4.7 5.3 4.7 4.5 4.5 6.0 5.6 4.3 3.5 5.7
##  [325] 5.0 2.8 4.6 4.4 4.9 4.4 4.5 3.2 3.8 5.4 4.5 4.7 4.7 4.3 4.1 4.9 5.3 4.7
##  [343] 3.4 3.9 5.4 3.7 5.8 5.3 4.2 3.3 4.5 4.1 6.1 3.7 5.4 3.4 5.7 5.6 4.4 2.8
##  [361] 4.5 5.2 4.9 5.0 5.4 4.3 3.7 5.2 4.1 4.5 3.7 4.3 4.8 4.9 4.5 5.7 4.7 4.3
##  [379] 3.4 6.0 4.5 5.2 5.7 4.5 5.0 5.0 4.7 3.4 4.1 4.0 4.3 4.7 3.5 2.7 4.8 3.9
##  [397] 5.8 5.7 4.3 5.6 5.2 4.1 4.7 5.0 3.8 6.2 3.6 3.2 4.7 5.1 3.7 5.2 3.2 4.2
##  [415] 5.0 6.2 4.2 5.7 3.4 4.3 5.0 3.4 5.6 4.9 4.2 4.1 4.2 5.4 2.9 4.0 4.4 6.0
##  [433] 4.4 5.5 4.5 4.7 4.8 5.6 2.3 4.7 4.5 3.5 4.7 4.9 5.3 5.1 4.2 4.1 3.9 4.7
##  [451] 4.0 3.9 3.2 5.6 3.2 3.9 5.1 5.0 4.8 6.4 4.8 4.4 5.1 2.7 4.8 4.5 4.9 4.5
##  [469] 5.9 4.8 5.1 4.8 5.4 5.8 4.4 3.5 6.0 5.6 4.0 3.8 4.2 5.0 4.0 3.8 4.0 5.3
##  [487] 4.5 4.4 4.7 5.0 4.6 5.6 4.6 5.7 4.8 4.9 3.6 6.5 5.3 3.4 4.8 5.1 4.1 5.1
##  [505] 3.2 7.0 4.6 4.1 5.4 3.8 3.1 4.8 3.7 4.1 3.3 4.3 4.0 3.2 5.0 5.1 4.3 4.6
##  [523] 3.3 5.7 1.9 3.3 4.9 5.7 4.0 4.2 4.6 4.7 4.6 3.7 4.6 4.6 4.8 3.7 4.5 3.0
##  [541] 4.0 2.2 3.5 4.0 6.0 4.2 6.5 5.4 4.4 4.2 4.9 2.4 4.1 3.7 4.4 6.2 3.0 4.3
##  [559] 5.0 4.4 5.3 5.3 4.6 5.6 6.2 3.4 3.6 3.4 5.5 4.2 5.1 4.9 4.6 3.9 5.0 4.3
##  [577] 4.9 5.3 4.2 2.1 3.7 4.0 4.7 4.1 5.0 3.6 5.7 5.2 4.6 5.5 5.3 2.7 3.7 4.6
##  [595] 3.6 4.1 1.9 2.6 4.2 4.9 4.7 3.4 5.4 4.8 4.9 3.3 4.7 3.1 2.8 3.5 3.9 4.7
##  [613] 2.7 3.2 5.5 5.6 5.0 4.8 4.6 4.8 4.2 3.4 5.0 3.3 3.3 4.0 4.1 2.0 4.3 5.8
##  [631] 4.6 4.5 4.2 3.9 3.2 5.5 4.2 3.6 4.6 4.0 4.7 4.4 5.1 5.6 4.4 4.0 3.2 5.0
##  [649] 5.8 4.9 4.3 4.4 4.9 5.7 4.9 5.6 6.2 6.1 4.9 4.3 3.9 3.1 5.2 5.0 3.4 2.6
##  [667] 3.6 3.0 4.4 5.1 3.0 4.0 3.9 5.4 4.2 4.2 4.7 3.7 4.2 3.6 5.4 4.1 4.6 3.4
##  [685] 5.4 5.0 3.4 5.5 5.4 5.2 3.8 4.7 4.0 4.5 4.3 3.4 4.2 4.7 4.1 4.6 4.7 5.4
##  [703] 4.9 7.5 3.8 4.9 4.9 5.2 4.8 3.9 3.9 4.3 4.7 5.0 5.1 4.4 4.4 6.3 3.5 3.2
##  [721] 5.1 4.2 4.6 3.4 5.9 4.6 6.2 5.1 4.3 5.5 4.4 4.8 4.3 5.0 5.2 3.8 4.6 5.8
##  [739] 4.8 3.5 4.9 5.1 3.0 5.7 5.5 6.1 3.2 4.2 4.6 4.3 4.7 3.0 4.7 3.4 6.2 4.2
##  [757] 6.3 4.3 2.6 4.5 3.3 4.3 5.7 5.6 4.6 4.4 4.4 5.1 3.3 4.4 5.2 3.2 2.3 4.0
##  [775] 4.7 3.9 4.4 4.9 4.4 4.7 4.4 3.9 5.6 6.5 4.6 6.0 4.1 5.2 3.6 3.3 5.1 4.2
##  [793] 5.6 4.6 3.9 3.2 3.6 4.9 2.9 6.1 3.8 4.0 3.2 3.1 6.3 4.7 4.1 3.6 5.6 5.2
##  [811] 6.2 4.2 3.8 4.7 4.3 4.2 4.8 5.8 4.1 4.6 5.6 5.3 5.1 3.8 4.0 4.7 2.8 3.8
##  [829] 4.7 6.0 3.3 5.7 4.0 4.8 4.5 3.8 4.5 5.1 2.1 4.9 4.9 5.9 4.7 3.1 4.5 5.8
##  [847] 5.7 5.3 5.5 4.3 5.4 4.6 3.9 5.4 5.3 3.1 5.8 5.2 4.2 4.9 3.8 4.4 5.8 5.9
##  [865] 3.4 4.3 4.7 4.0 5.3 3.0 4.2 4.1 5.1 5.5 3.5 5.6 3.9 4.2 3.5 4.5 5.4 4.2
##  [883] 2.8 4.9 4.8 4.7 4.8 4.5 5.3 4.7 4.8 3.4 4.2 4.3 3.0 5.9 5.1 3.1 5.0 5.5
##  [901] 4.4 5.0 3.4 2.8 4.7 4.1 3.7 5.1 4.3 4.7 3.0 4.3 4.6 3.5 3.3 4.6 4.0 3.3
##  [919] 4.5 6.4 5.0 5.4 4.2 5.9 5.7 6.5 5.2 5.0 4.7 4.5 5.5 3.4 6.3 3.8 5.0 3.7
##  [937] 4.2 3.1 3.8 4.3 4.9 4.2 5.0 4.5 5.4 3.6 5.6 3.9 3.7 5.3 5.0 5.7 2.9 5.8
##  [955] 4.0 4.7 5.1 5.9 4.7 2.8 4.4 5.0 5.2 5.0 4.0 3.2 3.6 6.9 3.3 3.9 4.5 4.7
##  [973] 5.5 5.6 3.2 5.0 4.6 5.2 5.2 3.3 5.5 5.0 3.1 4.4 6.0 5.2 5.6 4.5 5.1 6.0
##  [991] 5.0 4.7 3.9 4.7 3.4 3.7 4.3 4.6 4.7 3.8
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
## 2.7975 6.2000
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
##    [1] 4.7 4.8 4.8 4.4 6.2 4.4 3.5 4.2 5.6 5.3 5.7 3.8 6.0 5.5 3.0 6.0 4.5 3.3
##   [19] 4.5 3.0 4.1 4.6 6.2 5.0 5.3 4.1 3.3 4.7 5.3 3.1 5.1 2.4 3.1 3.3 4.4 3.4
##   [37] 5.1 3.9 3.0 4.7 2.9 4.1 3.6 4.8 4.6 6.4 4.3 4.5 3.9 4.8 4.4 5.6 4.4 3.9
##   [55] 6.1 4.4 3.4 3.3 4.8 4.7 6.2 4.6 5.9 3.1 4.8 3.9 5.9 5.2 4.9 5.8 4.2 4.1
##   [73] 4.4 3.8 6.1 3.7 2.9 2.9 3.8 3.7 5.7 4.1 4.4 3.8 5.7 4.9 3.9 4.4 3.8 3.7
##   [91] 6.3 5.2 4.1 4.3 6.3 5.8 5.3 3.8 4.0 3.5 4.9 4.4 4.1 4.8 4.3 4.1 5.5 4.9
##  [109] 5.1 4.6 4.4 4.6 5.4 5.0 5.6 5.8 4.6 4.3 5.3 6.0 5.4 4.3 4.8 4.0 3.5 3.6
##  [127] 3.6 6.5 6.9 4.2 3.3 5.0 5.5 5.3 4.0 4.9 4.7 3.3 4.2 3.1 6.4 5.5 5.7 5.5
##  [145] 4.5 3.3 5.1 5.7 3.9 2.5 4.8 5.2 5.3 4.9 3.0 3.3 5.0 5.1 4.2 5.1 5.8 6.7
##  [163] 4.8 3.6 4.5 4.2 5.1 2.0 4.8 4.7 2.9 5.0 4.4 3.3 4.3 4.0 6.3 3.7 4.4 4.4
##  [181] 4.2 3.9 4.3 4.7 4.3 4.4 4.9 4.9 6.2 3.7 4.1 3.2 3.2 3.8 4.3 4.6 4.8 5.7
##  [199] 4.4 5.9 3.3 3.6 2.4 5.1 4.0 4.6 3.8 5.1 4.5 4.9 4.6 4.2 4.5 4.5 3.7 3.9
##  [217] 5.0 5.1 4.1 4.9 3.2 4.4 4.4 4.9 5.2 6.0 5.1 4.3 4.6 4.9 4.6 4.6 3.3 5.0
##  [235] 4.5 4.4 4.1 4.9 4.1 5.2 3.2 4.7 3.2 4.1 4.6 3.9 5.5 5.4 6.5 3.1 5.3 3.9
##  [253] 5.7 5.1 4.3 3.6 3.7 5.2 4.3 5.4 3.1 4.8 4.6 4.5 3.5 3.4 5.6 4.7 4.4 5.8
##  [271] 3.6 4.6 3.4 5.8 4.0 4.7 5.5 4.2 5.6 5.4 4.9 5.2 3.2 3.9 4.7 4.9 5.1 5.1
##  [289] 6.7 5.8 5.1 4.0 3.1 4.2 6.3 4.2 4.8 3.6 4.4 3.2 5.1 4.5 5.3 5.4 5.1 4.2
##  [307] 5.5 5.6 2.7 4.4 6.1 4.3 5.1 5.7 4.7 4.9 4.0 4.0 4.8 4.7 6.1 4.1 5.8 4.8
##  [325] 4.2 4.3 4.0 4.2 3.9 4.7 4.1 5.6 4.5 5.1 4.9 6.2 6.2 5.0 3.8 4.5 3.9 5.0
##  [343] 4.1 5.1 4.0 5.3 3.8 4.8 3.8 5.4 4.9 4.5 3.8 4.4 5.1 6.6 4.7 3.9 4.1 5.2
##  [361] 4.3 5.1 5.6 5.2 3.1 3.7 4.5 4.7 3.8 3.0 3.4 3.7 6.9 5.3 5.9 4.8 5.8 4.2
##  [379] 2.8 3.8 5.8 5.0 4.6 5.7 5.0 5.2 3.7 4.6 3.4 4.2 4.0 4.7 5.3 4.5 2.9 4.5
##  [397] 4.5 4.7 3.0 3.1 5.3 4.4 2.4 5.4 4.2 5.2 4.1 4.1 4.9 4.9 3.4 3.4 4.1 2.8
##  [415] 4.9 4.8 6.7 4.5 3.8 4.4 3.4 5.5 4.7 5.9 6.3 4.1 5.1 4.9 6.4 5.4 3.3 6.3
##  [433] 4.5 2.4 4.9 5.0 5.4 4.0 4.3 7.3 3.1 4.6 6.5 2.7 4.2 4.1 5.2 5.1 3.8 4.6
##  [451] 4.1 6.5 5.0 5.3 6.7 5.1 3.9 3.3 5.7 3.4 7.5 5.0 6.3 4.1 4.1 3.5 4.4 5.3
##  [469] 3.8 5.1 2.2 3.4 5.2 3.9 5.6 3.7 4.9 5.4 3.2 4.7 4.8 5.5 3.2 6.0 4.4 5.2
##  [487] 3.7 3.5 6.2 5.1 3.4 4.7 4.2 3.8 6.6 5.1 4.4 5.4 5.5 4.5 4.2 5.5 4.0 3.9
##  [505] 3.7 4.0 4.6 5.2 4.6 5.3 5.8 3.9 5.0 4.1 4.5 4.9 4.2 4.2 3.1 3.7 4.3 3.9
##  [523] 4.8 3.0 5.0 5.6 4.4 4.9 3.4 4.4 5.5 6.1 5.1 4.6 3.5 3.2 3.7 4.5 4.6 4.7
##  [541] 4.4 6.7 3.7 4.7 3.3 4.6 5.0 3.7 4.9 4.7 4.0 3.8 5.0 5.6 6.4 4.9 4.7 4.9
##  [559] 3.8 3.3 4.7 5.3 2.3 3.0 4.3 4.1 3.1 6.0 5.4 4.9 5.5 4.1 4.8 4.9 6.1 5.2
##  [577] 4.8 4.1 2.6 4.1 5.9 4.8 3.3 4.3 4.7 3.9 4.5 5.0 5.2 6.8 4.4 4.5 2.9 5.2
##  [595] 5.4 4.7 5.4 5.1 4.6 3.8 3.0 3.8 3.5 3.8 4.7 6.4 4.6 5.0 3.5 3.9 4.8 4.6
##  [613] 3.8 5.0 3.6 3.1 5.2 3.9 4.8 5.4 3.0 5.7 4.5 4.9 4.6 5.6 4.6 5.5 6.8 4.2
##  [631] 3.9 2.9 4.8 5.4 5.0 4.3 5.2 4.4 4.7 5.2 6.1 4.1 4.7 5.0 4.9 4.4 3.0 5.7
##  [649] 5.5 4.1 4.5 5.7 4.9 4.5 4.6 4.7 4.1 4.7 4.1 3.6 4.7 5.6 5.7 5.8 3.8 5.1
##  [667] 5.4 4.3 4.0 4.6 4.0 5.7 4.3 4.9 5.1 3.7 4.3 3.6 4.5 2.9 6.5 6.1 5.8 5.2
##  [685] 5.2 4.7 6.0 5.4 4.1 3.7 4.8 5.5 5.0 5.6 3.5 6.3 6.0 4.5 5.8 3.5 3.7 4.2
##  [703] 3.9 5.1 4.5 4.5 4.7 4.2 5.4 4.9 4.2 3.1 4.5 5.9 3.0 4.1 5.3 6.2 4.6 3.6
##  [721] 4.1 3.0 4.9 3.7 5.0 5.4 3.8 5.4 5.6 3.6 3.2 2.5 4.6 4.4 4.2 4.2 4.8 5.3
##  [739] 6.0 4.2 3.8 2.9 2.8 4.3 5.0 5.6 5.6 4.3 5.0 4.3 4.0 5.2 5.4 4.8 5.5 3.2
##  [757] 5.8 5.0 4.5 5.4 5.0 5.0 4.7 4.4 4.9 5.6 6.3 4.4 4.3 3.9 5.6 5.7 5.8 5.6
##  [775] 5.8 3.6 5.8 5.1 3.7 4.6 4.8 2.8 4.3 3.9 3.5 5.6 5.2 4.2 5.8 5.3 3.9 4.8
##  [793] 3.2 2.1 3.9 5.7 4.6 6.8 3.6 4.6 6.4 4.3 3.7 4.0 4.2 5.1 5.5 4.7 4.1 4.9
##  [811] 5.3 3.1 4.4 4.4 4.9 5.0 5.9 4.4 4.0 5.7 4.9 6.5 4.5 3.9 3.6 2.8 4.4 4.1
##  [829] 5.1 4.5 5.8 3.7 3.2 4.6 6.1 5.0 4.9 3.6 6.2 4.6 3.2 4.4 6.6 2.9 5.2 5.2
##  [847] 3.8 3.9 3.3 3.6 4.2 5.1 2.9 5.5 2.4 6.2 5.2 3.8 4.1 3.5 3.3 5.0 5.1 5.6
##  [865] 5.6 4.6 4.2 2.4 2.4 4.1 5.5 5.1 4.9 4.5 4.8 5.1 4.8 5.0 3.8 3.7 4.3 6.3
##  [883] 4.7 5.4 4.4 3.6 5.7 4.4 4.2 5.4 4.7 6.1 4.9 4.5 3.4 5.3 3.1 3.5 4.6 4.2
##  [901] 3.8 5.7 5.2 5.6 5.5 5.7 4.2 5.3 7.1 4.7 4.9 4.7 5.9 5.5 4.7 4.3 6.0 3.6
##  [919] 4.3 3.5 5.0 5.1 3.4 4.3 4.6 6.0 3.2 5.3 4.9 3.1 5.5 4.5 3.0 4.7 3.4 4.8
##  [937] 3.8 3.4 3.9 3.5 4.0 4.1 4.0 4.4 4.0 4.4 4.1 3.6 5.1 3.8 4.6 3.5 4.7 4.1
##  [955] 4.9 5.3 5.2 5.3 3.7 4.5 5.2 4.4 4.0 4.0 4.5 5.6 4.2 3.1 4.5 3.3 4.4 3.1
##  [973] 6.1 4.1 4.0 3.9 4.9 4.2 3.8 4.2 4.6 3.9 4.2 3.0 4.6 3.7 4.8 1.6 5.5 3.2
##  [991] 3.6 4.3 4.7 4.1 6.2 3.9 6.4 3.5 4.5 3.9
## 
## $func.thetastar
## [1] 0.067
## 
## $jack.boot.val
##  [1]  0.57747253  0.42031662  0.39380531  0.26593407  0.07450425 -0.07181818
##  [7] -0.09174312 -0.09942029 -0.29173333 -0.43690141
## 
## $jack.boot.se
## [1] 0.9420781
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
##    [1] 3.3 3.4 5.3 5.4 5.2 4.6 4.6 4.2 6.0 4.3 5.5 2.7 5.7 4.9 4.9 2.9 5.3 6.2
##   [19] 4.1 5.4 5.0 4.1 5.7 4.4 4.8 3.2 5.5 3.6 4.5 4.1 5.4 4.6 4.4 4.6 3.9 3.8
##   [37] 3.6 4.6 3.4 4.2 5.7 5.2 5.7 3.7 3.5 4.8 4.6 3.2 4.2 5.2 3.7 4.1 3.7 3.5
##   [55] 3.7 5.1 4.7 6.4 5.8 5.9 4.0 5.1 3.5 4.7 5.2 5.2 4.8 3.9 5.0 2.8 4.1 4.5
##   [73] 4.1 3.9 4.0 5.4 3.4 4.9 4.7 5.9 3.4 6.5 3.7 4.1 5.3 4.8 5.3 5.9 3.1 5.4
##   [91] 3.8 4.7 5.0 3.6 2.3 4.0 2.6 2.9 4.1 3.6 4.4 3.8 3.2 5.4 4.7 4.8 4.8 3.8
##  [109] 3.4 5.7 4.4 4.7 4.8 5.6 5.9 5.2 5.8 5.4 4.1 5.2 4.4 5.1 5.1 4.1 5.0 4.0
##  [127] 4.9 4.6 3.5 2.8 4.2 5.1 3.6 3.3 4.6 5.0 5.5 5.7 4.7 5.2 3.0 3.3 3.9 5.5
##  [145] 4.7 5.3 2.7 3.7 5.0 4.3 5.1 4.2 5.8 4.4 5.0 6.0 5.0 3.9 3.0 4.8 3.9 5.3
##  [163] 3.4 4.3 4.2 4.7 5.3 4.8 4.2 6.8 4.6 5.1 3.1 4.8 3.7 4.2 4.4 4.8 3.5 5.1
##  [181] 6.2 4.0 3.7 4.4 4.7 4.0 5.2 3.5 5.3 6.4 5.3 5.9 4.5 2.2 5.9 3.4 4.0 4.9
##  [199] 4.6 4.4 3.4 3.4 4.4 3.1 3.3 3.4 5.7 3.2 4.4 3.5 3.4 4.0 4.7 3.8 4.9 3.4
##  [217] 2.5 3.9 3.9 4.2 3.5 4.0 3.8 4.3 4.1 3.4 5.7 5.1 4.0 3.9 3.0 4.8 5.6 4.7
##  [235] 4.3 5.7 5.6 3.7 3.2 5.0 4.0 3.9 3.8 4.6 4.8 5.3 4.6 5.5 4.3 4.7 3.2 4.4
##  [253] 5.4 3.3 3.4 4.1 4.0 5.2 4.7 4.6 5.5 5.4 3.9 4.4 4.6 4.5 3.7 4.9 5.3 5.0
##  [271] 4.4 5.4 4.9 4.2 5.1 4.2 2.4 4.3 5.6 2.5 4.3 5.4 4.2 4.2 4.3 3.0 4.8 2.3
##  [289] 5.6 3.3 5.5 4.4 4.5 2.8 4.2 4.8 4.8 4.8 3.4 4.5 5.4 4.7 4.9 5.4 5.5 6.1
##  [307] 4.8 4.8 5.0 3.4 3.8 4.9 3.6 5.0 5.9 4.6 3.3 4.3 4.9 2.0 4.9 4.8 4.2 4.1
##  [325] 5.4 4.5 3.6 5.4 4.1 3.4 5.1 4.4 5.3 4.4 4.5 2.9 2.9 5.4 3.7 4.1 3.4 4.2
##  [343] 4.6 3.4 3.1 5.3 5.4 4.9 3.8 5.2 4.2 3.5 2.7 5.3 4.0 4.9 4.6 4.8 5.7 4.2
##  [361] 5.6 4.9 6.2 4.3 4.3 4.8 3.5 5.8 5.0 5.5 3.1 3.9 2.6 5.0 4.3 4.6 2.8 4.2
##  [379] 4.3 4.9 4.8 3.5 4.5 4.7 4.4 4.4 3.4 5.1 5.2 2.6 5.1 3.8 6.1 5.8 4.4 4.8
##  [397] 5.5 3.9 3.9 5.0 4.1 3.0 4.3 3.6 3.1 3.0 3.8 5.7 3.7 4.4 3.9 4.4 3.8 4.3
##  [415] 5.0 3.8 3.9 4.6 4.4 5.9 4.1 4.4 5.1 5.7 5.9 6.5 5.0 3.9 5.4 5.0 3.9 4.4
##  [433] 4.3 6.8 4.8 3.2 2.7 5.8 4.6 4.7 4.1 5.6 4.9 5.5 4.2 3.6 4.1 3.3 3.8 4.8
##  [451] 3.6 3.5 4.9 5.2 5.2 4.7 4.6 3.2 5.8 2.8 6.3 3.5 5.0 2.2 4.1 5.1 4.4 5.5
##  [469] 5.2 4.2 2.7 5.2 5.4 3.8 4.5 4.3 4.3 3.7 4.3 4.3 4.8 4.1 4.0 5.0 4.2 3.7
##  [487] 4.4 4.0 4.5 3.9 4.2 4.8 3.5 5.0 3.7 4.2 5.8 3.7 3.3 4.5 5.1 5.1 5.1 4.3
##  [505] 5.3 4.5 6.0 6.0 3.7 4.4 4.2 4.6 3.2 5.4 4.5 5.5 4.7 5.1 3.9 4.8 3.5 3.5
##  [523] 3.4 5.3 3.6 3.7 6.1 5.1 3.3 4.5 4.5 5.4 5.3 2.6 3.7 4.4 5.8 3.6 3.6 6.4
##  [541] 6.1 3.2 4.3 7.2 4.9 3.9 3.6 4.6 2.7 3.5 4.8 3.9 5.6 4.5 5.9 3.5 4.5 3.8
##  [559] 5.2 5.0 3.7 4.9 6.1 4.9 5.0 4.7 3.5 4.8 3.9 4.9 6.6 4.7 4.9 5.3 5.8 3.3
##  [577] 3.5 4.5 5.6 4.7 2.8 4.5 5.4 3.7 3.5 4.5 2.3 5.4 3.6 4.9 5.0 3.9 4.8 3.5
##  [595] 4.4 5.6 4.5 4.3 5.9 5.6 4.6 5.3 5.8 4.0 4.3 4.7 3.5 4.6 4.7 4.7 6.6 3.8
##  [613] 3.5 4.9 5.0 5.0 4.1 4.7 4.3 4.5 3.7 4.7 5.4 3.5 5.8 4.4 4.9 4.6 3.2 4.9
##  [631] 4.6 4.1 5.5 3.8 5.9 5.0 5.2 5.5 3.5 4.4 4.3 6.1 5.3 3.3 4.1 6.1 6.6 6.0
##  [649] 6.3 4.6 4.7 3.6 4.5 5.2 5.9 4.7 4.3 6.0 3.8 4.8 3.9 5.4 5.5 4.5 4.6 4.8
##  [667] 5.0 4.4 5.7 2.9 2.9 4.2 5.8 4.7 3.8 3.6 6.5 5.1 6.1 4.3 6.3 5.8 3.3 3.7
##  [685] 5.1 4.6 5.6 6.2 4.8 4.9 4.5 4.2 3.2 4.4 5.5 4.5 4.6 4.7 4.5 4.6 5.3 6.3
##  [703] 3.7 4.6 6.0 4.2 4.3 4.0 4.9 4.3 5.0 6.2 5.1 4.2 5.9 5.5 3.6 6.6 4.2 5.3
##  [721] 4.1 4.1 3.1 5.0 5.4 4.4 4.5 4.6 3.9 5.1 3.9 6.3 3.3 3.9 5.9 4.3 4.7 3.9
##  [739] 5.2 3.5 5.6 4.5 4.2 4.1 4.6 5.1 3.6 3.3 4.7 4.4 2.9 5.6 4.7 4.4 4.7 4.3
##  [757] 4.7 4.6 6.2 3.5 4.6 6.8 3.9 4.6 5.0 4.9 5.2 6.3 4.7 3.9 4.9 4.2 4.3 5.2
##  [775] 5.3 4.1 4.5 3.6 3.5 3.9 6.2 3.9 4.0 6.0 4.6 3.6 3.1 5.3 3.2 3.7 4.5 3.6
##  [793] 4.7 4.5 3.7 4.6 3.8 4.5 5.3 3.9 3.3 3.1 5.1 5.7 3.7 3.8 2.2 3.7 4.7 5.7
##  [811] 4.5 2.2 3.0 4.0 4.4 4.2 5.6 5.5 5.9 5.7 3.7 3.1 5.2 5.3 4.8 6.1 3.9 4.9
##  [829] 5.4 4.7 3.8 5.5 4.9 4.0 4.3 3.6 4.7 5.0 3.8 3.6 4.8 4.6 3.7 4.5 4.3 2.2
##  [847] 4.1 5.3 4.6 4.9 4.0 5.1 4.3 4.1 5.5 6.0 5.1 4.4 3.7 5.0 4.2 4.6 3.4 4.7
##  [865] 4.1 5.6 6.4 5.2 3.9 3.0 6.0 5.7 3.7 4.2 4.0 3.2 4.8 2.7 4.2 3.0 4.9 4.4
##  [883] 4.5 6.3 5.3 5.7 3.0 3.0 4.9 3.6 5.1 3.3 3.7 3.4 3.6 2.7 3.7 3.5 6.4 3.9
##  [901] 3.5 5.0 4.2 5.3 3.4 5.0 4.3 6.2 6.0 3.9 4.3 3.9 4.8 4.2 3.9 2.9 4.5 3.4
##  [919] 4.6 3.5 4.9 3.6 5.2 4.1 4.7 3.3 5.4 5.5 4.1 5.4 3.2 4.7 3.9 3.2 4.2 5.6
##  [937] 5.3 5.0 5.2 1.3 3.8 5.1 3.4 3.1 4.1 2.4 3.6 3.9 5.0 5.1 5.9 5.4 3.9 3.9
##  [955] 4.5 4.2 5.8 3.0 3.3 5.8 3.1 3.6 4.1 5.6 2.8 4.1 5.9 6.0 3.9 5.1 6.7 3.8
##  [973] 5.9 3.9 3.7 4.1 4.2 4.0 3.8 4.2 4.2 4.4 3.5 3.6 4.3 5.6 4.4 4.4 5.3 6.8
##  [991] 3.4 4.4 4.8 5.6 4.5 6.1 5.0 3.6 5.7 5.4
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.3 5.2 5.1 5.0 4.9 4.7 4.6 4.5
## 
## $jack.boot.se
## [1] 0.9748846
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
## [1] -0.1025882
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
##   3.544604   6.388131 
##  (1.516520) (2.936165)
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
## [1] 1.1811287 0.3475126 0.5284140 0.6514467 0.4430701 0.5676323
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
##    [1]  0.1033800493 -0.4278012232  0.7549178891  0.2832317249 -0.0883896365
##    [6]  0.3236519505  0.0955453023  0.1766316387 -0.7227306183  0.6985145482
##   [11] -0.2929501002  0.8836617349  0.3789025518 -0.5915470833  0.2440363967
##   [16] -0.9829126949  0.1149704879  0.3545887165 -0.7481819407 -0.0979587827
##   [21] -0.2134059881  0.5796018213  0.0857104126  0.7029510141  0.3695330586
##   [26]  0.1846886994  0.4415878913 -0.2623665231 -0.7275434953  0.1327194856
##   [31] -0.3903807964 -0.0250831621  0.7932313972 -0.5263088760 -0.7188746861
##   [36]  0.3390519228 -0.0388761683  0.5373142295  0.7445219933  0.4293990090
##   [41] -0.3371128593 -0.3356343302  0.1197058424  1.0031721213  0.3116238797
##   [46] -0.1815639828  0.4274308032 -0.5044871720  0.3667640352  0.2926512286
##   [51]  0.2253245487  0.5240261742 -0.2118241210 -0.6988950364 -0.4236535138
##   [56] -0.1329500282 -0.7944937535  0.6639372388  0.0958740156  0.1702836341
##   [61] -1.4245950007 -0.3652941367  0.3747090716 -0.0576403938 -0.6383565030
##   [66]  0.0006125413 -0.2798396070 -0.0736998798 -0.0810996147  0.3414078681
##   [71] -0.1413949646  0.7126408710  0.3584026060  0.4061990723 -0.0473509931
##   [76]  1.0683631569  0.1613482492 -0.2867886327  0.0639373198  0.4379505399
##   [81] -0.5615228676  0.0188320738  0.1946445984 -0.0403469076 -0.3541190184
##   [86] -0.3103396291 -0.1449977570 -0.4179273253 -0.4934868201  0.2506502752
##   [91] -0.5641306195  0.0234967701 -0.3909904057  0.4599397615  0.1481004846
##   [96]  0.6111622560 -0.2141140469 -0.4430138653 -0.0493536515 -0.0895013610
##  [101] -0.2697242724  0.0986139965  0.0579137985  0.0576026706  0.4595330238
##  [106]  0.0557387252 -0.4651457045 -0.4310300126  0.5145889005 -0.4445256311
##  [111]  0.1527548673  0.4168643261  0.1099378414 -1.0442746895 -0.7699822572
##  [116]  1.0957732626  0.1215841509  0.1834535399 -0.8470844630 -0.4349569291
##  [121]  0.9595597952 -0.0940234989  0.2926405378 -0.5213807538  0.0390656633
##  [126] -0.9808766762  0.7376424748  0.1384485634  0.6812284073  0.4316359929
##  [131] -0.5007794469  0.5433891215 -0.3259488798  0.1499851623 -0.2272080759
##  [136]  0.5642494899 -0.4549285985 -0.9249004605  0.4605632831 -0.3518346519
##  [141] -0.9978401203  0.1745803689 -0.2185015608  0.2447390325 -0.4081942662
##  [146] -0.4520500404  0.1583670372 -0.3619962718  0.2204936298 -0.2876866604
##  [151]  0.5406853921  0.2549653497 -0.7634430677 -0.2945264731  0.1882278045
##  [156] -0.0831717379 -0.6424961540  0.3215510798  0.7018976111 -0.1919238405
##  [161] -0.5333314663  0.1194698007  0.4251230373  0.0839506414 -0.4290788187
##  [166] -0.2469945475  0.1985177736 -0.0812004806  0.0588771919  0.2110074267
##  [171]  0.2122503420  0.2738628109  0.2615709685 -0.5357328591  0.2184667608
##  [176]  0.2416860057  0.6396698980 -0.0727002542 -0.1154153988 -0.0876197074
##  [181]  0.0889218181  0.5038897628 -0.2774206706  0.0256467496  0.7999274071
##  [186] -1.1162423394  0.5086244722  0.0650723898 -0.0475908680 -0.1530166482
##  [191] -0.6553198169  0.2229235693 -0.4853160740  0.0177841887  0.1583446486
##  [196] -0.0479907034 -0.2181052300  0.0930780104  0.3784389119 -0.0971457132
##  [201] -0.3051453941 -0.0584205543  0.0724580968 -0.0834218257  0.4279242982
##  [206]  0.0129880827  1.0108298553 -1.1520732801  0.5152274984 -0.3069473975
##  [211] -0.2395152816  0.3824117515 -0.6329293906 -0.0825561487  0.1956233720
##  [216] -0.6070095556 -0.0221018377 -0.3534058357 -0.1766273105  0.1690056823
##  [221]  1.3213757593  0.7253366057 -0.1129939601 -0.2391237476  0.2225882000
##  [226]  0.3156455172  0.1164950641 -0.7549355994  0.7588354323 -0.1261970901
##  [231]  0.0101592367 -0.4853160740 -0.2135934248 -0.3794914456 -1.2805014576
##  [236] -0.2991266524 -0.6290457226 -0.1765214060  0.7169891611  0.0403751771
##  [241]  0.2453356336 -0.3737867666  0.9205929510 -0.3907027056 -0.5727972698
##  [246]  0.8208304778 -0.5332256816 -0.3096164793 -0.1976538170  0.7087965583
##  [251]  0.2045532887  0.3327801273  0.1314368745 -0.2155783521  0.5009998756
##  [256]  0.0610130761 -1.1519418517  0.2331931960  0.4918344728  0.5827044568
##  [261]  0.3213778508  0.0511217541  0.2110074267 -0.2913196435 -0.2032790314
##  [266]  0.0418067453 -0.0132165306  0.0316737672 -0.4554363664 -0.2198379304
##  [271]  0.3592550756 -0.1591593554 -0.2086923976 -0.0192982349 -0.7200482550
##  [276]  0.5156153021  0.2604775190 -0.3048212320  1.2988938542 -0.4053699942
##  [281] -0.7760907376  0.0876478327 -0.7793135991 -0.7354218526 -0.1848131205
##  [286]  0.2197085950 -0.1062389817 -0.7270628648 -0.5024787388 -0.4442433319
##  [291] -0.8225268917  0.5827121736  0.1910080058  0.2022671340 -0.3088474098
##  [296]  0.0153029536  1.6313249082  0.2087467374 -0.0078372783 -0.1151105777
##  [301]  0.1999397777 -0.0269351844 -0.0444445266  0.4966001725 -0.4826805570
##  [306]  0.1492699218 -0.0224643697 -0.2734999656 -0.3582153486 -0.3166063871
##  [311] -0.5777528718  0.0458511028 -0.3943237691  0.0421999442 -0.0019717663
##  [316] -0.2038809052  0.9812499599  0.2303319894 -0.4476200571 -0.4812442673
##  [321]  0.0765904800 -0.9599691113 -0.0907853359 -0.1401707033  0.0840226536
##  [326]  0.3253000633  0.0232831960 -0.2047022845 -0.0662401732 -0.1148093381
##  [331]  0.0046972101 -0.8685096894 -0.1525214209 -1.3167507335 -0.0850971266
##  [336]  0.1711697360 -0.3326907173  0.3879510235 -0.2049162304 -0.8470844630
##  [341] -0.8147145863 -0.3017313228 -0.7833647053 -0.4703221979 -0.1541794919
##  [346] -1.0470814122  1.0148241870 -0.5265841782  0.2504788004  0.0500918300
##  [351]  0.1712818057  0.1907009222 -0.2348750723  0.5101022165  0.0702335985
##  [356] -0.6830687534 -0.5038019634 -0.0189870102 -0.5408661823 -0.4426512380
##  [361]  0.3045060247  0.2296932543 -1.1108921131 -0.0292787405 -0.1729429095
##  [366] -0.3057934694 -0.5546314980 -0.8863801852  0.7544444544 -0.0051629296
##  [371] -0.0188881467 -0.1718130545  0.6664039642  0.4801043583 -0.8712167156
##  [376] -0.0807306585 -0.1990409343  0.8718459047  0.7825705418 -0.5233983989
##  [381] -0.4773042958 -0.3978542260  0.1128869429 -0.5028842052 -1.0834976977
##  [386]  0.5270485460  0.6351903697  0.6172594086 -0.5428589164 -0.5841843850
##  [391] -0.2466432108  0.3292496391  0.1799132606  0.8220961342 -0.7843581055
##  [396]  0.2585047974 -0.0884410766  0.0541924128 -0.9597020072 -0.8074366808
##  [401]  1.3921833663  0.7316756735 -0.0379940279  0.3616329328  0.7207266001
##  [406]  0.3957642652 -0.4263668327  0.4733252382 -0.1542013081 -0.1642207556
##  [411] -0.3504738788  0.1143569496 -0.4322225396 -0.8181166482 -0.3987989896
##  [416] -0.1022073843 -0.0400138040 -0.0702397556 -0.3822869482  0.2775427530
##  [421] -0.2227709754 -1.0579074799 -0.1203288872  1.5082809111 -0.0461987622
##  [426] -0.2891785729 -0.3007848250 -0.3119105007  0.0682290493  0.9091254716
##  [431] -0.0939413179 -0.6529066402  0.2571676869 -0.4410864093  0.5791950082
##  [436] -0.1052141590  0.3846870936  0.2208547200  0.1954696631 -0.3333894096
##  [441] -0.1489346333 -0.2841978415  0.2674076218 -0.4301804092  0.4163167694
##  [446] -0.0905628947  0.1458698836 -0.2285605194  0.0299606638  0.1678334880
##  [451]  0.1670838140  0.4931436238  0.5805827442  0.1478751071  0.2052849980
##  [456] -0.2156931493 -0.5501469948 -0.1674162175  0.6214609365  0.2397121578
##  [461]  0.2030371926 -0.3870463350 -1.2411804743  0.0400527102 -0.8049161802
##  [466] -0.0046662143 -0.3658636930  1.0018788888 -0.0839517355  0.0298590362
##  [471]  0.2562894007  0.1073411993  0.3927401043  0.3778047906  0.0156905570
##  [476] -0.0502693500 -0.2897574297 -0.0839095828  0.2045631285 -0.6177617762
##  [481] -0.4955677495  0.3958085470 -0.6124448626  0.0089432236  1.1493811727
##  [486]  0.1643465864 -0.2299220437  0.7055717785 -0.4615314097 -0.8622690919
##  [491]  0.4285044797  0.5843390937 -0.4747530319  0.1917048386 -0.4228343909
##  [496]  0.4479603439 -0.0389825274  0.1243931254  0.2629266214 -1.1955128090
##  [501] -0.0141375155 -0.8101765929 -0.1932811592  0.5791950082  0.2733161330
##  [506]  0.4358832352 -0.5498486166 -0.4589819465 -0.1427792936 -0.1456199367
##  [511] -0.3034409757 -0.0941819059 -1.5404375632  0.1998869177 -0.0894932439
##  [516] -1.3541302145 -0.3671301161 -0.7907816743  0.1179066006  0.5287772106
##  [521] -0.5599705460  0.4507156363  0.6299655249 -0.0816743881 -0.7326414637
##  [526] -0.0367949531  0.2629047223  0.0780423589  0.0191720405  0.2955763477
##  [531] -0.0786706676 -0.3667774248 -1.1473175793  1.1353516654 -0.2204195804
##  [536]  0.5201397438  0.4121627452 -0.1457043557  0.9927416303  0.9814722775
##  [541] -0.2558478769  0.4058709248  0.0926404009 -0.5385757594  0.3122841408
##  [546]  0.1133448460 -0.6443000813 -0.2582833581  0.8612310454 -0.8588662729
##  [551]  0.0270076907 -0.1964686382 -0.0359254898 -0.3268529668  0.5721684951
##  [556] -0.1605584803  0.2252073720 -0.8403216310  0.5984108851 -0.6283652342
##  [561] -0.7790221415 -0.0608276328 -0.3132230409  0.4384129971 -0.2285535137
##  [566] -0.4876447677  0.0032423074  0.6366600930 -0.1427792936 -0.5800751134
##  [571] -0.1684090408  0.1992886856  0.1425765890 -0.3057388313  0.5616586169
##  [576] -0.1636195853 -0.5637157242  0.0211432052 -0.4265868846 -0.3446037422
##  [581] -0.5536330468  0.4498521758 -0.1469628877 -0.0454505663  0.8427370470
##  [586]  0.2158217458  0.1844507314  0.3668808892 -0.4303027649  0.3594633250
##  [591] -0.3705397983 -0.3986265415  0.1985177736  0.6240218852  1.1439150432
##  [596] -0.2083890110  1.1155771837 -0.1157457867 -0.1251557416  0.0930578863
##  [601] -0.3191986846 -0.3854339152  0.0857983597  0.6540425303 -0.6770363974
##  [606] -0.9983950176 -0.3750237249  0.0825748309 -0.0149482246  0.1760344154
##  [611] -0.1713975730 -0.4473164963  0.2032257561 -0.0834748609  0.4507006147
##  [616]  0.7285279728  1.1002808071 -0.2907890308  0.1996157337 -0.4726750649
##  [621]  0.0948883366  0.5341911756 -0.2613428392 -1.0480996957 -0.3833307727
##  [626] -0.1159795570  0.7488290474 -0.0764913031  0.2332020485 -0.5579904958
##  [631] -0.1860618847  0.6618706690 -0.2511948698 -0.5217289925 -0.4887153997
##  [636] -0.2984149584  0.3189912835  0.4921241626 -0.2132873884  0.4610626044
##  [641] -0.3514970119  0.8596710252 -0.3458823021  0.3116238797  0.1936876883
##  [646] -0.1401707033 -0.8860024829  0.4261114007  0.1598491335 -0.6985754313
##  [651]  0.1683762048  0.0182069516  0.0131038669  0.1635585683  0.9385020988
##  [656] -0.1194683677 -0.6440505031 -0.0696965608 -0.2427094769  0.2375458627
##  [661] -0.3686894170  1.1398079518 -0.2086455536  0.3044190024 -0.9535219774
##  [666] -0.7874252582 -0.0268666394 -0.0335408277 -0.4772691871  0.3472699789
##  [671]  0.1525202839  0.2151734246  0.3526781042 -0.7518116145 -0.0561755283
##  [676] -0.1755483680 -1.0296142168 -0.4105853679  0.3684323350  0.4796306963
##  [681] -0.2585119207  0.3723833542  0.1671116368  0.1060481143 -0.8260541943
##  [686]  0.1404523455 -0.0856090574 -1.0720238603  0.9020137972 -0.3709892171
##  [691]  0.6172814024 -0.1259592863 -0.1390949852  0.1192335854  0.2895636230
##  [696]  0.1258229050 -0.8266978859  0.0954804516  0.3975872363  0.3227781437
##  [701]  0.4667077677 -0.4356591170  0.0158577963 -0.0308533387  0.8943224638
##  [706] -0.4631088418  0.6444575768 -0.5385757594  0.1732890635  0.6785823788
##  [711]  0.2902714702 -0.4973267143 -0.6046747015 -0.0047309466  0.3333310329
##  [716] -0.3381436252 -0.3418725721  0.8239130085 -0.4045131922  0.1763938072
##  [721]  0.4394544227  0.6729910898  0.7488290474 -0.1488562961  0.0796998014
##  [726]  0.4139451480 -0.6354518175 -0.7944937535  0.1167406715 -0.1860439702
##  [731]  0.5751897110 -0.2382707397  0.1028896165  0.7343396418 -1.6610507241
##  [736]  0.2613813976 -0.1886524508 -0.2232786158 -0.2135934248 -0.4010659517
##  [741]  0.5286914746 -0.0924564739 -0.0283804167  0.0637101615 -0.5834070313
##  [746] -0.3082113345 -0.0352600272  0.2866554947 -0.0494685698  0.5079998631
##  [751]  0.3412988476 -0.2576421136  0.1653640010  0.8205579194 -0.0227410907
##  [756] -0.7483213362  0.7454486487  0.5123396992  0.0892225087 -0.1160792941
##  [761] -0.1626952254  0.6454251596 -0.3831973620  0.3343770060 -0.7797631123
##  [766]  0.3845800802  0.1744698116 -0.7035266615  0.7829257290  0.5307653216
##  [771]  0.6043373945  0.2136893063 -0.1734296052 -1.0490484187  0.3723833542
##  [776]  0.7303857096  0.2077933087 -0.3864099694 -0.0275786488 -0.3319933070
##  [781]  1.1305934798  0.7416393832  0.7083133694  0.4844633341 -0.2244066550
##  [786]  0.2273049603  0.1454459303 -0.5269013345 -0.6164233724 -0.4808118505
##  [791]  0.3268006761  1.0978346253  0.2987259876  0.2615415629  0.0904333818
##  [796] -0.2304314764  0.2416286183 -0.0041852486 -1.2460400072  0.3609469934
##  [801]  0.3334260799 -0.0733652643 -0.7042573667  0.1239457498  0.1502842608
##  [806] -0.3053079113 -0.1005152488  0.3253425444  0.1671116368  0.3086305838
##  [811] -0.3928380594 -0.1409645132 -0.0334205423 -0.3102004846  0.4472336874
##  [816]  0.0175402968  0.3979878356  0.2308940227  0.1928231062  0.0188099975
##  [821] -0.2765176465 -0.9677125768  1.7198783556  0.0898852567 -0.6733874206
##  [826]  0.4015986235 -0.2093952251 -1.6837864600  0.0986230327 -0.2894337473
##  [831]  0.6335232518 -0.2945307804 -0.4158283296 -0.2391947663  0.1055530411
##  [836] -0.1561806569  0.4001401948 -0.0214107421  0.0182069516 -0.5140532353
##  [841] -0.5212493039 -0.0933876411 -0.3312279632  1.0309404544 -0.3541192991
##  [846] -0.4349009233 -1.2974007960  0.2444188091  0.3294470985 -0.1160792941
##  [851] -0.7843581055  0.3230170318  0.3252733843 -0.3639788943 -0.2821676470
##  [856]  0.2550180735 -0.0579177590 -0.7225997955 -1.1258807641 -0.5208128026
##  [861]  0.6151885510 -0.7045363265 -0.8137532082  0.0467307398  0.5090276671
##  [866]  0.0176500619 -0.1278207988 -0.6495918106  1.2449779202 -0.2622294535
##  [871] -0.1071109446  0.0822205217 -0.1246279960  0.0212386984  0.3944289621
##  [876] -0.1324450684  0.0302893316 -0.1560150206 -0.0609040140 -0.4923512418
##  [881]  0.6501160439 -0.7587945480  1.4038571072 -0.4346720117  1.0457703223
##  [886] -0.0837354873  0.1274540624 -0.0331590414 -0.0611800607  0.2834444961
##  [891] -0.0541609142  0.9258152000  0.7390877753  0.1067919101 -0.7216317315
##  [896]  0.3945266491 -0.3685581475  0.4428125418  0.0435173844  0.5337940248
##  [901]  0.2521682500  0.0120519786  0.4898152300 -0.3571789291 -0.0878400465
##  [906]  0.1373971357  0.5269826538  0.3392825718 -0.3659521701  1.0314596371
##  [911]  0.0323645822  0.1069941507  0.0191862540 -0.2858393507  0.3841360924
##  [916]  0.8546749899  0.4955919334  0.6579940935  0.5019517453  0.4371431565
##  [921] -0.3048212320 -0.1816520482 -0.1465283742 -0.5113387891  0.5336644935
##  [926]  0.3716988355 -0.0931158153 -0.0124945056  0.4485923112 -0.0059593575
##  [931] -0.4345654847 -0.4674252335 -0.2582647716 -0.0890500255 -0.4614921426
##  [936]  0.2388651046  0.0011650917 -0.2474367125  0.9033796500 -0.4265502591
##  [941]  0.3727418266 -1.1707084377 -0.5007794469 -0.3779951633  0.1202866496
##  [946]  1.3059603582 -0.3992663750  0.0507576203  0.2988381084  0.1360785616
##  [951]  0.0774996548 -0.6593339183 -0.2691801847 -0.2605902059  0.8431931729
##  [956]  0.7318274652 -0.0417571146 -0.1077351073 -0.2679738373  0.0061817799
##  [961]  0.1147009739  0.2078785980 -0.2091071904  0.2090836121 -0.1814765093
##  [966] -0.1898096322 -0.5198505317 -0.9954908923 -0.9143625115 -0.4600183708
##  [971]  0.7767240980 -0.1715623485  0.2266673531 -0.3505308601  0.4323747668
##  [976] -0.6497992827 -0.7055584893  0.0505461571 -0.5195823829 -0.0453531644
##  [981] -0.2744237011  0.2264393420 -0.2270773206  0.4954162507 -0.0337937663
##  [986] -0.0641579346  0.3892310397  0.3644890115 -0.8886414668  0.0498401984
##  [991] -0.6583628917  1.5067351067  0.5650772474  0.4385475500 -0.2430322442
##  [996] -0.7047579785  0.1101690174 -0.1321364063 -0.5126866066 -0.0992874014
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

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.55487349   0.25140970 
##  (0.07950273) (0.05621353)
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
## [1]  0.66272540  0.07491449 -0.02519675 -0.22404794  0.99191113  0.82804775
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
## [1] -0.0191
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9357889
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
## t1*      4.5 -0.009309309   0.9306641
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 4 6 8 9 
## 1 3 1 2 1 2
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
## [1] -0.0041
```

```r
se.boot
```

```
## [1] 0.9102647
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

