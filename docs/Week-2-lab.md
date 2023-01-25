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

Lets say we are trying to find the median age of all pennies in circulation. We can't figure this out exactly, because we can't collect all the pennies in circulation, but we each have a sample of 10 pennies. The median age of the pennies in our sample is a reasonable estimate for the median age of all pennies in circulation. 

What is our uncertainty about that number? How far might our estimate of the median age be from the true median age? In this case, we don't know the underlying distribution of penny ages. (Let's brainstorm this for a bit. Do we have any guesses what this distribution might look like? What might be a reasonable distribution to describe the shape of penny age?) 

Let’s use bootstrapped samples to calculate the s.e. associated with that estimate.

Procedure: 
1. Sample WITH REPLACEMENT a group of 10 pennies. (To sample with replacement you will have to sample one penny, write down the age, and repeat that 10 times.)
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
## 2 3 5 6 7 8 9 
## 3 1 2 1 1 1 1
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
## [1] -0.0341
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
## [1] 2.688742
```

```r
UL.boot
```

```
## [1] 6.243058
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.3
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
##    [1] 3.2 4.5 5.1 3.9 6.7 4.8 3.8 5.4 4.9 4.1 4.9 3.1 3.9 4.4 6.0 4.4 2.2 4.4
##   [19] 4.4 3.8 4.8 3.7 4.4 4.7 5.9 4.9 5.6 3.0 4.4 4.9 4.8 5.6 5.4 4.8 3.3 3.8
##   [37] 5.2 4.2 4.7 3.8 2.8 6.4 5.1 3.0 4.0 4.8 4.8 5.7 5.2 3.7 4.7 4.1 4.3 5.7
##   [55] 4.3 3.9 3.1 3.2 4.5 5.1 4.6 4.1 3.8 3.8 6.5 3.5 4.3 3.5 4.1 6.4 3.7 6.0
##   [73] 5.0 3.4 5.0 6.0 3.2 5.2 4.6 5.2 4.9 5.9 6.1 5.0 4.3 5.0 5.6 4.0 3.7 5.7
##   [91] 4.7 3.6 5.6 4.9 5.1 4.6 4.1 4.4 4.4 5.1 4.3 3.7 5.7 5.0 5.8 4.6 4.0 4.6
##  [109] 7.2 4.2 5.2 3.5 3.6 6.1 4.7 3.3 5.9 3.5 4.7 5.4 4.4 3.8 4.8 5.5 3.6 5.2
##  [127] 3.9 4.4 5.0 3.8 4.2 4.7 3.9 3.3 5.0 3.2 4.3 5.8 4.4 4.2 4.5 2.5 4.9 3.0
##  [145] 6.1 3.3 5.4 4.1 5.1 6.3 4.1 5.7 6.0 5.1 5.0 4.3 5.6 3.6 3.5 4.6 4.7 5.9
##  [163] 4.9 3.5 5.1 5.1 4.4 5.2 4.1 4.7 3.7 3.9 5.5 3.9 4.6 4.0 4.8 5.6 6.2 2.7
##  [181] 4.8 2.9 5.1 5.2 5.1 5.5 5.4 4.4 5.9 3.9 4.0 5.2 5.2 3.4 4.0 4.2 4.8 3.7
##  [199] 6.1 4.7 5.0 4.7 5.6 3.7 3.6 3.9 2.9 4.8 3.3 2.8 4.7 5.3 4.2 2.7 6.0 4.6
##  [217] 5.1 6.0 5.1 3.9 5.4 4.7 3.5 4.6 4.2 5.4 4.0 5.3 4.5 4.9 4.8 5.3 5.7 4.3
##  [235] 3.8 4.7 4.3 4.8 4.1 2.2 4.1 5.3 3.4 5.2 3.8 3.9 3.9 4.5 5.7 3.9 5.1 3.4
##  [253] 5.0 3.8 2.7 3.5 3.3 3.4 5.1 4.8 4.4 4.0 4.5 4.8 4.2 5.7 4.5 2.7 3.9 4.7
##  [271] 3.7 5.0 4.0 6.0 5.6 5.9 6.0 5.9 5.0 4.6 3.2 2.9 3.8 5.0 3.6 4.4 4.7 3.7
##  [289] 3.5 5.9 4.9 4.7 4.9 3.7 3.0 4.9 4.2 4.4 4.1 4.8 3.0 5.6 4.9 4.4 3.4 4.9
##  [307] 4.5 4.7 3.2 4.6 4.6 4.1 6.0 3.6 3.5 4.5 3.2 5.2 4.6 4.1 4.4 5.5 5.3 4.4
##  [325] 4.4 4.1 5.4 4.3 4.1 4.0 5.3 4.3 5.8 5.9 4.7 3.1 5.1 6.2 4.4 5.2 3.5 3.7
##  [343] 4.3 3.8 3.8 4.2 3.9 5.2 3.3 4.8 4.7 3.7 5.0 4.0 4.5 3.3 3.6 6.2 4.2 4.2
##  [361] 4.9 3.8 5.7 4.8 3.3 3.5 5.0 4.0 2.9 4.7 5.6 6.1 5.0 3.9 2.9 4.3 2.8 4.2
##  [379] 5.3 4.5 4.8 2.8 4.6 3.2 4.9 4.4 5.2 5.3 5.7 4.8 2.9 3.7 3.8 4.8 5.3 4.2
##  [397] 4.8 4.4 5.8 3.9 3.6 4.0 4.6 4.5 5.7 4.9 4.6 2.8 4.6 2.9 4.6 3.4 4.3 3.1
##  [415] 4.6 5.6 3.6 4.2 5.3 3.5 2.7 5.2 2.6 4.2 5.0 4.4 5.9 4.0 5.3 5.9 4.6 4.0
##  [433] 5.7 4.2 6.4 5.8 5.2 5.5 6.1 4.9 4.4 2.7 3.9 5.2 4.5 5.8 3.7 5.2 3.1 5.2
##  [451] 3.4 5.1 4.5 3.6 4.1 2.9 4.3 4.0 4.0 5.6 4.1 4.0 5.1 3.8 5.9 3.8 5.0 3.7
##  [469] 2.3 4.3 6.6 5.3 4.0 5.2 4.6 4.3 6.1 3.1 4.3 3.0 4.1 5.8 5.4 5.4 4.7 5.1
##  [487] 6.8 5.3 5.3 5.1 3.4 4.0 4.4 4.6 4.0 5.4 4.5 4.9 4.3 4.2 4.5 4.8 5.5 4.6
##  [505] 3.8 4.8 5.3 4.7 5.6 4.4 4.7 4.4 3.2 4.9 4.1 5.1 5.0 5.7 4.2 6.3 4.7 4.3
##  [523] 5.7 4.2 4.9 5.2 5.2 1.9 4.1 6.3 4.0 3.6 4.5 4.2 4.7 4.1 4.0 5.9 4.0 3.1
##  [541] 3.9 5.6 2.5 4.3 3.8 5.0 3.9 3.6 3.9 3.5 5.1 5.4 4.0 3.8 4.2 4.1 4.8 6.0
##  [559] 4.6 4.1 5.9 5.8 3.7 3.9 3.2 5.3 4.0 4.8 4.4 3.8 6.2 5.5 4.3 5.5 4.0 2.8
##  [577] 3.9 4.4 4.3 2.1 4.5 4.7 3.7 3.1 4.4 3.6 4.5 5.9 5.2 4.1 5.4 4.1 4.9 4.5
##  [595] 5.3 4.6 5.0 5.8 3.8 3.1 3.4 5.0 4.9 3.9 4.7 3.8 6.1 4.8 3.1 5.0 3.6 5.0
##  [613] 5.2 4.7 4.7 4.2 3.4 3.7 4.4 5.4 5.0 4.6 5.0 4.1 4.5 5.0 5.1 4.9 3.9 4.2
##  [631] 5.0 4.0 2.9 5.4 5.2 4.1 4.4 3.3 5.5 5.6 4.7 3.7 6.2 5.1 3.2 4.7 4.8 4.7
##  [649] 5.6 5.6 5.2 5.4 4.6 3.1 4.6 4.7 4.5 3.8 6.4 3.4 4.5 6.5 2.9 5.2 3.6 3.5
##  [667] 4.7 5.6 5.2 5.3 4.6 3.6 4.2 4.0 5.5 5.1 4.0 3.9 3.5 5.9 4.2 3.8 2.7 3.9
##  [685] 6.9 4.9 5.8 3.8 4.2 4.5 3.9 3.8 5.0 3.8 4.5 5.9 5.0 3.9 4.5 4.5 3.6 5.3
##  [703] 3.0 4.9 5.1 2.9 4.7 4.6 4.5 3.9 5.5 6.3 4.4 5.1 4.0 5.7 4.2 5.3 3.5 4.9
##  [721] 4.1 5.5 4.2 4.0 5.1 5.1 4.8 3.2 6.1 5.5 4.2 4.9 3.2 4.1 5.3 4.2 4.6 4.3
##  [739] 4.8 3.5 4.6 3.8 3.6 6.6 4.9 4.1 4.2 6.3 5.7 4.6 4.9 5.1 6.2 5.3 4.4 3.9
##  [757] 3.7 2.9 4.6 5.5 5.3 3.9 6.3 5.1 4.0 5.6 4.3 5.8 4.5 5.2 3.0 5.5 3.8 4.9
##  [775] 5.9 5.7 4.1 5.0 5.4 4.6 4.1 5.6 4.8 5.7 4.5 4.6 3.0 5.4 5.5 5.3 5.9 5.6
##  [793] 6.5 3.1 3.1 5.7 4.9 5.2 5.4 4.8 3.7 5.8 4.9 3.6 3.3 5.5 4.1 3.1 3.2 4.6
##  [811] 4.1 3.9 2.9 4.2 3.9 5.4 4.2 3.4 5.7 5.1 5.2 4.6 4.1 4.4 4.6 5.4 4.5 6.0
##  [829] 4.2 3.0 3.3 3.9 4.2 5.9 5.4 4.0 4.6 4.6 5.0 3.7 4.2 4.6 5.1 3.5 4.4 4.1
##  [847] 3.1 4.7 5.7 5.5 4.0 5.2 4.5 2.8 4.3 3.2 3.8 4.8 4.8 4.6 3.9 3.5 4.3 5.0
##  [865] 3.5 4.9 5.4 4.2 5.0 3.3 4.0 4.9 4.7 3.9 4.2 4.1 4.1 3.0 6.0 5.5 4.4 4.0
##  [883] 5.1 3.0 5.1 4.0 5.7 5.5 3.4 4.4 3.8 6.5 3.8 3.5 3.7 4.9 5.2 5.0 5.2 4.6
##  [901] 4.3 3.1 5.2 2.6 2.8 4.5 4.8 2.7 5.4 6.7 4.5 5.4 5.9 3.9 2.6 4.4 5.1 5.8
##  [919] 3.6 5.8 4.4 5.3 2.5 3.9 4.8 3.0 4.1 4.1 6.2 3.9 5.6 4.6 3.9 4.2 2.3 4.2
##  [937] 6.3 5.0 3.9 3.8 6.0 4.4 5.1 3.8 4.1 4.9 4.6 4.0 4.2 2.7 3.5 5.3 2.8 3.0
##  [955] 4.9 5.5 4.3 5.6 4.7 5.7 4.8 2.8 4.6 5.4 5.1 6.1 4.9 3.6 5.0 5.8 4.3 4.2
##  [973] 4.1 4.2 6.1 4.3 5.4 4.6 4.8 4.6 4.4 3.8 3.9 4.0 5.4 3.2 5.2 3.1 4.6 4.3
##  [991] 3.9 4.3 5.4 4.8 4.8 3.5 4.5 5.2 4.9 4.5
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
##    [1] 2.6 2.7 5.3 4.6 3.9 5.5 4.9 3.9 6.2 5.6 4.5 5.9 1.4 3.7 4.0 3.7 4.2 4.9
##   [19] 4.5 2.8 3.7 5.7 3.3 4.6 3.8 3.8 4.3 4.2 3.9 4.7 4.0 4.7 4.3 4.7 5.1 4.7
##   [37] 4.1 4.5 4.7 5.2 4.4 4.8 5.5 4.2 4.1 5.0 4.5 3.4 4.3 4.3 5.8 5.3 4.7 5.0
##   [55] 5.4 4.3 4.7 4.5 4.4 4.6 4.4 4.0 5.2 3.7 4.5 4.7 4.2 4.9 4.9 4.0 4.7 5.9
##   [73] 4.3 3.7 4.3 5.0 5.6 5.6 5.8 3.5 3.0 5.5 2.9 3.9 5.1 3.3 3.6 3.6 5.4 5.3
##   [91] 5.2 4.4 5.5 4.8 5.0 2.7 5.4 3.7 3.6 4.7 5.8 4.2 4.7 4.9 4.0 5.1 4.9 3.7
##  [109] 3.6 2.8 4.8 4.1 3.0 4.9 4.6 6.4 4.7 4.8 5.5 3.2 4.6 3.3 5.9 5.3 5.6 5.7
##  [127] 5.3 3.0 6.3 4.5 4.8 5.3 4.4 4.1 4.7 4.7 4.2 5.0 6.3 3.8 4.6 4.3 4.2 5.3
##  [145] 5.0 6.0 5.1 3.8 4.1 5.2 4.2 5.9 3.1 6.1 4.9 5.4 4.0 3.0 4.6 5.5 5.6 5.2
##  [163] 4.5 4.4 6.8 5.1 5.2 5.2 4.0 4.3 3.4 2.8 4.8 3.6 3.1 5.3 5.3 6.0 4.4 4.0
##  [181] 4.9 4.8 6.0 3.8 3.6 4.2 4.5 3.7 4.0 4.9 5.7 5.7 4.3 6.2 3.1 4.9 5.5 3.6
##  [199] 4.7 3.0 5.9 5.2 4.2 4.7 4.0 4.3 5.5 3.3 4.7 4.1 4.2 4.1 4.3 3.9 2.6 4.7
##  [217] 5.7 5.6 4.6 5.4 5.3 4.1 5.1 4.0 5.0 6.4 3.2 4.3 4.4 2.9 4.4 5.8 5.4 3.6
##  [235] 3.1 3.4 6.9 4.7 2.7 3.9 4.1 3.6 6.0 2.8 4.7 3.0 5.8 4.8 4.6 3.7 3.8 4.1
##  [253] 5.1 3.4 4.4 4.1 5.3 4.3 4.8 5.0 4.3 3.6 4.1 4.1 6.2 4.5 4.9 2.4 5.2 4.9
##  [271] 5.5 3.7 4.5 5.1 4.0 4.1 2.9 6.6 4.7 4.7 4.1 5.5 3.9 4.0 4.8 5.0 3.4 5.6
##  [289] 2.5 4.7 4.5 4.5 5.6 4.1 3.5 4.3 4.7 2.0 2.8 4.8 4.5 3.2 3.6 5.1 3.6 3.0
##  [307] 3.4 2.8 3.2 5.8 5.4 4.0 3.0 5.2 4.1 3.2 4.1 4.4 4.6 4.4 4.4 4.5 5.3 3.7
##  [325] 3.8 4.5 5.3 4.8 3.7 4.1 5.1 4.8 5.7 3.1 2.0 6.2 4.6 3.8 5.1 5.5 4.8 3.6
##  [343] 3.8 4.5 5.6 5.3 4.4 3.7 3.7 5.2 3.8 5.2 3.9 6.1 6.8 5.3 4.4 5.9 4.5 3.5
##  [361] 4.9 1.6 3.8 5.2 4.9 5.5 4.7 4.2 3.4 4.7 4.5 4.9 4.7 4.2 4.2 5.0 5.0 4.3
##  [379] 2.5 3.2 4.8 4.3 5.7 4.7 4.7 5.5 4.2 3.4 4.2 3.2 5.8 5.3 4.4 4.0 3.9 5.1
##  [397] 5.3 4.8 5.7 4.6 2.9 3.3 5.0 2.6 3.4 4.3 4.9 5.3 4.4 5.4 3.8 4.9 4.2 5.7
##  [415] 4.7 3.9 5.3 5.6 2.4 3.9 4.1 3.8 4.8 5.0 6.1 5.0 3.5 4.9 1.6 4.0 4.4 3.7
##  [433] 3.8 4.8 4.5 4.6 6.4 4.6 3.8 4.9 4.9 4.6 6.3 4.6 4.1 4.8 5.0 4.4 4.6 4.0
##  [451] 5.1 3.6 3.2 4.9 4.3 4.2 3.4 3.3 4.8 3.0 4.9 4.3 3.9 5.3 5.0 4.7 4.0 5.5
##  [469] 5.2 3.2 5.0 4.3 6.5 4.6 3.3 4.1 4.2 4.3 4.4 5.2 6.0 4.3 5.9 4.9 3.9 6.0
##  [487] 3.4 4.5 5.0 4.5 4.6 4.6 6.3 4.8 5.1 3.9 4.9 4.5 3.4 4.1 4.0 3.0 3.9 3.5
##  [505] 5.8 4.1 4.2 4.3 5.5 4.4 5.2 4.0 4.4 5.2 4.1 5.5 3.5 4.3 4.9 4.8 4.1 5.2
##  [523] 4.3 3.8 4.1 4.5 4.6 4.5 4.4 5.0 3.9 4.5 3.7 5.9 5.3 4.2 4.6 2.6 3.3 6.3
##  [541] 5.7 5.5 4.1 6.2 4.9 5.7 4.2 4.5 4.4 4.7 4.8 6.8 5.6 3.3 4.9 5.4 3.7 5.6
##  [559] 3.8 4.5 4.4 3.4 5.7 4.5 4.7 6.5 4.6 5.2 3.9 3.2 5.6 4.5 4.0 4.2 1.7 3.9
##  [577] 5.0 6.5 4.5 4.4 5.2 3.6 4.9 4.6 6.0 3.5 5.6 3.6 4.1 4.2 3.2 6.3 5.2 3.9
##  [595] 5.0 4.0 3.3 5.5 4.5 3.6 3.9 5.1 3.1 4.7 3.1 5.2 2.2 3.3 4.0 4.0 4.3 4.8
##  [613] 3.8 5.0 5.1 3.8 4.1 3.0 4.8 5.4 3.0 5.7 3.7 4.7 4.8 4.0 3.1 5.1 5.3 3.7
##  [631] 4.9 4.5 5.8 4.6 3.4 3.4 3.0 4.0 4.0 5.9 4.5 3.6 2.9 5.7 5.9 3.4 3.7 4.8
##  [649] 4.7 4.4 3.6 6.2 3.5 6.1 2.7 4.6 4.2 5.1 4.4 3.3 5.9 4.8 2.9 6.0 5.2 4.3
##  [667] 4.3 5.0 5.1 4.3 4.2 4.1 4.2 5.2 3.7 2.9 4.2 5.9 5.6 3.7 5.1 4.9 3.5 3.4
##  [685] 4.3 4.7 3.1 3.5 4.5 4.4 5.8 4.6 4.1 3.7 3.7 5.0 3.3 5.3 4.2 2.7 5.1 4.2
##  [703] 5.5 5.8 5.0 5.9 6.1 4.3 5.3 4.5 3.1 4.2 5.0 3.1 5.7 3.2 3.5 5.4 4.0 2.9
##  [721] 4.7 5.4 5.7 4.1 4.8 4.1 4.9 5.5 3.6 4.3 3.3 2.9 5.4 6.4 3.0 4.1 5.4 5.2
##  [739] 4.0 5.4 4.4 4.0 3.5 4.8 5.7 4.3 2.7 4.7 5.1 4.8 5.3 5.6 4.9 3.3 5.1 3.8
##  [757] 4.9 4.8 4.7 6.2 4.2 3.2 4.5 4.2 5.1 5.0 4.4 2.5 3.2 3.3 6.2 4.0 4.0 6.3
##  [775] 3.5 3.0 3.7 4.6 4.5 5.1 4.8 4.4 5.4 4.3 2.2 5.4 2.8 6.4 5.1 4.3 4.9 4.7
##  [793] 6.3 4.4 2.6 5.7 3.9 4.3 4.5 4.3 4.5 4.8 4.1 4.8 4.6 4.9 5.0 2.7 4.2 3.9
##  [811] 4.9 5.1 5.4 5.4 4.4 3.7 3.6 4.2 4.3 4.9 4.0 5.3 5.2 5.7 5.1 4.2 5.1 4.4
##  [829] 5.2 4.8 4.7 5.6 5.2 2.8 4.8 4.5 4.8 4.3 4.1 3.9 3.9 5.4 5.6 5.5 3.9 5.6
##  [847] 2.9 5.0 4.0 5.3 4.2 6.0 5.7 3.6 3.5 4.9 6.0 4.8 5.2 5.4 5.8 4.9 3.6 3.9
##  [865] 5.0 5.0 2.9 4.3 5.7 5.0 4.8 4.6 4.7 5.6 6.2 2.8 4.9 1.7 4.6 3.8 4.7 4.9
##  [883] 3.3 3.6 2.8 5.7 4.8 5.3 6.7 5.1 6.2 4.2 5.4 3.4 4.3 4.6 4.4 3.5 4.0 4.8
##  [901] 6.0 3.3 4.9 3.6 5.1 4.0 4.9 5.7 5.5 4.0 4.5 4.5 4.7 5.9 3.9 3.7 4.3 6.3
##  [919] 3.6 2.4 4.1 2.8 5.1 3.5 4.8 4.4 3.2 4.6 4.9 4.8 3.6 4.2 2.6 5.8 3.4 1.9
##  [937] 2.8 4.5 4.6 3.4 5.3 4.2 5.3 3.3 4.2 2.8 5.6 4.5 5.1 4.8 5.0 3.8 4.7 2.3
##  [955] 3.7 5.1 2.5 5.6 4.4 2.4 5.9 4.2 3.6 3.7 3.8 5.3 3.7 3.3 4.3 3.2 6.0 4.8
##  [973] 5.0 3.2 3.0 3.4 4.4 5.8 2.9 4.2 4.8 2.5 5.8 5.6 6.1 3.6 4.9 4.8 4.7 4.4
##  [991] 3.2 3.9 4.6 5.4 3.9 3.4 4.2 4.3 4.5 5.0
## 
## $func.thetastar
## [1] -0.0286
## 
## $jack.boot.val
##  [1]  0.46705202  0.38265896  0.30790960  0.09419525  0.06577381 -0.02870370
##  [7] -0.23920973 -0.35654762 -0.42506812 -0.56454294
## 
## $jack.boot.se
## [1] 1.016617
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
##    [1] 6.4 6.6 5.4 7.0 4.9 2.6 3.9 5.3 5.1 3.2 5.2 3.3 3.8 4.0 5.1 4.4 4.0 5.2
##   [19] 5.6 4.8 6.0 4.1 4.4 3.2 4.5 3.8 6.2 4.3 4.0 5.3 3.6 3.4 3.6 4.0 4.9 4.3
##   [37] 4.4 5.1 3.7 5.5 5.7 5.9 3.1 2.8 5.5 3.2 5.8 3.0 4.7 3.9 4.2 6.4 4.7 3.9
##   [55] 5.1 4.7 5.7 4.6 3.6 3.5 4.0 5.9 4.9 3.4 5.1 6.4 4.5 5.4 4.2 3.7 3.2 5.3
##   [73] 6.2 3.4 5.9 3.7 4.3 3.2 4.7 4.5 4.1 3.6 4.2 3.7 4.6 4.6 5.3 4.1 4.7 4.9
##   [91] 6.9 5.3 3.7 4.7 3.5 3.7 4.0 5.0 4.8 3.5 2.9 4.8 3.9 4.3 4.1 3.4 4.2 4.2
##  [109] 2.4 4.6 4.7 3.3 4.3 3.2 4.3 5.1 3.6 4.7 5.2 5.2 3.7 4.5 4.3 4.2 4.1 5.3
##  [127] 3.0 3.4 5.5 3.7 5.6 5.0 5.0 4.0 5.2 4.1 4.0 4.8 4.5 6.1 4.6 4.2 4.5 4.1
##  [145] 7.1 4.8 4.2 2.6 4.4 5.6 4.7 4.2 4.8 6.1 6.3 4.1 4.9 2.9 3.9 5.4 5.2 6.4
##  [163] 4.3 4.8 4.8 3.8 4.3 2.8 5.1 5.4 3.6 4.4 4.7 4.6 3.3 2.8 3.7 3.7 4.3 5.4
##  [181] 3.6 4.3 4.0 6.1 5.6 4.4 3.9 5.0 4.1 3.2 5.3 4.9 3.5 5.3 5.2 5.2 4.2 3.8
##  [199] 4.9 5.3 4.3 3.5 3.6 3.9 4.5 3.0 3.6 5.5 4.7 6.5 5.6 4.7 4.9 4.0 4.0 3.9
##  [217] 4.5 5.0 3.2 4.9 4.9 4.9 4.3 3.0 3.8 5.2 5.7 4.0 4.2 4.3 5.8 3.3 3.6 2.9
##  [235] 5.9 4.9 4.7 4.6 4.5 4.1 3.8 4.3 3.5 4.0 4.8 4.0 4.4 5.2 4.3 5.3 3.9 3.9
##  [253] 4.4 4.4 4.7 2.9 3.2 5.3 3.2 5.1 5.1 4.8 3.3 4.7 5.0 4.6 5.6 4.8 4.3 2.5
##  [271] 4.5 5.5 4.1 6.0 5.0 2.6 5.6 3.1 4.8 6.2 4.0 4.9 3.8 5.8 4.9 5.3 4.0 4.6
##  [289] 6.4 2.9 2.8 3.1 2.7 5.7 4.5 3.3 3.9 3.7 5.4 4.2 2.7 4.6 6.5 5.7 3.6 3.5
##  [307] 6.4 3.5 4.7 3.8 4.1 5.6 3.9 5.8 3.2 4.7 5.0 3.5 5.2 3.8 4.0 3.6 5.8 6.0
##  [325] 4.6 3.5 5.0 5.3 4.8 5.0 4.1 6.8 4.0 4.3 5.7 4.9 5.3 4.4 5.5 3.0 4.8 4.0
##  [343] 5.4 4.0 3.3 3.5 4.6 3.7 4.1 5.4 3.3 5.0 5.0 4.0 2.0 4.6 4.3 5.9 5.8 4.1
##  [361] 4.7 4.0 4.8 5.1 4.4 4.6 4.8 5.6 4.3 7.1 3.1 5.9 6.1 3.4 3.9 4.9 3.7 5.1
##  [379] 5.2 2.8 5.6 4.9 5.0 5.8 4.8 4.2 3.0 4.5 4.4 5.1 6.0 4.6 3.6 5.9 4.5 6.1
##  [397] 4.2 2.1 5.4 5.1 4.4 3.0 4.6 5.3 6.2 4.4 3.6 5.8 5.0 3.8 5.1 3.2 3.5 4.2
##  [415] 5.0 5.4 4.8 4.2 4.3 6.3 3.9 2.1 3.2 5.2 5.7 5.1 4.8 3.7 4.6 5.4 4.0 5.7
##  [433] 5.8 4.4 4.9 3.7 5.6 4.8 3.6 5.4 4.0 6.3 3.1 3.8 5.6 5.5 4.4 5.2 2.5 6.2
##  [451] 5.5 5.5 4.4 3.7 4.4 5.4 5.2 3.4 4.1 4.5 3.6 4.4 5.1 4.0 5.2 3.9 4.8 5.8
##  [469] 3.7 4.6 3.3 2.9 4.0 4.8 4.8 3.4 5.2 5.2 2.9 5.6 4.8 2.6 5.9 4.3 4.9 3.9
##  [487] 3.7 4.8 3.5 5.2 4.4 5.1 4.2 3.2 4.2 6.1 5.6 3.2 4.2 3.5 3.0 3.8 3.2 2.5
##  [505] 4.8 5.1 5.5 4.0 5.4 5.1 3.7 3.9 5.0 3.6 3.5 5.0 3.3 4.0 3.8 4.1 4.4 3.0
##  [523] 4.7 4.2 5.4 4.0 5.3 4.0 5.0 3.5 4.4 4.5 5.0 5.2 4.6 5.0 3.4 5.0 4.3 4.0
##  [541] 4.0 5.7 5.0 2.6 3.0 4.9 3.8 3.9 5.8 4.9 5.0 2.9 4.5 4.5 5.5 6.1 6.3 5.8
##  [559] 5.0 3.8 3.7 3.5 2.5 3.7 3.8 5.2 4.2 4.1 4.2 3.1 4.0 4.5 3.3 5.8 4.5 3.9
##  [577] 6.4 4.3 5.1 4.5 5.1 4.6 5.0 4.0 4.3 3.8 3.4 4.8 3.9 3.4 3.7 5.1 4.6 3.4
##  [595] 4.4 4.7 5.0 4.4 4.9 5.2 4.7 3.3 5.7 3.8 4.7 5.7 4.6 4.7 4.8 4.7 5.0 4.2
##  [613] 5.1 3.7 5.5 3.0 6.0 3.6 5.4 3.7 5.8 4.8 6.3 5.8 4.8 5.5 4.5 5.4 3.5 5.6
##  [631] 3.0 4.7 5.2 4.7 6.1 5.0 3.0 5.3 5.1 3.5 4.6 5.3 5.0 4.1 5.2 4.7 5.2 4.9
##  [649] 3.7 5.4 5.9 5.0 5.1 3.9 3.2 3.9 4.4 4.3 5.1 4.3 3.0 4.0 6.2 4.6 4.9 5.5
##  [667] 6.0 6.4 4.9 3.7 5.7 5.1 2.3 5.6 4.8 4.1 4.8 5.0 5.1 4.0 3.8 4.1 3.3 5.9
##  [685] 4.9 2.7 5.2 3.5 3.9 5.4 3.1 2.7 5.2 5.5 4.6 5.0 4.7 3.0 3.2 4.8 4.0 3.3
##  [703] 5.5 4.6 5.3 3.9 2.4 5.2 5.1 4.2 4.3 5.1 4.5 3.4 4.1 5.3 5.4 5.5 4.2 2.6
##  [721] 4.7 5.8 5.3 4.7 6.1 4.9 4.6 5.1 3.9 3.3 4.6 4.7 4.4 4.8 4.1 5.5 5.5 5.9
##  [739] 2.6 5.2 4.3 5.6 5.1 3.2 3.7 3.3 4.8 4.9 2.5 4.3 5.8 3.0 4.5 4.7 3.9 5.0
##  [757] 4.6 4.9 4.2 3.8 3.6 5.9 5.2 5.1 5.4 3.5 3.6 4.3 5.1 4.3 4.9 4.9 3.0 6.3
##  [775] 3.1 4.7 4.8 6.3 3.8 4.5 3.8 5.1 5.5 5.1 4.8 5.7 5.8 4.1 3.1 4.6 3.5 4.2
##  [793] 4.5 3.5 5.0 5.2 3.6 5.9 5.5 5.7 4.7 4.4 2.5 4.7 4.7 3.4 4.7 4.4 5.4 4.5
##  [811] 3.6 3.5 3.1 3.8 6.4 4.2 4.9 5.4 3.9 3.7 4.1 3.6 5.2 4.7 3.8 5.7 3.9 3.4
##  [829] 4.7 5.3 3.4 4.1 4.2 4.4 4.6 6.2 5.3 4.5 3.7 3.2 5.8 3.5 3.9 4.7 3.4 5.1
##  [847] 6.1 4.9 4.5 4.6 5.7 3.6 5.2 5.1 4.1 4.5 3.1 5.5 4.2 4.5 3.3 4.1 4.5 5.5
##  [865] 4.4 4.6 4.8 4.4 4.5 5.1 5.0 5.3 5.2 3.9 4.4 6.9 4.3 4.5 4.9 4.4 4.4 5.3
##  [883] 3.8 5.5 4.1 5.3 5.5 5.1 4.6 4.5 4.4 4.9 5.5 3.3 3.8 2.5 3.3 5.9 4.5 5.2
##  [901] 4.5 3.9 4.4 4.3 5.2 5.6 3.5 5.0 5.1 5.6 4.6 4.9 3.7 5.3 3.4 5.4 4.3 4.2
##  [919] 3.3 3.1 3.7 3.7 4.3 5.3 4.2 4.1 5.6 5.2 4.0 5.7 3.2 4.3 3.7 4.0 3.0 4.7
##  [937] 4.1 3.3 5.3 4.2 4.6 4.2 4.9 5.0 4.4 4.5 6.8 4.6 6.5 3.3 4.6 5.6 4.7 4.6
##  [955] 2.6 4.3 3.2 3.4 5.5 4.2 4.9 4.2 5.3 5.8 4.9 3.3 3.4 4.7 3.3 5.6 3.9 4.0
##  [973] 4.1 4.9 4.2 4.7 4.1 5.0 3.3 3.7 2.9 3.6 5.4 5.4 5.3 4.6 5.1 4.8 4.3 4.4
##  [991] 4.7 4.2 4.0 5.0 5.3 4.9 4.7 2.6 3.4 4.2
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.4 5.4 5.3 5.2 5.1 5.0 5.0 4.8 4.7 4.5
## 
## $jack.boot.se
## [1] 0.861162
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
## [1] 0.7059796
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
##   5.131785   8.678949 
##  (2.224415) (3.952235)
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
## [1]  0.4470149  0.2816148 -0.7754494  0.3336893  2.1534809 -0.1628017
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
##    [1]  0.3864728452 -0.6787898419  1.3321636976 -0.1660379204  1.0997776015
##    [6] -1.0629491958  1.2684613221  1.0461900587  0.4660506435 -0.0889244548
##   [11]  0.5669369098  0.6064483347  1.2174984964  0.0014299724  1.0326567324
##   [16]  0.7497809855  0.7989220169  0.4166191369  0.3816911467  1.1328275548
##   [21]  1.2511156330  0.4244321436  1.6972931905  1.8846467791 -0.3755299532
##   [26] -0.4481005693  1.2672921232 -0.4544987822  0.2381381183  0.0309101900
##   [31]  0.3862644959 -0.0868613001  0.0860632328  0.6793629805  2.0774421569
##   [36]  1.0019036425  0.5092997834  0.1529095074  0.2210423186  0.6521580942
##   [41] -0.2013874571  0.6947047362  0.8090152422 -0.2209991296  1.1855881968
##   [46] -0.8571782573  0.3425893358 -1.0021456730 -1.0659586973 -1.3626789186
##   [51]  0.1104427460  0.8698385769 -0.6348295608 -1.0348062067 -0.5260449015
##   [56]  0.3104209560  0.7336187074 -0.4937281141  1.5335950516 -0.5546454347
##   [61]  0.4141957142 -1.2567087294  0.8156912855 -0.2577153207  1.7345838509
##   [66]  0.4716524155 -0.2128928310 -0.6487980826  0.8940709014 -0.0454289009
##   [71]  0.8565009607  1.7510790467  0.1755078102  0.4439664043 -0.7025125284
##   [76]  0.0930259965  0.6588144885  2.1285219906  0.8466385629  0.8558576582
##   [81] -0.5976891079 -0.3273578766 -1.0989856322 -0.2995383779  0.4589085427
##   [86]  0.6166843011  0.6108539147  0.4458060794 -0.3666175497  0.7527825700
##   [91]  1.1480110998  0.9125106004  0.2789840897  0.6332381963  1.3936151992
##   [96] -0.1132987957  1.0038437835  0.8052673678  0.8726357621 -0.2856099721
##  [101]  0.9845366141  0.4415669045  0.6066924971 -0.0749459065  0.8467663649
##  [106] -0.1498402246  1.5541919301  0.6802476915 -0.9196190097  1.2064372484
##  [111] -0.4211619552 -0.0260878244  0.6954129411 -0.1330195813 -0.6090887385
##  [116] -0.1461802953  0.5365590036  0.4394536497  0.4844087017 -0.5620371101
##  [121] -0.8603813813  0.0533298984 -0.1600163338 -0.3144744081  0.8326897758
##  [126]  1.5985035520  0.3134744454  0.6220209927  0.9254657323 -0.2842055251
##  [131]  0.6692911357  0.7117871729  0.5173424370  1.0316672150 -0.7369097293
##  [136]  0.3959219108 -0.9461492672  0.7088704108 -0.1970235546  0.4024458543
##  [141] -0.4072186343  0.3764715050  0.3769527773  1.1055722495 -0.8312351982
##  [146]  0.4331197248 -0.8699450921  0.6652330680 -1.3980700260  0.6034320530
##  [151]  1.2144333000  0.8909120178 -0.8054513250  0.6589645509  0.9522414062
##  [156] -0.5487855610  0.9846323019 -0.3586619403  0.3873415017 -1.1143579346
##  [161] -0.4938964243 -0.5632711196  0.5321762235  0.2699616697  0.4543762437
##  [166] -0.9494710204  1.0764519937 -1.9376798128  0.7454245271  0.3809824228
##  [171]  0.6032515627  1.6325325316  1.1704530551  1.7064112036  0.8580334281
##  [176] -0.6621403150  1.6023722931 -0.2285971791  0.1223516365  1.8580033910
##  [181]  1.1239753925 -1.2124303348  1.7902523766  0.5933156656  0.6069678178
##  [186]  0.5685361600 -0.7103063108  0.9527533171  0.6944929118 -1.2388189561
##  [191] -0.7340398358  0.5021568038  0.2737411480  0.2172443850 -0.9675815056
##  [196]  2.2999932428  0.3311019080  0.2686314078 -1.2497400495 -1.4522010341
##  [201] -0.8339030039  0.6695529738  0.5400161542  1.0640935872 -0.1880721928
##  [206]  0.7587640703  0.1424860041  0.3229479444  0.7764199965  0.8967306861
##  [211]  0.6646052138 -0.9556398774  0.7789249701  0.0005354074 -0.5259330155
##  [216]  1.1644019731  1.6972931905  0.6232795356  0.1822321338  0.0802279508
##  [221]  0.7437215710 -0.7996001342 -0.5559380990 -0.1994733192  1.1058189442
##  [226]  1.4291553920 -0.3790637226  0.6555710778  0.5540110430  0.4427947707
##  [231]  1.1350273736  0.8326014915 -0.1745534198  0.2572111732  0.7513029172
##  [236]  1.2224133065  1.5985035520  0.1535024281 -0.5718256780  0.9244260425
##  [241] -0.4807696689  1.4068230338 -0.8597593500  0.4854672160  1.3006096680
##  [246]  0.3359927444  0.2139561936  1.0217260188  0.6138579237  0.8518587275
##  [251]  0.2632923893 -0.6772675092 -1.5705339371  1.6265170428 -0.9232698993
##  [256] -0.6398445925 -0.3842091810  0.8489392018  0.7356306037 -0.7772192119
##  [261]  0.1588559769  0.7675930460 -0.4778526232  0.5891129994  1.1260857914
##  [266]  0.2956509653  0.0543588055  0.6144154450  1.0269744562  0.4727700905
##  [271]  0.9180540823 -0.1736146873  0.8903477310  1.2780715430 -0.3796301479
##  [276]  0.6473517647  0.3000447648  0.8857174678  0.1232305261  0.8611016431
##  [281]  1.0635252048  1.0620322325  0.3162906139  0.1232355588  0.4814832472
##  [286] -1.0978671055 -0.8550470118  1.0510023610  1.2219341821 -0.9702354948
##  [291]  0.9602248449  0.8582547484 -0.4033156094  0.1670660758  0.9273028568
##  [296]  0.5540541956  0.2147422086  1.5925919142 -0.2611706263  1.5883289618
##  [301]  0.6603044481  0.0217192411  0.0747265852  1.1213522944  1.0386599687
##  [306] -1.2017290129  0.0991970820  0.8526242119  0.6147471565  0.0286958756
##  [311]  1.1358608987 -1.2537448920  0.4574036269  1.2420489458 -0.4815575534
##  [316]  0.7952911062  0.8087517325 -0.6117950088 -0.5491975547  1.2562782064
##  [321]  1.2298160031  0.0678399699  0.8426745997  0.7784500821  0.4993093006
##  [326]  0.7337250972 -0.6161783402  1.4231461401 -0.8410493426  0.1790021389
##  [331]  1.2094968399  0.5248713007  0.1710780641  0.6664194755  0.6117889061
##  [336]  0.8654197504 -0.5649771474  1.8355746858  1.3148722291  0.4413265155
##  [341]  0.5711149698 -0.2260137290  1.2359027324  0.2901947812  1.9166591007
##  [346]  0.9886716126 -0.6428091862  1.0674070473 -0.6393196670  0.5218724328
##  [351] -2.1873071471 -0.0666612933  1.0059560204 -0.8952459808  0.7288633940
##  [356]  0.2801421483  1.5115360765  0.3525851297  0.8379124671  0.5057674995
##  [361]  0.8681978377  1.7748220606  0.5558269532 -0.3774654596  0.6051570691
##  [366] -0.4458443591 -0.0974768099  1.0434737070 -1.3674053906  1.0330433930
##  [371]  0.9123171594  0.7812008861  0.5289284231  0.8610026298  0.4249797253
##  [376] -0.7123757446  0.8571565912 -1.0951496459 -0.6464101443 -0.6634729102
##  [381]  0.2728963040  0.1708503818 -1.1077883661  0.8635813714  0.5083737842
##  [386] -0.1460867666  0.4267846193 -0.2531849549  0.7310647168  0.6187009367
##  [391]  0.1574316355  1.2033376358 -0.4751042779  0.6388707940  0.3105702777
##  [396] -0.7019640318  0.6301581904  0.0267247194  0.7298564897  1.6034123305
##  [401]  0.6270064761  0.0399423501  0.9430170876  0.0594651696  1.7375356085
##  [406]  0.6912713828  0.6362441893  1.3910139755  0.4130777712  0.7743767107
##  [411]  1.3939177538 -1.0637183715  0.4958151943  0.4271479884  0.7145719728
##  [416]  1.5153233379 -0.3308033562 -0.7098928346 -1.3831070380  0.7733307301
##  [421] -0.7396159797  0.9170871578 -0.3751498515  0.6208050221  0.3228706307
##  [426] -0.8814794788  0.6080861086  0.7477156886  0.9487830549  0.4167939940
##  [431]  0.8353906328  0.4769740706  0.3940455651  1.0697674021  0.2530255043
##  [436] -0.2568802119  0.5468578693 -0.0653693408 -0.7055859781  0.6085976841
##  [441] -0.1520152959 -0.2548243492  0.5675524523  1.3270292089 -0.1429847603
##  [446]  0.5195743893 -1.1042064540 -1.2497400495  0.9221730181  0.5788257280
##  [451] -0.5968148039  0.0695452198 -0.3164735696 -0.7953038038  0.3621393801
##  [456]  0.4979352187  0.5769725288  0.4707530848  1.0497180675  1.2054893522
##  [461] -0.2861062197 -0.6843673790 -0.8833400882  0.7301046556  1.1147258660
##  [466]  0.7145330241 -0.8347949190  0.6073173741  1.1608571779 -0.6333504505
##  [471]  0.7431147814  0.8664575022  0.5158391192  0.7227700967  0.1699702634
##  [476]  1.1667440820 -0.9518363799  1.7180967112  2.1831590424  1.5101942771
##  [481] -0.2525230177  1.1024852746 -0.1791221629  0.3698894922  0.9834803913
##  [486]  1.2263188999 -0.9106242498 -0.4522228696  0.3565795139  0.8332448172
##  [491] -0.6608945306  0.4278604616 -0.4938842383  2.1035153463  0.7957659632
##  [496]  0.7834767999  0.4252827038  0.1758061787  0.5222154395 -0.5332506382
##  [501]  1.1261431968 -0.6437951350 -0.1742928026  0.5825851716  1.2327850870
##  [506]  0.6565914612  1.6100950791  1.4255176368 -0.7753269845 -0.1159382341
##  [511] -0.9155401114  0.5825396587 -0.7599395337  0.7828721215  0.9137011421
##  [516] -0.2533711473  0.0438460592 -0.2955667546  0.8526242119  0.9341139593
##  [521]  0.7876576833 -0.5352035170  0.3333634212  1.0831953709 -1.1049280432
##  [526]  0.9755672190  0.4232616436 -0.7991527526  1.7862424966  0.9245871606
##  [531]  1.4789027714  0.5082842382  0.5909924770  0.2628569331  0.0999071086
##  [536]  0.2573502406  0.2210557682  0.3730258909  1.5493034649  1.0965330392
##  [541] -1.5495227782  1.5431556238  0.4169658893  0.3398523233  0.7503272125
##  [546]  0.9905081721  0.5986095850  0.9317406453  1.0442965469  1.5725928542
##  [551]  0.2349309292  0.1806821137  1.4927460281  0.7709114498  0.1280675069
##  [556]  0.2110163885 -1.2167966828  0.9526798654  0.4755003508 -0.2276517235
##  [561]  0.3432512659 -1.0910779971 -1.4212738902  1.9497313013  0.9564841557
##  [566]  1.5961100067 -0.4170961569 -0.3290207493  0.9453937225 -0.0144719459
##  [571] -0.4298635115  0.3845195846  0.2736228688 -0.0011065435  0.0147534105
##  [576]  0.8293443584 -0.8095777110  1.2447117034 -0.4188321281  0.1903247048
##  [581]  1.0476286891 -0.1689846927  1.3630678446  1.2617921796  1.9235034607
##  [586] -0.1476377629  0.6791634502  0.0282991702 -0.2543018195  1.1126939373
##  [591]  0.8571565912  0.3593275481 -0.6829975579 -0.1010387403  1.4079762321
##  [596]  0.3944469671  0.7782489296 -0.2339522462  0.9215314618  0.9618375804
##  [601] -0.9441037866 -0.8266244361  0.7431062138 -0.5625953310  1.0306263663
##  [606]  0.9984922280  1.8714848495  1.7654042267 -0.9595234788  0.9179350529
##  [611]  1.0491977951  0.4432825895  0.7727816690  0.5307512778 -0.1677268339
##  [616]  0.5817337339  0.8604991706 -0.0525465025  0.6240332223 -0.2772006403
##  [621] -0.4024612243  0.6941571846  0.4485560140  0.8793664019 -0.8464612407
##  [626]  0.9138496974  0.9410431255  0.7692973710 -0.4222188447  1.0280139495
##  [631]  1.1434215901  1.1305805472  0.5252516423  0.8571565912 -0.5858868756
##  [636]  0.5649421760  0.2471863661  1.3780453248  0.3119602021  1.7559105589
##  [641] -0.2235686386  0.7559230036 -0.3365761931  1.1665520209  1.0800293336
##  [646]  1.1618255415  0.3077804771  0.0004486654 -0.5987714376  0.1833819627
##  [651]  0.9176569994  1.0962836496  0.3265237268 -0.8433383945 -0.1903283335
##  [656] -0.0811172788  0.9295959827  1.1156730840 -0.1480053112  0.9453002115
##  [661] -0.5515203797  2.4695639309  1.6351185902  0.8296122939 -0.9223523316
##  [666]  0.5693949146  0.8898516842 -0.0523707508  0.6612565512  1.3363049056
##  [671]  0.7474569799  0.6231658175  0.8520522713  0.9971572453  2.3152928482
##  [676]  0.2123212976  0.9425631324  0.9940576893  0.0904079084  0.2971800444
##  [681]  0.0059577880 -0.0911015611  0.7868615195  1.4246721546  0.2070202178
##  [686]  0.4343913758  0.6693519800  0.5112471108 -0.4926734490 -0.5953314014
##  [691] -0.3164553293 -0.4003100799  0.8517342194  0.0830800622 -0.8532354817
##  [696]  0.2620558208 -0.5937830071  0.3505905058 -0.4048026135  1.4908929598
##  [701]  1.0009090588  1.2740426496  0.2616753240  0.2216486540  0.0451529030
##  [706]  0.4964569521  0.8948657101  0.2824009795  0.8640150571  0.4833520471
##  [711]  0.7600166540 -0.9379912132  0.3217929239  0.3341097368  0.3161248696
##  [716]  0.1924903431  0.6177423108  0.6857813966  0.5665299892  0.6773990217
##  [721]  1.2687502359  0.1282805825 -0.5013775500  1.9662604232 -2.0284642254
##  [726] -0.1208529663  0.2679805952  1.0647858163  0.2856569130  1.5971050745
##  [731]  0.9602559551 -0.1911540425  0.9508401472  0.3848272375  0.5064959604
##  [736]  0.5668586842 -0.3374928668  0.6375799385  0.7299261208  0.8136534110
##  [741]  0.4899115345  1.2044368527  0.8298334493  0.9726133979 -0.2686297725
##  [746]  0.0294793502  1.2458134030  0.7944216128  0.5127532772  1.0735755728
##  [751] -1.6060926379 -1.4224725335 -0.4829255443 -0.7494653527  0.2840457679
##  [756] -0.0913318614 -0.4938964243  0.9550735547 -0.0204428626  0.8973363100
##  [761]  1.1339296957  0.8798167583  0.3699666461  0.6366541434  0.6006655283
##  [766] -1.0662175512  0.6914841885  0.6823881042 -0.6393196670  0.0418922765
##  [771] -0.0680446663  0.8109513705 -0.7402848701  0.0618718732 -0.4800655723
##  [776] -0.3866081672  0.6614749681  0.0135694874  0.6061111994  0.1884519048
##  [781]  0.3841570732 -0.9779519139  0.2179110130  1.2681995909 -0.0550722972
##  [786]  0.9636832957  0.5614169282 -0.2491522688 -1.0205418869 -0.3655205667
##  [791]  0.3859422033 -0.1736146873  0.7327648505  0.7463122391 -1.0983879146
##  [796]  0.7653008721 -0.5237528359  1.0474052748 -0.5887022560 -0.5833323207
##  [801]  0.7760063153  0.8229710120 -0.2464365818  0.0758647470 -0.4285593984
##  [806]  0.5587846650 -1.4843372934  0.2684485234 -0.6454429549  1.0637352075
##  [811]  0.6079160457  1.3565260978  0.8802928287  0.1496030973 -0.1480053112
##  [816]  0.5247488207 -0.3389820335 -0.4227216048  0.6177160884  0.5116665373
##  [821]  0.9677538758 -0.1716778262 -0.6934919119  0.1347890552  0.0623571345
##  [826]  0.8164482117 -0.4631533753 -0.1450283841  0.8569286645 -0.5913084089
##  [831] -0.5832157101  0.6397062036  0.2851178462  0.2978898142  0.4342450288
##  [836] -0.8554149299  1.1039010997  0.3771539075  0.6046611473  0.8051841893
##  [841]  1.0120695542  0.9952038186  0.3887601090 -0.2703362981  1.3861934771
##  [846]  0.7210468369 -0.5086927156 -0.3844967524  0.6005691424  0.8709324950
##  [851]  0.9846161305  1.8877598262  1.1260445602  0.9714685146  1.1463038850
##  [856] -0.1151934856  0.8071077534  0.8673950791  0.5086819358  0.1864936793
##  [861] -0.8326347627  1.3841844044  0.5648908020  1.2663851133  1.4783659844
##  [866]  0.5458174513  0.5095274979  0.8916115423 -0.8998911978  0.9702963907
##  [871] -0.3625724310 -0.7735118275  0.6704262272  0.8703816960 -0.4079991500
##  [876]  0.4623776725  0.6156920304  0.7113665051  0.6716884081 -0.9784423291
##  [881]  1.3535506217  0.4457346909  0.6245066324  1.1227294545 -0.4982517965
##  [886]  0.7011183223  0.7436665021 -0.1458888765  0.8657958822  0.5553133603
##  [891]  0.4030972436 -0.8390950887 -1.6536436118 -0.1007267609 -0.4804082206
##  [896]  0.4159894294  1.6655971129  0.0089515973  0.2054478363  1.0762970599
##  [901] -0.1248476437 -0.7691433866 -0.5251157106 -0.3337893818  0.4751495688
##  [906]  2.0351349858  0.4055161980  1.8831764240  1.6084448617  0.3585450805
##  [911] -1.0434237676 -0.1261412803  0.9448028972  0.6719352568  0.9916481853
##  [916]  0.4422286987 -1.4551826222  0.3336574804 -0.4812191468  1.2903300654
##  [921] -0.0637343668  0.6290491787  0.9666553104  1.2032591295  1.5922784501
##  [926]  0.0199610473  0.9832110108  0.4501019150  1.4510221411  0.4332636778
##  [931]  0.2260409150  0.1494852393  0.8936360065  0.3146999850  0.4873326656
##  [936]  0.5180067466  0.5563256632  0.8429452857  1.0108297577  0.9255918810
##  [941]  1.3484220106  1.1126152190 -0.7002356652  1.2913485875  0.5521127458
##  [946] -0.8348090276 -0.7330892389  0.7772799426  0.4511242445  0.1442493295
##  [951]  0.6330011905 -1.4190877653  0.9879069578  0.8179635932 -0.3268315717
##  [956] -0.5146911291  0.0390728658 -1.2603182483  0.8258459491  0.1428520688
##  [961]  0.7760072520 -0.5139730493 -1.1097311467 -0.3139271812 -0.2655093928
##  [966]  1.0365573337 -0.9138595828  0.8415531703  1.0411401233  0.8580423541
##  [971]  0.6228535883  1.4139841465  1.9475010799 -0.1945870970  1.1249443388
##  [976]  0.6632883373  0.0708243272 -1.5682772781 -0.3111684205 -0.0022117425
##  [981]  0.6757368492 -0.6664284598  1.1835609532 -0.8380218864  1.5409428702
##  [986]  0.3268130773  0.4783936801  0.1124085580 -0.9803653986  0.4293937965
##  [991]  0.1655530348 -0.0142955145  0.2436367559 -0.3393465072 -1.1193094775
##  [996] -0.7713290157  0.1066233022  2.1005298433  1.7660214448  0.6902307757
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
##   0.59129047   0.25229581 
##  (0.07978294) (0.05641164)
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
## [1] -0.06449201  0.63802144  0.24268393  1.40658970  0.69901169  0.67434963
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
## [1] 0.0151
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9441363
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
## t1*      4.5 -0.03993994    0.899118
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 4 6 7 8 9 
## 1 1 2 1 4 1
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
## [1] 0.0035
```

```r
se.boot
```

```
## [1] 0.9195343
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

