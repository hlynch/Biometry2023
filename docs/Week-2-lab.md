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
## 0 3 6 7 8 9 
## 2 1 2 1 3 1
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
## [1] 0.0156
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
## [1] 2.786535
```

```r
UL.boot
```

```
## [1] 6.244665
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
##    [1] 4.4 3.6 4.3 4.5 3.2 5.5 4.9 5.1 5.4 4.5 6.3 4.2 4.1 5.7 4.9 4.5 3.1 3.7
##   [19] 3.9 3.8 3.7 3.6 3.1 5.4 4.2 4.4 5.3 4.8 3.4 4.4 5.1 5.6 4.7 4.9 4.3 5.7
##   [37] 4.5 6.1 3.8 2.6 4.8 5.4 4.6 6.4 4.5 4.5 3.4 4.4 5.5 3.2 5.9 4.6 3.9 3.0
##   [55] 4.3 4.4 3.9 6.2 4.8 5.1 5.0 4.1 6.4 6.4 3.1 3.2 4.8 5.5 5.6 3.6 4.8 5.6
##   [73] 3.2 4.7 6.3 4.8 5.1 4.9 5.0 4.1 4.7 4.6 2.8 5.8 3.9 5.0 4.8 4.7 5.7 3.8
##   [91] 3.5 3.6 3.0 5.1 4.8 5.0 5.7 3.4 6.8 4.1 3.3 4.6 6.8 3.7 4.8 5.0 4.1 5.9
##  [109] 4.3 4.0 5.2 4.2 4.5 4.9 4.1 3.8 4.8 2.3 5.2 5.0 3.9 5.5 4.6 4.5 4.8 4.3
##  [127] 5.3 4.1 3.3 4.7 5.9 5.7 4.6 4.4 3.7 4.5 5.3 5.1 3.9 4.6 5.6 5.1 3.5 5.2
##  [145] 4.7 3.9 2.3 3.5 5.5 3.5 6.9 4.5 1.6 4.8 3.1 5.0 3.4 4.9 5.3 6.0 3.6 3.7
##  [163] 5.0 3.8 4.8 5.3 3.5 5.1 5.4 4.5 4.6 4.6 4.0 3.3 4.3 6.0 4.0 5.4 6.1 4.0
##  [181] 5.8 3.6 4.3 6.6 4.1 3.0 3.3 5.4 3.9 5.2 5.6 5.9 5.1 3.9 4.4 4.2 4.1 4.2
##  [199] 4.5 7.0 6.2 4.4 4.3 5.6 5.7 4.3 5.6 3.0 4.5 4.3 3.9 5.0 5.1 3.3 3.6 5.0
##  [217] 3.8 4.4 2.9 4.4 3.2 5.0 4.7 4.9 4.7 5.0 6.7 4.2 4.6 4.1 4.1 5.2 5.1 4.2
##  [235] 5.2 4.9 6.0 4.8 4.2 3.4 5.6 3.5 3.1 4.5 5.6 4.3 4.8 5.4 4.4 5.1 3.8 4.8
##  [253] 4.2 4.7 3.1 4.3 4.5 5.8 2.9 3.8 3.4 5.2 3.9 3.7 5.5 5.6 3.9 4.4 5.0 2.9
##  [271] 3.6 3.3 4.8 3.6 3.5 6.5 4.6 4.2 3.4 4.9 3.5 4.2 5.4 6.2 4.9 5.9 4.8 5.9
##  [289] 5.2 6.1 3.6 2.2 5.0 4.4 4.9 5.0 4.9 4.8 5.5 4.5 5.4 4.1 5.1 4.9 4.9 3.7
##  [307] 5.5 3.0 3.9 4.4 3.8 5.9 2.6 4.4 6.0 4.2 4.1 5.1 5.1 5.0 6.5 3.8 5.4 3.1
##  [325] 4.6 3.9 4.2 4.2 4.7 6.4 4.1 3.9 4.3 5.2 5.7 5.7 4.8 4.8 4.7 4.1 4.3 4.5
##  [343] 4.3 5.3 4.2 4.5 5.2 5.3 4.5 4.4 5.6 4.9 4.0 5.6 3.1 3.9 3.9 4.7 3.5 3.2
##  [361] 3.7 5.3 5.1 5.3 4.4 4.9 3.9 3.8 3.6 3.7 6.0 2.5 4.5 3.4 3.2 6.8 4.4 4.0
##  [379] 4.1 4.5 3.8 3.9 3.3 5.4 3.7 4.3 5.2 4.0 4.5 5.0 3.1 5.1 5.1 3.2 4.5 4.0
##  [397] 4.0 4.7 5.3 5.2 4.0 5.3 2.8 4.7 3.8 5.6 4.1 5.2 3.0 5.0 4.3 6.8 4.3 2.0
##  [415] 6.2 4.4 5.6 4.5 4.4 5.2 4.7 4.6 4.5 4.3 5.0 4.3 5.4 4.7 4.9 4.0 3.4 4.0
##  [433] 5.3 5.1 5.0 5.5 4.8 3.4 4.0 4.7 5.3 5.6 6.1 6.7 4.4 5.0 5.4 5.5 3.9 3.8
##  [451] 4.8 5.5 3.9 3.9 4.8 6.2 3.9 6.8 5.2 6.9 5.1 4.7 4.8 3.7 4.5 4.4 3.3 6.9
##  [469] 4.6 4.2 6.0 4.7 2.6 3.7 4.4 3.7 4.6 4.4 5.5 4.9 3.2 3.3 4.9 4.9 3.3 5.3
##  [487] 4.2 4.8 5.3 6.0 4.5 3.1 4.0 4.3 2.8 4.5 4.3 4.1 4.9 3.7 4.2 4.2 4.8 4.3
##  [505] 3.5 3.5 4.2 6.2 4.2 3.7 5.1 4.6 5.4 3.1 3.7 3.6 4.0 4.3 5.4 5.3 5.6 6.3
##  [523] 5.0 5.5 4.9 3.7 4.8 3.9 4.0 3.0 3.8 4.5 5.2 4.9 4.0 2.9 5.6 5.3 5.2 6.0
##  [541] 4.9 3.5 4.1 4.6 5.2 5.3 3.3 2.5 4.7 6.4 4.8 2.8 4.2 4.2 3.9 3.9 4.1 3.5
##  [559] 2.9 4.8 4.7 2.7 5.6 3.1 4.0 3.2 4.5 4.4 4.3 5.3 4.3 4.8 4.7 3.9 3.9 3.6
##  [577] 4.9 4.2 4.0 5.7 4.3 6.1 4.6 5.0 5.6 5.1 4.9 4.5 5.2 4.4 2.6 4.4 4.5 4.1
##  [595] 4.0 5.2 3.9 4.7 5.3 6.0 4.6 2.7 5.2 4.3 3.3 5.2 3.2 5.7 3.3 4.5 6.2 4.0
##  [613] 5.2 6.0 4.5 4.0 4.4 3.1 5.3 3.6 4.5 5.9 4.5 4.3 4.9 5.4 6.0 4.6 5.6 4.3
##  [631] 3.3 5.3 4.3 5.9 5.6 5.3 4.2 4.0 4.8 5.0 4.9 4.9 4.9 4.2 5.2 3.1 4.4 6.2
##  [649] 3.0 3.3 3.9 4.7 3.9 6.8 4.0 5.0 5.2 4.3 5.1 5.7 3.3 5.0 5.6 2.7 4.6 3.3
##  [667] 3.5 5.4 4.9 6.1 4.5 4.9 5.1 4.0 4.0 4.7 4.9 4.8 3.9 5.6 2.6 3.1 3.5 4.2
##  [685] 4.2 4.4 4.5 4.3 3.8 3.4 4.4 4.4 3.1 4.9 3.7 2.5 6.3 3.2 6.4 5.0 3.6 3.9
##  [703] 3.7 5.3 4.1 4.2 3.7 4.0 4.7 4.4 3.6 4.2 5.3 4.6 5.4 5.2 4.7 6.5 2.4 3.7
##  [721] 4.7 3.8 4.4 4.8 3.9 5.5 5.4 5.1 5.7 3.7 6.6 3.2 3.7 4.6 3.5 4.7 6.2 5.3
##  [739] 4.3 4.5 3.9 3.9 5.0 3.4 5.0 3.5 3.8 5.1 5.9 5.3 4.1 2.8 5.5 5.1 4.2 4.5
##  [757] 3.1 4.5 5.4 2.9 3.9 4.1 4.1 3.3 4.1 5.0 3.8 4.9 4.2 5.0 4.0 3.8 6.3 5.4
##  [775] 4.3 6.2 6.4 5.2 5.3 5.4 5.3 2.0 6.2 3.8 3.6 4.2 2.9 4.4 3.3 5.8 4.1 4.5
##  [793] 5.3 4.2 4.4 5.2 3.9 4.2 4.3 6.4 5.3 3.6 3.5 4.4 4.9 4.0 3.1 4.6 4.6 4.5
##  [811] 6.9 6.2 5.8 5.5 4.9 5.7 4.5 6.6 3.1 4.9 4.6 5.6 4.0 4.4 3.1 4.1 2.9 3.8
##  [829] 5.3 4.3 3.1 3.5 3.5 4.5 5.1 4.7 4.5 4.4 4.2 5.2 4.3 4.4 4.2 5.9 4.4 6.1
##  [847] 5.6 3.4 3.6 5.3 4.7 5.7 3.9 3.8 5.9 4.6 4.7 3.6 4.8 3.1 4.3 5.4 6.5 5.2
##  [865] 3.3 4.9 3.8 3.3 4.4 2.3 4.9 4.6 3.2 5.8 3.6 4.4 4.6 3.9 2.8 4.2 4.9 1.9
##  [883] 5.3 4.5 4.3 2.2 2.5 5.8 5.6 3.5 3.9 4.5 6.2 1.3 3.8 4.9 4.6 4.5 4.2 3.0
##  [901] 4.8 2.3 3.2 3.9 4.8 3.5 3.9 4.2 3.0 5.6 4.9 4.5 4.8 3.7 5.2 5.8 3.6 6.8
##  [919] 4.9 4.1 3.5 4.4 5.5 4.9 3.9 4.0 4.5 4.2 5.4 4.0 3.4 5.1 5.8 5.0 4.3 6.4
##  [937] 5.6 4.4 4.1 5.1 5.8 4.6 5.2 4.3 6.6 5.2 5.4 4.7 5.1 4.6 4.8 4.5 2.7 4.4
##  [955] 4.7 5.4 3.3 4.7 4.1 5.9 3.7 3.6 4.7 5.0 4.5 3.7 5.3 4.2 5.4 4.5 3.9 6.4
##  [973] 6.3 4.7 4.0 2.8 5.7 4.0 5.1 2.5 3.4 3.9 4.3 3.8 5.4 4.1 5.5 5.1 4.8 3.4
##  [991] 4.5 4.9 5.0 5.3 5.8 5.9 4.1 3.0 6.2 2.9
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
##   2.7   6.4
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
##    [1] 4.0 4.5 3.3 5.3 3.9 5.3 3.7 4.7 3.4 4.2 4.1 5.3 3.0 4.9 4.2 3.8 5.1 6.2
##   [19] 3.8 4.1 3.9 3.9 5.7 5.4 4.2 4.3 5.5 5.8 3.9 6.7 5.0 5.3 3.5 4.5 5.3 3.2
##   [37] 3.2 5.2 4.4 3.7 4.4 2.8 4.6 4.1 4.6 4.7 3.9 3.5 4.4 3.7 2.0 4.0 4.1 2.9
##   [55] 5.2 4.7 4.5 4.4 4.6 3.1 4.0 5.4 4.7 5.3 4.8 5.4 6.0 4.5 5.1 4.4 5.8 3.6
##   [73] 4.3 5.3 3.6 5.0 5.3 4.1 5.9 5.5 3.3 3.6 5.3 3.3 4.4 4.9 4.4 4.2 5.4 4.2
##   [91] 5.5 4.1 4.8 4.7 4.3 4.8 4.8 3.6 6.1 3.2 4.8 4.8 3.4 4.5 5.2 4.2 3.5 4.2
##  [109] 4.0 5.2 4.1 4.6 4.0 3.7 4.0 4.4 4.7 3.3 4.5 5.1 4.2 3.7 3.8 6.0 3.9 4.7
##  [127] 3.5 4.1 6.6 4.2 6.0 4.2 4.5 4.7 3.3 5.3 4.6 3.8 3.8 5.3 3.3 3.2 4.7 3.8
##  [145] 5.1 3.2 3.8 5.8 5.0 4.3 5.7 3.7 5.4 3.9 4.5 5.1 5.3 3.3 4.9 4.2 4.9 3.9
##  [163] 5.3 3.6 6.1 2.8 4.7 5.5 3.0 3.0 4.0 4.8 5.5 3.1 4.2 4.2 3.9 4.7 4.8 3.2
##  [181] 6.0 5.2 4.7 5.7 3.6 4.0 2.3 6.1 4.2 4.6 6.4 6.1 5.6 4.2 5.3 5.4 5.1 3.8
##  [199] 4.2 3.8 3.6 3.4 6.0 3.4 4.1 6.1 4.9 4.7 3.7 4.1 6.3 2.9 4.4 4.6 4.4 6.5
##  [217] 4.0 4.6 5.1 2.9 5.2 5.5 4.3 4.9 4.7 5.8 3.6 4.2 4.8 4.8 4.0 4.2 5.5 4.7
##  [235] 4.2 6.3 3.6 4.8 4.6 5.0 4.3 4.3 6.6 3.7 4.1 5.4 5.7 6.2 4.7 2.4 4.7 5.3
##  [253] 3.7 4.1 5.3 5.5 4.5 5.6 3.6 6.0 4.0 4.8 2.9 4.3 5.4 3.8 5.2 4.4 3.5 4.8
##  [271] 4.4 4.6 4.8 4.5 2.9 2.4 5.4 4.7 5.6 5.3 4.3 4.1 4.6 2.6 3.6 4.4 6.0 4.6
##  [289] 4.7 5.8 4.1 6.0 4.1 5.3 2.6 3.4 2.7 3.0 3.7 2.2 5.5 4.6 4.0 5.3 3.5 5.2
##  [307] 5.3 4.1 4.0 4.5 4.1 3.4 4.0 4.4 4.0 3.8 4.4 4.9 5.1 6.0 5.2 4.2 3.5 4.4
##  [325] 5.0 5.4 3.6 3.9 3.3 5.2 4.7 5.4 3.8 5.4 3.8 3.4 4.4 6.0 3.9 3.5 4.1 4.0
##  [343] 3.1 5.2 4.3 3.1 3.7 4.5 6.0 4.4 5.3 4.0 4.8 4.5 5.0 4.2 5.2 4.5 3.7 3.1
##  [361] 3.7 5.2 4.6 6.2 5.8 4.9 6.2 4.9 4.0 5.6 4.4 4.8 4.3 4.2 3.9 3.7 4.3 4.0
##  [379] 4.3 4.0 4.7 4.8 3.1 5.6 4.7 5.3 5.0 5.4 5.0 3.7 4.0 5.3 4.6 4.4 3.2 5.6
##  [397] 3.6 2.3 4.2 4.3 4.6 3.9 4.1 3.1 4.2 5.3 5.2 3.8 3.8 3.3 4.6 4.7 3.6 5.1
##  [415] 3.6 4.9 3.9 4.6 2.9 2.7 3.7 3.8 2.5 5.1 4.5 5.9 3.8 3.7 4.4 4.9 5.2 4.0
##  [433] 4.1 4.4 4.6 5.0 3.4 4.2 4.2 5.9 5.3 5.4 4.3 3.2 5.8 4.9 5.0 3.7 3.7 4.3
##  [451] 3.1 4.5 5.4 4.0 4.5 2.3 3.8 4.7 4.4 5.9 4.4 4.8 3.7 4.8 3.2 5.2 5.3 4.6
##  [469] 5.5 4.3 5.3 4.3 4.1 4.1 5.3 3.7 2.5 6.3 4.1 3.1 2.9 3.8 5.0 4.0 3.7 4.8
##  [487] 4.3 5.1 4.0 4.4 4.2 5.5 5.6 4.1 4.1 5.1 5.6 5.7 4.0 4.4 3.6 5.3 4.7 3.5
##  [505] 4.0 5.8 4.5 6.0 4.4 4.9 3.9 3.7 3.9 2.7 4.8 3.5 3.1 4.8 5.3 5.4 5.1 5.4
##  [523] 3.5 5.1 4.3 5.5 5.3 4.3 3.6 3.7 3.6 4.4 2.9 3.7 5.6 4.6 4.6 4.8 6.6 4.5
##  [541] 3.7 3.7 5.6 3.4 3.0 3.2 5.4 5.7 4.2 3.5 4.0 4.5 4.0 5.5 3.0 4.9 6.0 4.2
##  [559] 5.8 2.8 5.7 4.2 4.2 3.0 5.4 3.5 2.9 4.1 2.9 4.6 5.5 4.6 4.4 3.0 4.2 4.4
##  [577] 3.5 4.0 5.1 2.7 4.5 5.1 3.4 3.0 3.8 6.1 4.9 5.5 3.7 5.1 5.2 4.5 4.3 4.5
##  [595] 4.0 6.7 3.1 3.3 4.8 2.9 3.3 3.1 3.4 5.1 5.4 5.4 4.4 4.8 3.0 4.7 5.1 3.9
##  [613] 5.2 3.9 3.2 5.9 4.1 5.5 4.8 4.3 3.2 5.3 4.1 4.7 3.7 4.4 4.7 4.0 4.8 3.5
##  [631] 4.5 4.0 4.1 4.8 3.4 4.4 5.6 4.6 5.8 2.9 4.8 5.1 2.7 4.0 5.2 5.8 4.1 4.5
##  [649] 4.7 3.5 4.4 3.0 3.9 4.2 5.0 4.1 5.0 4.0 4.1 5.7 3.9 2.6 2.7 3.8 3.9 4.4
##  [667] 5.4 5.0 6.0 3.4 4.3 5.4 5.1 4.5 4.5 4.2 4.2 3.6 3.7 5.3 3.4 3.6 3.6 3.0
##  [685] 6.7 2.9 4.1 4.6 4.1 5.9 4.3 5.0 4.1 4.6 4.7 3.3 4.4 4.3 4.3 2.7 3.4 3.4
##  [703] 4.4 2.5 4.8 5.1 5.7 5.2 5.2 3.8 4.7 4.0 5.6 6.3 5.4 5.5 3.0 3.7 3.9 5.0
##  [721] 5.0 4.2 3.7 3.0 4.6 3.7 2.9 4.7 5.6 4.8 5.3 4.0 3.8 5.7 5.4 4.9 5.1 3.6
##  [739] 5.7 5.1 4.4 5.9 3.5 4.5 4.2 5.3 3.8 3.8 5.9 3.5 3.8 3.7 5.4 4.1 3.5 4.9
##  [757] 4.4 3.4 4.1 5.1 3.9 4.3 3.4 4.7 2.8 4.1 4.1 4.8 5.7 4.2 4.8 4.0 3.8 4.3
##  [775] 5.1 5.7 3.5 3.9 4.5 3.4 5.0 4.7 4.7 3.9 4.1 4.9 5.0 4.2 5.6 5.1 4.6 5.3
##  [793] 3.4 4.0 5.5 5.8 5.4 4.7 4.2 4.3 3.4 4.1 3.2 3.4 5.0 4.1 5.1 6.3 6.2 4.5
##  [811] 4.5 4.3 5.0 4.7 6.0 3.5 3.6 3.4 4.5 4.5 4.5 5.0 5.1 2.8 4.2 4.3 4.2 4.0
##  [829] 3.3 3.5 5.0 4.8 5.9 4.0 3.7 4.3 3.7 3.6 3.7 4.6 4.5 4.7 4.5 3.8 4.1 5.1
##  [847] 3.5 4.4 4.0 5.2 4.5 3.8 4.1 4.3 3.6 4.2 3.5 4.0 5.1 4.2 5.7 4.6 5.7 3.6
##  [865] 5.3 4.9 6.1 4.4 5.5 6.2 4.4 5.5 4.1 5.5 4.3 4.1 4.3 4.0 3.6 3.8 5.8 3.8
##  [883] 5.2 6.4 5.0 4.9 3.8 5.2 3.9 5.9 2.0 4.8 3.6 4.9 4.5 3.7 5.1 5.2 3.3 4.7
##  [901] 4.3 4.3 4.6 4.3 4.0 6.7 6.5 4.5 3.4 4.3 5.0 5.9 3.0 4.8 3.7 3.4 3.9 4.6
##  [919] 4.8 3.6 5.5 5.1 5.8 4.9 3.6 4.4 4.4 4.9 4.1 5.8 5.5 4.2 5.3 4.7 4.8 4.3
##  [937] 5.1 3.9 5.5 5.5 4.4 5.0 5.3 3.7 4.8 6.2 5.3 5.0 5.8 5.3 4.4 4.6 3.7 4.2
##  [955] 5.6 4.7 4.4 4.7 4.0 4.9 2.4 4.4 3.2 5.5 5.8 5.7 4.6 4.8 4.3 5.2 4.6 4.9
##  [973] 6.2 3.7 4.2 3.6 3.9 3.8 5.8 7.1 5.5 4.5 5.3 4.4 5.1 6.2 6.1 4.1 4.9 3.3
##  [991] 3.2 5.0 3.4 4.7 5.0 5.1 4.7 4.9 3.6 5.1
## 
## $func.thetastar
## [1] -0.0427
## 
## $jack.boot.val
##  [1]  0.40718232  0.26627907  0.23482143  0.11181556  0.02154696 -0.04942529
##  [7] -0.20375723 -0.27427746 -0.39185185 -0.54169096
## 
## $jack.boot.se
## [1] 0.8763847
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
##    [1] 4.0 4.7 4.4 4.9 3.1 2.8 3.8 5.2 4.9 4.3 2.3 3.9 4.3 4.5 5.9 4.9 6.0 4.7
##   [19] 3.7 4.6 6.1 5.0 4.6 4.3 4.4 4.1 5.0 3.2 4.1 5.5 5.6 6.0 4.0 4.6 3.7 3.8
##   [37] 5.9 2.9 4.9 4.0 4.1 5.0 4.1 5.7 3.6 4.4 4.8 4.7 5.3 4.8 4.9 5.3 4.3 3.8
##   [55] 2.5 5.0 4.9 3.2 5.4 4.4 4.4 5.6 5.4 4.7 4.0 4.7 5.8 3.9 4.8 4.9 5.3 5.5
##   [73] 5.3 3.3 5.3 3.1 5.4 4.1 3.4 4.7 4.3 4.1 5.4 4.0 3.5 3.9 4.3 3.6 4.3 3.3
##   [91] 6.0 6.4 4.2 3.1 4.7 5.1 4.2 3.1 6.3 4.3 4.8 4.2 5.1 3.5 4.3 3.2 4.3 5.9
##  [109] 4.4 5.4 1.8 4.2 5.4 4.9 4.7 5.4 5.0 4.2 3.4 2.7 4.9 3.8 3.3 5.3 3.8 4.8
##  [127] 4.1 4.4 3.5 4.8 4.0 4.7 4.1 5.1 6.0 5.5 4.8 5.7 4.0 2.9 4.4 4.8 2.7 4.5
##  [145] 3.8 4.5 4.9 5.5 2.8 3.6 5.7 5.3 4.4 4.4 3.7 4.1 5.7 3.3 5.0 4.7 4.6 1.9
##  [163] 3.8 3.6 4.4 3.7 4.6 4.0 3.5 5.9 5.3 5.2 3.3 3.8 4.2 4.6 4.2 5.1 4.1 4.9
##  [181] 3.6 5.0 3.8 5.3 4.3 5.1 4.1 4.5 5.6 3.3 4.3 5.3 4.4 3.1 5.0 5.4 4.9 6.0
##  [199] 5.1 4.3 3.4 6.8 3.6 4.5 6.5 4.8 4.1 3.5 4.8 4.6 2.2 5.8 5.0 3.2 4.4 3.6
##  [217] 5.1 4.6 3.5 5.5 3.7 4.5 3.2 5.0 2.7 4.3 4.2 4.1 7.0 6.3 5.2 3.4 5.5 4.8
##  [235] 4.1 3.9 3.4 3.6 2.8 3.2 3.6 4.3 3.0 3.5 3.9 3.4 4.0 4.8 5.6 3.0 5.6 3.3
##  [253] 4.3 4.8 5.6 3.8 4.6 3.9 4.6 3.4 4.7 4.8 5.5 3.1 3.2 5.2 5.5 1.7 2.7 4.3
##  [271] 4.2 4.2 4.9 5.7 4.7 3.5 4.2 2.9 6.4 5.8 3.8 4.6 5.3 2.8 4.7 5.8 3.2 4.8
##  [289] 3.9 4.2 5.2 5.8 4.9 4.4 4.0 4.8 4.3 4.2 4.2 5.5 4.4 4.0 4.2 4.1 3.0 2.7
##  [307] 4.6 3.8 5.1 5.1 6.6 4.0 5.2 4.7 4.4 4.5 4.8 4.6 2.2 5.9 5.1 3.8 4.5 4.0
##  [325] 3.5 2.5 3.9 6.9 3.6 4.8 3.4 6.4 4.7 4.0 3.5 7.4 4.4 4.6 5.5 4.8 5.8 2.9
##  [343] 4.5 4.9 3.5 3.6 3.7 3.6 4.1 4.3 4.5 3.0 3.5 4.5 6.8 4.3 4.1 4.7 3.3 5.4
##  [361] 3.9 4.3 4.2 3.8 4.4 2.9 6.2 4.3 4.1 4.0 4.0 3.7 4.4 4.9 4.2 4.5 5.0 6.1
##  [379] 5.5 6.0 4.4 2.2 6.0 4.7 5.1 4.7 3.9 4.3 6.5 3.3 4.0 4.0 3.0 4.3 6.3 2.9
##  [397] 4.5 4.9 5.4 4.6 4.9 5.1 3.0 3.9 5.0 3.3 2.8 4.2 3.5 4.5 5.3 5.1 4.1 3.7
##  [415] 5.3 3.5 2.8 3.7 5.7 4.0 3.9 4.8 4.0 5.7 5.7 3.6 5.1 5.4 3.6 4.7 6.4 4.8
##  [433] 4.7 4.5 6.1 3.6 4.4 4.6 5.6 3.5 3.4 3.7 5.3 6.4 3.0 4.8 5.1 3.3 6.2 4.9
##  [451] 3.6 4.1 4.6 2.2 4.3 3.6 4.5 4.7 4.8 6.4 3.0 4.0 4.4 3.4 4.1 4.5 5.8 2.7
##  [469] 4.6 3.1 4.5 5.4 4.4 5.4 3.0 2.8 5.5 4.5 4.2 2.6 2.8 3.5 3.1 5.8 5.5 4.9
##  [487] 4.2 4.9 3.3 3.0 5.0 4.5 4.6 4.3 4.4 4.2 4.2 5.5 6.1 2.9 6.0 4.2 5.0 4.1
##  [505] 4.5 3.5 3.5 4.3 3.4 5.2 3.4 5.4 4.6 5.7 3.8 6.0 4.6 3.3 5.0 4.0 4.1 4.2
##  [523] 3.6 4.5 4.9 3.4 3.5 5.4 5.8 5.4 4.7 5.0 4.8 4.0 4.2 3.6 2.3 5.1 4.8 6.0
##  [541] 3.9 3.9 5.8 3.4 4.6 5.4 5.1 4.2 3.7 3.7 3.1 5.3 5.6 6.6 4.7 3.0 6.1 4.3
##  [559] 5.1 3.4 4.2 4.5 4.2 3.8 4.7 4.8 3.0 3.1 4.3 3.7 5.3 5.0 4.1 5.0 4.5 4.5
##  [577] 5.7 4.4 5.2 4.1 5.2 3.8 2.8 5.1 4.1 2.4 4.2 5.4 4.1 5.1 3.2 4.1 3.5 4.6
##  [595] 4.0 5.3 3.2 2.7 4.9 3.9 5.6 3.4 5.9 3.3 5.1 4.7 5.4 3.7 6.2 3.9 4.8 5.9
##  [613] 3.8 5.7 5.5 4.4 4.1 4.5 5.5 5.2 4.0 4.3 3.3 3.7 4.3 4.0 4.1 4.0 4.8 4.9
##  [631] 3.8 4.4 4.7 5.0 4.6 5.3 5.1 4.1 4.0 4.3 5.1 5.2 4.0 3.7 3.7 4.6 4.9 4.6
##  [649] 4.2 4.0 5.2 4.0 5.1 2.9 4.6 3.8 5.1 4.7 4.9 4.2 3.8 3.2 6.4 4.4 5.2 5.9
##  [667] 6.5 5.6 3.7 3.9 4.5 3.9 5.1 4.8 3.3 5.3 4.8 4.1 5.9 5.5 3.1 5.2 3.7 4.9
##  [685] 3.5 5.1 4.5 4.5 5.0 5.9 3.8 4.3 4.9 4.8 4.4 4.6 4.3 4.2 4.1 4.5 4.5 3.5
##  [703] 5.2 4.5 4.3 5.3 2.7 4.6 5.4 4.1 3.9 4.6 3.7 4.4 5.0 3.2 4.6 5.4 3.8 5.5
##  [721] 4.7 6.7 4.4 4.3 5.6 5.0 4.8 5.5 3.8 5.2 3.8 4.4 6.8 5.3 5.3 5.3 3.5 3.8
##  [739] 4.4 4.8 4.0 5.2 4.4 4.0 4.9 4.5 4.4 4.6 5.5 5.4 4.7 5.1 5.1 4.3 6.2 6.2
##  [757] 5.3 5.2 3.8 5.8 3.2 3.8 5.7 4.3 4.8 4.1 4.6 3.7 5.8 4.4 3.3 5.8 4.8 3.8
##  [775] 4.7 7.0 4.3 3.7 3.9 5.8 5.6 6.3 5.3 4.4 4.7 5.0 5.0 4.9 4.0 3.9 5.7 5.4
##  [793] 3.5 5.3 3.0 4.1 5.3 3.3 4.8 4.4 3.7 3.0 2.9 3.7 4.9 5.3 4.0 4.5 3.9 4.6
##  [811] 5.8 2.2 3.9 3.8 3.5 4.4 4.5 3.3 5.1 3.5 5.0 4.8 4.1 3.9 4.0 4.5 6.3 3.1
##  [829] 3.2 4.0 5.1 2.7 3.2 4.7 5.5 4.3 3.6 5.9 3.9 2.5 4.1 6.2 5.0 5.3 5.0 4.8
##  [847] 5.3 4.3 3.5 5.6 5.5 6.0 3.7 6.1 3.3 4.9 4.4 6.1 3.7 3.7 6.4 6.2 4.5 5.2
##  [865] 4.1 2.9 3.6 5.8 4.3 3.6 4.9 5.0 5.2 4.7 6.1 5.2 4.4 5.4 5.2 4.7 6.0 6.3
##  [883] 3.1 3.6 4.7 4.7 3.9 5.2 4.5 4.4 3.6 5.4 4.5 3.9 3.3 3.9 4.8 5.5 3.2 4.1
##  [901] 4.7 6.2 3.3 5.1 3.3 4.5 4.2 4.2 4.5 4.9 3.3 4.9 4.6 3.9 3.4 3.4 4.6 4.4
##  [919] 3.9 4.6 4.5 4.6 5.2 3.9 4.1 4.6 4.4 3.0 3.6 5.0 5.3 3.8 4.7 4.1 3.3 5.1
##  [937] 4.8 4.1 5.6 3.4 4.9 4.8 4.8 6.0 5.3 5.3 2.3 3.9 4.5 3.4 4.4 3.7 5.3 4.6
##  [955] 5.1 4.5 3.8 5.6 2.5 5.4 5.2 6.0 4.1 4.9 4.6 4.8 4.0 5.6 3.8 5.5 5.1 5.6
##  [973] 4.7 5.5 3.3 3.9 5.0 4.4 4.6 5.6 6.1 3.8 4.8 3.9 2.8 4.9 2.8 4.4 4.1 2.8
##  [991] 4.9 5.9 3.6 3.6 3.7 5.2 3.6 4.4 5.0 4.7
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.284 5.100 5.080 5.100 4.800 4.600 4.600 4.400
## 
## $jack.boot.se
## [1] 1.054365
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
## [1] 0.205196
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
##   3.983551   6.747484 
##  (1.712134) (3.090850)
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
## [1] 0.311353768 0.002740914 1.085593861 0.934903706 0.659252604 1.557068660
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
##    [1]  1.0106700801  0.5421365040  1.5802455793  0.3766333927  1.0870704510
##    [6]  0.1221728936  1.6124754935 -0.6536616876  0.1122532396  0.0502887555
##   [11] -0.4663653677  0.1114846482  0.1738888207 -0.7508636623  0.2739615333
##   [16]  0.5101429335  0.3676458027  0.7326630194  0.3166675847 -0.1569381850
##   [21]  1.0343429071  0.2509013413  1.5479796789  0.4814045065  0.0897899248
##   [26] -0.1585806168  0.8474062590  0.1083380323  0.1506628170 -0.1440590682
##   [31] -0.3767644395 -0.2153356373 -0.2377449790 -0.2281338274 -0.1840810033
##   [36] -0.1375104603  0.2589332332  0.0747197633  0.2510517480  0.5080764726
##   [41] -0.1749733475 -0.5996591996  1.1871256969 -0.6172932962 -0.2650063049
##   [46]  0.1296209955 -0.2202106580 -0.4363270535  0.3975928828  0.3875881598
##   [51] -0.1316294863  0.2909471201  0.2219299310 -0.0920432764  0.0694168712
##   [56] -0.2740127043  0.2068817539  0.0588992201  0.4262843163  0.3851313501
##   [61] -0.0731676470 -0.3958011458  0.0494091400  0.3760870208  0.1227407066
##   [66]  0.7657402820 -0.4397813169 -0.4975648581  0.4507419355 -0.3136934823
##   [71] -0.0957591616  0.2421817731  0.7439106982  0.5120512762  1.2739357631
##   [76] -0.1194158667  0.7738270281 -0.2652236944  0.9472669578 -0.4677077284
##   [81] -0.3219959597 -0.6181920380 -0.0252345045  0.7119027314 -0.0524897675
##   [86]  0.0654472816  0.6883050952 -0.5528171459  1.0111606428  0.4005724690
##   [91]  0.0226866564  0.4870891750  0.7063282853 -0.3605705325 -0.5657697769
##   [96]  0.2790573940 -0.0670393241  0.2528207671  1.0037561771  0.0280541625
##  [101]  0.2344573798 -0.9220033765 -0.5891108270  0.8294213839  0.4277536071
##  [106]  1.0473952684  0.4042223329  0.1368986045  0.3055362759  0.4973330356
##  [111]  0.2478996166  0.0048009642  0.1752974339  0.1212766657  0.5638243736
##  [116]  0.1198949648  0.8705765809  0.5959209807  0.9036873868  1.0798356807
##  [121]  0.0541652285  0.2219299310  0.1003227608  0.5229718338  0.3273043960
##  [126] -0.0398469538  0.7091547992 -0.4378957169 -0.4473308879 -0.5257872456
##  [131]  0.3526976028 -0.0953854952  0.1741978016  0.0741836833 -0.1739534321
##  [136]  1.4319750189  0.3874987873  0.5280645206 -0.2003426833  0.4405564786
##  [141] -0.2564055166  0.1645899110 -0.0736009565  0.1224601497  0.0053209898
##  [146]  0.7037614857 -0.8520754410 -0.7934254628 -0.2028795523  0.9273454678
##  [151]  0.1514298600  1.4979882545  0.2997347134  0.2072874025  0.5160809253
##  [156] -0.3411778649 -0.4369141502 -0.2246291444 -0.4482675287  0.2698209715
##  [161]  0.3072041189  1.0538946562  1.2090034644 -0.0828133694  0.2362122467
##  [166]  0.1700134840  0.5246596191  0.8108885303  0.4829069402  0.6071644707
##  [171]  0.7909654385 -0.4950769404  0.4886300279  0.6065760714 -0.1318785196
##  [176] -0.1318785196  0.2155506199 -0.6433515299  0.2608100301 -0.1343520088
##  [181] -0.1686768152  0.0937830069  1.2410327034  0.3419277886 -0.1504761040
##  [186]  0.5550504476  0.3172097849 -0.0302699258  0.1835063285  0.1422050323
##  [191]  0.5363708475 -0.6639075117 -0.3725561499  0.6124674516 -0.0624060871
##  [196]  1.3248589543 -0.2162468447  0.0967209799 -0.7638642641  1.1323725304
##  [201]  0.4758407444 -0.3721429150  1.0080216634 -0.2804034129 -0.9703023282
##  [206]  0.4458407179  0.7988769563  0.2537057465  0.3018886388 -0.0684894978
##  [211]  0.9901343961  0.4319294738  0.3594830846  0.9235214903  0.6411935791
##  [216]  1.3265910660  1.2247564833  0.2576667456 -0.3801303463 -0.0106851674
##  [221] -0.2458228922  0.2777610951 -0.3004714908  0.4735985938  0.9814981567
##  [226]  0.5075868767  0.1545746881 -0.2804331195  1.0325452343  0.9726116721
##  [231] -0.8716564270  0.9015020711  0.0486509980 -0.6206698690  0.5618257197
##  [236] -0.0311232478  0.1594429802  0.3984941592 -0.1692883070  0.7942289502
##  [241] -0.3461778866 -0.8844775118  1.0131661038  0.2573002193 -0.5859354828
##  [246] -0.8533949741  0.1694362658 -0.8988245323 -0.1690414662 -0.0238963726
##  [251]  0.0051925779 -1.5029488773  0.1450380632 -0.0725374502  0.4803268336
##  [256]  0.5569616561 -0.3946002140  0.0819083995  0.5320656048  0.1767799448
##  [261] -0.2449844382 -0.5674737702  0.7647497413 -0.0226045952 -0.0148929124
##  [266] -0.2829324262  0.4943741750 -0.0402854991  0.2565218887  0.2087629114
##  [271]  0.3210905021  0.3560079680 -1.0669243336 -0.6455799001  0.3396637676
##  [276] -0.5629980768  0.2533865665  0.4317163691 -0.2979031016  0.1958253671
##  [281] -0.6363270507  0.8455928295  0.7885160041  0.2835791312  0.4786102302
##  [286]  0.7514505053  0.1729410913 -0.4212314830 -0.0726899645  0.2540063752
##  [291] -0.1294399421 -0.3589532481 -0.0307472459  0.8175361809  0.3897183730
##  [296] -0.1743862447  0.1914678549  0.5173728220  0.5073884442 -0.8558360827
##  [301] -0.1686522499 -0.1599097286 -0.2428387908  0.4391108901  0.6446831217
##  [306] -0.7055625095 -0.1822782440 -1.6326288217  0.5817888409  0.2216683956
##  [311]  1.3293636234  0.1205348401 -1.0133066335  0.1000935296 -0.0110096979
##  [316]  0.3735093022 -0.2921952872 -0.3048217156 -0.4222324987 -0.1028662023
##  [321]  0.0767145991 -0.5024129975  1.1402346471  0.8540814388  0.3421808829
##  [326] -0.5600591451  0.1739029201  0.0173469828  0.0331655319  0.1327928333
##  [331]  0.5266366442  1.2336923735 -0.0308195227 -0.2507159155  0.3904275544
##  [336]  1.0956525360  0.1147858976  0.4803268336 -0.2784996220  1.3609293057
##  [341] -0.2126325500  0.4341724431  0.4438650607 -0.5038492571  0.1694362658
##  [346]  0.3468492205  0.0215901046  0.1032509564  0.5773750757  0.0653848060
##  [351]  0.4204682443  0.1570489892  0.4816525417  0.9566230194  0.4268708498
##  [356] -0.0491032840  0.7192645964 -0.3194627824  0.5356445162 -0.4253039537
##  [361]  0.4888369948 -1.2435629600  0.3164117944  1.0777022328  0.2765707470
##  [366]  0.0234331781  0.0842307125  0.3254346947  0.3691455621  0.5309574298
##  [371] -1.3300750042  0.4410715630 -0.2198775669 -0.1818747686 -0.1073922286
##  [376]  0.0416450996 -0.3430974699 -0.2742721823  0.2325923651  0.2669963293
##  [381]  0.1212594898 -0.2663421690 -0.0481423361  0.0916907113  0.0228319237
##  [386]  0.2974049043 -0.0069689961 -0.1875451391  1.2800250433  0.2993095522
##  [391]  0.3343379081  1.0272222938  0.7463896369  1.2800250433  1.2400832026
##  [396] -0.1027864442  0.1833331693  1.0755309578  2.2011321250  0.9377445046
##  [401]  1.1561857888  0.7651491930  1.0322917500  0.5900523495 -0.4382380977
##  [406] -0.1178031492 -0.3247721512 -0.3276687129  1.0311001597 -0.5474973122
##  [411]  0.9679843213  0.1503310525 -0.0818807320  0.7387059117 -0.0829342455
##  [416]  0.5040934421  0.4790343474 -0.5151728719  0.5056148984  0.9537555668
##  [421]  0.6526812359  0.4703579931 -0.1514545962  0.1276651238 -0.3919662385
##  [426]  0.5049708653  0.9238334185  0.5690193008  0.1727373280  0.7634506754
##  [431]  0.6288252446  0.0950866472  0.2134356148  0.4337517649 -0.3754318972
##  [436]  0.8108885303  1.2870846341  0.5094790767 -0.1883267812  1.0348943786
##  [441] -0.1546454441 -0.7339371166  0.0073553810  0.6574695801  0.3146975131
##  [446] -0.7036425039 -0.8159977587  0.5903247998  0.9082152875 -0.4222324987
##  [451] -0.2880338232  1.0015677945  0.1315648758  0.5235557545  0.7249327418
##  [456]  0.6224879061  0.1745385248  0.3581028786  1.2709766673 -0.2722786627
##  [461]  0.1134908299  0.6124674516  0.2911935060  0.7344565005 -0.0929502562
##  [466] -0.7226469623  0.6565115889 -0.2095061833 -0.1442700217 -0.3165286378
##  [471]  0.5305301369  1.2394096159 -0.0509589877 -0.6443451612  0.4951417743
##  [476] -0.1190464564  1.0327777862  0.2226188336  0.1681938743  0.9495311834
##  [481]  1.0237992489 -0.1976356412  0.5943452889 -0.1289048238  1.1988278309
##  [486] -0.0540932590  0.7654380440 -0.0202283693  0.0483009855 -0.0957043488
##  [491]  0.5013232240 -0.1764610748 -0.3321937313 -0.4943399686 -0.7113897314
##  [496] -0.3868283049  0.0011743995  0.0304665859  0.3419220566  0.5794194452
##  [501] -0.0132369161  0.6291293180  0.6377388813  0.2357045752  0.1706633388
##  [506] -0.0181708633  0.4570994271 -0.0866314895  0.2609661244  0.3306105322
##  [511] -0.1616334894 -0.2520636057 -0.0357098185  0.1166902266  0.3817140012
##  [516]  0.6239183916  0.4523688687  0.4257334767  1.3186290385 -0.4389906192
##  [521] -0.5933964070  0.9516409463  0.5607663625 -0.5214906232 -0.1836052080
##  [526]  0.4571513103 -0.1128287299 -0.1289048238  0.6520742525  0.8610905505
##  [531]  0.5738324726 -0.0708647553 -0.0575685805  0.1442660840 -0.0572227012
##  [536]  0.1451138719  0.4118015126 -0.0368996484  0.3950957798  0.3170092547
##  [541]  0.4445621878  0.4738323185  0.7805511488  0.6014228528  0.0499428228
##  [546] -0.0387112760 -0.4700377921 -0.9802982025 -0.7274646387 -0.5227237200
##  [551]  0.6125656575 -0.2870727548  0.4424234141  0.7800524912  0.3273585287
##  [556]  0.4789444269 -0.2211623442 -0.0919745419  0.6571892198 -0.0451267268
##  [561] -0.5029294902  0.1805809049  1.2217399491  0.1131697586 -0.1574497399
##  [566] -0.4332040880  1.0787583706 -0.5469471281  0.9917382805  0.5179410279
##  [571]  0.5266366442  0.9669356345  0.6018961813  0.9203572372  0.5361958407
##  [576] -0.3590148960 -0.4270512032 -0.6356267042  0.0829831572 -0.3593424453
##  [581]  0.0056104134 -0.0401129642  0.1077677006 -0.1560716990 -0.0361857852
##  [586]  0.7408900340  0.1358319845  0.6191430352  0.6910837656  0.6265297247
##  [591]  1.6928169763  0.4802985492 -0.6388526357 -0.4215318449  1.2248406838
##  [596]  0.5564942426 -0.0601798429  0.5200866537 -0.1978248519  1.3219120685
##  [601]  0.6036627386  0.5689267044  0.6882320374 -0.9286563310 -0.0375445452
##  [606] -0.0017204361  0.0507851985  0.8690073666  0.4819432659 -0.7972720304
##  [611]  0.7876361908  0.1118715328 -0.0932453101  0.0873789400  0.4998309622
##  [616]  0.8178053826  0.4404374883  0.4134265270 -0.2115254527  1.0213183652
##  [621]  0.2811456897  0.6954857608  0.1815048840 -0.4734619687 -0.7054768561
##  [626]  0.7855327051  0.8065360392  0.0111015135  0.9346827501 -0.4468339254
##  [631] -0.6512793699  1.4149578222  0.0673948373  0.4524485366  0.4778500361
##  [636]  0.5420971013  0.3026219883 -0.2553674359  0.2409828044  0.0604638738
##  [641] -0.4227037698 -0.4280152824  0.5627366119  0.6620058275  1.0303993882
##  [646] -0.3682750607  1.4219645929  0.9460942174  1.0822146761 -0.3233709237
##  [651]  0.9832130877  0.2293797314  0.8168818688  0.5669001463  0.9917382805
##  [656] -0.0262867860  0.3254508987  0.2545496090  0.2026422491 -0.8321284450
##  [661]  0.3628903973  0.3146822025 -0.0823692394 -0.2167545475 -0.2453722106
##  [666] -1.1436066826  1.3963182952 -0.6943583447  1.2217399491  0.9277896259
##  [671]  0.3633560892 -0.7906266109  0.7170109196  1.1010940890  0.5246596191
##  [676] -1.3175912117  0.8193283447 -0.1033888814  0.3837057813 -0.3070011378
##  [681] -0.4500573234  0.6270288936  0.7235291994  0.0193640128 -0.7479531339
##  [686]  0.5484255280  0.5191955623  0.0576778435  0.7165550239  0.4965830303
##  [691] -1.2504930325  0.3963732506 -0.3829746275 -0.0150969842 -0.5845243956
##  [696]  0.6110898666 -1.2185328665 -0.3308080392  0.2828836509  0.3005203570
##  [701]  0.6846460271  0.4591783415  0.7337776742  0.2361711377 -0.1478658422
##  [706]  0.1217443935  0.9691520242  0.4136335532  0.3495929442 -0.2016173484
##  [711]  0.1917123721  0.3732282561  0.1205461900 -0.5847791803 -0.5402838896
##  [716]  1.0326550472  0.2367443200  0.5809884706 -0.4293795940  0.3131351822
##  [721] -0.5009274705  0.1494963264 -0.0244943883  1.5142470745 -0.6841132594
##  [726]  0.3249461152 -0.5207804259  1.3633089888  1.2064636481  0.5547494447
##  [731] -0.2486776378  0.5964833197  0.7474592209 -0.1979359452  1.3617766134
##  [736] -0.3852079395 -0.9531088756 -0.0657987450  0.3024483269  0.5912736965
##  [741]  0.1645316738 -0.6951650186 -1.2026887817  0.9801185456  0.3191296462
##  [746]  0.2804991814 -0.1130728024  0.4786102302 -0.6863039651 -0.6326003497
##  [751] -0.1280919024 -1.2188654659  0.6327932875 -0.0396466102 -0.3416756312
##  [756]  0.1557850358 -0.0554020049  0.5647480043  0.1809963858  0.1297549989
##  [761] -0.2410725452  0.0905076587  0.1084512613  0.7744244137 -0.5877767901
##  [766]  0.1763363778 -0.2457761187  0.2633148162  0.0479117946 -0.1426794022
##  [771]  0.8613188367  0.1172377500  0.4279052903  0.3677919803 -0.3526262972
##  [776]  0.0527145979  0.1925638415  0.6730274555  0.0985839461  0.3811865494
##  [781]  0.8587455592  0.4511790720 -0.5289842960 -0.7111998070 -0.1732920532
##  [786]  1.0848827754 -0.4688868405  0.6760420734  0.8232511613  0.1171369201
##  [791]  0.3210905021 -0.1305700287  0.9753538725  1.4923283016  0.6666005612
##  [796]  0.0005115444  0.8515126213  0.2909452429 -0.1133538342  0.1935520899
##  [801]  0.5199955593 -0.0569186373 -0.0364028955  0.1425721408 -0.0286904347
##  [806]  0.4853486868  1.2546353683  0.9864554992  0.1201398757 -0.1668518142
##  [811] -1.3286528363  0.9361828974 -0.2429411003  0.4833230249  0.2256214280
##  [816]  0.8098387548  2.3057468771 -0.1951248178  0.1938669008  0.3183179528
##  [821]  0.2747448213  0.9405199050 -0.4321245613 -0.2551059778 -0.1450467772
##  [826] -0.0705693045  0.0118118944 -0.3604517172  0.7623184384 -0.0076773376
##  [831] -0.6688993138  0.4310063621 -0.0517009191  0.5276103897  0.4443608326
##  [836] -0.4769113714  0.1132929720  0.2729053865  0.1671775771  0.1333621916
##  [841]  0.0766687770 -0.0655510034  0.7447003363 -0.2301358835 -0.2520004880
##  [846] -0.5779827085 -0.2630820506  0.0873572257 -0.0712122913  0.0989969029
##  [851]  0.7179313265 -0.0375445452  0.5933466658  0.6567902221  0.9454559455
##  [856]  0.0764148935  0.4902267662  1.3413403150  0.1102851306 -0.8159977587
##  [861] -0.5095104618 -0.3440348432  0.5788803734 -0.6207535459  1.2382338721
##  [866]  0.4801760131  0.5165766547  0.8015606677  0.2795963917  0.1926060240
##  [871] -0.5010756965 -0.1509698567 -0.5469463659 -0.1926034919  0.8689141555
##  [876]  0.8340275777 -0.2893581107  0.8868279906  0.3057794289  0.1413541153
##  [881]  0.0572473131 -0.1329488816  1.1561857888 -0.0013383629  0.5107485251
##  [886] -0.2781088939 -0.1009936028  0.6797648679  0.0433521873 -0.6469924150
##  [891]  0.4758122288  0.1810816682  0.3021490200  0.5760584386 -0.1432776251
##  [896]  1.1010940890  0.5537820595  1.0286366943  0.8548264312 -0.3754368020
##  [901]  0.3111096230 -0.8037147103  0.1699334892  1.3660584920  0.4662010779
##  [906] -0.2537727224 -0.2394818092 -0.7811206137  0.8785288452 -0.1140959132
##  [911]  0.7060498762  0.2136763188 -0.5799850400  0.0115406548 -1.1262032658
##  [916]  0.5123527613  1.2798166278 -0.0380252393  0.2421489319  0.4661656291
##  [921]  0.9618721135  0.7547046289 -0.3316057623  0.7648703877 -0.1524685322
##  [926] -0.1262096049  0.3343216025 -0.1817574976 -0.3135038125  0.0347823088
##  [931]  0.0796785883 -0.8898483996 -0.1929930361  0.1722236316 -0.0447155009
##  [936]  0.9009789823  0.2399041413 -1.8922033988 -1.0783870043 -0.6105108335
##  [941]  0.1276651238  0.1287005338  0.7141024309  0.9190044385  0.2918473660
##  [946]  0.1331080881  0.4585203078  0.1640549134 -0.2523935702  1.0396286028
##  [951]  0.1387265083  0.1874745608 -0.0846607579  0.1775969170  0.0150873272
##  [956]  0.8208840519 -0.1300981623  0.6395977452 -0.4679592995 -0.0832226121
##  [961] -0.1407206647  0.7922789306 -0.3204213196  0.3753370699  0.4732120477
##  [966]  0.1972003013  0.9460942174  0.2484672646  0.2410550714 -0.1407206647
##  [971] -1.2119410298  0.2804477650  0.2215286275 -0.0364317901 -0.3830343252
##  [976]  0.3194688545  0.5056088874 -0.5347645419  0.5419884715  1.3218751461
##  [981]  0.2261806620  1.1566174800  0.6269455622  0.4503256514  0.9897968273
##  [986] -0.0615415815  0.5415779392  0.5323587206 -0.1612204349  0.2072874025
##  [991]  1.2234505828  0.1445665099  0.1902489441  0.7976406022  0.1133165086
##  [996]  0.0693758461  0.0867782846  0.3135112299  0.1174216300 -0.4202304566
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
```

```r
fit2
```

```
##       mean          sd    
##   0.59037570   0.28541654 
##  (0.09025663) (0.06381713)
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
## [1]  0.01820792  0.21510031 -0.38112664 -0.58655971  0.55656025  0.20264554
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
## [1] -0.0294
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9293543
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
## t1*      4.5 0.02582583   0.9143992
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 4 7 9 
## 3 2 1 2 1 1
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
## [1] 0.0326
```

```r
se.boot
```

```
## [1] 0.9234121
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

