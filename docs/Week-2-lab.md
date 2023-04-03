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
## 0 1 4 5 6 8 
## 2 1 1 2 2 2
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
## [1] 0.0142
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
## [1] 2.741905
```

```r
UL.boot
```

```
## [1] 6.286495
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
##    [1] 2.7 4.2 4.5 4.0 3.6 5.5 4.9 5.2 4.0 4.1 5.2 4.8 6.4 4.3 2.7 2.9 3.2 6.1
##   [19] 5.4 5.8 3.9 3.6 3.2 5.5 5.7 4.4 4.7 5.9 4.2 4.8 4.4 3.8 4.6 4.9 4.4 6.8
##   [37] 5.0 4.8 4.5 5.6 5.8 3.5 4.9 4.9 4.0 4.5 3.8 4.6 3.8 3.5 3.5 3.4 4.0 3.2
##   [55] 5.9 4.8 4.1 4.8 4.5 5.3 4.3 3.0 4.1 4.1 4.2 6.7 4.6 3.7 3.7 4.4 3.4 3.2
##   [73] 2.2 3.5 3.7 4.9 2.8 5.3 5.3 4.0 4.5 6.6 3.9 4.9 5.4 4.1 4.6 5.0 5.6 4.5
##   [91] 4.1 3.3 4.4 3.9 2.9 4.9 4.8 4.7 3.6 3.6 4.8 3.5 5.3 6.2 5.0 4.7 5.2 4.5
##  [109] 3.0 5.1 5.2 4.2 5.1 3.6 3.8 3.7 3.0 4.8 5.2 3.6 4.3 3.3 4.7 5.2 3.4 4.2
##  [127] 3.9 5.7 4.6 4.2 5.0 3.5 4.7 5.1 4.8 4.9 4.9 5.3 6.5 5.7 5.7 6.2 4.0 4.7
##  [145] 3.8 5.7 6.6 4.1 3.7 4.1 4.7 5.2 5.4 3.9 5.2 3.9 4.1 5.3 4.9 4.1 4.4 4.3
##  [163] 5.2 5.3 5.7 5.5 4.0 5.4 4.2 4.5 5.6 4.8 6.4 5.7 6.2 3.8 5.4 3.7 4.7 3.5
##  [181] 3.4 4.7 4.4 4.0 6.2 5.1 4.9 4.3 4.2 4.0 5.1 4.8 3.5 4.5 4.0 3.6 4.0 3.7
##  [199] 4.7 5.4 5.3 3.6 4.2 5.1 5.3 3.7 4.5 5.1 2.7 5.0 4.7 3.7 5.2 5.3 3.7 4.3
##  [217] 5.7 4.6 6.2 4.3 6.4 4.4 3.2 2.3 6.5 3.8 5.2 3.4 5.0 4.8 3.9 5.8 3.8 6.1
##  [235] 4.6 5.3 5.2 4.0 4.4 5.8 4.5 2.8 1.9 3.3 3.4 4.4 5.2 4.9 4.2 4.1 5.0 4.4
##  [253] 4.6 4.6 6.0 4.5 5.4 5.0 5.4 3.9 3.7 3.6 6.0 3.3 4.8 4.1 4.7 6.7 4.0 4.6
##  [271] 3.1 5.5 3.9 4.1 4.5 3.6 5.2 4.0 3.2 3.0 3.2 4.6 5.0 6.3 3.0 5.3 4.8 3.4
##  [289] 4.3 5.8 6.2 3.8 5.2 5.0 4.9 3.4 3.8 4.6 3.5 4.4 2.9 4.6 4.1 6.7 4.5 4.8
##  [307] 4.0 4.5 5.8 5.1 5.5 3.7 6.2 3.7 5.0 5.2 4.1 4.7 6.2 3.7 6.0 4.8 3.6 4.8
##  [325] 5.3 5.5 3.9 5.0 5.1 5.7 4.9 3.5 5.4 5.3 4.2 5.2 4.6 5.2 4.0 3.7 4.7 4.9
##  [343] 3.6 4.4 4.3 5.1 5.1 4.6 4.5 3.7 5.2 3.8 4.3 3.2 5.0 4.1 4.5 4.4 4.5 4.5
##  [361] 4.0 5.6 6.0 3.3 3.4 5.2 5.5 4.7 3.4 4.1 4.4 6.2 5.5 5.3 5.0 4.3 4.0 4.0
##  [379] 4.5 4.7 4.9 5.3 4.5 3.6 3.7 6.7 3.7 4.0 5.6 4.5 5.0 4.2 6.7 4.2 4.6 4.4
##  [397] 4.7 4.5 3.3 5.1 3.9 5.1 4.8 4.4 5.1 5.0 4.0 4.4 5.6 5.4 3.5 3.4 2.9 3.9
##  [415] 3.8 4.7 4.9 5.3 4.2 4.1 4.6 2.1 6.8 3.3 4.1 4.1 3.4 5.4 4.9 4.4 4.7 4.2
##  [433] 5.0 3.0 5.0 4.9 4.8 5.7 3.3 4.0 3.2 4.3 2.5 4.3 3.8 5.5 3.4 4.4 4.8 5.0
##  [451] 4.2 4.9 5.7 3.3 5.3 4.2 4.7 3.7 3.5 4.3 7.1 4.8 4.7 6.5 4.2 4.5 6.0 4.7
##  [469] 5.5 4.4 5.0 3.8 3.9 4.5 5.5 3.8 3.4 2.9 2.5 5.0 5.3 6.1 3.7 5.8 4.7 4.3
##  [487] 6.3 3.5 5.6 3.2 3.7 4.6 4.6 4.2 4.2 3.9 2.7 4.4 4.9 6.0 5.1 4.3 4.3 4.1
##  [505] 3.9 4.8 5.1 5.9 3.3 5.1 2.9 4.2 5.7 4.4 4.3 5.4 4.8 3.9 4.7 5.2 4.2 2.8
##  [523] 3.3 6.1 4.8 4.5 3.1 4.0 5.5 4.9 4.3 4.2 3.3 5.1 5.0 5.8 5.2 4.2 4.2 2.6
##  [541] 3.2 4.7 4.2 5.6 2.3 4.5 4.0 5.9 2.3 3.2 4.0 4.3 4.9 3.9 4.9 3.0 3.3 3.3
##  [559] 4.6 3.1 3.3 3.5 5.0 4.7 6.2 4.2 4.1 5.1 4.4 4.5 5.3 4.1 5.2 3.6 4.6 3.3
##  [577] 3.6 5.1 5.5 4.8 4.6 2.8 3.7 4.0 4.3 4.3 4.8 4.7 3.8 4.9 5.2 5.1 6.0 4.8
##  [595] 4.5 3.8 4.6 3.3 2.5 4.6 4.5 4.9 5.3 5.1 3.5 2.1 5.3 3.2 4.8 4.2 5.2 3.4
##  [613] 6.2 5.1 3.3 4.2 3.5 5.0 4.0 4.6 5.4 2.9 3.6 5.2 2.9 5.3 4.0 5.3 4.6 3.6
##  [631] 5.5 4.2 4.9 4.3 4.9 4.3 3.6 4.3 5.4 5.5 3.3 5.6 4.7 3.5 6.0 4.5 4.3 4.4
##  [649] 2.7 4.6 3.7 4.5 5.7 3.0 4.0 4.7 4.0 3.5 4.5 3.2 5.2 4.6 3.2 5.9 5.0 3.9
##  [667] 3.4 4.2 4.3 5.9 5.6 2.3 4.4 6.3 3.8 4.1 3.5 6.2 3.7 4.9 3.9 7.0 4.5 4.7
##  [685] 5.7 2.7 5.9 5.7 4.3 2.8 4.3 3.4 5.7 4.8 5.6 4.9 4.3 4.1 4.5 4.0 3.7 3.9
##  [703] 3.9 4.7 4.2 5.3 5.5 4.8 3.9 4.6 3.6 4.3 3.9 5.1 3.6 4.0 5.6 4.9 4.1 5.2
##  [721] 7.0 4.0 4.5 4.0 5.4 4.2 2.8 4.3 4.2 5.3 3.6 4.6 3.6 6.2 4.1 4.8 4.6 5.7
##  [739] 3.2 4.4 4.7 3.1 6.2 3.3 4.4 4.8 4.7 6.2 5.4 4.9 3.5 4.5 3.8 4.7 5.5 4.0
##  [757] 4.5 3.9 3.6 5.2 4.0 5.6 3.5 5.4 4.8 3.8 2.6 5.0 4.0 5.2 4.8 4.0 4.6 3.4
##  [775] 3.9 3.6 5.6 4.4 4.3 3.7 4.0 3.6 5.1 4.1 5.4 5.2 4.5 6.5 6.2 3.8 5.1 3.3
##  [793] 4.3 4.4 4.0 4.5 3.8 5.3 6.0 3.4 3.9 5.3 4.3 4.7 6.4 4.3 5.1 5.2 4.0 4.1
##  [811] 3.4 4.7 4.9 3.7 4.4 5.0 4.4 3.4 3.5 3.2 6.1 3.3 4.3 5.0 6.2 4.4 4.0 3.8
##  [829] 5.7 4.3 3.8 3.4 3.6 4.8 6.5 3.0 3.9 3.7 6.0 5.1 5.2 5.3 4.4 3.8 5.0 3.7
##  [847] 4.6 6.2 5.5 3.6 4.0 4.0 3.8 3.2 5.9 4.1 3.6 5.6 5.4 4.9 4.7 5.6 3.4 3.8
##  [865] 6.5 2.9 5.4 5.1 4.5 5.0 4.2 4.9 4.2 5.1 5.1 3.6 3.8 5.1 4.5 4.6 3.8 4.2
##  [883] 4.3 5.0 5.1 4.6 4.2 3.5 4.2 6.2 4.5 5.3 5.4 5.0 5.4 2.5 3.9 4.7 4.9 3.2
##  [901] 4.4 2.5 5.3 4.4 3.2 4.7 3.3 4.4 5.6 4.9 5.5 4.7 5.4 4.6 5.8 5.2 4.1 5.2
##  [919] 4.4 3.6 4.5 4.5 5.3 5.3 5.6 5.4 4.7 1.9 4.6 5.6 6.0 5.9 4.6 4.9 2.9 5.5
##  [937] 3.1 3.9 5.4 3.9 4.5 5.3 4.9 3.3 5.6 2.8 3.4 3.6 3.5 3.8 5.7 3.5 4.6 5.7
##  [955] 4.1 3.7 5.7 3.9 4.0 4.5 3.7 5.6 4.0 4.3 5.3 5.8 6.9 2.8 5.0 3.4 5.1 4.3
##  [973] 5.6 5.5 4.5 3.5 3.8 4.0 4.0 4.4 5.5 6.2 4.4 4.0 4.2 5.9 5.5 4.8 4.6 4.9
##  [991] 5.7 5.4 4.0 6.6 3.5 4.7 5.6 3.7 5.6 4.7
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
##    [1] 4.3 4.4 2.0 5.4 5.4 2.9 4.2 5.1 4.9 2.5 5.1 5.8 5.0 4.6 5.1 5.6 4.4 5.5
##   [19] 5.1 4.9 6.7 4.4 6.0 2.7 4.2 5.8 3.6 4.9 6.0 5.8 4.1 3.3 5.5 4.5 4.4 3.5
##   [37] 6.2 3.6 1.6 3.5 3.4 5.3 2.8 4.8 3.4 4.3 4.5 4.7 5.4 4.1 4.5 4.8 4.7 3.2
##   [55] 4.1 4.7 4.0 3.6 4.4 4.5 5.7 3.2 3.5 5.8 5.6 3.1 4.2 3.5 5.9 3.3 4.7 5.2
##   [73] 3.0 3.4 3.8 4.9 6.4 3.8 4.0 4.8 6.2 5.4 4.2 4.1 4.2 3.3 3.8 5.0 4.1 4.8
##   [91] 4.9 4.7 3.7 5.7 5.5 4.7 4.3 5.7 2.8 5.9 5.1 5.9 4.5 5.2 3.5 4.2 3.3 4.7
##  [109] 4.6 5.9 4.9 3.4 5.0 3.7 4.9 4.7 3.0 4.8 4.0 5.8 5.6 4.5 5.0 5.4 4.9 5.2
##  [127] 5.8 5.8 6.3 4.2 4.7 4.3 4.0 5.9 4.0 4.7 3.0 4.8 5.3 3.7 3.7 4.2 2.8 3.4
##  [145] 6.1 6.5 4.9 3.5 4.5 5.4 4.0 4.0 5.9 5.4 5.9 3.6 4.3 5.7 4.3 3.6 3.0 4.9
##  [163] 6.0 4.3 2.9 3.9 4.2 5.8 5.9 4.4 3.8 5.2 2.5 4.1 4.9 4.6 5.2 5.2 3.3 3.3
##  [181] 3.9 5.2 3.7 5.2 4.6 2.4 5.1 4.7 3.9 5.2 4.4 5.0 3.9 4.7 4.4 4.9 4.0 5.5
##  [199] 3.8 2.5 6.6 3.9 3.0 5.0 5.0 4.8 5.1 4.3 4.3 4.3 4.8 3.8 5.1 4.5 4.6 4.7
##  [217] 4.4 4.0 5.9 3.9 3.3 4.8 5.7 5.8 4.3 5.2 3.3 3.4 5.7 3.3 4.4 3.4 5.8 5.7
##  [235] 5.6 4.1 3.1 5.5 3.9 4.5 5.2 6.1 3.3 3.8 3.4 4.1 4.6 4.5 3.3 4.2 5.1 4.9
##  [253] 3.4 3.4 4.4 3.2 4.3 5.7 4.5 4.3 4.2 5.4 3.6 4.5 4.0 4.3 3.8 4.0 5.8 3.4
##  [271] 3.2 3.6 4.5 3.9 5.2 4.1 4.2 5.2 4.6 6.2 4.2 4.7 4.9 4.6 3.5 3.4 2.6 4.4
##  [289] 5.8 5.8 3.5 3.9 3.9 2.3 5.9 3.5 5.4 4.2 4.5 5.0 4.0 4.0 4.1 5.8 3.8 4.5
##  [307] 4.2 3.5 5.1 4.0 5.1 4.7 3.8 4.9 4.8 4.5 5.5 3.6 5.7 4.4 2.4 3.4 4.1 6.9
##  [325] 6.2 4.5 5.3 5.3 3.5 4.0 4.6 6.0 4.2 3.4 4.1 3.4 3.3 4.2 5.2 3.5 3.1 3.4
##  [343] 4.9 5.6 4.8 3.8 5.6 4.3 4.9 4.0 4.8 3.2 4.9 4.0 4.0 4.6 4.0 4.6 4.6 5.6
##  [361] 5.2 4.7 5.1 5.1 5.3 4.0 3.1 4.3 3.2 4.7 2.7 5.2 5.0 3.7 4.8 5.5 3.7 3.7
##  [379] 3.3 2.7 4.5 3.7 4.3 3.6 4.9 4.6 4.5 4.2 6.0 5.5 4.0 3.6 4.2 4.2 4.9 4.9
##  [397] 5.8 5.1 5.9 3.4 5.5 3.3 5.1 3.6 4.9 5.4 5.9 2.8 6.0 6.1 4.2 6.3 4.1 4.2
##  [415] 5.1 5.2 4.5 5.8 4.6 4.8 5.2 3.0 4.5 3.4 5.6 3.9 4.0 5.6 4.9 5.0 4.8 3.6
##  [433] 4.8 5.8 3.9 3.7 4.4 5.8 5.0 4.4 5.2 4.2 4.4 4.5 4.3 4.0 5.9 4.3 3.6 3.8
##  [451] 4.5 4.9 2.6 5.2 3.1 6.2 2.3 4.0 4.4 5.6 6.1 4.1 3.5 4.4 5.5 4.3 3.7 5.3
##  [469] 6.0 3.2 3.9 4.7 3.5 5.5 4.1 4.6 4.6 3.7 4.3 4.9 4.3 4.2 3.4 3.4 5.3 5.3
##  [487] 2.1 4.1 3.7 3.8 4.0 5.4 6.0 6.3 5.3 5.5 4.9 3.3 3.5 5.1 3.7 5.0 3.2 4.8
##  [505] 4.7 4.3 4.2 5.5 4.8 3.7 3.7 6.3 6.1 3.4 3.2 4.6 3.7 4.6 4.6 4.6 5.4 5.3
##  [523] 6.1 4.4 4.5 2.6 4.1 3.3 3.9 5.3 3.2 4.2 5.5 4.6 4.2 3.8 4.5 5.3 4.5 2.9
##  [541] 4.8 4.5 2.0 6.0 5.4 4.7 4.7 3.6 5.9 4.8 4.4 5.5 5.3 5.0 4.2 4.4 5.7 4.9
##  [559] 4.4 4.2 4.8 5.2 5.0 4.5 5.3 3.6 4.0 4.5 5.7 4.3 4.5 4.2 5.2 2.9 5.0 5.2
##  [577] 4.8 5.4 3.6 3.9 4.6 3.2 4.1 3.2 3.3 5.0 3.4 4.7 3.9 5.5 4.9 3.9 4.8 4.7
##  [595] 4.2 3.6 4.9 4.4 4.1 4.2 5.0 4.4 6.8 3.7 4.9 3.8 4.6 3.8 3.3 3.3 3.5 4.8
##  [613] 5.2 2.8 7.7 4.8 5.8 3.1 3.7 6.2 5.3 4.8 4.6 2.9 6.4 3.9 3.5 5.4 5.0 5.3
##  [631] 4.8 3.3 4.9 4.8 6.0 3.5 4.3 4.2 4.4 4.4 4.9 5.2 5.6 3.0 3.2 3.8 5.6 5.0
##  [649] 4.5 5.5 4.8 3.1 4.5 5.0 3.3 2.6 4.8 3.7 5.0 4.7 2.8 6.0 3.9 6.5 4.5 3.1
##  [667] 4.5 5.5 3.7 4.9 5.2 4.8 3.5 5.5 3.5 4.9 3.1 4.7 3.4 5.1 2.8 4.2 5.2 5.0
##  [685] 3.0 5.1 5.9 4.6 4.5 4.1 4.7 3.0 4.2 2.6 4.8 4.6 5.2 4.2 5.3 3.9 4.9 5.1
##  [703] 4.3 4.5 4.4 5.4 4.0 3.7 4.8 4.8 3.7 7.5 3.6 5.6 5.1 3.9 5.1 4.8 4.5 4.7
##  [721] 4.4 4.9 3.4 2.3 3.9 3.4 3.4 4.2 5.6 4.0 4.9 3.8 5.6 3.9 5.9 5.6 6.0 3.4
##  [739] 4.9 4.6 4.8 3.7 3.9 4.5 4.3 4.4 5.7 5.1 4.5 5.6 5.0 3.5 4.8 2.9 5.1 4.8
##  [757] 4.8 4.4 3.7 4.9 5.0 4.3 5.3 4.3 4.5 5.1 4.5 5.1 4.9 5.7 4.0 4.4 4.7 3.8
##  [775] 4.1 4.6 3.2 5.1 5.5 3.7 4.0 2.6 2.2 3.5 3.8 4.3 4.9 5.0 4.2 3.1 3.5 4.1
##  [793] 3.6 6.3 3.9 5.0 4.4 5.1 6.8 4.0 4.6 3.5 4.4 5.9 4.0 4.8 5.8 6.3 5.4 3.6
##  [811] 4.9 4.3 4.4 3.5 3.6 4.6 5.0 4.5 3.6 4.8 5.4 4.2 3.9 4.7 4.2 4.2 3.1 4.8
##  [829] 3.5 4.0 4.0 4.8 5.4 4.8 6.1 4.1 5.8 4.3 4.2 3.5 4.8 5.0 4.7 5.5 5.2 5.0
##  [847] 4.1 3.8 4.4 3.1 5.1 3.3 4.0 3.2 4.2 4.1 5.4 5.2 3.7 4.2 5.1 4.5 5.2 5.4
##  [865] 4.4 4.8 3.9 4.2 4.2 4.5 5.0 4.7 5.5 5.0 5.5 3.5 3.5 6.0 3.6 3.9 3.1 4.9
##  [883] 5.5 4.2 4.4 5.4 4.1 6.1 3.8 5.3 3.7 4.8 5.1 4.8 4.0 3.7 4.3 6.3 5.3 4.2
##  [901] 5.3 5.1 5.8 4.8 3.7 3.8 3.9 2.2 5.0 3.4 3.4 3.9 5.4 4.3 5.6 4.8 3.6 5.4
##  [919] 3.5 4.7 3.3 4.9 4.9 5.7 4.8 3.8 5.3 5.2 4.1 4.6 4.3 6.2 4.8 4.8 4.7 4.9
##  [937] 5.4 4.7 4.4 5.2 2.9 4.4 4.7 3.8 3.9 3.6 5.4 4.3 4.5 4.9 4.1 3.9 3.8 4.6
##  [955] 4.8 2.9 4.5 5.5 4.5 4.9 3.4 4.4 2.9 3.1 2.7 2.7 5.3 3.9 4.0 5.4 4.7 4.8
##  [973] 3.8 3.5 3.3 4.3 5.6 4.2 1.5 3.1 4.7 5.5 4.3 5.3 4.7 3.9 4.3 5.5 5.8 4.0
##  [991] 4.1 3.8 3.5 5.3 3.7 3.7 4.3 4.2 4.9 4.5
## 
## $func.thetastar
## [1] -0.0249
## 
## $jack.boot.val
##  [1]  0.50970874  0.38184438  0.22823529  0.13575581  0.01688312 -0.12057292
##  [7] -0.21054217 -0.25597484 -0.41178082 -0.49572650
## 
## $jack.boot.se
## [1] 0.9539695
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
##    [1] 4.5 3.6 4.0 4.1 4.1 3.4 4.6 3.9 5.7 5.4 5.0 5.8 5.7 3.9 3.3 3.8 5.2 4.0
##   [19] 4.5 3.8 5.6 5.2 4.2 4.4 4.6 5.7 4.2 4.2 4.1 5.5 5.5 4.1 4.9 3.8 3.7 4.3
##   [37] 2.0 3.6 5.0 4.1 3.4 5.5 4.9 4.7 4.6 6.2 6.0 6.6 3.3 3.5 5.0 6.7 4.0 4.7
##   [55] 3.9 4.6 3.4 3.2 5.2 4.4 4.4 3.9 4.1 5.2 3.2 3.5 2.1 5.5 4.4 6.0 5.6 5.1
##   [73] 5.1 4.8 2.8 4.2 3.8 3.4 4.1 5.2 5.4 4.4 5.6 5.4 5.1 4.9 5.7 4.6 5.0 5.8
##   [91] 5.0 5.0 5.3 4.5 5.2 5.1 4.0 4.4 3.8 6.8 4.6 4.9 5.1 4.0 5.4 4.3 4.8 5.0
##  [109] 5.9 4.4 3.8 4.5 5.6 3.2 3.9 5.3 5.5 2.8 3.9 4.5 5.2 4.5 3.9 6.3 5.8 3.2
##  [127] 4.0 5.6 4.3 3.4 4.0 4.9 5.3 4.0 3.8 3.7 4.7 4.4 4.1 4.6 4.7 3.7 5.0 3.5
##  [145] 3.7 4.0 5.5 3.2 5.8 5.5 5.2 5.5 3.9 4.1 3.1 4.2 4.0 3.7 5.0 3.2 6.5 4.7
##  [163] 3.6 5.2 6.1 4.6 5.1 6.0 3.7 3.3 3.1 5.0 4.8 3.7 5.1 5.0 4.1 4.6 2.8 5.3
##  [181] 4.1 6.5 4.4 3.3 6.6 4.2 4.5 5.1 4.4 4.4 3.1 6.2 5.4 3.4 5.0 5.0 4.1 4.2
##  [199] 4.6 4.3 1.9 3.4 4.0 4.0 6.1 5.6 4.3 5.3 3.9 4.2 3.7 4.7 4.8 3.5 3.2 5.8
##  [217] 4.5 4.8 4.5 5.5 5.4 5.5 4.0 5.3 5.3 3.5 4.0 6.0 5.4 4.3 4.8 4.4 5.4 3.4
##  [235] 4.8 5.6 5.0 4.8 5.2 4.3 5.0 6.0 4.5 5.2 5.5 5.2 2.6 3.8 3.8 4.2 4.6 3.2
##  [253] 6.1 4.7 4.7 3.7 5.6 5.1 4.3 3.9 4.8 3.2 5.5 4.7 5.4 5.3 5.1 5.5 4.7 4.8
##  [271] 3.0 4.9 5.0 6.6 3.6 4.7 4.0 5.2 6.4 4.5 4.7 5.1 5.9 4.2 4.0 4.3 3.0 6.3
##  [289] 4.1 4.4 4.3 5.9 3.3 5.6 3.5 3.7 5.4 3.8 4.3 4.5 5.2 5.8 6.2 3.7 4.0 6.2
##  [307] 3.7 4.7 4.5 5.2 5.9 5.3 3.3 4.1 5.0 6.2 4.9 4.7 4.5 3.9 3.8 3.5 4.5 3.6
##  [325] 4.6 3.5 5.3 5.0 4.8 3.7 3.5 2.9 4.2 4.0 5.6 5.1 4.7 5.1 5.3 5.3 4.9 5.9
##  [343] 5.2 2.7 5.8 3.2 4.5 3.8 3.8 5.4 3.6 3.3 4.9 5.8 6.0 4.3 4.1 6.2 4.6 4.0
##  [361] 4.6 2.9 4.4 3.5 5.2 3.4 3.3 4.2 3.8 5.5 3.9 5.2 2.7 4.7 3.9 4.5 5.4 5.0
##  [379] 5.4 4.1 3.0 5.0 3.9 4.2 4.0 3.8 4.8 4.5 4.5 4.6 3.0 5.4 4.2 4.6 4.5 5.3
##  [397] 6.1 5.1 3.6 5.4 5.1 6.0 4.6 3.8 5.3 5.1 5.7 3.2 4.5 4.3 3.0 4.0 3.8 3.7
##  [415] 5.2 4.4 4.0 5.0 4.5 4.8 3.6 4.3 4.5 5.5 4.9 4.6 3.3 3.9 4.5 4.6 5.7 4.1
##  [433] 4.0 4.8 3.9 4.8 4.7 4.7 4.1 3.2 3.8 5.8 4.6 3.7 4.1 5.5 3.0 5.5 5.5 2.8
##  [451] 4.7 4.3 7.0 6.4 4.2 4.9 3.0 4.6 3.0 6.1 4.5 3.8 4.5 4.2 3.6 4.5 3.3 5.8
##  [469] 3.4 4.5 5.1 5.9 5.7 4.1 5.4 2.2 2.1 4.3 5.2 2.9 4.6 4.4 5.2 3.6 3.7 3.9
##  [487] 6.0 5.3 3.5 5.1 5.7 5.3 4.9 3.7 5.8 5.5 4.7 5.3 4.1 4.9 4.7 6.1 3.3 5.0
##  [505] 3.9 4.9 5.0 4.3 4.3 5.8 5.4 4.9 4.6 4.8 4.5 5.6 4.0 4.6 5.5 2.4 4.3 3.4
##  [523] 3.9 4.6 5.1 4.7 5.5 5.3 5.4 5.9 2.9 5.1 2.3 4.3 4.1 3.5 4.0 4.8 2.1 5.6
##  [541] 5.4 4.5 5.6 3.8 3.8 4.7 5.7 6.7 5.9 5.2 4.3 3.6 4.6 5.2 6.4 3.5 3.2 3.9
##  [559] 4.1 4.0 5.2 5.7 3.2 5.8 4.3 4.4 5.0 5.4 4.2 5.9 3.4 4.8 4.9 5.3 4.0 4.0
##  [577] 4.4 4.4 4.2 4.5 4.2 2.4 5.1 4.8 4.6 5.2 4.8 4.4 5.0 5.3 4.1 4.7 4.2 4.0
##  [595] 6.0 5.4 5.4 4.2 6.4 4.4 5.2 4.8 5.6 4.3 4.7 5.2 5.2 4.9 6.4 4.6 6.4 5.2
##  [613] 5.1 4.4 5.7 3.9 4.6 3.7 5.6 5.0 5.3 5.2 3.6 3.7 5.6 4.0 4.0 5.1 4.1 4.4
##  [631] 5.6 3.6 6.1 3.2 5.5 4.9 4.9 5.4 3.9 4.5 4.5 4.9 3.1 4.2 3.4 6.0 4.1 4.2
##  [649] 2.7 4.0 3.2 4.0 3.7 4.2 3.8 2.8 5.5 3.2 4.6 4.7 3.9 5.4 4.5 5.1 3.3 4.8
##  [667] 4.3 4.3 4.1 4.6 3.9 4.2 3.8 4.5 5.0 3.9 3.4 6.3 4.3 5.5 4.6 3.6 5.0 2.9
##  [685] 4.6 4.7 5.2 5.1 5.8 3.4 2.4 3.9 5.4 3.4 6.2 2.9 5.6 4.3 5.6 4.6 3.5 5.6
##  [703] 4.3 3.6 5.2 4.0 6.3 4.8 4.1 6.3 5.4 4.1 4.6 5.0 5.0 4.2 4.3 3.6 3.5 5.5
##  [721] 3.9 4.5 5.0 3.8 3.3 3.8 4.6 4.5 4.1 4.6 3.5 3.8 4.6 4.0 4.7 4.3 3.2 4.3
##  [739] 2.8 3.7 4.8 5.5 4.0 3.8 5.0 5.7 4.2 3.6 4.7 3.7 4.5 4.4 5.0 5.0 5.1 4.7
##  [757] 3.9 3.1 4.6 5.5 3.8 6.1 2.9 3.4 3.5 5.1 3.9 4.2 2.5 3.0 5.4 4.8 3.1 5.6
##  [775] 5.5 4.1 4.3 4.8 5.4 4.8 5.8 3.5 3.9 4.8 3.7 4.7 3.5 5.3 5.1 3.9 2.8 4.0
##  [793] 3.5 3.8 3.1 5.3 4.2 4.3 5.6 3.2 3.1 5.2 6.3 3.3 3.2 2.0 5.0 4.3 3.7 3.7
##  [811] 3.3 5.6 5.0 5.4 5.2 4.1 3.1 5.6 5.4 5.3 4.0 4.2 5.5 5.5 5.4 6.8 3.7 5.0
##  [829] 4.2 4.8 5.1 4.1 5.0 4.8 5.2 3.5 4.3 5.5 3.8 5.3 5.2 3.1 4.7 3.4 3.8 3.4
##  [847] 4.3 3.6 4.7 4.4 4.9 5.1 4.1 4.2 4.0 2.5 4.1 4.3 5.7 3.0 4.1 5.3 4.0 3.2
##  [865] 6.5 2.7 4.7 3.6 5.8 3.2 5.0 4.5 4.1 3.8 6.4 5.9 4.1 3.7 5.8 5.7 5.9 4.7
##  [883] 5.8 6.5 3.7 4.0 5.4 4.0 5.1 5.6 5.5 3.7 5.5 5.1 4.3 4.1 3.4 3.6 4.1 4.5
##  [901] 4.0 5.9 5.9 3.9 5.7 3.6 3.8 5.2 3.7 5.0 3.5 5.6 3.1 5.6 4.1 5.4 3.3 1.9
##  [919] 5.3 3.9 5.2 6.2 2.5 4.6 5.4 4.7 2.9 5.1 4.2 5.2 3.4 4.4 5.5 3.8 5.7 2.8
##  [937] 2.5 4.7 3.5 4.6 4.2 4.1 3.4 4.5 5.0 5.5 5.1 6.1 4.3 6.0 3.3 3.9 5.5 5.6
##  [955] 5.7 5.2 4.4 4.2 4.2 6.0 5.1 4.0 4.8 3.3 5.1 5.0 4.8 2.7 3.8 4.7 4.3 3.9
##  [973] 3.7 3.0 6.7 4.4 4.6 3.5 4.5 3.6 6.0 4.9 3.8 4.6 3.8 4.9 4.2 3.3 4.5 4.8
##  [991] 4.6 5.8 3.4 5.0 5.6 3.9 3.7 5.7 5.5 6.3
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.4 5.3 5.2 5.0 4.9 4.9 4.5 4.6
## 
## $jack.boot.se
## [1] 1.02
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
## [1] 0.8155757
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
##   3.129254   5.529070 
##  (1.331605) (2.551988)
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
## [1]  1.1189195 -0.1139517  0.1467729  1.7325899  0.6578741  0.3318624
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
##    [1]  1.210279394  0.702493360  0.107462326  0.132817772 -0.103924382
##    [6]  0.820718103  0.530908924  0.929729118  0.836228521  0.745007623
##   [11]  0.272427460  0.075816567  0.288830996  0.354495326  0.744752280
##   [16]  1.166809902  0.411613412  0.767934674 -0.114121759  0.948110684
##   [21]  1.469812267  0.098844957 -0.508467363  0.849995247  0.737759168
##   [26]  1.014234823  0.904012811  1.305045662  0.832121459  1.514343177
##   [31]  0.259096687  0.120129031  0.701141213  0.719387432  0.858748685
##   [36]  0.143356721  0.792986400  0.408765192  0.399087141  0.384565244
##   [41]  0.353465827  0.118445621  1.197538609  0.581451616 -0.132222962
##   [46]  1.553602377  1.188204323  0.209988422  0.762911269  0.474420364
##   [51]  1.230025229  0.513845018  0.761701738  0.723997309  0.750183035
##   [56]  0.538852570  0.815093671  0.271484182  0.718363839  1.370114820
##   [61]  0.837328665  0.502478298  1.560744787  1.025590001  1.413463407
##   [66]  0.177020097  0.578253422 -0.662204548  0.713021333 -0.238630664
##   [71] -0.785192690  0.839519428  1.305593187 -0.227744487  0.289975432
##   [76] -0.187679206  0.951059368  0.546262639  0.113599632 -0.025004309
##   [81] -0.197821593 -0.134642803  1.405054323 -0.263768185  0.084247383
##   [86]  1.013174607  1.325605537  0.727217963  1.175689086  0.998023768
##   [91]  1.090452476  0.664871074 -0.127267260  0.909955080  0.328672785
##   [96]  0.591627787  1.175583648  0.675578001  1.083164713  1.266345357
##  [101]  1.179184619  1.614940987  0.203454458  0.637545406 -0.267207458
##  [106] -0.354547340 -0.071350583  0.045497292  0.292344295 -0.621585245
##  [111] -0.011412555  0.540466960  0.994137562  0.366528117  0.046343705
##  [116]  0.870873753  0.616821146  0.858049393  0.527796883 -0.240310448
##  [121] -0.063828059  1.150040796  0.332266793  0.951502488  0.742151838
##  [126] -0.147617047  0.204093409  1.363586431 -0.021226090  0.526498696
##  [131]  0.831204321  0.382803970  0.622707841  1.154074742  0.555542901
##  [136]  0.282877555 -0.262891730  0.528073943  0.750077085  0.147232539
##  [141] -0.129890557  0.772864796  1.058885198  0.149458391  1.099412374
##  [146] -0.344690343  0.990961524  0.469798299  1.681253528  1.584768042
##  [151]  1.143514775  0.924610485  0.953519079 -0.155780737  1.508556359
##  [156]  0.982596510  1.350529664  0.860872771  1.420304100  1.852707054
##  [161]  0.272042373  0.483087021  0.376685462 -0.158055004  0.259585614
##  [166] -0.206687396  0.609362016  0.994788956 -0.280528079  0.675982713
##  [171]  1.174629735  0.278758825  0.512688302  0.787742543  1.096610059
##  [176]  1.165285502  0.058090764 -0.177765040  0.623054090  0.886441136
##  [181]  0.950617029  0.077618610  0.609727030  1.153650168 -0.517813458
##  [186]  1.146994733 -0.196802772 -0.304174491  0.359400254  0.642338812
##  [191]  1.231210371 -0.253249774  1.498760466  0.428466414 -0.394526466
##  [196]  0.608975876  0.010343113  0.442111246  1.316008068 -0.440288208
##  [201]  0.272686248  1.213443749  1.236604070  1.553742099  1.542858266
##  [206]  0.318781012  0.762247044  0.368121057  0.509173279  0.437553523
##  [211]  0.101376350  1.520573187  1.008329194  0.749901197  0.925770401
##  [216]  0.477114133 -0.269544490  1.559003695  0.410265284  1.344329828
##  [221]  0.279191152  0.229644592  0.706794234  0.792447000  0.620135546
##  [226]  0.438397703  0.834605193  0.432957636  0.045506929  0.502564397
##  [231]  0.347465099  0.406717233  0.760768209  0.623253375  0.528784802
##  [236]  0.173269542  0.724466489  0.856673242  0.534187122  0.957554433
##  [241]  0.131595115  0.220682873  0.104064910  1.459883484  0.472289431
##  [246]  1.196307378  0.823255238 -0.290698212  0.320219312  0.367158323
##  [251]  0.799539897 -0.065905946  0.057993204  1.323289287  0.888354115
##  [256]  0.823255238  0.420539898  0.904510867  0.458127328  0.614971362
##  [261]  1.087513071  0.108720987  1.667157721  0.566383235  0.736900073
##  [266]  0.761263356  0.728733928 -0.098262037  0.590079153  0.603734141
##  [271]  0.799862766  0.815303670 -0.476573555  0.060243132  0.601293220
##  [276]  0.947063708  0.274941747  1.096416744  1.409320940  0.839856172
##  [281]  0.251581476 -0.075072564 -0.213219782  0.190217662  0.450238324
##  [286]  1.109937287  0.924251267  0.409360417  0.429512968  0.066906872
##  [291]  2.088562023  1.525912450  0.337874913  0.556690895  0.349459912
##  [296]  0.552950310  0.428633585  0.268587172  0.489624501  0.433052531
##  [301]  0.389777487 -0.084661898  0.488787346  1.493761505  0.573885339
##  [306]  0.449693878  0.637129893  1.064447971  0.856112927  1.342940123
##  [311]  2.057304137  0.565998867 -0.103901890  0.110093159  0.152968468
##  [316] -0.159104139  0.737114364 -0.290828552  1.063922968  0.458127328
##  [321]  0.615542762  1.063470031  0.722590947  0.427639245  1.681176544
##  [326]  0.443189028  0.331177676 -0.338903719  1.633350702  0.895199604
##  [331]  0.215684526  1.202555259  0.921368269  0.860264133  1.238547296
##  [336]  0.215116342  0.084944033  0.703518813  0.606131028  0.747716840
##  [341]  1.292045048  0.007070895  0.681441755  0.773844841  1.242559438
##  [346] -0.267387331 -0.189497462  0.506409377  0.976802917 -0.218937300
##  [351] -0.009871468  0.248221721  1.277183859  0.094460202  0.991499486
##  [356]  0.421196296  1.082349169  0.440490402  0.017204446 -0.130725047
##  [361]  0.784466374  0.706799563  0.584789391  0.864016032  1.112555959
##  [366]  1.067648552  0.158090081  0.177020097 -0.024724482  0.069348445
##  [371]  0.870284746  0.302382450  0.571989247  0.144300628  0.105985761
##  [376]  1.008648675  0.664564799  1.710166567  1.098679132  0.546404847
##  [381]  1.145055408  0.086596511  0.450178978  1.280987214  1.537155994
##  [386]  0.960435724  1.555367088 -0.046122384  0.269862660  0.707067254
##  [391] -0.029067796  0.873471225  1.204640033  0.240868930  1.061235023
##  [396]  0.820490553  1.014239475  1.364139582  1.073080782  0.824144799
##  [401]  1.332246180  0.184080191 -0.201390290  0.850401077  0.501954161
##  [406]  0.552830183  0.766246200  0.823997739  1.164066294  0.006637052
##  [411]  0.825458960  0.835759863  0.219668664  1.466015506  0.582853190
##  [416]  0.514951952  1.330681860 -0.946376700  0.700116530  0.681570618
##  [421]  1.464497221  0.997813421  0.394030296  1.160070444  1.379440238
##  [426]  1.028828964 -0.517651416  1.191971710  1.314808420  0.548508224
##  [431] -0.166067901  0.227626735  0.957187619  0.501209199 -0.125288219
##  [436]  1.178122150  0.669298864 -0.250084454  0.213284988  1.112774272
##  [441]  0.260772818  0.403760554  0.470339093 -0.243125659 -0.464938166
##  [446]  1.007061872 -0.691909279  0.901070106  0.373003484  1.498760466
##  [451]  0.243092422 -0.141257492  0.571989247  0.714758552  0.088924629
##  [456]  1.382813010  1.097597455 -0.119230928  0.420367531  1.001385378
##  [461]  1.011421128  1.498908587  1.223398994  0.184322238  0.583474620
##  [466]  0.220626063  0.466111386  0.832091640  1.135583937  0.359619442
##  [471]  0.488440827 -0.749378741  1.084757387  0.333934935  0.008171981
##  [476]  1.546246441  0.020349434  0.332620464  0.281503380  0.162443694
##  [481]  0.013743397  0.345298250 -0.008414046  0.699445861  1.296857558
##  [486]  0.661771095  1.332420788  0.270759937  0.791791226  0.334425460
##  [491]  1.225404225  0.357359606  1.326162109  0.704092169  0.547210266
##  [496]  0.394748163  0.548636894  1.085528421  1.086965656  0.326919878
##  [501] -0.207985434  0.110675820  0.579972887  0.644352441 -0.431497522
##  [506]  1.684536335  0.085830490 -0.168679649  0.947775179  0.112998788
##  [511]  1.626430577  0.509662828  0.536050604  0.562109774  0.798268472
##  [516] -0.307661713  0.891307223  1.160092620  0.628498363  0.731821394
##  [521]  0.820527630  0.505855429  0.807327319 -0.121473705  0.747820348
##  [526]  1.710874141  0.996411239  0.476773924  0.970376260  0.719703924
##  [531] -0.034098443 -0.240422113  0.242167336  0.746301037  1.533272757
##  [536]  0.855703872  0.367280138  0.461876049  0.382808679 -0.101647869
##  [541]  0.379031494  1.498835985  0.428316550  1.255055587  1.102543939
##  [546] -0.060928032  1.248793786  0.085902986  0.218236364  0.745670455
##  [551]  0.668827966  1.159065177  0.691254335  0.968869174  1.334289867
##  [556]  0.840541084  0.683169582  0.446617052  0.840749359  0.574330671
##  [561]  0.215058407  0.706066195  1.574013004  0.943005220  0.068190075
##  [566] -0.196830406  1.596606122  0.715801500 -0.117232381  0.717594914
##  [571]  0.125105710  0.958439275  0.459010287  0.270329599  0.896348927
##  [576]  0.138299968  0.393758394  0.803492353  0.428471933 -0.127222757
##  [581] -0.028649092 -0.152733113  0.927659891  1.344329828  1.131906097
##  [586]  0.956014093  1.257689761  0.267109886  0.363629678  0.810999503
##  [591]  0.288480180  0.785380226  0.428633585  0.833308182  0.974316993
##  [596]  0.943414519  0.451415939  0.178670704  0.585828520  0.143926215
##  [601]  0.839938818  0.822137335  0.814924573  0.393084582  1.217463339
##  [606]  0.761263356  0.722543246  0.289676142  0.760362679  1.213443749
##  [611]  0.007577413  0.292656563  0.632600524  0.657672153  0.803333421
##  [616]  0.817674764  1.057276852  0.514753243  0.141290660 -0.043093684
##  [621]  0.273051317  1.476923289  0.771336928  0.746450906  0.121264425
##  [626] -0.151851210 -0.463138809  1.384232191  1.246151356 -0.031759551
##  [631]  0.965886209  1.644609446  2.093910251  0.087483772  0.397551685
##  [636]  0.679192998  2.192023992  0.622191878  0.213169000  0.930300060
##  [641]  1.505352831  0.342277911  0.998310167 -0.285559693  1.033558322
##  [646]  0.749904039  1.252219417  0.438012436  1.577907544  0.407708901
##  [651]  0.252546378 -0.238798704 -0.255746015  0.374625606  0.201031828
##  [656]  0.507401438  1.742816726  1.075615459 -0.042305860  0.962554299
##  [661]  1.006150342  0.948987780  0.588390604  1.208660344 -0.055172691
##  [666]  0.943714810 -0.472245846  0.875210150  0.203790618  0.037867338
##  [671]  0.785277893  0.739825899  0.068016628  0.893705811  0.029101518
##  [676] -0.835805477  1.316436583  0.321728316  0.970128419  0.804646200
##  [681] -0.557088781  0.767016066  0.303430215 -0.255337350  0.707349738
##  [686]  0.104169147  0.476434335  0.899876628  0.793728274  0.097863043
##  [691]  0.458602754  0.964045590  0.714324373 -0.357375055  0.653446195
##  [696]  0.429344775 -0.264508741  1.136638287  1.230461335  0.629930696
##  [701] -0.472771718 -0.470837530  0.643323225  0.597951515  0.671139671
##  [706]  1.157725984  0.926143403  0.701883331 -0.240414995  0.428921282
##  [711]  0.851251824  0.961658849  0.663636113  1.379387058  0.262776362
##  [716]  0.193146141  0.679696651 -0.694484751 -0.052953203  0.294510158
##  [721] -0.452524495 -0.312613511 -0.127827770  0.827577482  0.992222592
##  [726]  0.059464554 -0.244775922 -0.084393747  0.477916270 -0.314739082
##  [731]  0.383819880  0.985917336  1.399629118  0.252623606  1.339542235
##  [736]  0.629987862  0.626583594  1.008237690  0.800116992  0.264584491
##  [741]  0.765779256  0.754929311  0.719305233  0.590873526  0.587028253
##  [746]  1.230461335  1.247096921 -0.567448575  0.450440172  0.830070369
##  [751] -0.455110011  1.257757703  1.537110885  0.625257496  0.295347786
##  [756]  0.459330908  0.220015547  0.466618228 -1.398798159  0.975273158
##  [761]  1.068438684  0.765586768  0.869086807  0.377916751  0.165612566
##  [766] -0.162941154  0.996681698  0.318616582  1.174629735 -0.109998435
##  [771]  0.306937495  0.633047966  1.340721341  0.917190953  1.006507435
##  [776] -0.267253297  0.114996458 -0.049042366 -0.239294690  0.366025390
##  [781]  1.330932357  0.169537191  0.636543049 -0.450365919  0.457390909
##  [786]  1.112209063  1.181452329  0.422593603  0.362998246  1.281650117
##  [791]  1.138859274  0.146039082  0.370150234  0.664358310  0.707477812
##  [796]  1.228538067  0.372900964  0.703346969  0.604411990  0.021511781
##  [801]  0.584203816  1.217388226  1.165609677  0.032146249  0.063156612
##  [806]  1.845724458 -0.534814989  0.575196874 -0.200920536  0.410019218
##  [811]  0.153151643  0.926303076  0.793528265  1.407674839 -0.218680549
##  [816]  0.345506359 -0.671522812  0.578576679  1.339542235  0.322957784
##  [821]  1.021566987  0.799625606  0.877820097  1.195213095  1.347317830
##  [826]  2.044110754  1.037718578  0.971809248  0.658199260  1.498831461
##  [831]  0.600941410  0.120147240  0.585138753  0.449583331  0.075105790
##  [836]  0.156954403  0.874191409  0.428633585  0.906118035  0.779997904
##  [841]  1.637214118  0.570147511  0.929500568  1.019654419  0.522467387
##  [846]  0.839178651  1.120562027  0.277665192  0.738551711  0.428028260
##  [851]  0.584882689  0.878932097  0.877310854  1.985380600  1.318180860
##  [856]  0.331425745  0.900908068  0.692411812  0.686200749  0.250001762
##  [861]  0.189286629  0.316495946 -0.090472287  2.285415218  0.825362979
##  [866]  0.699512789  0.948987780  1.160973310  0.251129339  0.823997739
##  [871]  1.873958625  1.304453712  0.936290525  0.639217851  0.419122491
##  [876]  0.550509622  0.491107034  0.812506551 -0.440512320  1.571615295
##  [881]  0.012227286  0.193392782  0.445381312  0.215705364  0.160718186
##  [886]  0.778121352 -0.436383234  1.177260987  1.182598308  1.878767070
##  [891]  0.281959853  0.600399101  0.574330671  0.742357796  1.368344115
##  [896]  0.625241143  1.076827031  0.100368045 -0.390301467  1.853220812
##  [901]  1.035737443  0.816998813 -1.060179457  0.191557043  0.756422897
##  [906]  0.718321761  0.968929389  0.220127906  0.886457590  1.129083000
##  [911]  0.384342144 -0.670762606  0.033624489  0.183894031 -0.226812825
##  [916]  0.215185592  1.464608409  0.431745994  0.131637360  0.368763100
##  [921]  0.362511518  0.488964851  0.721854746  0.052670908  0.355635232
##  [926] -0.574258935 -0.508531243  1.530211009  1.236840578  1.171957002
##  [931]  0.820720968  0.864812991  0.253833411  0.809241073  1.024215081
##  [936]  0.710635523  0.813081252  1.700795255  0.230296809  0.795939240
##  [941]  0.498715992  1.473797849 -0.336861390  0.489569855  1.301984945
##  [946]  0.675308839  0.154948176  0.907898418 -0.099473318  1.013338390
##  [951]  0.692076930 -1.180828438  0.884891394  0.590342427  1.503730652
##  [956]  2.173926868  0.994429463  1.614559357  0.653754061  0.139855481
##  [961]  1.559385063  0.646397490  0.831204321  1.268663321  1.699557186
##  [966]  1.877831830  1.086474251  0.197328528  0.178923009  0.735008823
##  [971]  0.580733284  0.606671445  0.884891394  1.723213818  0.580555415
##  [976]  1.518314493  1.776139554  0.534715475  0.464148975  0.433693134
##  [981] -0.361252867 -0.015076354  0.198714554  0.263606878  1.037283333
##  [986]  0.917378168  1.097597455  0.469412160  0.452449910  0.567683299
##  [991]  0.286110053  0.781272160  0.081641409  0.711073173  1.253826120
##  [996]  0.338048814 -0.011656563  0.111888218  0.132984374  0.080936164
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
##   0.56596354   0.32012298 
##  (0.10123177) (0.07157834)
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
## [1]  0.9658284  0.6613413  0.9154821 -0.4076636 -0.1342364 -0.5501379
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
## [1] -0.0243
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8865018
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
## t1*      4.5 -0.03143143   0.9008196
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 4 5 6 8 9 
## 2 1 1 1 1 1 1 1 1
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
## [1] -0.0276
```

```r
se.boot
```

```
## [1] 0.9264538
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

