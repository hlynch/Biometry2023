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
## 0 1 2 4 8 
## 2 2 2 1 3
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
## [1] -0.0409
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
## [1] 2.657496
```

```r
UL.boot
```

```
## [1] 6.260704
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
##    [1] 5.6 3.9 4.7 2.9 4.3 4.2 5.1 5.4 4.0 4.0 4.6 3.1 4.4 2.9 4.4 4.6 5.0 5.5
##   [19] 5.2 6.0 3.9 4.4 5.2 3.8 4.1 3.4 3.7 3.2 5.5 4.1 6.0 5.0 5.1 3.2 4.1 4.6
##   [37] 4.6 5.5 3.3 3.7 5.3 5.0 4.5 4.6 3.5 5.0 3.8 5.4 5.7 5.8 5.5 3.4 5.2 5.1
##   [55] 3.9 3.6 3.9 4.5 4.3 4.5 4.1 3.9 4.2 3.2 5.2 5.8 4.1 5.2 4.3 3.5 5.1 2.0
##   [73] 4.2 3.5 5.5 3.3 5.8 4.6 5.9 4.0 5.0 5.1 3.2 4.1 4.0 5.3 3.5 4.0 3.6 5.0
##   [91] 3.7 4.8 4.6 2.6 6.5 4.4 3.9 5.4 4.1 3.0 4.1 3.7 4.5 3.8 4.3 5.3 6.4 3.5
##  [109] 5.0 5.7 4.3 4.1 4.6 4.0 4.7 3.0 4.2 5.7 3.7 3.8 4.2 5.7 5.2 4.3 5.0 6.4
##  [127] 4.9 4.2 6.2 5.3 6.3 5.2 4.1 4.9 4.0 4.7 5.2 5.2 5.5 4.2 5.1 4.0 3.4 4.3
##  [145] 4.7 4.7 4.1 5.0 4.7 5.3 3.7 5.4 5.5 5.3 3.7 2.1 4.3 4.6 6.2 3.7 4.3 3.9
##  [163] 5.1 5.0 5.7 5.4 4.2 4.3 3.4 5.0 5.1 4.7 4.5 6.1 5.2 4.8 5.6 3.6 4.9 5.6
##  [181] 4.1 4.5 5.0 4.8 3.9 4.6 4.9 5.4 4.8 2.7 4.4 5.2 4.9 4.5 3.2 2.7 6.3 3.5
##  [199] 3.3 5.4 4.3 5.1 4.0 6.1 2.6 3.5 5.1 3.8 5.8 5.8 5.0 6.2 4.7 4.2 3.6 4.5
##  [217] 3.9 3.6 3.4 5.5 2.8 4.4 6.1 6.6 3.9 3.1 3.4 3.5 5.1 3.7 3.7 5.3 5.3 6.3
##  [235] 4.7 5.4 4.2 4.0 4.0 4.8 5.3 3.0 3.8 3.7 3.9 5.9 5.7 4.5 3.8 4.2 3.7 5.4
##  [253] 5.0 4.8 3.0 3.9 5.1 3.8 2.7 4.5 4.5 4.4 3.9 4.4 4.1 4.0 5.0 6.3 4.5 3.5
##  [271] 2.7 5.9 5.6 4.2 3.6 5.0 5.3 4.3 5.8 4.8 4.8 4.5 3.7 6.0 4.2 5.1 4.1 2.7
##  [289] 5.4 3.5 4.0 3.8 4.9 5.5 2.9 2.3 4.2 4.4 4.4 5.4 4.2 4.4 4.2 5.6 4.4 4.3
##  [307] 4.7 3.7 5.6 3.2 4.3 4.7 3.6 4.3 4.8 5.5 5.2 4.3 4.6 3.8 4.9 5.3 5.4 5.1
##  [325] 3.9 3.9 4.6 3.1 3.9 4.9 3.8 3.7 4.9 6.1 4.0 4.7 3.4 4.5 5.0 4.2 4.7 4.1
##  [343] 4.5 4.7 4.9 4.2 2.8 6.0 5.5 4.7 4.9 5.4 4.8 3.5 4.3 6.3 2.6 4.6 3.8 3.7
##  [361] 5.7 4.8 5.3 4.1 4.8 4.0 4.9 5.1 3.6 4.9 4.0 4.5 4.7 4.5 4.6 3.7 5.9 5.9
##  [379] 3.5 2.9 3.6 4.8 4.9 4.8 3.6 3.7 5.1 3.5 5.0 4.8 3.5 4.7 3.9 4.4 4.7 4.8
##  [397] 3.4 5.6 3.7 4.6 4.1 3.8 4.1 4.8 4.7 3.4 4.9 5.0 5.0 5.0 3.0 4.7 4.6 5.0
##  [415] 3.3 4.6 6.5 5.6 3.4 6.5 4.0 4.9 5.4 6.1 4.4 5.8 6.0 5.4 2.9 2.8 5.0 4.5
##  [433] 6.0 4.8 4.2 4.7 5.6 5.9 5.1 3.9 6.4 3.3 6.1 4.1 4.1 5.0 3.2 5.5 4.6 5.1
##  [451] 3.7 3.2 3.8 5.3 6.5 3.5 3.6 5.0 5.3 3.6 4.1 3.2 5.8 4.1 4.8 3.8 4.2 5.2
##  [469] 3.9 3.6 5.1 5.2 5.5 3.8 5.5 3.6 3.4 4.0 5.6 7.2 6.0 5.0 4.7 4.4 5.7 5.1
##  [487] 4.9 5.8 4.4 4.8 4.7 5.8 4.8 3.7 4.5 5.3 4.5 4.8 3.8 4.9 5.1 3.8 4.9 5.3
##  [505] 3.7 3.9 4.5 4.4 5.3 5.5 5.5 4.5 5.7 4.9 3.4 3.9 4.6 4.5 3.4 4.8 5.9 5.8
##  [523] 4.3 4.8 4.3 5.4 5.6 3.5 4.3 3.6 4.1 6.2 5.7 5.9 5.1 4.7 5.9 3.8 4.6 4.7
##  [541] 5.2 3.4 4.1 5.4 5.7 4.1 3.8 4.5 5.3 3.7 6.0 5.2 4.3 5.8 3.0 4.3 6.0 3.9
##  [559] 4.0 3.6 4.8 4.2 4.7 4.6 3.9 3.9 4.5 4.5 3.0 4.2 4.6 4.7 5.3 4.9 5.6 2.4
##  [577] 3.6 2.5 3.0 4.6 5.3 5.9 5.5 5.0 4.4 4.2 5.0 4.8 4.7 3.1 2.9 4.8 5.4 5.2
##  [595] 4.3 4.8 3.4 4.7 5.7 5.6 3.6 3.1 3.9 2.6 4.6 4.1 3.1 3.3 2.8 5.1 4.9 3.8
##  [613] 5.1 6.0 6.0 4.4 3.9 4.7 5.8 4.6 5.5 5.9 5.3 3.9 4.3 4.3 3.0 4.6 3.7 4.2
##  [631] 4.6 4.1 4.8 5.5 3.4 4.4 4.1 5.4 4.9 4.2 5.0 5.1 3.7 3.0 4.7 4.6 4.9 4.0
##  [649] 4.7 5.0 5.4 6.6 3.6 6.3 5.1 4.3 4.9 5.4 6.4 4.5 4.8 4.3 4.6 5.1 4.2 3.9
##  [667] 5.4 4.9 5.2 4.3 4.5 5.5 3.4 6.1 4.3 4.7 4.2 5.0 6.0 3.8 4.5 4.8 3.6 4.9
##  [685] 4.5 3.5 4.3 4.1 4.1 5.0 4.6 4.0 3.7 2.5 3.3 5.0 2.2 5.1 5.7 4.0 4.0 4.9
##  [703] 5.4 5.5 4.4 4.0 4.9 5.5 3.9 4.8 3.6 4.2 3.5 2.9 4.4 5.0 3.7 5.2 6.3 5.1
##  [721] 5.3 4.7 4.6 5.3 5.3 4.7 4.8 6.1 5.8 5.7 5.0 5.7 2.9 3.6 4.2 3.6 4.4 4.8
##  [739] 4.6 4.6 6.8 3.9 4.5 3.4 5.6 4.0 4.0 5.3 4.0 3.4 4.1 5.0 6.3 2.9 4.5 4.8
##  [757] 7.6 4.2 3.1 2.1 4.6 5.8 4.0 6.3 3.5 1.8 3.8 4.6 4.2 6.3 5.4 5.2 4.4 4.6
##  [775] 3.5 5.5 3.6 4.0 5.2 5.4 5.4 4.2 5.2 3.3 4.7 4.4 5.4 5.8 2.7 4.9 4.1 5.9
##  [793] 5.3 3.4 4.7 3.0 5.3 4.3 3.8 3.5 3.7 4.8 4.3 4.0 4.5 5.3 5.1 3.8 5.0 4.5
##  [811] 6.4 5.1 4.2 4.4 4.6 3.7 4.5 3.3 6.1 5.5 3.2 4.6 5.1 4.3 6.0 5.4 4.4 3.7
##  [829] 2.5 4.3 2.6 4.1 2.8 2.8 4.0 4.6 6.1 5.9 4.1 3.5 5.2 5.3 5.9 5.6 3.8 5.4
##  [847] 6.2 4.1 5.7 3.4 5.9 4.8 4.4 4.4 4.3 3.4 4.5 4.7 3.5 5.7 5.3 3.8 3.4 4.8
##  [865] 4.9 5.4 3.4 3.9 3.8 5.1 4.3 3.5 4.9 6.0 4.9 5.8 5.6 6.4 4.0 4.7 5.7 4.4
##  [883] 3.2 3.4 4.6 4.7 4.7 6.7 4.6 4.6 3.1 4.2 2.7 4.2 4.7 3.5 5.4 4.1 5.0 5.6
##  [901] 2.9 5.3 3.8 4.2 4.4 3.0 5.1 4.9 5.0 5.0 5.7 4.4 5.0 5.3 4.4 4.7 3.0 4.0
##  [919] 1.9 2.7 4.1 2.8 3.6 3.5 4.8 5.5 4.2 4.5 4.2 3.2 4.7 5.1 3.4 3.7 5.0 4.8
##  [937] 4.3 4.3 4.7 2.9 4.5 3.5 4.8 4.9 4.0 4.6 5.1 3.8 4.8 4.7 5.1 5.7 5.1 4.9
##  [955] 4.9 3.7 5.2 5.2 5.5 3.6 5.0 5.4 4.8 3.3 4.2 3.8 3.5 5.3 5.8 4.3 4.4 4.3
##  [973] 6.6 6.0 6.1 3.7 4.9 4.4 3.8 4.8 5.4 5.2 4.4 4.2 2.8 5.4 5.8 3.4 4.7 4.7
##  [991] 4.3 5.3 3.1 4.9 5.8 4.8 1.9 3.9 4.6 5.4
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
## 2.7975 6.3000
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
##    [1] 4.3 3.5 4.7 4.3 5.5 4.5 3.9 4.0 4.7 3.9 4.9 6.7 4.0 4.2 3.9 3.2 4.5 3.5
##   [19] 3.1 4.3 5.0 4.3 3.9 5.6 4.9 2.9 4.9 4.7 5.3 4.8 3.6 4.1 3.5 4.7 5.0 6.2
##   [37] 4.7 4.5 5.5 5.9 4.5 5.2 6.3 5.0 3.4 5.0 3.3 5.2 5.0 4.3 4.5 5.1 7.1 2.3
##   [55] 5.1 7.3 3.6 3.5 5.3 5.0 5.4 4.9 3.7 1.9 5.7 5.0 3.7 4.6 2.9 2.9 5.4 4.7
##   [73] 3.1 4.7 6.7 5.3 3.6 6.6 4.5 5.3 3.7 4.4 4.5 4.3 5.5 4.4 3.3 5.1 4.2 5.5
##   [91] 3.9 3.9 5.2 5.5 4.0 4.1 3.5 4.0 4.2 4.2 3.5 3.6 5.4 4.0 5.2 3.7 3.1 3.5
##  [109] 5.7 4.7 4.6 3.6 4.1 4.9 5.5 3.1 5.2 3.5 4.3 5.5 5.1 3.9 5.2 4.8 4.0 6.7
##  [127] 3.4 4.5 4.5 5.7 4.5 4.5 4.8 4.0 3.9 4.3 5.5 5.9 5.8 4.4 3.8 4.4 3.6 4.3
##  [145] 5.4 3.4 6.5 5.3 4.0 2.6 5.4 3.8 3.5 5.8 4.3 5.2 4.3 4.5 4.5 5.7 3.2 4.8
##  [163] 4.0 5.4 3.8 5.5 4.2 4.7 4.1 5.2 4.5 4.1 4.3 5.0 5.2 4.8 5.8 4.8 2.6 5.7
##  [181] 4.8 4.9 5.1 4.5 4.8 3.5 5.8 4.4 4.5 5.2 5.4 3.6 6.3 5.8 5.7 4.9 2.4 3.3
##  [199] 4.0 4.2 5.5 4.7 3.6 2.9 5.1 5.0 5.0 5.4 3.1 5.5 6.4 4.4 1.6 3.5 4.4 5.0
##  [217] 6.0 3.2 4.5 4.8 4.6 3.5 6.3 4.3 3.6 2.9 4.3 5.5 3.2 5.3 5.4 4.3 4.6 5.3
##  [235] 2.6 5.9 2.9 5.1 4.5 4.5 4.6 4.1 5.6 3.9 4.4 4.0 4.3 4.3 6.5 4.9 3.9 3.7
##  [253] 3.9 2.7 3.2 4.0 3.7 5.5 3.3 4.7 2.9 5.5 5.5 4.3 4.2 3.1 4.8 4.5 3.8 5.6
##  [271] 5.9 4.9 4.7 2.6 5.5 4.8 4.7 4.7 6.0 4.2 2.9 5.5 3.8 3.0 5.2 4.3 4.5 4.4
##  [289] 4.6 3.5 3.3 4.2 5.2 4.5 5.1 4.3 4.2 3.7 4.1 6.0 4.9 5.3 4.9 3.2 4.8 4.6
##  [307] 4.9 4.2 3.5 2.8 4.4 4.9 5.6 4.0 5.7 4.5 5.8 3.6 4.9 4.8 4.2 4.7 6.5 5.1
##  [325] 4.1 6.1 5.0 3.6 3.6 5.6 2.2 4.7 4.7 3.0 4.7 3.3 5.1 4.0 4.1 5.6 4.6 4.9
##  [343] 4.4 5.6 5.0 5.6 4.8 4.5 3.3 3.6 4.9 4.2 4.0 5.3 4.8 3.1 4.6 5.5 5.0 5.3
##  [361] 3.8 4.9 3.8 5.8 3.1 5.3 4.6 5.1 3.8 5.4 5.4 4.5 2.9 2.8 2.7 5.5 4.6 3.5
##  [379] 2.7 4.7 4.8 6.2 5.0 4.7 4.1 3.0 3.5 3.0 5.4 2.4 4.4 4.4 4.1 3.9 3.3 2.7
##  [397] 4.4 4.1 3.7 4.2 4.5 5.0 3.6 5.1 5.6 5.1 3.9 3.9 3.2 5.0 3.8 4.5 5.0 4.8
##  [415] 2.6 4.8 3.7 4.6 3.8 4.2 3.4 5.0 5.3 3.5 3.0 4.2 4.0 6.1 5.7 4.5 5.6 5.0
##  [433] 3.5 2.4 3.9 5.3 5.5 4.4 5.3 4.4 5.9 4.3 5.5 4.9 5.5 6.8 5.0 4.2 6.8 5.2
##  [451] 3.3 2.9 4.7 3.6 5.5 4.6 6.3 5.6 5.2 3.9 3.1 4.6 3.6 3.9 4.6 4.7 3.5 2.8
##  [469] 3.9 5.3 4.6 4.4 4.1 5.8 4.0 5.7 4.8 2.9 5.6 4.6 2.9 6.3 5.6 5.7 3.7 3.6
##  [487] 5.3 4.3 4.2 4.5 4.4 4.2 5.7 4.0 4.0 4.4 5.0 4.3 3.9 4.2 4.2 3.6 3.4 5.5
##  [505] 4.3 3.4 5.3 5.6 4.7 4.8 5.1 4.9 5.7 4.9 5.3 4.6 5.0 3.0 4.4 4.5 4.2 6.7
##  [523] 5.4 4.1 5.3 5.2 5.3 3.6 4.8 5.2 4.9 3.5 2.9 2.9 5.5 5.6 3.8 4.6 4.0 4.2
##  [541] 3.9 4.1 5.7 5.2 3.8 5.6 4.5 5.1 3.9 4.6 3.7 3.9 5.3 4.2 3.8 2.6 3.0 4.4
##  [559] 5.0 5.3 4.6 4.5 5.8 3.3 4.8 4.0 5.6 4.4 3.5 3.8 5.2 4.1 5.1 3.6 4.6 2.7
##  [577] 5.7 4.2 4.5 4.0 3.8 4.7 3.0 4.3 5.8 4.4 4.6 5.5 4.7 5.0 4.1 4.7 5.5 4.7
##  [595] 5.8 3.6 3.3 4.6 4.2 5.9 3.6 4.4 3.5 4.6 4.1 4.3 4.3 3.2 4.2 3.7 5.4 3.9
##  [613] 5.1 4.6 4.2 3.2 5.3 5.0 4.7 5.5 3.3 3.6 4.5 5.7 4.7 6.4 5.3 3.7 3.3 5.0
##  [631] 4.4 4.4 4.4 3.9 6.2 3.7 4.4 3.8 5.2 5.2 6.0 3.9 5.4 5.7 4.1 4.8 3.9 5.8
##  [649] 4.2 3.8 5.7 5.4 2.7 5.0 3.5 4.4 5.2 4.7 4.8 4.6 3.6 5.5 4.9 3.0 5.9 4.7
##  [667] 4.6 4.0 2.8 4.2 4.4 3.4 3.9 3.9 3.5 4.2 3.5 5.0 4.0 5.2 5.0 4.0 5.4 3.8
##  [685] 5.8 5.4 3.9 3.7 4.7 3.8 5.5 3.2 2.9 4.9 3.2 5.7 5.1 3.6 5.9 5.5 3.5 4.3
##  [703] 6.0 5.5 4.2 4.3 4.2 4.3 4.8 5.0 4.7 4.4 4.0 5.0 3.8 4.7 3.9 5.2 4.6 6.0
##  [721] 3.3 4.3 4.2 4.9 5.9 5.1 4.4 4.6 5.0 6.5 3.7 5.1 5.5 5.2 3.1 5.6 4.4 4.8
##  [739] 6.3 4.1 3.1 4.5 4.4 4.2 5.6 5.8 2.8 4.8 5.2 5.5 5.4 5.5 4.8 4.7 4.4 4.6
##  [757] 4.4 3.7 5.9 4.9 2.0 4.3 2.8 4.3 5.4 6.3 4.2 4.6 5.1 6.1 5.5 5.4 3.9 3.7
##  [775] 3.6 4.4 4.5 5.3 3.5 5.3 3.9 5.2 2.5 3.8 4.3 4.1 4.7 4.1 5.5 4.7 4.7 4.0
##  [793] 5.7 3.8 5.3 5.3 6.0 5.2 3.4 6.4 3.2 5.5 5.4 5.2 3.4 4.7 4.8 4.5 3.3 5.3
##  [811] 4.2 3.1 3.9 3.9 2.4 5.3 5.2 4.4 4.9 5.2 5.0 5.1 4.0 5.5 2.4 3.6 4.9 4.6
##  [829] 4.0 4.6 4.8 4.8 4.7 4.5 4.2 5.4 5.2 4.7 4.0 5.1 5.6 4.4 4.7 4.9 5.4 5.2
##  [847] 4.3 3.4 3.6 6.0 3.3 4.3 6.1 4.4 3.8 4.6 5.1 3.8 4.2 4.3 3.9 3.1 4.6 3.9
##  [865] 3.2 5.3 5.7 5.3 5.8 5.2 5.8 2.5 3.9 3.0 6.3 5.0 4.8 6.9 3.0 3.2 4.8 5.8
##  [883] 5.5 5.5 4.3 4.3 4.4 5.4 5.6 3.5 4.6 5.3 3.8 4.9 6.0 4.0 4.4 2.2 3.7 5.0
##  [901] 3.6 4.8 2.5 3.8 4.3 3.5 3.7 5.1 5.0 3.9 4.9 6.1 4.8 3.8 4.2 5.9 3.1 5.6
##  [919] 5.4 3.6 4.8 5.9 3.6 3.4 5.0 5.4 4.8 3.4 4.2 5.4 5.9 4.6 4.9 3.4 4.3 3.6
##  [937] 5.3 3.6 3.3 4.3 5.9 3.0 4.5 5.2 4.9 4.4 3.8 3.6 4.2 4.6 4.6 3.1 5.0 4.9
##  [955] 3.9 3.7 3.3 5.3 4.1 4.3 4.6 5.6 5.6 6.1 4.8 3.9 5.7 3.9 5.0 5.3 3.8 6.2
##  [973] 5.5 4.5 4.5 3.6 3.5 5.2 5.7 4.5 5.6 4.4 5.7 2.8 4.6 6.3 6.5 4.3 4.6 4.4
##  [991] 6.2 5.0 4.9 4.0 3.7 5.6 4.2 6.4 5.5 6.9
## 
## $func.thetastar
## [1] 0.0258
## 
## $jack.boot.val
##  [1]  0.49858757  0.38925620  0.30724638  0.19039548  0.09556213 -0.02164384
##  [7] -0.12631579 -0.25273775 -0.39296188 -0.53993902
## 
## $jack.boot.se
## [1] 0.9785577
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
##    [1] 4.4 2.5 4.9 5.1 4.9 2.8 4.6 5.5 4.3 4.2 5.4 4.0 4.7 4.8 3.7 4.7 4.7 5.2
##   [19] 4.6 5.2 5.5 5.0 4.3 4.7 3.6 4.9 3.3 5.3 5.1 3.8 3.3 4.7 5.4 4.1 5.7 4.4
##   [37] 4.5 4.8 5.4 4.3 4.9 5.4 5.7 5.5 4.8 3.9 4.7 4.8 2.6 6.2 5.8 7.4 3.6 3.2
##   [55] 3.6 4.8 5.8 4.5 4.5 4.9 1.6 4.5 3.3 3.8 2.8 5.0 4.9 3.6 5.1 4.4 3.9 3.9
##   [73] 5.8 5.1 4.9 3.7 4.3 4.6 5.1 4.7 5.4 4.9 4.3 3.5 4.6 5.8 4.4 5.6 5.7 4.8
##   [91] 3.8 4.7 4.4 4.6 5.4 4.5 4.7 4.3 3.9 3.1 6.1 5.8 4.2 4.6 3.7 4.5 4.1 4.0
##  [109] 3.8 4.0 3.3 6.2 4.3 3.9 5.8 2.9 4.7 5.5 4.1 4.0 5.8 5.6 2.7 5.2 6.0 4.8
##  [127] 3.9 4.1 3.0 5.0 3.2 5.3 5.1 6.1 3.4 4.0 5.1 4.4 3.2 6.6 4.2 4.2 6.0 4.9
##  [145] 5.1 4.4 4.6 5.3 3.4 4.7 4.1 4.2 3.8 3.1 3.6 5.0 4.1 4.7 6.5 4.2 2.9 4.3
##  [163] 3.8 3.5 4.3 4.4 5.6 4.8 5.4 4.6 2.5 3.5 3.7 4.6 5.1 2.9 4.9 5.5 4.1 5.3
##  [181] 5.3 5.5 5.9 5.6 5.0 3.8 4.6 3.9 5.3 4.2 2.4 6.1 3.3 4.0 3.1 4.2 3.6 5.3
##  [199] 4.5 6.9 4.0 3.8 3.6 2.9 5.4 3.5 4.7 5.8 4.5 4.9 4.2 3.3 4.0 5.2 4.6 4.5
##  [217] 5.2 5.7 4.5 4.1 5.9 3.1 6.0 4.4 5.5 5.1 3.7 4.4 5.1 5.8 3.4 5.7 3.5 4.5
##  [235] 3.7 5.2 4.7 3.4 2.7 5.2 4.2 5.7 3.8 6.4 3.7 5.3 3.9 4.6 4.8 4.9 5.2 4.4
##  [253] 5.1 3.8 5.7 2.7 2.8 4.5 3.9 4.1 4.8 5.4 3.6 3.4 5.0 3.4 6.3 3.8 5.4 3.6
##  [271] 3.7 3.1 6.2 3.2 4.0 3.9 3.4 3.5 4.2 3.4 2.9 3.2 4.7 3.3 6.1 3.7 3.5 3.2
##  [289] 6.1 4.8 3.1 4.0 4.1 4.4 3.5 4.6 4.9 4.7 5.1 4.9 4.8 4.9 5.2 4.6 3.7 4.2
##  [307] 4.4 3.2 6.0 5.3 3.9 4.3 4.9 4.4 4.5 4.6 2.7 4.0 6.5 4.2 5.5 4.9 5.4 5.5
##  [325] 4.2 4.4 6.5 6.8 6.1 5.9 4.0 3.1 3.8 4.8 4.7 3.7 5.4 3.7 5.1 4.6 3.1 4.3
##  [343] 5.1 3.5 3.3 3.8 3.6 2.6 4.2 4.7 3.0 3.7 2.7 2.6 4.3 5.5 4.4 5.8 4.5 4.0
##  [361] 3.0 5.4 6.1 3.4 4.1 4.4 5.1 4.7 5.0 4.1 5.3 3.2 4.6 4.4 4.8 4.4 3.9 5.5
##  [379] 4.0 4.0 6.3 5.4 3.9 4.0 4.6 4.9 3.9 3.9 4.7 4.5 5.0 4.9 4.4 5.5 3.6 2.9
##  [397] 4.9 3.1 4.8 3.6 4.1 4.0 4.1 4.8 4.2 5.1 3.0 4.2 5.2 3.6 5.0 3.3 4.8 3.6
##  [415] 3.4 3.8 4.3 4.0 3.3 4.5 7.2 2.6 5.0 4.9 5.9 4.0 3.6 5.3 4.8 5.4 4.9 3.6
##  [433] 5.2 4.0 5.8 3.8 4.4 6.5 4.5 4.4 5.3 5.3 4.3 2.6 3.2 4.8 4.2 5.9 2.1 4.5
##  [451] 5.5 4.6 4.6 3.9 3.7 4.7 2.8 4.1 6.9 5.2 5.4 4.3 4.7 4.6 4.1 4.8 4.5 3.5
##  [469] 6.2 3.9 4.3 4.6 5.9 4.8 4.9 4.3 4.4 4.2 4.0 3.8 2.0 4.6 4.8 4.3 5.6 4.0
##  [487] 3.6 3.1 4.3 5.1 4.5 4.5 4.6 4.1 5.5 5.8 4.9 2.8 4.8 4.1 5.7 4.4 4.6 3.7
##  [505] 4.5 5.1 5.1 5.5 3.6 3.3 3.2 4.5 4.1 3.0 3.6 4.9 5.6 4.5 3.4 2.7 6.0 4.6
##  [523] 5.3 6.0 5.1 4.8 6.4 6.4 2.9 5.6 5.7 4.2 5.1 3.4 5.1 4.7 5.5 3.4 2.7 5.0
##  [541] 3.6 4.7 5.7 5.2 3.2 5.0 3.6 6.6 4.3 3.3 5.4 5.8 4.6 5.3 4.4 6.6 3.5 3.1
##  [559] 4.5 4.8 3.1 4.4 4.5 4.1 5.1 3.5 5.2 5.0 3.5 4.3 6.0 6.5 4.8 5.7 3.4 3.1
##  [577] 5.1 4.0 4.5 3.3 4.3 5.2 4.6 3.7 4.8 4.6 4.7 3.7 4.4 3.0 5.3 4.8 4.0 5.6
##  [595] 3.0 4.9 5.3 4.0 4.5 5.8 4.5 4.7 3.2 3.6 4.3 4.3 5.3 3.5 4.7 5.2 4.4 4.0
##  [613] 5.0 7.4 4.1 3.4 4.6 4.5 4.4 5.3 5.6 4.3 6.1 4.9 2.6 5.6 4.9 4.5 3.8 5.4
##  [631] 4.9 5.1 4.5 5.9 4.9 5.4 6.1 4.5 4.3 5.4 5.1 2.8 3.6 5.2 4.7 2.6 4.6 4.9
##  [649] 3.7 5.5 5.5 3.2 4.6 3.9 5.0 3.2 4.8 3.7 4.6 4.2 4.1 5.3 4.4 4.6 3.8 3.2
##  [667] 5.3 5.3 4.4 3.5 4.4 3.6 5.3 4.6 5.7 5.0 5.3 3.8 6.1 4.4 5.0 3.7 5.1 5.2
##  [685] 6.6 4.9 3.4 6.8 4.4 3.8 4.0 5.6 4.6 5.6 4.2 6.7 4.2 4.8 4.2 2.8 4.3 2.6
##  [703] 3.5 3.4 6.0 6.0 3.9 5.4 4.1 3.2 5.5 3.7 5.8 6.0 5.2 4.5 5.0 3.5 5.2 4.1
##  [721] 4.2 4.4 3.2 3.9 4.3 4.4 4.2 5.2 4.6 4.6 6.0 5.0 4.5 3.3 4.2 5.2 6.7 6.3
##  [739] 3.8 5.2 4.0 4.4 4.5 4.2 4.8 3.9 4.3 5.5 3.7 3.7 4.6 4.1 3.4 4.9 3.6 5.1
##  [757] 4.7 5.9 4.7 4.7 4.3 5.0 4.1 4.8 6.2 5.6 3.4 3.9 3.9 4.3 5.9 4.7 4.0 4.0
##  [775] 7.6 3.7 3.7 3.0 5.3 6.3 5.2 4.5 5.0 4.9 4.1 4.5 5.3 5.4 5.4 4.3 5.2 6.5
##  [793] 4.4 4.0 3.6 2.3 3.7 4.9 5.1 4.4 3.3 4.3 4.0 4.8 4.2 5.3 3.6 6.3 5.4 3.8
##  [811] 6.2 5.8 4.2 4.9 4.7 6.3 3.2 3.9 5.1 4.9 4.2 5.1 4.5 4.0 4.4 6.2 4.9 5.0
##  [829] 6.2 3.9 4.8 3.8 4.6 5.3 4.8 4.9 4.5 5.7 4.9 4.3 5.0 4.0 5.1 4.3 4.2 4.7
##  [847] 5.1 6.2 4.7 4.0 5.9 4.1 3.4 5.6 4.2 4.4 3.1 5.6 3.6 3.8 3.3 5.5 3.6 3.4
##  [865] 4.3 3.6 4.7 3.3 3.8 3.5 4.7 3.9 4.4 5.6 3.8 5.2 6.2 4.1 6.0 4.8 4.9 5.6
##  [883] 4.9 4.4 4.8 5.9 4.6 5.1 4.6 3.8 4.6 2.8 4.0 5.7 5.6 3.9 4.7 4.8 5.4 3.6
##  [901] 4.1 6.8 3.8 4.2 5.4 6.1 4.4 4.2 4.3 3.5 3.7 4.0 4.4 5.2 5.7 5.6 4.8 4.0
##  [919] 4.1 4.5 6.1 3.4 5.2 3.9 5.6 4.1 6.0 4.0 5.1 3.8 3.3 4.3 4.5 3.8 4.3 2.6
##  [937] 4.8 2.9 5.4 3.8 4.7 4.2 4.5 5.0 5.0 4.6 4.4 5.7 4.9 4.5 4.8 4.3 6.0 4.7
##  [955] 4.3 3.3 6.0 3.6 4.3 4.7 4.7 4.5 3.8 5.0 5.3 4.7 4.6 4.6 5.3 4.4 4.8 5.1
##  [973] 5.9 4.0 3.0 3.3 2.3 5.2 5.3 3.8 3.3 2.7 4.9 4.5 3.5 4.9 4.1 4.1 3.9 4.0
##  [991] 4.9 5.4 5.8 4.9 5.3 3.9 4.8 5.0 2.6 5.4
## 
## $func.thetastar
##   72% 
## 5.028 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.400 5.200 5.100 4.900 4.900 4.800 4.624 4.420
## 
## $jack.boot.se
## [1] 1.018155
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
## [1] 1.648047
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
##   4.338224   6.966585 
##  (1.870298) (3.184240)
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
## [1]  1.6350628  0.1618364  0.2263381 -0.7026499 -0.8142039  0.9339388
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
##    [1]  2.002137408  0.135016584 -0.845005406 -0.830459179 -0.303714605
##    [6]  1.265182900 -0.880348575  1.247652854 -0.820821061  1.080524783
##   [11]  1.586893593  0.077598779  0.799736156  1.627538564  1.383563743
##   [16]  1.011543294  1.745373381 -0.861648941  1.581847155  1.318128235
##   [21] -0.354953843  0.286218637 -0.369158993 -0.045502124  1.881374712
##   [26]  2.277625591  1.197664875  1.067159201  0.210621513 -0.683903082
##   [31]  0.815820051  1.378329130  1.638706751  2.227289427 -0.341608554
##   [36] -0.613765691 -0.269408332  0.615026545  0.987110243  1.126679823
##   [41] -0.160484296  0.789063278  1.755034511  2.395723087  1.680641473
##   [46]  0.327819577  1.615287124 -0.481970792  2.025867630  1.242889555
##   [51]  1.875402832  0.431704562  1.640875216 -0.543800416  1.250265114
##   [56]  0.925468056  1.102828031  1.821607977 -0.220557339  1.161126966
##   [61] -0.168993947  0.305013946  1.638573187  2.131302413 -0.191602182
##   [66]  1.170794291 -0.031217265 -0.304853803 -0.102067471  1.302793789
##   [71]  0.541036851  1.450559392  1.024235457 -1.330117303  2.135465919
##   [76]  1.601364819  0.960321002  1.141869959  0.149345195  2.343439606
##   [81]  1.501359960 -0.596131441  0.667415408  0.699027633 -0.415536784
##   [86]  0.464167855  1.397926936  0.552770051  1.489465707  1.071650891
##   [91]  1.819815434 -0.204954925  1.492992012  1.249883104  0.198040958
##   [96] -0.805963013 -0.617797750  0.992729292  1.220191302  1.850753305
##  [101] -0.854193161  1.078115144 -0.248515209  1.227514340  2.272976828
##  [106]  1.244496425  1.461510795  1.655731616 -0.351661523  1.212151930
##  [111]  1.945246678  1.520245063  0.451135513  1.012300234  1.176323534
##  [116]  0.732132027  1.511303120 -0.828931368  0.506990217  1.669623925
##  [121]  1.296474852  1.679877661  1.917999299  0.544059053  1.236499285
##  [126]  1.624369103  0.442245679  2.052633541 -0.211259251 -0.896926783
##  [131]  1.239645696  1.146924657  0.918259011  0.666591692  0.803697736
##  [136] -0.632761733  2.344256523  1.405287500  2.016794064  2.422824207
##  [141]  2.063013044  1.451522195  0.793625000  0.928935214  2.081027447
##  [146]  1.683937182 -0.242917548  1.532594418  0.749211665  1.250268757
##  [151] -1.329143164 -0.585372166 -0.557273970  1.484915171  2.238289269
##  [156]  0.142503369 -0.269161914  2.327425433 -1.061601582  0.932701084
##  [161]  0.158861091  1.067167830  0.277683713  1.184347314 -0.466164572
##  [166]  0.687617658  1.255128614 -0.016986310  1.098562433  0.362255404
##  [171] -1.204790947  0.089570642  1.447817866  1.223649609  0.257769523
##  [176] -0.232506462  2.297158879  0.613210934  0.665202284 -0.349395388
##  [181]  0.600922182 -0.044489388  2.101055851  1.970867627  1.012110514
##  [186]  0.177522518 -0.043898135  1.805139500  1.924843959  0.928173769
##  [191]  0.151806875 -0.388003535  2.343195590  1.788048287  1.444510578
##  [196]  1.950165606 -0.578778454  2.256386951  0.733645800 -0.150430693
##  [201]  0.167396556  0.363079667  2.414179116 -0.748736821  0.606497493
##  [206]  2.313188108 -1.089660946  1.489505399 -1.444635597  2.272485068
##  [211]  1.707005368 -0.651273836  1.936282803  2.373804987  0.510766883
##  [216] -0.112248890  1.486637931  0.986140041  0.290074804 -0.312419316
##  [221] -1.173617045 -0.100499164  1.462750595  0.858765172  1.063874967
##  [226]  0.941003634  1.635131594  1.774690132  0.133482324  1.940621785
##  [231]  0.745321204  2.052861313  0.353948978  1.428069723  2.276036507
##  [236]  1.909718487  1.174953101 -0.757010437 -1.430670337 -1.454743377
##  [241]  1.795262802 -0.716638546  1.373842606  1.138006820 -0.563412296
##  [246]  0.922639712  1.144372761  1.394216384  1.262668348  1.212111389
##  [251]  1.904204390  1.154976937  2.083539391 -0.315156691  0.447867512
##  [256]  1.471876221  0.971578813  1.918047760  1.780345240  1.437196291
##  [261]  1.085708199  0.149981424  2.014579187  2.329323432  0.842821220
##  [266]  1.572171638  0.751959548  1.512468983  2.125774431 -0.042739778
##  [271] -0.424250282  1.013512132 -0.876235056  1.158583770  0.030292404
##  [276]  2.076097129  0.601357543  0.632492374  0.814879450 -0.287984893
##  [281]  1.945492014  1.054124154  1.776614834  0.051605764  0.649178144
##  [286] -0.233711090  1.689340966  0.534645811  0.486663083  0.559784533
##  [291] -0.307742959  2.380861607  1.000193566  0.432818789  1.491999671
##  [296]  2.147984589  1.222102747  1.662199400  0.703317484  1.319689191
##  [301] -0.004516744  1.621690263  1.188935731 -0.578715255  1.755034511
##  [306]  1.589808085  2.338766666  2.048120035 -1.063528154 -0.602987287
##  [311]  0.202453512  0.991806986  1.076273370 -0.044718392  0.742021502
##  [316]  1.220191302 -0.887730800  1.359219388  0.206711430 -0.657852497
##  [321]  0.140199797  1.212745833  1.980011942  1.662829364  2.126700249
##  [326]  0.545663960  1.613068761  1.615099172  1.103347331  1.410688423
##  [331] -0.759932663  0.015211644  0.817659869  0.052500903  1.611159001
##  [336] -0.355995911  1.196975486 -0.817895704 -0.144754601  2.053436399
##  [341]  1.496763247  0.495682428  1.454718233  2.214722960  0.714072857
##  [346]  0.938419268  2.261346293  0.375804842  0.805415379 -0.644549532
##  [351]  1.705248994  1.584821590 -0.223370881  1.134709813  1.188223078
##  [356] -1.073483965  1.727105646  0.291625538 -0.523401981 -0.517278965
##  [361]  0.804389005 -0.486142502  0.114821708  2.110207975  0.687357348
##  [366]  0.822362348 -0.278926484  1.935208556 -0.224001064  0.646807788
##  [371] -0.225413582 -0.826044929  0.795761264  1.234122080  2.320615328
##  [376] -0.040985633  1.358749081  1.254287607 -0.700514832  0.432878217
##  [381]  1.820098938 -0.939602017  0.917101339  1.640036342  1.119343126
##  [386]  1.631843359 -0.229107364  2.130130950  0.294641904  0.730361470
##  [391]  2.021950068  1.623037969 -0.596701211  1.776760671  1.838173480
##  [396]  1.621571902 -0.511997597  0.775641781  1.013129630  2.303503362
##  [401] -0.706337569 -0.574653584  0.835842758  2.302834251  0.418709569
##  [406]  1.483905447  1.910754156  1.263369761  1.394216384  0.337120558
##  [411]  0.729470808 -0.072230870  1.242684762  1.065926253  1.326919614
##  [416]  2.319250636  0.338800950  1.425534219  1.236499285  0.798674323
##  [421]  0.995887225  1.008148079  1.227122206 -0.090253143  1.598474664
##  [426] -0.052186008 -1.181834742  1.431210247 -0.570421775  1.665647598
##  [431] -0.369126646 -0.587466157  0.507147733  1.144340470 -0.547212280
##  [436]  0.289851004  0.814955499 -0.018127071  2.102243183  1.984538847
##  [441]  0.974765930  0.642901737  1.023722557 -0.366318131  1.788586745
##  [446]  1.498194429 -1.118407371  1.390727461  1.687054838  2.217091076
##  [451]  0.675889844  2.063258544  2.120840381  1.837845598  2.062374399
##  [456]  0.699651108  2.210144815  0.273121506 -1.043569486  1.150226532
##  [461] -0.158830091 -0.366136010 -0.258180499  1.246451738  0.850624438
##  [466]  0.452523555  2.312013736  0.351187228  0.858776318  2.073040110
##  [471]  0.053902190  0.871547773  1.328769569 -0.127726608  0.819100334
##  [476]  0.267512516 -0.151299535  1.927576825 -0.561482198  0.954552177
##  [481]  1.679882175  2.252593408  1.982819269  0.564226737  1.404129699
##  [486]  1.325458758  1.427141871  1.914523011 -0.221250610  2.048120035
##  [491]  2.055701364  0.892104240  0.773328348  1.044452586 -0.461349592
##  [496]  1.444645570  0.034237682  1.641440185  1.021752049  1.732003615
##  [501]  1.221836023 -0.631512027  1.428482824  1.773796899  1.304614601
##  [506]  1.680095943  1.895019842 -0.298820695 -0.713164588  1.472161993
##  [511]  2.023912321  1.343409246  0.722619393 -0.394737657  1.082903518
##  [516]  1.155526624  0.196889353 -1.129223036  1.396352859 -1.218799843
##  [521]  1.671733543  1.898899448  1.304896030  0.803389085  1.396682687
##  [526]  1.741862608 -0.351114675  1.950829964 -0.845243002  1.476842953
##  [531] -0.386855602  1.622100110  0.359900104  0.769073712  1.268591551
##  [536]  1.409142631  2.635077606  1.779754731 -1.980813398  1.250857139
##  [541]  0.588645255  1.055848737  0.346721349 -0.658579838 -0.207211777
##  [546] -1.243081885  0.132131794 -0.353354516 -0.846031488  1.116459112
##  [551]  1.405562123  1.570484070  2.045041121  1.937281122  1.490836246
##  [556]  1.312421487 -1.156464103 -0.812338430  1.281484535  0.556073408
##  [561]  1.023999125  1.656726727  1.404256965 -0.307690435 -0.270291782
##  [566]  1.196843853  2.008362860  2.361334066  1.414032577  1.689392273
##  [571]  2.304204260  1.611535353  1.649092382  1.403007577  0.875430397
##  [576] -0.724600734  2.058957000  0.054549774 -0.186941132  1.910000382
##  [581]  1.535419124  1.868627252  1.045558384 -0.681360131  1.901001227
##  [586]  1.568030023 -0.609250170  0.916169125  1.368290535  1.484417353
##  [591]  1.103133487  0.036197510  1.755486011 -0.632263487  0.161122667
##  [596]  0.871547773 -0.498081714  1.727207290  2.179930003  1.837730593
##  [601]  2.003504783  1.705704837 -0.744210339  1.236778912  2.075096026
##  [606]  0.091064225  0.644260886  0.658261559  0.040141559  1.951681772
##  [611] -1.271112623 -0.506770884  2.248191684  1.299258950  0.519384013
##  [616] -0.766707195  1.394943418  1.766697654  1.743021886 -0.682202741
##  [621]  0.788731932 -0.490510465 -0.226986796 -0.588469767 -0.114616289
##  [626] -0.123503390  0.110713032  1.065696376  1.874089317  1.410920780
##  [631]  0.175565795  1.895499808  1.621066135  1.796676107  1.028147511
##  [636]  0.994216863  2.001802539  2.413698002  1.687045868 -1.163110026
##  [641]  1.488718488  1.127794174  1.260153413  0.272345188 -0.543809697
##  [646]  1.571379528  0.323727849  0.654474709  0.510526363  1.649526898
##  [651] -0.261149877 -0.509111288 -0.125169189  1.103987936  0.815513915
##  [656]  2.402971863  1.703821339  0.939610804  1.142384292  0.182694667
##  [661]  1.867645593 -0.509344491  1.931601977  0.627534914  1.041841341
##  [666]  0.701545826  1.402515421 -0.393249459  0.264813887  1.584193019
##  [671] -0.081731178  1.171912995  1.700853501  1.999928687  0.196586237
##  [676]  1.000131606  1.774144806  1.255042665  2.124692138  1.678921230
##  [681]  1.082627567  1.282845121  0.956477738  2.004876116  1.647894365
##  [686] -1.037875919 -0.765510819  1.752918094  1.445187826  0.295886108
##  [691]  1.221732374 -0.721441393  1.953522877  2.107091008  1.850245601
##  [696]  1.975916202  1.561155567  2.246597964  2.030322149  1.092041486
##  [701]  2.048211785 -0.522789275  1.798549074  1.854374567  1.732533035
##  [706]  1.588994837 -0.170527878  1.257462004  1.656081329  1.945271059
##  [711]  1.234905555 -0.143722274  1.211269786  1.281745509  1.386138052
##  [716] -0.629538393  1.818129949  1.892435831 -0.175667607  1.325661391
##  [721]  2.265500128 -0.849345930  1.356751212  1.289078299  1.414670756
##  [726]  1.934799462  0.262456730 -0.078985422  0.901334745  2.394882949
##  [731] -0.120467710  1.689630277  1.808840808  2.054287249  1.783479520
##  [736]  1.119782437  0.009347500 -0.366154885  1.252341849  1.584545370
##  [741]  1.278607827 -0.110711593  1.648046970  2.067616852  0.940632192
##  [746] -0.822364968 -0.517142281  1.602920988  1.164745517  2.149413114
##  [751]  1.535948461 -1.063920177  0.595001699  1.114376899  1.772072760
##  [756]  0.703264918 -1.325951415  1.088557661 -0.172391099 -0.689377247
##  [761] -0.153863710  1.304426613  1.620573034  2.113092392  1.115820189
##  [766]  0.899495544  0.808363458  2.346485218 -0.498195880 -0.028997961
##  [771]  1.041337729  2.012984984  1.561476209  0.177950220  0.150090125
##  [776]  0.034409201 -0.339004637  0.004122280  0.759758654  1.984939451
##  [781]  1.690929983  1.887093832  1.466971506 -1.041312778  0.465333965
##  [786]  0.356852347  1.776204806 -0.200333755  1.826732378  0.720862843
##  [791]  2.251833679 -0.508069591  1.384338610 -0.589061278  1.674943311
##  [796] -0.150430693  1.538736154  0.818526633  1.882669135 -0.044534015
##  [801]  1.213253224 -0.880961861  0.472393324  0.354283682  1.636509029
##  [806]  1.934652491 -0.808390643 -0.166329973  0.725947848  0.419165052
##  [811]  1.755800847 -0.825037839 -0.987143638  2.125861554  2.091495759
##  [816]  2.031060764  1.656113153  0.384353187  1.225202625  1.651028609
##  [821]  0.292965369  1.054814633  0.200428989 -0.320907173 -0.366998988
##  [826]  1.450910703  1.450482504  1.690652506  0.942026226  2.012555274
##  [831]  0.327522197  0.677341887  0.906675854 -0.038115916  1.155543392
##  [836]  1.253709845  1.064149027  2.345074906  1.525758248  0.993906224
##  [841]  1.803466825  2.345989807  1.064132121  1.161069857  1.027908828
##  [846]  1.232470122  1.867389537  2.307313505  1.188318883  1.782669431
##  [851]  1.679251291  0.291808540  0.460867924  1.016784919 -0.344651424
##  [856]  2.094724117  1.601406610  1.519358403  1.925660479  2.351423467
##  [861] -0.796902551  1.070670772 -0.529920282  0.545210446  1.162406726
##  [866]  1.882098347  1.195113494  2.059428903  1.689843175  0.364084582
##  [871] -1.566722733  1.233763357  0.623978767 -0.491628659  2.061808020
##  [876]  0.489022608  0.698536152  0.515300378  0.821503075  2.204035267
##  [881]  0.528515394  1.079931002  1.823658125  1.048440040  1.295971751
##  [886]  1.689392273  0.355227933  1.640875216 -0.241956023  1.275195200
##  [891]  1.793426693  1.343537164  0.106820372  1.272499207  1.846607869
##  [896]  1.190306777  1.150840356  0.882718717  1.365774062  1.084411145
##  [901]  1.489600738  1.065433203  1.006478732  1.467455332  2.300266344
##  [906]  1.066087463  1.670445208 -0.866479192 -0.745692844  1.432537609
##  [911]  0.518774541  1.278766446  2.086912607  0.589454421  2.006999388
##  [916]  1.108927911  0.370689320  2.276036507  2.000999842  0.737927253
##  [921]  1.478331477  1.253823467  1.175724185 -0.538987202 -0.345990188
##  [926] -0.381120562 -0.551886304  0.296353437  1.268591551  1.343774469
##  [931]  0.960817771  2.081578281 -0.350021263  0.373516646  0.078210845
##  [936]  1.822462429  1.064223386  1.178186975  0.194814387 -1.353719255
##  [941]  1.194453015  2.001802539  2.185253835  0.715457625  2.454389136
##  [946] -0.089080931  0.955220962  1.126874524  1.898778255  1.012222633
##  [951]  2.329091783  1.156810265 -0.701239430 -0.610282355  1.673814848
##  [956] -0.364187056  1.373468543  1.665748799 -0.182216460  1.476196840
##  [961]  0.281084128  2.310976968  0.944132014  1.438599692 -0.384052791
##  [966] -0.153733840  1.846071487 -0.183826087  1.730310494  1.976047517
##  [971]  1.562173448 -0.726870456 -0.639593804  1.839907081 -0.651109968
##  [976]  1.243148132 -0.032586574  0.833477759 -0.373590073 -0.377838939
##  [981] -0.762919129  1.324630971  1.220646241 -0.227363856  2.080908417
##  [986]  0.833037498 -0.116819691  1.984158063 -0.389499102 -0.845017576
##  [991]  1.081330647  1.360035151 -0.664196634  1.170694422  0.820583361
##  [996]  2.167945093  1.409957426  1.933835648  0.928667074 -1.156269538
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
##   0.62271488   0.33132460 
##  (0.10477404) (0.07408276)
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
## [1]  0.6974397 -1.9874781 -0.1278136 -0.0016019 -0.3031284  0.3184975
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
## [1] -0.0159
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9036004
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
## t1*      4.5 0.01491491   0.9264688
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 5 6 7 
## 2 1 1 3 1 1 1
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
## [1] -0.0179
```

```r
se.boot
```

```
## [1] 0.8713603
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

