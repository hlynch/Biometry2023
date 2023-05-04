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
## 1 2 3 4 6 8 9 
## 1 2 1 1 2 2 1
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
## [1] -0.0209
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
## [1] 2.723378
```

```r
UL.boot
```

```
## [1] 6.234822
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
##    [1] 4.2 3.6 3.9 4.6 4.4 4.6 5.7 6.1 4.5 3.4 4.4 4.0 4.8 4.5 4.0 4.2 5.3 5.0
##   [19] 4.0 5.8 4.3 4.5 5.3 5.1 4.5 4.4 3.7 6.2 5.7 4.4 4.0 4.3 4.8 3.9 5.0 4.6
##   [37] 5.1 5.2 5.7 4.5 4.3 4.2 3.4 3.4 4.7 3.5 5.1 4.2 5.0 3.2 4.1 3.9 5.7 3.6
##   [55] 4.5 6.1 4.5 5.1 2.8 3.8 5.8 4.8 4.1 4.6 5.3 5.4 3.5 5.9 5.9 3.6 5.9 4.3
##   [73] 5.6 5.3 4.0 4.8 4.7 5.0 2.4 4.6 4.3 5.4 4.3 5.1 3.7 3.3 4.7 3.5 4.4 5.3
##   [91] 4.3 5.0 5.7 4.9 3.5 4.8 4.4 3.5 5.0 5.9 5.5 4.2 3.0 4.1 5.7 3.2 3.3 3.4
##  [109] 4.2 4.2 4.6 4.3 4.2 5.4 5.3 4.2 2.7 5.2 4.6 5.8 4.0 3.5 4.9 5.7 5.2 3.3
##  [127] 4.4 5.5 5.5 5.2 3.6 4.2 3.7 3.2 2.5 5.2 5.2 5.3 5.6 3.4 4.8 5.9 4.3 3.9
##  [145] 3.8 6.7 4.5 3.8 4.8 5.0 4.0 5.2 5.5 3.5 5.7 4.0 3.8 4.4 5.7 3.8 6.5 4.1
##  [163] 5.7 4.3 5.1 4.8 3.8 4.6 4.9 5.6 4.5 3.2 4.5 4.2 4.7 4.4 4.1 4.1 5.6 6.3
##  [181] 5.3 4.9 5.1 4.4 4.7 5.6 4.2 4.1 3.9 4.6 6.2 4.9 2.9 4.6 3.2 2.8 3.9 4.9
##  [199] 4.3 5.4 4.0 5.4 4.6 3.8 5.3 4.7 5.7 3.9 6.0 6.1 4.1 6.9 5.6 5.4 5.7 4.2
##  [217] 6.2 3.2 5.7 3.9 6.0 5.0 3.7 4.6 6.5 4.6 5.3 5.8 6.1 3.8 3.0 6.2 5.4 3.2
##  [235] 5.2 4.3 5.3 4.8 4.6 6.2 4.2 6.4 4.8 4.0 4.0 4.4 4.9 3.5 5.0 3.3 3.3 4.4
##  [253] 5.9 3.6 4.9 4.2 4.7 4.7 4.1 3.9 5.3 4.8 3.6 5.4 3.3 4.8 4.7 3.2 4.3 3.4
##  [271] 4.6 3.9 3.8 5.2 4.0 4.4 4.8 4.8 5.5 4.4 4.7 5.6 4.1 3.8 4.1 5.3 4.9 5.2
##  [289] 2.8 4.1 3.5 5.3 4.9 3.2 3.4 4.3 5.0 4.4 3.9 4.6 4.8 3.7 4.5 4.1 5.1 5.0
##  [307] 3.3 4.4 4.3 5.5 4.4 3.9 4.8 4.5 4.6 4.3 4.3 3.2 4.3 5.1 3.5 5.3 5.2 4.6
##  [325] 5.2 6.5 5.0 5.4 3.6 3.5 4.1 2.3 5.2 4.7 4.1 3.2 4.2 3.8 5.1 4.7 5.2 4.3
##  [343] 5.0 5.4 4.7 3.6 5.2 5.6 5.4 4.7 5.2 5.4 3.4 5.7 4.0 4.1 4.8 5.8 4.4 5.0
##  [361] 3.1 2.5 4.6 5.8 4.5 3.8 5.5 4.6 4.2 4.2 3.7 5.3 5.2 4.3 4.7 4.5 4.9 4.5
##  [379] 5.0 3.8 6.2 6.0 4.5 4.0 4.8 2.6 4.2 2.6 3.4 4.4 6.1 6.0 4.5 4.6 5.0 3.6
##  [397] 4.8 4.0 4.3 5.7 4.8 5.4 5.4 5.8 4.4 3.8 5.0 2.4 4.3 4.8 5.4 4.8 4.3 4.0
##  [415] 3.7 5.1 3.7 4.7 4.0 4.2 5.7 5.1 3.4 4.6 3.4 4.0 5.4 5.0 2.9 4.8 5.1 3.9
##  [433] 4.4 4.9 5.3 3.2 6.0 4.7 4.5 2.3 3.7 4.5 3.0 5.2 4.2 4.9 6.0 3.3 4.2 4.1
##  [451] 4.5 4.3 4.5 4.7 4.5 5.5 3.7 4.0 5.4 3.7 3.6 6.6 3.7 5.7 4.7 4.2 4.3 3.0
##  [469] 4.6 4.9 4.6 4.2 4.2 4.1 5.6 3.7 3.7 5.3 4.8 4.0 4.9 4.7 4.2 5.5 3.7 5.9
##  [487] 6.2 5.6 5.2 3.1 4.2 4.9 4.3 4.4 4.6 4.2 4.4 5.1 4.9 4.6 4.2 3.4 4.8 4.5
##  [505] 5.2 3.9 6.0 5.3 4.4 2.8 5.6 3.9 4.4 3.3 4.1 5.4 5.9 5.5 3.9 6.2 5.4 2.5
##  [523] 3.8 4.2 6.5 3.4 4.2 5.2 3.8 4.9 3.6 3.2 3.7 6.0 4.8 4.7 4.5 5.8 4.4 4.0
##  [541] 3.9 3.1 3.0 6.2 3.8 5.9 4.4 4.6 3.8 5.0 4.2 2.0 6.4 3.7 4.0 5.8 6.1 4.6
##  [559] 4.6 3.9 3.0 2.6 6.4 5.6 5.1 4.6 3.5 5.8 5.1 5.5 3.6 3.4 3.1 2.8 6.1 5.1
##  [577] 4.7 6.3 5.0 5.9 3.7 6.6 4.5 4.5 3.9 3.8 3.9 4.7 5.0 4.9 4.9 5.1 4.6 3.8
##  [595] 3.8 3.9 5.3 4.3 3.9 4.7 3.7 4.6 3.9 4.6 1.9 4.0 5.0 5.3 4.4 5.0 4.4 6.6
##  [613] 5.9 3.3 4.4 3.3 6.5 5.1 4.2 2.6 3.5 3.1 5.8 4.7 4.5 4.7 3.7 4.6 5.7 5.0
##  [631] 3.6 4.1 5.2 5.1 5.0 5.4 4.5 4.0 4.6 4.4 5.8 3.5 2.9 5.5 4.9 5.0 4.3 4.6
##  [649] 5.6 3.2 4.4 3.9 4.5 4.0 6.1 4.2 3.3 2.7 6.1 5.9 3.2 3.6 4.7 5.4 3.3 4.4
##  [667] 5.5 5.1 4.6 4.5 4.2 5.0 5.2 5.3 4.8 5.1 3.9 5.8 3.6 4.6 6.2 5.1 4.9 5.6
##  [685] 5.4 4.3 4.6 5.1 3.0 4.8 4.9 4.5 4.5 6.1 5.4 2.1 3.7 4.1 3.7 5.8 3.7 4.4
##  [703] 4.8 3.8 4.5 5.1 4.3 5.4 4.2 3.6 3.7 4.7 3.2 3.1 5.6 4.1 4.5 5.7 4.4 5.0
##  [721] 2.0 3.8 4.3 6.1 3.6 3.4 4.2 5.9 2.7 5.7 3.9 3.3 5.6 4.4 3.7 5.4 5.6 5.2
##  [739] 6.4 5.7 5.4 3.7 4.9 5.7 4.0 4.1 4.5 3.7 4.2 4.6 5.0 5.6 4.4 4.8 4.5 5.2
##  [757] 5.0 4.7 3.9 5.2 3.6 4.7 4.4 4.1 5.2 3.9 3.9 5.5 4.2 5.3 2.8 4.5 3.9 4.3
##  [775] 4.2 3.0 3.6 5.7 3.7 4.6 4.2 3.0 5.0 4.3 5.7 5.4 4.6 5.6 4.0 4.0 3.5 4.5
##  [793] 3.9 4.0 3.1 4.4 3.5 4.5 5.4 1.9 5.5 3.3 5.1 4.8 4.7 4.2 5.3 5.6 4.1 4.6
##  [811] 3.9 6.2 5.0 4.1 4.7 4.3 5.8 4.6 5.9 3.7 4.0 4.3 4.8 5.5 4.5 5.3 3.9 3.1
##  [829] 4.3 4.7 5.1 5.9 3.4 3.8 3.4 3.7 5.1 2.5 4.9 4.1 2.0 4.5 3.5 5.7 3.8 3.8
##  [847] 5.0 4.7 4.4 4.1 5.1 5.0 6.1 4.7 6.6 5.6 3.8 4.5 4.0 2.6 5.4 4.6 2.9 4.2
##  [865] 5.5 5.3 3.6 5.1 5.3 5.6 4.2 4.5 5.5 5.4 4.8 4.3 5.1 5.3 4.9 3.0 4.6 5.1
##  [883] 4.3 5.7 4.8 4.6 4.7 5.9 4.2 4.5 4.0 4.7 3.3 5.3 5.2 4.4 5.9 4.0 4.4 4.5
##  [901] 4.3 4.4 4.1 4.1 4.2 4.3 2.5 5.3 4.2 4.1 6.3 3.4 5.5 4.9 4.4 3.4 4.8 5.7
##  [919] 4.1 4.4 4.9 3.5 4.6 5.7 4.2 5.9 4.3 5.7 4.6 4.2 5.5 3.9 3.5 4.6 4.6 4.2
##  [937] 3.2 6.0 5.2 5.1 4.2 4.0 5.1 3.8 4.2 3.9 3.6 4.1 5.8 4.7 4.8 5.3 4.9 4.4
##  [955] 4.8 5.4 4.5 3.1 4.5 3.8 3.6 4.6 3.7 4.9 4.8 5.0 3.8 4.0 5.2 4.4 4.0 3.6
##  [973] 4.4 4.1 5.1 5.0 4.5 4.3 4.8 4.6 4.4 5.1 3.7 3.7 4.5 6.2 6.1 4.6 4.7 4.3
##  [991] 3.8 3.9 3.6 3.2 4.4 3.9 3.1 3.8 4.0 3.5
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
##    [1] 5.5 3.8 4.2 5.8 4.8 4.3 3.3 6.5 5.3 4.1 5.6 3.7 6.5 4.2 5.5 6.4 4.1 5.0
##   [19] 4.7 3.3 5.8 3.6 4.2 2.9 4.1 6.5 4.2 2.9 3.5 4.3 3.2 4.2 4.0 6.6 3.2 5.0
##   [37] 4.5 5.1 6.3 5.1 5.1 4.6 5.1 5.7 5.0 1.9 5.7 4.2 2.4 5.8 3.2 4.3 6.1 1.9
##   [55] 5.5 3.8 5.4 4.7 5.3 5.5 6.4 4.4 3.8 5.8 5.5 4.6 4.8 4.5 4.3 5.0 3.7 5.1
##   [73] 4.2 3.1 5.8 5.8 5.0 4.9 4.3 4.6 3.9 3.6 4.4 2.8 3.6 2.1 4.5 5.0 3.5 5.5
##   [91] 4.9 5.6 4.2 6.0 3.1 4.5 5.0 4.2 3.4 5.6 7.4 5.8 3.5 4.1 5.1 3.5 5.1 4.7
##  [109] 4.2 6.2 3.5 4.7 4.3 4.8 4.0 5.0 3.1 3.0 4.6 3.7 4.3 5.9 4.2 6.3 5.2 2.7
##  [127] 4.5 5.0 3.4 4.4 3.3 3.7 1.9 3.3 4.4 4.4 5.1 4.7 2.8 5.7 3.8 3.7 4.3 4.6
##  [145] 5.0 3.8 3.4 3.9 6.5 5.2 4.6 3.4 4.9 5.1 4.9 3.8 4.8 3.2 4.8 4.1 5.6 3.2
##  [163] 4.8 4.1 4.8 5.1 5.1 5.7 3.4 3.5 4.3 5.4 4.6 3.2 4.1 4.8 5.7 3.3 3.5 4.2
##  [181] 3.7 5.8 3.4 5.5 4.5 7.0 3.7 3.2 4.8 2.6 4.1 4.1 4.2 5.3 5.0 4.8 4.3 4.3
##  [199] 5.3 3.0 4.5 4.0 3.4 4.6 3.4 5.0 3.0 3.2 4.6 3.9 6.4 4.4 4.7 3.1 6.7 5.8
##  [217] 4.0 5.4 3.6 4.0 5.7 4.1 4.4 5.2 3.8 5.7 4.8 4.0 3.6 4.5 4.3 4.8 5.8 4.7
##  [235] 3.2 4.0 2.8 5.6 3.4 4.7 7.4 6.8 5.8 4.3 4.7 4.5 4.6 5.7 5.8 4.6 4.7 5.6
##  [253] 5.2 4.9 4.9 3.7 4.6 4.0 3.7 5.5 3.4 4.4 4.6 5.9 4.2 3.4 2.8 3.3 4.1 5.8
##  [271] 5.7 5.1 4.6 5.7 3.4 5.7 4.6 4.6 3.5 5.0 3.9 4.6 5.8 4.7 4.2 4.3 5.6 5.9
##  [289] 5.1 3.6 3.3 3.7 3.4 5.6 4.8 3.3 4.7 3.3 4.4 3.3 5.5 4.6 4.9 3.8 2.3 4.6
##  [307] 5.1 2.8 3.8 6.1 4.6 3.7 5.8 3.7 4.3 4.3 5.1 5.9 4.8 6.4 4.3 4.8 4.0 3.4
##  [325] 4.5 5.0 3.6 5.4 4.2 2.8 4.4 4.2 5.4 3.7 3.8 5.4 5.1 6.7 4.3 4.9 5.4 4.5
##  [343] 5.5 4.8 3.3 2.8 6.0 3.6 5.8 4.1 3.6 5.1 4.9 5.7 4.0 4.3 2.9 4.7 4.8 4.8
##  [361] 5.1 5.0 5.1 4.7 3.2 6.3 4.5 3.6 6.2 3.4 3.2 5.0 3.4 3.4 3.1 5.1 2.6 5.5
##  [379] 4.5 5.5 6.4 6.0 4.2 3.8 3.7 4.4 2.8 3.6 3.4 5.1 4.7 4.3 3.8 4.8 4.2 4.4
##  [397] 4.5 4.1 4.9 4.8 5.0 5.1 3.5 5.5 6.7 4.5 5.1 3.6 4.2 3.5 5.9 4.1 4.2 4.5
##  [415] 4.3 4.2 2.6 4.1 5.4 5.8 4.2 5.1 5.3 5.6 5.0 4.5 3.7 5.1 5.0 5.0 4.4 4.1
##  [433] 3.0 5.4 5.0 4.6 4.3 5.3 4.2 5.8 3.5 3.3 5.0 4.4 3.5 4.7 5.7 4.8 4.8 3.9
##  [451] 4.9 4.2 4.3 4.0 3.4 3.7 3.8 4.1 5.5 5.1 4.2 5.4 3.6 4.7 3.5 3.6 5.4 5.1
##  [469] 2.7 3.4 4.3 4.3 4.9 4.9 5.6 5.1 5.4 5.0 4.6 6.0 4.0 5.1 5.9 2.8 6.4 4.5
##  [487] 5.8 4.9 4.5 5.7 3.9 4.5 4.4 4.2 3.6 5.5 4.9 3.3 5.8 3.5 5.6 2.7 4.0 3.6
##  [505] 5.2 4.3 3.4 5.1 4.8 3.1 4.0 4.7 3.7 5.4 4.9 5.0 4.6 4.7 4.1 5.2 5.4 5.6
##  [523] 5.6 5.1 4.6 5.0 4.7 5.6 4.5 2.7 4.6 4.7 4.8 4.6 4.2 5.2 3.3 3.9 5.0 2.9
##  [541] 3.8 4.2 5.0 5.3 5.5 4.6 5.2 3.1 5.4 5.2 4.6 3.7 4.8 4.9 5.4 5.0 5.1 4.3
##  [559] 5.5 2.2 3.9 5.9 6.0 3.1 3.4 6.2 5.4 6.7 3.8 3.1 4.5 6.2 5.3 5.1 3.6 4.0
##  [577] 5.8 7.0 4.5 4.8 4.7 3.2 3.0 4.5 3.5 3.7 3.8 2.6 4.0 5.2 3.4 3.5 5.4 3.5
##  [595] 3.5 6.8 5.7 4.0 3.6 5.5 5.1 4.5 3.1 5.2 4.9 4.9 4.2 5.3 4.9 3.7 5.2 4.8
##  [613] 4.7 4.2 5.0 2.6 4.0 5.7 6.5 4.6 4.4 5.4 4.9 4.2 3.4 5.8 4.4 5.3 5.2 6.3
##  [631] 3.9 3.7 3.7 4.4 5.7 5.2 5.6 4.3 6.0 3.7 5.3 4.3 5.8 3.8 5.0 5.2 5.7 5.1
##  [649] 6.3 3.7 4.6 4.8 4.7 4.4 5.8 5.6 4.9 5.1 4.5 6.8 4.9 5.3 5.3 5.6 5.0 5.9
##  [667] 4.9 3.9 5.0 5.0 5.8 5.4 3.6 5.0 4.7 2.7 5.0 5.4 3.2 5.7 5.4 3.6 5.5 5.2
##  [685] 7.2 3.8 3.9 4.1 5.2 5.9 3.2 4.7 4.2 3.8 4.9 5.8 4.1 4.5 3.9 5.8 3.0 2.5
##  [703] 4.2 4.2 5.8 3.2 4.9 4.8 3.6 3.3 3.2 4.8 5.1 5.2 3.3 3.7 3.9 3.9 3.9 5.2
##  [721] 3.3 4.0 3.3 4.1 4.3 5.2 3.9 3.6 5.0 5.6 4.5 4.1 4.4 4.4 4.2 3.6 4.8 6.1
##  [739] 5.7 3.9 4.5 3.9 4.4 4.9 5.6 5.3 4.6 5.5 3.7 4.3 5.6 3.4 3.7 5.1 4.5 3.6
##  [757] 5.6 4.6 4.5 3.6 4.4 4.1 5.3 6.0 4.4 4.0 5.1 4.1 4.2 6.0 3.5 4.8 4.2 5.0
##  [775] 3.3 4.5 4.2 4.8 5.0 7.0 3.0 4.4 3.4 3.8 5.3 3.3 6.4 3.9 6.2 4.6 3.7 3.3
##  [793] 2.7 4.9 5.3 6.0 5.3 4.5 3.5 4.9 3.3 4.3 4.8 5.3 4.6 5.0 3.5 3.1 5.2 5.1
##  [811] 4.6 3.8 4.0 3.1 5.1 3.7 4.7 4.9 4.5 6.2 6.4 3.3 3.6 5.1 4.9 3.3 3.0 4.4
##  [829] 2.6 5.1 5.1 4.7 3.5 4.7 2.6 6.2 2.8 3.4 4.7 5.1 5.4 4.2 4.3 4.8 5.2 4.0
##  [847] 5.4 5.9 3.8 3.0 4.3 3.1 6.2 4.5 5.2 5.8 3.4 4.5 5.1 3.3 4.5 4.0 3.7 3.6
##  [865] 4.7 4.8 3.0 3.8 4.3 4.1 3.9 3.7 4.2 4.3 6.6 6.0 4.3 5.4 3.8 3.6 5.1 4.3
##  [883] 3.9 3.8 4.5 6.1 4.9 5.7 4.8 2.9 4.5 2.5 3.5 5.9 4.5 3.8 2.6 5.5 3.4 6.0
##  [901] 5.4 4.3 5.0 3.5 3.0 4.3 3.7 4.0 4.4 2.9 3.4 4.9 3.2 4.4 3.9 3.7 4.3 4.7
##  [919] 4.1 4.9 4.5 4.7 4.3 5.1 3.7 4.8 4.1 3.1 4.9 5.1 3.6 3.8 5.5 3.8 5.1 4.8
##  [937] 4.5 4.3 4.3 4.2 5.1 2.3 4.5 5.6 6.2 2.5 4.0 3.9 3.4 4.1 4.0 5.2 4.2 6.1
##  [955] 5.1 3.9 4.3 5.0 4.9 4.5 5.1 5.7 4.4 5.0 5.8 4.6 4.7 4.1 3.3 5.3 3.6 4.6
##  [973] 4.5 5.9 4.9 3.2 4.7 3.1 2.9 5.0 5.0 3.6 3.0 5.1 6.6 4.4 4.9 4.1 4.4 4.2
##  [991] 4.1 4.6 4.1 4.8 4.9 3.9 5.3 3.3 5.7 4.9
## 
## $func.thetastar
## [1] 0.0189
## 
## $jack.boot.val
##  [1]  0.54200542  0.41737892  0.34903047  0.25276243  0.03949580 -0.07058824
##  [7] -0.16397695 -0.30200000 -0.42408964 -0.44811594
## 
## $jack.boot.se
## [1] 1.01969
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
##    [1] 4.4 4.2 4.3 4.5 4.7 5.7 4.2 4.5 5.7 5.5 3.0 5.2 4.3 4.7 3.0 3.5 5.5 6.2
##   [19] 6.1 5.1 3.8 3.8 4.4 4.9 4.7 4.8 4.8 6.1 5.2 4.8 4.5 4.8 5.7 5.2 6.0 4.4
##   [37] 4.0 5.4 3.7 2.4 3.5 5.4 4.6 5.9 5.4 3.8 4.8 4.3 2.6 6.6 3.3 5.3 3.3 2.8
##   [55] 5.4 5.6 3.5 6.6 3.5 6.3 5.6 6.0 3.5 3.7 2.4 3.9 4.6 4.0 3.4 5.0 3.9 5.5
##   [73] 5.7 4.1 4.6 5.5 5.4 5.3 4.9 4.6 3.4 4.4 3.6 3.0 6.0 4.2 4.0 3.8 3.4 5.0
##   [91] 4.9 4.4 5.6 5.6 3.6 4.1 5.7 4.6 6.2 5.0 5.5 5.4 2.4 3.6 5.3 5.6 4.0 5.9
##  [109] 3.1 5.0 4.8 3.2 5.2 3.6 5.0 4.9 4.4 4.9 4.1 3.9 3.3 6.2 5.3 4.6 4.9 3.6
##  [127] 4.9 6.0 5.0 3.4 3.5 6.0 5.5 4.5 6.2 4.1 4.7 4.1 5.6 3.7 3.4 4.2 4.3 4.2
##  [145] 4.8 4.3 4.4 4.5 4.3 4.0 4.5 3.6 4.2 2.3 4.1 4.2 3.2 2.7 4.3 7.6 5.1 5.8
##  [163] 3.3 5.9 5.1 4.2 5.9 4.7 4.3 4.9 3.8 3.1 4.4 3.6 5.2 4.7 4.6 3.9 6.2 3.4
##  [181] 4.4 3.8 5.4 5.1 5.8 4.4 3.8 4.0 3.9 4.2 4.2 6.9 4.6 3.7 4.2 4.6 4.7 3.2
##  [199] 6.1 3.8 4.5 5.8 5.3 4.9 3.5 3.9 4.1 4.3 6.2 5.1 5.3 2.7 6.3 5.7 5.5 3.8
##  [217] 5.8 5.5 3.6 4.5 3.4 4.5 5.9 5.6 4.8 3.4 5.0 5.7 5.2 4.0 5.2 4.2 5.1 3.2
##  [235] 4.2 5.5 3.1 6.6 5.0 5.8 4.8 4.1 3.7 7.2 5.3 4.2 5.0 4.3 4.7 4.4 3.6 4.1
##  [253] 4.7 4.9 4.7 4.8 6.1 3.9 5.3 4.0 3.5 4.7 5.0 4.0 5.3 5.4 4.7 4.7 4.1 5.0
##  [271] 4.4 3.1 4.5 5.7 3.6 4.3 5.8 3.9 3.3 3.3 4.9 6.0 4.2 4.6 5.2 3.9 4.9 4.6
##  [289] 4.4 5.1 6.4 4.6 3.8 4.6 5.1 4.3 5.9 4.8 3.2 4.5 3.9 4.9 3.3 5.3 5.8 5.2
##  [307] 4.5 4.9 5.1 4.7 4.2 4.6 3.8 5.4 5.6 4.8 4.9 5.1 4.6 4.8 5.3 3.0 4.7 3.7
##  [325] 4.2 6.4 5.2 3.5 5.1 5.0 5.6 3.0 3.7 4.1 4.3 5.1 4.5 4.5 2.8 4.0 4.2 4.5
##  [343] 3.6 5.7 3.4 3.0 4.8 5.2 5.0 4.8 6.4 4.7 3.9 3.8 3.6 5.1 5.1 5.8 2.7 3.2
##  [361] 5.1 5.2 4.0 3.2 4.8 4.1 3.7 4.0 3.5 5.1 5.1 2.7 5.0 4.1 4.6 4.7 4.3 4.0
##  [379] 5.5 3.1 6.4 2.9 4.0 2.7 4.6 4.1 5.1 4.3 4.1 3.3 4.1 3.3 4.5 5.3 4.9 4.3
##  [397] 4.8 4.6 4.9 4.2 4.0 3.9 3.9 4.1 3.4 4.2 3.0 3.8 4.8 4.9 5.4 5.2 5.7 2.6
##  [415] 4.8 4.2 4.4 3.1 4.0 5.4 4.6 3.7 5.6 5.5 4.1 4.3 6.4 4.6 3.8 4.7 4.7 2.5
##  [433] 4.3 5.8 4.8 5.4 5.7 3.9 5.1 2.9 4.2 4.8 4.0 3.7 2.8 5.6 4.1 5.1 5.7 5.3
##  [451] 3.6 5.0 4.0 3.8 4.2 3.5 5.0 5.2 4.7 2.7 6.4 3.3 3.7 4.3 6.5 5.2 4.7 4.0
##  [469] 5.2 3.2 1.9 4.2 5.1 4.9 4.7 3.6 3.9 4.6 3.4 3.6 2.3 4.0 4.4 4.7 4.5 6.3
##  [487] 5.7 4.0 4.9 5.1 5.3 4.3 4.1 5.8 4.6 4.1 5.2 5.2 5.1 4.8 3.5 4.2 4.4 4.4
##  [505] 4.3 2.5 3.5 4.6 4.4 4.3 4.3 2.8 3.9 3.1 4.6 4.9 2.9 4.6 5.4 4.4 3.8 5.0
##  [523] 3.9 5.1 3.8 4.5 5.2 5.1 5.0 4.1 3.3 3.8 5.0 5.5 5.8 3.5 4.6 4.1 5.6 4.3
##  [541] 4.7 5.3 5.4 4.1 5.1 5.2 4.1 3.9 4.6 5.1 3.4 6.3 5.6 4.8 5.2 4.0 5.6 4.2
##  [559] 4.2 3.4 4.7 4.7 3.5 3.5 3.0 4.6 3.4 3.2 3.9 4.3 4.1 5.3 3.8 3.3 5.7 4.5
##  [577] 3.5 5.0 4.9 5.0 4.5 4.9 3.1 4.9 3.1 3.3 4.4 5.0 4.0 3.7 3.7 4.1 3.3 5.3
##  [595] 3.8 4.4 5.8 4.0 3.7 3.8 5.7 4.4 5.5 5.0 5.2 4.6 5.4 4.3 4.3 7.2 4.0 3.8
##  [613] 3.6 3.8 4.2 5.2 5.0 3.8 5.2 3.9 4.2 4.7 6.0 4.9 4.5 4.5 3.5 5.1 4.6 4.0
##  [631] 4.6 2.4 4.0 3.1 5.8 6.2 3.0 5.4 3.9 5.4 5.6 4.3 3.1 5.3 5.0 4.3 4.0 4.7
##  [649] 5.5 4.6 4.1 4.8 4.3 3.8 3.7 5.6 4.6 5.1 3.5 5.9 5.7 3.6 5.9 4.0 5.3 2.4
##  [667] 4.9 4.8 5.9 4.3 4.0 5.4 3.6 3.2 4.1 4.7 3.7 4.3 3.6 4.1 3.8 3.9 4.4 5.0
##  [685] 4.5 5.7 4.2 3.9 4.9 4.5 3.6 4.2 3.2 5.3 4.4 5.2 5.1 3.1 4.2 5.4 5.5 4.5
##  [703] 5.0 4.7 5.1 5.3 4.1 3.0 3.4 6.9 4.9 6.4 4.6 6.1 3.5 4.3 4.5 4.5 3.0 4.2
##  [721] 4.9 2.4 4.0 4.7 5.3 3.8 6.0 5.1 4.6 3.5 3.7 5.2 5.6 5.5 4.5 4.6 3.7 4.4
##  [739] 5.7 4.0 5.9 3.0 3.5 4.9 4.2 4.7 4.6 4.7 4.2 4.5 3.9 4.9 4.4 2.6 4.5 4.8
##  [757] 4.1 3.6 3.8 4.1 5.8 5.4 5.6 5.4 3.4 2.9 5.1 5.0 2.8 5.7 5.1 4.9 4.8 5.1
##  [775] 5.1 5.4 4.3 5.3 2.5 3.4 4.7 4.0 4.3 3.8 5.8 4.2 3.2 4.8 3.5 2.9 5.6 4.6
##  [793] 3.6 4.7 4.1 5.1 3.0 4.6 5.8 3.5 4.4 6.2 3.4 3.6 4.9 5.5 6.1 5.7 4.0 4.7
##  [811] 4.6 4.9 3.6 4.3 4.8 4.6 4.1 5.4 4.0 5.2 5.9 3.9 6.5 5.4 4.4 4.4 4.6 4.9
##  [829] 4.3 4.5 4.5 3.8 3.5 4.9 2.0 4.1 3.1 4.6 4.5 4.4 5.1 3.1 3.4 5.6 2.9 3.0
##  [847] 5.6 4.4 3.2 4.7 4.8 5.2 3.9 3.1 5.8 3.1 4.3 5.2 4.2 3.9 4.4 4.0 4.5 4.0
##  [865] 4.4 3.3 3.6 4.2 4.5 5.1 4.8 4.5 4.6 3.3 4.7 5.2 5.2 4.7 4.7 5.2 3.3 5.6
##  [883] 4.3 5.9 3.2 4.5 4.5 4.5 5.4 4.1 5.3 2.6 5.4 4.5 5.7 4.5 5.2 4.6 3.2 5.0
##  [901] 5.0 5.7 5.0 5.5 6.1 3.0 3.7 4.4 4.5 3.3 3.4 4.8 3.7 3.8 3.4 3.7 6.7 4.7
##  [919] 4.3 5.1 5.8 4.6 6.6 3.8 4.2 6.0 3.9 5.1 3.5 3.9 4.1 5.2 3.2 3.7 3.7 5.2
##  [937] 5.1 4.5 4.3 4.7 5.9 6.5 5.2 4.9 6.1 6.0 4.2 5.8 5.3 4.5 4.3 3.2 3.8 5.6
##  [955] 4.9 6.3 2.9 5.2 4.4 4.1 4.6 5.4 3.5 4.4 3.5 4.1 3.8 3.7 4.3 5.8 4.1 3.4
##  [973] 4.6 4.4 5.1 4.8 4.2 4.1 4.6 3.5 4.0 3.3 4.1 4.1 5.6 4.8 4.7 6.3 5.0 5.1
##  [991] 5.0 4.0 3.5 6.3 4.6 5.7 4.4 5.2 4.7 4.0
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.344 5.100 5.100 5.000 4.900 4.800 4.500 4.500
## 
## $jack.boot.se
## [1] 0.9932956
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
## [1] -0.4844789
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
##   4.076192   6.335331 
##  (1.753439) (2.900277)
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
## [1] -0.72234637  0.01779705  0.23785588  1.32871267 -0.04783183  0.65670869
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
##    [1] -0.803832707  0.215032086  0.193028686 -1.162523338 -1.357585376
##    [6] -0.776753778 -1.109604250 -0.454567521 -0.095833132 -0.217483768
##   [11] -0.823641520 -0.708079781 -0.500752702 -0.396001087 -0.463070651
##   [16]  0.139715911 -0.161330088  0.214332175 -0.436243181 -0.789902217
##   [21] -0.514278470  0.182156154 -0.447611037 -0.377899232 -0.663993257
##   [26] -0.674915846 -1.058186134 -0.842617249 -0.373329480 -0.331072099
##   [31] -1.474001785  0.291097435 -0.682797872 -0.068708396  0.185504938
##   [36] -0.210039310 -0.167751738 -0.519767014 -0.729189367  1.446798667
##   [41] -0.420429911 -0.898036002 -0.371527998 -0.659347406 -1.521389981
##   [46]  0.054337811 -0.516709096 -2.197889651 -0.489598392 -1.058939516
##   [51]  0.277221779 -0.557255174 -0.346593016 -0.825171973 -0.560627604
##   [56] -0.802683728  0.238497943 -0.065992047 -0.203476127 -0.182743074
##   [61]  1.454609353  1.067884618 -0.408734805  0.119359532 -0.992811408
##   [66] -0.384051218 -0.467166488 -0.509618876  0.304117274 -1.191094992
##   [71] -0.894482343 -0.359983870  0.222196549 -0.430469479 -1.479780779
##   [76]  0.320434182 -0.521556500 -0.473299755 -2.251251725 -0.899960085
##   [81] -0.785084333  0.516256453 -0.433913954 -0.063836207 -0.690931930
##   [86] -1.010289825 -0.579352534 -1.753208257 -0.376595376 -0.188485296
##   [91] -0.221262155 -0.841060320 -1.390813766 -0.249415223 -0.052367453
##   [96] -0.478794578 -0.873724203  1.831675468 -0.829326669 -0.888094029
##  [101] -0.806182930 -0.534567877 -0.525511711 -1.099281871 -0.449301981
##  [106]  0.139254948 -1.209616083  0.027304761 -1.185913693 -0.665268759
##  [111] -1.589352204 -0.071006889 -0.438840595 -0.542950285 -0.699533336
##  [116] -0.409656950 -0.949040033 -0.919490215 -1.006370826 -0.837155779
##  [121]  0.116550677 -0.058812626 -0.896136829 -1.346673274 -0.039701680
##  [126] -0.802372766 -0.909897913 -0.920513226  1.382726741 -0.179517956
##  [131] -1.220448042 -0.211582411  0.026155299 -0.882669863 -0.811867994
##  [136] -0.457357767 -0.484328376 -0.987100716 -1.290460597 -0.371496060
##  [141] -0.120225364 -1.354156126 -0.560965296 -0.297469756  0.663947319
##  [146]  0.047851381 -0.105621873 -0.592284752 -0.764133257  0.221193280
##  [151] -0.510618531 -0.032087846 -0.078837659 -0.779565979 -0.574517946
##  [156] -2.221103195  0.256527907 -0.336933312 -0.518155810 -1.359898613
##  [161] -0.518091983 -0.290640019 -0.530171528 -1.481981771 -0.919270930
##  [166] -1.540930497 -0.279588564 -0.117074174 -0.353395980  0.183801603
##  [171] -0.443122454 -0.178869308 -0.953128317  0.026884924  0.225558598
##  [176] -0.571736870  0.245987783 -0.079592543  1.270547193 -0.143590384
##  [181] -0.455103799 -0.147280615 -0.659642378 -0.946405743 -0.525302461
##  [186] -0.842529228  0.408631483  0.276732245 -0.227072188 -0.701377121
##  [191]  0.189747228 -0.579572888 -1.244531817 -0.847634254 -0.141145344
##  [196]  0.663985848 -0.862582589 -0.530650753 -0.586737354 -0.109911369
##  [201] -1.371019242 -0.387794418 -0.394784186  0.005686066 -1.022139270
##  [206]  0.175618219 -0.284760559 -0.467166488 -0.524965241 -0.340841197
##  [211] -0.024595394 -0.079170509 -0.729189367 -0.570235412 -0.815732203
##  [216] -0.921221334 -0.819003185 -0.030584746 -0.453372971 -0.211584710
##  [221]  0.105461386  0.286467005 -0.832251160 -0.899277095 -0.072196848
##  [226] -0.866550040 -0.142663076 -0.232470856 -1.436375868  0.033158846
##  [231] -1.169012059 -0.436858455 -2.142638955  0.857461908 -0.929868978
##  [236]  1.006332477  0.331539142  0.586339546 -0.794004903  0.574765557
##  [241] -1.916509534 -0.097680409  0.100479757 -0.905355154  0.517211665
##  [246] -0.296602783 -0.615916218 -0.892160074 -0.590792152 -1.034647430
##  [251] -0.597075277 -1.336361071 -0.474682086  0.295206427 -1.274112388
##  [256] -0.816998591  0.142188595  0.154532219 -1.360633252 -0.916703311
##  [261] -0.107528680 -0.961611303 -0.438843820 -0.682451889 -0.169750012
##  [266] -0.725596194 -1.326802144 -0.570790986 -0.967255349 -0.429787016
##  [271] -0.084563538 -0.128877210 -1.397673802 -2.190073812 -0.447720923
##  [276] -0.437283293 -0.449360876 -0.580366789 -0.797207151 -0.798813725
##  [281] -0.995425995 -1.333579645 -0.137522174 -0.235146215 -1.132178994
##  [286] -0.319027146 -1.013052712 -0.332152789  0.191876811 -1.541696191
##  [291] -0.700406238  0.003077798 -0.174541014 -0.830445866 -0.450103682
##  [296] -0.969033306 -1.143748167 -0.399350091 -0.857481014 -0.492698862
##  [301] -0.682740942 -1.251911136 -1.040888684 -2.356499255 -0.494438204
##  [306] -0.359912413  0.754009024 -0.947464986 -0.323865098 -0.828619886
##  [311]  0.178312205 -1.331551856 -0.754150889 -0.871656874 -0.324181926
##  [316] -0.136951948 -0.864067059 -0.280311143  0.132340829 -0.645302411
##  [321] -0.691118541  0.234639794 -0.789615179 -0.650320160  0.159336956
##  [326] -0.095680002 -0.043776810 -1.387174407 -0.460782555  0.179842010
##  [331] -0.962496180 -0.585363045 -0.429647800 -0.067499453 -0.167751738
##  [336] -0.492633940 -0.607853030 -0.765104374 -0.453355955  0.058238379
##  [341]  0.422198206 -1.480093066 -0.853470615 -0.411436256 -1.526957958
##  [346] -0.413271287 -1.193763895 -0.976592717  0.188258235 -1.332160188
##  [351]  0.497148982 -1.128664505 -0.720560161 -1.380737613 -0.380406724
##  [356] -0.555702966 -0.734693701 -0.688732556 -2.345620017 -0.450203203
##  [361] -0.088588226 -0.528562853 -0.967145025 -0.617975306 -0.801468719
##  [366] -0.825611433 -0.868816290  0.317811924 -0.092647021 -0.085870223
##  [371] -1.383395944 -0.074464119 -0.926147978 -0.175742334 -0.756185434
##  [376]  0.144328953 -0.839925455 -0.928924611 -0.923937102 -1.142854306
##  [381] -2.336018524 -0.441315064 -0.775555821 -0.444401477 -0.872436263
##  [386] -0.941493535 -0.945774005  0.133793677 -0.148967607 -0.408372313
##  [391] -1.286399061 -0.430157325 -0.030970697 -0.397581980 -0.431814337
##  [396] -0.427029720  0.002319674 -0.446614624 -0.554666055  0.003363866
##  [401]  0.154492791 -1.398883656 -0.254900297 -0.429929573 -1.328706545
##  [406] -0.019769604 -0.298055561  1.613323171 -0.188628699 -1.762303028
##  [411] -0.283120514 -0.797207151 -1.099272415 -0.482337328 -0.678430983
##  [416]  0.611328490 -0.418008440 -1.047678369 -0.617882428 -1.550685964
##  [421] -1.155520776  0.243803446 -0.795440626 -0.620301030  0.207758168
##  [426] -0.534825772 -1.127589430 -0.733401235 -0.016136974 -0.050312994
##  [431] -0.465430958 -1.190104901 -0.926878471  0.273495978 -0.499355260
##  [436] -0.927561213 -0.878931811 -0.247080941 -1.460026846  0.004468727
##  [441] -0.599603376 -0.621323518 -0.987975435 -0.882928323  0.558916271
##  [446] -0.686439203 -1.537813667 -0.497964299 -0.781472387 -0.518353603
##  [451]  1.391952924 -0.354675474 -0.779368648 -0.032169912 -0.802510880
##  [456] -1.171600704 -0.873266117 -0.456924952 -1.544503789  0.080086076
##  [461] -0.701092633 -0.934351086 -0.578359329 -0.116961040 -0.077822371
##  [466] -0.908807128 -0.459565355  0.510621566 -1.854166613 -0.496989663
##  [471] -0.917602076 -0.996991414 -2.614745287  0.211319722 -2.269155454
##  [476] -0.636799201 -0.016044967 -0.153810118 -2.235528400 -0.666766440
##  [481] -0.243736263  0.596150999 -0.443585708 -1.459825880 -0.271035183
##  [486] -1.289555173 -1.382054683 -1.267834236 -0.486625541 -0.160068180
##  [491] -0.779565979 -0.628453979 -0.490384965 -0.811684781  0.464662699
##  [496]  1.434297550 -0.950012303 -0.542353635 -0.335965467 -1.249865978
##  [501] -0.881329314  0.105461386 -0.434896296  0.257874100 -0.563388167
##  [506]  0.226855077  0.297505470  0.295920640 -1.340394467 -0.882389351
##  [511] -0.150937545 -0.282407675 -1.526254648 -0.838645415 -0.200698501
##  [516] -0.061787040 -0.197913179 -0.618431852 -0.828181989 -0.323640219
##  [521] -0.819008772 -0.560101920  0.173234125 -0.233647996 -0.081504620
##  [526] -1.080186322 -2.234268817 -0.770742925 -0.487236079 -0.981536465
##  [531] -1.283939898 -0.643119152  0.211560339 -0.924023853  0.289552745
##  [536] -0.820626641 -0.709744309 -0.905355154 -0.688273665 -0.308782936
##  [541] -0.244485012  0.236220770 -1.396827515 -0.833283789 -1.083778910
##  [546] -0.683588636 -0.839498953 -1.141080604 -0.521869251 -0.553366664
##  [551] -0.342280938 -0.899285034 -1.295684856 -0.284446353  0.171903209
##  [556] -0.150299900  0.227676258 -0.950441024 -0.953982398 -0.103009544
##  [561] -0.776312005 -2.080764555 -0.543612191 -0.410172007 -0.444401477
##  [566] -0.796359160 -0.257204508 -1.363991289  0.481591288 -0.224576878
##  [571] -0.528544546 -0.295706868 -0.070353685 -0.346635494 -0.064228681
##  [576]  0.025663369 -0.332167620 -0.588063688  0.934191267 -0.091020506
##  [581] -0.901817338 -0.775215618 -0.792348507 -0.844777668 -1.571270338
##  [586]  0.313854471 -0.399627447 -0.388167683  0.216836331  0.509497484
##  [591] -0.345535083 -0.470480596 -1.405278824 -0.851101966 -0.475232183
##  [596] -0.645701384 -1.269452410 -1.493142859 -0.460713760  0.214996432
##  [601] -1.108944745 -0.428769037 -0.585356953 -0.385005601  0.202138009
##  [606] -0.422128493 -0.842815807 -0.971598042 -1.214254759 -0.469789715
##  [611] -1.902033383 -0.083508429 -0.778720233 -0.554614455  0.841728370
##  [616] -0.539202153 -0.166060058 -0.643572451 -1.328904314 -0.357776367
##  [621] -0.081688673 -2.195800328 -0.817365811  0.108751274  0.178537882
##  [626] -0.825661962 -1.327832403 -0.438882395 -0.143012742 -0.801964499
##  [631] -1.394321772 -0.520976061 -0.840196517 -0.460399930  0.195390800
##  [636] -0.471783389  0.003260457 -0.442059067 -0.810330864 -0.820089592
##  [641]  1.826597652  0.207384898 -0.485346594  0.128847177 -0.867902733
##  [646] -1.389920682 -0.972477896 -1.155242496 -0.400729853 -0.399676133
##  [651] -0.242331913 -0.221262155 -0.333249577 -0.535897638  0.946423369
##  [656] -0.446030888 -0.558828380  0.217599585 -0.385526489 -0.565664478
##  [661] -0.415947605 -1.250050375 -0.275111923 -0.107701355 -0.665386411
##  [666] -0.376831768 -0.348129576 -0.603753826 -0.109217994  0.222737670
##  [671]  0.311441100 -0.282387490  1.909404909 -1.290771249 -0.247460162
##  [676] -2.218106323 -0.897802648 -0.794885498  0.913207416 -0.755072588
##  [681] -0.613011821  0.612066683 -1.210299604  0.255479814 -0.828498692
##  [686] -0.025268714 -2.207561488 -0.186469729 -0.046359394 -0.633994253
##  [691] -1.673732356  0.149714741  0.641030197 -0.428582532 -0.700406238
##  [696] -0.439046198 -2.243714721  0.999052252 -1.387139346 -0.891016147
##  [701]  0.061133021 -0.504088665 -0.406904397  0.008218370 -0.211457371
##  [706] -0.970251815 -0.600253936 -0.879931865 -0.513921303 -0.139968900
##  [711] -0.547512414 -0.428307090 -0.042487225 -0.778941753 -0.111814800
##  [716] -0.638998848 -0.482766103 -0.529278511 -0.827279991 -0.765784409
##  [721]  0.719238954 -0.484200315 -1.073178531 -0.476295058 -1.506201891
##  [726] -0.121469653 -0.844777668  0.048157678 -0.053674970 -0.865897709
##  [731]  0.337376348  0.310187817 -0.411018678  0.070507226 -0.255884549
##  [736] -0.147663622 -0.185327710 -0.207827213 -0.461859492 -0.689238696
##  [741] -1.382044693 -1.018170699 -0.540767002 -0.812577758 -1.467691266
##  [746] -0.918517418 -0.549857226  0.682278492 -0.262322591 -1.357083266
##  [751] -0.987501114 -1.345276776 -1.397517484 -0.504706499 -0.911344645
##  [756] -0.051786991 -1.445296782  0.091672730 -0.925466242 -0.569757065
##  [761] -0.483260980 -0.465403861  0.320538450 -0.905241597 -0.775950002
##  [766] -0.908470807 -0.545533565 -0.438547855 -0.584932800 -0.883823265
##  [771] -0.647112762 -0.065039148 -1.005147019 -1.373420271 -0.234792863
##  [776] -0.802683728 -0.262231134 -1.265619462 -0.092748139 -0.027646195
##  [781] -0.089966585  0.197245488  0.106446852 -0.056714891 -0.226733841
##  [786] -1.453151404  1.403511760  0.965601461 -0.076593881 -1.192331472
##  [791] -2.055887300 -0.395901547 -0.133576152 -0.095656517 -0.109067712
##  [796] -0.262069591 -0.431853916 -0.385268090 -0.490059444 -0.223332755
##  [801]  0.295811943 -0.430276527  0.653352582 -2.270930351  0.632626125
##  [806] -0.228141219 -2.030839937 -1.015065366 -0.924023853 -0.021860344
##  [811] -0.800511560  1.206730573 -0.837090320 -1.404004297  0.172278188
##  [816] -0.397431418 -0.641031479  0.621403163 -0.074464119 -1.015933384
##  [821] -0.877193565 -0.933032892 -0.791351427 -0.473964774  0.004728137
##  [826] -0.147360995 -0.241633839 -0.772403641  0.198960787 -0.513744774
##  [831] -1.081604442 -1.177897544  0.306223038 -0.097680409  1.344454541
##  [836] -1.070300177 -0.841728233 -0.073126140 -0.075414572 -0.768803360
##  [841] -1.926524277 -0.416397394 -0.738338433 -1.004675812 -0.196144132
##  [846] -0.527461014 -0.909897913 -0.950803520  0.089642923 -0.625736219
##  [851] -0.061666363 -1.034798374  0.311704839 -0.619750953 -1.574055717
##  [856] -0.345137609 -0.452554509 -0.817781024 -0.446217902 -0.451150108
##  [861]  0.159679314 -0.455945164 -0.363667073 -0.370636394 -0.060473220
##  [866] -0.045617766 -0.411018678 -0.969155811  0.659483885  0.102055341
##  [871] -0.417217873 -0.437769406 -0.091578166  0.168285647 -1.284087082
##  [876] -0.893295430  0.111058409 -1.311016702 -0.124272417 -1.019078132
##  [881] -0.083018950 -0.901892367 -2.077183587 -1.315052319 -0.160823732
##  [886] -0.468217345  0.264554622 -0.993990825 -0.457321406 -1.921138108
##  [891] -0.556412994 -0.574654753 -0.882714567 -0.018217157 -0.318113529
##  [896]  0.044647081 -0.544388135  2.362194531 -1.475407550 -2.214007250
##  [901] -1.961920438 -0.114598281 -0.475343641  0.297501287 -0.460782555
##  [906] -1.350630191 -0.602111453 -0.939415992 -0.801287695 -0.773975694
##  [911] -0.630131218 -2.348156462 -0.166099969 -0.366177310 -0.902297714
##  [916] -2.321746029 -0.352528932 -0.489284829 -0.233988666 -0.228035193
##  [921] -0.767432117 -0.267361861 -1.380282013 -0.275166485 -1.016591621
##  [926] -1.357425568 -0.168485957 -0.429185603 -0.924482140 -0.504923870
##  [931] -0.629621637 -0.252305439 -0.121747355 -1.264500460 -0.467967358
##  [936] -1.024993498 -0.928624178 -1.444411524  0.014002784 -0.381815515
##  [941] -0.878119584 -1.071819347 -0.603711686  0.500663339 -1.032666523
##  [946] -0.499573346  0.257323243  0.064994089 -0.162966622 -0.429509569
##  [951] -0.165056009 -1.483438513 -1.076719728 -0.691740037 -0.323823009
##  [956] -0.549513072 -0.506129646 -0.447387700 -0.362226768 -0.441811053
##  [961] -0.828433051 -0.883241650 -0.345137609 -0.844064492 -0.765580606
##  [966] -0.079170509 -2.347016295 -0.394630520 -0.794337587 -1.491979586
##  [971] -1.400645684 -1.059112199  0.037468469 -0.247533677  0.340900773
##  [976] -0.195781000  0.192747171 -0.251556690 -0.014567457 -1.697225192
##  [981] -0.333041034 -0.446364405 -0.974822335 -0.302163658 -0.889106340
##  [986] -1.283861610 -1.683824486 -0.794818086 -0.831452425 -0.144044009
##  [991] -0.827279991 -0.536804200 -1.223926707 -1.316267582  0.225793359
##  [996] -0.875042884 -0.540091540 -0.034230322 -2.171062525  0.268863319
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
##   0.64340675   0.26710259 
##  (0.08446525) (0.05972140)
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
## [1]  0.22915148 -0.64595493  0.09334679  1.14195770 -0.70773621 -0.19463597
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
## [1] 0.0463
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8891552
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
## t1*      4.5 0.01171171   0.9022867
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 5 6 7 8 
## 2 3 1 2 1 1
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
## [1] -0.0334
```

```r
se.boot
```

```
## [1] 0.8754032
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

