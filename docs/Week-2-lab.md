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
## 1 3 5 6 7 9 
## 1 2 1 2 3 1
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
## [1] -0.0543
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
## [1] 2.686381
```

```r
UL.boot
```

```
## [1] 6.205019
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.6975 6.2000
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
##    [1] 4.1 4.2 5.9 5.6 3.0 4.0 4.5 6.7 5.7 4.9 3.6 3.4 4.2 4.5 6.4 4.1 3.3 4.4
##   [19] 3.9 4.1 4.2 5.5 4.1 4.4 5.4 4.4 4.0 5.2 3.8 4.2 4.3 4.0 4.6 4.0 3.7 4.8
##   [37] 4.9 4.3 5.3 5.0 3.1 5.7 2.8 4.7 3.4 4.7 4.5 3.8 4.3 5.7 3.4 4.2 3.9 4.3
##   [55] 4.4 4.4 5.6 5.3 4.7 3.3 3.7 3.4 3.5 6.4 4.0 4.8 4.4 4.7 4.7 3.6 3.8 4.1
##   [73] 5.7 2.8 4.9 4.9 5.6 4.8 3.8 5.8 5.6 3.9 4.8 3.9 5.1 4.3 6.0 5.3 2.3 5.4
##   [91] 6.7 4.3 4.4 4.5 5.1 4.4 6.1 5.1 4.2 4.3 4.4 4.6 5.0 3.8 3.8 4.2 4.9 4.2
##  [109] 3.3 3.6 4.5 4.3 3.7 4.6 3.9 4.4 4.7 5.5 4.2 4.5 3.1 4.8 5.0 4.6 5.4 4.4
##  [127] 4.0 1.3 4.8 5.2 4.6 4.1 5.5 4.2 5.6 4.3 3.3 5.8 4.6 4.2 4.9 3.7 5.3 3.9
##  [145] 2.8 5.3 4.0 4.9 3.6 3.0 4.0 4.5 3.4 3.6 4.4 5.3 3.6 6.7 4.5 3.2 5.4 3.9
##  [163] 3.9 4.9 4.7 3.2 5.0 6.8 4.4 3.8 3.9 4.9 4.6 3.4 4.1 3.7 5.8 4.6 4.7 4.3
##  [181] 5.4 4.6 4.9 5.2 5.4 5.2 4.4 5.6 4.2 3.2 5.5 3.9 5.1 5.7 3.9 4.7 4.8 4.9
##  [199] 6.2 2.7 4.2 4.7 6.7 5.0 4.7 5.0 3.2 5.5 4.6 4.1 5.7 4.5 4.8 4.5 4.3 3.7
##  [217] 4.5 4.4 5.5 3.5 3.8 3.1 4.6 3.6 6.0 4.1 3.7 3.8 3.9 4.7 4.6 6.6 3.8 6.0
##  [235] 4.1 4.3 3.3 6.1 4.8 4.1 5.2 5.4 4.2 5.5 4.4 4.1 4.2 4.4 5.2 5.5 3.2 5.1
##  [253] 2.6 3.7 3.8 4.7 5.4 3.5 4.8 4.0 2.7 4.9 5.7 4.7 5.5 5.0 3.4 4.3 5.4 4.1
##  [271] 4.5 5.1 3.3 4.4 3.9 4.2 5.3 4.7 6.5 6.7 2.8 4.8 3.9 5.4 4.1 5.5 5.1 4.4
##  [289] 5.9 4.6 3.6 3.6 2.9 4.0 4.4 4.5 5.3 4.0 4.8 3.5 3.8 3.7 4.8 5.0 3.7 5.3
##  [307] 4.4 4.4 3.2 3.7 6.1 3.7 4.3 4.2 5.8 4.9 5.3 4.6 5.1 6.0 4.5 2.3 2.8 3.2
##  [325] 4.3 2.9 4.3 4.3 4.7 5.0 4.3 5.3 3.5 3.3 4.4 5.5 3.8 4.5 3.4 4.7 3.8 4.8
##  [343] 3.5 5.6 4.7 5.0 4.9 4.1 5.0 5.8 4.8 4.9 4.1 4.5 4.8 4.4 4.7 2.9 5.9 3.3
##  [361] 5.3 5.2 4.7 4.3 4.9 3.4 5.6 4.6 6.7 4.1 5.0 5.2 3.7 2.2 5.1 3.7 4.0 4.5
##  [379] 5.1 4.6 4.6 5.7 5.4 4.1 4.9 6.8 5.3 4.6 3.5 3.8 4.6 4.1 3.7 3.7 5.0 4.5
##  [397] 5.1 4.1 4.5 4.1 3.5 3.5 4.8 4.6 4.4 4.1 5.3 3.8 5.2 4.5 4.1 4.9 4.3 4.9
##  [415] 3.8 4.8 3.4 4.2 4.3 4.1 4.1 4.0 4.5 3.5 4.7 5.0 5.2 3.2 4.5 4.4 4.3 4.4
##  [433] 4.3 4.3 3.0 4.6 3.8 3.9 3.6 4.6 4.5 5.3 5.7 6.3 3.8 3.6 5.3 4.8 5.0 2.3
##  [451] 3.8 3.8 3.9 3.5 3.6 3.5 4.8 3.5 6.6 4.3 3.7 4.5 5.2 3.5 4.8 6.9 5.8 5.7
##  [469] 5.9 5.6 4.3 4.8 6.8 2.7 3.4 5.0 3.5 6.3 4.0 2.2 4.3 4.5 5.0 4.9 5.7 2.8
##  [487] 4.3 4.0 4.1 4.6 4.0 3.3 2.4 3.8 4.4 4.4 4.6 4.1 4.6 5.1 6.5 3.9 5.1 4.3
##  [505] 2.8 4.0 6.2 1.6 3.2 4.8 4.3 5.5 5.0 3.5 4.5 4.3 5.4 5.9 4.1 4.9 4.7 4.0
##  [523] 5.2 5.8 4.9 4.4 2.6 4.6 4.7 3.6 3.9 3.4 3.7 6.4 4.8 6.5 3.9 4.2 3.1 5.0
##  [541] 4.6 5.8 5.0 4.0 3.9 5.2 4.0 4.9 3.7 2.3 2.9 3.4 3.8 5.5 4.0 5.5 3.4 2.5
##  [559] 2.9 6.0 6.3 4.5 4.2 5.0 3.2 4.4 3.7 4.4 5.2 4.0 6.5 3.6 4.6 5.6 3.9 2.0
##  [577] 3.4 3.8 5.7 3.8 5.0 4.3 5.8 4.9 4.4 5.7 4.3 5.1 4.4 3.0 2.8 5.1 6.4 4.6
##  [595] 3.5 6.0 3.8 3.6 4.3 4.3 4.0 6.9 5.3 4.5 5.6 5.7 2.9 4.6 4.6 5.1 4.6 4.6
##  [613] 3.6 3.2 4.6 3.6 5.6 4.4 2.5 5.1 2.4 4.3 4.5 4.4 4.8 5.6 5.6 3.9 4.5 5.0
##  [631] 5.3 4.3 2.7 4.1 3.7 5.4 4.9 3.6 4.4 5.4 5.0 4.1 3.2 4.8 4.6 4.5 2.7 4.8
##  [649] 4.2 3.8 5.4 4.4 4.3 4.7 5.9 4.2 5.8 4.2 3.3 4.9 2.6 4.0 4.5 3.3 3.0 5.7
##  [667] 5.2 3.8 3.8 3.5 4.8 4.2 3.4 4.5 4.5 5.6 2.8 4.8 3.2 3.7 3.6 4.1 4.6 4.7
##  [685] 5.1 3.7 4.5 4.4 5.0 4.1 5.2 4.8 3.8 3.8 5.1 3.6 5.4 4.2 4.8 3.9 5.1 6.2
##  [703] 3.5 4.2 4.7 4.3 4.2 6.2 4.5 4.7 4.3 4.9 5.0 4.9 3.6 4.6 4.7 4.9 5.3 5.4
##  [721] 5.7 5.3 5.1 4.8 5.7 4.8 4.3 4.5 4.5 5.7 3.2 3.5 3.8 3.9 4.4 4.7 4.7 5.3
##  [739] 3.7 4.9 4.3 4.0 4.1 3.9 5.3 2.9 5.2 4.5 4.5 5.4 4.4 5.1 3.8 3.2 4.2 4.3
##  [757] 5.6 2.9 2.5 3.3 3.2 4.1 3.8 4.5 5.0 4.0 4.3 3.6 4.8 5.2 5.8 5.8 5.4 1.7
##  [775] 4.6 4.5 4.2 3.3 3.9 4.7 6.2 4.1 4.8 5.1 5.6 4.1 4.9 5.0 5.2 3.9 4.8 6.1
##  [793] 5.8 4.0 3.1 3.5 4.9 3.6 5.4 3.4 5.2 5.4 4.1 4.3 5.0 3.6 5.0 4.8 5.1 5.4
##  [811] 6.1 4.9 5.9 4.6 4.3 4.4 4.6 4.3 5.2 3.5 3.6 2.9 4.5 3.7 4.8 4.9 3.8 5.1
##  [829] 3.8 5.0 6.5 4.9 4.4 4.6 4.2 2.9 5.1 4.6 5.3 4.0 5.8 5.8 4.7 5.5 5.5 5.2
##  [847] 5.1 5.0 5.1 5.6 4.5 5.1 4.3 3.7 4.0 4.9 5.1 5.3 5.4 5.3 5.2 6.7 3.7 6.5
##  [865] 5.2 3.7 4.5 3.9 5.8 3.2 5.1 3.9 3.7 4.6 2.7 5.0 5.4 3.2 4.2 4.8 3.7 3.7
##  [883] 2.4 3.0 3.5 5.3 4.5 4.2 4.7 4.9 5.0 3.5 4.9 5.3 4.1 4.6 4.3 3.2 5.3 4.9
##  [901] 4.0 5.2 3.8 4.6 3.7 3.6 5.4 4.8 4.0 3.6 3.8 3.5 4.6 5.2 4.0 4.5 5.5 4.6
##  [919] 5.2 3.0 6.5 3.7 5.0 5.2 5.0 2.2 5.7 4.2 5.2 4.6 6.3 5.0 5.8 3.2 4.4 3.4
##  [937] 2.9 4.4 5.4 5.2 4.2 4.2 4.0 5.0 4.8 4.4 5.1 3.2 5.8 2.3 4.9 4.4 4.0 5.0
##  [955] 5.6 3.8 6.0 4.2 4.0 5.0 4.0 4.1 3.8 2.3 3.5 5.7 5.3 3.6 3.9 6.3 3.9 3.7
##  [973] 4.8 5.0 4.6 4.1 4.4 3.5 3.6 4.0 4.7 4.3 5.4 6.3 5.0 4.5 3.9 4.2 3.6 4.0
##  [991] 3.4 5.3 4.3 5.0 3.2 4.0 4.4 4.3 5.8 4.1
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
## 2.7000 6.3025
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
##    [1] 6.4 5.2 4.3 5.7 3.9 4.1 4.5 5.4 5.2 3.3 2.9 2.7 3.4 4.2 5.4 5.1 3.3 2.2
##   [19] 3.4 4.9 5.5 5.9 3.8 2.8 2.6 5.6 4.8 4.5 3.8 2.5 3.9 5.0 4.5 3.0 5.5 4.0
##   [37] 6.2 6.8 4.9 5.1 6.3 5.2 3.4 3.5 4.2 4.5 5.5 5.2 3.9 4.4 3.7 2.7 4.2 3.6
##   [55] 4.3 5.8 4.1 3.8 5.0 3.5 3.8 5.4 6.0 4.5 4.7 5.3 2.9 4.8 3.1 4.5 5.4 5.5
##   [73] 3.4 4.5 2.8 3.6 5.0 4.5 5.5 5.3 5.1 4.1 5.7 4.7 5.8 3.2 5.1 4.5 5.3 4.9
##   [91] 5.0 4.8 5.3 4.7 4.2 4.6 4.7 5.1 4.0 4.7 4.3 5.2 5.6 3.8 4.7 3.1 3.6 3.9
##  [109] 3.8 5.0 5.5 4.0 5.9 4.5 4.7 4.6 6.0 4.3 3.8 2.0 4.5 4.2 4.4 3.6 4.4 5.0
##  [127] 6.4 3.4 2.6 6.8 4.7 6.0 3.6 6.7 3.4 5.5 3.6 6.6 5.9 5.1 5.8 4.7 2.9 6.9
##  [145] 3.0 4.8 4.8 4.0 3.3 3.7 3.9 4.4 4.4 3.7 3.0 3.1 4.5 6.3 3.8 5.0 6.1 3.8
##  [163] 3.1 5.3 4.6 3.5 3.9 5.1 5.4 5.5 3.8 4.0 2.9 3.9 4.5 5.2 4.6 6.1 5.1 4.2
##  [181] 4.8 5.3 3.8 3.7 3.5 3.9 4.9 4.9 4.4 4.5 4.8 3.5 5.9 2.9 5.0 4.8 3.6 3.5
##  [199] 4.7 5.5 4.6 3.3 3.8 5.1 4.1 5.2 5.6 3.3 3.1 5.0 4.5 3.9 5.4 3.9 3.0 5.1
##  [217] 4.2 6.5 4.1 4.3 4.4 3.4 6.2 4.7 4.5 2.9 4.2 5.2 3.4 5.2 4.5 4.8 5.8 4.7
##  [235] 5.4 4.2 5.0 6.5 4.1 4.9 4.2 5.6 6.6 5.1 5.5 2.8 3.2 5.1 5.2 5.9 3.7 4.4
##  [253] 4.3 4.6 3.1 4.3 6.1 5.4 3.9 3.5 4.9 4.5 5.0 4.5 4.5 6.6 3.8 3.2 4.3 3.7
##  [271] 2.1 5.2 5.2 4.3 3.9 5.3 5.2 5.3 3.8 5.0 2.8 3.4 3.5 3.9 5.6 3.3 5.6 3.5
##  [289] 4.1 4.6 4.6 3.1 5.5 3.8 3.6 5.0 4.4 4.1 3.8 2.5 4.9 4.2 4.8 5.2 6.1 4.0
##  [307] 5.1 3.6 4.8 4.1 4.9 4.7 3.1 4.5 2.8 4.6 3.3 4.8 4.2 4.6 4.8 4.5 3.5 3.5
##  [325] 4.2 4.7 4.5 5.1 5.2 4.2 3.6 3.7 4.9 5.4 4.7 5.9 5.1 3.3 4.7 5.8 4.5 3.9
##  [343] 3.5 5.4 2.6 5.0 5.5 3.3 4.5 3.7 4.1 5.8 3.8 3.8 2.9 2.2 2.8 5.0 3.8 5.6
##  [361] 2.6 3.5 4.1 5.2 4.4 3.3 4.8 5.0 5.9 2.5 5.4 5.0 5.9 3.2 4.4 6.0 4.6 5.2
##  [379] 3.0 3.7 3.6 4.8 4.0 5.2 3.7 5.5 4.8 5.5 5.5 5.2 3.6 4.2 4.3 5.1 4.6 5.0
##  [397] 3.9 4.9 3.5 5.3 5.0 4.1 4.6 4.1 4.3 5.5 6.3 3.3 4.7 4.0 4.8 5.2 5.0 5.0
##  [415] 3.2 4.4 3.7 3.7 4.8 5.5 6.1 3.7 3.7 4.7 2.8 5.1 2.6 6.3 4.1 5.1 3.7 6.0
##  [433] 4.8 5.6 2.2 4.5 5.8 4.8 4.4 4.1 5.3 4.8 4.7 5.1 2.7 4.2 4.5 4.9 3.6 5.9
##  [451] 3.8 4.9 3.6 5.4 3.9 3.7 4.0 4.1 4.1 4.2 4.8 4.2 4.1 5.5 4.9 4.9 7.2 4.4
##  [469] 4.0 4.6 4.0 5.4 3.5 3.4 4.4 4.6 5.9 5.2 3.6 6.0 4.0 2.7 4.8 2.6 4.1 4.7
##  [487] 4.5 3.6 4.8 4.4 4.0 3.7 4.1 3.0 3.7 4.7 4.1 4.3 4.7 6.7 4.1 5.4 3.0 4.5
##  [505] 4.2 5.0 5.1 5.1 4.8 4.2 5.3 5.6 5.7 5.1 3.8 5.8 4.6 5.7 3.8 4.0 3.5 4.6
##  [523] 3.4 5.7 4.4 3.6 4.0 3.5 4.4 5.0 5.1 5.1 5.2 4.8 5.0 4.1 5.2 5.5 6.0 3.9
##  [541] 4.8 4.5 3.2 4.4 3.3 4.5 4.4 3.7 3.1 4.4 4.1 4.2 4.7 3.9 3.1 3.4 4.8 5.5
##  [559] 5.6 5.2 4.4 3.7 3.1 4.8 5.5 5.0 5.4 3.7 4.4 4.9 3.9 5.2 4.3 5.0 4.2 3.5
##  [577] 4.5 4.8 4.6 5.3 3.8 5.2 4.6 4.4 5.0 4.2 4.7 3.4 3.9 4.4 3.6 5.2 4.4 3.3
##  [595] 4.9 3.8 4.0 4.0 6.3 5.5 4.7 3.6 3.6 4.5 3.8 5.6 3.7 5.0 5.0 4.5 3.1 4.1
##  [613] 4.7 3.3 4.8 5.6 1.9 4.0 5.4 4.4 3.7 4.1 4.0 4.4 5.0 4.6 4.4 5.2 5.1 6.1
##  [631] 4.2 3.6 4.4 5.1 5.2 5.4 5.4 3.8 4.7 4.6 4.1 5.5 6.4 5.8 4.8 6.1 4.7 3.4
##  [649] 4.8 4.5 4.4 4.4 5.5 5.1 4.2 3.4 3.4 7.3 5.3 4.9 4.0 5.2 5.5 4.5 4.8 3.8
##  [667] 5.2 3.9 5.8 4.3 5.1 6.0 4.4 6.0 3.2 5.6 5.8 4.2 5.3 4.2 3.8 4.3 6.7 4.4
##  [685] 4.6 4.3 4.3 3.8 2.9 3.6 4.3 4.7 5.0 3.6 4.2 5.5 4.9 5.8 4.7 5.2 4.4 3.4
##  [703] 3.6 4.7 3.2 4.4 3.7 5.4 4.3 3.6 4.5 5.9 4.0 6.0 4.8 4.9 5.1 5.9 5.2 4.6
##  [721] 6.0 5.9 3.9 2.9 6.3 5.6 5.2 4.8 5.4 3.4 4.8 4.5 4.7 4.8 2.5 3.0 3.7 5.4
##  [739] 5.3 3.3 4.2 4.7 3.3 4.8 5.8 5.1 4.4 3.3 3.6 5.0 4.9 5.0 4.9 3.8 4.2 4.2
##  [757] 2.8 3.9 4.8 4.6 3.6 2.4 4.1 5.3 4.7 3.9 3.6 3.7 4.8 5.1 4.2 5.0 5.2 4.6
##  [775] 6.2 6.0 6.1 2.9 3.5 5.7 3.9 3.7 4.5 5.3 4.2 3.3 4.4 4.2 3.1 4.4 4.2 3.6
##  [793] 4.8 4.3 4.2 4.4 4.9 4.2 5.1 3.6 2.2 4.8 3.9 5.3 5.0 4.6 6.2 2.9 4.3 4.9
##  [811] 4.8 4.9 5.6 5.1 3.0 6.1 4.3 4.6 4.4 4.5 4.8 2.4 3.7 5.0 5.2 3.9 4.3 4.9
##  [829] 6.4 3.1 4.8 4.7 3.1 3.7 4.0 4.8 5.5 3.2 4.5 4.9 4.9 4.7 3.5 4.3 5.4 4.9
##  [847] 3.1 5.8 4.3 4.2 3.8 4.2 3.7 4.4 5.0 5.3 5.5 3.5 5.2 4.9 6.2 4.6 5.8 5.2
##  [865] 3.6 3.5 4.6 4.9 6.6 3.9 3.3 2.5 5.2 4.8 5.2 4.3 5.4 3.8 3.8 3.6 3.3 7.2
##  [883] 6.0 3.5 5.0 3.8 5.3 4.9 3.8 5.4 3.6 4.6 4.8 3.8 4.5 4.8 5.4 6.0 3.9 4.5
##  [901] 4.0 5.7 5.6 3.0 4.4 5.1 6.2 3.5 4.3 5.3 3.8 5.1 5.7 4.4 3.8 3.3 6.6 4.7
##  [919] 3.6 4.6 5.5 3.1 5.0 2.9 2.7 3.5 4.6 4.4 5.7 3.9 5.2 5.7 6.7 4.2 4.0 5.0
##  [937] 4.0 3.9 5.4 5.2 4.8 4.5 6.3 4.2 6.0 4.4 6.5 3.7 6.2 3.7 5.0 3.7 2.9 4.3
##  [955] 5.9 6.5 5.7 6.4 4.7 5.5 4.4 4.8 3.5 3.6 4.6 3.3 5.4 2.7 5.6 2.9 4.7 3.8
##  [973] 4.3 3.5 5.7 3.8 4.6 3.6 4.0 4.5 3.3 5.3 4.1 3.9 3.1 4.8 4.7 3.0 4.8 3.9
##  [991] 2.4 6.1 3.2 5.1 3.8 2.1 3.8 3.2 4.8 4.9
## 
## $func.thetastar
## [1] -0.0077
## 
## $jack.boot.val
##  [1]  0.52521994  0.38276836  0.35226586  0.15000000  0.01162791 -0.06307278
##  [7] -0.19076087 -0.41860465 -0.46607670 -0.56040462
## 
## $jack.boot.se
## [1] 1.085112
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
##    [1] 3.5 4.7 5.5 5.1 4.0 5.4 4.8 4.5 4.5 4.7 5.4 4.0 4.8 3.7 3.2 4.1 4.2 4.3
##   [19] 4.7 4.7 3.3 3.0 4.6 3.4 5.1 4.5 3.1 3.4 4.5 4.2 3.3 4.1 4.6 5.3 3.6 3.9
##   [37] 5.4 4.7 4.0 4.2 4.7 4.8 4.5 3.8 5.2 4.1 5.4 4.7 5.2 3.5 2.2 4.6 4.7 5.7
##   [55] 3.9 5.6 4.4 4.7 4.6 3.1 4.1 2.1 3.8 5.6 3.7 3.7 4.9 5.0 3.6 6.5 4.8 7.2
##   [73] 5.1 4.8 6.0 4.9 3.9 2.8 6.0 4.5 4.0 4.0 4.7 4.5 3.4 4.8 3.4 2.9 2.9 3.0
##   [91] 5.5 6.4 5.6 3.4 4.5 4.3 4.6 4.4 5.5 4.4 5.8 3.5 5.5 3.9 3.4 5.5 4.5 5.3
##  [109] 4.9 2.6 4.4 4.1 3.5 5.8 4.3 5.6 5.0 5.8 4.7 4.5 5.5 4.3 4.9 4.0 4.1 6.1
##  [127] 4.8 5.2 3.5 4.5 3.9 5.1 3.9 4.1 3.9 3.2 4.7 5.4 4.5 4.7 5.9 4.2 5.4 4.4
##  [145] 5.5 5.4 3.9 2.8 5.2 3.1 4.3 3.5 4.2 6.2 5.6 5.4 4.5 4.9 4.4 4.8 5.5 4.1
##  [163] 3.8 4.8 5.8 4.8 3.8 5.6 4.9 4.4 3.4 3.6 4.0 2.5 6.0 4.6 3.9 2.7 3.5 2.7
##  [181] 5.4 4.5 5.6 5.2 4.3 3.9 3.9 3.2 4.7 4.7 5.3 5.3 3.2 2.7 4.8 4.6 4.6 5.1
##  [199] 6.0 5.4 2.9 5.8 6.2 3.6 5.1 4.2 5.4 5.2 5.1 3.8 4.9 5.5 6.4 4.8 3.0 4.8
##  [217] 5.6 5.0 4.2 3.0 3.9 3.6 4.1 5.1 4.5 5.8 4.5 5.4 4.7 4.7 3.5 4.8 5.2 3.2
##  [235] 3.5 4.8 3.8 4.7 4.3 5.1 2.9 3.8 4.8 6.0 3.1 3.7 4.5 5.7 3.8 3.5 4.1 4.4
##  [253] 4.9 5.3 4.6 4.4 5.1 4.4 3.8 2.7 4.0 5.9 4.0 4.6 3.8 4.3 4.4 2.5 5.0 5.7
##  [271] 4.9 4.1 4.8 4.9 4.8 3.8 5.4 4.6 4.1 5.6 5.1 2.9 3.7 6.4 3.7 5.1 5.8 3.6
##  [289] 4.1 4.8 4.5 5.4 4.1 5.3 4.1 5.3 3.3 5.6 5.2 3.6 4.2 3.8 3.1 4.6 4.7 5.3
##  [307] 4.2 3.6 3.6 4.4 4.3 4.9 6.2 4.8 3.4 4.3 6.3 3.7 4.4 4.7 3.7 4.3 4.9 4.7
##  [325] 7.0 4.9 5.3 3.9 2.9 3.6 3.9 5.6 3.7 3.7 4.5 5.1 4.4 3.7 4.7 3.4 3.6 3.9
##  [343] 6.0 4.8 4.7 5.0 5.0 7.2 5.1 2.3 5.5 4.3 4.4 5.3 4.7 4.3 5.4 5.1 3.7 3.1
##  [361] 4.6 5.1 4.4 4.6 5.5 4.6 4.8 4.1 3.8 5.5 4.9 4.0 4.3 3.8 6.2 6.1 5.2 4.2
##  [379] 4.8 5.0 3.9 3.1 5.1 5.7 3.9 3.7 6.2 4.6 5.3 4.3 4.4 5.1 4.8 4.1 5.1 3.8
##  [397] 3.7 3.6 4.9 5.3 4.5 4.6 4.6 3.5 6.2 4.4 5.8 5.3 4.6 4.1 5.8 5.1 6.7 5.3
##  [415] 4.9 3.2 6.9 4.9 4.2 3.8 4.1 3.5 5.3 4.9 5.5 5.2 3.8 3.0 4.2 2.9 6.2 5.8
##  [433] 4.8 4.4 5.5 4.5 4.4 3.9 5.5 4.4 5.4 5.1 5.1 4.8 5.0 3.5 3.6 3.8 4.2 4.5
##  [451] 3.0 3.8 6.6 3.0 4.7 3.2 5.4 5.0 4.0 3.8 4.8 3.7 3.2 4.1 5.2 4.0 3.9 3.8
##  [469] 4.5 4.4 3.5 3.6 4.5 5.0 5.4 2.8 5.3 5.3 4.2 4.6 5.6 5.6 5.8 4.2 3.4 4.2
##  [487] 3.0 3.8 5.7 5.2 5.1 4.7 4.6 4.4 4.1 5.1 5.4 4.9 4.8 4.4 4.3 5.7 5.3 4.9
##  [505] 4.1 2.9 3.2 5.7 3.5 4.8 5.5 4.9 5.8 4.3 4.8 3.1 3.5 4.4 5.5 5.2 3.4 4.3
##  [523] 5.3 4.0 4.5 3.4 4.3 4.8 4.2 4.7 5.6 4.9 5.4 4.0 5.7 4.3 3.6 4.9 4.1 3.9
##  [541] 5.2 5.0 4.2 6.0 4.8 4.5 3.7 4.4 4.3 3.6 4.6 5.3 4.1 6.1 4.9 4.4 4.8 3.8
##  [559] 4.0 6.1 4.3 4.5 3.2 5.3 3.7 3.9 5.5 4.5 4.7 4.1 5.3 3.3 4.5 3.7 3.7 5.5
##  [577] 5.3 5.3 4.3 2.8 5.6 4.4 5.8 4.7 4.1 4.7 3.5 5.7 3.4 2.9 3.3 3.7 4.8 4.4
##  [595] 2.2 4.1 5.3 5.4 5.0 4.7 3.4 4.2 3.2 4.9 4.6 3.9 5.1 3.6 4.0 4.4 4.2 4.0
##  [613] 4.2 4.3 3.8 5.0 4.8 5.6 3.7 3.4 4.2 4.7 5.5 3.2 6.0 4.3 5.2 6.0 4.5 5.5
##  [631] 3.9 5.6 3.3 6.4 5.2 2.8 5.7 3.2 5.1 5.3 3.8 4.3 4.8 5.1 3.8 5.9 4.2 5.0
##  [649] 4.8 3.7 3.0 4.3 3.7 4.7 4.3 3.7 3.7 4.4 4.2 4.5 4.3 4.0 4.8 4.1 4.5 4.7
##  [667] 3.6 4.7 3.6 4.8 4.8 4.2 5.6 4.1 4.3 3.6 4.7 5.2 6.4 5.2 4.3 4.4 5.2 4.2
##  [685] 6.4 4.6 5.5 5.5 4.1 3.9 4.7 4.9 3.9 5.7 5.2 5.1 3.7 3.9 6.0 5.4 5.1 3.3
##  [703] 5.3 3.5 6.1 5.0 5.4 4.4 4.5 4.8 5.3 4.8 4.7 4.1 4.1 5.3 2.8 4.1 4.8 4.0
##  [721] 5.5 4.1 5.2 5.5 3.8 4.2 3.2 5.4 3.3 4.6 4.6 3.3 5.4 5.9 4.3 3.7 4.1 3.3
##  [739] 5.3 4.9 4.5 4.9 4.9 3.6 2.9 4.1 4.5 4.2 3.5 3.5 4.7 5.3 7.3 5.5 5.7 3.7
##  [757] 6.1 4.2 3.2 4.9 5.8 6.8 5.0 5.6 5.0 3.4 4.0 4.8 5.3 3.1 4.2 3.4 4.3 5.0
##  [775] 5.5 4.1 4.8 2.0 5.7 4.3 5.1 4.1 5.2 3.8 4.8 3.1 4.7 4.2 4.2 2.7 4.3 3.0
##  [793] 4.3 6.2 3.6 4.3 5.5 3.1 4.6 3.7 4.6 4.9 5.7 5.1 4.1 4.1 4.9 3.7 4.9 3.9
##  [811] 4.8 5.1 5.1 2.9 5.8 3.7 4.3 4.3 4.6 5.3 4.1 4.8 4.5 3.2 3.3 4.5 3.5 5.6
##  [829] 4.5 2.5 4.3 5.2 5.5 3.5 4.4 3.9 3.8 4.5 5.1 4.6 4.5 4.5 5.3 4.1 6.1 4.5
##  [847] 3.0 5.5 5.6 4.8 3.2 4.1 5.3 6.5 5.0 5.0 4.1 6.6 4.5 4.3 5.3 5.2 3.7 3.9
##  [865] 5.0 4.4 5.4 4.4 4.4 3.3 4.0 6.5 4.8 3.7 5.0 4.5 4.7 4.6 4.5 4.0 3.2 5.5
##  [883] 3.7 4.4 3.0 3.8 4.5 4.0 5.2 4.0 5.3 4.4 5.7 3.2 6.4 5.0 4.3 5.3 3.4 4.2
##  [901] 4.1 4.3 4.2 4.3 3.9 3.2 3.7 3.6 5.2 4.5 4.0 3.9 4.5 4.4 5.0 3.9 4.5 3.8
##  [919] 4.0 3.6 3.8 4.1 4.7 4.0 4.8 3.5 3.9 3.8 3.8 5.0 3.8 4.0 4.7 3.7 6.2 5.8
##  [937] 5.4 4.4 4.5 4.9 4.1 3.8 5.4 5.0 3.1 5.9 3.9 4.7 5.4 3.6 5.7 3.4 4.5 4.3
##  [955] 4.7 4.7 5.3 4.2 4.1 5.8 5.6 4.9 5.3 3.7 4.2 5.4 4.4 5.3 4.0 5.2 3.4 4.7
##  [973] 4.6 4.9 6.1 3.7 2.8 3.6 3.4 5.2 4.1 5.1 4.4 4.5 4.6 4.4 5.6 4.2 4.5 4.1
##  [991] 4.6 4.0 5.4 4.5 4.3 3.2 3.2 5.7 5.3 4.3
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.400 5.300 5.200 5.100 4.944 4.900 4.800 4.612 4.500
## 
## $jack.boot.se
## [1] 0.9056057
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
## [1] 1.737746
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
##   5.943965   8.740674 
##  (2.587062) (3.969645)
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
## [1] -0.18019640 -0.17320107  0.91371166 -0.08048664  0.99537350  1.25539045
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
##    [1]  1.682541950  0.066184785  1.014002421  2.084731523  1.715442421
##    [6]  1.400293111  1.652280896 -0.297194115  1.144658955  1.119846536
##   [11]  2.041629894 -0.252358922  2.102055847  1.798053023  2.071709151
##   [16] -0.856642823  1.782097076  0.682615045 -0.438981934 -0.156114834
##   [21] -0.235503282 -0.353805395  1.687504572 -0.347419286  0.965861958
##   [26]  1.640447703 -0.290345473  1.090154713  1.398255216  0.273680459
##   [31] -0.940229189 -1.021500361  1.348391471  0.097697273  0.655609815
##   [36]  2.000760798  1.732106249  0.204658798 -0.647894957  2.183360406
##   [41]  1.893052186  2.470055886 -0.580446607  2.174754490  0.975337023
##   [46]  1.687087063  0.038965276  1.906529109 -1.247924836  1.715160585
##   [51]  1.522491995 -0.034712648 -0.748613824  0.386865234  1.452784168
##   [56]  0.139432152  0.005854342  0.741577157 -0.096281122  0.996669875
##   [61]  1.341316368 -0.493309533 -0.448963911 -0.208193352  1.381004215
##   [66]  0.662467461  1.660148501  1.816545712  0.929459108  1.309880493
##   [71] -0.236479382  0.358878470 -0.324765573  1.181700938  1.287219698
##   [76]  1.205383556 -0.488155089  2.022685036  1.817554239 -0.104148357
##   [81]  1.716597132  1.887465223 -0.915942512 -0.348980149  1.519662681
##   [86]  1.897210278  0.354165666  1.963382396  0.028823886 -0.071209691
##   [91] -0.487139655  1.045389642  0.301749333  1.031279643  2.310949536
##   [96] -0.024145261  0.667546237  1.583205916  1.725067860  0.558380705
##  [101]  0.780439648 -0.355478839  1.781306386  1.803808417  1.502709815
##  [106]  1.251013972  1.810896097 -0.455231781 -0.231955164  0.384645418
##  [111]  1.520091966  1.577182381  2.175937593 -0.677758366  1.466613721
##  [116]  2.152816268 -0.030673420  1.723180265  0.608379008  2.395873046
##  [121] -0.680071537  1.967488067  2.120743511  0.214325465 -0.554645955
##  [126]  1.924864756  1.047856985 -0.592185858  0.539360554  1.049038957
##  [131]  1.918338014  0.587679620  0.690548619  1.666173937  2.448278892
##  [136]  1.056216166  1.761472259  1.811658392  1.793660350  1.703164014
##  [141]  1.209253910  1.231536982  1.618007761  1.681295894  1.749688159
##  [146]  0.496963277  1.758465427  1.292034705  1.180518787 -0.285968867
##  [151]  1.410070673  0.673210481 -0.193201347  1.776695114 -0.361816219
##  [156]  1.174835697 -0.891834196 -0.483914180 -0.175254068  1.713602017
##  [161] -0.609496712  1.131252010  0.030229593 -0.093710492  1.290768036
##  [166] -0.287825850 -0.534442403  1.153350261 -0.634527834  0.614397911
##  [171]  1.014308641 -0.464469858  1.805196866  0.122767878  1.161597216
##  [176]  1.992482553  1.813027951  1.661199972 -0.289730075  1.114437974
##  [181]  1.508447370  1.013224429  1.134740562  1.632947165  1.967917108
##  [186]  1.816545712 -0.218647240  0.130468038  1.378834491  1.158424882
##  [191]  1.531190048  1.122683457  2.031028668  0.644275430  1.156423369
##  [196] -0.764391101  0.041192673  0.439818094  1.628689540  1.093592889
##  [201]  1.706699858  1.702122360 -0.909831091  1.171795433  1.851682177
##  [206]  1.049446551  0.290840255  1.220063969  0.613026822  1.743932815
##  [211]  0.945631277  0.093576053  0.276133859  1.792224919  2.255372680
##  [216]  1.129779119  1.139450219 -0.429526758  1.901647054  0.553521015
##  [221]  0.027507004  1.700958005  1.498544318  1.939506315 -0.104691714
##  [226] -0.721160734 -0.204984692  1.735787129  1.179034267 -0.455047854
##  [231]  0.247964695  0.589130154  0.636198756  2.111812451  0.629919338
##  [236] -0.348823122 -0.307678044 -0.165316687  1.044076229 -0.509971079
##  [241] -0.741668414  0.231676407 -0.054749682  1.277013984  1.864582023
##  [246]  1.859441841 -0.346612570  1.821473885  1.173482404  1.781309177
##  [251]  0.570499759  0.460731995 -0.799509025  1.726873360  1.197200537
##  [256]  0.384333006 -0.310727549 -0.589799390  0.078512986  0.109097563
##  [261]  1.167758594  1.077348357  0.394798298  2.034151522  0.242370607
##  [266]  0.092134814  2.152542591 -0.322281438  0.790595983  1.716663992
##  [271]  0.688816673  1.243948782  0.146431144  0.480787455  1.187343470
##  [276] -0.603719480  1.629241028  1.350496810  0.716081983 -0.072700113
##  [281]  2.260394081  1.798311237  1.750785705  0.674456712  0.172592694
##  [286] -0.693847756 -0.049915144  0.920551142 -0.002399170  0.108954553
##  [291]  2.299765504 -0.813899764  1.132344264 -0.801473781  1.819753957
##  [296]  1.133845485  1.985170647  1.053085795 -0.074467016  0.399984278
##  [301]  0.014416477 -1.729083315 -0.629654652  0.240634709  1.144749497
##  [306]  0.925844247 -0.241404529  1.866772095  2.017564984  1.534268281
##  [311] -1.132909746  0.349406520  0.594450470 -0.197414415  1.997903071
##  [316]  1.896177894 -0.027177658 -0.270989431  0.494320133  0.323221837
##  [321]  1.760613376  1.999839614  1.072339827  1.073152405  1.170015933
##  [326]  1.993219344 -1.233051716  1.209771371  1.033063023 -0.164552217
##  [331]  1.922939576  1.277324576  0.668203482  0.673210481  0.041787619
##  [336]  1.737360452 -0.331111862  0.660125360  1.935117732  1.259526487
##  [341]  2.259278825  1.758620127  0.144755418  1.161874957  1.153492177
##  [346] -0.395148527  2.000651443  2.197638173  1.011969844  1.315981688
##  [351]  1.785978634 -0.842183330  0.136956981 -0.290661997  1.164361906
##  [356]  1.798383700  0.099599785  1.092039188  1.444040824  1.928074806
##  [361]  1.974270310  1.951562575  1.482198880  1.289724908  1.636351922
##  [366]  0.475687478  1.429234526  1.843175792  1.895888531  1.824885210
##  [371]  0.006216596 -0.532159042 -0.854022388 -1.001418552 -1.613031528
##  [376]  1.369201961 -0.155048203 -0.320067209 -0.406224411  1.287371096
##  [381]  1.231536982  0.721929588 -0.679990315  0.578781399 -0.704887646
##  [386]  1.320037352  1.021247062 -0.330668211  1.858267460 -0.637259738
##  [391] -0.851226287  1.759622135  1.710404209 -0.081476348  1.083674011
##  [396]  1.610617442  1.389685304 -0.106360665  1.723226542  2.042332688
##  [401]  1.924786826  1.118428345  0.321085740  0.355968186  2.069743375
##  [406]  2.414026551  1.183348008 -0.548118601  0.217507544  0.010360694
##  [411]  1.859441841  0.056172920  0.195830658  1.217644773  1.195902027
##  [416]  0.362795813  0.680308996  1.399546223  1.488509283  1.186646149
##  [421]  1.770117872 -0.733790834  2.194317285  0.035390349  1.731115099
##  [426]  1.750889018  1.854903245 -0.527105946 -0.565758176  0.360013552
##  [431] -0.041808941 -0.629462114  2.100823574  0.308100998  1.797815055
##  [436] -0.450265169  0.279144743  1.962763162  0.389404230 -0.948493593
##  [441]  2.034594056 -0.446484779  1.827942254 -0.077287972  1.339820080
##  [446]  1.755848439  1.773033206  1.128117358 -0.717328282  1.254225489
##  [451]  1.824402392  1.398255216  1.828241151  2.031797231 -0.320401004
##  [456]  1.983599420 -0.286534827  1.834256256  1.895742656  2.274702698
##  [461]  1.187616817 -0.061951244  1.911809679 -0.094200619  0.691737953
##  [466] -0.659169044 -0.768073034  0.554944105 -0.476938524  0.106049252
##  [471]  2.277258235 -0.603330890  0.388045359  1.125660843  1.207834636
##  [476]  1.358757963  1.138178645  1.740056415 -0.778083395 -0.854416476
##  [481]  1.235888463  0.610232870  0.627381572  1.018204366  1.586562288
##  [486]  2.040603908  1.025450067  1.763737157  1.884937194  1.729466020
##  [491]  1.052340538  0.089929623 -0.720263295  0.081729573  1.733061306
##  [496] -0.535068079 -0.040599032  0.653715295 -1.126559855 -0.486667066
##  [501]  0.264672371  1.224183513  2.189506510 -0.894253591 -0.355157948
##  [506]  1.058727217  1.070281892  1.961612837  1.504220909 -1.598449005
##  [511]  1.773716336  2.138906200  2.165931822 -0.555782362  1.022871251
##  [516]  1.300937627  1.696461106  1.960221263 -0.631485602  1.052981411
##  [521]  1.286645241  1.663645242  1.261889953  1.115877038  1.063210810
##  [526]  2.020326891  0.654117908  1.043030750  1.791596337 -0.100607676
##  [531]  1.625938928 -0.460797908  1.570524539 -0.599899005  1.918258744
##  [536] -0.724145871  1.962722803  0.556086529  1.959840420  2.244274789
##  [541]  1.813516909  0.687126185 -0.300487381  1.432058986  0.190651690
##  [546]  1.792517443  1.127897471 -0.601749584  1.693098346  2.207281926
##  [551]  1.473674650  1.174194603  0.702410289  1.070200775  1.558663909
##  [556] -0.946655069  1.588417722 -0.366589654  1.971065525  1.842630251
##  [561]  0.275560336  0.536753016  2.105698052 -0.350476066  2.109994132
##  [566] -0.693532216 -0.695455935  1.170037798  1.841117285  1.432452903
##  [571] -0.447487766 -0.050005524  2.245086189 -0.441340909  1.718507545
##  [576]  2.369070753 -0.206906282  1.860227536  0.607047987  1.101476421
##  [581] -1.178057073  1.277660505  1.908555065  0.014202480  1.969486181
##  [586]  1.861497987  0.589381380  1.804872768  0.202110794  0.226582882
##  [591] -0.589027678  1.671768146  1.117723336  1.752526587  1.945443743
##  [596]  2.108887418  1.070861788  1.023732443 -0.348977810 -0.736971918
##  [601]  2.212992180  1.373100995  1.706307156  1.113672595 -0.781906778
##  [606]  1.142501066  1.873889428  2.291547059  0.027363429  1.955688156
##  [611]  1.045843547  1.935694955  1.909219745  1.827765118  0.674725360
##  [616] -0.579535066  1.353903199  0.649727659  0.971341565 -0.551652583
##  [621] -0.247955561  1.709447314 -0.769372986  1.309337837  0.066282940
##  [626] -0.452654513 -0.286248559  2.005706780 -0.047785091  0.056876283
##  [631] -0.699015140  1.882040102  1.239101073  1.249525771  1.020946907
##  [636]  1.918894450 -0.076634030 -0.228016036  1.558895772  1.194349292
##  [641]  1.045341898  2.079764304 -0.009994032  1.069228250  1.066511290
##  [646]  0.196125512 -0.844770012  1.049990510  0.313953393  0.791405901
##  [651] -0.396614386  1.585752495  0.006093864 -0.498445086  0.636723354
##  [656] -1.340626734 -0.119319183  0.692494577  1.296277227  2.254367962
##  [661]  1.915250823  1.087650021 -0.306274959  2.306678785 -0.658763244
##  [666]  0.793587374  0.969068111  1.989992678 -0.452678922 -0.187477794
##  [671]  1.838097035  1.026186548  1.625362572  0.664349496  1.649316540
##  [676] -0.248628123 -0.082140311  1.519343787  1.559338188  1.901709852
##  [681] -0.376106997  1.422410132  2.291013496  0.767687333  1.629995242
##  [686]  1.371666170  1.571384652  1.165666519  1.783494309  0.137662867
##  [691] -0.741148140  1.753572751  1.637058614  1.127357803  0.031873781
##  [696]  0.709019922  0.648921727  1.852280241 -0.352708269  1.088194178
##  [701]  0.214415450  1.743877500  1.047501248  1.083154530  1.200234582
##  [706]  1.387725377  1.217644773 -0.487482669  1.753693812  1.797306968
##  [711]  1.177022048  0.782354850  0.333287388  2.037341285  2.105750390
##  [716]  2.030289803  1.751578816 -0.333249004  1.100856606  1.648156845
##  [721]  1.466152042  2.050098122  1.580347652 -0.362910203  1.940877081
##  [726] -0.747374473 -0.104901648  1.678935036 -0.228862383 -0.030271189
##  [731]  1.803657101 -0.047701267  1.902035294 -0.165062875  1.147301736
##  [736]  0.194213852 -0.019087895  0.189140276 -0.668160622 -0.452654513
##  [741]  2.036647484  2.052174101  1.014776898  2.096649380  1.087837523
##  [746]  1.654026605  1.904062910  1.531152989  0.823682348  1.617434382
##  [751]  0.162042789  1.496414915  1.770262769  0.723403511 -0.428730562
##  [756]  1.072714849  0.687120051  2.040291507  0.478195523  1.158565001
##  [761]  1.999259079  1.621475565 -0.542796145  0.907719846 -0.134970236
##  [766]  1.160811536 -1.433542706  0.752390958 -0.718588454  0.161423289
##  [771]  1.914112807  1.630655567  1.642465914  1.290193486  1.117841935
##  [776]  0.944711749 -0.864304180  1.934239299  1.827121409 -0.602052673
##  [781]  2.222746152 -0.537541193  1.222160388  1.479626937  2.389101309
##  [786]  1.315021888  0.668731369  2.272379417  1.428674625 -0.018297508
##  [791]  1.895657523  0.083708852  1.794070580  0.042266061 -1.036003952
##  [796]  1.578639996  1.219570485  1.659983581  0.636139461 -0.015929779
##  [801] -0.308239359 -0.833301698  2.274009142 -0.326020197  0.210194196
##  [806]  1.830056580  1.963382396  0.873083438  0.678598200  0.644820476
##  [811]  0.374712381  1.256945949  1.553310718 -0.107307486  1.239453006
##  [816] -0.592935576  1.323171853 -1.007184798  1.194095639  2.118717293
##  [821]  1.288370245  1.376770384  1.053601623  0.219445827  1.125116019
##  [826]  1.003038814  1.875614029  0.939091265  1.584496683  1.851030590
##  [831] -0.083093657  2.267902078  2.155439991  0.958332722 -0.614954771
##  [836] -0.089885190  1.791150821 -1.191967404 -0.617622270  1.868526282
##  [841] -0.061063244 -0.135456823  0.664022497 -1.002231537  0.218426127
##  [846] -0.364046113  0.908405578  0.632887739  1.724442786  1.856629164
##  [851]  0.144788937  1.044411378  1.066008310  0.135500043  1.654042872
##  [856]  0.923289746 -0.581241571 -0.017715124  1.607700347 -0.667004406
##  [861]  1.714928632  1.376500481  1.030910692  1.121293745  1.184626259
##  [866]  1.094567693  1.165870234  0.051355315  2.002896648 -0.122906773
##  [871]  2.382761387  1.697378092 -0.489894611  1.725984951  0.067258623
##  [876]  1.572112842  2.022543728  0.216449606  1.098488292  0.112381433
##  [881]  1.682493729  1.654064871 -0.206660949  1.738835207  2.102063176
##  [886] -0.507505993  1.698222857 -0.218252454 -0.556101330 -0.067819454
##  [891]  1.833541250  2.078112395  1.080313192  1.397102006 -0.366404460
##  [896]  1.084982693 -0.294808459 -0.507323673  1.633389261  0.690578362
##  [901]  2.321232641  2.169457666  1.893284462  0.842456159  0.379758417
##  [906]  1.685444344  2.050882480  1.099365632 -0.574046553  1.245660100
##  [911] -0.585790496  1.055385941  1.115474311  1.044763671  1.744338223
##  [916]  1.187113091  1.312572779  1.163733834  1.785122585  1.697409920
##  [921]  0.552312420  1.002324460 -0.569509871  1.580868686  1.806831051
##  [926]  1.054458859 -0.299857512  1.911973288  1.496450858 -1.529149005
##  [931] -0.038434978  1.714080457  1.729367680  1.182233662  1.957429336
##  [936]  2.149345942  1.619075922  0.621972544 -0.180568601  0.422617526
##  [941] -0.452101404  0.986320250  0.933491775  2.072819028 -0.300736643
##  [946]  1.913435602 -0.587986423  1.768617166  0.736566801  1.795869317
##  [951]  2.289040838  1.513658169  2.044398932  1.675363938  1.663824431
##  [956]  1.780642448  1.738322589  0.385957779  2.097307795  0.576661500
##  [961]  1.266043262 -0.101409277  0.280428995  1.237463237  1.861755192
##  [966]  2.175289626  1.203786928  1.680426074 -0.075467647  1.765863210
##  [971]  2.136675722  1.776902302  0.294657887  0.004855767  1.295700222
##  [976]  1.554813608  1.209601415  1.833221805  1.156782673  1.071735509
##  [981]  1.193268313 -0.025244105  1.004391103  0.596571454  0.676012009
##  [986] -0.278186509  1.274702136  1.852006791  2.011864932  0.367347908
##  [991] -0.575274034 -0.401872633 -0.021504318 -0.476853483 -0.701097809
##  [996]  0.300323217  1.028877303  1.959412161  0.692242800  1.047098658
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
##   0.6800369   0.3182832 
##  (0.1006500) (0.0711680)
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
## [1]  0.3546411  0.1677528 -0.2454866  0.8120601  1.1080410  0.5033178
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
## [1] 0.0424
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9296486
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
## t1*      4.5 -0.01531532   0.9120193
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 6 8 9 
## 2 2 1 1 4
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
## [1] -0.0065
```

```r
se.boot
```

```
## [1] 0.8972808
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

