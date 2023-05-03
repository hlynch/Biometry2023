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
## 0 1 2 3 4 5 6 7 8 
## 2 1 1 1 1 1 1 1 1
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
## [1] 0.0225
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
## [1] 2.722747
```

```r
UL.boot
```

```
## [1] 6.322253
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
##    [1] 4.1 3.5 3.1 3.8 5.8 4.8 6.0 4.3 4.3 4.8 5.2 4.2 5.0 5.9 5.4 5.4 4.4 4.3
##   [19] 5.1 4.9 4.2 5.0 5.2 5.7 4.8 4.3 5.5 4.5 4.2 4.0 4.1 4.8 3.7 5.5 5.8 4.0
##   [37] 4.6 5.2 5.3 4.1 5.1 4.6 5.6 4.9 5.5 4.0 5.4 4.9 4.3 3.8 4.9 3.8 3.8 4.5
##   [55] 4.4 4.7 4.6 4.7 5.0 4.3 4.1 5.3 5.3 4.8 4.9 4.9 5.1 1.8 3.3 4.1 4.2 5.3
##   [73] 5.1 4.6 4.3 4.5 4.6 4.3 5.3 4.0 4.7 4.5 3.6 3.8 4.3 5.2 3.9 3.4 3.8 4.5
##   [91] 4.6 4.8 4.9 3.5 3.8 3.0 4.9 5.1 5.6 3.6 3.9 4.4 4.4 3.8 4.3 4.7 6.0 5.9
##  [109] 5.2 4.2 3.0 2.8 3.6 4.2 5.2 3.1 3.6 3.4 6.3 4.8 3.4 4.3 4.8 4.4 4.5 4.8
##  [127] 5.5 3.9 4.8 5.7 4.6 3.4 3.6 4.1 4.5 5.4 6.1 4.8 4.3 2.6 5.2 5.0 4.5 4.1
##  [145] 4.2 6.0 4.2 3.7 3.3 4.8 3.1 4.8 3.8 4.0 5.4 5.9 3.8 4.5 4.0 4.1 4.8 6.3
##  [163] 5.8 3.8 5.7 6.2 5.6 4.5 4.7 3.9 3.4 4.9 4.2 4.0 4.2 5.3 6.7 4.0 5.3 4.4
##  [181] 4.8 4.8 5.2 2.8 3.9 4.1 3.1 3.6 4.3 4.4 4.4 3.1 3.8 2.9 4.0 4.3 3.7 2.5
##  [199] 4.3 5.8 4.2 4.7 5.2 4.2 3.4 4.9 5.0 3.1 4.3 4.6 3.4 5.0 4.3 4.7 5.4 4.4
##  [217] 3.5 3.7 4.4 5.9 4.8 3.4 5.9 5.1 3.1 3.3 5.2 3.5 4.3 3.2 4.5 2.3 4.2 6.0
##  [235] 5.7 4.8 3.6 5.4 4.1 4.3 6.1 5.1 3.7 4.8 4.9 5.0 5.8 3.5 4.7 5.3 4.3 5.4
##  [253] 5.4 4.6 5.6 2.9 2.6 4.6 3.3 6.4 4.5 4.9 5.7 4.9 5.7 5.0 3.9 5.3 3.3 4.7
##  [271] 4.9 4.0 4.2 3.3 4.4 5.5 5.2 5.3 3.1 4.7 5.7 5.1 4.9 4.7 3.8 5.2 5.6 4.2
##  [289] 3.3 4.4 4.0 5.0 5.6 5.0 3.6 4.9 4.8 3.6 4.6 5.3 4.7 4.6 3.0 4.5 4.3 4.9
##  [307] 5.1 5.2 4.1 5.3 4.0 4.5 4.2 4.2 5.7 5.4 4.6 5.3 5.2 5.8 5.9 3.9 3.5 4.7
##  [325] 5.4 3.8 3.0 5.5 4.7 4.2 4.1 5.1 5.7 5.9 3.3 5.6 3.7 4.7 3.9 5.3 3.8 3.6
##  [343] 3.7 2.7 4.5 4.1 6.4 5.4 5.2 4.1 4.5 5.0 3.4 5.0 3.4 2.7 3.8 3.8 4.0 4.3
##  [361] 4.6 5.8 4.1 5.3 6.1 6.0 3.4 6.3 5.1 5.1 4.3 4.4 4.4 3.9 5.9 4.1 5.4 5.4
##  [379] 4.6 5.3 4.4 3.7 3.4 3.8 4.2 5.9 4.9 4.6 5.3 4.6 3.7 6.0 4.9 4.3 4.9 6.2
##  [397] 5.3 4.6 4.4 5.5 4.4 5.0 5.0 6.0 4.6 5.5 3.6 3.4 5.7 4.9 4.3 5.7 4.5 4.2
##  [415] 4.5 4.4 3.9 5.1 4.8 4.6 4.4 3.8 2.6 3.4 5.7 4.2 5.4 5.6 5.8 2.6 4.9 4.5
##  [433] 3.9 6.5 2.7 4.6 4.1 3.8 4.1 4.0 4.2 4.7 4.2 4.4 3.6 4.8 3.1 4.9 3.0 5.6
##  [451] 3.9 3.9 5.2 3.5 4.9 4.1 5.2 4.1 5.4 4.0 5.7 4.6 2.5 4.1 5.6 4.3 6.2 3.8
##  [469] 4.1 4.6 4.4 5.2 3.7 2.7 4.2 5.5 4.4 4.6 6.2 5.3 3.9 3.2 5.3 4.3 6.1 4.5
##  [487] 5.1 5.7 4.6 4.4 4.0 4.3 5.0 4.2 4.3 4.2 4.7 5.3 5.3 5.0 3.4 3.9 5.1 2.9
##  [505] 2.5 4.7 5.0 3.4 3.9 5.3 4.7 3.2 4.6 4.2 5.6 4.9 4.8 3.3 3.7 4.8 6.0 5.1
##  [523] 4.9 4.1 4.3 4.1 5.4 6.1 3.6 4.1 3.8 3.7 5.0 4.8 4.1 3.1 3.3 5.6 6.1 4.4
##  [541] 4.6 5.5 4.8 3.9 5.2 5.0 3.8 3.5 3.8 5.8 3.9 5.4 4.8 4.7 4.4 4.8 5.0 2.6
##  [559] 5.6 4.4 4.6 3.4 5.6 5.0 6.0 4.8 4.8 5.9 4.8 3.2 3.8 4.2 4.0 3.5 5.5 4.8
##  [577] 3.4 4.8 5.0 4.3 3.8 4.3 4.3 4.0 4.1 4.9 4.6 4.4 5.7 5.5 6.4 4.6 5.3 4.3
##  [595] 2.8 3.6 4.5 4.8 3.9 4.3 3.5 4.1 4.3 5.3 5.0 3.5 4.9 4.6 5.4 3.5 3.7 5.4
##  [613] 5.3 3.1 4.1 5.4 4.4 4.2 3.8 4.6 2.9 3.7 6.3 4.9 6.3 3.3 5.1 5.0 4.2 4.5
##  [631] 4.7 4.4 3.6 3.0 6.5 5.6 3.4 3.9 5.2 4.5 5.7 4.0 4.9 4.0 4.1 3.6 4.1 3.8
##  [649] 5.3 3.9 4.0 4.3 5.3 4.1 2.8 5.0 5.2 4.1 4.8 5.3 4.1 3.7 3.9 3.3 4.8 3.5
##  [667] 5.2 6.2 4.6 4.9 4.6 4.4 5.2 4.6 4.4 4.1 4.9 3.2 4.4 3.6 4.8 3.9 4.3 5.0
##  [685] 4.7 2.8 5.9 4.7 5.1 3.5 3.1 6.6 4.4 4.7 5.5 3.7 4.2 5.8 4.4 3.6 4.2 3.6
##  [703] 5.1 5.5 3.8 5.5 6.5 3.8 3.7 4.7 5.1 5.6 3.8 5.1 6.2 3.1 4.5 3.6 4.4 5.9
##  [721] 4.7 3.9 3.8 4.6 3.6 5.7 5.4 4.9 4.7 5.4 4.8 4.5 3.6 5.2 4.0 5.3 5.5 3.8
##  [739] 4.6 5.1 2.8 5.0 3.5 4.1 3.1 4.1 3.0 4.4 6.1 4.6 3.8 3.8 5.5 4.3 3.8 4.0
##  [757] 4.9 4.5 4.3 3.5 4.8 3.5 4.6 5.4 3.4 4.4 4.0 2.4 5.0 5.2 3.8 4.0 3.8 5.2
##  [775] 4.2 3.4 4.0 4.9 4.7 3.1 4.8 5.6 4.5 4.6 4.6 4.1 3.5 5.3 2.7 3.5 5.3 3.7
##  [793] 2.2 4.3 4.5 3.1 4.7 3.4 5.0 4.7 4.7 4.2 4.6 5.2 6.7 4.1 4.4 3.6 3.9 3.4
##  [811] 5.2 7.2 5.9 3.0 3.4 5.7 4.1 5.8 5.5 4.2 5.0 3.5 4.8 4.5 4.5 3.4 4.3 4.5
##  [829] 4.9 4.6 3.9 4.3 4.2 4.5 5.1 4.2 4.3 5.0 6.2 4.4 6.1 3.7 5.2 4.6 4.7 4.7
##  [847] 5.2 5.0 5.6 2.4 5.6 4.2 3.7 5.1 6.1 4.6 7.2 3.6 4.5 2.5 5.7 4.3 4.5 4.5
##  [865] 3.4 5.3 4.0 4.5 3.8 5.4 2.9 5.3 5.0 4.7 5.2 5.1 3.7 3.0 5.5 4.6 5.3 3.7
##  [883] 5.4 5.1 4.0 4.6 5.5 4.1 7.4 4.0 6.6 4.3 4.9 4.9 2.8 3.8 3.9 4.0 4.5 4.1
##  [901] 4.8 5.3 4.2 3.9 3.2 4.4 4.6 4.0 6.7 4.0 4.9 4.9 4.4 2.8 3.8 4.4 4.6 4.2
##  [919] 3.9 2.9 5.3 4.3 4.5 4.7 4.1 4.0 4.5 4.3 3.7 4.4 3.8 5.8 6.1 5.0 4.5 6.0
##  [937] 5.0 3.5 5.3 3.9 4.2 5.5 3.0 4.9 4.9 5.7 3.1 3.2 4.9 5.2 2.9 4.7 7.0 5.1
##  [955] 5.3 3.4 5.0 2.5 5.5 5.3 4.9 3.9 4.5 4.3 4.2 5.2 4.2 4.7 4.4 4.4 5.7 5.2
##  [973] 3.7 5.5 3.9 4.6 3.9 4.0 3.3 4.8 4.2 4.3 5.3 5.6 3.4 5.0 4.4 5.0 4.3 5.0
##  [991] 3.7 6.4 3.5 5.6 3.8 4.0 5.7 4.3 4.2 5.6
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
##    [1] 4.6 4.7 5.2 3.3 3.4 3.6 5.5 4.3 5.4 3.6 4.3 4.8 5.2 5.0 4.5 4.7 4.8 3.5
##   [19] 7.0 4.8 3.8 4.3 4.6 3.1 3.7 3.3 3.3 5.0 4.3 4.1 4.0 4.6 4.4 4.1 3.8 4.7
##   [37] 3.2 5.0 3.8 6.1 5.1 3.3 5.4 6.0 3.7 3.9 5.2 4.3 2.8 6.5 4.9 3.5 4.0 4.1
##   [55] 4.5 6.6 6.2 4.3 5.6 4.4 5.6 4.0 4.7 3.6 4.5 5.6 4.5 4.8 5.1 4.1 4.2 3.2
##   [73] 4.6 3.0 3.9 5.1 4.0 3.6 5.1 3.7 3.7 4.5 4.3 4.0 1.6 5.4 5.8 5.9 4.4 3.6
##   [91] 4.9 3.6 4.6 5.6 3.4 4.3 4.0 5.8 3.8 4.3 4.8 4.3 3.7 5.0 3.3 4.8 4.7 2.4
##  [109] 5.9 3.4 6.1 5.0 3.3 4.2 6.2 4.5 4.2 3.8 4.9 4.8 5.2 4.6 4.9 3.6 3.3 4.0
##  [127] 4.6 4.6 3.6 4.5 2.6 3.8 3.6 4.9 3.2 5.5 4.8 4.0 3.6 4.9 4.2 4.5 4.7 6.3
##  [145] 3.4 4.7 4.4 4.8 5.0 4.7 4.2 4.4 5.8 3.3 4.6 5.1 4.6 3.8 3.3 3.6 3.9 5.5
##  [163] 4.2 5.5 4.9 6.3 4.4 3.4 3.5 5.0 4.1 4.5 3.9 4.9 3.5 5.8 4.9 3.8 5.7 5.8
##  [181] 5.8 4.1 3.8 6.1 3.3 4.5 4.9 4.2 3.4 3.1 3.6 2.6 3.3 3.7 4.0 5.3 4.4 3.5
##  [199] 5.2 4.5 3.5 5.2 3.7 4.4 5.3 4.3 4.9 5.3 6.3 5.5 3.2 3.4 3.9 5.3 4.9 5.7
##  [217] 4.8 4.7 4.2 3.6 4.8 4.8 4.4 3.4 5.2 5.0 3.5 6.2 4.7 4.7 5.4 4.2 4.8 5.5
##  [235] 2.5 5.0 4.0 3.4 6.3 4.2 4.7 6.7 3.9 3.2 5.5 4.1 3.2 4.0 5.7 5.1 4.4 5.1
##  [253] 5.6 4.3 4.3 5.5 4.2 4.2 5.0 5.4 3.8 5.8 3.7 3.6 3.1 2.9 4.5 5.1 2.3 5.8
##  [271] 3.9 3.8 4.8 5.1 5.9 7.2 3.3 3.3 4.4 5.1 5.2 5.3 4.1 5.2 3.5 5.0 5.0 3.6
##  [289] 4.9 6.0 4.2 3.1 4.1 6.0 5.3 6.2 4.9 4.2 6.4 5.5 4.0 4.5 5.2 4.7 5.3 3.0
##  [307] 4.7 3.6 5.9 5.0 4.5 5.3 4.3 2.3 5.2 4.8 3.7 5.6 4.4 3.6 4.1 4.1 5.0 3.2
##  [325] 4.5 4.8 5.0 4.7 5.7 4.2 3.4 6.1 2.9 4.0 4.2 4.0 5.7 4.1 4.0 4.5 2.6 3.4
##  [343] 3.5 5.3 4.6 4.0 4.5 3.6 6.1 4.8 5.4 5.4 3.7 4.4 4.6 4.5 5.2 4.8 4.8 4.7
##  [361] 5.3 3.8 4.0 5.2 4.7 5.7 3.7 3.1 4.0 4.0 4.9 5.7 4.6 3.2 6.4 4.4 3.2 5.9
##  [379] 4.1 4.1 4.8 5.4 3.3 5.6 4.3 5.6 5.2 5.8 6.0 5.5 4.6 5.7 5.8 4.4 4.8 4.8
##  [397] 4.6 3.7 4.8 4.9 4.6 3.8 4.4 4.1 6.5 3.1 7.0 4.8 2.9 3.2 5.6 2.9 5.1 5.0
##  [415] 4.3 6.2 4.1 5.1 6.0 3.0 5.2 5.6 4.9 4.8 2.4 3.1 4.0 3.6 5.5 3.4 3.2 3.6
##  [433] 2.9 5.5 3.8 4.4 4.8 2.7 4.7 3.8 4.0 5.5 4.3 5.7 3.9 3.2 5.6 5.4 5.1 3.8
##  [451] 5.3 3.8 5.3 5.8 4.8 4.2 4.8 4.3 4.3 4.3 5.1 4.5 3.9 3.6 6.0 4.5 3.1 5.8
##  [469] 2.6 4.4 4.3 3.8 3.6 3.6 4.1 4.4 7.1 4.8 3.3 3.8 6.0 4.6 4.7 5.8 4.9 4.1
##  [487] 4.1 5.2 6.0 5.2 3.5 6.0 3.7 3.6 3.9 3.8 5.7 5.2 4.2 3.7 3.0 5.3 5.1 4.1
##  [505] 4.6 6.0 4.4 5.2 4.8 5.0 5.9 2.8 5.3 4.2 3.4 4.4 4.6 4.2 4.2 2.5 5.3 4.0
##  [523] 4.4 3.8 4.4 4.8 4.2 4.6 4.7 4.0 4.1 3.8 4.9 6.6 5.6 3.8 4.4 4.6 3.8 5.4
##  [541] 4.7 6.1 4.7 7.2 3.7 2.9 4.7 5.0 3.7 3.9 4.0 5.4 5.6 4.8 5.8 3.9 4.3 4.3
##  [559] 5.2 3.1 4.7 3.4 3.1 4.7 3.6 4.6 5.2 5.5 3.2 4.0 4.1 5.5 5.4 4.7 2.6 5.4
##  [577] 3.5 5.7 5.6 4.8 6.6 4.5 3.6 4.4 5.0 4.3 5.0 4.9 3.9 4.6 6.2 5.2 6.5 4.3
##  [595] 3.5 3.5 4.3 3.7 5.2 5.3 3.8 3.1 3.6 3.6 5.0 4.3 3.8 5.4 4.4 4.8 3.4 4.3
##  [613] 4.5 4.6 3.3 3.4 5.3 4.9 4.3 3.0 3.7 3.0 4.0 4.1 4.2 4.3 3.5 3.4 4.9 6.0
##  [631] 3.1 6.6 3.5 4.5 4.0 4.2 4.2 4.4 5.0 5.7 4.4 3.7 4.9 4.7 3.9 4.8 4.1 5.3
##  [649] 3.4 4.8 3.6 3.6 5.0 3.1 4.2 3.6 6.4 5.6 5.8 5.4 4.1 4.6 4.7 4.5 3.8 5.6
##  [667] 5.9 6.2 4.8 3.4 4.9 7.3 4.8 3.8 4.3 4.6 3.2 3.2 7.1 5.4 3.8 3.6 6.5 2.7
##  [685] 4.5 3.6 3.1 4.1 2.7 4.2 5.8 5.2 3.8 4.1 5.5 3.5 4.4 4.0 3.8 2.5 3.9 4.5
##  [703] 4.0 6.2 3.9 4.0 3.0 5.8 3.0 6.1 4.4 2.9 5.3 5.9 2.7 4.9 2.5 4.2 3.7 4.9
##  [721] 4.4 2.7 4.0 4.4 3.3 4.7 4.5 5.1 4.9 5.6 5.2 4.5 2.3 3.1 3.8 3.4 4.6 5.4
##  [739] 3.9 5.0 5.7 5.1 2.4 3.6 4.0 4.2 4.8 3.5 4.4 4.4 4.4 4.6 4.8 5.1 4.0 4.3
##  [757] 5.4 4.5 3.0 3.6 5.8 3.5 5.5 5.3 4.2 3.7 3.8 5.7 2.5 4.6 4.7 4.7 4.8 3.2
##  [775] 5.1 5.4 4.1 5.5 6.4 5.1 4.3 4.1 4.9 5.6 4.6 3.7 4.2 4.7 2.2 4.3 4.6 4.3
##  [793] 4.9 4.2 3.7 4.9 5.5 3.8 3.6 6.5 4.9 4.8 3.1 2.4 3.4 4.4 4.2 2.8 4.5 4.4
##  [811] 5.7 5.4 6.1 4.2 4.3 3.4 5.4 4.7 4.5 3.9 4.5 4.7 4.1 3.5 4.0 4.2 4.2 4.1
##  [829] 5.5 5.1 4.3 4.7 5.1 3.7 4.1 3.0 4.4 3.4 4.0 1.9 4.9 4.5 5.4 4.2 4.2 3.0
##  [847] 4.5 4.8 5.4 4.4 5.2 3.9 3.3 5.4 3.5 4.8 4.2 4.0 4.9 3.8 3.6 4.4 5.1 6.7
##  [865] 4.4 6.4 3.2 4.0 4.9 4.8 4.7 3.9 2.8 2.4 3.4 3.7 5.2 6.4 4.5 4.1 4.9 3.6
##  [883] 3.9 3.7 5.2 4.8 3.7 3.9 4.9 3.9 4.3 6.2 4.8 5.1 5.0 2.8 2.8 3.4 5.2 5.3
##  [901] 4.5 4.1 5.6 4.3 4.4 4.3 4.8 4.0 5.8 4.0 4.1 5.4 5.2 2.7 5.1 3.9 5.9 6.1
##  [919] 5.7 3.9 4.6 4.0 6.4 5.0 3.9 2.3 4.5 3.2 4.0 4.3 6.2 4.1 5.1 4.3 4.5 5.6
##  [937] 6.0 2.7 3.8 4.3 4.4 4.0 4.3 3.4 4.7 4.9 4.3 5.6 3.6 5.1 5.7 4.7 5.1 5.4
##  [955] 5.0 2.4 4.7 4.0 4.2 4.4 3.2 5.0 4.2 3.5 4.1 3.0 5.2 4.6 4.8 3.1 3.3 5.0
##  [973] 5.0 5.2 5.4 4.3 3.4 2.6 4.4 3.4 4.1 5.2 4.5 3.0 4.9 4.9 3.9 7.0 5.2 4.2
##  [991] 5.5 4.5 4.2 6.0 4.3 6.4 4.6 4.3 3.5 3.6
## 
## $func.thetastar
## [1] -0.0339
## 
## $jack.boot.val
##  [1]  0.49452450  0.36562500  0.28048048  0.13706897  0.09619883 -0.10451977
##  [7] -0.16791444 -0.31242938 -0.41994609 -0.54806630
## 
## $jack.boot.se
## [1] 0.9928175
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
##    [1] 5.7 4.1 4.6 5.4 4.7 4.7 4.7 5.0 4.6 4.2 5.4 4.0 4.2 3.9 4.4 5.1 6.5 4.1
##   [19] 4.9 3.2 4.5 3.8 5.0 6.2 4.1 5.1 4.7 4.6 4.8 2.0 5.5 5.5 3.9 5.3 3.9 3.8
##   [37] 4.9 1.5 3.0 4.8 3.7 5.4 4.1 4.7 5.2 4.5 2.7 3.5 4.6 4.6 5.2 3.7 5.3 5.0
##   [55] 4.0 4.4 6.3 4.9 3.5 3.9 5.1 4.3 4.7 3.8 3.7 4.7 6.5 4.2 3.2 3.5 5.4 2.1
##   [73] 3.3 6.3 5.4 5.7 5.2 5.4 4.6 6.3 3.7 4.6 4.7 6.7 4.2 4.9 4.2 2.4 4.3 3.7
##   [91] 4.6 4.0 4.2 5.2 3.4 5.0 4.5 4.6 3.2 3.2 4.3 3.7 3.5 3.8 3.9 3.9 4.9 3.6
##  [109] 4.1 3.6 5.6 6.0 2.6 3.8 6.2 6.3 5.9 3.1 4.3 3.9 5.1 6.4 3.8 5.5 4.0 3.0
##  [127] 3.8 4.5 5.6 3.4 4.2 5.0 3.4 4.4 5.9 4.9 5.2 4.5 4.4 5.4 5.7 4.3 5.0 6.4
##  [145] 4.5 6.2 5.1 3.4 3.8 6.4 5.9 4.6 4.7 4.8 5.0 6.3 3.4 3.7 3.9 2.4 5.1 4.6
##  [163] 3.5 5.4 4.8 3.6 4.3 3.8 3.2 5.7 3.9 5.8 4.8 4.0 4.2 4.0 4.3 3.7 3.4 3.7
##  [181] 5.6 3.7 5.1 5.5 4.0 6.7 4.0 5.2 4.3 5.2 4.5 5.2 4.8 3.7 4.8 3.3 5.4 5.7
##  [199] 5.1 5.8 5.3 5.1 5.3 5.8 4.8 4.2 4.5 2.9 4.0 5.6 3.9 4.2 5.3 5.1 5.3 3.9
##  [217] 3.8 6.0 3.9 4.3 6.1 4.0 6.8 4.5 3.4 6.1 2.1 5.6 5.4 4.4 4.9 5.2 5.2 4.1
##  [235] 3.8 3.3 5.0 3.1 3.8 2.8 5.0 4.3 3.8 5.6 2.8 4.3 4.8 5.1 3.4 3.2 3.6 5.9
##  [253] 5.0 2.9 4.5 2.7 5.0 4.9 3.8 3.1 4.7 5.8 4.4 3.7 4.4 4.7 5.2 4.1 3.9 4.0
##  [271] 4.6 6.3 4.3 5.7 4.1 4.1 4.2 6.0 4.5 5.1 6.0 3.2 3.0 5.7 3.6 3.3 6.7 5.8
##  [289] 4.7 5.6 4.1 4.4 2.9 4.2 3.5 4.5 3.2 4.3 4.8 4.3 3.9 3.0 3.0 4.7 5.0 4.1
##  [307] 4.4 4.7 5.2 4.7 4.4 4.1 3.9 3.9 4.9 6.0 3.4 3.2 4.6 5.2 3.1 4.5 5.1 3.8
##  [325] 6.2 3.4 3.9 3.8 5.3 5.6 5.0 4.6 4.5 4.4 4.6 2.8 4.3 4.2 3.6 5.0 3.6 3.2
##  [343] 4.2 3.7 4.3 3.8 3.5 3.7 5.8 3.2 4.9 2.6 3.5 4.5 5.1 5.3 3.4 5.2 4.0 3.6
##  [361] 6.1 4.4 3.9 6.0 3.7 4.6 4.6 4.1 6.8 5.4 3.7 4.1 3.8 4.1 4.2 5.3 5.6 4.5
##  [379] 5.0 4.5 3.9 3.8 4.7 3.7 4.3 4.6 2.7 4.6 4.1 5.0 4.3 3.6 5.3 4.0 4.7 4.2
##  [397] 3.6 4.0 4.3 3.4 3.6 3.2 3.5 5.6 3.5 5.2 5.0 3.8 4.9 6.2 4.4 4.9 4.3 4.8
##  [415] 4.3 3.8 4.8 3.8 4.8 5.1 5.8 4.5 3.4 6.0 3.9 5.6 4.2 4.9 4.6 4.4 6.3 5.4
##  [433] 4.7 3.5 3.1 4.3 4.6 4.5 4.0 4.7 5.6 4.4 4.5 5.4 5.5 5.0 3.9 4.7 4.6 4.4
##  [451] 4.0 5.7 5.8 4.9 4.6 5.7 4.8 4.7 4.7 5.9 2.5 5.6 5.3 4.2 5.1 5.2 3.6 4.0
##  [469] 3.0 3.5 3.8 4.8 2.9 4.1 4.0 2.6 4.2 6.1 5.6 4.2 3.1 4.4 3.9 4.8 4.7 6.1
##  [487] 4.3 4.5 5.6 4.6 3.9 5.6 4.5 4.9 4.5 5.3 5.2 4.0 4.6 5.2 3.7 3.3 5.1 3.4
##  [505] 5.3 2.9 5.7 5.7 4.8 4.7 6.0 4.6 4.9 6.8 4.8 3.2 3.8 4.9 3.9 5.1 4.3 5.3
##  [523] 4.8 4.9 4.5 6.0 4.8 5.0 4.0 3.0 6.4 4.4 4.5 4.7 3.8 6.4 5.0 3.3 5.1 5.5
##  [541] 2.4 6.1 5.7 4.7 3.7 2.6 5.2 3.5 6.8 5.3 3.9 5.2 4.8 4.2 4.8 5.2 5.2 3.3
##  [559] 4.6 4.3 4.7 4.8 5.2 3.9 3.7 5.3 4.1 3.9 4.7 3.2 4.1 5.5 3.4 5.5 5.1 3.0
##  [577] 3.8 3.9 4.3 5.6 4.4 3.5 3.9 3.8 4.3 4.7 4.1 3.5 5.0 3.5 4.2 5.3 6.2 5.9
##  [595] 3.0 4.0 4.7 2.5 3.6 3.8 3.9 3.4 4.8 5.8 4.4 4.9 3.2 4.0 3.5 4.3 3.9 5.2
##  [613] 3.8 5.9 2.4 3.5 2.9 5.2 5.4 6.0 4.5 3.6 2.8 3.8 3.8 5.0 3.8 6.1 5.0 4.8
##  [631] 3.8 3.1 4.9 3.8 4.2 6.3 4.5 3.7 5.1 3.9 3.0 4.8 4.3 3.5 4.8 4.4 4.3 4.5
##  [649] 6.4 5.5 4.6 3.0 3.9 4.7 2.8 5.0 4.0 3.0 4.5 5.2 3.6 5.6 5.7 6.2 3.9 2.9
##  [667] 5.3 5.7 3.4 3.9 2.7 3.1 5.1 4.3 4.1 3.8 3.2 4.1 3.2 5.0 6.0 5.2 4.3 5.4
##  [685] 3.2 4.9 3.0 4.4 5.5 3.1 4.6 3.9 4.4 4.4 3.9 5.3 3.0 4.0 4.1 5.1 5.2 4.6
##  [703] 3.0 4.2 3.6 4.3 3.5 5.1 3.2 3.8 2.8 3.7 5.6 6.8 4.8 4.0 4.3 4.3 4.8 4.8
##  [721] 5.1 6.4 2.8 2.8 3.0 4.7 5.3 5.1 4.4 4.6 3.6 2.9 3.6 4.9 5.2 5.6 6.0 3.7
##  [739] 6.6 4.9 3.6 4.2 4.6 3.5 3.5 4.5 4.0 3.5 3.9 3.4 5.1 4.8 4.5 4.7 4.0 2.4
##  [757] 5.1 3.9 2.8 4.5 4.9 4.5 4.8 4.1 7.1 6.0 4.0 4.8 6.4 4.7 4.6 4.3 4.5 3.9
##  [775] 5.9 4.9 4.2 5.5 4.8 4.1 4.0 6.1 3.6 3.9 3.4 4.9 5.0 5.3 3.6 3.7 5.8 3.9
##  [793] 4.9 4.4 2.6 3.6 3.7 5.0 3.5 3.8 2.8 5.2 4.9 3.8 4.6 6.0 6.1 4.8 3.9 2.9
##  [811] 3.2 4.9 5.0 4.8 3.9 6.8 3.5 5.2 2.9 3.0 4.1 4.3 3.2 4.2 7.4 3.3 5.0 5.1
##  [829] 5.0 4.2 3.5 4.1 5.5 3.9 4.8 4.2 3.3 4.9 5.3 6.4 5.4 3.5 4.3 5.5 3.1 5.0
##  [847] 3.3 5.0 3.5 4.4 3.6 5.3 4.1 5.7 4.7 3.7 4.5 4.9 4.5 3.0 5.8 4.9 4.6 4.3
##  [865] 3.0 5.0 6.2 5.9 5.4 4.1 4.3 4.0 3.7 4.9 4.8 5.0 2.9 3.6 6.9 2.9 2.0 4.8
##  [883] 3.9 3.9 4.9 4.2 5.2 5.1 5.0 4.3 7.2 4.6 5.8 5.1 5.1 3.7 3.1 3.1 4.0 5.2
##  [901] 4.3 4.5 4.3 3.7 4.2 5.2 4.3 5.9 3.5 5.7 3.7 5.4 3.6 2.9 4.2 3.0 4.1 2.6
##  [919] 6.1 4.0 4.1 4.7 4.8 4.8 6.7 4.8 4.1 4.6 4.5 3.5 4.5 4.0 4.8 4.1 4.2 3.1
##  [937] 3.7 5.4 6.1 4.6 3.3 4.2 4.0 3.2 4.6 4.0 5.9 3.2 4.9 4.2 5.8 4.7 3.8 4.4
##  [955] 4.8 4.0 3.9 5.3 5.0 4.2 4.5 4.5 5.1 3.5 3.7 4.1 4.2 3.4 4.5 3.2 4.3 6.0
##  [973] 4.2 4.7 5.6 3.8 3.9 3.5 3.5 2.7 4.8 5.2 4.7 5.5 4.1 3.9 4.5 5.8 4.0 5.3
##  [991] 4.5 4.9 3.7 4.5 2.7 2.5 4.4 4.2 3.6 4.4
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.600 5.400 5.200 5.116 5.100 4.960 4.800 4.600 4.600 4.300
## 
## $jack.boot.se
## [1] 1.1323
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
## [1] 0.3359093
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
##      shape       rate   
##    8.790046   13.871350 
##  ( 3.858728) ( 6.266524)
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
## [1]  0.6137561  0.2006635  0.1937063  0.5348553  0.4328713 -0.2802715
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
##    [1] -0.583902358 -0.006626336  0.678691024 -0.381346307  0.474312941
##    [6]  0.585442461 -0.262369016  0.645544967 -0.402646934  0.238831119
##   [11] -1.191402833  0.189763900  0.140351122  0.188402616  0.447487246
##   [16]  0.354474745 -0.149779418 -0.733935217 -0.277429989  0.560265102
##   [21] -0.022264957  0.375438186 -0.617607619  0.093632388 -0.109152427
##   [26]  0.690574131  0.273258953 -0.159491489  0.152549704  0.044034440
##   [31]  0.147230498 -0.324457847  0.351086674  0.660552989 -0.124901435
##   [36] -0.015807883 -0.012166844  0.279826230  1.116483233 -0.554660486
##   [41] -0.252300343 -0.020199936  0.148711990  0.430891417 -0.855036820
##   [46]  0.153376148 -0.250164461 -0.427494022  0.630828170  0.177590207
##   [51]  1.007317727  1.169181413  1.131960653 -0.438675226 -0.216765279
##   [56]  0.474956632  0.478851782  0.221841615  0.466890304  0.613161131
##   [61]  0.893629861  0.221145345  0.345123801 -0.659642222  0.312138942
##   [66]  0.257487800  0.343376417  0.498439983 -0.413570165  0.346384059
##   [71] -0.602122625  1.255200920  0.453879427 -0.460364944  0.671286269
##   [76]  0.271933142  1.395369951 -0.037343206  0.304115152  0.461041879
##   [81]  0.961807516 -0.037148112  0.089624630 -0.041788124  0.414236041
##   [86] -0.821991193 -0.183665539  0.085881144 -0.348660003 -0.269698788
##   [91] -0.623164643  0.227413454  0.292301637 -0.298334921  0.342738629
##   [96]  0.244001355  0.335013045  0.705388222  0.776033859  0.437626813
##  [101] -0.329264070 -0.154461189  0.559305536  0.503417071 -0.248592206
##  [106]  0.119623967 -0.385641607  0.610127368  0.232839652  0.922752864
##  [111]  0.999960941  0.469240795  0.696448580 -0.343948563  0.555038273
##  [116] -0.284421978  0.065181278 -0.226104564  0.144511413 -0.249812912
##  [121]  0.354311101 -0.144949190 -0.082053501 -0.051342433  0.711057960
##  [126]  0.281791576 -0.976583548 -0.159377621  0.484490094  0.050502227
##  [131] -0.114655973  0.161752978  0.494796395 -0.224537006  1.300722942
##  [136]  1.193035661  0.299553660 -0.200913763  0.408655110 -0.896643435
##  [141] -0.173158745 -0.241001726 -0.654963589  0.465553028  0.810719275
##  [146] -0.273675668  0.304047513 -0.283307294 -0.017985384 -0.950288656
##  [151]  0.335689022 -0.246078282  0.463997550 -0.331417252  0.055572651
##  [156]  0.400659197  0.666311931  0.285231564 -0.048657601  1.372927721
##  [161]  0.577093274  0.647342070  0.346964010  0.293005466  0.095846143
##  [166] -0.454835681  0.294358580  0.640663008 -0.198088893  0.722124655
##  [171]  0.266446782 -0.506677350  0.355786571  0.018116035  0.403929935
##  [176] -0.107331523  0.632638888  0.109349464  1.200866825  0.074042535
##  [181]  0.203287161 -0.268887152  0.497608647  0.302630816  0.074395363
##  [186]  0.571187867 -0.336819268  0.369534572  1.199796825 -0.072016441
##  [191]  0.122554781  0.759408180  0.051757943  0.670097663  0.036015450
##  [196] -0.032571654  0.141298441  0.460330131  0.199089769  0.375628854
##  [201] -0.059536070 -0.345576984  0.267627195  0.615071187  0.321718792
##  [206] -0.023226374  0.473718728  0.184783938  0.293900963  0.060075415
##  [211]  0.151254126  0.797657419  0.093224177  1.109547809 -0.026886833
##  [216]  0.801319473  0.194102820 -0.203322534  0.383532909 -0.908848245
##  [221]  0.420939996 -0.307222513  0.461261972  0.872918323  0.146379088
##  [226]  0.207635176  0.625915025 -0.270755606 -0.081072370  0.574514167
##  [231]  0.590463130  0.519482406  0.091940850  0.538672858 -0.511647630
##  [236]  0.536241858  0.056114904  0.350177040  0.424433810  0.603172648
##  [241]  0.500629484  0.750207102  0.472097468 -0.035751559 -0.206024326
##  [246] -0.022759232  0.106618319  0.665490930  0.349377868  0.522171916
##  [251] -0.311666721  0.347231107  0.081720346  0.263564851  0.050890766
##  [256] -0.601776873  0.861949203  0.717298433  1.073441378 -0.079313489
##  [261]  0.349009216  0.236397267  0.352190843  0.388695589 -0.083878438
##  [266] -0.202471611  0.161673020 -0.188838795 -0.680074911  0.210191759
##  [271]  0.696419720  0.430843141  0.503451574  0.625252021 -0.918469876
##  [276] -0.037625263 -0.195471113  0.443180012  0.166877324  0.319226030
##  [281] -0.905009466  0.033128320 -0.122935911  0.321983327 -0.962257512
##  [286]  0.823582042  0.525690200  0.161195143  0.869642284  0.653416033
##  [291]  0.178728233  0.386732058  0.520102946 -0.234711373  0.189519279
##  [296] -1.099332022  0.399609919 -0.264043054  0.199550489 -0.104692745
##  [301]  0.613817475  1.017705706 -0.190925462 -0.181722102  0.216964995
##  [306]  0.180032837  0.359130432 -0.287675894  0.642226557 -0.040714024
##  [311]  0.338096191  0.171347713 -0.438545844  0.187227138  0.347661781
##  [316]  0.878605058  0.308362670 -0.295607764  0.273286306  0.776033859
##  [321]  0.287712665 -0.612727538  0.249578647  0.565341751  0.374464527
##  [326] -0.284933448  0.288979197 -0.105028597  0.065181278  0.224944231
##  [331]  0.317052237 -0.391052620  0.168750203  0.405149014  0.018713015
##  [336]  0.575342857  0.302811990  0.313142293  0.135944117  0.395339171
##  [341] -0.493856853  0.542521754  0.808002864  0.599140504 -0.164928814
##  [346]  0.471355488 -0.255291702  0.015856709 -0.259528498  0.457817328
##  [351] -0.327173671  0.286335045  1.143910749 -0.248627866  0.814255620
##  [356]  0.623182658 -0.260458500  0.548741940  0.377634029  0.010147262
##  [361] -0.486608062  0.264819517 -0.251762558  0.143904501  0.179151438
##  [366]  0.396918839  0.692248360  0.226613805  0.502628872  0.207720741
##  [371] -0.567942427 -0.292703968  1.200017813  0.319878576  0.484123881
##  [376] -0.383817848  0.247997485  0.922575395  1.520054914 -0.836309155
##  [381] -0.209717094  0.047073372  0.071441146 -0.072900526 -0.002963949
##  [386] -0.066045955  0.074233893  0.939241615  0.388801078  0.038272302
##  [391]  0.100134228 -0.378346223  0.286593757 -0.534133264  0.357354409
##  [396]  0.814855400  0.836703109 -0.895237329 -0.384958093  0.453670015
##  [401]  0.547639281 -0.734240193  0.859976346  0.044059844  0.471874212
##  [406]  0.323261855  0.045760065 -0.111873720  0.313748749  0.694586582
##  [411] -0.241865545  0.201209992 -0.083619676 -1.902961706  0.533996511
##  [416]  0.318772884  0.333770588  0.050466284  0.732417284  0.169695727
##  [421] -0.099895745  1.174787147  0.719308241  0.626853218  0.721073078
##  [426]  0.074028396  0.509445704  0.903803643 -0.133452520  0.545046406
##  [431]  0.507456975  0.270739138  0.822559574  0.722782148 -0.047013881
##  [436]  0.092871244  0.804918442 -0.227998916 -0.128336003 -0.064889484
##  [441]  0.005291198  0.627395272  0.430888252 -0.444709453  0.447727263
##  [446]  0.879660503 -0.271444171 -0.314446961  0.170904026  0.351949071
##  [451]  0.156991654 -0.258182290  0.332177030 -0.644659139  0.777936558
##  [456]  0.050752328  0.141716680  0.601977528  1.040034672  1.044078878
##  [461]  0.667883374  0.179211141  0.123977472 -0.276741315  1.654667235
##  [466] -0.123362811  0.465841237 -0.645385901  0.607510413 -0.634809537
##  [471] -0.101108249  0.080195057 -0.080285545 -0.580273917 -0.631608785
##  [476] -0.726523427 -0.080071857 -1.231778108 -0.191791752  0.835637809
##  [481] -0.325955470 -0.155721499  0.565514190  0.019299515 -0.029792972
##  [486] -0.053509491  0.355444364  0.347231107 -0.063175913  0.334546367
##  [491]  0.755134287  1.067017468 -0.560702194 -0.645385901  0.033002631
##  [496] -0.104839591 -0.493010782  0.440646735  0.357238361  0.036134985
##  [501]  0.226953154 -0.875522132 -0.063953324 -0.135871300 -0.642031938
##  [506] -0.281078456  0.133828862 -0.100690431 -0.150143593  0.382459668
##  [511]  0.194840273  0.500404631  0.256550278 -0.488114038  0.170507869
##  [516] -0.305747437  0.786700036 -0.409721703  0.677553395 -0.149659593
##  [521]  0.499531664  0.352916051 -0.275815525  0.711251861  0.163977658
##  [526]  0.425112597 -0.035981497  0.277846200  0.435056870 -0.326645048
##  [531]  0.307266993  0.542018702  0.148711990  0.254568366 -0.226358440
##  [536]  0.299700189  0.862796979  1.423691337  0.251519960  0.438037098
##  [541] -0.427409596 -0.206354698  0.254075737  0.431277491  0.123270993
##  [546]  0.190307543  1.265806838  0.575579821 -0.012894732 -0.226558384
##  [551]  0.766258798  0.008481214 -0.458739582  0.601697480 -0.107013444
##  [556]  0.040697126 -0.574528589 -0.256581946 -0.241001726  0.485558900
##  [561]  0.195525329 -0.074520416 -1.153816413 -0.543488513  0.641729809
##  [566]  0.326993444  0.428876350  0.395879264  0.543552401  0.563790041
##  [571]  0.282344967 -0.256557938  0.795858049  0.141628772  1.072575160
##  [576]  0.206383322 -0.063670769  0.133643983  0.426315566  0.178711306
##  [581]  0.282027516  0.367705547  0.136742575  0.594907142  0.235211808
##  [586] -0.253374781  1.032379340  0.642709850  0.221801526 -0.700704303
##  [591] -0.140340420 -0.234598478  0.279829316 -1.106863347  0.931985012
##  [596]  0.327210931  0.599687895 -0.230632929  0.096878782 -0.626410072
##  [601]  0.610922812  0.656525899 -0.387712518  0.878094571  0.034441796
##  [606] -0.207665906  0.243965514  0.586618522 -0.127958372  0.556558822
##  [611]  0.120259914 -0.616070196  0.699311483 -0.026886833 -0.257079184
##  [616] -0.121720854  0.544277124  0.600625355  0.640766102 -0.509386493
##  [621]  0.308479422  0.252190047  0.341744500  0.947554718  0.458390954
##  [626]  0.001573574  0.091940850  0.250007106  0.507289912  0.445957710
##  [631]  0.876066856 -0.167887028  0.152434939  0.129815824 -0.434247789
##  [636]  0.842157179  0.620913793  0.477192545  1.011579090 -0.088706237
##  [641] -0.743583038  0.599490965  0.386804990 -0.003074714  0.393783414
##  [646]  0.754860528  0.607561999  0.346317116 -0.598937935  1.008654353
##  [651] -0.047474225 -0.157866536 -0.066631908  0.381608903 -0.501096961
##  [656]  0.445594144  0.242157901  0.687541430 -0.039310312  0.457817328
##  [661]  0.466932023  0.312651796  0.018898251  1.023908619  0.132742561
##  [666]  1.300722942 -0.785788776  0.847297250  0.507553950 -1.085379248
##  [671]  0.787241786  0.393551197  1.087041470  0.304287136 -0.194379164
##  [676] -0.361572374  0.642097212  1.350234677  0.633816214 -0.563419856
##  [681]  0.985352666 -0.273828161  0.354311101 -0.868598711  0.268156459
##  [686]  0.232461894  0.493911756 -0.039704620 -0.215073648  0.547330064
##  [691]  0.035175338 -0.626884595  0.336068355  0.305142901  0.368272996
##  [696]  0.001573574  0.217506768 -0.378012087  0.384238465  0.561289056
##  [701]  0.004281782  0.934309375  0.079556366  0.832913600  0.370799350
##  [706]  0.271031709  0.153276624 -0.015868509  0.476205770  0.002817604
##  [711] -0.089505876  0.437267302 -0.329433776  0.634750519  0.598991780
##  [716]  0.926040742  0.498748139 -0.037542005 -0.392828115  0.593285582
##  [721] -0.232400793 -0.523782524  1.027753837  0.645867225  0.770498424
##  [726]  0.225294669 -0.073996512  0.799705161  0.735563748  0.997832688
##  [731] -0.144604612 -0.253650902  0.232852660 -0.454217898  0.085431975
##  [736] -0.314265078 -0.609151007  0.035058894  0.447946002 -0.272576124
##  [741] -0.255509633 -0.062823636  0.028679782  0.078808505  1.034947763
##  [746]  1.116812288  0.615182316  0.671834345  0.652690871 -0.320819244
##  [751] -0.260933908  0.774207853  0.011492880  0.399967961  1.285014395
##  [756]  0.437971707  0.657612866 -0.754632874  0.452365281 -0.514282408
##  [761]  0.192133810 -0.209357659  0.604692340  0.030360131  0.442923747
##  [766] -0.021863092  0.099595274  0.103699731  0.169188369  0.508930617
##  [771]  0.714446055  0.416380382  0.059698130  0.067012994  0.188975411
##  [776]  0.541700407 -0.308699293  0.295726218  0.538381800  0.360610198
##  [781]  0.380729207  0.486719545  0.285094446 -0.097507551 -0.358333284
##  [786]  0.476865715  0.350094740  0.745112781  0.839280617 -0.393230250
##  [791]  0.642653800  0.186818708  0.316221918  1.068417517  1.000898129
##  [796]  0.124760557  0.597115357 -0.152492101 -0.604233098  0.306726045
##  [801]  0.250852575  0.285512021  0.777357928  0.227507943 -0.516504015
##  [806]  0.945019478  0.813578527  1.033639324  0.031409384  0.696852079
##  [811] -0.114760096  1.315240489  0.279028541  0.551702605  0.291772202
##  [816] -0.747608430 -0.184585185  0.359130432 -0.808717785 -0.089865086
##  [821]  0.335170209  0.345539593  0.514412600  0.218173327  0.581168966
##  [826]  0.208730276  0.010716261 -0.542385736  0.091526759  0.211874053
##  [831] -0.076356623  0.046300093  0.511070413  0.636379349  0.212590262
##  [836]  0.531582444  0.323749915  0.495926510  0.668473838  0.042289936
##  [841] -0.009435444  0.624508288  0.261821113  0.240505693  0.190414491
##  [846]  0.400752397  0.787516843  0.571973278 -0.389110911  0.786209735
##  [851]  0.813129503  0.948290734  0.265446146  0.018088578 -0.801850563
##  [856] -0.436084993  0.253046165 -0.271540461  0.433291180 -0.367817668
##  [861] -0.087668123  0.146790328 -0.338456066  0.201704022  0.123748780
##  [866] -0.044498412  0.144831701  1.514385791  0.141005087  0.192424137
##  [871] -0.133482201  0.649697280  0.168928764 -0.137523695  0.159669872
##  [876] -0.172895392  0.398663872 -0.016213968  0.423330662 -0.330360856
##  [881] -0.085049231  0.589585560  0.260936178  0.629669610  0.955286051
##  [886]  0.697271014 -0.302809550 -0.282638577  0.512755471 -0.203464870
##  [891]  0.600412379 -0.304850422  0.237651167  0.036316501  0.814381751
##  [896]  0.815313794  0.826807318  0.453747313 -0.413786953  0.140760631
##  [901]  0.170569149 -0.366935923  0.355908215  1.390627441  0.296549049
##  [906]  0.646766531 -0.406118987 -0.006966186  1.152949029 -0.429331164
##  [911]  0.231000000  0.752341632  0.311669623  0.254655992  0.278775719
##  [916]  0.403507066 -0.013899874  0.447618806 -0.170252067  1.835337897
##  [921]  0.538937643  0.468903508  0.337619440  0.113855297 -0.238986189
##  [926]  0.342614444  0.366827095  0.363000465  0.143387457 -0.027485324
##  [931]  0.285852560  0.042076492 -0.061984902 -0.489859939  1.130649596
##  [936]  0.327396737  0.496469412  0.125045639  1.116483233  0.429621019
##  [941] -0.013106039 -0.436768951  0.210001052  0.133356337  0.856655536
##  [946] -0.315098017 -0.316175192  0.761819891  0.665001220  0.302166490
##  [951]  0.533882094  1.757441052  0.457142858 -0.099597189  0.762731454
##  [956]  0.247503382  0.170594838  0.120688696  0.278775719 -0.350574187
##  [961] -0.369668277 -0.301181060  0.528774349  0.607892313  0.560952604
##  [966]  0.335909276  0.730399132 -0.226885033  0.387763262 -0.150759008
##  [971] -0.622360508  0.024453931  0.714040403 -0.251317267 -0.297558541
##  [976] -0.188217573  1.089442349  0.129714904  0.746421245  0.406570172
##  [981]  0.182595896  0.253825043 -0.509609220  0.029038695 -0.692143781
##  [986]  0.722272244 -0.132184653 -0.167895138  0.213032779 -0.434506292
##  [991] -0.363352090 -0.863881648 -0.043451540 -0.267775925 -0.357721352
##  [996]  0.497737480  0.643830464  0.639336794 -0.647022910 -0.039117727
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
##   0.63368249   0.21136893 
##  (0.06684073) (0.04725885)
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
## [1] -0.93254054 -1.44227604 -0.42803013  0.01350051  0.65209011 -1.32626282
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
## [1] 0.0166
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8610493
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
## t1*      4.5 -0.04454454   0.9427435
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 6 8 
## 2 3 1 2 2
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
## [1] -0.001
```

```r
se.boot
```

```
## [1] 0.9352613
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

