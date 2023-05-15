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
## 0 1 3 4 5 6 7 8 
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
## [1] 0.004
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
## [1] 2.747544
```

```r
UL.boot
```

```
## [1] 6.260456
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.2
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
##    [1] 5.4 3.0 2.9 5.7 6.6 5.0 4.1 5.2 4.9 5.2 3.9 3.8 3.9 1.9 4.1 2.9 4.1 5.9
##   [19] 4.6 4.8 4.7 2.6 5.0 5.0 4.5 4.7 4.4 4.7 4.5 3.2 4.7 3.0 5.2 3.8 3.7 4.7
##   [37] 3.7 6.3 3.9 2.8 4.2 4.7 4.0 3.1 5.8 5.7 3.3 6.0 4.3 3.0 4.0 5.7 5.3 4.6
##   [55] 4.8 3.9 2.8 4.4 3.8 4.0 4.0 3.1 3.4 4.6 5.8 4.7 4.7 5.1 4.7 5.7 4.8 4.6
##   [73] 4.5 4.2 4.6 5.6 4.2 5.5 3.6 3.6 4.9 3.2 5.5 4.4 4.7 4.2 3.0 3.3 4.7 4.8
##   [91] 3.4 5.4 5.6 5.0 3.7 5.0 4.8 4.8 4.6 5.5 4.7 3.6 3.8 4.7 3.5 5.5 2.8 4.1
##  [109] 3.6 3.5 2.8 3.9 5.3 3.0 4.9 4.6 6.2 6.3 3.6 6.1 5.3 5.0 3.1 4.9 4.7 3.4
##  [127] 4.4 3.5 4.8 4.6 4.7 4.7 5.5 4.6 4.5 2.2 3.7 4.7 4.7 1.9 4.9 5.2 4.2 2.6
##  [145] 4.2 5.9 4.5 4.9 3.8 4.5 5.2 6.0 5.1 3.9 4.7 3.9 3.1 5.7 2.8 4.7 2.9 7.1
##  [163] 4.5 5.0 2.4 4.3 3.2 4.6 4.4 4.3 2.6 4.7 4.7 4.5 6.3 4.8 4.9 4.9 5.3 5.3
##  [181] 3.8 3.9 5.2 4.3 4.0 4.6 5.1 4.9 4.6 4.2 3.7 4.8 3.7 3.0 4.0 5.9 3.3 4.5
##  [199] 4.0 5.9 4.7 4.5 3.6 4.6 3.8 4.5 4.1 4.1 4.7 3.3 4.8 4.2 3.9 3.1 4.8 5.4
##  [217] 4.2 4.0 4.0 4.1 4.4 5.6 4.9 5.8 5.0 6.0 3.2 3.3 4.6 3.1 6.0 3.6 4.7 4.5
##  [235] 4.0 3.4 3.7 4.2 4.3 5.0 3.0 6.3 5.1 3.1 5.5 6.6 3.6 4.2 5.6 4.3 5.9 4.7
##  [253] 3.5 4.5 5.1 4.0 4.9 5.3 3.5 4.5 4.7 4.9 4.7 5.2 5.5 3.4 4.9 2.9 4.9 5.1
##  [271] 6.9 4.8 5.5 5.0 4.5 3.5 4.7 4.1 5.1 5.5 1.9 2.5 4.8 4.7 4.1 4.9 4.5 6.2
##  [289] 3.4 4.4 5.9 5.6 4.6 3.8 4.3 3.9 3.8 4.6 3.4 4.4 5.0 4.7 5.7 5.6 2.9 6.8
##  [307] 4.4 4.9 4.2 4.4 4.2 4.0 5.7 5.2 4.6 5.4 5.9 5.0 5.5 5.4 5.2 4.9 4.2 5.3
##  [325] 4.4 6.1 6.2 4.5 6.3 4.9 3.3 4.6 3.2 3.4 5.5 6.2 2.9 4.8 5.0 4.2 4.2 4.8
##  [343] 4.2 3.8 4.6 4.2 4.7 5.6 4.1 4.8 4.5 3.0 4.5 4.5 5.0 4.8 4.3 3.3 6.6 4.9
##  [361] 3.4 5.4 4.8 5.2 3.8 4.4 3.2 4.5 5.9 4.3 5.0 5.2 4.5 5.2 3.6 5.8 4.5 5.2
##  [379] 4.6 4.7 3.7 5.5 7.0 4.6 5.7 2.6 5.0 4.3 6.7 4.5 4.3 4.0 4.8 6.1 4.8 3.8
##  [397] 4.8 5.3 4.1 5.4 4.5 4.8 3.2 4.5 2.5 4.2 4.3 6.0 5.1 5.5 4.4 3.9 3.5 4.9
##  [415] 4.2 3.8 5.5 4.6 4.5 5.7 4.9 5.4 3.9 4.1 4.1 3.7 5.3 4.6 4.4 4.1 4.1 5.8
##  [433] 2.6 5.7 4.1 4.6 5.1 4.3 4.5 3.0 2.7 5.5 5.5 5.2 3.8 5.1 3.7 4.0 5.9 3.8
##  [451] 3.9 5.5 5.1 4.7 4.6 4.2 4.1 3.8 3.7 4.6 4.6 5.8 4.6 3.9 3.9 6.0 4.4 5.2
##  [469] 3.9 3.9 5.4 5.8 4.2 4.3 3.9 4.0 6.4 5.1 4.5 2.7 4.7 5.0 3.6 5.1 5.0 4.5
##  [487] 5.9 6.9 2.4 4.0 5.9 3.2 3.1 2.9 5.6 5.0 4.3 5.9 4.8 4.7 4.4 5.8 4.2 5.7
##  [505] 3.9 4.7 3.1 3.9 3.1 3.9 5.2 2.9 4.2 4.6 5.9 5.2 4.7 5.8 4.8 5.9 4.5 3.8
##  [523] 4.3 3.5 4.5 3.7 4.3 5.4 5.0 6.1 3.7 3.2 5.1 3.6 4.5 4.3 3.0 4.0 5.7 4.2
##  [541] 6.1 4.3 5.6 3.3 4.5 3.3 3.3 6.1 5.1 6.3 4.2 4.5 4.5 7.0 3.4 4.6 4.1 4.4
##  [559] 5.4 5.8 3.7 5.0 3.1 5.4 2.8 2.9 4.4 5.6 4.0 4.6 6.4 4.1 4.4 5.3 5.0 5.4
##  [577] 4.9 4.8 5.2 6.2 2.3 5.4 3.4 6.5 5.2 4.1 4.2 4.9 5.9 3.4 3.9 5.5 4.0 4.6
##  [595] 4.1 2.7 3.7 6.2 4.4 4.1 4.2 5.7 4.8 4.7 5.8 5.6 3.5 4.7 4.4 4.2 6.4 2.4
##  [613] 2.7 4.6 5.2 6.0 5.1 4.2 3.1 4.5 3.5 3.3 5.0 4.6 5.6 3.3 4.2 2.0 3.7 3.7
##  [631] 4.2 4.8 4.3 5.2 3.6 4.4 5.8 4.4 4.6 4.0 4.2 5.0 3.8 3.0 5.4 3.1 4.5 4.1
##  [649] 4.8 3.5 5.3 4.1 5.6 4.8 5.1 5.2 4.6 4.7 4.0 6.0 5.2 3.7 5.1 3.7 5.9 6.2
##  [667] 5.0 4.7 4.4 3.1 4.7 4.4 5.3 4.5 4.8 3.9 4.8 4.7 4.0 3.6 4.2 4.3 4.1 3.6
##  [685] 4.2 3.7 4.5 5.5 4.8 4.5 4.3 4.4 3.5 4.8 5.1 4.8 5.0 5.4 3.9 5.6 5.1 3.3
##  [703] 4.0 4.8 3.7 5.7 3.5 4.4 4.2 5.8 3.2 6.2 4.6 2.5 6.1 4.3 5.5 3.7 5.9 4.5
##  [721] 3.4 4.1 4.9 2.9 5.3 4.1 3.3 6.1 4.2 3.6 3.2 5.8 4.9 4.2 4.9 5.3 3.9 4.4
##  [739] 4.6 4.6 3.7 4.7 5.2 3.7 6.0 4.0 4.5 5.1 4.4 4.2 4.2 5.8 4.7 6.4 5.1 4.5
##  [757] 5.2 3.0 5.3 4.1 3.8 5.3 4.4 4.0 5.3 2.7 4.3 3.6 3.1 6.4 3.9 3.4 3.8 3.7
##  [775] 3.8 6.6 2.3 3.5 5.5 5.1 4.7 4.7 5.3 5.6 2.7 4.8 5.0 4.3 4.9 4.1 4.5 5.3
##  [793] 3.8 5.0 3.8 4.9 4.4 6.0 4.0 3.0 5.8 4.5 4.3 4.5 2.8 6.6 3.1 4.4 4.0 4.1
##  [811] 5.6 3.8 4.9 2.6 4.1 5.5 4.7 5.5 4.8 5.6 5.1 3.2 5.0 4.5 4.5 5.6 3.4 5.7
##  [829] 5.6 6.5 3.0 4.4 4.0 4.9 5.7 3.4 2.2 4.4 3.5 4.0 3.6 4.8 3.9 4.8 4.3 5.5
##  [847] 4.8 6.1 3.4 4.6 5.8 4.7 4.7 5.8 4.7 4.3 5.2 5.0 5.8 3.0 4.3 5.0 4.7 4.0
##  [865] 3.9 4.6 3.2 4.1 5.9 5.5 4.8 6.8 4.0 4.2 4.6 2.6 5.1 2.9 4.8 5.0 4.3 6.2
##  [883] 2.2 5.9 4.7 5.4 5.1 5.7 4.8 4.0 5.4 3.8 4.3 3.7 2.9 5.8 5.0 3.8 5.0 2.2
##  [901] 4.8 4.4 5.2 5.4 3.9 3.5 4.5 5.0 3.5 3.9 5.2 2.8 4.8 3.9 3.7 4.7 2.9 4.3
##  [919] 3.5 4.7 5.2 4.7 3.8 4.3 2.1 4.4 5.7 4.1 4.3 4.7 3.0 2.8 4.1 4.7 2.3 5.1
##  [937] 5.6 4.4 5.0 4.7 5.9 6.1 3.5 4.6 3.8 3.4 3.6 3.6 5.2 4.5 4.4 3.5 4.3 3.1
##  [955] 2.5 3.9 3.9 6.1 3.7 5.6 5.3 4.3 5.3 5.9 5.8 4.4 4.8 4.0 5.6 3.9 5.1 6.1
##  [973] 3.2 5.5 3.2 4.9 3.6 6.1 3.8 2.8 4.8 2.7 4.4 4.9 2.8 5.3 3.1 4.1 4.4 5.4
##  [991] 4.2 4.5 4.4 4.7 3.9 3.6 5.2 3.3 5.0 4.3
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
##   2.6   6.3
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
##    [1] 4.4 5.5 4.5 5.9 5.1 4.5 5.0 5.9 4.2 5.5 5.0 4.5 4.0 5.4 4.1 4.4 3.8 5.5
##   [19] 5.1 5.4 4.7 4.1 4.6 5.5 4.7 5.2 4.0 3.9 4.7 4.9 6.2 5.4 4.3 2.4 4.4 4.8
##   [37] 4.2 3.5 2.9 4.8 5.8 3.6 5.7 4.0 3.5 3.5 3.4 3.1 3.3 4.2 3.6 4.6 4.3 4.7
##   [55] 5.4 3.8 4.9 6.1 4.9 4.7 6.0 5.9 3.6 5.9 2.2 4.4 6.0 5.2 3.2 3.7 5.7 5.0
##   [73] 6.1 3.4 5.9 3.3 4.0 4.8 3.8 6.2 4.7 4.1 6.3 3.4 4.6 3.3 4.1 6.9 4.0 4.1
##   [91] 4.2 4.9 6.9 4.6 3.7 5.5 3.6 4.9 3.8 5.9 5.0 4.4 4.7 4.8 6.2 4.6 6.6 3.1
##  [109] 4.7 5.4 5.3 4.0 3.7 4.7 3.1 5.8 3.6 4.4 2.9 4.1 4.2 5.0 3.2 3.5 3.2 4.3
##  [127] 2.6 4.6 4.9 4.6 3.5 3.8 4.6 5.4 3.5 4.2 5.1 4.6 4.4 4.8 4.3 5.2 2.6 4.9
##  [145] 5.5 5.2 3.8 5.6 3.2 5.9 6.5 3.7 4.3 3.0 3.4 3.5 4.0 4.0 4.7 4.9 4.1 3.3
##  [163] 4.6 4.5 5.1 4.4 4.3 6.2 3.8 3.7 4.4 4.7 3.3 3.2 5.9 4.2 4.0 3.4 3.1 3.8
##  [181] 3.3 4.7 5.5 4.4 5.7 3.1 4.9 5.0 4.4 5.0 4.6 3.6 4.3 4.7 5.7 5.9 4.2 5.2
##  [199] 4.4 4.6 4.9 5.7 2.7 4.4 5.4 3.4 2.9 5.2 3.5 5.0 4.7 5.3 5.7 5.8 4.6 3.5
##  [217] 4.2 5.2 4.0 4.8 4.0 5.2 4.1 4.3 4.0 4.0 3.7 4.2 3.7 4.6 5.0 5.8 4.1 5.2
##  [235] 4.5 5.6 3.0 5.4 3.6 3.4 3.7 3.3 3.2 4.0 4.7 4.4 4.4 3.6 3.5 3.7 3.0 4.2
##  [253] 4.4 5.4 5.0 3.4 3.1 4.8 6.3 3.4 5.2 2.8 5.2 4.3 5.2 6.3 5.3 5.2 3.9 6.3
##  [271] 5.4 3.8 4.7 4.6 4.7 3.5 5.4 4.9 6.4 5.2 6.0 4.7 3.4 3.6 3.9 3.6 3.4 3.6
##  [289] 2.1 5.0 3.8 4.3 3.4 5.0 4.2 3.2 3.7 6.3 5.5 3.9 5.4 6.0 4.8 4.8 5.8 3.2
##  [307] 3.3 5.5 4.7 5.8 4.2 5.6 5.3 3.2 5.2 3.5 3.8 4.3 4.8 5.5 4.5 4.6 4.9 4.7
##  [325] 3.5 4.5 2.6 3.8 4.5 4.8 4.3 4.3 4.2 6.2 4.4 3.6 5.0 2.5 4.1 4.8 2.8 4.8
##  [343] 3.8 5.8 4.9 3.5 3.2 2.9 4.6 6.3 4.8 4.6 2.9 4.4 3.5 4.2 5.7 2.4 3.2 5.9
##  [361] 6.3 4.4 4.2 5.3 3.9 3.1 4.2 5.4 5.1 4.4 3.7 4.7 6.3 4.5 4.6 3.5 4.6 2.7
##  [379] 4.8 5.4 5.4 3.6 2.4 4.9 5.6 2.3 3.6 6.1 4.4 5.0 3.4 3.5 5.4 4.8 4.5 4.2
##  [397] 5.6 6.4 5.5 2.3 3.5 4.8 4.0 3.0 4.1 5.2 5.1 3.1 4.1 4.6 5.2 5.5 4.0 3.4
##  [415] 5.1 5.3 6.0 4.6 4.8 5.4 3.8 4.3 4.3 4.4 3.6 4.1 4.1 5.4 4.1 5.5 3.8 4.5
##  [433] 4.5 3.3 4.8 4.5 4.9 5.2 4.4 4.8 4.2 4.4 3.8 4.5 4.1 4.0 5.4 5.7 4.2 3.1
##  [451] 4.9 3.5 5.4 4.1 6.2 4.7 4.8 5.4 4.5 6.2 5.4 5.4 4.6 4.8 5.7 5.4 4.9 4.3
##  [469] 5.6 3.6 5.2 4.9 4.8 4.5 4.7 4.9 4.4 3.2 3.8 4.3 5.6 6.1 4.4 4.9 5.1 6.5
##  [487] 3.2 5.1 5.7 5.2 4.4 4.6 5.1 3.7 4.0 5.6 5.3 5.7 3.7 6.4 5.1 5.6 3.5 4.5
##  [505] 5.0 5.7 3.8 4.1 4.2 3.8 3.5 4.5 5.1 3.1 4.6 6.7 4.4 3.4 4.8 4.5 4.9 4.7
##  [523] 2.7 4.2 4.9 3.6 2.3 6.0 5.5 2.8 5.2 5.4 5.5 4.6 4.5 6.3 4.2 3.8 3.7 5.0
##  [541] 4.5 4.0 5.1 4.1 3.0 4.0 3.7 5.2 5.7 4.7 5.5 3.4 4.7 5.5 3.8 5.2 3.9 3.0
##  [559] 3.2 5.3 5.1 4.3 3.6 4.6 5.7 4.8 4.1 5.1 4.0 4.8 4.9 5.5 3.6 4.1 4.1 5.4
##  [577] 5.1 4.4 3.0 4.1 5.8 3.5 4.5 4.2 3.6 6.3 3.0 4.7 5.6 4.3 4.5 4.4 4.2 4.9
##  [595] 3.7 5.5 4.9 4.5 3.9 4.8 5.0 4.6 4.1 4.8 4.7 4.5 5.2 4.1 4.9 3.0 5.1 3.7
##  [613] 4.1 5.0 5.3 4.0 4.3 4.2 5.4 5.3 3.1 4.3 5.5 3.2 3.8 4.9 4.7 5.3 4.9 4.0
##  [631] 4.6 3.9 4.0 5.5 4.7 4.4 4.0 4.3 4.9 4.9 3.5 4.4 6.1 4.4 5.0 3.3 4.3 5.3
##  [649] 3.9 3.9 3.3 5.7 3.8 4.4 4.2 4.1 6.3 5.9 4.1 1.9 3.6 6.4 3.6 5.7 5.7 3.5
##  [667] 4.2 5.4 4.7 4.5 3.9 5.4 4.5 4.9 4.0 3.6 4.0 3.4 3.7 5.0 4.9 3.5 4.6 6.2
##  [685] 4.7 4.6 3.0 4.9 4.7 2.9 4.0 4.1 6.3 4.6 5.4 4.5 4.2 4.1 2.8 3.8 5.1 5.4
##  [703] 4.0 3.8 3.7 4.0 4.8 4.1 4.3 4.1 5.4 4.5 4.4 4.7 4.9 4.1 4.3 2.3 4.5 4.5
##  [721] 4.1 3.9 3.4 4.9 6.2 5.2 4.3 3.8 4.0 5.2 4.7 5.7 3.4 4.2 4.5 4.6 5.0 3.6
##  [739] 5.0 4.6 3.5 4.5 4.5 3.6 5.0 3.5 3.3 4.6 4.7 3.8 5.2 3.2 6.0 5.9 3.4 3.5
##  [757] 4.4 4.9 3.7 4.2 5.1 4.2 5.2 5.2 5.0 3.8 5.8 3.9 4.4 4.3 4.5 4.1 5.0 4.2
##  [775] 4.2 5.5 4.9 6.3 3.5 4.2 3.9 5.9 5.0 3.1 5.3 5.1 4.2 4.6 3.1 5.2 5.5 5.2
##  [793] 4.0 3.6 5.6 4.5 5.8 3.9 3.3 5.1 5.7 4.8 3.3 4.3 3.6 4.8 4.2 3.7 5.6 3.7
##  [811] 6.5 3.8 4.1 4.5 3.8 4.5 2.4 4.9 4.1 4.3 5.5 4.6 3.6 4.0 3.2 3.7 3.8 4.0
##  [829] 2.4 5.1 3.6 5.1 5.4 5.0 3.3 3.9 4.4 5.7 6.3 4.2 4.1 5.2 4.7 3.1 3.5 4.5
##  [847] 4.1 4.0 5.6 5.0 5.2 5.7 6.1 4.6 4.8 3.6 5.0 3.8 4.7 4.4 5.1 4.0 3.7 5.4
##  [865] 5.6 4.9 4.5 4.6 3.9 5.1 4.4 4.0 4.2 3.6 4.4 4.7 5.2 4.9 4.0 4.6 4.2 4.2
##  [883] 4.8 4.1 4.2 4.4 4.2 3.9 4.6 4.1 4.3 4.6 6.2 6.6 4.5 5.7 5.9 4.9 4.7 3.3
##  [901] 4.3 3.9 3.0 3.4 6.4 4.2 4.4 5.1 5.0 5.6 3.9 4.4 5.5 5.7 4.0 3.4 4.1 4.2
##  [919] 5.4 3.5 4.0 3.7 5.0 3.7 4.9 2.9 3.5 4.8 3.5 5.3 3.5 3.8 4.0 3.7 4.9 5.4
##  [937] 4.7 5.3 2.5 3.8 3.8 5.1 4.5 3.3 4.5 5.5 4.5 5.4 4.7 2.6 4.9 5.6 5.1 4.4
##  [955] 4.0 4.5 4.8 3.7 3.9 6.0 5.8 4.6 3.1 4.1 5.2 5.9 6.0 5.2 3.8 5.6 5.1 4.7
##  [973] 4.6 6.0 3.9 3.3 4.3 6.0 5.4 5.3 5.4 4.4 5.5 4.4 5.5 5.2 5.8 4.3 4.9 4.3
##  [991] 3.4 4.8 4.2 5.4 4.5 4.6 5.7 5.7 3.7 5.8
## 
## $func.thetastar
## [1] 0.004
## 
## $jack.boot.val
##  [1]  0.47567568  0.37534626  0.29944444  0.10415512  0.01135135 -0.07134670
##  [7] -0.16169014 -0.25317919 -0.34336283 -0.41384181
## 
## $jack.boot.se
## [1] 0.8757957
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
##    [1] 4.2 4.7 5.3 4.2 4.6 3.8 4.3 3.7 4.8 4.2 3.3 5.5 4.8 3.7 3.0 4.5 3.9 5.5
##   [19] 5.1 3.7 4.1 3.7 3.1 4.5 4.6 4.6 4.9 3.8 4.7 2.5 5.3 4.4 6.3 5.3 6.4 4.4
##   [37] 5.2 4.8 5.0 4.1 4.4 4.4 4.7 3.8 3.5 4.9 4.9 3.7 5.0 4.0 4.4 5.5 4.1 4.9
##   [55] 4.9 4.8 4.2 5.3 3.6 5.2 5.9 4.5 4.5 4.8 5.0 3.1 3.2 3.4 4.9 5.3 4.7 6.1
##   [73] 4.7 3.4 2.1 3.5 4.5 3.8 6.2 4.9 5.5 4.6 4.2 5.1 4.3 4.5 4.6 5.9 4.9 4.4
##   [91] 3.8 3.1 3.8 3.6 4.4 3.0 4.2 3.5 2.8 4.9 4.4 3.1 6.0 4.9 5.5 2.5 3.8 6.5
##  [109] 6.6 6.2 4.0 5.6 5.3 4.0 4.4 4.7 5.4 4.1 5.6 3.5 4.7 4.9 3.2 5.5 4.2 3.0
##  [127] 4.5 6.2 4.2 3.7 4.8 5.9 4.2 4.6 4.9 4.8 4.4 4.6 4.2 1.6 4.7 5.0 5.4 5.8
##  [145] 6.7 4.8 3.6 6.4 4.4 5.0 3.3 4.9 3.6 5.0 4.2 5.2 5.6 4.0 3.2 5.0 4.5 3.7
##  [163] 5.0 3.3 4.7 4.7 4.0 4.5 5.3 4.0 4.7 4.8 4.7 4.9 5.2 3.3 4.9 5.3 4.7 4.0
##  [181] 5.0 4.9 5.0 4.4 3.4 5.1 4.9 4.9 4.2 6.7 2.9 3.9 5.3 6.1 4.8 6.1 2.8 4.4
##  [199] 3.9 3.6 5.0 4.0 4.3 5.9 2.5 4.7 6.3 3.9 5.5 3.8 3.9 4.6 6.6 4.3 5.4 4.5
##  [217] 3.6 3.6 4.2 5.6 5.0 5.7 5.0 3.9 4.0 4.5 5.0 4.7 6.1 5.7 4.1 5.0 5.4 4.7
##  [235] 5.4 5.3 2.9 6.2 5.4 4.4 6.6 3.7 4.9 4.5 4.7 5.6 4.5 5.1 4.9 5.6 5.0 4.2
##  [253] 5.5 5.6 5.0 5.2 4.7 4.3 3.7 4.2 5.2 4.0 4.7 3.9 6.2 3.3 5.7 3.2 5.0 5.3
##  [271] 2.1 4.8 4.9 4.8 5.0 4.0 4.4 4.6 4.1 5.5 5.1 4.9 4.5 3.6 3.8 3.2 6.5 5.2
##  [289] 4.1 5.6 4.9 4.9 4.7 4.5 5.7 2.6 4.9 4.4 4.7 3.9 3.8 4.5 5.8 3.9 3.9 6.4
##  [307] 4.8 5.1 5.9 3.7 4.0 3.2 3.4 4.2 3.2 2.7 4.4 4.3 4.2 3.6 4.8 4.7 5.3 4.2
##  [325] 5.0 5.3 4.9 4.4 5.9 6.1 5.8 4.7 5.9 5.6 3.0 3.3 4.6 6.9 4.8 3.0 4.6 5.1
##  [343] 3.6 4.3 3.4 3.4 3.7 5.5 6.1 4.6 4.2 5.7 4.5 4.0 5.3 5.7 4.7 3.6 4.9 4.7
##  [361] 4.4 3.0 4.4 4.3 3.4 4.3 5.5 5.8 5.5 4.1 3.4 4.6 5.6 5.8 4.5 4.3 4.3 4.7
##  [379] 4.6 4.2 4.0 4.6 3.4 3.4 3.9 5.4 2.8 3.7 4.5 2.8 6.4 4.2 3.3 4.6 3.7 3.5
##  [397] 5.0 5.3 5.2 4.3 6.1 4.1 4.1 5.2 5.6 3.9 4.3 3.6 3.6 4.8 5.6 3.8 5.4 5.4
##  [415] 4.5 4.2 3.7 3.7 2.7 3.6 2.6 4.2 4.9 4.8 4.5 6.3 3.7 4.1 5.0 6.4 4.7 5.0
##  [433] 2.1 4.6 5.1 3.9 4.6 4.7 4.6 4.8 5.2 2.7 3.8 5.1 4.2 4.2 4.5 5.1 5.8 3.0
##  [451] 3.9 3.5 4.3 5.3 4.8 3.9 4.3 3.7 2.4 3.5 3.5 4.2 4.5 4.9 3.6 4.7 5.1 4.8
##  [469] 5.8 3.7 5.8 4.8 2.5 4.8 5.5 3.7 3.0 3.8 4.8 5.3 4.5 4.8 4.4 3.0 3.6 5.2
##  [487] 3.9 4.0 2.7 3.5 4.6 2.9 4.1 4.2 5.4 4.1 4.9 3.9 3.5 4.3 2.0 4.4 5.5 4.7
##  [505] 5.2 5.4 3.6 3.8 4.1 4.0 5.1 4.1 4.9 6.0 4.3 5.2 4.5 5.1 3.6 3.1 3.1 4.8
##  [523] 2.5 4.5 5.4 3.6 5.0 3.5 4.0 5.1 4.8 2.6 5.1 2.8 3.9 4.7 4.3 4.7 2.6 3.8
##  [541] 4.5 3.2 4.2 6.0 4.8 6.4 4.6 5.5 5.3 3.4 4.2 4.8 3.6 4.5 3.5 2.2 3.2 3.9
##  [559] 5.9 5.0 3.2 4.9 3.4 3.6 3.5 4.0 4.3 3.8 5.5 3.8 3.9 3.4 6.5 4.0 2.7 3.9
##  [577] 5.4 4.3 3.3 3.4 4.8 4.7 5.8 4.5 5.0 5.1 4.9 5.2 4.8 4.1 4.8 5.2 3.9 5.3
##  [595] 6.0 4.4 4.1 5.0 4.7 3.2 5.7 4.7 6.2 3.9 5.0 3.2 4.0 4.4 5.3 5.4 4.1 4.4
##  [613] 3.9 3.3 5.0 4.5 5.1 5.7 6.0 4.1 3.4 3.9 2.9 4.2 5.3 4.4 4.5 4.6 2.8 4.7
##  [631] 2.9 3.9 3.9 5.2 2.5 3.6 4.2 3.5 3.7 3.9 4.9 4.6 4.4 5.6 4.7 4.0 3.1 5.4
##  [649] 5.0 5.3 5.2 5.5 4.7 4.1 3.9 6.0 5.2 4.5 3.9 5.2 3.3 4.0 3.3 3.5 4.0 3.0
##  [667] 4.9 4.8 4.2 4.8 4.5 4.2 3.5 3.4 4.0 4.6 3.2 5.5 4.5 5.1 4.6 3.3 4.4 4.5
##  [685] 4.8 4.7 5.3 2.9 6.3 5.3 5.1 4.1 4.6 2.7 4.0 6.1 4.2 3.4 3.4 5.1 4.3 4.1
##  [703] 3.1 2.7 4.3 4.0 4.3 4.1 4.1 5.5 4.4 5.4 4.4 3.7 3.7 5.5 5.1 5.4 6.3 5.4
##  [721] 1.5 4.3 3.7 3.7 5.6 4.6 3.6 3.0 3.2 3.5 4.9 4.0 3.7 3.5 5.1 4.0 4.7 5.1
##  [739] 4.8 5.1 5.9 6.2 4.9 4.2 4.5 3.4 4.3 3.8 4.3 5.1 5.1 2.9 3.6 6.4 5.0 6.3
##  [757] 5.9 4.3 4.6 2.0 5.4 5.0 4.4 4.9 3.5 3.9 4.2 4.3 3.8 5.4 6.4 3.4 3.7 7.3
##  [775] 5.6 3.1 3.5 3.2 4.2 3.9 6.7 4.3 4.3 5.0 3.7 3.5 5.1 4.6 4.9 4.3 4.9 4.5
##  [793] 5.0 4.4 3.8 3.1 4.5 4.2 3.0 4.2 6.0 4.6 6.0 3.7 5.6 4.5 2.8 4.6 4.0 5.9
##  [811] 6.0 3.2 5.7 4.1 3.4 3.9 4.3 6.3 5.5 5.2 4.5 4.1 2.9 4.0 3.8 4.6 4.0 4.9
##  [829] 5.5 4.2 5.0 3.5 4.2 4.7 6.2 4.7 3.6 4.8 6.1 3.7 4.6 3.5 4.5 4.0 4.5 5.1
##  [847] 3.1 3.3 4.9 3.7 5.0 4.9 4.0 4.4 3.7 4.7 5.4 5.4 4.8 5.3 4.7 3.5 4.7 4.7
##  [865] 3.0 3.0 4.8 5.6 3.0 4.1 4.3 3.5 5.3 4.1 2.7 4.6 3.5 5.3 3.9 5.8 3.4 2.6
##  [883] 4.3 5.6 4.3 4.6 4.7 3.6 3.3 4.4 4.2 3.4 5.3 4.9 4.8 4.4 4.8 3.7 6.3 5.8
##  [901] 2.4 3.2 2.6 4.4 5.1 3.8 3.6 5.1 3.6 4.9 4.2 3.0 5.6 3.8 5.0 5.1 6.4 3.4
##  [919] 5.0 3.5 4.0 3.4 4.0 4.7 4.4 5.2 5.0 4.1 2.9 4.2 2.1 3.3 5.5 5.0 4.6 3.9
##  [937] 4.7 6.4 5.3 3.3 5.0 5.1 5.0 2.4 5.4 4.0 4.4 4.2 4.6 2.6 4.2 3.6 4.3 5.3
##  [955] 5.9 5.4 3.5 4.4 4.3 5.0 3.2 4.9 6.0 3.4 3.5 4.3 3.8 4.8 4.7 4.4 5.5 3.7
##  [973] 4.5 4.3 4.5 3.6 2.4 5.5 5.1 6.7 4.6 5.5 5.1 4.0 4.5 4.4 3.2 5.4 4.1 4.6
##  [991] 2.7 6.1 4.5 5.7 5.3 5.0 4.3 4.6 4.0 4.7
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.400 5.200 5.164 5.000 5.000 4.800 4.700 4.600 4.500
## 
## $jack.boot.se
## [1] 0.9106008
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
## [1] 0.5493184
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
##   1.9793209   3.5195347 
##  (0.8215117) (1.6612300)
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
## [1] -0.73864151  0.02506659  0.83106651  0.54415307  1.31096096  0.44293440
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
##    [1] -0.052259672  0.637824421  0.313918925 -0.142093688  0.294503124
##    [6]  0.816231957 -0.011165796 -0.098549699 -0.002037872  0.392684276
##   [11] -0.124496775 -0.104268291  0.364437806 -0.446331834  0.739323784
##   [16]  0.968479650  0.365586728  0.356679689  0.312841655  0.178876890
##   [21] -0.332473812  0.586713507  0.745240550  0.261366257  0.448835930
##   [26] -0.004658357  0.845357634  0.388824741  0.796027703 -0.138405113
##   [31]  0.949553958 -0.331764518  0.129100501  0.377667196  0.778993853
##   [36]  0.236580815 -0.100321032  0.185758694  0.339972204  0.196304909
##   [41]  0.940539132  0.544479214  1.475844432  0.783447031  0.248865959
##   [46] -0.273264730  0.103720324  0.862749376  1.221958061 -0.704166553
##   [51]  0.497479388 -0.043444654  1.036122753  0.752386678  0.025340459
##   [56] -0.661392094  0.248125793  0.479615854  0.103381555  0.011757553
##   [61]  0.494413209  0.594597075 -0.300567000  0.543025679  0.732757190
##   [66]  0.890787946  0.096896414  0.854522356  0.175045657  0.273645450
##   [71]  0.727839200 -0.070799198  0.837319035  0.513033372  0.778640473
##   [76]  0.408060368 -0.052443621 -0.123303230 -0.150554354 -0.401892301
##   [81]  0.725537335  1.735177867 -0.378864067  0.422630399  0.092143781
##   [86]  1.069371808 -0.180731087  0.919157857  0.133747150  0.246129496
##   [91]  0.869703601  0.236075111 -0.074954884  0.401388574  0.364365597
##   [96]  0.597445651  0.442984063  0.005858097  0.835726984  0.657327409
##  [101] -0.243531773  0.940717894  1.205337915  0.508505744  0.634702304
##  [106] -0.227714995  0.161179166  0.051155709  0.166643059  1.045073983
##  [111]  0.388486631  0.210288535  0.420172820  0.303386482  0.129100501
##  [116] -0.277913425  0.943806831  0.722023929 -0.235042820  0.653905329
##  [121]  1.923986520 -0.587057694 -0.207445982  0.269180417 -0.029365584
##  [126]  0.466059862  0.372157941  0.672088099  1.440023766  0.245552684
##  [131]  0.638103616  0.379339913  0.734756135 -0.055413992  0.183808039
##  [136]  0.483338374  0.244893206  0.275052165  0.575975658  0.089797358
##  [141]  0.238233042 -0.095703430  0.381057301  0.098694853  0.712876422
##  [146]  0.209507719  0.629534443  0.192004917  0.217955315  0.987278913
##  [151] -0.386570093  0.354476857  0.524190894  0.363223805  0.577282422
##  [156]  0.552195778  0.182246086  0.182369657  0.752320874  0.226657945
##  [161]  0.259286049  0.327181424 -0.122250493  0.735673384 -0.404376961
##  [166] -0.569842022  0.225261784  0.463396025  0.381503422  0.461255569
##  [171]  0.689339911  1.156112103  0.400796536  0.763600211 -0.110571550
##  [176]  0.065131107  0.751823220  0.362796894  0.868288875  0.107350510
##  [181]  0.929932960  0.504472246  0.103572947 -0.826368025  1.691113542
##  [186]  0.236558944  0.392255038  0.189953794  0.104867862  0.787120499
##  [191]  0.122232581 -0.253877919  0.624035795  0.806210762  0.321143670
##  [196]  0.109564849  0.010547298 -0.268953557  0.303195504  1.881920474
##  [201] -0.985402544  0.288470871  0.874187279  0.860197818  0.796715592
##  [206] -0.203726625  0.948900823 -1.094038389  0.551060863  0.813327449
##  [211] -0.135253958  1.839281677  0.616308385  0.056508231  0.546470559
##  [216]  0.340759211  1.039136638  0.539016684  0.906468626  0.829980655
##  [221]  0.103558382  0.628489293  0.969542859 -0.638775880  0.140783544
##  [226]  0.102347508 -0.572830541 -0.077581881  0.744483551  0.117911895
##  [231]  0.947182478  0.179493140 -0.007784880  0.382477927  0.527975445
##  [236] -0.474734016 -0.141469280 -0.043013152  0.461655002 -0.022314320
##  [241]  0.570743764  0.604957305  0.363093095  0.408123061  1.410371226
##  [246]  0.433823860  0.994793004 -0.203642526 -0.259670155 -0.200978876
##  [251]  0.533180653  0.564424628  1.022640270  1.146215462  0.554737683
##  [256]  0.723952719  0.378722220  0.807631789  0.523280179 -0.288662838
##  [261]  0.230494310  0.044211878  0.758021632  0.342421781 -0.018758208
##  [266]  0.191428693  0.004483420  0.266503902  0.452432672 -0.162764534
##  [271] -0.119784919  0.126235017  1.073110647  1.797914052  0.202933779
##  [276]  0.082043535  0.848623519  0.834737626 -0.274071080  0.397036952
##  [281]  0.619798308  0.382984809 -0.462480011  0.162175679 -0.115472875
##  [286]  1.090000249  0.001636405  0.359594496  0.251406914  0.442240635
##  [291]  0.206672657  1.260020096  0.670446578  0.490218972 -0.528168385
##  [296] -0.701378191  0.413973607  0.588416103  0.146614198  1.177972730
##  [301] -0.165708368 -0.348576100  0.483002078 -0.473424836  0.916438252
##  [306]  0.142178022  0.672235919  0.458182583  0.355866067 -0.007589567
##  [311]  0.500661696  0.269892991 -0.172098959  0.483161500  0.281871603
##  [316] -0.094489822  0.241717047 -0.015848927 -0.435918977  0.529182234
##  [321]  0.422069407 -0.387869461  0.183019203  0.403065472 -0.211567399
##  [326]  1.219760303  0.449887200 -1.335378389  0.660806579  0.345443761
##  [331]  0.448029400  0.185222613  0.356033571 -0.316057364 -0.089638635
##  [336] -0.079202003  0.170629603  0.242976903 -0.057618357  0.052333220
##  [341] -0.925488612  0.793912036  0.215334704  0.462007200  0.352189350
##  [346]  0.207678458  0.872324189  0.088408528  0.941164078 -0.144576020
##  [351]  0.756960612  0.673787217  0.950798122  0.203446570 -0.249749781
##  [356]  0.501397936  1.049465316  1.024231654  0.732313772 -0.167872062
##  [361]  0.047783845  0.929932960  0.381638040  0.113364977  0.513870148
##  [366]  0.836803291 -0.008824141  0.616574160  0.610386812 -0.055236902
##  [371]  0.730666327  0.459889678  0.509112927  0.141074644  0.246312193
##  [376] -0.383426242 -0.156984158  0.102508581  0.250776050  0.581541715
##  [381]  1.035005005  1.121237961  0.160794615 -0.161984359 -0.194220654
##  [386]  1.175764785  0.096264939  0.664768882 -0.021848879  0.088588532
##  [391]  0.500588724  0.323756829  0.665977894  0.166293823  0.016102456
##  [396]  0.654014070  0.860197818  0.012502082  1.387763954  0.872356457
##  [401]  1.050758269  0.214574959  0.199327265  0.869322496  0.528569578
##  [406]  0.356127866  0.172652829  0.819375917  1.767181611 -0.035752761
##  [411]  0.734756135  0.042475021  0.452444875  0.534200018  0.632746331
##  [416]  0.476872158  0.298461039  0.681126379  0.945393693  0.477373680
##  [421]  0.264308400 -0.731698964 -0.388621225  0.646726938  0.361631991
##  [426]  0.355330448  1.045592345  0.448428771  0.085324139  0.685928568
##  [431]  0.661497133 -0.278924021  0.302650158  1.658888780  0.787756625
##  [436]  0.480156784 -0.287223307  0.525220299 -0.138337941  0.173794534
##  [441]  0.351900704  0.194319177  0.654702896  0.114157707  0.683181714
##  [446]  0.376808119  0.623296768 -0.602778365  0.908146198  0.668932513
##  [451]  0.803689588  0.290943646  0.617296235  0.741346183 -0.273029128
##  [456]  0.360204150  0.970949948  0.947752499 -0.604112839  0.306815818
##  [461]  0.483011251 -0.152908096 -0.432199191  0.296415533  1.123291920
##  [466]  0.036635956  0.036993992  0.221682515  0.499178041  0.597867707
##  [471]  0.187578178 -0.243531773 -0.140391033 -0.514396316 -0.067637121
##  [476]  0.996645055  0.246851913  1.136973861  0.282566384  0.834352674
##  [481]  0.875164448  0.326321651  0.207849427 -0.096672149  1.426465645
##  [486] -0.075597779 -0.194911135 -0.935357255  0.200989352  1.178537286
##  [491]  1.213336646  0.312230656  0.534514093  0.644023524 -0.975410692
##  [496]  1.032550863 -0.416010761  0.618287724  0.445089675 -0.449268965
##  [501]  0.530536666  0.140415030 -0.008927974  0.910585513  0.375034546
##  [506]  0.497963349 -0.304119506  1.230177775  0.130812475  0.581541715
##  [511]  1.052482802  0.389090098 -0.121879542  0.231103220  0.692741957
##  [516]  0.774480191  0.895081079 -0.101482691  1.115850169  0.774850788
##  [521] -0.161196515 -0.185465372  0.095830387  0.283176945  0.846427821
##  [526]  0.745628057  0.651983306 -0.148215197  0.797497059 -0.206157722
##  [531]  1.115626659  0.235818786  0.378206509  0.291409714  0.682436670
##  [536] -0.557399576  0.321327891  0.113856188  0.302017917  0.262164858
##  [541]  1.573641634  0.135868628 -0.325551119  0.672597086  0.193242550
##  [546]  0.935240584  0.506043344  0.465611394  1.327368291  0.012019837
##  [551]  0.620662716  0.080490106 -0.489707766  0.200503876  0.382902983
##  [556]  0.264308400  0.330333118  0.857662483  0.352460107  0.515061495
##  [561]  0.056833085 -0.737533695  1.224863627  0.818129542  0.430912973
##  [566]  1.126144415  1.596448313  0.133281107  0.393666130  0.507557803
##  [571]  0.213261235  0.114919488  0.824419475  0.558624937  0.456894412
##  [576]  0.180043576  0.811597017  0.343875107  0.893040911  0.897561808
##  [581]  0.278860703  0.763227285  0.517552098  0.452444875 -0.157631056
##  [586]  0.757405032  0.678631597  0.770192363  1.401416914  0.070133690
##  [591]  0.476992183  0.552195778  0.093937743 -0.502452839  0.815120206
##  [596]  0.052275526 -0.026999716  0.376930345  1.397502395 -0.173774190
##  [601]  0.773395459  0.673787217 -0.217685596  0.154696295 -0.947975209
##  [606]  0.191736581  0.164811339  0.443949578 -0.144579409  0.664211433
##  [611]  0.946377748  0.553692712  0.437652793  0.605882058 -0.504059390
##  [616]  0.662531634  0.318665002  0.187017542 -0.007072192  1.275517193
##  [621]  0.928510941  0.378206509  0.872356457  0.234074408  0.061513864
##  [626] -0.523082258  0.956492971  0.752223838  0.700034454  0.684281851
##  [631]  0.539741101 -0.023422427  0.507663454 -0.420162867  0.812921098
##  [636]  0.516667050  0.033246898  0.440349299  1.093862922 -0.379604827
##  [641]  0.658172464  1.376296711 -0.016561482  0.371381036  0.611668775
##  [646]  0.364114760  0.541684366  0.598566743  0.542270585  0.466552122
##  [651]  0.359653048 -1.029849148  0.991259082 -0.270662789  0.601672187
##  [656] -0.016023676  0.239462642  1.781128184  0.598566743  0.348271817
##  [661]  0.952252398  0.838571001 -0.050642751  0.406147364  0.413618268
##  [666] -0.026212478  0.931011562  0.668568836 -0.736813843  0.572467408
##  [671]  0.493035164  0.371036335 -0.238184828  0.572621888  0.047462661
##  [676]  0.229125480  0.336592503  0.725994526  0.758486043 -0.909654171
##  [681]  0.996645055 -0.740260569 -0.049094015 -0.149724521  0.071540869
##  [686]  0.904179485  0.394570239  0.313396397  0.381826741  0.788714435
##  [691]  0.055905342 -0.341028096  0.801486505  0.457591233  0.031091761
##  [696]  1.195715996  0.268451841  1.062857318  0.258446356 -0.211744947
##  [701]  1.653682750  0.451542584  0.439871984  0.435135273  0.968267313
##  [706]  0.176919028  0.416688560 -0.023957520  0.154304405  0.183355647
##  [711]  0.396551475  0.226623837  0.616203828  0.133953970  0.193643543
##  [716]  0.709805791 -0.080671407  1.360204735  0.625301941  0.576725558
##  [721]  0.302216381  0.322078347  0.783976183  0.250667387  0.864944223
##  [726]  0.327514658  0.245449657  0.803976850  0.401500910 -0.342302267
##  [731]  1.159795430  0.266906660  0.807533354 -0.528229275  0.762242084
##  [736]  0.551922834 -0.021007906  0.764090857 -0.459878191  0.805364670
##  [741]  1.150967966  0.439044780  0.438511013  1.171688322 -0.681495407
##  [746] -0.162098489  0.771051337  1.101521806 -0.033307235  0.361937703
##  [751]  0.933442862  0.832560587  0.972962166 -0.412238515  0.785169111
##  [756]  0.546182364 -0.383750439  0.033110807  1.480904419  0.564323554
##  [761] -0.669923131 -0.073814180  0.190136770  0.698580247 -0.135357667
##  [766] -0.012429080  1.102990018  0.189827958  0.032653429  0.824700107
##  [771]  0.732482841  0.536856020  0.115054460  0.719101414  0.352594370
##  [776]  0.781970889  0.545913280 -0.229638194 -0.177734363 -0.233996053
##  [781] -0.013079657  0.868112244  0.460527636 -0.212913182 -0.180731087
##  [786] -0.353831058  0.152238397  0.607314049  0.138963399 -0.036939549
##  [791]  1.480904419  0.319859145 -0.119215118  0.528540940 -0.141513668
##  [796]  0.155888457 -0.327264540  0.267599897  0.536962209  1.014041745
##  [801]  0.174171041  0.058783919 -0.276077371  0.268451841  0.723891512
##  [806] -0.040295384 -0.128881668  0.570402097 -0.293419422  0.390742993
##  [811]  0.440027811  0.467246515  0.631449076  0.662941104  1.090877778
##  [816] -0.272220518  1.237518094  0.605350369 -0.139484818  1.618180944
##  [821]  0.213163306 -0.440351977  0.045139636  0.304147376  0.537135308
##  [826]  1.006691667  0.377669584  0.246360430  0.743261124  1.046442764
##  [831]  0.194634908  1.323278665  0.135868628 -0.385127112  0.933930872
##  [836]  0.741843385  0.248708411  0.267298045 -0.604288758 -0.039264463
##  [841] -0.229715549  0.774888182 -0.211005041  0.238087401  0.672987654
##  [846]  1.255771180  1.276290187  0.094517532 -0.826789372  1.002575319
##  [851]  0.058429489  0.334839388  0.047108634  0.128160179  0.312731204
##  [856]  0.489511449  0.166468935  0.125050099 -0.506824502  0.212163723
##  [861]  0.830083091  0.918182718 -0.249963485  0.589442514  0.904108599
##  [866]  0.664760252  0.347283673  0.318237176  0.804617874  1.053684692
##  [871] -0.301341280  0.254386034  0.194626127 -0.202608245 -0.125778364
##  [876]  0.729288370  0.400989540  0.414289402  0.587234155  0.706848781
##  [881] -0.018875250  1.393706958  0.272284390  0.440342751  1.072411059
##  [886] -0.023957520  0.142944201  0.617953839  0.750774026  0.024712646
##  [891]  0.151796587  0.425859659  0.343923317 -0.457816972  0.249433315
##  [896] -1.038128759 -0.286383760  0.319409736 -0.161034874  0.285368180
##  [901]  0.266197598  1.121923874  0.345515988  0.893947483  1.908591091
##  [906]  0.423580406  0.811148541  0.006140170  1.576747547  0.378301039
##  [911]  0.199676749 -0.294547583  0.595490025  0.188845932 -0.042165889
##  [916]  0.358147726 -0.255222576  0.403964103  0.404551583 -0.202297319
##  [921] -0.095925411  0.769776986  1.045102420  0.527581179  0.420379852
##  [926]  0.174793959 -0.204022695  0.460453794  0.104174174  0.221402206
##  [931]  0.418283212  1.310490228 -0.260819706  0.471665280  0.092492878
##  [936]  1.733364580  0.088289215  0.268252006  0.108779941 -0.279179402
##  [941]  0.602863546  1.377275268  0.423848373  0.077643566  0.431956802
##  [946]  0.684263148 -0.490011444  0.216190239  0.607789988  0.674797142
##  [951] -0.336788021 -0.235200391  0.148348274  0.231730565 -0.475326429
##  [956]  0.474299132  0.837244386  0.136328513  0.229466192  0.037820323
##  [961]  0.127598393  0.464269126  0.904386366  0.362766327  0.332180751
##  [966] -0.236569750  0.075998676  0.934350617  0.387489380  1.134298584
##  [971]  0.733990687  0.548263218  1.054238042  0.400610741  0.967605255
##  [976]  2.157130132  0.671539393  0.039281223 -0.169292323  0.706510175
##  [981]  0.832453005  0.567162290  0.055240950  0.286384093 -0.575102101
##  [986]  0.846473498  0.430141684  0.687398077  0.327140036  0.347841403
##  [991]  0.194473057  0.252269145  0.415005692  0.734779729  0.616574160
##  [996]  0.577527712  0.534890946  1.455259714 -0.051303760 -0.148215197
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
##   0.56238160   0.37004655 
##  (0.11701899) (0.08274247)
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
## [1] -0.15333875 -0.27957934  0.29763027 -0.04347275  0.25597114 -0.57483715
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
## [1] -0.0211
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9130161
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
## t1*      4.5 0.02872873   0.8721392
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 4 5 6 8 9 
## 1 1 1 1 1 2 1 2
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
## [1] 0.0271
```

```r
se.boot
```

```
## [1] 0.8920714
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

