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
## 2 4 5 6 8 9 
## 1 2 3 2 1 1
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
## [1] -0.0105
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
## [1] 2.732982
```

```r
UL.boot
```

```
## [1] 6.246018
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
##    [1] 4.4 5.6 4.6 5.1 5.4 4.3 4.1 5.6 2.8 4.3 4.4 4.5 6.2 3.7 4.3 5.3 4.3 3.9
##   [19] 3.6 4.4 4.2 3.2 4.1 4.5 2.9 6.0 4.2 4.1 3.6 5.9 5.1 4.3 2.6 6.5 5.0 5.2
##   [37] 4.8 4.6 4.2 4.0 4.5 3.3 6.3 3.2 4.4 4.0 3.5 4.6 5.4 4.3 3.8 4.1 3.9 3.3
##   [55] 4.6 3.5 4.6 4.8 4.3 4.8 4.9 4.8 4.5 3.8 4.1 4.4 5.5 4.1 3.5 3.3 3.5 4.3
##   [73] 5.0 2.7 6.1 4.6 3.0 3.7 4.4 3.5 5.4 4.7 3.9 2.0 3.5 4.5 3.1 3.7 4.7 3.5
##   [91] 2.9 4.3 5.5 5.7 6.4 3.3 5.7 3.0 4.3 3.2 3.9 4.3 4.0 5.0 3.6 5.6 6.0 2.7
##  [109] 5.4 3.9 6.7 5.4 5.3 4.3 6.0 4.7 4.7 4.3 4.8 5.5 5.9 5.4 5.0 4.0 3.9 5.9
##  [127] 5.7 3.0 3.5 5.4 4.3 4.3 5.2 4.8 4.2 5.4 4.9 4.3 4.0 3.0 5.8 4.2 4.0 2.2
##  [145] 4.7 5.4 3.4 4.8 6.0 3.9 2.6 4.6 4.8 3.9 6.1 5.7 4.4 5.0 4.3 4.7 3.7 5.7
##  [163] 6.4 5.8 5.1 4.5 3.7 4.5 3.5 5.7 3.1 5.2 4.3 5.2 4.2 5.4 3.7 3.8 4.9 6.5
##  [181] 3.7 3.5 5.4 4.5 5.1 4.9 3.9 4.6 4.1 5.3 4.6 4.4 3.9 4.5 3.9 3.1 4.4 3.1
##  [199] 6.0 5.0 3.9 6.2 5.0 5.5 6.1 4.2 4.0 3.4 4.5 3.9 3.6 3.3 3.3 5.5 3.4 4.3
##  [217] 3.8 4.9 4.6 5.6 4.9 4.2 4.2 4.0 4.4 3.5 5.9 4.5 5.8 4.6 5.3 2.6 3.4 5.8
##  [235] 4.8 5.0 4.2 5.2 4.8 4.1 4.7 3.0 3.2 6.3 3.8 3.6 4.2 3.6 4.7 4.5 4.9 5.2
##  [253] 4.5 3.9 3.4 4.7 4.4 5.5 4.5 4.7 3.5 5.8 3.2 4.2 5.3 2.3 3.1 5.0 4.9 2.5
##  [271] 3.6 4.0 2.7 6.0 4.7 4.6 3.4 3.1 4.0 4.7 4.3 4.2 4.6 4.9 5.7 4.5 5.7 4.7
##  [289] 3.4 4.2 4.0 2.8 3.6 4.8 5.0 3.9 4.1 4.2 4.7 4.7 4.8 3.5 3.4 4.0 4.3 3.4
##  [307] 5.0 5.6 5.7 3.8 5.2 5.4 3.1 5.4 5.5 3.7 5.4 4.2 2.8 5.7 4.9 5.3 4.0 5.7
##  [325] 3.9 4.7 4.4 5.6 3.4 4.7 5.5 4.1 3.7 4.0 4.6 4.0 4.8 5.2 4.5 5.4 4.2 5.4
##  [343] 5.2 3.8 4.3 3.1 4.6 3.9 3.4 5.7 4.6 4.7 4.3 5.5 4.8 6.9 3.4 4.3 5.3 3.6
##  [361] 3.8 4.0 5.4 3.1 6.2 5.2 5.5 4.3 4.3 3.3 5.5 5.1 4.3 4.7 2.8 4.8 4.0 3.9
##  [379] 5.0 5.4 4.8 5.0 4.0 5.3 5.6 4.1 4.4 7.0 4.3 5.3 4.6 2.5 3.7 4.8 5.5 5.1
##  [397] 4.9 4.8 6.2 4.8 5.3 3.4 3.5 3.8 3.5 4.5 5.1 6.5 5.2 5.6 5.6 3.9 4.6 4.7
##  [415] 3.7 3.1 3.7 4.5 3.4 4.6 5.1 4.5 4.1 2.6 5.7 6.7 4.5 5.2 4.2 3.8 5.5 5.4
##  [433] 3.6 5.4 4.0 5.6 4.5 4.0 4.4 3.7 4.2 3.3 5.0 5.6 6.0 3.5 4.1 4.3 5.8 4.2
##  [451] 3.8 4.2 4.4 4.6 5.9 2.6 3.4 4.7 3.7 4.1 5.0 3.7 4.9 4.7 4.8 4.8 2.2 3.0
##  [469] 4.4 5.1 5.3 6.0 6.4 4.2 5.8 4.3 4.9 4.0 4.4 6.0 3.1 4.3 4.7 5.1 3.7 3.9
##  [487] 4.7 4.0 5.2 5.1 5.2 6.2 6.1 3.5 2.9 5.4 3.9 4.1 4.0 4.1 5.4 3.5 4.9 4.6
##  [505] 5.6 5.0 3.6 4.4 3.6 3.3 5.3 5.1 3.5 4.9 4.2 3.7 4.0 4.2 4.2 5.0 5.3 5.2
##  [523] 3.7 5.7 4.3 5.2 5.2 4.8 6.1 5.0 3.5 3.9 3.6 5.1 5.1 6.4 5.0 3.3 4.1 4.8
##  [541] 3.1 4.1 4.0 5.0 5.1 5.8 3.7 5.5 3.8 5.1 4.2 3.3 5.4 3.1 5.0 4.6 4.9 4.8
##  [559] 3.5 4.8 4.4 3.3 4.9 5.5 4.0 4.9 3.5 5.4 5.5 3.6 5.3 5.6 5.1 3.6 4.7 5.3
##  [577] 4.4 4.1 4.0 6.3 3.4 4.5 6.1 5.3 6.6 4.1 3.7 4.3 5.1 5.2 5.8 5.1 2.9 5.1
##  [595] 3.7 5.1 4.6 4.0 4.7 4.4 5.3 3.4 5.5 3.3 5.1 4.7 4.1 3.6 6.0 4.4 4.6 3.6
##  [613] 4.3 4.0 4.8 4.7 5.5 4.0 3.9 5.0 5.4 5.0 4.8 4.1 6.3 4.1 4.1 4.8 5.0 5.0
##  [631] 2.9 5.0 5.8 3.3 4.4 5.2 4.1 5.1 5.3 5.8 4.8 3.9 4.1 6.6 4.0 3.7 5.0 4.9
##  [649] 4.3 5.1 2.9 4.2 5.0 4.6 4.8 4.3 3.7 3.2 3.7 4.1 5.6 4.6 4.6 4.7 5.3 4.5
##  [667] 5.4 5.3 3.9 3.4 5.3 3.8 4.3 3.9 4.4 4.8 5.2 3.4 4.1 2.9 4.8 3.1 3.5 4.7
##  [685] 4.2 5.5 4.3 4.3 4.9 5.3 4.2 4.2 4.5 6.0 4.4 2.7 5.8 4.5 4.6 6.7 3.3 2.8
##  [703] 4.4 4.6 5.1 2.5 4.7 3.1 5.1 5.2 4.8 5.7 3.2 4.8 5.3 3.5 4.5 3.5 3.3 6.0
##  [721] 4.7 4.2 4.7 4.2 4.8 4.6 5.7 4.5 6.6 3.9 3.2 5.1 4.9 4.9 6.5 4.2 5.4 3.1
##  [739] 5.1 5.5 4.8 3.5 4.2 3.9 3.4 3.5 4.9 4.2 4.8 4.2 3.8 4.7 4.4 4.1 3.7 3.7
##  [757] 4.9 4.6 5.3 4.3 3.8 5.4 3.4 5.6 4.8 4.8 4.7 3.3 4.6 3.7 3.5 4.2 3.4 3.8
##  [775] 3.6 3.6 3.9 3.8 5.0 3.9 4.2 4.2 3.6 4.6 4.9 3.6 5.0 5.3 4.4 2.7 4.7 3.9
##  [793] 3.4 4.1 3.0 5.6 3.8 2.9 4.6 4.7 3.2 3.6 3.1 4.6 2.6 5.4 4.4 5.0 3.4 5.9
##  [811] 3.4 5.3 4.8 4.1 5.2 4.8 2.8 6.1 4.8 4.7 5.8 5.2 5.7 5.3 4.0 4.4 4.0 3.8
##  [829] 3.6 3.7 3.4 6.4 3.5 3.9 3.1 4.6 5.7 4.6 4.8 3.8 5.1 5.0 5.4 4.3 3.2 4.6
##  [847] 5.2 6.2 3.8 4.7 3.6 5.1 5.6 4.7 6.5 3.7 5.5 3.4 4.2 3.4 5.4 6.0 4.1 4.0
##  [865] 4.6 2.6 4.2 5.0 4.2 3.9 4.6 3.4 4.7 3.9 5.0 4.4 5.4 4.9 3.8 5.4 4.1 5.1
##  [883] 3.7 4.2 6.2 4.0 3.2 3.9 3.5 5.3 5.1 4.3 3.3 4.7 4.0 4.1 4.1 4.3 5.7 5.3
##  [901] 4.7 4.8 3.6 5.0 5.6 3.0 3.0 2.8 3.2 5.5 2.5 4.7 5.3 5.3 5.1 3.8 4.3 4.2
##  [919] 4.9 5.1 5.5 5.5 5.2 3.3 4.3 5.2 3.5 3.4 2.5 5.2 4.5 4.0 5.0 3.8 3.6 3.5
##  [937] 4.2 5.6 2.8 5.2 6.0 4.8 3.7 6.6 3.6 3.2 4.6 4.1 4.9 3.3 4.3 3.5 5.4 4.8
##  [955] 3.0 4.8 3.5 6.0 5.3 5.9 4.1 4.5 4.6 3.2 4.9 4.3 6.1 4.2 4.6 4.6 3.7 4.1
##  [973] 3.3 3.6 4.3 4.2 5.3 4.7 5.6 4.3 4.8 4.3 4.1 3.3 6.1 6.0 5.2 4.7 4.4 5.4
##  [991] 2.9 4.2 4.8 4.5 4.6 5.8 4.7 4.9 5.1 4.7
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
##   2.8   6.2
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
##    [1] 4.6 2.1 3.7 3.9 4.4 6.6 5.7 4.9 3.8 3.8 5.3 6.9 3.7 4.5 3.4 5.7 2.0 3.5
##   [19] 4.2 6.3 3.3 5.1 4.4 2.9 4.3 3.5 5.8 5.2 3.7 3.8 3.7 5.9 4.3 3.5 4.3 5.9
##   [37] 5.5 6.5 4.3 4.0 4.2 6.5 4.0 5.1 3.9 4.4 6.0 5.2 2.4 5.9 4.1 3.8 5.0 5.1
##   [55] 3.9 5.3 4.8 5.1 3.7 5.0 4.0 6.3 5.2 2.5 3.7 3.8 4.3 5.0 3.7 4.4 4.2 3.7
##   [73] 4.1 5.1 6.1 4.8 4.3 4.6 3.4 3.0 3.7 6.6 5.0 3.7 4.6 4.6 4.0 5.0 4.4 4.0
##   [91] 4.0 2.5 3.7 4.5 3.5 4.2 6.1 5.2 5.8 4.5 5.3 4.0 4.5 3.8 6.1 4.2 5.8 4.8
##  [109] 4.0 3.2 3.5 3.6 5.3 5.5 4.9 4.8 5.7 4.6 3.0 3.9 4.4 4.3 6.1 3.7 3.2 4.9
##  [127] 3.2 5.5 6.4 3.1 4.7 4.2 5.8 3.1 4.4 4.8 4.0 4.7 4.1 4.6 5.8 4.1 5.3 5.1
##  [145] 5.9 3.4 4.6 2.7 5.4 5.1 4.8 5.1 6.4 4.3 4.8 4.4 3.4 3.9 3.3 4.6 3.7 4.3
##  [163] 6.8 4.8 4.1 4.3 3.0 3.9 5.0 4.2 3.8 4.3 5.0 5.1 4.3 3.5 4.3 4.1 4.4 5.8
##  [181] 5.1 4.9 3.8 4.5 4.7 3.1 5.7 5.4 4.2 2.3 3.7 5.6 3.0 5.4 5.6 4.8 4.9 6.4
##  [199] 5.4 4.2 3.4 3.1 3.4 3.5 4.1 4.6 3.4 4.6 5.9 5.9 5.0 4.3 4.2 4.2 5.3 5.5
##  [217] 5.6 4.8 6.0 5.6 4.2 5.2 4.8 5.2 6.4 4.8 4.0 3.1 6.8 5.1 2.5 5.8 3.5 3.7
##  [235] 5.6 4.1 4.4 6.4 4.3 5.1 3.6 5.8 3.8 5.2 3.7 5.4 5.3 2.5 2.9 6.1 5.2 4.9
##  [253] 3.9 3.5 5.4 3.4 4.0 4.1 4.6 3.0 4.2 4.3 6.7 5.8 4.2 4.6 4.7 4.8 5.1 4.2
##  [271] 3.1 5.0 4.5 4.8 5.2 4.0 3.0 4.1 6.9 5.4 4.1 4.3 3.7 4.6 4.9 2.6 5.0 6.2
##  [289] 4.3 4.3 3.3 2.5 3.3 3.6 4.5 4.0 3.0 3.6 4.7 3.6 4.4 4.8 4.6 3.3 4.2 6.0
##  [307] 4.1 5.1 2.6 4.5 2.9 4.3 4.1 5.6 6.0 3.6 5.2 5.7 3.5 4.5 4.8 5.0 3.9 5.3
##  [325] 2.9 4.2 4.5 5.6 4.3 3.9 4.0 4.3 4.3 4.7 4.3 3.6 4.2 4.5 4.2 5.0 6.6 3.2
##  [343] 4.9 5.4 4.5 4.0 4.7 4.8 3.9 5.2 3.7 3.6 5.0 5.4 4.3 3.2 4.3 5.5 5.8 5.5
##  [361] 3.4 5.3 3.7 4.7 6.2 4.7 5.3 6.3 4.7 3.5 3.3 4.4 4.2 5.5 3.5 4.7 3.0 4.2
##  [379] 4.4 4.6 3.5 2.0 4.7 3.8 3.6 3.1 3.2 4.2 5.0 4.5 3.8 3.6 6.6 3.1 2.9 5.1
##  [397] 3.2 5.2 4.7 5.4 5.9 4.3 4.3 5.2 4.4 3.3 3.3 5.0 5.6 5.0 5.0 4.4 4.6 4.5
##  [415] 6.0 3.1 5.7 5.9 4.5 5.9 4.2 3.7 5.4 5.1 5.0 5.0 3.7 4.6 5.9 6.0 4.3 4.4
##  [433] 4.0 4.7 4.8 3.2 5.1 3.5 4.3 4.8 5.0 5.5 3.1 5.0 5.4 4.7 6.1 3.6 4.2 4.9
##  [451] 5.5 3.6 3.8 3.2 4.1 5.4 4.0 4.4 4.8 5.3 4.4 3.8 5.1 5.8 6.1 4.2 6.4 4.1
##  [469] 4.7 3.6 3.5 5.2 6.3 3.1 3.2 3.6 5.1 4.9 3.1 4.8 2.1 3.6 5.5 5.0 5.7 3.5
##  [487] 4.3 4.6 4.3 5.5 4.5 4.9 5.9 4.0 4.2 4.8 5.2 3.9 5.0 4.0 5.4 5.1 4.9 4.6
##  [505] 5.2 4.9 5.4 5.8 4.2 3.1 3.5 4.1 5.7 5.4 4.6 4.3 4.4 4.5 5.6 4.1 5.6 3.8
##  [523] 4.1 5.0 5.1 4.9 3.1 4.9 4.4 5.6 5.1 4.8 4.3 4.3 4.0 4.2 4.5 3.6 4.1 5.0
##  [541] 3.9 5.0 4.0 5.4 4.4 2.8 3.5 3.7 4.5 4.2 5.6 4.1 4.7 4.9 4.2 4.0 7.4 4.4
##  [559] 4.7 3.9 4.3 5.7 5.7 3.9 3.7 3.9 5.7 3.7 3.2 4.9 5.0 4.0 5.4 4.4 3.5 4.1
##  [577] 4.3 4.9 5.2 5.9 5.8 5.5 4.7 3.6 6.1 4.3 5.1 5.3 3.5 4.5 4.5 5.9 6.1 3.6
##  [595] 4.8 5.6 5.3 5.5 6.0 3.2 4.6 4.0 4.6 4.1 4.7 6.1 4.3 3.8 4.8 4.9 4.7 4.8
##  [613] 3.1 3.6 2.9 4.6 5.2 4.2 3.5 5.1 5.2 2.5 4.5 3.5 4.6 5.4 5.7 4.5 5.6 4.7
##  [631] 3.5 4.7 5.3 3.7 4.8 4.3 4.1 5.5 3.8 4.0 3.3 4.1 4.7 5.1 4.1 5.0 5.3 3.5
##  [649] 3.8 5.0 5.0 5.6 4.5 3.9 5.1 5.0 4.1 3.3 4.5 4.0 3.1 4.7 5.1 7.1 4.6 4.6
##  [667] 5.1 5.2 4.3 4.8 5.1 5.9 5.0 5.4 5.0 3.8 3.8 4.0 4.8 4.9 5.0 3.6 5.6 4.9
##  [685] 5.0 5.7 5.8 4.3 4.4 5.1 5.0 3.6 5.4 3.1 4.3 4.5 4.6 7.0 3.5 3.8 4.8 3.9
##  [703] 4.8 4.3 5.7 3.4 4.8 4.2 6.0 5.8 3.5 5.3 4.1 3.6 4.0 5.6 5.1 3.8 5.0 4.9
##  [721] 3.3 3.5 4.8 4.0 3.2 2.7 5.6 2.7 3.5 5.6 4.0 3.2 4.7 5.0 4.1 4.4 4.8 4.1
##  [739] 4.5 4.3 5.3 5.3 4.1 4.2 4.2 4.6 4.4 4.6 4.4 4.6 4.2 3.9 3.8 4.2 2.0 2.6
##  [757] 4.9 4.2 5.8 3.9 5.7 5.9 4.3 4.9 4.5 4.1 3.6 4.1 5.7 4.5 4.1 5.0 6.3 5.6
##  [775] 5.8 4.9 4.8 5.3 3.2 3.7 4.7 3.2 5.6 4.3 4.1 3.4 3.9 3.5 4.3 4.7 4.9 3.3
##  [793] 3.0 4.4 4.2 3.0 4.6 5.6 4.5 4.5 5.1 3.3 4.9 4.5 2.1 3.5 4.7 5.5 5.1 3.7
##  [811] 4.2 4.7 3.9 4.2 5.0 4.2 5.0 4.7 2.9 4.2 3.9 5.0 6.0 5.0 4.4 3.3 5.0 3.6
##  [829] 4.5 3.1 4.9 3.8 5.2 4.5 3.4 4.8 3.4 3.2 4.5 6.0 4.8 4.8 3.4 4.5 4.3 5.6
##  [847] 2.9 4.3 4.1 4.2 6.7 3.4 4.4 3.6 5.3 3.8 4.2 4.8 5.2 4.2 5.1 2.7 4.4 5.4
##  [865] 3.9 2.3 4.7 4.2 3.7 3.2 4.9 6.2 6.2 3.4 3.7 3.4 3.7 5.0 3.9 3.4 3.5 4.8
##  [883] 4.6 4.4 4.7 5.0 4.3 7.3 5.5 5.6 4.7 4.0 6.5 5.9 5.3 5.2 5.8 2.5 3.5 5.8
##  [901] 5.3 3.7 4.8 4.7 4.1 4.3 4.7 2.8 5.2 3.9 3.4 4.5 3.4 4.1 3.4 4.8 4.2 5.6
##  [919] 5.6 4.0 5.0 4.8 3.7 4.2 4.8 5.4 4.2 4.6 4.5 5.2 6.0 4.7 6.4 5.4 3.9 4.1
##  [937] 4.5 5.1 3.6 4.2 4.7 5.4 3.6 5.7 3.6 3.9 6.7 4.6 6.4 4.5 3.2 4.8 3.0 3.8
##  [955] 5.2 4.6 4.5 2.7 4.0 5.8 4.9 2.3 4.3 5.9 5.2 3.7 4.2 3.2 4.6 3.8 4.7 4.1
##  [973] 4.0 3.0 4.1 4.9 4.1 5.7 5.0 4.7 5.8 4.4 5.1 4.9 5.5 4.0 6.4 5.2 5.2 4.4
##  [991] 5.6 5.8 4.6 4.8 4.0 4.1 4.0 4.5 6.2 4.4
## 
## $func.thetastar
## [1] 0.0132
## 
## $jack.boot.val
##  [1]  0.54385965  0.46766467  0.24664723  0.17275281  0.09211957 -0.06290323
##  [7] -0.08907563 -0.31035503 -0.37329377 -0.54500000
## 
## $jack.boot.se
## [1] 1.020095
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
##    [1] 4.7 3.1 4.0 4.7 4.2 4.7 5.2 3.6 4.3 5.6 5.8 5.4 5.3 3.6 2.8 5.0 4.0 4.1
##   [19] 4.4 3.7 4.7 4.5 2.6 4.5 6.6 4.5 3.9 5.3 4.7 5.0 3.9 4.8 4.2 4.6 3.4 5.2
##   [37] 3.7 5.1 5.3 4.1 3.4 4.4 5.4 6.0 4.4 2.9 5.9 5.9 3.9 4.7 4.9 4.2 5.3 3.3
##   [55] 3.2 3.6 4.4 3.5 3.9 4.5 4.5 4.3 4.0 4.9 4.2 2.2 3.7 3.4 5.7 5.0 3.0 3.3
##   [73] 3.6 2.0 3.8 4.3 4.7 4.7 3.0 3.8 3.2 3.3 4.2 4.7 4.1 4.7 3.7 3.8 3.9 4.4
##   [91] 6.3 3.4 3.6 5.8 3.2 5.7 2.9 3.8 4.9 4.7 4.9 3.6 5.5 4.0 4.0 2.8 3.5 3.3
##  [109] 2.9 4.8 5.1 3.6 4.7 5.3 5.0 4.0 5.3 3.8 3.4 3.6 4.8 3.0 4.9 4.8 7.1 4.7
##  [127] 6.4 4.7 2.9 5.1 2.9 4.4 4.9 4.3 3.3 7.0 3.9 5.4 5.3 5.3 6.1 5.2 3.7 4.4
##  [145] 4.1 5.4 4.2 4.6 4.5 5.0 4.9 2.8 5.0 2.6 4.6 5.3 3.7 4.7 4.3 4.9 4.5 5.4
##  [163] 2.7 3.9 4.0 4.6 2.1 6.6 2.9 4.2 5.0 4.8 7.2 3.8 5.1 4.7 4.5 3.9 5.9 4.4
##  [181] 4.1 4.4 5.2 6.2 5.0 3.5 5.1 5.8 4.2 4.6 4.4 6.2 5.5 3.2 4.3 4.8 4.9 4.9
##  [199] 3.2 4.4 4.2 5.2 5.1 3.8 5.3 2.4 5.1 4.0 2.8 3.4 3.7 3.4 3.9 6.2 3.3 4.8
##  [217] 5.1 4.1 4.5 5.3 5.0 2.7 7.2 3.8 4.8 4.5 3.4 3.6 5.9 4.0 4.1 4.0 4.3 4.1
##  [235] 4.3 3.4 5.8 2.9 5.5 4.7 3.1 4.5 3.9 5.7 5.1 4.8 5.9 4.8 4.6 3.7 4.3 5.8
##  [253] 4.5 6.1 3.8 4.8 4.9 3.3 5.3 5.3 4.5 5.1 4.1 4.6 5.2 3.5 4.9 6.8 5.5 4.8
##  [271] 4.7 4.6 4.5 4.4 4.6 5.4 5.9 4.6 4.8 5.1 3.7 5.4 3.5 4.8 4.6 5.0 5.3 4.0
##  [289] 6.1 4.7 4.6 2.9 5.8 4.3 4.8 4.2 3.6 4.1 3.7 3.8 4.6 3.5 5.3 4.2 4.9 4.2
##  [307] 4.5 5.1 4.9 3.5 4.6 4.0 3.1 4.2 4.0 5.4 4.8 6.0 4.9 2.9 5.0 2.5 5.6 4.9
##  [325] 5.3 5.7 4.2 4.2 5.1 5.5 3.6 4.5 4.7 4.0 1.9 5.3 3.4 4.8 5.0 5.5 5.5 4.5
##  [343] 4.8 3.4 3.2 5.0 4.9 3.2 4.6 6.2 4.1 5.8 3.3 4.2 4.4 4.4 4.7 3.6 5.0 5.1
##  [361] 2.6 4.0 3.9 4.3 3.5 4.2 3.4 5.7 3.7 4.5 4.4 5.4 4.4 5.8 3.9 4.3 5.6 5.5
##  [379] 4.5 5.3 5.4 4.3 3.7 5.8 6.9 4.5 3.8 3.7 5.0 4.4 3.1 3.3 5.4 4.1 5.1 4.7
##  [397] 2.5 4.0 5.1 4.3 5.3 5.1 2.6 3.8 4.9 3.6 3.8 4.6 4.7 4.9 4.8 3.2 3.5 6.0
##  [415] 3.5 4.3 4.8 4.4 5.6 4.5 3.5 4.7 5.1 4.6 3.8 3.2 5.0 4.3 3.9 4.3 5.7 3.7
##  [433] 4.9 4.7 4.8 4.1 4.2 3.3 6.0 2.5 5.0 4.5 4.8 4.8 3.6 3.7 4.1 4.9 5.3 2.3
##  [451] 3.7 4.2 3.9 3.2 5.6 3.6 4.4 3.5 5.3 3.1 3.7 5.2 6.2 4.9 4.8 5.1 4.6 5.8
##  [469] 4.9 5.5 1.6 5.7 3.1 3.8 4.5 5.4 4.0 5.9 6.2 4.6 4.0 3.6 4.7 4.3 3.8 3.2
##  [487] 5.2 2.8 6.6 5.1 5.5 3.2 5.2 4.1 3.5 4.6 3.8 5.9 4.7 5.3 2.9 4.0 3.7 5.4
##  [505] 4.8 5.0 3.3 4.4 4.5 3.5 5.8 6.2 3.9 5.7 5.3 5.2 3.1 4.9 5.6 4.0 4.2 5.2
##  [523] 3.4 3.9 4.4 6.4 4.8 4.9 5.5 4.0 4.8 4.5 4.1 4.6 3.2 4.2 3.8 4.4 5.1 4.9
##  [541] 4.8 2.9 3.5 4.0 3.7 4.2 4.8 5.2 3.0 4.5 4.2 4.7 4.5 4.0 3.2 4.1 5.7 3.7
##  [559] 3.0 3.4 2.6 5.0 3.0 3.7 3.3 4.1 4.4 4.4 4.8 2.9 4.5 4.4 4.7 4.0 5.4 4.8
##  [577] 5.1 5.3 4.7 3.4 4.1 2.7 3.2 3.5 5.0 4.8 3.4 3.5 4.7 4.2 4.0 4.7 5.7 4.2
##  [595] 4.7 4.6 4.5 5.0 4.4 5.4 4.6 4.6 4.7 4.6 5.2 3.8 4.8 4.0 4.8 3.7 4.4 4.8
##  [613] 3.7 3.8 4.4 4.3 4.5 4.3 4.4 4.0 3.7 5.0 5.0 3.2 5.1 3.4 3.5 4.0 4.5 4.9
##  [631] 5.7 4.4 4.3 4.4 3.1 3.9 4.3 5.4 3.8 4.2 5.3 4.4 6.1 3.8 3.8 4.0 3.6 5.1
##  [649] 4.5 4.4 3.4 3.2 4.2 2.7 5.6 4.3 4.3 5.4 3.7 4.4 2.9 5.6 4.8 4.5 2.6 4.2
##  [667] 4.5 4.4 4.0 3.9 5.2 4.6 4.3 4.2 5.2 5.3 4.3 5.8 3.4 5.3 4.7 4.9 2.1 3.3
##  [685] 4.5 5.6 5.9 5.2 3.9 3.8 4.7 4.2 4.7 3.4 4.5 4.7 5.7 5.8 3.0 4.8 5.0 4.4
##  [703] 5.5 5.0 3.8 4.2 5.8 4.9 6.2 4.7 4.0 4.9 4.9 3.9 4.1 4.2 3.9 5.5 3.7 5.8
##  [721] 4.4 3.7 3.7 6.1 5.2 4.2 5.5 4.7 4.6 4.6 3.7 5.8 2.3 5.1 3.8 4.0 3.9 5.5
##  [739] 4.3 5.2 4.1 4.5 4.3 4.8 4.4 5.4 4.6 3.9 5.3 7.0 5.3 3.1 4.0 6.5 4.6 2.7
##  [757] 4.7 4.4 3.5 6.5 2.8 5.9 5.5 4.1 5.3 4.5 3.5 3.4 3.5 4.4 4.1 5.0 3.3 5.5
##  [775] 4.2 4.5 5.2 4.8 3.6 5.9 4.6 3.8 4.3 4.8 5.6 4.0 3.9 3.7 4.9 3.7 5.3 4.1
##  [793] 3.6 5.1 3.2 4.8 3.7 3.7 7.1 5.6 4.4 4.7 5.5 5.9 3.7 4.8 3.1 4.1 3.5 5.4
##  [811] 4.1 3.4 4.7 5.1 3.0 4.0 3.8 4.4 4.6 2.8 6.1 4.9 5.1 4.7 5.4 3.9 4.6 4.0
##  [829] 3.6 2.7 4.0 4.5 3.2 4.2 4.2 3.8 3.4 6.1 3.4 5.4 4.2 3.2 4.9 4.8 4.4 3.6
##  [847] 5.1 4.6 3.7 4.7 6.0 5.5 5.1 5.1 5.4 4.9 3.5 4.2 4.3 6.3 3.0 5.5 3.4 5.0
##  [865] 5.0 3.6 5.5 4.6 4.7 6.2 4.7 5.1 3.9 4.8 5.3 3.8 3.5 3.1 5.1 1.5 4.2 4.0
##  [883] 4.7 5.4 3.3 3.1 4.3 4.7 3.9 4.7 6.2 4.2 4.5 2.9 3.1 4.4 3.5 4.9 5.7 4.1
##  [901] 5.4 5.1 5.2 5.2 4.0 3.6 3.6 4.7 4.5 5.3 4.9 5.7 4.0 4.3 3.9 4.6 5.2 4.5
##  [919] 7.6 4.8 3.5 3.8 6.7 5.7 5.2 4.4 4.9 4.0 3.8 5.5 3.6 4.4 3.7 4.1 5.4 5.6
##  [937] 3.2 4.7 5.1 4.5 4.7 4.1 4.5 3.4 5.3 5.4 4.5 3.1 3.4 5.5 5.5 3.0 5.6 4.6
##  [955] 4.7 4.3 3.5 4.0 5.8 4.7 6.3 4.5 5.3 2.9 3.9 4.8 3.6 4.8 4.1 4.7 2.3 3.3
##  [973] 4.8 3.5 5.9 3.7 4.4 4.0 4.0 4.6 4.8 3.1 4.3 4.6 5.0 4.7 4.1 4.6 4.7 2.8
##  [991] 5.5 5.7 5.3 3.1 6.2 4.4 3.1 5.4 6.6 3.6
## 
## $func.thetastar
## 72% 
## 4.9 
## 
## $jack.boot.val
##  [1] 5.400 5.400 5.300 5.000 5.000 4.896 4.800 4.700 4.700 4.500
## 
## $jack.boot.se
## [1] 0.8907396
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
## [1] 1.765088
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
##   4.664445   6.717860 
##  (2.015837) (3.065364)
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
## [1]  1.00130228 -0.13170199  0.07938776 -0.16157003  0.31944834  0.29609248
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
##    [1]  0.7664601536  1.3776404257  2.1085032134 -0.6254281213  1.9261625486
##    [6]  1.1176310460  0.2760074837  0.2661362688 -0.0173104600 -0.4338364286
##   [11]  1.8672230708  1.1711745202  1.6140384971  2.1211362644  1.0518229578
##   [16]  1.1765331280  1.6023872324  1.7175236450  1.1220106939  0.5230958207
##   [21]  1.2569576746  2.0411095017 -0.2307673797  2.1601389747  1.3730782224
##   [26]  1.2662515169  1.6074438925  2.1002770641  0.1738477747  1.7321474083
##   [31]  2.1459577447 -0.0442024765  1.0867694913  1.2027440356  1.7728856019
##   [36]  1.3782269134  2.2099782428  0.5948939049  1.4473449502  1.8571555321
##   [41]  1.1224824959 -0.5446969818  1.7194457698 -0.1117784989 -0.7276785099
##   [46]  1.8476190405  2.1372699110  2.0361640984  1.3266949915  0.7249412629
##   [51] -0.2123497917  1.2332917929  1.1840281165  1.5447927725  2.5770719838
##   [56]  0.0006905522  1.3901787248  1.3405310758 -0.1611638608  1.9392499162
##   [61]  1.9412307764  0.7018510429  2.3605801321  1.7913836140  1.7956126244
##   [66]  0.3130910347  2.0935019596  1.6859504831  0.0069486237  2.3292716158
##   [71]  1.1785141910  0.7107568548  0.5257361144  0.0845913496  0.0963452275
##   [76]  0.5839790390  1.1968164762  1.1266684135  1.6244142034 -0.2332902715
##   [81] -0.5212957452  0.0978410948  1.1290504654 -0.1841222320  0.5326939693
##   [86]  0.0401147647  1.9372916308  0.6631634356  2.0788708848  1.2220957302
##   [91]  1.7462378282  1.6404749072  1.2494061455  1.1918037612 -0.0242859844
##   [96]  1.0988606025  1.7300932437  1.8821499037 -0.0571051131  1.2412593826
##  [101] -0.0447132014  0.9687618300  1.2030211139  1.0983533688  1.6877353333
##  [106]  0.5503783388 -0.0278544145 -1.3910782082 -0.1444363313  0.8006060580
##  [111]  1.1460222218  1.1664579154  1.9521660533  0.8428467609  2.2331314046
##  [116] -0.0769914951  0.7123565152  0.7901274167  2.1678985818  1.8815911586
##  [121]  0.5271275725 -0.0349897786  0.9527406479  1.2839442521 -0.2790669128
##  [126]  1.8562382892  0.3094341336  2.1923454764 -0.2830686906  1.1213259321
##  [131]  1.9706289256  1.5707261611  1.3236976368  1.2393958211  0.9986187003
##  [136]  0.7017874702 -0.5925121988  1.7686936799  1.9545904124  1.1808436418
##  [141]  2.1105160222 -0.2123497917 -0.0807638356  1.0274101753  0.6656300574
##  [146] -0.3476100122 -0.4390640519  0.3155793748 -0.1769262475  1.9286224400
##  [151]  1.9571025642  1.8543502772  1.9897265427  1.7501994310  1.5115996929
##  [156]  1.5966402334 -0.7397456182  0.9280276351 -0.2169353492  1.8409543191
##  [161]  1.0152325217  1.9106357437  1.3952913840  0.8211802890  1.4259028682
##  [166] -0.4183122659  1.8416178391  1.1017838591  1.9791612783  1.8991756625
##  [171]  1.6095574550  2.1944763121 -0.3632191840  1.5703681006  0.0875325744
##  [176]  1.7664521138 -0.0053528192  1.2799355770  1.3211800544  1.3017051252
##  [181]  2.3134118895  1.2180936854  1.7079857867 -0.5266032011  1.2622344968
##  [186]  2.0832481031  0.7054372304  1.6539575539  2.2029658301  1.4665169692
##  [191]  1.5281157083  1.8796506472  1.9210199107  2.0370324221  1.7739942469
##  [196]  2.1879126473  1.9614082852  1.1399648877  0.0897002081  0.6025246593
##  [201]  2.4235517283  1.7545194251  2.2478175476  0.8076544485  2.1078144223
##  [206] -0.3367749140  1.1150617660  0.5479450524 -0.4030241696  1.3967159147
##  [211] -0.4663202257  1.1762576340  1.5834117179 -0.0484970207  1.9094934127
##  [216] -0.0963846323 -0.0640244107  0.6118608469  0.1078662243  1.7401657040
##  [221]  1.4748010375  1.2597146269  1.0574310795  0.1813067361 -0.1369273713
##  [226]  2.1322270943 -1.0877157499  1.5541740975  0.1426253772 -0.6114398841
##  [231]  1.7768451450  1.8545602045  0.7921560179 -0.2920471740  1.2088811524
##  [236]  1.7746569193 -0.1832161149 -0.1088491235  1.3658171222  1.2337744139
##  [241]  1.0308203367  1.1400579116  1.8661826460  1.1836652872  2.1404626532
##  [246]  0.5076607810  1.7594538041  1.7145624544  1.3609432894  0.3345210393
##  [251] -0.2318662214 -0.3110151113  1.7831611249 -0.2321021505  0.2023825760
##  [256]  1.4836893379  2.1025374023 -0.1006507745  0.0395381681  0.2026318306
##  [261]  0.7165484043 -0.4400391318  1.1678055484  1.7841200805  1.9100472754
##  [266]  1.6549138610  1.1927916419  1.1242558801  0.1363625732  0.3200834257
##  [271] -0.3853787897  1.1739880142  1.3893661196  1.5542064867  0.2230348698
##  [276]  1.9515853875  0.6775459243 -0.0173128319 -0.4000628075  1.1983536037
##  [281]  0.0097426698 -0.2730444205  0.0455647104  1.3748452707  1.6196498743
##  [286]  2.3831796862  1.0350370574  0.5775978681  1.3003105496 -0.0813455518
##  [291]  1.6602064228 -0.2688327059  0.6769719201  0.2353256876  0.7454616774
##  [296]  0.2475465494  2.0366555853  1.7477785280  1.7390725020  0.9512566012
##  [301]  1.8415392935 -0.1442611365  0.0445901299  1.7832325496  1.8929955129
##  [306] -0.1098977018 -0.2451950365  1.0624438528  1.1899107558  1.2373680965
##  [311]  0.7461098397  2.1101658371  1.0337626814 -0.1206964134  2.1816816253
##  [316]  1.6484005961  1.1643402162  1.1274134992  1.7116361911  0.0861677529
##  [321]  1.3934487480  0.1689036421  0.1859907969  1.7589463129  0.5220313497
##  [326]  1.7401494008  1.1664579154  2.0936325269  1.9581523801  1.8913288480
##  [331]  0.2100308586  1.7991511976  0.6904555379  1.3055279205 -0.4621431388
##  [336]  2.2699835158  2.0709580902  1.1926978240 -0.7112945478  1.7857962615
##  [341]  2.0803703456 -1.0594256052  0.5308984015  1.3660810477  0.9438189596
##  [346]  1.8723507835  1.8611419929  2.0620702245  1.1137592647  2.0146799716
##  [351]  1.3660810477 -0.1567184274  0.9765345030  1.3470922970  2.2345995931
##  [356]  1.5834117179  0.1039420724  1.5261503942  1.3505225840  0.6074153212
##  [361]  1.3728800702  0.1663604944 -0.3121537597  1.1638288080  1.0848629463
##  [366]  0.7766627725  1.9266210403  0.2777838152  1.9506226289  2.3210506596
##  [371]  0.8831038168  0.0456520657  1.9187738816  0.2713553256  1.9242525476
##  [376]  0.2843922531  1.7375522666  1.8217417681 -0.4226667055 -0.6045092386
##  [381]  0.1142584436 -0.0085589514  1.8560178511  1.6549138610 -0.3613091366
##  [386]  1.6248748782  0.9814695587  1.9458514256  1.5370572584  0.6050038673
##  [391] -0.4503518249  0.7438667792  0.2233691446 -0.1863597370  1.9482035314
##  [396] -0.1197881064  1.9416726277  1.3893723325  2.3849677519  1.1475805369
##  [401] -0.5827521082  0.3829538445 -0.9695987820 -0.5215373565 -0.3996615558
##  [406]  1.6601187404 -0.2800591636  2.1132097213  1.7131517042  1.1667490444
##  [411] -0.1072486378 -0.0152740349  1.2358903010  0.4787148292 -0.1796893882
##  [416]  0.7806321047  1.0497991015  1.8547021888  2.0727901813  1.5180857695
##  [421] -0.1485871477  2.0461706892  0.2161986911  0.1329738621 -0.4265306057
##  [426]  2.1788736276  0.1572136494  1.8402412963  1.9777137326  1.9781070861
##  [431]  1.2533214987  0.9811674542  1.1729491751  1.0607415974  1.9797405021
##  [436]  1.9086949823  1.8280467438  1.9833656287  0.0201460964  0.2999995934
##  [441]  0.9805308863  0.4706054507 -0.9576605874  2.0737310538  0.3908803734
##  [446]  2.0285183882  1.5529514429  0.6625824891 -0.6362825679 -0.2457307436
##  [451]  1.1551235289  1.3271707357  2.1125851343  0.1282900292  1.2725067379
##  [456]  1.6663161645  0.2328676956 -0.4619826618  1.1475849419 -0.5658778976
##  [461]  1.5349732591  1.6813995303 -1.7849766701  1.3533835181  1.5299866047
##  [466] -0.6031139425  2.5583483667  2.3126970411  1.2333441035  2.1481446254
##  [471]  1.3565106428  0.3951948745 -0.0573273090  2.0340326921 -0.8639971531
##  [476]  1.2042082799  0.0991749261  1.3074703999  0.6792251418  1.2501852564
##  [481] -0.9085559765  1.7469223845 -0.5924018515  1.7663536812 -0.1086293741
##  [486]  1.9873939642  0.5258336112  1.2279589399  0.2152200001  0.1141145233
##  [491]  1.2984125986  1.7386081106  2.1904646570  1.6570621279  1.8899849398
##  [496]  0.1976836962  1.4187716821 -0.3507247176  0.3520058876  2.3814074973
##  [501]  2.2237096900  2.2731680990 -0.0464086162  1.9474761747  1.5850690496
##  [506]  0.6425661637  1.2753460255  1.2352427746 -0.5217679167  1.5150467501
##  [511]  1.1032468939  0.3467717656 -0.1444363313  1.1701788134  1.5524892822
##  [516]  0.5995814209  1.2755670651  1.8484399864 -0.3901700478 -0.0154886354
##  [521]  1.8104748122 -0.2050079518  1.6238298950 -0.5925722737  1.0537925262
##  [526]  0.9321493975  0.3063551813  1.6603666095 -0.4607139477  0.7595769293
##  [531]  1.5721483914  0.5578111043 -0.2420491908  1.7940493811  1.6146282995
##  [536]  0.6922517102  1.4290888787  0.1392590219  0.1236238797 -0.3260289577
##  [541]  0.1235027705 -0.3571367110  1.2942274195  1.9763210918  1.9207082009
##  [546] -0.4972991623  1.8235499564  1.3146390655  1.1787565109  2.0363650687
##  [551] -0.2873083205  1.5503346339  1.1467214768 -0.2459214011  2.2169353683
##  [556]  0.4820631519  1.7365296155  1.1068855655  1.3944879707  1.4307701568
##  [561]  1.1658558777  0.6048993580  0.0947495116  1.4935550622  0.0686789900
##  [566]  1.5885378904  2.0622544402  1.5936695871 -0.1122078406  0.3013968067
##  [571]  1.3873896629  1.9442691110  1.1753252796  0.1246553417  2.0984942731
##  [576]  1.1975043435  2.0049277885  2.1139477649  0.0318895653  1.9182311932
##  [581]  0.5571111616  1.8156807595 -0.1514636975  0.9674990299  1.5721483914
##  [586]  1.0152325217 -0.1248449323  0.6047701676 -0.4890506960  0.4689000082
##  [591]  1.7447266048  0.2060830260  1.8715359512  2.0263897994  2.0410870789
##  [596]  1.4831173298  0.7078014981  1.9468907044  0.7092153637  1.8450404003
##  [601]  0.9856326045  1.5518402476  0.1264868565  0.9655683187  1.9361489046
##  [606]  1.3821277501  2.2949425028  1.8418696130  0.3212928640  0.3163416465
##  [611] -0.2666248956  0.0978210780  1.6036404686  0.1082791425  0.3238688277
##  [616]  1.8771387053  1.7375522666  0.9811674542  1.1283923132 -0.3459970956
##  [621]  0.2015652024  2.0717927460 -0.6565005727  1.1767502090  0.1614088838
##  [626]  2.0927942731  1.8461693744  1.1431379532  0.6876631658  0.1679813761
##  [631]  1.2565438141  0.1765909651  0.6224992557 -0.5709097823  0.6337967762
##  [636]  0.0396338795  1.0113989299  1.8535968017  0.5678431577  0.6102366718
##  [641]  1.8496266075  0.0284015801 -0.0719167441  1.5095667787  0.5250964076
##  [646]  1.7138089393  0.6239394874  0.2535917600  2.2688611393  1.0466313017
##  [651]  1.2650485633 -0.1206161043 -0.7238180742  0.5202124642  0.9715033230
##  [656]  2.4696382735  1.8897878451  1.1252640780  1.7838790281  1.2104127256
##  [661]  1.8323155148  1.1034611746  1.2560094738  0.1349996857  1.2166693630
##  [666] -0.9057097895  1.9780097693  1.1172066484  1.4946557313  0.1321708933
##  [671]  1.7637272123  1.9596756134  1.0768968230  1.1737226113  0.7799160540
##  [676]  1.7830129047  0.9268926899  2.2286576654  1.9365789012  2.3490221801
##  [681]  0.6196638899  1.5525767971  0.6144738222  0.0655985419  1.7465988496
##  [686] -0.1305383598  1.7213146946  1.1838487486  0.3212015889  1.3693905962
##  [691]  0.5416877416 -0.7307521471  2.4659948454  1.8204317423  1.1385100737
##  [696]  0.1308889007 -0.5447230150 -0.0216434946  2.2083865465  1.7079857867
##  [701]  1.9959668985  1.0350370574  1.0900834757 -0.2586450623  0.0734255140
##  [706]  0.7302541209  0.1155609994  0.3973683741  0.5268215726 -0.1707673247
##  [711]  1.7166368515  2.1136471693 -0.2611703309  1.2070387465  0.0963580536
##  [716]  0.7490166108  2.1093529958  1.6553106775  1.9809506913  0.1083996791
##  [721]  1.7326146572  0.0400111053 -0.3811384460  1.1675785782 -0.6782420384
##  [726]  1.4013914991  0.5819389207  1.5703391211  1.9482572898 -0.2654235371
##  [731]  1.0758958218  1.5701858444  2.1833858731  0.3293095534  1.0783646901
##  [736]  2.1038198313  2.0486772113  0.8549274994  1.9062760947  0.6562348972
##  [741] -0.0844727209 -0.3511739972  1.8916614770  1.1399917114 -0.1941795400
##  [746] -0.2046845181 -0.3126301999  0.0741762797  1.0546209379  1.2343633061
##  [751]  0.0853338369  1.8212704457 -0.9415182125  1.3116534464 -0.8157905540
##  [756]  1.0945725218  1.8288985463 -1.5322954710  1.0931667870  1.5441177145
##  [761]  2.3288544897  1.8949566505  1.4832484681  0.3628392494  2.3557857531
##  [766]  0.2634954764  1.1083469511  0.1169624924  1.6897117077 -0.2644553014
##  [771]  1.3656745970  1.0759274524  2.0943629999 -0.1971814632 -0.2513395814
##  [776]  2.1085663301 -0.0757688990  0.5482899733  2.0705700862  1.1372863181
##  [781] -0.4397823718  0.9282651089  1.6087041136  1.9931753221  1.9610522008
##  [786] -0.0126707281  1.8505594618  1.6253472910  2.0719109856  1.8983117528
##  [791]  0.6845167702  1.6679705409  1.9133555388 -0.8891020492  0.6090626267
##  [796]  1.4432923063  1.2485809072  1.6480824431  1.0298770548  1.2213466415
##  [801] -0.6841911798  1.3180850173  0.2956314289  1.1047353069 -0.4844047242
##  [806]  0.1966572368  1.9646819663  2.4941974720  1.8486134051 -0.5675242400
##  [811]  0.2843922531 -0.4106000778 -0.4328075666  1.0402729585  0.5321463456
##  [816]  2.1945951595  2.2134205688  1.7098325272 -0.1581499830  0.5028465390
##  [821]  1.5686903004  1.6896259935  1.7229081060  1.1578475407  0.4397145834
##  [826]  1.9715601767  1.2589020317  1.8673081784  2.2390334849  1.3458104639
##  [831]  2.0824089533  1.8681386744  1.4325842902 -0.5397557266  1.5817825858
##  [836]  1.7226786507  1.8182841720  1.6722341878 -0.1771162237  0.2661362688
##  [841]  1.7702430735  2.1353157458  0.5810394674  0.1368875514 -0.0086081144
##  [846] -0.4469687066  0.6768261201 -0.1077775347  1.0406098982  0.5769453379
##  [851] -0.6546459165 -0.3457737274  1.7356005807  1.2604339447  2.0851100800
##  [856]  0.5069739307 -0.4593497117  1.0920498645  0.0555187610  0.2135261513
##  [861]  1.7584617316  1.2771861797 -0.6500192642 -0.0523882740  1.8066946770
##  [866]  0.3686629857  1.7389444881  0.0097349822  1.1748017935 -0.3327056344
##  [871]  0.0804078306  0.5002769057  1.8461992505 -1.1168482506  1.4952821596
##  [876] -0.1739112978  1.0847365303 -0.2958669511 -0.3467962402  1.9872239362
##  [881] -0.4168694449 -0.1098544063  2.0741979219  1.5936829790  2.1256231606
##  [886]  2.0652408710  0.6147366340  2.2355905309  2.0466309105 -0.3846266658
##  [891]  1.0975983760  1.7266498309  1.1355410651 -0.3352241105 -0.7931039162
##  [896]  2.2670662395  1.0796318926 -0.6510538951  1.3441496122  1.9587453431
##  [901]  0.9701233250  1.9207410998  1.8624976629  1.1821323019  0.3262207247
##  [906]  0.4034184495  1.1523964854  0.6137851358  0.2075808921  1.4842763092
##  [911]  1.9909399248 -0.1290313996  2.0595270107 -0.0747752496  2.2197254399
##  [916]  2.4031266728  1.9495893479  0.2928021784  1.3645097770  1.3638740547
##  [921] -0.3941182543  1.5465987398 -0.2712353171  1.0321679901  1.2644897461
##  [926]  1.9490643742  2.1481782326  0.4948859739  2.0045231465  1.6601187404
##  [931]  1.9099895361  1.7949176840  1.9380941028  0.8260213620  0.8968010726
##  [936] -0.1828906436  1.5713363686 -0.3919993772  2.3588920336  1.0625810325
##  [941]  0.9209076184 -0.5931897197  0.7121184363  1.8177183719  1.7981296776
##  [946]  1.4783355554  1.4539729575 -0.2329007574  2.1766794682  0.0603259117
##  [951]  1.8849831318  1.3077877020  2.2219278833  1.9890468168  1.2183398298
##  [956] -0.8550732018 -0.4146092659  1.9093209042  1.4227986405  1.4185902924
##  [961]  0.3186900810  1.9975350176  2.2990438713 -0.5252330116 -0.7736159517
##  [966]  0.2314697961 -0.0223243297 -0.6963628192  0.1194137759  1.1612862190
##  [971]  0.3793662231  1.8642803944 -0.0736677525  2.1211362644  1.7698549988
##  [976] -0.5633428518  0.9801336244  0.4671689328  2.0797683875  1.1694746008
##  [981]  0.1202850624  1.6877645908  0.7467380945  0.0939081125  1.9741019107
##  [986]  2.3179728241 -0.1485871477  1.3055279205  0.1019190947  1.9086565380
##  [991]  1.8646422119  0.5464060215  0.1663604944  1.2332917929  1.5018217537
##  [996]  1.1827671674 -0.1235009194  1.7843327533 -0.0123708727  1.9741019107
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
##   0.69433672   0.36898049 
##  (0.11668187) (0.08250488)
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
## [1] -0.08112027  0.71638537  1.76969152  0.50933430  0.11443218  0.30521782
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
## [1] -0.0266
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9509241
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
## t1*      4.5 -0.01741742   0.9376435
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 4 5 6 7 9 
## 2 1 1 2 1 1 1 1
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
## [1] -0.0527
```

```r
se.boot
```

```
## [1] 0.8789911
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

