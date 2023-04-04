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
## 0 1 3 4 5 7 8 9 
## 1 1 1 2 1 1 1 2
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
## [1] -0.019
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
## [1] 2.685274
```

```r
UL.boot
```

```
## [1] 6.276726
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.6   6.3
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
##    [1] 3.6 4.6 6.4 5.6 5.3 3.6 4.6 3.7 5.3 3.9 5.5 4.7 3.6 5.3 4.0 3.1 4.4 4.8
##   [19] 4.3 4.7 5.2 3.8 4.9 3.4 4.0 4.6 4.9 3.6 3.0 4.0 6.4 5.1 5.5 4.9 3.7 4.6
##   [37] 4.1 5.3 4.2 4.9 4.0 3.9 3.5 4.2 4.5 4.6 2.7 4.0 4.9 3.0 4.5 5.7 5.3 6.3
##   [55] 3.2 3.5 5.5 5.7 4.9 4.1 4.1 3.7 6.4 3.9 5.0 6.2 4.3 3.5 5.2 3.2 3.3 4.4
##   [73] 4.1 4.2 4.0 5.3 5.0 4.5 5.4 3.9 6.0 4.0 6.1 4.9 5.4 5.5 4.1 4.2 2.8 3.7
##   [91] 5.2 3.6 3.5 5.5 6.3 4.6 4.7 4.3 4.3 5.7 4.0 3.5 4.2 5.3 3.8 4.3 4.4 5.3
##  [109] 4.7 6.5 4.1 5.3 4.1 4.0 4.8 4.2 5.3 4.1 5.7 4.5 4.5 5.1 3.4 5.6 5.9 6.0
##  [127] 4.6 5.1 4.7 3.8 4.8 6.0 4.6 5.7 4.5 3.5 4.3 6.0 6.5 5.8 6.2 4.8 4.9 5.1
##  [145] 5.8 3.0 2.7 4.0 3.8 3.3 4.1 4.3 3.7 5.3 3.4 4.1 4.9 3.9 5.6 4.4 4.1 5.2
##  [163] 5.1 3.5 3.9 5.6 3.1 4.3 3.0 4.7 4.3 5.0 4.4 4.0 5.3 3.8 4.5 4.8 5.8 5.3
##  [181] 4.1 4.4 4.4 5.3 3.6 4.9 3.8 5.8 4.9 4.8 4.4 3.2 2.8 4.5 3.2 4.9 4.2 4.9
##  [199] 4.2 7.0 6.7 4.5 3.7 3.5 4.1 4.4 5.0 5.3 7.0 6.4 5.5 6.0 6.0 4.5 5.3 3.2
##  [217] 3.3 3.7 4.5 4.1 4.3 5.1 6.5 5.3 4.5 4.8 5.9 7.0 5.3 4.1 4.3 4.6 4.2 3.9
##  [235] 6.1 4.2 4.1 2.6 4.0 5.1 3.3 5.1 5.4 4.6 3.2 6.3 4.3 3.0 5.1 2.5 4.5 4.6
##  [253] 4.1 5.5 3.8 3.8 5.1 2.8 4.6 2.3 4.7 3.6 4.0 5.1 5.5 4.0 4.6 5.5 6.3 4.1
##  [271] 3.6 3.3 4.9 4.3 4.1 5.3 6.4 4.6 3.8 4.5 5.8 2.2 4.2 4.5 4.0 4.5 5.1 3.8
##  [289] 3.7 3.2 3.2 5.3 4.3 3.2 5.0 5.5 4.9 4.4 4.5 2.7 3.8 3.1 4.4 3.4 2.7 5.6
##  [307] 5.9 2.8 5.8 5.7 4.3 4.1 4.3 3.5 5.4 5.1 4.6 4.0 3.6 4.0 4.7 4.8 6.4 4.3
##  [325] 5.7 4.9 4.5 3.7 4.5 4.3 3.9 4.1 4.4 3.2 5.0 4.8 4.2 3.6 3.7 3.9 2.1 3.6
##  [343] 3.1 4.7 3.9 3.0 4.6 5.2 5.7 4.6 3.3 3.7 5.4 4.6 3.3 3.1 3.8 4.1 4.3 4.0
##  [361] 4.1 4.2 4.8 4.9 4.6 4.0 6.3 5.1 4.9 4.0 5.7 2.2 4.1 4.2 6.5 4.1 5.5 3.7
##  [379] 2.8 4.2 4.9 4.8 4.3 5.3 3.5 2.6 3.7 4.8 3.6 2.8 4.7 4.7 6.1 4.3 4.9 4.7
##  [397] 3.8 4.9 3.4 3.6 5.1 5.4 3.4 5.2 4.9 4.3 4.0 2.4 3.8 3.8 3.8 4.4 3.8 4.5
##  [415] 3.6 5.1 4.7 5.1 4.7 4.6 4.7 4.3 3.6 4.2 3.5 3.6 4.6 3.9 3.8 2.6 4.5 5.0
##  [433] 4.9 4.6 4.4 6.3 5.6 5.1 4.4 3.5 3.7 3.7 5.5 4.6 5.6 3.6 6.2 2.6 6.0 6.1
##  [451] 3.5 4.3 4.0 4.4 5.2 3.8 4.3 2.6 5.3 4.8 5.4 3.1 4.7 2.7 5.6 5.6 5.5 4.9
##  [469] 2.9 3.1 5.1 4.7 5.1 4.6 5.0 4.9 5.9 4.0 4.8 4.9 3.5 5.3 4.9 4.9 4.8 4.0
##  [487] 4.7 4.8 5.4 4.4 2.9 6.1 4.7 5.3 5.5 5.2 5.6 3.5 5.5 6.2 5.3 3.5 3.9 5.7
##  [505] 3.8 4.1 4.0 4.7 4.9 3.3 4.6 4.8 4.8 3.4 5.8 4.8 5.8 5.9 4.3 5.6 4.8 4.1
##  [523] 5.1 5.1 4.8 3.0 3.5 4.7 4.7 4.4 3.8 4.0 2.6 4.2 5.4 4.2 6.4 3.3 6.0 4.7
##  [541] 4.5 6.2 4.8 4.7 4.8 3.9 5.2 5.6 4.0 3.3 2.9 5.1 4.9 4.9 6.8 4.2 3.2 4.5
##  [559] 5.5 5.5 4.1 3.4 4.0 4.2 6.3 3.9 3.7 3.9 5.1 4.0 5.4 5.4 5.4 6.0 5.0 4.4
##  [577] 2.9 3.7 4.2 3.7 3.9 5.2 3.6 5.4 3.3 5.1 4.6 4.0 4.6 5.1 3.8 3.9 3.9 3.8
##  [595] 2.5 4.5 5.1 5.6 5.3 4.1 3.9 5.5 5.4 4.9 5.2 4.8 5.0 4.4 4.3 5.1 4.9 3.2
##  [613] 4.7 4.8 3.4 4.6 3.7 6.0 4.4 3.1 5.6 4.4 4.0 4.7 3.2 3.7 5.8 3.9 4.6 5.5
##  [631] 4.8 4.3 4.2 5.0 4.5 3.5 5.3 4.7 4.3 4.0 4.4 3.6 6.0 3.5 4.9 4.8 5.0 5.3
##  [649] 3.7 3.9 6.2 6.1 4.3 3.3 5.3 5.0 3.9 4.0 6.7 4.4 4.5 3.8 4.9 3.7 4.9 5.3
##  [667] 3.9 5.6 4.5 5.6 4.5 2.5 4.1 4.2 4.8 3.7 5.8 4.3 4.8 6.3 3.2 3.8 6.2 5.0
##  [685] 5.5 3.5 4.0 5.7 2.9 5.1 3.6 3.2 3.1 5.2 4.7 4.2 4.0 4.9 4.1 3.3 5.2 3.2
##  [703] 4.9 2.8 5.2 3.3 4.5 3.9 4.6 3.7 6.1 4.2 5.8 5.7 3.7 5.6 4.8 4.0 4.2 5.0
##  [721] 6.3 3.6 3.3 4.1 3.3 4.2 5.7 4.7 3.2 3.6 3.2 3.8 3.8 5.6 2.9 5.3 4.4 3.4
##  [739] 5.4 3.8 5.4 4.4 3.8 4.4 4.3 5.1 4.0 3.2 5.7 4.0 5.3 5.0 4.1 5.3 4.9 4.9
##  [757] 6.3 6.3 5.7 5.0 2.3 3.7 3.3 6.2 6.4 4.0 4.5 3.6 4.8 5.3 5.8 3.0 4.7 6.4
##  [775] 5.0 3.5 3.0 5.4 5.8 5.6 3.3 3.9 2.9 3.1 6.9 4.5 6.1 4.3 5.2 4.4 5.8 5.1
##  [793] 3.5 4.6 4.3 4.7 4.8 5.8 3.4 5.3 5.7 6.3 5.0 4.6 4.3 5.3 4.7 4.1 5.1 4.3
##  [811] 5.8 5.1 4.6 2.9 4.5 3.8 4.3 4.1 3.8 3.9 3.7 4.2 4.9 3.3 4.6 3.9 5.8 4.3
##  [829] 5.6 3.1 4.2 5.0 4.3 3.1 4.2 4.4 5.8 5.3 5.6 3.0 3.9 4.2 6.7 4.0 4.7 5.1
##  [847] 3.4 4.4 5.1 6.2 4.0 5.1 3.6 4.4 4.5 3.7 3.7 4.9 3.8 2.7 5.8 3.4 3.6 7.1
##  [865] 4.1 4.2 3.3 4.9 4.0 5.5 4.2 4.7 3.6 3.9 3.0 4.7 5.8 4.7 4.7 4.2 4.1 5.0
##  [883] 5.9 4.7 3.6 4.9 3.2 4.5 6.0 4.9 5.4 5.1 4.6 3.4 5.5 4.7 3.7 3.9 6.1 3.5
##  [901] 5.3 5.9 4.2 5.4 4.0 5.5 5.3 5.0 4.4 4.2 4.2 4.3 5.9 5.2 4.5 5.1 2.3 5.0
##  [919] 4.4 4.5 4.1 5.4 5.8 3.4 4.9 5.3 4.7 4.3 3.7 5.0 4.7 6.4 2.9 6.7 4.4 4.1
##  [937] 5.0 5.2 3.9 5.3 6.0 2.8 3.0 4.1 4.7 4.0 4.7 2.1 5.1 3.7 4.1 5.2 2.5 5.8
##  [955] 5.5 4.1 4.6 4.6 4.6 5.3 4.1 3.6 4.1 4.6 4.5 5.3 4.6 4.1 4.4 5.3 5.6 3.5
##  [973] 4.6 3.8 6.5 4.4 2.9 5.4 5.0 4.2 5.0 4.0 6.6 4.6 3.0 5.2 2.6 3.0 4.6 5.3
##  [991] 3.5 5.1 3.8 4.7 4.4 3.1 6.5 4.1 4.6 5.1
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
## 2.7975 6.4000
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
##    [1] 4.8 4.6 3.8 6.5 5.5 6.0 4.1 4.6 3.4 5.3 5.7 3.4 4.8 3.1 4.5 5.1 6.2 4.0
##   [19] 2.4 4.7 5.5 4.2 5.0 4.8 5.0 4.0 4.6 5.3 3.9 4.0 4.3 5.0 4.1 5.6 4.8 2.9
##   [37] 3.9 4.8 4.1 4.2 3.8 5.9 3.5 3.7 6.0 6.2 5.1 5.3 4.1 4.1 5.5 4.4 5.5 4.7
##   [55] 3.6 3.2 6.3 3.6 5.1 4.2 3.3 4.8 3.3 3.7 4.3 4.6 5.0 4.5 5.2 5.0 4.2 3.6
##   [73] 4.0 5.8 4.2 3.2 4.7 3.7 3.6 3.7 1.9 4.3 6.2 2.2 3.6 3.7 2.7 4.9 5.4 3.1
##   [91] 5.0 4.8 5.2 4.8 4.1 3.7 5.1 4.0 3.3 3.8 4.7 5.8 4.0 4.4 4.6 5.2 3.7 3.3
##  [109] 3.2 3.6 3.9 5.9 6.3 4.5 5.0 4.3 6.1 4.9 4.8 4.4 3.7 5.1 5.3 5.1 3.1 3.9
##  [127] 4.2 4.6 3.0 5.5 5.0 4.7 4.9 5.6 4.5 4.6 3.3 4.5 3.7 5.0 4.8 4.1 3.5 3.9
##  [145] 4.6 3.4 3.6 2.8 3.4 2.9 3.5 3.7 3.2 3.7 4.2 3.5 4.5 3.8 4.2 5.6 5.5 6.0
##  [163] 3.1 5.7 4.5 4.3 5.6 4.2 5.6 3.4 5.4 4.4 4.4 6.2 3.5 3.7 4.9 2.8 5.1 3.8
##  [181] 4.3 5.0 5.4 6.2 3.0 5.1 4.0 4.8 5.2 4.5 5.5 3.7 5.2 5.3 5.6 4.8 5.0 6.0
##  [199] 5.3 1.9 3.5 4.5 4.5 3.5 5.1 4.3 4.3 4.7 4.0 5.5 5.0 5.2 5.0 3.1 5.1 4.0
##  [217] 4.8 4.0 4.8 4.9 3.0 4.4 4.8 3.6 5.0 3.1 5.8 5.5 4.6 6.1 5.6 3.8 4.0 2.7
##  [235] 3.3 5.3 3.3 5.6 6.2 4.9 4.3 3.6 4.7 4.9 5.0 6.2 3.1 3.5 3.9 5.1 3.6 4.9
##  [253] 3.1 3.5 4.5 5.6 4.8 5.6 4.2 5.6 4.3 5.1 6.5 4.3 4.2 4.9 5.8 4.6 3.4 2.2
##  [271] 4.2 4.2 5.1 3.8 2.9 3.4 4.9 4.4 4.0 4.6 3.8 4.6 3.8 2.9 4.8 5.4 4.1 4.7
##  [289] 3.7 4.7 4.7 3.9 3.6 3.5 5.2 5.1 4.4 4.6 3.8 5.2 5.8 5.2 4.4 5.5 6.4 5.0
##  [307] 3.7 6.7 3.4 3.1 5.5 4.5 5.3 6.9 4.7 4.9 4.5 3.7 3.6 5.3 5.3 6.0 3.7 4.7
##  [325] 4.1 4.3 4.9 5.6 5.4 3.6 5.7 5.8 4.4 4.0 7.0 3.8 4.9 6.1 5.7 4.8 5.7 3.9
##  [343] 4.5 3.7 5.0 4.4 5.5 4.8 4.4 5.9 4.6 4.3 3.1 4.6 5.3 4.8 4.2 4.1 6.7 2.8
##  [361] 4.8 5.4 3.2 4.5 5.2 4.7 4.9 5.1 4.9 4.5 3.6 4.2 6.1 4.5 5.7 4.4 4.8 5.7
##  [379] 5.0 5.0 3.9 5.3 4.7 6.2 4.4 3.1 3.5 4.5 2.7 3.9 4.3 3.8 4.5 3.2 4.8 3.7
##  [397] 3.3 5.2 5.0 4.2 3.7 5.2 5.1 5.0 3.9 4.9 5.8 3.7 6.3 5.4 5.7 3.3 3.7 4.2
##  [415] 5.2 2.9 4.5 4.2 4.6 3.3 4.9 5.4 6.7 5.5 4.4 3.7 5.2 5.7 3.3 4.8 4.6 4.6
##  [433] 3.1 2.7 5.1 3.7 3.9 4.2 5.3 4.2 5.2 3.7 4.2 6.5 6.0 4.5 3.3 4.8 3.5 4.9
##  [451] 5.1 3.2 5.2 4.0 4.5 4.3 4.0 4.1 6.1 3.7 2.9 4.1 5.4 5.2 4.9 4.7 4.7 4.1
##  [469] 4.6 4.6 4.0 4.8 5.1 4.1 5.8 5.1 3.9 4.9 3.1 4.1 4.4 6.0 3.7 5.1 6.8 4.5
##  [487] 5.9 4.1 4.4 4.1 5.0 4.2 5.3 5.8 5.1 4.3 4.8 4.2 6.7 4.8 5.7 2.8 4.2 4.9
##  [505] 3.7 4.9 6.0 2.8 4.2 4.6 3.6 4.6 6.4 4.2 5.9 6.8 4.2 5.5 4.2 4.1 3.0 4.3
##  [523] 4.2 4.9 3.0 4.1 3.2 5.0 5.0 3.9 4.3 5.3 5.3 6.3 3.8 4.3 4.8 2.9 4.1 4.2
##  [541] 3.7 5.3 4.4 4.9 4.3 4.9 5.5 4.4 4.8 6.1 3.9 5.8 4.3 4.4 5.1 4.5 4.2 5.4
##  [559] 3.6 4.2 4.5 3.7 4.3 5.8 3.7 3.2 5.1 5.5 4.2 4.9 5.1 2.6 5.8 4.8 3.9 4.9
##  [577] 4.2 3.6 4.5 3.3 6.3 4.7 4.1 4.8 4.1 5.0 4.2 3.6 4.1 4.1 4.4 5.1 4.2 2.9
##  [595] 3.9 3.8 4.4 5.1 4.5 3.7 2.7 5.6 3.7 5.4 4.8 3.9 4.2 4.9 2.7 4.1 5.2 4.7
##  [613] 6.6 3.6 4.5 3.6 2.8 5.9 4.8 4.9 3.5 2.8 3.6 5.5 3.3 4.8 4.8 5.0 3.8 3.1
##  [631] 5.6 6.9 4.0 5.2 4.9 4.3 3.9 5.7 4.8 4.2 2.6 3.4 3.9 4.9 4.7 5.8 3.3 5.7
##  [649] 4.1 5.1 5.0 2.6 4.7 3.9 5.9 5.4 3.0 4.3 3.5 5.8 4.7 4.0 5.3 6.3 3.5 4.2
##  [667] 4.5 4.3 5.3 5.2 3.2 3.6 5.5 4.2 4.8 4.5 4.6 2.4 3.6 3.4 2.9 4.1 4.2 4.4
##  [685] 6.2 4.3 4.4 3.8 4.6 3.0 4.8 5.3 6.3 4.0 4.7 6.0 4.6 4.1 5.0 5.2 3.6 5.3
##  [703] 5.1 5.7 2.8 4.5 3.5 5.3 4.1 5.8 4.1 3.7 5.7 3.7 4.0 4.4 3.8 5.2 5.9 4.7
##  [721] 5.6 3.8 3.8 3.8 3.2 4.6 4.3 4.5 5.0 4.7 5.6 4.2 3.5 4.7 2.8 3.8 3.3 5.2
##  [739] 4.7 5.4 4.6 4.9 5.1 5.5 3.5 3.3 4.2 4.6 5.2 5.4 4.1 3.5 4.0 6.3 3.9 4.4
##  [757] 3.3 3.7 3.9 5.0 4.8 4.7 5.7 4.5 5.7 4.2 3.3 4.0 4.4 4.8 4.2 4.6 4.7 4.1
##  [775] 4.2 5.1 4.6 5.0 6.3 3.1 4.4 5.5 5.0 3.9 4.8 4.5 5.7 2.9 4.7 5.2 5.2 5.4
##  [793] 6.3 3.8 4.2 3.7 5.6 5.7 2.6 4.6 4.3 4.6 4.3 3.3 5.7 4.5 3.8 5.7 4.1 5.0
##  [811] 4.8 5.0 4.8 3.9 6.2 4.0 4.3 5.1 5.0 3.5 5.3 4.0 5.0 5.2 5.4 5.2 4.2 5.2
##  [829] 5.1 4.3 2.7 4.2 4.8 3.4 4.2 5.2 4.3 4.8 4.6 5.5 5.2 5.5 6.2 5.7 4.8 4.4
##  [847] 6.3 4.7 5.5 5.8 4.6 4.4 3.4 4.2 5.5 5.2 5.0 4.8 5.1 5.8 4.3 5.5 4.5 3.1
##  [865] 4.8 4.4 5.1 4.7 4.3 5.1 5.8 5.2 3.1 3.0 4.7 5.2 3.3 4.5 3.9 4.3 3.7 3.6
##  [883] 3.5 5.9 5.3 3.7 4.7 4.0 3.1 4.7 5.1 4.5 4.8 6.3 3.9 4.6 4.4 4.8 3.9 4.6
##  [901] 4.4 5.3 6.6 4.0 4.0 4.1 6.9 4.6 5.1 4.1 4.4 5.0 4.5 4.5 4.1 5.1 2.3 4.1
##  [919] 6.5 4.6 4.7 3.4 6.7 3.6 4.7 3.6 4.0 3.8 3.4 2.7 4.2 4.8 5.4 5.1 4.2 5.8
##  [937] 3.7 3.9 3.9 5.4 3.3 4.2 5.1 4.3 2.8 3.7 6.0 4.8 4.8 3.6 4.8 4.9 4.3 5.6
##  [955] 3.0 3.1 5.5 5.0 4.6 6.0 5.4 3.3 4.6 3.7 4.8 2.7 4.2 3.9 5.5 5.5 3.6 4.6
##  [973] 3.5 5.2 4.0 4.4 2.9 5.3 5.0 4.5 5.0 3.6 3.6 5.1 4.3 3.7 5.1 4.4 4.5 4.4
##  [991] 5.2 4.1 6.0 4.1 4.2 5.2 2.5 4.4 3.3 3.5
## 
## $func.thetastar
## [1] 0.0167
## 
## $jack.boot.val
##  [1]  0.49424242  0.41467391  0.29759358  0.21651652  0.04758842 -0.05746479
##  [7] -0.14441341 -0.20029412 -0.40028169 -0.47257143
## 
## $jack.boot.se
## [1] 0.9477032
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
##    [1] 5.6 5.0 5.4 4.9 5.0 5.0 5.6 4.4 4.2 4.5 4.7 4.1 6.7 4.5 6.1 5.7 4.9 5.0
##   [19] 4.0 3.7 4.1 5.8 5.1 5.5 4.1 6.4 5.0 4.3 5.6 6.2 5.8 3.1 4.2 4.2 5.5 4.6
##   [37] 4.6 3.5 3.6 4.6 4.4 3.6 3.2 4.8 4.9 4.5 3.9 4.6 4.7 5.8 4.3 5.8 5.4 5.4
##   [55] 7.1 6.4 5.0 3.6 3.7 4.6 3.2 4.7 5.4 3.6 4.9 3.9 5.0 5.2 3.5 5.7 4.5 3.8
##   [73] 3.9 3.7 3.7 5.6 4.5 4.0 3.7 3.4 4.8 5.2 3.3 5.6 4.1 4.3 4.5 3.4 4.9 4.0
##   [91] 4.7 5.1 5.0 4.5 3.1 5.3 5.0 3.4 4.2 3.7 6.3 4.8 5.1 4.4 5.1 2.4 2.4 5.4
##  [109] 4.7 2.6 3.4 5.7 5.8 3.7 6.4 3.5 4.0 4.3 4.0 4.9 4.1 2.8 5.3 5.7 4.4 5.3
##  [127] 6.1 4.9 5.4 3.7 4.3 4.7 2.9 1.6 4.7 5.7 3.2 5.6 5.6 4.4 5.0 4.4 4.6 3.5
##  [145] 4.8 3.5 5.5 5.5 3.7 4.9 4.7 4.8 4.1 4.2 3.9 5.9 4.0 4.5 3.9 3.5 5.8 4.9
##  [163] 4.5 5.8 5.0 4.7 3.5 5.6 4.3 4.3 4.9 4.6 3.5 6.1 3.8 4.5 4.2 4.4 5.6 4.5
##  [181] 5.4 3.5 2.7 4.2 4.9 4.8 5.0 5.5 5.0 4.2 3.1 4.2 3.0 4.3 4.4 4.8 4.9 5.5
##  [199] 3.9 4.4 5.8 4.7 4.5 1.9 5.0 4.6 5.3 3.7 5.2 5.2 5.0 5.4 5.2 5.0 3.2 5.0
##  [217] 3.6 3.8 4.0 5.3 5.2 5.5 4.7 5.0 4.1 2.9 4.0 5.4 4.3 3.9 4.8 3.6 4.6 4.6
##  [235] 4.4 6.2 5.1 4.4 5.0 4.0 3.5 3.2 3.3 2.6 4.4 3.0 4.5 6.3 3.3 5.3 3.8 5.2
##  [253] 5.7 3.9 4.5 6.0 4.2 4.4 4.3 4.9 4.7 5.0 4.9 2.4 2.5 4.5 4.1 5.7 3.0 5.0
##  [271] 4.4 5.7 4.5 5.0 2.3 4.4 4.8 6.0 3.0 5.7 4.1 5.6 3.0 3.8 6.4 5.1 4.3 5.2
##  [289] 4.8 3.9 4.3 5.2 6.3 4.7 3.4 4.2 5.5 4.9 3.5 6.0 4.8 4.7 4.2 2.8 6.0 4.1
##  [307] 2.8 4.1 4.6 4.3 4.7 5.4 6.1 4.2 4.5 4.1 5.1 4.9 4.7 5.0 5.7 3.8 4.9 4.3
##  [325] 7.0 5.3 3.8 5.2 4.2 4.2 3.8 4.9 3.8 5.3 4.1 4.8 3.3 3.9 5.0 4.1 5.7 4.7
##  [343] 4.9 4.4 4.0 4.7 5.8 4.7 3.7 5.7 4.7 4.6 5.5 3.7 3.7 4.1 3.0 4.2 4.6 6.0
##  [361] 4.3 4.7 5.1 4.4 4.8 3.7 4.0 5.2 4.0 4.6 4.9 4.8 5.8 4.3 4.2 5.7 3.0 5.5
##  [379] 6.0 3.7 5.8 4.3 3.8 6.4 3.7 4.9 4.4 5.8 4.5 3.8 5.3 5.3 5.5 3.3 3.6 3.0
##  [397] 3.9 6.0 2.7 4.4 4.4 4.1 4.5 2.5 4.7 4.0 4.8 3.1 3.5 3.8 5.3 3.7 4.5 5.1
##  [415] 5.5 4.3 4.6 4.9 5.8 5.5 4.8 3.7 3.1 4.5 3.0 4.1 5.4 3.2 4.6 3.8 4.8 4.8
##  [433] 4.2 3.9 5.6 5.3 4.9 4.1 5.2 4.7 4.8 5.9 2.7 3.6 3.9 4.1 4.4 4.7 4.5 4.8
##  [451] 4.2 4.1 4.0 4.7 3.4 4.9 5.1 4.2 4.4 3.9 4.3 4.9 4.8 3.4 5.5 5.0 3.1 5.6
##  [469] 3.4 5.0 3.9 4.2 4.0 4.7 5.6 4.2 4.4 4.6 5.2 4.2 5.1 4.2 3.0 3.7 4.3 5.1
##  [487] 4.9 5.5 2.9 4.6 5.9 3.7 2.5 3.4 4.2 4.4 4.3 5.5 3.6 5.6 4.2 3.1 4.5 5.7
##  [505] 5.0 4.5 4.3 2.9 5.0 5.3 4.3 4.3 4.7 4.6 4.0 4.9 4.2 3.9 3.0 5.5 4.3 3.7
##  [523] 3.8 5.0 4.1 3.8 4.8 4.4 4.1 4.9 5.6 6.0 4.7 4.2 4.4 3.0 5.1 5.3 3.1 5.0
##  [541] 5.1 3.8 6.1 5.3 5.2 3.2 5.6 5.0 4.4 3.7 4.3 5.1 4.2 4.8 4.3 2.6 5.9 2.9
##  [559] 3.8 4.6 4.1 5.6 4.7 4.4 5.5 5.4 4.0 4.3 4.0 4.8 5.4 4.4 5.7 4.8 4.0 4.5
##  [577] 3.3 3.5 4.0 3.7 3.6 4.5 4.2 4.7 5.9 5.2 5.0 5.4 4.5 6.8 4.3 4.7 4.6 4.3
##  [595] 4.0 3.6 6.0 6.2 3.0 5.1 5.1 6.0 4.9 3.2 5.3 4.8 4.4 3.1 5.6 5.5 2.0 5.8
##  [613] 4.3 4.6 5.5 3.9 4.0 4.9 3.5 2.9 3.4 4.6 4.9 3.6 5.0 4.9 3.9 5.9 5.1 5.7
##  [631] 4.4 4.5 3.5 4.2 4.7 2.6 3.5 4.5 5.3 5.4 3.8 4.7 4.9 4.7 5.4 4.5 5.8 5.0
##  [649] 6.2 5.0 5.2 4.5 3.5 4.4 3.9 5.3 4.7 3.0 3.6 4.2 4.4 4.0 4.6 3.0 4.4 3.4
##  [667] 6.2 3.0 5.5 4.5 3.6 4.4 4.7 4.9 2.2 5.6 6.0 5.4 4.6 3.6 4.8 3.3 5.0 5.2
##  [685] 3.6 3.8 4.1 5.0 3.3 5.8 3.5 2.0 4.3 4.2 5.0 3.8 4.1 4.8 4.6 4.7 3.9 4.6
##  [703] 5.0 4.0 4.7 2.9 4.9 5.1 5.2 5.7 3.5 4.4 5.4 4.0 4.7 4.0 4.1 4.8 5.1 4.3
##  [721] 4.7 5.3 4.7 3.8 3.7 2.6 4.4 2.6 5.8 5.3 4.9 5.7 4.3 3.2 5.9 4.5 3.9 4.9
##  [739] 6.9 4.8 3.1 5.6 4.7 5.8 4.4 4.4 3.4 3.7 6.3 4.5 4.6 4.9 4.2 4.8 4.2 4.4
##  [757] 5.3 5.5 5.0 3.0 4.2 5.1 4.6 3.2 4.9 3.5 4.0 3.8 5.2 5.8 5.0 4.3 5.0 4.5
##  [775] 6.8 4.1 3.4 3.3 4.5 5.3 3.9 4.1 3.0 5.2 5.8 5.0 4.2 4.0 4.8 4.5 3.6 2.8
##  [793] 3.8 3.3 4.9 4.9 4.1 3.4 5.5 4.7 4.3 3.8 3.6 3.8 4.3 5.1 4.7 2.4 5.0 3.9
##  [811] 4.4 4.9 4.3 5.9 3.5 5.1 3.8 4.3 4.0 3.5 3.9 2.0 5.2 5.5 6.3 4.2 3.0 3.1
##  [829] 5.6 3.7 4.7 4.4 3.9 5.1 5.1 3.9 5.4 4.8 5.7 5.8 3.3 4.3 3.3 3.6 5.0 4.5
##  [847] 5.8 4.1 4.1 4.2 3.9 6.4 2.4 2.6 4.8 4.8 4.2 4.1 4.0 3.6 3.3 3.1 4.3 5.2
##  [865] 4.3 3.8 3.4 4.1 4.4 6.7 3.1 4.8 3.2 3.1 3.8 3.0 4.6 3.1 3.5 5.9 4.3 3.6
##  [883] 4.0 3.9 3.9 3.7 4.8 6.2 4.7 4.1 2.7 3.7 2.5 3.6 3.9 4.2 4.3 5.4 4.9 5.0
##  [901] 5.5 4.6 5.0 5.1 5.8 3.6 4.0 4.5 5.9 4.3 5.4 4.5 4.0 5.4 5.1 5.4 4.6 5.5
##  [919] 6.2 5.9 3.9 5.5 5.0 4.4 4.6 4.3 3.2 5.7 5.1 4.2 5.3 5.1 4.8 5.7 6.1 3.8
##  [937] 4.2 5.4 4.1 4.8 4.9 3.7 5.5 3.4 3.9 4.8 3.9 3.7 6.1 3.8 4.6 3.6 4.0 3.4
##  [955] 4.0 3.6 4.3 4.3 6.2 6.1 3.4 4.5 2.8 5.0 5.5 5.8 4.3 5.0 5.0 4.0 5.3 3.8
##  [973] 2.9 5.2 4.3 4.4 5.4 4.1 4.0 5.7 5.5 4.2 5.8 5.1 4.4 1.9 4.1 6.6 5.0 5.5
##  [991] 4.8 4.8 3.8 5.1 4.8 4.8 4.5 4.1 4.7 3.8
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.3 5.3 5.0 5.0 4.9 4.9 4.7 4.4
## 
## $jack.boot.se
## [1] 1.006231
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
## [1] 0.0506936
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
##   3.463628   5.393119 
##  (1.480453) (2.480638)
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
## [1] 0.2147494 1.4410644 0.6583935 0.3022270 0.4257299 0.2347573
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
##    [1] -0.1798482159 -0.2028724585 -0.0189074423 -0.2640313512  0.4684114768
##    [6]  0.0305951889  0.4253179276  0.3507436688  0.9555959998  0.2290165784
##   [11] -0.2146276712  0.2347647100 -0.5023792640 -0.6553146270 -1.1673430032
##   [16] -0.0608990431 -0.0175732009 -0.0959495456  0.1319866306 -0.2310063323
##   [21]  0.0433861583 -0.6792077868  0.3744784024 -0.3327232166 -0.0602950019
##   [26] -0.1469113363 -0.4429185855  0.0934058630 -0.1001457339 -0.0437117132
##   [31] -0.0340382126  0.1203074658 -0.3721608612  0.2499906000  0.1286911249
##   [36]  0.4079356119 -0.3069024263  0.3665178506 -0.0255109376  1.6402489610
##   [41]  0.4774493631  0.6307255410  0.1021344766 -0.0439909657  0.3660727693
##   [46] -0.1489351725  0.5359151626  0.0918046964 -0.4900997697 -0.1592390926
##   [51] -0.1488180351  0.1051724349 -0.2663379240  0.1625757828  0.3048501272
##   [56]  0.8285137907  0.3069754282  0.2848942052  0.8190481230 -0.7101011871
##   [61]  0.5316612649 -0.1047166930  0.1092137421 -0.0446715204  0.4000274367
##   [66]  0.1864858371 -0.2254439368  2.0306192491  0.7746863845 -0.1494279522
##   [71]  0.3755515579 -0.4743897127  0.2102237005  0.0719483522  0.6471587460
##   [76] -0.0459059400 -0.6501586895  0.3974947509 -0.4673216607  0.3505086803
##   [81] -0.1642837559  0.0128556074  0.2979489215  0.5200482941  0.3349327909
##   [86] -0.5255596511 -0.2884687355  0.1812574191  0.0615882906  0.5962609985
##   [91]  0.3343530611  1.5236206283 -0.2011135706  0.0623116145  0.9364503251
##   [96]  0.1321040259  0.5653821660  0.0693191876  0.6688992133 -0.3869850720
##  [101]  0.2751389718 -0.1073368751  0.2369043801  0.5942347809  0.1694689487
##  [106] -0.2366460073 -0.3985409064  0.2791112060  1.0136521211 -0.1425314677
##  [111]  0.0599211908  0.0706586058  0.0257078349  0.1054202964  0.1050067879
##  [116]  0.5397454502  0.6591098817 -0.1241667763 -0.1497322139  0.3549872325
##  [121]  0.2894753146 -0.0449467380 -0.5326604837 -0.2367885038 -0.5847949696
##  [126]  0.0090411277  0.2129517544 -0.1683686411  0.1629871805  0.2147030229
##  [131]  0.0481579889  0.0238781964 -0.4104506180 -0.9611056497  0.5640980460
##  [136]  0.3474299967 -0.0317065575  0.6212580632  0.1364109867 -0.5441102054
##  [141]  0.0555382685 -0.1197142637 -0.0375792441 -0.2425479490  0.5884274950
##  [146]  0.2998062615 -0.3268796418 -0.7393126241  0.1219087906 -0.0623115166
##  [151] -2.0432539446 -1.5671575370 -0.4693297608  0.0761787972  0.4376214661
##  [156]  0.2340285296  0.9268817358 -0.3475572542 -0.4573089156  0.6424361087
##  [161]  1.1035154259  0.7475772042 -0.1274410209 -0.0326196343  0.4138602080
##  [166] -0.4719801486  0.0158734818  0.3775770956 -0.6319543025 -0.0875080649
##  [171] -0.3978704112  0.0021911319  0.0473586786  0.1409468205 -0.5321215594
##  [176] -0.2519281510  1.1408226833  0.5386806344 -0.2224469596  0.4369595206
##  [181] -0.1340517977 -0.0608809790  0.0776869213  0.3371477950  0.2811404838
##  [186]  0.5443998422  0.1569765402 -0.6831990053  0.5878178816 -0.6312700705
##  [191] -0.1343371487  0.7836535351 -0.2027877682  0.5284638592 -0.0003239649
##  [196]  1.3403501897  0.5395211870 -0.2847224467  0.5281930149  0.4245424580
##  [201]  0.3760752676 -0.4723478507  0.5847018098  0.0498830364 -0.5799801355
##  [206]  0.6711848108 -0.8886032048  0.1907185282 -0.2134098621 -0.0192190861
##  [211]  0.1795478824  0.3542000070  0.4212227095  1.0147558445 -0.0954766009
##  [216]  0.4955303724 -0.1608081671  0.2707781512 -0.1358767318  0.0626080647
##  [221] -0.0306088735  0.3451604117  1.1103146274 -0.1756124183 -0.7097905058
##  [226] -0.1972786338  0.4264586776  0.2948456668 -0.0719650995  0.5106667629
##  [231]  0.2056060016  0.1475537929  0.4458996967  0.4685449431  0.3021225815
##  [236]  0.7801229084 -0.1573821038  0.2610928186  0.4132788000 -0.0204572281
##  [241]  0.2139062707  0.3730815928  1.0129534816 -0.4212551724 -0.0616456180
##  [246]  0.1278567163 -0.2326496408  1.1197061201 -0.5089852562  0.3323137476
##  [251]  0.0637585018 -0.2115973324  0.3141975244  1.0225228009  0.1691839505
##  [256]  0.2370906893  0.7507995268 -0.4047935185 -0.3074962289  0.7081074933
##  [261] -0.6366369640  0.4684114768  0.0513124111  0.5259419395  0.5203699412
##  [266]  0.4346771939 -0.7940927721 -0.2412672539  0.4602104248 -0.0437053362
##  [271]  0.2508187994 -0.8449044155 -0.4615865142 -0.0086904367 -0.3781471791
##  [276]  0.0362031308 -0.3306757383  0.2747236636  0.1182316458 -1.2661277705
##  [281]  0.1890841235  0.5954438622 -0.0069804291  0.2918608270  0.3921188460
##  [286] -0.0445297813 -0.1565958097 -1.7754049189 -0.6986484912  0.2495551915
##  [291] -0.6302800538  0.1369182110 -0.0786944050  1.1295925665  0.1058347298
##  [296] -0.1284346363 -0.1929301840  0.7547687597  0.3489326565  0.0746954593
##  [301]  1.2066476433 -0.6348501936 -0.6375318725 -0.0423369571  0.1403034473
##  [306]  0.8933857881 -0.3113128267  0.7712432546 -0.5962904263 -0.2754798853
##  [311]  0.2071119121  0.6585437212  0.4530629845 -0.0285850661  0.3126910446
##  [316]  0.2280738392  0.2894173012 -0.2165655953  0.8171902188 -1.3222391975
##  [321] -0.3356112378  0.5907637532  0.3938617537  0.9186505167 -0.5214732643
##  [326]  0.0392520660 -0.0217518963 -0.1677626816 -0.3322080579  0.1932689586
##  [331]  0.3567104422 -0.2553550695 -0.7171545384  0.5858889557  0.1687168776
##  [336] -0.2813172549 -0.1188871066 -0.1380859536  0.1094517538 -0.2432682297
##  [341]  0.4241777467 -0.3992089730 -0.1491386870  0.4254836320  0.0947026161
##  [346]  0.6929634280 -0.3073223400 -0.1548992103  0.4983182003  0.0031286701
##  [351] -0.4531008124  0.9602029698 -0.2037468525  0.2994489249 -0.0264053157
##  [356]  0.1678783894 -0.3400045710 -0.4397548990 -0.4287750923 -0.9943754594
##  [361]  0.5678975917 -0.3895754545  0.5681834214  0.0722788408 -0.4205130191
##  [366]  0.2869543588 -0.9336305660 -0.4162268398  0.6518270613  0.5415824208
##  [371] -0.5146123806  1.1000263814 -0.0513742916 -0.5228368510  0.1624249945
##  [376] -0.1890272089 -0.3171869116  0.4185736744  0.7387310083 -0.1605280746
##  [381] -0.3008137433  0.2776934406  0.3067522064  0.4013318145 -0.9569094710
##  [386] -0.7254724281 -0.0014165441 -0.2038137473 -0.0818899866  0.6445066779
##  [391] -0.2076497281  1.0182873619  0.1400315256 -0.9036995253 -0.1636347069
##  [396] -0.6398705110 -0.3327601644  0.1311841912  0.0530805066  0.0454359046
##  [401] -0.1062605459  0.2094010424 -1.0161251359  0.4954009346  0.2485172563
##  [406] -0.4238660841 -0.5497215073  0.2672074417 -0.0722921144 -0.5613067023
##  [411]  1.0614921569  0.6727145348  0.7821573700 -0.1262738294 -0.1845017855
##  [416] -0.1928574317  0.1509549796 -0.3849389435 -0.9652770389 -0.0715141464
##  [421]  0.5271944754  0.1266969203  0.8066087868 -0.0085403966  1.2149873997
##  [426] -1.0875771335  0.2310220047  0.1606004951 -0.1711862167  0.2411906514
##  [431]  0.3075593217  0.0512502831  0.2828901659 -0.4805802932 -0.4629728293
##  [436] -0.2325987562  0.1624249945  0.0858472819  0.3810089288 -0.1686289681
##  [441] -0.3841861883 -0.6768366248  0.3325090463 -0.3833375458 -0.0020807410
##  [446] -0.0480854545  0.1255616631 -0.4390191529 -0.6711551466 -0.4157287854
##  [451]  1.2127150509  0.0459873289 -0.7156460366  0.6944840467 -0.2768290776
##  [456]  0.7818693069 -0.1736303973  0.2431190080  0.0405331450  0.6112457975
##  [461]  1.3865451180 -0.2956595677 -0.2251298528  0.3336639579  0.2745182910
##  [466]  0.6017469259  0.4062119328  0.4752293628 -0.2222709930  0.2792633833
##  [471] -0.3374019363  0.9129021939  1.4861253400 -0.9184886122 -0.1190675705
##  [476] -0.2183849537  0.2961868478  0.2129385503  0.6022110157  2.0032830443
##  [481]  0.3176196988  0.3129062724 -0.0553191555 -0.9399633702 -0.3008137433
##  [486] -0.1804896174 -0.6690321569 -0.4969197898 -0.6060720824  1.4188462461
##  [491]  0.7579120879  0.3940447788  0.0738987594 -0.2654494628  0.0500900284
##  [496] -0.3562640349  0.2477031595  0.3141975244 -0.1447095126 -0.1675996341
##  [501]  0.0751488445  0.1962677250  0.8969463300 -0.0156525835  0.1865850411
##  [506] -0.0194304580  0.3448294179 -0.4397878006 -0.6678439727 -0.0967684329
##  [511] -0.1652105859  1.0356622131  0.3415123066 -0.8377724381  0.0794199049
##  [516] -0.9530463628  0.3671605227  0.3465253031 -0.0294854238 -0.9558560974
##  [521] -0.2221205778 -0.5288927967  0.7469153259 -0.0870922832  0.1595061791
##  [526]  1.2303201024  0.0745606758 -0.9760037638  0.3600723125  0.4006767047
##  [531]  0.3280218262  0.1541704209  0.7489676178  0.2033771164  0.0647718941
##  [536]  0.0790591147  0.2946802096  0.0893961617 -0.2356698783 -0.4198933625
##  [541]  0.1490756704 -0.2312924497  0.0661713105  0.6371856595  0.6391971253
##  [546]  0.1327966666 -0.1635053994  0.0153097681  0.1063515975 -0.0237009663
##  [551]  0.1348002084 -0.7156460366 -0.0955241734  0.2817639493  1.3760671784
##  [556]  0.0320025542  0.0819236295  0.1759691818  0.1174266491  0.3760390026
##  [561]  0.1093303667 -0.1946968182  0.7993983685 -0.1683539426  0.0184613843
##  [566]  0.1112680144  0.5300861207 -0.3322080579  0.0180145380 -0.2529115534
##  [571] -0.4369536216 -0.8115874390  1.8018721658  1.0510118500 -0.2145257816
##  [576] -0.6203756158  0.4273868611 -0.7338582349 -0.6476090841  0.3644814895
##  [581] -0.1635061678  0.6556252329 -0.4741017269 -0.1890083691 -0.5327787784
##  [586] -0.3475914444  1.0793976229 -0.0668974803 -0.2785007724  0.0339471020
##  [591]  0.3864787402  0.9343040704  0.1661014611  0.3041652315 -0.1912166040
##  [596]  1.1741182051 -0.2551160916  0.1862928657  0.1131906379 -0.1171365091
##  [601] -0.2603679476  0.1511312972  0.6029857037 -0.1656419907 -0.2192198961
##  [606]  0.4725535592  0.0386368200 -0.1434533793  0.1687688502  0.5795918639
##  [611]  1.1935347417  0.5924407134 -0.2230135906  0.0373645177 -0.2634216623
##  [616] -0.2205156630 -0.2000951774  0.5768420417  0.3270806562  0.1549357562
##  [621] -0.4998435268 -0.4653869489  0.4620512738 -0.2377194516  0.4310746222
##  [626]  1.1679276922  0.4690184657 -0.6710039017 -0.6292852740 -0.2812066555
##  [631] -0.1433278581  0.6080795263  0.0010459766  0.0627554804  0.4034558830
##  [636] -0.9053088424  0.5987192924 -0.0488674078  0.7668356279 -1.0621264288
##  [641]  0.4365699300 -0.8314927624 -0.2755552741 -0.2945916321 -0.6121808538
##  [646]  0.9900923051  0.3203090444  0.0654468216  0.3839335326  0.7322087565
##  [651]  0.2269814068  0.5509442971  0.7795565967 -0.1962262808 -0.2989590874
##  [656] -0.3760121930 -0.1475322897  0.1815365034 -0.0904025164 -0.3150216294
##  [661] -0.1278538717 -0.0375395184 -0.2838854058  0.3323868071  0.0490383549
##  [666]  1.0387313926  0.1008074304  1.0384360742  0.5401460281 -0.3654986815
##  [671] -0.6895309673 -0.2400082711  0.1635977756 -0.4312293700  0.1344931094
##  [676]  0.0689215237  0.0197548422 -0.0806486759  0.6325659473 -0.2967892221
##  [681] -0.4743897127  0.9347371382 -1.1987499099 -0.2299306163  0.0418499269
##  [686]  0.5258787396  0.3072289449  0.1515191693  0.2323752989  1.2796587009
##  [691]  1.2114652182 -0.3326689765 -0.3320702148  0.2610928186  0.9426896284
##  [696]  0.2748088722  0.1400922285 -0.2441594973 -0.4270900094 -0.4821562688
##  [701] -0.4269606469  1.1488454216  0.4265464647  0.1523462262  0.3492659991
##  [706]  0.2115922273 -0.0848222263  0.0697844909 -0.5159450497  0.7633250109
##  [711]  0.1306875680 -0.0896312297 -0.1476316879 -0.4833758530  0.1286953156
##  [716]  0.0654824015  0.2047099428 -0.2884687355 -0.0131632394  0.3176196988
##  [721] -0.4995737338  0.7020329384  0.4664169572 -0.2374125183  0.3992352924
##  [726]  0.8180141049 -0.4355993466  0.8227847761 -0.3041205663 -0.0935476466
##  [731]  0.6558316646  0.3419583589  1.5364123688  0.1189277417  0.4893029513
##  [736]  0.2164700904  0.7589598729  0.6765983144  0.1330087245  0.2280084019
##  [741] -0.3716332161  0.7910979763  0.7605368456 -0.0285850661  0.3979803934
##  [746] -0.0315041062 -0.4653869489  0.6317302779  1.1648435140  0.5281736180
##  [751] -0.3650650618  0.0435555314 -0.5639593285  0.0384892412  0.3023228628
##  [756] -0.3443276025 -0.0741826429 -0.3321719256 -0.8674768497 -0.6412667043
##  [761]  0.5791405859 -0.3094372958 -0.0218277473  0.2377589147  0.0549975640
##  [766] -0.2700893590 -0.6761920869 -0.3408844146  0.6381115335 -0.0234218046
##  [771] -0.1373775756  0.8084666709 -0.0835702480  0.6725016062  0.3431106038
##  [776] -0.1307506654 -0.4172686137 -0.2619411281 -0.5067216566 -0.0482115724
##  [781]  0.2970178990 -0.2925885731  0.5158343354 -0.5741520006 -0.0935476466
##  [786]  0.4000274367  0.7616948773  0.5827244170  1.0199236312  0.3539526504
##  [791] -0.4204654841 -0.0711856653  0.5410873002 -0.0927278594 -0.6471031733
##  [796]  0.1105205172 -0.4204399757 -0.2243038924  0.4929094204  0.3692245645
##  [801]  0.3482840055  0.4442432875  0.0202907526  0.2304930466  1.5544154207
##  [806]  0.3900425779 -0.0932883186  0.1839886205  0.0100499279  1.0680643360
##  [811] -0.0337437711 -1.5920869958 -0.0348595113  0.2295683529 -0.4206252523
##  [816]  0.4573917638 -0.2849453551 -0.4257671204 -0.0253517651  0.1853945751
##  [821] -0.4450404960  0.2744800997 -0.0926635654 -0.0344333176 -0.6789935882
##  [826]  0.5952382364  0.5371600541  0.0389240554  0.1499044497 -0.0196499415
##  [831]  0.0528880692 -0.2184739041  0.8443805450  0.6843590738  1.3423667389
##  [836]  0.4509997014  0.2206975604  0.3733265205 -1.2595967187  0.2775798061
##  [841] -0.7408851800  0.4000274367  0.0416806105  0.0421037759  0.3381351839
##  [846]  0.7571664006  1.8970355065 -0.3746674121  0.6449169305 -0.1512210453
##  [851] -0.1203983307 -0.4859265912  0.3069813945  0.8434318187  0.1598332807
##  [856]  0.1894543092 -0.3004005634  0.3565310068 -0.3429612396  0.1780791160
##  [861]  0.1454323282  0.8544979125  0.6234279855  0.1017005570  0.3982999879
##  [866]  0.2613289624  0.2930933606 -0.5865635277  0.5837842166  0.5442074285
##  [871]  0.1587994823 -0.4109539408 -0.0587733677 -0.3119792680 -0.0990838471
##  [876]  0.3266099558 -0.0216997645  0.9137601891  0.5877867549  1.0006833274
##  [881] -0.1072704438  0.0376557395  0.2171508263  0.0623886505 -0.4307147217
##  [886]  0.9602417163  1.5558808332 -0.2071723277 -0.9200918917  0.2652533483
##  [891] -0.3179411894  0.1899934096 -0.1293769911 -0.2922007519  0.4398744543
##  [896]  0.0309098193  0.9910518307  0.2770206836 -0.3040112473  0.2174497398
##  [901]  0.3470736253  0.3482840055  0.1057131405 -0.0036001239  1.0881472956
##  [906]  0.1093027446  1.8772819501  0.1765756093 -0.1243469451  0.4056154623
##  [911] -0.0007590814 -0.4649028537  0.0646046389 -0.5899479561  0.4124936026
##  [916] -0.4898551181 -0.7401345522  1.3933768156 -0.3730024191 -0.0856968461
##  [921] -0.7687509431  0.9913072246 -0.5319273375 -0.1346927711  1.4023188387
##  [926] -0.3080470988  0.2951305657  0.3843312047  0.1319866306 -0.1444270952
##  [931]  0.9427554847  0.2901616006 -0.6062367537  0.5570855660  0.5482555751
##  [936]  0.0067991066  0.8554676319 -0.2565502473  0.0147599061 -0.0935242976
##  [941]  0.5248097337  0.3785129369  0.4087022003  2.0429631961 -0.3207219319
##  [946] -0.2967892221 -0.5987001579 -0.4741751298 -0.4853932575 -0.3379051177
##  [951]  0.0637830164 -0.8629090442  0.2584436534  0.2380301080 -0.3631564286
##  [956] -0.0610911035 -0.4995649933  0.5883699138  0.1350217400  0.2807072277
##  [961] -0.6717500087 -0.8117276459 -0.7010625087  0.4506047362  0.3615386020
##  [966]  0.4794180452  0.2425473124 -0.4188840563 -0.5420593846  0.5265044417
##  [971]  0.8118110747  0.3869144646  0.4847505978 -0.1745739037 -0.1502882303
##  [976] -0.1792594486  1.1634796125  0.1857581067 -0.5110511973  0.3253736282
##  [981] -0.6541591920 -0.0085403966 -0.3409556916  0.1759632072 -0.0412626478
##  [986]  0.9389887209  0.3183593685  0.0559149652  0.3095598738  0.1166165965
##  [991]  0.1545990762 -0.5774197798  0.8024366402  0.4395159802 -0.0668083876
##  [996]  0.5883709306 -0.1016669114  0.3738996962  0.1262526394  0.0416705165
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
##   0.64223024   0.29485005 
##  (0.09323977) (0.06592754)
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
## [1]  0.265864428  0.571515713  0.107226203 -0.456636285 -0.006737362
## [6] -0.308508990
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
## [1] 0.0473
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9202606
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
## t1*      4.5 -0.006606607   0.9014775
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 3 4 7 9 
## 1 4 1 1 1 2
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
## [1] -0.0163
```

```r
se.boot
```

```
## [1] 0.9109963
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

