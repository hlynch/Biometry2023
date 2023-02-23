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
## 4 6 7 8 
## 3 2 1 4
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
## [1] 0.0675
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
## [1] 2.817701
```

```r
UL.boot
```

```
## [1] 6.317299
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.2
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
##    [1] 5.1 5.7 4.3 4.8 5.9 4.4 3.8 4.1 5.2 4.3 4.5 3.5 5.4 4.7 6.0 4.7 3.9 4.4
##   [19] 2.9 5.0 4.1 5.0 4.0 4.8 3.1 5.1 4.3 4.6 3.5 4.2 6.4 4.9 3.8 4.6 4.2 5.6
##   [37] 3.4 4.5 5.0 4.7 6.4 4.9 3.6 5.2 1.7 3.5 4.2 5.5 5.2 5.1 5.9 4.4 4.6 4.1
##   [55] 4.0 3.5 3.7 4.7 4.3 4.7 3.9 5.2 4.7 3.3 3.7 4.4 5.9 4.0 5.2 5.0 3.1 5.6
##   [73] 5.3 5.2 2.5 4.3 3.7 3.0 3.2 4.1 4.2 4.8 5.1 4.1 5.1 4.8 5.0 4.8 3.9 2.9
##   [91] 4.1 5.0 5.6 4.6 4.9 2.5 3.7 4.0 4.4 5.2 4.4 4.6 4.5 3.6 4.0 4.2 5.3 3.6
##  [109] 5.2 5.2 4.4 5.7 3.5 4.3 5.5 3.7 5.0 4.3 4.3 4.1 4.9 5.0 5.2 5.0 5.7 4.3
##  [127] 4.1 3.6 4.1 5.9 5.0 4.3 6.3 3.9 5.2 4.6 6.0 4.2 3.4 5.1 5.5 5.2 4.5 3.7
##  [145] 3.8 4.9 5.6 5.1 5.4 6.2 4.3 4.9 4.9 4.1 5.7 4.5 5.7 5.7 5.3 5.6 4.7 3.6
##  [163] 5.6 3.8 4.6 6.6 4.1 2.4 4.1 4.1 6.1 4.2 3.0 2.9 4.8 5.1 4.7 2.9 4.6 3.7
##  [181] 4.0 4.2 4.8 4.5 4.5 5.8 4.0 5.7 3.3 4.5 4.5 3.6 3.6 3.7 3.3 5.5 2.1 4.2
##  [199] 4.4 3.7 4.7 4.5 6.1 3.9 4.6 3.6 5.1 4.6 5.1 3.6 5.6 4.1 5.2 3.9 5.2 5.3
##  [217] 4.1 4.2 3.9 3.5 3.6 3.4 4.3 4.4 5.6 4.4 5.0 5.3 5.6 4.7 3.0 4.5 5.1 5.4
##  [235] 3.9 6.4 4.0 4.2 4.2 4.1 5.6 4.0 5.2 4.2 5.1 4.5 3.1 4.8 4.8 4.3 5.4 5.0
##  [253] 5.0 4.3 3.7 3.7 4.1 4.3 5.2 5.6 4.1 5.2 4.8 3.9 5.1 4.2 4.9 5.2 4.7 3.3
##  [271] 5.0 4.9 3.9 3.0 4.1 4.5 4.7 4.6 5.2 5.0 5.2 5.2 3.3 3.9 6.2 3.9 5.1 4.4
##  [289] 5.3 5.1 2.2 4.9 5.4 4.9 5.5 3.8 3.5 5.2 5.1 3.1 6.3 3.4 5.6 4.1 4.9 4.2
##  [307] 3.9 4.7 4.5 3.8 5.3 3.3 4.1 5.3 4.4 5.8 3.8 4.3 4.4 5.5 4.5 4.1 3.9 3.9
##  [325] 6.5 5.0 5.5 5.9 5.2 6.9 5.6 4.1 6.5 5.0 4.2 5.5 4.6 3.9 3.4 4.3 4.4 5.4
##  [343] 4.6 4.6 4.2 6.2 4.8 4.2 5.0 5.5 4.8 3.5 4.7 4.8 6.2 5.1 3.2 5.3 2.3 5.3
##  [361] 6.7 4.4 5.1 5.6 3.9 2.0 6.0 5.2 3.6 4.2 4.8 4.0 5.7 4.6 5.4 5.1 5.1 2.6
##  [379] 5.0 4.3 5.8 4.2 6.0 4.4 3.3 4.1 3.6 4.2 5.9 5.0 2.9 5.5 5.1 5.3 6.5 4.6
##  [397] 2.9 4.7 4.1 4.0 5.1 4.5 3.9 5.8 4.9 3.8 5.0 7.0 5.2 4.6 4.0 4.9 5.9 4.9
##  [415] 3.4 4.2 6.0 4.0 4.8 3.1 5.4 3.9 3.8 3.1 4.0 4.8 3.1 3.7 4.9 4.9 2.4 4.8
##  [433] 4.9 3.8 4.5 5.7 3.2 5.1 3.9 4.2 4.0 5.3 4.2 3.8 5.3 4.0 5.5 3.4 3.5 6.0
##  [451] 5.1 4.5 4.2 3.8 4.7 5.7 4.6 5.5 4.9 5.7 4.1 5.5 3.9 3.1 5.0 4.5 4.2 5.1
##  [469] 4.2 4.3 4.0 5.8 5.5 3.4 4.6 5.7 4.9 4.8 4.2 5.6 3.7 5.8 5.1 4.0 4.7 3.1
##  [487] 4.2 4.6 4.6 3.2 4.7 4.0 3.5 6.0 3.0 4.3 3.8 4.4 5.4 4.0 4.2 5.6 4.4 4.9
##  [505] 4.6 4.5 5.9 4.3 6.0 4.6 4.4 4.2 3.9 5.0 4.1 3.4 5.5 4.4 5.1 4.4 5.3 5.1
##  [523] 4.1 5.0 3.3 6.2 4.5 2.5 4.5 2.3 5.6 3.0 3.3 4.8 4.4 6.0 3.6 5.4 5.5 4.4
##  [541] 4.7 4.4 3.4 6.0 3.8 6.1 4.5 6.2 1.8 3.6 5.5 4.0 4.9 5.0 6.3 3.7 5.3 3.1
##  [559] 5.5 5.5 4.3 3.6 4.4 4.2 4.7 4.5 5.6 4.4 4.9 4.9 5.0 4.9 4.3 2.7 5.5 3.7
##  [577] 4.5 2.7 4.5 6.1 6.5 3.8 5.6 4.6 5.2 4.9 6.0 4.2 4.2 2.9 3.4 5.0 4.8 4.1
##  [595] 4.8 2.9 3.9 5.2 4.8 4.4 5.4 4.4 4.7 4.9 4.6 5.8 5.4 4.1 3.9 5.7 3.0 5.2
##  [613] 5.2 3.6 4.9 1.8 4.8 4.7 5.5 3.8 4.1 3.4 3.9 4.7 4.5 4.9 4.6 4.0 3.7 5.2
##  [631] 4.8 4.7 4.8 3.2 3.8 3.8 5.2 4.2 4.3 4.9 5.5 3.9 4.7 2.9 3.5 5.0 4.8 4.1
##  [649] 3.9 3.4 3.7 4.5 5.7 3.2 4.4 2.6 4.9 5.8 4.8 6.1 4.3 4.8 5.1 4.4 4.0 4.8
##  [667] 5.2 6.1 4.7 4.5 3.2 4.5 5.4 3.4 5.5 3.1 4.0 5.0 6.0 6.9 4.3 3.6 2.5 3.8
##  [685] 5.0 3.0 5.7 5.8 4.7 5.7 4.8 3.2 5.0 5.9 3.9 5.2 3.9 4.2 3.5 3.6 3.8 3.1
##  [703] 5.4 4.1 5.3 3.9 4.6 5.3 4.5 4.7 6.0 4.0 2.8 5.0 4.3 3.9 4.4 5.3 4.7 3.9
##  [721] 4.5 3.7 3.2 3.4 3.9 3.8 4.4 4.8 5.1 4.3 4.3 5.0 4.5 4.0 3.2 4.8 5.5 4.5
##  [739] 3.8 5.7 3.8 6.1 3.7 4.4 4.3 3.4 4.5 6.0 4.6 4.2 3.6 3.4 5.5 3.8 4.4 5.1
##  [757] 5.1 4.5 3.1 5.2 5.9 4.0 6.3 5.5 3.9 6.3 5.1 3.7 3.4 5.5 4.9 3.6 4.6 4.8
##  [775] 4.1 3.2 4.7 4.8 5.1 4.1 3.7 4.0 3.7 4.0 5.1 5.1 4.2 3.9 4.4 5.2 4.2 5.3
##  [793] 4.7 3.5 4.8 3.8 5.8 5.3 5.1 2.8 3.7 4.3 3.3 3.1 3.7 5.0 5.5 5.4 4.1 5.1
##  [811] 5.0 3.6 4.0 3.4 3.2 5.7 4.3 5.3 4.4 4.9 3.8 4.0 4.8 3.3 5.3 3.1 4.2 5.0
##  [829] 3.0 3.7 3.5 3.7 4.8 3.9 4.1 3.3 3.9 3.5 3.5 3.0 5.7 5.1 4.4 4.0 5.8 2.9
##  [847] 5.2 4.0 6.0 3.3 4.9 3.8 4.3 4.7 5.5 5.4 5.0 3.6 3.2 4.1 5.7 5.5 3.0 4.3
##  [865] 4.9 4.5 5.3 5.2 3.8 5.5 5.6 2.5 5.7 3.6 3.9 4.5 5.2 2.3 4.7 5.0 1.7 5.3
##  [883] 3.8 5.2 5.2 3.6 4.7 5.4 4.0 5.7 5.5 5.0 5.1 4.5 4.1 3.1 3.6 4.9 4.9 6.0
##  [901] 3.9 4.8 4.7 4.1 4.3 4.0 5.3 4.4 3.0 5.5 3.8 2.8 6.0 2.4 5.0 4.5 4.4 3.8
##  [919] 5.0 2.3 5.6 4.5 3.7 5.6 5.8 5.2 4.1 4.2 5.3 6.4 4.6 4.5 4.5 3.7 4.6 5.5
##  [937] 3.5 3.6 5.0 4.5 3.5 3.7 4.5 4.1 5.2 5.1 5.3 3.8 3.6 5.2 4.3 4.3 6.3 3.3
##  [955] 3.1 2.7 4.6 3.1 5.4 6.0 4.6 4.0 6.3 4.8 3.6 4.1 4.0 3.6 4.6 4.7 5.1 3.7
##  [973] 6.1 5.5 4.8 5.8 6.0 4.9 6.2 4.9 6.0 4.4 4.0 3.7 5.0 4.2 5.3 3.0 4.2 4.4
##  [991] 4.5 4.0 5.2 4.8 5.3 5.2 4.4 4.7 5.0 4.6
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
##    [1] 3.7 2.8 4.9 3.6 4.4 2.7 5.1 5.0 3.8 4.0 4.1 5.3 3.9 5.3 4.6 3.7 5.4 5.2
##   [19] 3.7 2.6 4.2 3.4 4.7 4.2 3.7 4.3 5.4 5.0 4.5 3.7 4.4 4.0 4.9 3.7 3.6 4.1
##   [37] 4.4 5.6 4.0 3.8 4.8 4.9 6.8 4.1 6.7 4.1 6.3 5.5 5.1 3.6 4.6 5.6 5.5 5.2
##   [55] 4.3 4.8 4.1 3.6 3.5 4.0 5.2 4.3 6.3 5.6 4.8 4.3 4.3 3.9 4.7 3.0 4.6 5.2
##   [73] 5.7 3.6 5.3 4.7 4.6 3.7 6.2 5.9 4.6 4.5 4.3 5.3 4.5 4.4 4.2 4.6 4.4 4.8
##   [91] 5.4 4.0 3.5 3.9 4.3 5.3 3.7 5.5 3.9 6.4 4.7 3.9 6.4 3.1 3.7 4.0 3.5 3.4
##  [109] 2.6 4.0 4.5 3.1 4.6 4.6 4.4 5.3 3.3 4.1 4.1 5.1 4.2 4.6 4.2 5.0 5.2 4.1
##  [127] 4.0 4.5 2.7 5.1 4.2 4.4 3.5 3.3 3.2 4.3 3.9 4.7 4.1 4.6 4.0 4.7 5.6 4.1
##  [145] 4.8 4.3 3.3 4.4 4.9 2.4 4.9 3.3 4.1 5.0 5.3 5.7 4.9 3.9 3.7 5.7 1.6 5.3
##  [163] 4.5 4.1 4.6 6.1 4.9 3.2 3.7 4.0 6.2 5.0 4.7 6.0 4.5 4.2 6.0 3.3 5.7 4.4
##  [181] 4.4 5.2 4.8 4.1 5.2 3.8 5.0 4.3 4.4 5.3 5.8 4.8 5.0 5.0 6.3 3.8 5.1 5.3
##  [199] 5.9 3.7 4.2 5.1 3.1 4.6 6.7 4.2 3.8 4.8 5.4 5.9 4.1 4.1 4.0 4.8 5.2 4.5
##  [217] 4.0 4.1 4.6 4.5 4.5 5.4 5.1 4.0 4.7 3.0 7.0 4.2 4.7 4.3 4.6 5.2 6.3 3.8
##  [235] 4.5 6.0 5.1 3.8 3.8 5.5 3.8 4.1 3.3 4.4 4.5 3.9 3.5 3.7 5.5 5.6 5.7 6.2
##  [253] 4.4 3.5 4.6 5.1 4.7 5.7 5.9 3.8 4.3 3.3 4.9 5.5 5.2 5.7 4.6 3.6 3.1 6.2
##  [271] 4.3 3.2 4.8 4.4 5.2 5.8 4.4 5.3 1.8 4.1 4.3 4.3 3.9 3.6 4.3 3.9 5.7 2.5
##  [289] 5.3 4.3 2.4 6.0 4.5 6.7 5.0 6.0 5.6 4.2 4.8 4.8 5.0 3.8 4.5 4.8 4.6 3.6
##  [307] 4.8 3.7 4.5 3.4 4.7 5.6 3.6 3.4 4.1 3.1 4.2 4.8 3.9 6.3 5.1 5.4 3.8 3.0
##  [325] 4.5 4.1 4.9 6.3 4.6 4.3 3.1 4.7 5.7 3.7 4.1 4.0 3.8 5.0 4.4 4.7 3.5 4.4
##  [343] 5.3 3.8 5.1 4.5 4.7 5.1 5.6 4.4 4.6 4.6 5.6 5.5 3.1 3.7 5.1 3.4 4.8 4.8
##  [361] 3.2 5.8 6.3 5.5 6.1 2.5 4.5 4.7 5.3 5.1 4.6 3.5 3.6 2.7 5.4 3.9 5.1 4.2
##  [379] 4.6 4.5 3.4 4.4 4.8 2.8 4.2 5.0 5.7 4.4 3.6 5.1 4.4 5.2 2.7 5.1 4.5 4.2
##  [397] 4.7 3.7 2.4 4.5 3.2 3.7 5.4 5.2 4.6 6.4 3.9 5.3 4.6 3.5 4.0 4.3 4.2 4.1
##  [415] 4.5 2.7 7.0 4.4 4.6 4.9 4.8 5.9 4.3 6.0 2.7 3.7 4.3 4.9 3.7 3.2 2.3 5.9
##  [433] 3.4 4.8 3.9 4.8 4.3 3.7 3.6 4.5 3.4 4.4 3.5 5.3 5.4 4.1 4.9 3.6 4.2 5.9
##  [451] 4.4 4.4 5.5 4.2 2.8 3.8 5.0 5.9 4.8 4.1 4.1 4.2 5.0 3.0 4.2 4.5 3.8 4.5
##  [469] 4.7 5.9 4.2 3.8 5.8 3.7 5.0 6.0 4.5 5.7 5.2 5.3 5.7 4.9 4.9 4.1 5.6 5.6
##  [487] 3.5 6.3 5.2 4.6 3.8 3.4 5.4 3.9 3.1 5.9 5.1 4.6 4.3 4.6 4.3 4.7 3.5 4.7
##  [505] 3.2 4.8 3.3 4.8 3.6 3.8 4.5 4.9 5.4 5.5 3.6 3.6 2.6 5.0 5.1 3.7 4.1 4.3
##  [523] 3.7 3.2 4.2 4.2 4.6 3.2 5.2 3.8 4.9 4.9 3.7 5.7 4.0 4.7 3.7 3.3 5.1 5.3
##  [541] 3.6 6.0 4.5 5.4 3.7 5.0 3.7 3.3 4.3 4.0 5.8 4.3 4.8 6.3 4.8 5.8 4.7 5.7
##  [559] 4.1 4.9 3.6 5.6 4.5 5.1 4.1 4.5 2.8 3.2 4.4 3.4 6.6 4.7 4.6 2.4 4.0 5.5
##  [577] 4.4 4.4 4.1 5.9 4.8 4.0 4.1 6.6 3.4 4.0 3.7 3.7 4.2 4.6 4.7 3.9 3.8 3.2
##  [595] 5.6 4.9 2.2 4.1 6.0 4.4 4.3 5.4 5.0 5.3 4.5 5.6 4.5 5.4 6.0 4.3 3.7 3.0
##  [613] 4.4 4.7 3.9 3.3 3.1 3.0 6.1 4.0 4.0 5.4 5.6 6.1 4.2 4.4 4.6 5.5 4.0 3.3
##  [631] 3.5 3.7 4.4 4.4 4.7 3.7 3.3 5.8 4.6 5.7 4.9 4.8 4.2 4.5 4.1 4.6 4.2 5.9
##  [649] 3.4 3.7 2.1 4.7 5.4 4.7 5.9 4.2 4.4 5.6 4.7 4.0 3.2 4.6 3.9 4.9 4.8 3.6
##  [667] 4.4 4.2 3.7 5.0 2.4 5.5 3.7 6.8 4.3 4.9 4.7 4.0 4.7 2.5 5.1 5.1 4.4 5.4
##  [685] 5.0 3.9 3.4 4.4 5.8 3.9 5.8 3.9 5.5 5.6 4.7 3.5 5.8 6.0 5.7 4.9 3.8 2.9
##  [703] 5.1 5.4 3.8 5.2 4.2 6.1 3.6 5.5 5.2 3.3 5.5 5.2 4.9 4.2 5.1 5.2 4.0 4.4
##  [721] 3.6 5.1 4.2 4.7 4.9 6.1 4.2 4.1 4.3 3.7 5.3 5.0 5.1 5.0 5.9 3.1 4.1 2.8
##  [739] 5.2 5.3 2.9 4.0 6.2 4.6 6.0 6.0 5.6 6.2 5.8 3.8 5.3 4.6 3.0 7.1 3.6 4.3
##  [757] 4.7 6.3 3.5 3.7 5.3 4.6 4.4 4.3 5.3 5.1 3.4 4.0 5.2 5.1 4.9 4.7 4.5 3.6
##  [775] 4.1 3.2 6.1 5.4 5.1 4.2 3.8 5.8 4.1 4.9 3.2 3.9 4.0 3.5 5.0 4.6 4.4 4.1
##  [793] 4.4 4.2 4.9 4.5 5.7 4.2 5.3 5.3 4.5 4.1 3.9 3.1 5.0 5.9 6.2 4.1 4.0 2.9
##  [811] 5.7 4.9 4.3 5.1 3.7 4.9 4.5 3.0 4.0 2.9 2.4 4.3 5.2 4.7 3.6 5.0 4.2 5.4
##  [829] 5.5 3.4 4.2 4.6 5.5 4.7 4.0 5.6 3.0 4.0 6.0 4.3 2.9 5.4 4.1 4.3 5.3 3.4
##  [847] 4.1 5.5 4.0 3.5 6.4 4.7 3.7 5.4 5.9 2.0 4.3 4.7 5.1 4.3 3.5 5.6 6.7 5.5
##  [865] 4.8 4.7 6.3 3.6 3.8 4.4 2.3 4.5 4.4 4.5 4.7 4.7 5.5 5.7 5.5 5.3 5.5 4.5
##  [883] 4.4 4.8 5.6 4.6 4.1 4.9 3.7 4.7 5.3 5.7 4.3 6.0 4.4 3.8 3.1 3.1 4.3 3.8
##  [901] 5.4 4.8 3.7 3.9 3.9 6.2 4.2 6.7 6.0 5.8 4.1 5.6 5.1 5.8 4.2 3.7 4.9 2.7
##  [919] 4.9 4.2 3.6 5.4 2.8 4.8 4.8 3.8 4.4 6.1 3.5 4.9 4.1 5.1 2.4 5.5 3.9 5.9
##  [937] 4.6 5.6 6.8 4.0 5.1 4.9 5.1 3.9 3.0 4.6 4.7 3.6 4.8 5.4 4.6 5.1 2.8 3.7
##  [955] 3.7 2.7 5.3 3.2 4.7 5.3 3.5 5.1 4.9 5.1 3.8 4.4 3.4 4.1 4.9 4.1 3.2 5.2
##  [973] 5.7 5.1 4.2 5.2 3.7 3.7 4.2 3.7 4.2 4.8 5.0 2.9 3.3 5.8 4.4 4.7 5.4 5.1
##  [991] 3.1 4.5 3.7 6.3 3.3 4.5 5.5 2.4 4.6 5.4
## 
## $func.thetastar
## [1] 0.0133
## 
## $jack.boot.val
##  [1]  0.497746479  0.428440367  0.355685131  0.153645833  0.005232558
##  [6] -0.020689655 -0.098498498 -0.271207430 -0.386235955 -0.545738636
## 
## $jack.boot.se
## [1] 0.9998375
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
##    [1] 5.2 3.6 3.9 4.3 3.0 4.4 4.5 4.7 4.7 5.0 3.5 4.6 5.1 3.8 5.0 4.3 4.5 5.0
##   [19] 4.6 4.9 4.6 2.8 4.5 3.5 3.7 4.7 4.7 5.2 4.8 4.6 4.2 4.4 4.8 6.1 3.3 5.4
##   [37] 3.7 3.7 4.7 6.3 6.3 4.4 5.0 3.4 6.7 4.9 3.6 4.2 4.5 4.8 5.1 5.4 3.8 4.2
##   [55] 3.7 3.7 4.7 3.7 4.2 3.9 4.6 4.4 4.5 4.5 4.3 6.7 5.3 3.8 5.7 5.3 4.5 5.0
##   [73] 4.6 3.7 3.1 3.9 5.0 3.5 4.4 3.5 4.3 5.0 4.9 5.4 3.8 4.4 5.3 4.2 2.8 4.4
##   [91] 3.3 3.7 5.0 5.2 3.4 3.7 5.5 4.1 4.7 7.1 4.8 2.9 4.7 4.6 3.2 3.7 3.8 5.4
##  [109] 5.7 4.8 4.9 4.0 4.4 4.2 4.9 4.1 3.3 4.3 4.3 3.3 4.3 3.2 4.0 4.3 5.5 3.9
##  [127] 4.2 5.5 3.7 3.4 5.6 5.9 4.2 4.9 3.9 3.9 3.9 4.6 5.1 3.3 6.0 5.3 4.6 5.5
##  [145] 5.4 5.3 4.0 3.4 3.9 4.1 5.7 4.4 4.5 3.6 3.4 3.0 3.5 3.8 4.3 5.1 4.0 3.9
##  [163] 4.6 5.3 5.0 3.4 5.4 3.5 3.4 3.3 3.8 4.5 3.7 6.5 4.2 5.0 2.6 4.1 4.1 5.4
##  [181] 3.8 3.6 2.2 2.9 4.2 3.1 3.7 5.3 5.1 4.0 5.0 4.5 2.7 3.1 2.6 2.5 6.3 2.7
##  [199] 4.1 3.2 2.9 4.4 4.1 5.0 3.9 3.3 5.3 4.9 3.8 5.9 2.3 7.0 5.1 4.3 3.0 4.3
##  [217] 5.3 5.6 5.9 5.6 3.9 4.0 5.3 5.4 4.8 3.9 3.9 4.4 5.1 5.4 5.8 3.0 4.8 4.2
##  [235] 3.0 5.5 4.5 5.0 3.6 4.7 2.4 5.5 3.7 4.2 3.8 4.1 3.1 3.7 4.3 4.0 3.6 5.7
##  [253] 4.3 3.9 2.2 4.3 5.5 4.6 2.8 5.0 5.4 4.2 5.4 4.9 4.1 5.7 4.2 5.3 4.7 5.8
##  [271] 4.3 3.9 4.7 6.1 2.5 6.7 4.9 5.0 3.0 6.2 5.9 3.7 5.0 6.3 2.9 3.8 4.0 5.2
##  [289] 4.6 5.5 4.8 4.9 3.0 4.4 4.6 5.3 4.8 3.8 6.1 3.9 5.4 4.8 5.9 3.9 3.6 3.6
##  [307] 3.6 4.1 2.5 5.2 6.5 4.0 4.1 5.4 3.6 3.7 4.2 5.9 2.7 4.0 2.9 4.7 5.2 4.3
##  [325] 4.7 3.9 5.1 3.5 4.1 5.0 2.7 3.7 5.6 4.4 3.6 4.2 5.5 4.8 5.2 5.2 4.3 5.4
##  [343] 2.0 2.8 6.0 5.8 6.0 5.0 5.5 3.2 4.8 5.3 5.9 4.1 4.3 3.1 3.1 1.7 3.8 6.4
##  [361] 3.1 5.4 4.4 5.7 5.6 3.8 5.3 4.3 2.3 4.1 3.2 3.1 3.6 4.4 4.0 5.1 4.4 4.4
##  [379] 4.5 4.5 4.5 3.1 3.3 2.7 3.3 5.0 5.6 5.2 3.2 4.3 3.8 5.2 5.0 2.8 5.6 4.7
##  [397] 4.1 4.1 4.8 5.6 2.9 3.8 6.0 4.8 5.2 4.9 5.9 4.9 3.2 5.2 4.6 3.5 4.8 4.8
##  [415] 2.7 5.8 5.8 5.5 4.5 3.9 2.8 5.6 3.0 4.3 4.1 5.4 2.3 5.9 5.7 6.3 5.3 5.3
##  [433] 3.3 4.2 3.2 2.3 4.4 4.4 4.8 4.8 3.5 4.7 3.8 4.2 5.3 5.5 2.9 5.2 4.1 4.2
##  [451] 5.5 4.4 5.6 3.9 4.9 5.3 2.9 6.0 4.9 4.3 5.5 5.8 5.2 6.3 3.0 3.6 4.4 4.7
##  [469] 4.0 4.7 5.1 5.2 4.5 3.9 4.7 3.8 4.0 5.0 5.0 4.3 2.8 5.4 4.8 5.8 4.7 4.1
##  [487] 3.5 5.0 3.0 3.7 4.8 4.8 6.0 3.8 5.0 5.8 5.5 4.1 2.3 4.5 4.8 4.3 3.5 3.4
##  [505] 5.9 4.2 6.6 3.7 3.9 4.6 4.7 5.8 5.5 5.5 5.0 5.3 3.8 4.2 4.1 2.1 4.6 5.5
##  [523] 5.6 4.0 4.9 4.6 5.3 4.4 4.2 3.9 4.2 6.0 5.1 3.4 5.6 4.5 4.4 4.1 5.3 5.0
##  [541] 4.9 6.0 6.4 3.9 5.0 4.9 4.1 3.1 5.4 3.2 3.7 3.8 6.2 4.1 3.8 5.2 3.5 5.3
##  [559] 5.4 5.5 3.8 3.5 6.1 4.4 4.5 4.6 4.9 6.2 5.3 3.1 2.4 3.5 4.5 5.1 3.4 5.2
##  [577] 5.4 4.9 3.7 4.8 3.9 4.2 6.3 3.5 2.4 4.1 6.1 4.5 5.5 3.8 4.9 6.1 3.4 4.4
##  [595] 4.8 6.2 4.9 4.7 4.5 4.8 5.6 3.3 5.9 4.6 4.4 5.1 4.3 3.5 4.9 5.7 3.5 4.5
##  [613] 4.7 4.7 4.6 5.8 5.7 4.4 4.5 4.0 3.6 3.9 4.3 5.8 4.1 6.3 4.9 3.6 4.6 5.2
##  [631] 4.8 4.4 3.9 3.6 4.4 4.9 4.9 5.6 4.3 3.4 3.7 5.3 4.2 4.7 4.7 5.0 5.4 4.8
##  [649] 4.7 5.1 4.1 4.7 4.7 4.4 3.3 3.1 4.3 7.0 5.6 4.8 4.9 3.9 3.8 4.2 6.0 5.6
##  [667] 3.3 6.3 4.3 3.2 5.6 6.2 3.5 3.4 2.8 5.8 4.4 3.5 6.2 4.8 3.6 4.4 4.4 3.7
##  [685] 5.9 2.4 3.4 5.1 3.7 4.5 4.3 6.8 6.9 4.9 4.3 5.6 6.0 5.8 4.1 4.0 4.7 5.1
##  [703] 3.3 3.5 4.7 3.1 4.8 4.9 4.0 6.5 5.5 2.4 3.9 4.6 4.8 5.0 5.1 5.3 4.0 4.0
##  [721] 3.3 4.1 3.5 4.5 3.7 4.6 3.9 4.7 4.7 4.7 4.4 6.2 5.1 4.6 3.6 5.2 5.1 5.5
##  [739] 5.1 3.9 6.0 3.7 5.5 4.8 3.6 5.4 3.7 3.6 2.7 5.0 4.8 5.5 4.4 5.1 4.8 3.3
##  [757] 4.7 3.5 5.3 5.0 4.5 3.0 5.4 5.2 5.6 4.1 3.7 2.2 5.4 4.0 5.9 6.0 4.2 3.8
##  [775] 6.4 3.2 7.3 4.4 5.1 4.1 4.7 4.5 2.4 5.6 4.5 4.8 4.2 5.5 3.9 5.6 4.6 5.0
##  [793] 4.5 4.6 6.5 5.6 4.8 4.8 3.2 4.2 4.1 4.7 4.8 4.9 4.5 2.9 3.6 5.7 4.5 3.4
##  [811] 3.5 4.9 4.4 6.5 4.4 2.7 3.2 5.3 5.3 4.2 5.0 5.0 5.3 4.3 6.1 4.6 4.3 3.9
##  [829] 5.9 4.1 4.5 5.1 5.1 4.6 5.7 4.7 3.8 3.7 2.6 4.3 3.7 1.9 3.2 3.9 5.4 5.0
##  [847] 6.5 4.9 4.8 5.4 3.7 4.5 5.3 6.2 4.7 5.2 5.1 4.4 4.5 3.4 4.8 5.0 5.5 4.4
##  [865] 4.1 4.6 3.8 5.6 4.5 4.0 4.2 4.8 2.1 5.4 4.5 3.6 2.5 5.8 5.6 2.9 5.7 4.4
##  [883] 4.3 4.5 4.6 4.1 5.6 4.8 5.0 5.6 4.3 5.5 4.0 4.2 5.2 5.0 5.3 2.5 4.7 5.7
##  [901] 5.0 3.7 5.6 4.5 3.6 4.0 3.8 3.3 3.9 4.4 3.5 4.2 5.3 6.2 5.3 5.6 4.4 4.8
##  [919] 4.3 5.1 5.0 6.2 5.1 4.8 3.5 4.3 6.2 3.6 4.5 2.6 6.7 2.8 5.6 4.5 4.7 4.2
##  [937] 4.8 4.5 3.6 4.0 3.7 3.7 5.9 2.8 4.7 3.1 4.9 4.7 4.0 5.7 5.1 3.7 3.8 4.8
##  [955] 5.0 5.4 4.0 3.3 5.8 5.7 6.3 3.9 4.0 2.0 3.6 4.6 3.8 5.9 2.9 5.1 6.4 4.7
##  [973] 5.1 4.6 5.9 4.4 3.2 5.6 5.1 4.7 3.6 3.7 4.5 4.7 4.5 4.6 3.1 4.0 5.4 4.5
##  [991] 4.8 5.7 5.5 2.8 4.1 6.3 4.0 4.2 5.2 4.8
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.500 5.500 5.332 5.300 5.200 5.100 5.000 4.700 4.700 4.500
## 
## $jack.boot.se
## [1] 0.9999507
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
## [1] 0.2250226
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
##    7.900029   16.091809 
##  ( 3.460965) ( 7.278508)
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
## [1]  0.75846867 -0.62630618 -0.01082540  0.02945749  0.75046601 -0.20735502
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
##    [1]  5.530912e-01  3.286288e-01 -4.461000e-01  8.084286e-01 -5.164641e-01
##    [6]  3.847090e-01  5.646503e-02 -2.461702e-01  3.357087e-01  3.110428e-01
##   [11]  1.160432e-02  4.283633e-01  6.536365e-01  2.323094e-01  6.397578e-01
##   [16]  1.073095e-01  3.682988e-01  2.378789e-01 -1.118342e-02  7.836706e-01
##   [21]  1.345073e+00  1.380351e-01  5.090563e-01  1.321084e-01 -2.314058e-01
##   [26]  1.001034e-01  1.501489e-01  8.041303e-02  1.480597e-02 -9.068334e-01
##   [31]  8.249805e-01  1.599986e-01 -2.182746e-01  2.252214e-01 -2.484297e-02
##   [36] -2.707450e-01  4.775409e-01 -5.751607e-01  6.709003e-01  4.708097e-01
##   [41]  5.001185e-01  9.031287e-01  3.524562e-01  1.366168e-02  5.259988e-01
##   [46]  1.728448e+00  6.739752e-02 -1.147442e-01  6.784379e-01  6.601322e-01
##   [51] -1.106553e-01 -5.325301e-01  1.296900e+00  5.179036e-01  1.152029e-01
##   [56] -8.545467e-02  8.181719e-01  3.216332e-01  8.774133e-01  1.581788e-02
##   [61] -1.678065e-01  7.285383e-02  7.007687e-02 -8.846969e-01  1.709091e+00
##   [66]  4.218217e-02  5.590160e-01 -2.221006e-01 -1.950308e-01 -1.776047e-01
##   [71]  2.382247e-01  4.075920e-01 -2.759207e-01  2.042567e-01  5.609022e-01
##   [76]  2.268462e-01  2.216197e-01 -1.548355e-03 -8.200553e-01  5.930802e-01
##   [81]  9.329593e-02  2.954757e-01 -4.052416e-01 -1.682897e-01  4.063712e-01
##   [86] -1.194262e-01 -1.239100e-02 -9.116969e-02 -2.352475e-02  2.085565e-01
##   [91]  7.867988e-01 -2.181675e-01 -3.576336e-01  1.992601e-01  2.876784e-01
##   [96] -8.027906e-01 -2.734377e-01  5.939629e-01  3.920322e-02  5.363658e-02
##  [101]  4.692093e-01  3.182376e-01  6.316901e-01 -1.408678e-01  5.857523e-01
##  [106]  1.071316e+00  2.539137e+00 -4.646624e-01  6.398313e-01  1.709211e+00
##  [111] -3.785667e-01  5.389706e-01 -8.920489e-01 -8.241112e-02  2.362880e-01
##  [116] -2.254347e-01 -4.841720e-01 -1.561714e-01  7.757393e-01  4.004086e-01
##  [121]  1.302202e-01  4.596577e-01  4.325815e-01 -9.147264e-01 -6.619383e-01
##  [126]  1.741383e-01  3.098526e-01  9.630750e-01  7.076555e-01  4.664034e-01
##  [131]  3.979223e-01 -4.845961e-02  6.535631e-01 -6.046341e-01  3.665105e-01
##  [136]  4.298159e-01  6.202672e-01  1.250344e-01  4.681558e-01  7.915664e-01
##  [141] -2.993538e-01  2.273209e+00  6.921149e-01 -1.898862e-01  7.054307e-01
##  [146] -2.330190e-02  3.274733e-01  1.646456e+00 -1.000950e-01  3.708999e-01
##  [151]  1.298043e-01 -2.004132e-01  3.022600e-01  7.250878e-02  7.230326e-01
##  [156]  5.709586e-01 -2.601306e-01  5.371637e-01  2.964850e-01 -1.707113e-01
##  [161] -1.937045e+00  2.464254e-01 -1.832788e-01  6.859935e-01 -1.540325e-01
##  [166]  2.123733e-01  8.292855e-02  7.574804e-01  2.151228e-01 -7.594549e-01
##  [171]  2.433494e-01 -1.918017e-01 -1.191364e-01 -5.813943e-02  6.604182e-01
##  [176]  6.471579e-01  5.485386e-01  4.461097e-02 -8.374841e-03 -1.661044e-01
##  [181] -5.917156e-01  2.130739e-01  9.501177e-02  1.211664e-01  4.955735e-01
##  [186]  3.558226e-02  9.632368e-01 -1.445102e-01  4.647309e-01  1.131681e-01
##  [191]  3.423360e-01  1.830211e-01 -7.372395e-01  4.763487e-01  1.118494e+00
##  [196] -2.705802e-01  4.704842e-01 -1.217523e+00 -5.259229e-01 -2.956419e-01
##  [201]  3.024777e-01 -2.746714e-01  1.172248e-01  2.125580e-01  3.117731e-01
##  [206]  2.496220e-01  3.855148e-01  5.732438e-01  6.998245e-02  2.201989e-01
##  [211]  5.387441e-02  2.688085e-01  4.723383e-01 -1.149036e-01  2.510614e-02
##  [216]  1.214725e+00  5.680509e-02  7.739249e-02  4.292223e-01  1.969462e-01
##  [221]  6.181841e-01 -6.520811e-01  1.112658e-01  9.518815e-02  6.960061e-01
##  [226] -3.376301e-01  5.924044e-01 -1.035487e-01  4.696894e-01  3.202779e-01
##  [231]  1.120272e+00  8.259786e-01 -3.030088e-01  5.412257e-01  2.197561e-01
##  [236]  5.308025e-02  3.668832e-01  4.991112e-01  1.074881e+00 -3.949647e-01
##  [241]  3.148683e-04  2.897986e-01 -2.501424e-02  1.392335e+00  7.384111e-02
##  [246]  2.667309e-01 -1.414177e-01 -2.632171e-01  6.107967e-01 -2.587897e-01
##  [251] -2.429040e-01  2.593994e-01  3.731544e-02  9.406177e-02 -4.615107e-01
##  [256]  7.571590e-01  2.314330e-01  6.230308e-01  4.335272e-02  2.487153e-01
##  [261]  6.362664e-01  4.297053e-01  7.015642e-01 -1.224292e-01 -8.986324e-02
##  [266] -5.267927e-01  1.571870e-01  4.932399e-03 -1.002274e+00  4.735820e-01
##  [271] -1.015167e-01  2.192240e-01 -1.183242e-01  2.745344e-01 -1.623052e-01
##  [276] -1.280844e-02  6.202504e-02  9.649044e-01 -2.720207e-01  7.319249e-01
##  [281]  5.868374e-01  3.072219e-01 -2.227940e-01 -3.969144e-01  3.654791e-02
##  [286] -3.923649e-01  1.053407e-01  7.206964e-01  1.645956e-01  3.781689e-01
##  [291]  1.826822e-01 -4.806599e-01  6.006874e-01  5.414928e-01 -1.110636e+00
##  [296]  3.798014e-01  2.140348e-02  6.857833e-02  7.580904e-01  6.140320e-02
##  [301]  4.657681e-01 -1.686637e-02  1.418998e-01  3.589837e-01  7.990348e-01
##  [306] -1.273834e-01  8.790756e-01 -4.876814e-02  3.314323e-01  6.546182e-03
##  [311] -1.200730e-01  2.927640e-01  1.836391e-05  1.068799e-01  5.150837e-01
##  [316]  4.162354e-01  8.370185e-01  4.839595e-01 -1.520257e-01  1.023484e+00
##  [321]  2.269569e-01 -1.628851e-01  3.564829e-01  1.923939e-01 -1.012027e+00
##  [326] -6.946189e-01  5.094608e-01 -4.007803e-02  1.423038e-01  3.002368e-01
##  [331] -1.269855e+00 -3.151508e-01  6.608959e-01  4.075920e-01  8.782989e-02
##  [336] -9.149421e-02 -4.416187e-03 -6.594906e-01  2.914116e-01  3.566959e-01
##  [341] -6.144735e-01  5.409143e-01 -2.215719e-01 -9.035742e-02 -1.720486e-01
##  [346] -6.447529e-02  1.033560e-01 -9.159514e-01  4.220945e-01  8.515595e-01
##  [351] -8.930312e-01  2.988926e-01  3.410164e-01 -4.230833e-01  5.545823e-01
##  [356]  7.684894e-01  1.153878e+00 -2.655088e-01  1.053578e+00  8.590374e-01
##  [361]  2.237835e-01  4.488870e-01  1.017737e+00  1.204947e-01 -1.034381e+00
##  [366]  9.736007e-03  1.174840e+00  1.298303e-02 -4.591263e-01  6.874329e-01
##  [371]  6.392722e-01  5.759984e-01  3.552169e-01 -9.452835e-02  8.774815e-01
##  [376]  1.076425e-01 -4.289202e-02  2.742127e-01  5.415123e-01  5.689032e-01
##  [381] -1.080674e-01  7.666922e-01 -2.070372e-01  5.081953e-01  9.885095e-01
##  [386]  6.142812e-01 -1.801433e-01  5.499055e-01 -1.364529e-03  4.009845e-01
##  [391]  2.253153e-02 -3.498158e-02 -2.290519e-01 -3.523126e-01  5.313592e-01
##  [396]  1.418413e-01  6.669207e-01  2.875024e-01 -5.242146e-01 -2.149208e-01
##  [401] -2.843985e-01 -5.289124e-02 -1.289459e-01 -5.099919e-01 -7.120375e-03
##  [406]  3.265375e-01  7.122480e-01 -5.350355e-01 -3.738595e-01 -1.397992e-01
##  [411]  8.819364e-02  2.111001e-02  5.362154e-01 -3.748285e-01  4.090308e-01
##  [416] -8.565929e-01 -3.248412e-01 -3.676979e-01 -1.892605e-01 -1.728936e-01
##  [421] -5.771380e-01 -4.246640e-01  6.426855e-01 -6.144386e-01  8.887222e-01
##  [426]  1.918694e-01  8.181968e-02 -6.569954e-01  3.433283e-03 -1.027207e-01
##  [431] -1.592672e-01 -6.085666e-02  1.772757e-01 -6.030155e-01  2.515626e-02
##  [436] -4.441329e-01  6.831998e-01  2.801786e-01 -2.950408e-01  3.126727e-01
##  [441]  3.976548e-01  7.298015e-01  1.658432e+00  1.248834e-01 -1.816175e-01
##  [446] -1.656383e-01  3.626381e-01  6.948809e-02 -8.901841e-02  1.763972e-01
##  [451] -5.479197e-01  6.372573e-01 -3.725879e-01 -4.544787e-01  1.217471e-01
##  [456] -3.338218e-01  4.382923e-01  9.357877e-02  5.002029e-01  1.610592e+00
##  [461]  4.117871e-01  5.303994e-01  4.007290e-02  5.101984e-01  2.819868e-01
##  [466]  2.110234e-01  6.762042e-02 -6.448990e-01 -4.373248e-01 -1.222680e-01
##  [471] -7.047109e-01 -1.856440e-01  6.419765e-01  1.636408e-02  3.929486e-01
##  [476] -3.786658e-01  1.113106e-01  1.097033e+00  6.645429e-02  2.005759e-01
##  [481] -3.745783e-02  1.239195e+00 -7.143720e-01  4.779618e-01  4.499164e-03
##  [486] -1.365936e-01 -9.403579e-01  2.469949e-01  2.698743e-01  3.954640e-01
##  [491] -9.911280e-02  6.540737e-02  5.222481e-01  5.173134e-01  4.718621e-01
##  [496] -5.183233e-01 -3.733933e-01 -1.118105e+00  3.714528e-01  2.985451e-01
##  [501] -5.705943e-01  9.272587e-01  1.251484e+00  1.149890e-01  1.711592e+00
##  [506]  4.111864e-01  1.557124e-01  1.203279e+00  4.093278e-01 -2.336389e-02
##  [511]  2.097946e-01  1.275175e-01 -1.989189e-02  5.756308e-01  3.902390e-01
##  [516] -5.999508e-03 -7.901557e-01  1.434927e+00  1.101326e+00 -2.623441e-01
##  [521]  4.646278e-01  5.480641e-01 -1.793407e-01  3.318773e-01  1.136102e-01
##  [526]  3.323219e-01  3.457431e-01 -1.324731e-01 -3.795886e-01  5.247777e-01
##  [531] -6.211826e-01  6.582501e-01 -1.437240e-01 -9.089667e-01  3.055860e-01
##  [536]  2.738756e-01 -2.252061e-01 -1.944728e-01  9.867069e-02  7.979357e-01
##  [541]  1.645213e+00  1.498330e-01  4.074468e-02 -2.783234e-01  6.130922e-01
##  [546]  2.770515e-01  5.094124e-02  3.327207e-02  2.794365e-01  5.487599e-01
##  [551] -4.488756e-01 -5.325301e-01 -4.279693e-01  4.624858e-01  2.059351e-01
##  [556] -1.147442e-01 -4.247991e-01  2.058665e-01  6.855284e-01 -7.512582e-01
##  [561]  3.004238e-01  4.134349e-01  2.181114e-01 -1.846979e-01  1.689854e+00
##  [566]  2.813166e-01  4.472844e-01 -8.887132e-01 -3.593614e-01 -6.619471e-02
##  [571]  1.473072e-01  5.252457e-02  2.553133e-01  4.996285e-01  7.846846e-01
##  [576]  1.015511e-01 -6.918347e-01  1.317464e-01 -7.327292e-01  1.677212e-01
##  [581]  9.691223e-01 -6.604560e-03  4.622684e-02  8.195766e-01  1.554195e-01
##  [586]  1.127739e+00  3.005440e-01  8.474227e-02 -7.362408e-01 -3.140182e-01
##  [591]  9.828958e-01  2.298020e-02 -1.289027e-01  8.686031e-01 -2.705226e-01
##  [596]  4.993787e-01  1.728448e+00  1.670126e-01  1.019503e-01  5.477174e-01
##  [601]  4.657681e-01  2.038490e-01  1.044952e+00  8.063781e-01  2.321788e-01
##  [606]  3.275460e-02  6.304775e-01  3.282294e-01  3.187109e-01  3.104632e-01
##  [611] -7.472295e-01  1.560423e+00 -1.668163e-01 -8.368571e-01  9.705591e-01
##  [616] -3.653064e-01  3.435672e-02  3.304982e-02  2.047588e-01 -7.762964e-02
##  [621] -8.374841e-03  2.040464e-01  2.015478e-01  2.002293e-01  6.799649e-01
##  [626] -1.367337e-01  1.702585e-01  3.519611e-01  6.606217e-01 -8.525679e-01
##  [631]  1.141890e+00  2.382247e-01 -1.722517e-02  5.406569e-01  1.692713e+00
##  [636]  1.073240e+00 -5.993559e-01  8.713162e-02  1.774861e-01  6.181609e-01
##  [641] -2.376287e-01  5.077190e-02  8.335522e-01  2.582157e-01  1.436707e+00
##  [646]  1.935431e-02  1.447432e+00  1.025989e-01 -2.214820e+00  6.231408e-01
##  [651] -4.271704e-02  6.439605e-01  2.699703e-01 -1.654424e-01 -7.232470e-02
##  [656]  2.745344e-01  8.623808e-01  7.143532e-01  8.004030e-02 -1.333489e-01
##  [661] -2.191804e-01 -6.425888e-01 -8.137837e-02  3.629470e-01  8.557548e-01
##  [666] -1.149277e+00 -3.815429e-01  5.066973e-03  3.505385e-01  1.149313e+00
##  [671] -4.012294e-01  6.290842e-01  9.657932e-01  1.318334e+00 -2.579163e-01
##  [676]  1.482251e-01  4.084616e-01  8.652570e-02 -4.534738e-01  9.993312e-01
##  [681] -7.827507e-01  1.623023e-01 -4.732907e-02  1.454413e-01 -5.279838e-01
##  [686] -3.669577e-01  1.590269e-01 -1.537042e+00 -6.681361e-01 -5.361118e-01
##  [691] -1.001558e-01  6.877075e-01  8.207835e-01  1.092704e+00 -1.547436e-01
##  [696] -3.289536e-02 -4.411503e-02  9.353042e-02 -6.616402e-01  1.423791e+00
##  [701]  8.876934e-01 -1.009363e+00  6.462855e-01  6.067820e-01 -3.054268e-01
##  [706]  2.979911e-02 -3.106067e-01 -1.568542e-01  2.841473e-01 -9.806061e-01
##  [711]  1.326841e-01  7.010687e-01 -1.078815e-01  8.168374e-01  6.722711e-02
##  [716] -8.472875e-02 -8.232584e-01  1.500077e-01 -9.006867e-04 -8.511510e-03
##  [721]  4.068860e-01  3.300176e-01  4.677550e-01 -1.037910e+00 -9.385012e-01
##  [726] -4.414389e-01 -1.044967e+00 -2.942426e-01 -2.060353e-01  4.667936e-02
##  [731] -1.151733e-01 -8.007618e-02  5.144408e-01  1.723072e-01  2.023208e-01
##  [736]  2.649424e-01 -5.529065e-01  1.113106e-01  4.035483e-01  5.604531e-01
##  [741] -2.713280e-01  3.251402e-01  1.208661e+00  1.811567e-01  1.048064e+00
##  [746]  4.309575e-01  5.282258e-01  1.174840e+00 -9.144165e-01 -9.879859e-01
##  [751] -8.437361e-02 -1.621585e-02 -1.707684e-01  8.068820e-01  5.023775e-01
##  [756] -1.326102e-01  5.362152e-01 -2.435598e-01 -1.412999e+00 -2.576156e-01
##  [761] -2.846951e-01 -5.153247e-02 -3.392426e-01  5.416291e-02 -4.489001e-01
##  [766]  3.300671e-01  5.764054e-01  7.561251e-02 -3.856624e-01 -2.324132e-01
##  [771]  6.357919e-01  3.979613e-02  5.729768e-01 -1.481751e-01  5.382490e-01
##  [776]  1.028306e+00 -3.913418e-01  3.331571e-01  7.285590e-01  3.722982e-01
##  [781] -2.024795e-03 -1.478093e-01  6.672507e-01 -6.910356e-01  2.615815e-01
##  [786]  3.766549e-01  5.061339e-02  3.743098e-01 -1.548355e-03 -2.473112e-01
##  [791] -3.919433e-01  1.971016e-01  1.208676e+00  7.713069e-01  4.728458e-01
##  [796]  4.743457e-02  2.649565e-01 -2.569036e-01  6.146040e-01  2.900352e-01
##  [801] -1.307665e+00  1.494007e-01  5.747159e-01  9.037780e-01 -5.205401e-02
##  [806]  2.569018e-01  7.311529e-01  2.024387e-01  1.125400e-01  5.850843e-01
##  [811]  7.629178e-01  2.455431e-01  3.549798e-01  3.048031e-01  2.028849e-01
##  [816] -1.215805e-01  1.329389e-01  2.544330e-01  2.670119e-01  1.109091e-01
##  [821]  2.321788e-01 -1.876371e-01 -1.183860e-01 -2.420258e-01  1.058036e+00
##  [826]  5.505291e-01  9.417577e-02  3.510804e-01 -1.283429e-01 -4.997062e-01
##  [831]  9.878980e-02  6.244527e-01  7.977463e-01 -6.848677e-01 -4.843281e-01
##  [836]  3.319674e-01  4.684448e-01  5.016801e-01  2.306976e-02 -5.153247e-02
##  [841]  4.974297e-01 -2.826514e-01  1.788437e-01 -6.605075e-01  5.768286e-02
##  [846] -5.585486e-01 -4.334723e-02  5.387441e-02  2.747026e-01  6.603575e-02
##  [851]  2.078662e-01  1.366381e-01  4.722952e-01 -4.334723e-02 -8.539225e-01
##  [856] -1.174387e-02  2.220068e+00 -2.654790e-01  4.119313e-01  7.566655e-01
##  [861]  1.802353e+00 -2.135485e-01 -2.147370e-01  2.529758e-01 -7.824584e-01
##  [866]  2.636449e-01  1.717228e-01 -2.245893e-01  2.765426e-01 -2.135917e-02
##  [871] -3.847448e-01 -2.448801e-01  9.072227e-01 -4.341709e-01  2.290196e-01
##  [876]  4.597957e-01 -2.523813e-01 -2.823047e-01  8.550420e-01  3.464469e-01
##  [881]  2.969810e-01 -1.414593e-01  8.639869e-01  4.605516e-01  4.575397e-01
##  [886]  2.364511e-01  8.622463e-02  9.705338e-02  4.138981e-01  6.315830e-01
##  [891] -4.543142e-03  7.697025e-01  6.482304e-01  1.753845e-01  1.101889e-02
##  [896]  1.369583e-01  1.557579e-01 -3.815429e-01  9.815968e-01 -1.065875e-01
##  [901] -5.161483e-02  5.837864e-01 -7.644242e-02  1.634679e-01 -1.012825e-01
##  [906]  6.158271e-01  7.516511e-01  2.741023e-01  1.494433e-01  3.059819e-01
##  [911]  1.193691e+00  3.351345e-01  2.223116e-01 -5.680683e-01 -1.363522e-01
##  [916]  6.668134e-01  4.869924e-01  7.541676e-01 -6.626738e-01 -3.909174e-02
##  [921]  2.455177e-02  1.555423e-01  6.756516e-01  2.599114e-01 -7.098493e-03
##  [926] -2.367744e-01  1.275175e-01  1.210489e+00 -6.324946e-01  4.159965e-02
##  [931] -1.130990e-01  7.771115e-01 -7.644242e-02 -1.206027e-01 -2.149055e-01
##  [936] -5.581946e-01  1.843821e-01 -6.048233e-02  7.417582e-01 -6.174900e-01
##  [941]  6.474352e-01  6.331915e-01  7.150637e-01  2.043148e-01  3.356845e-03
##  [946]  3.923378e-01  3.436978e-01  4.408308e-01  4.817540e-01  4.783383e-02
##  [951]  1.013887e-01 -9.149421e-02 -1.007048e-01  2.774885e-01  1.376284e+00
##  [956]  2.584614e-01  7.491506e-01  1.675112e-02  5.714649e-01  4.681558e-01
##  [961] -3.198136e-01  1.272414e-01  7.503279e-02 -3.375184e-01  2.295065e-01
##  [966] -3.169437e-01 -4.334723e-02 -7.032696e-01  1.225804e+00  3.967988e-01
##  [971] -9.056488e-02  2.074706e-01 -5.566864e-01  2.559227e-01  2.819676e-03
##  [976]  2.170497e-01  3.701931e-01  4.462396e-02 -5.084764e-03  1.053198e+00
##  [981] -3.595366e-01 -5.717604e-01 -1.080016e-01  7.343254e-02 -9.019174e-01
##  [986] -6.936118e-02  9.314005e-01  1.189240e+00 -7.118186e-01  8.208429e-01
##  [991]  7.003882e-02 -7.197771e-01  5.291769e-02  3.745480e-01  8.968233e-01
##  [996]  2.641606e-01  5.540950e-01  3.769101e-01  8.931308e-01 -4.152659e-01
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
##   0.49093968   0.16741999 
##  (0.05294285) (0.03742987)
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
## [1] -0.12323468 -0.29041917 -0.16995911  0.62345290 -0.36876699  0.03647254
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
## [1] -0.0119
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.917142
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
## t1*      4.5 -0.004904905   0.9142635
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 4 5 7 
## 2 1 3 3 1
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
## [1] 0.0139
```

```r
se.boot
```

```
## [1] 0.8828569
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

