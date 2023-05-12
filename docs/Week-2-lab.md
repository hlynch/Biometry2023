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
## 0 1 4 5 6 8 9 
## 2 2 1 1 1 1 2
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
## [1] 0.035
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
## [1] 2.813001
```

```r
UL.boot
```

```
## [1] 6.256999
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.7975 6.2000
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
##    [1] 3.9 3.4 5.0 4.8 2.7 2.8 4.6 4.4 4.8 5.5 4.2 4.5 4.5 5.8 4.1 4.9 3.1 4.0
##   [19] 3.0 5.5 4.6 3.6 3.1 6.6 4.7 6.3 4.2 3.8 5.2 6.1 4.2 3.7 3.9 5.5 5.3 4.2
##   [37] 5.1 4.5 2.9 4.9 3.9 3.2 2.9 3.2 6.3 5.3 5.5 4.7 2.4 4.2 3.7 4.0 2.8 4.3
##   [55] 3.9 5.0 4.6 4.7 4.3 4.6 5.1 4.9 2.6 5.1 3.6 4.4 5.1 5.1 5.2 4.6 4.9 4.1
##   [73] 4.1 3.5 5.0 5.6 3.7 6.1 3.2 5.1 5.4 3.8 5.4 5.3 3.7 4.7 3.7 4.3 5.6 5.5
##   [91] 5.5 4.0 4.5 4.4 5.1 4.2 6.2 4.6 3.6 4.5 4.1 5.7 2.7 3.2 2.9 5.9 4.5 4.9
##  [109] 4.4 6.0 4.4 5.1 2.8 3.9 4.6 5.0 6.1 5.3 3.3 3.1 4.5 4.9 3.8 4.6 5.4 3.3
##  [127] 6.1 5.1 4.0 3.8 3.4 3.9 4.8 2.3 4.8 6.4 3.5 3.8 5.3 4.2 6.5 2.9 4.0 4.7
##  [145] 4.5 3.0 3.0 4.6 3.2 4.3 5.7 3.7 4.8 5.2 5.6 5.2 4.7 3.5 5.1 5.3 4.6 4.6
##  [163] 4.6 5.3 6.1 3.4 4.2 4.4 4.7 6.0 4.4 3.7 3.3 4.5 3.8 3.0 3.7 4.4 5.4 6.2
##  [181] 6.0 3.6 4.9 5.1 4.7 5.3 4.7 4.2 2.6 3.8 3.2 3.2 5.1 3.1 4.9 4.8 4.5 4.5
##  [199] 3.7 4.5 3.9 3.3 3.8 4.7 4.9 4.6 4.0 5.6 4.6 5.6 5.2 3.8 4.5 3.5 5.9 4.2
##  [217] 5.0 5.4 5.3 4.3 4.6 4.9 2.3 5.0 3.6 6.7 3.9 4.1 2.8 4.3 4.3 5.6 2.9 5.3
##  [235] 4.1 3.6 4.5 5.0 4.2 3.8 4.9 4.2 3.9 3.9 5.7 2.9 3.7 5.2 4.1 4.7 3.9 5.6
##  [253] 3.5 4.2 4.6 3.7 4.2 3.9 4.6 3.3 3.5 3.6 5.6 5.4 4.3 3.3 5.3 4.9 5.1 5.2
##  [271] 3.6 4.0 5.0 3.5 5.5 2.0 3.0 2.7 2.9 4.4 4.8 3.0 4.5 4.7 3.4 3.2 5.6 4.9
##  [289] 3.5 5.1 3.3 4.5 5.2 5.6 5.1 5.3 3.8 4.4 5.2 4.9 4.8 4.8 4.9 3.2 3.9 4.8
##  [307] 2.4 4.0 4.1 5.0 5.3 3.5 5.2 3.3 3.7 5.1 3.6 5.5 5.8 4.2 4.4 4.8 4.8 3.7
##  [325] 3.8 5.3 3.8 6.0 5.6 4.1 5.9 5.6 4.6 4.2 4.2 4.5 4.6 3.3 4.4 5.7 5.5 4.2
##  [343] 4.0 4.0 4.9 5.4 4.5 4.5 5.9 5.1 3.2 4.0 3.8 5.0 5.7 5.0 4.9 4.3 5.5 3.3
##  [361] 3.7 5.3 6.8 3.8 4.1 2.5 4.2 3.9 5.3 3.4 3.8 3.8 5.4 3.2 4.9 5.8 5.0 5.0
##  [379] 3.5 4.9 3.8 4.3 4.5 3.1 5.0 4.5 3.4 6.0 4.3 3.9 4.6 3.4 3.3 5.6 5.0 2.7
##  [397] 4.7 4.7 4.1 4.3 4.3 4.6 3.5 5.1 5.7 4.0 5.3 3.1 4.4 6.3 4.5 4.0 4.4 4.3
##  [415] 5.8 5.5 4.9 5.5 5.1 6.0 5.9 5.2 3.7 5.6 5.0 4.4 3.4 5.3 4.9 4.6 2.9 5.7
##  [433] 5.8 3.2 3.7 3.2 4.7 3.5 3.0 4.7 4.9 4.7 5.3 4.8 4.9 5.5 3.0 5.6 4.2 3.3
##  [451] 4.1 3.6 5.5 4.7 3.0 3.2 3.0 4.0 5.2 5.6 5.1 3.2 4.7 5.3 5.6 4.0 4.2 4.5
##  [469] 4.2 4.1 5.0 4.7 4.6 4.2 3.6 3.3 5.8 5.0 4.9 5.2 5.3 4.5 3.6 4.5 3.1 4.1
##  [487] 4.1 4.7 3.8 5.0 4.4 4.6 4.0 3.4 6.6 4.6 5.0 3.7 3.4 3.9 5.6 5.5 5.7 6.5
##  [505] 5.5 5.5 4.1 4.9 4.4 2.3 3.7 5.5 6.0 6.3 4.0 5.7 4.8 5.2 4.3 3.8 4.2 4.1
##  [523] 4.4 5.0 6.3 5.8 5.3 4.1 4.5 5.0 4.1 5.5 3.9 4.1 6.5 5.9 3.4 3.3 5.6 5.2
##  [541] 5.5 5.6 5.1 4.6 4.4 5.2 5.1 4.0 4.9 4.3 5.1 5.3 5.0 4.0 3.8 5.3 3.7 3.9
##  [559] 6.0 5.3 3.5 5.2 3.6 4.9 4.0 4.5 3.8 4.5 3.1 4.0 4.3 4.1 4.0 3.5 4.3 3.7
##  [577] 3.1 3.6 4.5 5.3 3.7 3.6 5.8 3.5 5.0 4.8 3.3 5.2 4.8 4.1 4.1 3.9 4.4 4.5
##  [595] 5.2 4.7 2.4 3.8 4.5 4.6 4.2 4.9 4.0 3.9 4.9 5.4 3.9 4.4 5.2 5.6 5.2 3.4
##  [613] 5.5 6.8 4.5 4.3 5.6 4.7 4.3 5.0 4.4 4.1 3.9 4.6 4.8 3.3 4.3 3.4 4.0 6.2
##  [631] 3.3 4.5 3.0 5.0 6.4 4.2 5.3 2.9 6.4 5.7 4.4 4.2 4.3 2.2 3.7 3.2 3.4 4.6
##  [649] 5.2 5.4 4.7 4.5 5.0 4.9 4.3 4.2 3.6 6.1 4.7 4.6 5.5 4.5 4.5 4.2 4.5 5.1
##  [667] 4.3 6.9 5.8 4.9 5.8 4.1 5.1 4.3 5.8 3.7 4.1 4.9 5.5 6.0 6.0 4.0 4.6 5.2
##  [685] 4.5 3.9 4.4 6.4 4.1 4.7 4.1 4.7 5.2 3.9 4.0 3.8 4.8 4.9 3.9 4.4 4.6 2.2
##  [703] 4.2 4.6 3.9 2.7 4.8 4.5 4.3 3.9 2.8 5.1 6.3 4.6 3.9 4.1 3.4 3.9 3.2 3.0
##  [721] 3.7 4.7 2.8 5.0 3.4 4.3 4.4 4.1 3.8 3.6 5.7 3.7 5.2 4.1 4.5 5.6 4.3 4.8
##  [739] 5.3 4.2 5.9 3.8 4.2 4.0 5.6 4.5 5.2 5.5 4.8 4.3 5.4 3.8 3.0 4.4 5.5 5.2
##  [757] 4.5 3.7 3.2 5.0 3.8 3.7 3.9 5.0 3.5 4.9 4.9 5.6 3.1 3.8 4.7 4.1 4.7 4.0
##  [775] 5.5 4.9 5.2 6.3 6.6 5.1 4.3 4.1 3.9 4.7 4.5 3.6 3.2 5.6 6.8 5.6 5.8 3.7
##  [793] 5.4 3.4 4.9 3.0 4.9 4.2 3.6 5.3 5.1 3.9 5.3 6.4 5.7 4.2 4.7 4.6 2.9 3.7
##  [811] 3.8 5.4 4.5 3.0 4.4 4.9 4.1 3.4 4.8 3.3 5.4 4.5 5.3 3.9 5.2 3.7 4.6 4.8
##  [829] 4.5 4.1 5.2 5.8 5.4 3.7 3.5 4.2 3.9 4.4 4.5 4.5 5.3 4.0 3.9 3.4 4.2 4.5
##  [847] 5.2 3.9 4.1 3.4 5.0 4.0 4.8 3.9 5.1 5.9 4.6 6.1 4.9 5.4 3.4 4.4 5.4 4.2
##  [865] 4.9 3.6 4.7 4.1 2.7 4.0 3.9 4.1 4.3 3.4 4.7 5.5 3.5 3.9 4.2 4.1 5.5 4.3
##  [883] 2.8 3.4 4.3 5.1 4.2 3.9 5.2 4.1 5.8 5.4 4.7 3.9 4.2 2.8 6.3 5.1 4.2 2.8
##  [901] 5.5 5.0 5.3 2.8 4.6 3.7 5.2 6.2 5.5 4.9 3.7 5.2 4.6 4.8 5.4 3.4 5.3 5.4
##  [919] 4.4 4.8 5.0 4.1 4.8 3.8 3.6 4.5 4.2 3.5 3.3 4.5 3.8 3.7 4.6 4.6 3.9 4.9
##  [937] 4.7 5.3 5.1 3.7 4.9 5.3 3.2 5.2 4.1 5.2 6.5 4.8 3.4 4.0 3.6 5.0 4.5 3.8
##  [955] 5.6 4.1 5.2 6.9 6.8 4.3 5.5 3.6 5.6 4.9 4.9 5.3 3.8 4.6 3.8 4.4 4.2 4.4
##  [973] 6.8 4.7 5.2 5.7 4.4 3.8 2.4 7.2 4.9 2.9 4.9 5.6 4.7 5.0 4.4 5.1 5.4 3.5
##  [991] 5.6 4.5 3.4 4.4 5.9 4.1 5.6 5.0 3.6 3.5
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
##   2.8   6.3
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
##    [1] 4.9 4.6 4.3 7.0 3.1 5.7 4.9 3.9 4.6 2.7 4.9 5.3 4.8 4.8 2.1 4.9 5.0 3.8
##   [19] 4.8 6.7 4.8 4.2 4.9 4.7 4.9 4.5 6.3 4.4 3.8 4.4 3.5 5.8 2.9 6.2 5.3 4.0
##   [37] 3.4 4.3 4.2 3.1 5.5 5.4 5.5 4.4 4.0 4.7 5.1 3.7 3.7 5.7 5.1 4.8 5.2 3.6
##   [55] 3.9 4.3 3.5 4.3 5.1 5.0 4.6 4.8 4.6 4.2 5.4 2.9 3.8 2.7 4.0 4.0 4.7 3.1
##   [73] 3.0 5.4 4.7 4.6 4.2 3.7 6.8 4.0 4.8 5.2 4.4 3.9 4.4 3.5 5.2 2.7 5.6 4.6
##   [91] 4.2 5.1 4.2 5.4 4.0 4.8 5.6 6.7 3.6 4.9 6.7 5.0 4.0 3.8 3.6 4.8 4.7 4.5
##  [109] 3.6 4.2 3.5 4.5 6.0 4.1 5.2 4.8 4.9 4.7 3.4 3.0 4.7 4.3 4.8 3.8 5.4 4.6
##  [127] 4.4 4.0 5.6 5.4 3.7 4.8 4.9 3.9 3.8 4.9 4.1 5.5 5.3 3.4 5.1 6.5 5.2 4.7
##  [145] 4.3 5.6 2.2 2.4 4.8 5.1 7.2 3.9 5.5 6.9 3.1 3.2 4.4 4.4 4.4 4.3 4.6 5.9
##  [163] 3.8 4.8 3.8 3.9 2.5 5.4 4.5 4.5 3.2 3.4 4.8 3.9 3.7 5.3 5.6 2.7 3.9 4.4
##  [181] 4.7 4.7 5.7 3.9 5.4 4.5 3.2 4.7 5.1 3.9 3.6 2.8 4.5 4.1 5.6 4.3 3.7 5.1
##  [199] 4.5 4.9 5.6 4.7 6.7 4.5 2.8 2.6 2.9 3.2 5.1 5.5 2.1 2.9 4.0 3.6 5.5 5.9
##  [217] 4.3 4.0 5.7 4.5 4.4 5.4 6.2 2.7 4.3 4.9 5.0 2.8 5.5 3.7 4.2 6.5 3.1 3.9
##  [235] 5.0 5.1 5.1 4.1 5.2 4.3 5.6 3.1 6.1 4.0 3.8 5.0 4.0 5.0 4.4 3.8 4.2 3.4
##  [253] 3.2 5.7 4.8 4.8 2.6 5.3 3.6 6.7 5.8 4.9 4.6 5.6 2.6 4.2 3.8 6.0 4.5 5.2
##  [271] 4.0 5.7 4.4 4.6 4.8 5.6 5.0 4.9 5.2 4.9 4.9 5.8 5.3 4.5 4.7 4.1 4.3 4.1
##  [289] 3.5 5.3 3.9 4.4 3.6 6.2 6.1 5.1 5.2 5.9 2.6 5.8 5.2 4.9 5.7 3.8 2.7 4.4
##  [307] 4.7 4.6 5.9 3.3 4.2 3.8 3.4 5.0 5.8 4.0 5.3 4.3 4.3 4.0 4.2 3.2 5.1 3.1
##  [325] 4.8 4.0 6.0 4.7 3.4 3.6 4.0 3.9 4.0 5.2 5.9 4.5 4.3 3.0 4.6 4.0 4.4 3.9
##  [343] 5.8 5.6 3.3 5.3 5.0 4.3 3.2 4.5 4.1 4.3 3.9 5.5 5.6 4.5 4.8 5.7 4.2 2.3
##  [361] 4.9 4.8 5.8 2.9 4.2 3.2 4.5 5.1 4.3 4.0 4.6 3.3 5.9 4.5 3.4 4.4 4.4 4.9
##  [379] 3.5 4.4 4.9 5.4 4.0 3.9 3.1 5.4 4.9 3.9 3.7 4.2 2.9 4.2 3.0 4.9 5.5 3.8
##  [397] 2.8 3.8 4.5 3.8 1.9 3.6 3.5 4.4 3.8 3.6 4.2 4.8 3.8 5.1 4.8 5.0 5.1 5.6
##  [415] 5.9 4.8 4.3 4.2 5.9 4.2 5.8 4.2 2.2 5.3 5.2 5.0 3.9 6.6 3.2 2.8 5.0 4.2
##  [433] 4.8 5.7 4.1 4.5 4.8 3.7 2.8 5.5 3.9 3.5 4.1 3.0 4.3 4.9 3.8 5.0 4.9 7.0
##  [451] 4.5 5.8 4.0 4.8 5.4 5.3 5.2 3.2 4.7 3.3 6.3 4.4 5.6 4.0 2.7 4.0 4.4 5.5
##  [469] 5.8 4.9 4.4 5.1 5.0 4.9 4.7 5.4 4.3 4.2 2.3 3.7 2.6 4.6 3.7 5.1 4.4 4.8
##  [487] 5.0 1.8 3.8 4.6 6.1 4.9 3.2 4.6 5.4 4.2 5.1 4.7 4.7 4.3 3.7 5.1 3.7 3.0
##  [505] 3.4 3.3 4.3 4.7 3.6 4.2 5.5 4.2 5.1 4.4 3.8 5.1 5.7 4.7 5.3 4.0 4.2 6.1
##  [523] 4.4 6.2 3.5 3.7 3.6 4.2 4.7 3.3 4.2 4.8 6.0 3.7 6.0 5.1 4.4 3.7 4.3 6.3
##  [541] 5.2 4.5 6.1 4.7 3.9 4.3 4.4 5.2 4.2 3.5 6.4 4.1 2.8 3.7 3.7 4.0 4.7 3.7
##  [559] 4.2 6.1 5.0 3.7 4.0 5.2 3.2 4.9 6.1 4.4 5.0 5.8 5.5 5.8 3.2 3.8 3.6 5.0
##  [577] 5.2 5.3 5.2 5.0 5.4 4.8 3.7 4.5 3.9 5.9 4.8 4.8 5.2 5.1 4.2 3.5 3.0 4.1
##  [595] 3.1 4.3 3.6 4.1 2.9 3.7 5.4 4.9 5.9 4.7 3.7 4.5 4.6 3.9 5.1 3.2 4.2 5.7
##  [613] 3.5 5.8 4.0 3.6 3.9 4.9 3.6 5.1 4.8 4.1 5.3 3.4 3.8 4.5 4.7 3.9 4.9 3.5
##  [631] 4.0 4.5 3.1 6.3 4.9 5.4 4.1 4.5 5.1 6.1 5.0 4.4 5.2 4.7 3.8 4.7 3.5 5.9
##  [649] 5.1 4.8 5.9 4.2 4.1 4.8 4.2 4.1 4.9 4.9 4.4 4.0 3.2 5.3 5.7 3.3 4.6 3.6
##  [667] 3.6 3.0 3.8 4.2 6.2 3.3 5.2 2.7 5.5 5.5 5.2 5.4 3.4 4.6 5.9 4.4 6.4 3.7
##  [685] 4.7 5.0 5.3 3.9 4.3 5.1 5.2 4.6 5.7 3.6 4.1 4.2 3.1 4.4 4.9 4.5 6.4 3.0
##  [703] 5.2 3.5 4.7 4.7 3.4 3.6 3.5 3.4 5.2 4.3 5.6 4.4 4.8 4.1 3.6 4.4 4.4 4.7
##  [721] 4.0 4.6 3.9 7.0 5.6 5.0 4.6 6.1 4.9 4.9 3.2 2.9 5.9 3.9 6.0 4.9 3.6 3.9
##  [739] 4.3 3.1 4.1 5.7 2.4 6.4 4.6 4.8 4.2 3.7 5.9 4.0 3.6 3.5 3.7 4.2 4.3 2.1
##  [757] 3.1 4.4 3.9 5.4 5.3 4.3 3.0 4.4 3.6 4.1 3.9 5.3 4.6 4.5 4.6 4.3 3.7 5.6
##  [775] 5.3 4.0 5.2 6.0 4.6 4.5 5.3 5.7 4.7 4.5 6.2 3.8 3.6 4.7 4.8 3.8 4.7 3.6
##  [793] 6.0 4.3 2.6 3.9 4.5 5.0 4.9 5.5 1.9 5.4 4.7 5.7 4.0 4.7 3.4 4.3 4.5 4.8
##  [811] 3.8 4.6 4.1 5.0 6.7 3.0 4.0 4.7 4.5 5.1 3.7 3.9 3.3 3.6 4.4 4.6 5.8 5.4
##  [829] 4.9 5.3 5.6 4.8 4.9 5.0 4.9 4.0 4.4 5.3 4.5 4.5 4.7 3.8 5.9 4.5 5.3 5.6
##  [847] 3.1 5.2 4.1 2.7 5.2 5.6 4.7 4.8 4.4 3.8 5.9 3.9 3.5 4.8 4.6 4.3 5.6 3.9
##  [865] 6.0 5.4 3.4 4.3 3.7 2.9 4.7 4.2 4.4 3.6 6.3 5.1 2.5 5.5 4.4 2.8 4.9 4.7
##  [883] 5.8 4.4 5.6 4.6 5.4 5.1 6.2 5.4 5.0 5.3 5.4 3.4 5.9 4.7 3.1 2.5 5.7 4.2
##  [901] 3.4 5.3 5.2 3.8 3.1 3.3 4.1 5.6 4.0 4.0 2.6 3.4 3.1 4.7 4.7 5.3 5.2 3.6
##  [919] 3.7 3.3 4.9 3.8 4.2 2.0 4.8 5.1 5.7 6.2 3.8 4.3 5.7 5.9 2.5 5.4 2.2 5.1
##  [937] 4.2 5.3 3.9 3.4 4.5 5.1 5.4 3.5 3.9 6.4 5.1 4.4 4.1 3.6 4.7 5.7 4.0 4.6
##  [955] 5.7 2.9 3.3 4.6 5.6 3.9 4.2 5.6 4.3 3.4 3.9 6.1 4.9 2.7 5.0 3.6 5.1 3.8
##  [973] 4.5 5.0 5.0 4.6 5.9 3.9 4.4 3.9 4.3 3.1 4.6 3.2 3.9 2.8 4.1 4.0 4.1 4.1
##  [991] 5.4 3.8 5.2 3.7 4.3 4.1 3.7 3.4 4.7 4.7
## 
## $func.thetastar
## [1] -0.0216
## 
## $jack.boot.val
##  [1]  0.48595989  0.40760870  0.26051873  0.16713092 -0.01307190 -0.08196721
##  [7] -0.24022989 -0.30533708 -0.37078652 -0.52500000
## 
## $jack.boot.se
## [1] 0.9787602
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
##    [1] 4.6 5.4 4.1 3.4 4.3 3.9 4.5 3.3 5.5 3.2 4.0 5.7 4.5 4.9 4.6 3.9 5.2 4.7
##   [19] 3.0 3.9 5.2 2.6 6.0 4.7 5.9 5.2 4.8 4.7 5.5 3.4 4.4 2.7 4.8 4.8 5.5 3.6
##   [37] 5.4 4.8 5.3 6.0 3.1 4.9 4.1 5.5 4.1 4.5 5.8 5.1 4.2 3.4 4.7 4.7 2.8 3.7
##   [55] 6.9 3.7 5.4 6.3 4.3 6.0 4.3 4.4 5.6 2.6 5.3 4.0 4.7 4.9 4.3 3.1 5.4 5.9
##   [73] 5.1 4.1 4.8 3.2 5.0 3.8 5.0 4.0 3.6 4.7 4.1 4.9 4.4 3.3 3.6 5.7 3.6 4.7
##   [91] 4.6 4.9 4.7 4.4 5.4 4.8 3.9 2.8 4.3 4.4 4.1 3.9 5.7 4.6 3.8 5.6 4.2 4.3
##  [109] 4.5 4.2 4.9 4.1 4.5 5.1 4.4 3.5 4.4 3.4 2.8 4.6 5.7 4.5 4.5 3.7 4.0 4.4
##  [127] 5.7 5.0 5.3 5.0 6.3 5.2 4.7 4.2 2.0 3.1 4.2 5.7 3.7 3.0 5.0 4.9 5.8 5.2
##  [145] 5.0 4.5 4.7 5.4 4.9 4.8 4.2 4.4 4.2 3.9 6.9 5.1 4.8 5.9 4.4 3.9 4.1 3.7
##  [163] 4.6 4.9 4.4 3.7 6.0 3.2 5.3 2.9 6.0 6.3 5.8 3.8 4.0 4.4 4.6 2.6 4.6 5.1
##  [181] 5.3 5.3 4.8 5.3 4.9 4.3 4.3 5.9 4.6 3.9 3.5 5.3 4.3 5.0 4.2 4.5 4.6 3.0
##  [199] 5.6 6.0 4.5 4.2 4.5 4.2 4.1 4.4 5.1 4.1 3.1 6.0 4.6 4.0 3.5 4.2 4.5 3.8
##  [217] 5.1 4.1 3.5 4.6 5.2 4.7 3.2 5.3 4.0 5.0 4.8 4.2 3.5 3.7 5.8 4.1 4.8 2.7
##  [235] 5.2 4.3 4.9 5.1 3.8 3.8 3.5 3.5 4.2 2.9 3.4 3.8 5.5 4.2 4.0 4.7 4.5 5.2
##  [253] 4.5 5.8 5.4 4.9 4.3 4.3 3.5 4.3 5.4 3.8 4.6 5.1 4.7 4.1 3.8 5.2 3.4 4.4
##  [271] 5.4 3.5 5.2 5.5 4.4 4.7 3.9 5.8 4.2 4.5 3.5 5.4 3.6 5.3 4.3 4.3 4.1 5.5
##  [289] 4.6 4.1 4.9 5.5 3.7 4.1 5.1 2.8 4.7 4.0 5.9 5.3 6.2 3.7 5.5 5.7 4.6 5.2
##  [307] 4.6 4.5 4.5 3.3 3.2 3.9 6.4 6.1 4.7 5.1 5.2 4.9 5.8 3.4 4.8 4.8 2.0 5.8
##  [325] 5.3 4.8 5.6 5.7 4.3 4.1 3.6 4.5 3.9 3.5 3.1 4.4 4.9 5.8 4.5 4.0 4.1 5.1
##  [343] 3.5 5.6 4.4 4.0 3.2 6.1 5.5 5.0 4.3 4.2 5.4 4.5 5.1 3.2 5.0 4.0 4.7 3.5
##  [361] 4.5 5.5 5.5 4.0 5.6 5.2 5.2 3.5 4.3 3.7 5.6 4.6 4.2 4.8 5.3 1.9 4.7 2.9
##  [379] 4.6 3.5 5.5 2.8 4.9 4.4 5.5 4.2 5.4 4.4 5.0 4.6 6.0 4.2 3.7 4.5 4.2 4.0
##  [397] 5.0 4.5 5.1 3.8 5.1 5.6 3.5 4.3 2.5 5.3 5.3 4.7 5.1 5.4 3.9 5.1 4.4 5.7
##  [415] 5.3 5.8 3.6 6.1 4.1 5.3 4.3 6.4 2.9 5.0 5.3 5.6 3.9 3.4 4.4 5.2 4.9 5.0
##  [433] 6.4 5.1 4.0 5.3 4.5 4.6 4.2 4.1 4.9 5.0 3.9 3.1 4.5 5.2 5.2 5.9 4.8 3.3
##  [451] 5.0 5.2 3.0 3.2 4.3 4.7 4.9 5.0 4.1 4.3 6.7 4.2 3.1 4.4 6.3 4.6 4.8 4.3
##  [469] 5.5 4.7 3.9 4.6 4.4 3.3 5.4 4.3 3.1 4.7 4.5 4.1 4.3 5.9 3.1 3.8 5.0 4.1
##  [487] 6.0 5.3 5.2 4.8 3.5 4.7 3.6 1.7 3.1 4.9 4.2 6.2 5.3 4.2 5.5 4.3 3.6 3.9
##  [505] 4.5 2.8 4.7 4.9 6.2 5.0 2.9 3.9 5.2 3.5 3.0 4.2 3.8 4.2 5.6 2.9 5.3 2.6
##  [523] 3.5 4.0 5.8 4.4 3.4 4.0 3.4 5.3 3.7 3.9 5.0 4.2 5.2 5.4 5.2 3.8 4.1 3.5
##  [541] 5.0 4.0 5.3 4.9 3.7 3.5 4.7 3.8 4.0 4.0 4.8 4.8 2.6 4.4 3.9 4.9 4.5 4.3
##  [559] 6.2 3.8 5.9 4.9 3.3 3.4 4.8 4.0 5.2 6.5 3.3 5.6 4.2 3.1 5.2 3.8 5.4 5.5
##  [577] 4.3 5.0 2.7 4.2 4.5 4.9 4.4 4.1 4.9 4.7 4.0 4.0 5.1 6.0 5.4 5.5 4.6 4.6
##  [595] 4.5 4.2 5.5 3.2 4.7 4.5 4.0 3.4 5.0 4.0 4.2 5.1 4.7 4.8 4.0 4.2 4.8 3.7
##  [613] 5.9 4.7 5.0 4.0 4.9 5.3 3.7 4.6 5.9 4.8 4.9 4.0 3.9 3.9 5.4 4.0 4.7 5.8
##  [631] 4.4 3.9 5.3 3.6 5.1 5.9 3.6 2.3 3.8 6.1 4.5 5.2 3.7 2.7 5.2 3.2 3.9 5.2
##  [649] 3.5 5.2 5.6 3.8 5.3 4.9 4.7 5.5 3.1 5.1 4.8 4.8 3.5 5.5 4.3 2.4 5.0 4.3
##  [667] 4.9 4.4 3.8 6.1 5.2 4.9 5.0 6.0 4.2 4.9 5.0 3.9 5.2 3.8 5.6 4.9 5.2 3.9
##  [685] 3.8 4.7 5.4 6.3 5.1 4.6 5.0 4.0 3.4 4.9 2.7 4.1 4.4 6.3 5.6 3.0 6.1 4.1
##  [703] 4.5 4.9 4.9 3.5 3.0 4.5 4.5 4.4 5.4 5.2 4.5 4.4 5.5 3.5 4.7 3.6 2.7 5.8
##  [721] 5.9 4.3 4.5 3.9 5.7 3.3 6.2 4.9 6.1 5.2 4.0 5.6 5.7 4.3 4.4 5.3 4.1 5.1
##  [739] 5.5 4.0 3.3 5.4 5.2 4.8 4.0 5.3 4.1 4.0 5.4 4.2 4.5 5.1 4.0 4.9 5.6 4.1
##  [757] 3.4 5.1 4.4 4.1 4.5 4.7 5.1 5.9 4.8 6.1 4.9 4.4 4.3 5.7 4.8 4.3 4.2 4.1
##  [775] 3.5 3.0 3.3 5.8 3.5 5.0 3.4 3.5 4.1 4.3 5.0 5.4 6.2 3.7 5.1 4.0 3.9 5.3
##  [793] 6.0 3.6 3.8 3.2 4.3 4.3 4.5 3.6 4.8 6.7 5.3 3.0 4.9 4.8 4.6 3.1 4.1 3.5
##  [811] 6.3 4.0 4.0 5.0 5.0 4.3 4.0 6.2 4.1 5.0 4.5 6.0 4.5 3.4 4.0 3.3 3.8 3.5
##  [829] 3.7 4.2 3.8 4.2 4.4 3.9 4.6 4.7 4.6 4.9 5.1 4.8 4.0 3.3 5.5 5.0 7.3 5.1
##  [847] 4.6 3.5 3.9 4.9 3.1 5.1 4.2 4.9 5.6 4.4 4.0 2.7 3.1 3.5 4.0 5.0 3.4 4.5
##  [865] 4.7 4.1 3.5 6.1 2.3 4.8 4.4 4.9 3.6 3.5 4.3 5.7 4.2 2.0 4.6 3.6 3.6 4.7
##  [883] 4.8 4.7 4.1 4.3 5.8 5.2 4.6 2.9 5.0 5.9 4.5 5.2 5.9 4.3 4.3 5.0 5.0 4.2
##  [901] 2.8 3.3 4.2 4.8 2.6 3.6 4.5 4.0 4.4 3.1 5.1 5.1 3.6 4.4 5.0 3.6 4.3 2.7
##  [919] 2.3 6.1 3.5 5.2 4.3 5.7 5.1 2.8 5.3 4.9 3.5 4.7 3.8 4.2 4.5 4.2 5.0 3.6
##  [937] 4.7 4.2 4.7 5.1 3.7 4.2 4.6 4.4 3.7 5.5 5.4 4.2 5.4 4.0 3.7 6.2 4.5 4.3
##  [955] 4.4 4.6 5.6 3.0 4.2 6.1 4.5 4.0 4.5 3.1 4.5 3.6 3.9 5.2 4.9 4.2 5.3 4.5
##  [973] 6.5 4.0 4.5 3.0 2.6 4.4 5.4 5.3 3.4 5.8 4.5 4.5 4.1 3.3 3.5 5.6 4.5 5.5
##  [991] 4.2 5.9 5.4 4.2 3.6 5.4 3.1 4.0 3.8 4.0
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.4 5.4 5.3 5.2 5.0 5.0 5.0 4.6 4.7 4.5
## 
## $jack.boot.se
## [1] 0.9241753
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
## [1] 0.4228745
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
##    4.309381   10.588592 
##  ( 1.857433) ( 4.840552)
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
## [1]  0.3463835  0.7889221  0.1798829  0.2607128 -0.6540522  1.6569255
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
##    [1] -0.013568111  0.037763226 -0.525898242  0.371115919  0.242281508
##    [6]  1.122819073  0.331243200  1.602655966  0.411054195  0.447113439
##   [11] -1.058535484  0.874880154 -0.070463365  0.181244812  0.490757889
##   [16]  0.211749845  0.608269098  0.386248510 -0.434161936  0.675196610
##   [21] -0.177810319  0.756098275  0.524132558 -0.567075419  0.672472105
##   [26]  0.823022358  1.110890683  0.612582528  0.599249371  2.447241237
##   [31]  1.752505392  1.198209319  0.313720831 -0.181842307 -0.621006980
##   [36]  0.226617632  1.256022110  1.093717780 -0.152899676 -0.183052736
##   [41]  0.134663440  0.504382019 -0.218033171  0.400465401 -0.539391797
##   [46]  0.445946893  1.409248112  0.769606362  0.611886680  0.291282099
##   [51]  0.771774421  0.026239195  0.655764341  1.152689197  0.331562085
##   [56]  0.636818414 -0.381072246  0.544840491  0.838396245  1.126787600
##   [61]  1.292361703  1.706787351 -0.665109323  0.968357663  1.137092773
##   [66] -0.285768950 -0.825656160 -0.147043473 -0.303033930  0.419602959
##   [71] -0.832650159  0.831725902  0.357384666  1.337522903  0.633546656
##   [76]  0.571876110  0.021818626 -0.189449429  1.114154257 -0.161675085
##   [81]  0.431382871  0.613308322  0.606120393 -1.000705036  0.541160123
##   [86]  0.612262508  0.365617918  0.194134066 -0.382763852 -0.870669732
##   [91]  1.021908473  0.490718714  0.425075819  1.128178865 -0.473444489
##   [96]  0.031251649 -0.145040606  0.433832060  0.649357766  0.070254612
##  [101] -0.152065558  1.720328520 -0.404407146  1.446568724  1.680404061
##  [106]  0.235032870 -0.897500291  0.104685591  1.676857353  0.433850989
##  [111] -0.152143150 -0.176789178 -0.337311308  0.817219841  0.602799857
##  [116]  0.660897108  0.892675101  0.598598380  0.675594033 -0.374734344
##  [121]  0.389454697  0.172367441  1.456144504  0.894886281  0.845205528
##  [126]  0.591067788  0.859997878  0.647789040  0.226022434  1.083196419
##  [131]  0.010368914  0.313958726  0.005888577 -0.004805707 -0.836802185
##  [136]  0.270319676  1.166417680  0.709618807 -0.451606339  1.069501852
##  [141]  0.156305424  0.409737188  1.025990725  1.678184768  0.103689058
##  [146]  0.503603197 -0.322872503  0.462067679  0.713824258 -0.021999907
##  [151]  1.722112911  0.632898464  0.957015795  0.733558037  0.203547130
##  [156] -0.133220454 -0.027160184  1.009369109  0.661149935 -0.349472668
##  [161]  0.613018698  0.666509042  0.257363510  1.419915110  0.838599404
##  [166]  0.773477318  0.072140445 -0.297618886  1.392723362  0.236493432
##  [171]  0.393700858  0.495689762 -0.004257845  0.836543526  1.116788874
##  [176]  0.027219227 -0.174525262  1.023501257  0.282239861 -0.178453583
##  [181]  0.638202597  0.581565084 -0.082587440  0.381260957 -0.064545360
##  [186]  0.514488342  0.220126145  0.705832274  1.106337773  1.845136146
##  [191]  0.330421234 -0.167768968 -0.109157490 -2.130558016  0.331024735
##  [196]  0.002323897  0.476183581  0.661149935  1.311249037  0.836674347
##  [201]  0.017232204  1.232849549 -0.177357689  1.332579540 -0.213900208
##  [206]  0.939788796 -0.237242879  0.559595668  1.186234741  0.004292722
##  [211]  0.580612056  0.845174693 -0.214703822 -0.456495281 -0.381387612
##  [216]  0.995353949 -0.364932295  1.158854765  0.312897344  0.426505306
##  [221]  0.809868082  0.660006335  0.482869580  1.318289094 -0.216315355
##  [226] -0.237896786 -0.603632732  1.272948457  1.207221319  1.229085689
##  [231]  0.432897782  1.203576339  0.557232914  0.960005781  0.250862979
##  [236]  0.337370352  1.353623024  0.189647220  0.408073509  1.033859837
##  [241]  0.601231605  0.426998567  0.195218283  0.607520625 -0.310339952
##  [246]  0.318973831 -0.751497930  0.275977263  1.237029268  0.016142091
##  [251]  1.367070696  0.496152287  0.623030902  0.590016771  0.378448203
##  [256]  0.688255798 -0.135076323  0.343732914  0.330021266 -0.132722987
##  [261]  0.771134983  0.261023855  0.364340026  0.395120538  0.858775783
##  [266]  1.403590747  0.439110075  1.133331981 -0.421011586  0.614409217
##  [271] -0.401450092  0.667930884  0.389815404  0.302355965  0.720386185
##  [276] -0.079197546 -0.180295913 -0.592160072 -0.308231299 -0.312676728
##  [281]  0.427175392  1.227876654  0.694455355  0.390000589  0.070918993
##  [286]  0.201955088  0.888399387 -0.022568817  0.719613806 -0.025074744
##  [291]  0.022141469  1.608769367  0.651020958  2.489717102  0.871827638
##  [296]  0.273259956  0.088052903  0.553976211  0.883927414  0.947990155
##  [301]  1.364352736  0.437559774  0.208748043  0.676457783  0.674085945
##  [306]  0.015531773  1.775601996 -0.211347275  1.258317672  0.431259220
##  [311]  0.707015594  0.723970651  0.804088779  0.155302172  0.871192866
##  [316]  0.765742192  1.228619761  1.163309344  0.755376423  0.264058053
##  [321]  1.057741895  0.176792074  0.166407967 -0.327749884  1.776765554
##  [326]  0.410104583  0.818625433  0.408673587 -0.620245876  0.594116309
##  [331]  1.028595631  2.258994167  0.511165254 -0.221187968  0.515866508
##  [336]  0.218321766  0.403432541 -0.499349045 -0.562381708  0.632945088
##  [341]  0.748537998  0.328162722  0.424634608  0.053855699  0.100145075
##  [346]  0.217131589  0.426491214  1.398836180  0.406165145  0.069774543
##  [351]  1.107980282  1.450755697  1.858714090  0.624907542  1.321192746
##  [356] -0.237896786  0.385583742  0.369129739  0.588987452  1.504695057
##  [361]  0.199759380  0.904642301  0.145758327  0.916442480  0.029088024
##  [366]  0.551671219  1.052510740  0.335450439  0.762760579  0.837404425
##  [371]  0.583069504  1.075939794  0.864380126  0.872165039 -0.051914541
##  [376]  0.614874805  0.529810138  0.584878158  0.369475021  0.245977630
##  [381]  0.912991936  0.856597854  1.756842785  0.264979006  1.232189696
##  [386]  0.824063184  1.238250612 -0.377167713  0.921592002  2.079810129
##  [391]  0.038062055  0.117848548  0.524194476  1.265288916  1.205329045
##  [396]  0.290286628  1.116091667 -0.042044028  0.083590269 -0.406602125
##  [401]  0.357659140  1.594994227  1.604612325  0.201612089 -0.009258570
##  [406]  1.106138734  0.054945955  0.137664546  0.387218425  0.322838244
##  [411]  0.375124826  1.238404975 -0.390803131 -0.181278397  0.989894698
##  [416]  0.128034353  0.383674430  0.619003512  0.416348572  0.416835955
##  [421]  0.833979518  1.467097020  0.497358101  0.628658765  1.294329169
##  [426] -0.293295813  0.808585729  0.476586343 -0.187295454  0.650340624
##  [431]  1.101535443  1.025976246  0.410840210  0.692654830 -0.372461147
##  [436]  0.801537767  0.034355759 -0.382093904  0.591346086  0.047323910
##  [441]  1.080832563  0.257448231  0.496363989  0.216769490  0.182990485
##  [446]  1.013080737 -0.121399718  1.030064408 -0.393802728  0.004408212
##  [451]  1.711535129  0.205548822  0.306813678  0.017741941  0.422874472
##  [456]  0.245651330  1.162872355  0.722324920  0.691920641  0.233556366
##  [461]  0.481393231  2.017452724  0.251789146 -0.188230174  0.196827484
##  [466]  0.637640360  0.833509479 -0.013289351  1.475432087  0.851025906
##  [471] -0.021422149  0.653243116  0.750842406  1.819036303  0.123354584
##  [476]  0.162699636  0.816664380  1.054081931 -1.107367108  0.652075457
##  [481]  0.199759380  0.396463033  0.367395497  0.244340124  0.399787223
##  [486]  0.510355970  0.866465126 -0.189213764  0.830662590  0.700719174
##  [491]  0.919898710  0.190916024  0.361497588  0.694633396  1.334030941
##  [496] -0.365213608  0.241643876  0.917638284  0.818507487  0.001723177
##  [501]  0.832594417  0.253962811  0.298201792  0.891718273 -0.632496894
##  [506]  0.329964761  0.607894988 -0.336824203  0.163250835 -0.054063905
##  [511]  0.237196393  0.726296785  0.827775105  0.626154642 -0.597640435
##  [516] -0.464591376  0.623421881 -0.624371713 -0.167766558 -0.311483143
##  [521]  0.266164599  0.514403672  0.349384236  1.723163003 -0.186655821
##  [526]  1.740430261 -0.227206296  0.903096903  0.761339063  0.682817165
##  [531]  0.634160025  1.082353705  0.858402334  1.071805892 -0.367848082
##  [536]  0.542448871  0.050915405  1.155189418  0.825489712  1.858470800
##  [541] -0.159986292  0.987466137  0.305430019  0.375788538 -0.651870670
##  [546]  0.863553153 -0.826533051  0.680042131  0.792992774  0.569431627
##  [551]  1.151831956  0.176378544  0.736412093 -0.728961449  0.197490489
##  [556]  0.690621911  1.596564015  1.081211782  0.591095364  0.158662563
##  [561] -0.135727353  0.077998516 -0.203112188  0.871827638  0.166046606
##  [566]  0.291936530  0.645406748  0.379927139  0.152505781  1.684493362
##  [571] -0.151274237  0.186246168  0.239930149  0.007593794  0.358001866
##  [576]  0.935618489 -0.977888919  0.324802090  0.133285186  0.319631918
##  [581] -0.164449296 -0.786019555  0.618162860  1.602544830  0.653526054
##  [586]  0.670296247  1.035906733  0.759909906  1.246445962  0.860226403
##  [591]  1.778880829  0.258005138  1.113815717 -0.010211125 -0.143973454
##  [596]  0.673248045  0.953052511  1.224891517  0.193552150  0.219719618
##  [601]  0.841613656  0.782578205  0.139392902  1.440167473 -0.816783993
##  [606]  1.046036113  1.580509405  0.595989173  0.806615344 -0.421933673
##  [611]  0.210759393  0.006345290 -0.048598890  0.607986828 -0.372795113
##  [616]  0.343369303  1.162922666  0.743678830  0.900815529  0.839151194
##  [621]  0.269001787  1.068267963  1.098847187  0.187305635  0.755530062
##  [626]  0.803279571 -0.154294588  0.586365434  0.288457476  0.054269978
##  [631]  0.399411357  0.828923564  0.210207734  0.430666313  0.437492952
##  [636]  0.045453617  0.785973877  0.576271031  1.041125538 -0.187477902
##  [641]  0.420473557  0.265635287  0.200144134  0.538872857  1.012650509
##  [646]  0.553152719 -0.069820123  0.653563205  0.716551090 -0.200611466
##  [651]  1.091698968 -0.567619178  0.850737710 -0.313229404  0.607308666
##  [656]  0.719731395  0.855602033  1.569136204  0.603805422 -1.088495541
##  [661]  1.855990598 -0.185817641  0.794250345  2.606369607  0.375017753
##  [666]  1.084650141  0.741433593  0.254299005  0.988734463  0.573342169
##  [671]  0.527111555  0.197173197 -0.367894219  0.749248578  1.868517226
##  [676]  1.633777462  0.383049233  0.232056255  0.210933818  0.406315745
##  [681]  1.833093923 -0.867024503  1.236819846 -0.126418840  1.766159975
##  [686]  0.863008456 -0.188243672  1.034959842  1.109353232  1.126784320
##  [691]  0.721579321  0.078868287  0.067234231  0.296144543  0.036752707
##  [696]  0.232061330 -0.040249537  0.733752108  0.813570038  0.582947024
##  [701]  0.590675501 -0.197518733  1.595345089  0.302454624  1.116648224
##  [706]  0.872316614  0.628171476  0.180958957  0.152374762  0.459801012
##  [711] -0.495593472  1.228488818  0.022857283 -0.082081936  0.118892329
##  [716]  0.828083636  0.107617577 -0.005176025  0.224928907  2.150061574
##  [721]  0.317534783  1.417356822  0.785071614 -0.348201233  0.412549778
##  [726]  0.182160748  0.202669878 -0.018054357 -0.191119709 -1.559492286
##  [731]  0.521882410  0.233734819  1.273004222  0.182592608  0.207163049
##  [736] -0.768698704 -0.074020448  0.104932992  0.263378746  0.077549549
##  [741]  0.597586853  0.290818459  0.854296962  0.632898464  1.024500807
##  [746]  0.152072083  2.158706127  0.163293787  1.756842785  0.220280896
##  [751]  1.075624537  0.533123736  1.144281893  0.199002150  0.346759358
##  [756]  1.170920064 -0.191919058 -0.342301720  0.530585820  0.519443669
##  [761]  0.288247448  0.289876615  0.151621645  0.370319147  1.310621460
##  [766]  1.684493362  2.461267788  0.965506258 -1.005202354  0.690894812
##  [771]  0.770029197  0.897309555  0.429295473  0.812255646  1.126250122
##  [776]  0.127815107  1.213429326  0.688249199  0.636037875  0.731625077
##  [781]  0.599264103  0.413525460  0.243361999  0.836614621 -0.531534905
##  [786]  0.395565440  0.321894547  0.626256072  0.283355227 -0.184975755
##  [791]  0.234054888 -0.276055164 -0.363103202 -0.012140951  0.084412964
##  [796] -0.290681325  1.706519471 -0.852990909 -0.022457909  0.472396159
##  [801]  0.414809526  0.112812670  0.206405657 -0.121905637  0.189898587
##  [806]  0.414609945  0.529077829  0.174403412  0.165883528  1.114551913
##  [811]  0.039973979  0.306045535 -0.018833264  0.796705691  0.252911686
##  [816] -0.526242178  1.230288074  1.000502444  0.833897171  0.855455346
##  [821]  0.315310201 -0.484248794  0.861700626  1.077885290  0.416599336
##  [826]  0.637321629  0.993092776  0.207532495  0.661889989 -1.260688786
##  [831]  0.179615187  1.118270344  0.746893266  0.424200181  0.815706820
##  [836]  0.702956973  0.399072566  0.799099245  0.525830962  0.152557476
##  [841]  1.607551301  0.397921099  1.815343023 -0.367249208  0.318883217
##  [846]  0.544000716 -0.726996728 -0.151550551  0.434217171  0.476463333
##  [851] -0.437554711  0.747971806  0.890702338  0.179270997  0.434140672
##  [856]  0.806615344 -0.357181758  0.667427952  0.351556165  0.773574309
##  [861]  0.306523998 -0.350253995  0.376429419  0.883525191  0.599091956
##  [866] -0.142862440  0.701750074  0.868296728  0.825071715 -0.106617704
##  [871]  0.500407959  0.208997707  0.673324228 -0.161555313 -0.456652664
##  [876] -0.020233206  0.285914575  0.572992507  0.772840698  0.680861574
##  [881]  0.177610519 -0.018711754  0.313125942  1.779849597  0.785174255
##  [886]  0.258116323  1.267189641  0.846532611  0.793286927  0.642444256
##  [891]  0.403259469  0.839242074  0.341000958  0.345199253  1.133985334
##  [896] -0.557963881 -0.011057024  1.055370497  0.287522488  0.661990176
##  [901]  0.620048346 -0.103946478  1.150872869 -0.674087224  0.212153543
##  [906]  1.667839386 -0.526530699  0.413012901  0.317000440 -0.160687992
##  [911]  1.335779801 -1.074767596  0.184025817  0.439274299  1.694454871
##  [916]  0.641477133  0.104291471 -0.064559088  0.162258201 -0.813484949
##  [921]  0.890542360  1.105394381  0.843463227  0.375544520  0.569651363
##  [926]  0.375412488 -0.151955441 -0.003445292  0.189137492  0.204324204
##  [931] -0.174517032  1.195948828  0.014064632  0.012663451 -0.003544887
##  [936]  1.216368151 -0.532056678 -0.365651474  0.035643676  0.016085977
##  [941] -0.592594971  0.888873228  0.673049123  1.571630785  0.660885250
##  [946]  0.745858104  0.413952360  0.612032588  0.635684073  0.186025889
##  [951]  0.952691611  1.432766850  0.384594826  1.089466020  1.116154254
##  [956] -0.200195872 -0.245615398 -1.557550880 -0.298534122 -0.172792733
##  [961]  0.322838244  0.638627056  0.225602796  0.199054596  1.312911555
##  [966] -0.389668983  0.842232636  0.643489499  1.085583561  0.979878797
##  [971]  1.120139679  0.764069299  0.078202338  0.573342169  0.874499347
##  [976] -0.980197323  0.476463333  0.617304077  0.962624206  1.038886127
##  [981]  1.436408373  0.875271820 -0.091740105  0.114733873  0.002433231
##  [986]  0.481974938  0.846254102  0.040890410  0.672083197  0.016977203
##  [991]  0.235578634  0.515898208  0.184449528  0.445776933  0.382954635
##  [996]  1.232668163 -0.027787814  0.072740802  0.371722959  1.094205368
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
##   0.4069811   0.1937967 
##  (0.0612839) (0.0433292)
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
## [1]  0.7520658  0.4059853 -0.5727870 -0.7243862 -0.3602769  0.1161693
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
## [1] -0.0523
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9051044
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
## t1*      4.5 -0.007907908    0.914078
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 5 6 8 9 
## 1 2 4 1 1 1
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
## [1] 0.0389
```

```r
se.boot
```

```
## [1] 0.9148408
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

