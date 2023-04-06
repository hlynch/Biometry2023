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
## 0 1 2 3 4 7 8 
## 2 1 2 1 1 2 1
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
## [1] 0.0402
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
## [1] 2.774442
```

```r
UL.boot
```

```
## [1] 6.305958
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
##    [1] 4.8 4.7 5.6 3.3 3.4 3.5 5.9 4.5 4.2 5.8 4.2 4.3 4.0 3.6 3.4 6.5 4.5 5.4
##   [19] 4.2 3.5 6.4 4.7 3.3 4.8 3.5 4.4 4.1 3.6 3.4 5.1 4.4 6.3 5.6 3.6 3.3 2.3
##   [37] 3.7 4.7 5.7 3.9 5.1 3.1 1.5 4.2 4.6 5.8 4.3 5.1 4.6 4.1 4.9 3.0 5.0 5.6
##   [55] 5.9 4.0 4.0 4.5 6.2 4.4 5.3 4.7 3.9 5.2 5.0 4.3 3.9 4.9 4.6 3.4 4.5 5.7
##   [73] 5.3 3.4 3.6 3.7 3.3 5.1 3.7 4.9 5.5 3.4 5.2 4.6 5.5 3.4 4.6 2.5 5.0 3.6
##   [91] 5.0 4.3 5.1 3.2 5.5 4.5 3.6 5.1 4.1 5.2 5.2 4.5 5.0 3.8 5.7 5.6 5.3 5.8
##  [109] 4.2 5.3 5.5 4.9 5.7 5.2 5.8 4.0 4.9 5.0 4.4 4.3 5.2 5.1 4.6 5.2 5.4 4.5
##  [127] 4.9 5.1 3.8 5.0 5.5 5.0 5.4 2.8 4.1 4.1 4.5 3.9 4.8 4.7 4.6 3.7 3.9 5.0
##  [145] 3.6 3.6 4.6 4.4 5.0 4.8 5.6 2.6 4.1 5.1 5.0 3.4 5.0 4.7 4.2 4.4 5.9 5.0
##  [163] 5.3 3.9 5.2 4.0 4.2 4.4 2.4 5.5 4.7 6.1 4.5 3.5 5.4 3.2 2.7 3.9 3.6 5.7
##  [181] 4.9 5.7 3.4 3.5 4.5 3.4 3.9 5.1 5.5 2.3 4.0 6.0 4.8 4.3 3.3 4.6 4.8 4.8
##  [199] 5.2 4.9 4.4 4.3 3.7 4.1 5.5 3.9 5.4 4.2 3.8 4.1 3.5 3.6 4.0 5.1 3.4 3.2
##  [217] 5.5 3.8 3.1 5.2 5.0 6.1 5.3 4.3 4.1 2.5 3.3 4.1 4.0 4.9 6.4 5.3 3.6 3.0
##  [235] 4.9 4.3 4.9 4.6 4.1 3.3 3.1 4.1 5.5 5.5 4.3 3.7 3.1 4.2 4.7 2.6 4.5 5.9
##  [253] 3.5 5.0 4.8 4.7 3.2 5.1 4.8 4.0 5.1 3.6 5.1 4.4 3.9 3.7 5.0 5.4 4.3 4.9
##  [271] 4.6 4.9 5.9 5.8 4.1 3.0 5.3 5.4 3.4 4.6 5.2 5.5 4.4 4.8 5.7 5.4 3.9 2.7
##  [289] 5.0 3.5 4.9 5.4 4.1 3.4 4.3 3.0 4.7 4.2 6.6 4.4 4.2 4.8 4.3 4.3 3.9 4.7
##  [307] 5.8 6.1 5.3 4.6 3.0 4.7 4.6 4.3 5.3 3.4 5.8 4.4 3.0 5.0 3.6 5.1 4.1 3.9
##  [325] 4.0 5.4 4.9 4.6 5.1 4.5 5.4 5.1 5.4 4.5 3.4 4.6 4.3 4.0 4.7 4.0 4.3 4.5
##  [343] 4.1 4.0 4.6 6.0 3.8 5.4 5.2 5.4 6.5 5.8 5.9 4.3 4.0 4.2 7.1 3.9 3.7 3.0
##  [361] 3.6 6.1 3.0 2.9 4.7 4.2 5.2 3.3 4.2 5.5 2.6 5.3 3.7 4.0 4.1 4.6 5.8 3.6
##  [379] 3.7 4.6 5.8 4.8 4.1 4.0 5.0 4.5 4.7 4.7 6.0 5.2 6.0 4.8 4.2 4.2 5.3 3.0
##  [397] 4.3 3.7 5.8 5.3 4.3 5.9 2.2 4.9 3.6 3.8 5.6 5.3 3.4 5.2 5.0 5.9 4.5 5.5
##  [415] 4.8 5.9 4.6 5.7 3.8 4.2 4.4 4.4 3.2 5.8 3.6 4.1 3.8 4.6 4.4 4.6 3.8 4.8
##  [433] 4.7 4.4 4.2 3.6 3.9 2.2 4.0 4.9 4.3 5.5 5.5 3.7 5.6 5.1 3.4 5.0 6.1 5.0
##  [451] 4.2 5.4 5.0 4.6 4.3 5.9 2.2 4.0 3.2 3.7 3.1 4.9 4.9 4.0 4.4 5.0 6.0 6.0
##  [469] 3.2 2.4 5.6 5.0 3.4 2.3 3.9 4.7 5.6 5.2 5.4 4.7 4.1 3.5 4.7 3.1 5.7 3.2
##  [487] 5.2 4.0 3.4 5.8 4.6 4.7 6.0 4.3 4.4 6.6 3.4 4.1 4.6 3.0 4.6 5.6 5.0 4.8
##  [505] 3.9 5.8 5.2 3.7 3.5 4.8 4.0 5.6 3.6 5.2 4.9 3.1 4.2 4.6 4.4 4.4 4.3 5.3
##  [523] 6.1 4.2 6.2 3.6 3.4 5.0 3.0 4.3 3.0 5.2 4.8 5.2 4.2 4.1 1.8 4.3 4.8 5.3
##  [541] 5.1 5.5 4.5 4.6 4.1 4.6 4.6 5.2 4.7 4.5 4.0 4.1 3.1 2.6 5.2 4.5 4.3 5.3
##  [559] 3.9 3.6 5.7 7.7 4.2 4.2 5.7 4.5 2.6 3.5 4.8 3.9 4.7 5.3 4.8 3.9 4.3 3.5
##  [577] 4.2 3.4 5.6 2.4 3.5 3.1 3.1 4.6 5.1 5.1 4.1 2.3 5.7 5.2 7.3 4.1 4.0 4.7
##  [595] 4.5 3.5 3.4 6.2 4.1 4.5 4.3 4.7 2.7 3.4 4.7 5.5 3.9 4.2 3.6 3.3 3.9 4.9
##  [613] 3.4 4.6 4.0 4.6 4.3 3.7 4.9 4.6 6.1 3.1 4.2 5.1 3.7 5.3 4.3 3.2 4.9 4.3
##  [631] 4.3 3.9 4.7 3.8 4.6 3.9 3.3 3.9 3.0 5.7 5.6 4.3 4.7 6.5 4.4 5.0 4.8 3.5
##  [649] 4.1 4.5 4.8 3.6 5.5 4.4 4.7 5.4 6.3 4.9 5.9 4.5 4.2 5.7 3.8 2.2 4.6 5.3
##  [667] 5.1 5.6 4.4 4.1 5.7 4.0 3.9 4.6 5.3 4.2 3.8 5.9 3.9 2.3 5.6 2.9 5.4 4.6
##  [685] 4.7 5.2 3.0 4.2 2.2 4.6 5.1 5.6 5.1 4.1 5.4 4.9 5.4 4.4 4.3 5.0 4.9 2.6
##  [703] 4.5 4.3 3.5 4.7 2.4 3.0 3.4 5.7 5.2 2.6 4.1 5.3 4.7 3.4 3.9 5.2 2.6 4.1
##  [721] 3.1 4.9 1.8 4.6 3.7 5.2 7.2 4.8 3.9 5.1 2.7 4.8 2.9 4.4 3.8 3.9 5.7 3.3
##  [739] 2.0 2.9 5.0 4.9 4.1 3.4 4.7 5.1 4.3 3.2 5.1 4.9 6.1 4.8 3.9 2.8 3.9 4.5
##  [757] 4.4 4.0 5.6 3.0 4.4 4.4 4.2 3.5 3.7 3.1 5.7 4.0 4.2 3.5 5.3 2.6 4.6 3.3
##  [775] 1.8 3.9 3.7 5.8 4.2 3.1 5.7 4.3 5.1 4.6 4.1 2.2 5.4 5.3 6.2 4.1 6.8 4.1
##  [793] 6.0 4.6 4.0 4.3 4.2 4.5 4.6 4.0 5.8 4.1 4.3 5.0 3.9 3.0 5.2 4.6 3.3 4.3
##  [811] 5.3 5.1 4.4 5.1 4.9 4.9 4.1 3.4 4.7 4.9 5.7 4.6 6.1 3.5 3.8 2.6 3.6 4.0
##  [829] 2.7 3.8 4.4 5.7 4.8 4.6 4.8 5.2 4.3 3.7 4.6 3.7 4.4 4.8 4.6 5.1 5.4 5.6
##  [847] 4.4 5.8 4.6 4.4 3.8 2.8 5.9 4.7 4.7 5.0 3.8 3.1 5.0 4.9 3.7 5.0 4.7 2.3
##  [865] 4.2 5.8 5.7 4.2 5.5 2.7 6.5 4.9 3.3 4.5 4.6 4.6 5.3 3.5 6.7 5.2 4.3 3.4
##  [883] 5.3 4.2 4.0 5.1 4.9 5.0 4.4 4.4 5.1 5.0 3.7 4.4 4.9 4.1 4.4 5.3 2.2 5.1
##  [901] 3.7 5.0 3.7 3.4 4.2 3.4 5.0 4.4 5.4 3.2 3.4 5.4 3.9 3.3 6.0 3.8 4.7 3.2
##  [919] 3.9 4.7 4.8 3.3 3.1 5.6 4.7 5.5 4.0 3.9 3.6 4.9 4.1 5.5 4.8 5.3 4.5 6.4
##  [937] 2.5 6.1 4.0 4.9 4.4 5.1 6.1 5.7 4.0 4.0 5.4 5.2 5.5 4.3 5.0 4.7 4.4 4.8
##  [955] 3.3 4.0 4.9 3.3 5.4 5.0 5.2 3.5 4.3 4.6 5.2 5.0 2.6 3.8 4.1 3.8 4.3 4.2
##  [973] 5.4 5.0 4.9 3.6 4.1 4.7 5.5 4.7 3.8 5.1 3.5 2.8 3.3 5.2 3.7 3.2 4.8 3.7
##  [991] 4.1 4.8 3.3 5.9 4.7 2.4 4.8 4.1 3.5 5.2
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
##   2.5   6.1
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
##    [1] 4.5 1.9 4.8 4.4 4.5 4.5 4.6 2.9 5.6 5.3 4.2 3.5 4.7 4.0 4.6 2.9 4.8 2.8
##   [19] 2.5 4.7 5.0 5.9 4.0 5.5 4.7 3.6 3.1 2.7 4.1 4.1 5.1 5.7 4.8 4.6 4.9 3.9
##   [37] 5.3 5.1 5.3 2.7 6.8 5.0 4.5 4.4 4.6 4.5 3.5 4.5 5.3 3.6 5.2 5.3 5.0 3.6
##   [55] 4.1 2.8 3.9 5.4 3.8 5.7 4.2 3.3 3.8 5.0 3.8 3.5 5.7 5.1 4.0 5.7 4.4 4.3
##   [73] 3.6 5.3 4.9 5.2 4.6 5.0 5.5 3.4 2.3 4.6 3.9 4.6 5.4 6.7 4.9 6.1 3.2 5.1
##   [91] 4.3 5.4 3.2 5.9 4.0 4.4 4.6 2.8 4.5 4.5 6.4 4.6 4.6 4.8 4.8 4.2 3.7 4.2
##  [109] 4.7 3.2 4.0 5.1 3.4 4.7 4.8 4.9 4.5 3.3 5.0 2.4 6.1 4.5 4.3 5.2 5.1 3.3
##  [127] 3.8 2.2 5.5 4.5 4.9 3.2 5.0 4.6 4.7 6.6 4.4 4.7 5.2 4.9 5.0 4.6 6.8 6.5
##  [145] 4.0 6.7 4.7 4.8 6.8 4.6 4.3 4.1 4.8 3.6 3.4 3.8 5.0 3.8 4.7 4.4 4.8 4.4
##  [163] 5.2 4.4 4.3 3.1 3.9 3.9 6.1 3.9 4.1 2.8 4.9 4.2 5.9 3.6 4.6 5.2 3.4 5.8
##  [181] 2.8 4.3 4.1 4.3 4.6 4.4 4.3 4.2 4.1 5.3 3.7 3.4 4.3 3.1 4.9 3.8 2.6 5.6
##  [199] 4.7 6.6 4.8 4.4 4.8 2.1 4.2 3.0 4.4 4.4 5.9 4.0 5.4 4.1 4.5 3.4 4.8 5.3
##  [217] 5.2 4.5 4.2 2.9 6.0 4.1 4.3 3.7 4.9 3.0 3.7 4.5 4.3 3.9 3.9 4.7 5.8 5.2
##  [235] 3.3 4.3 2.7 3.2 5.0 5.9 5.6 4.3 5.8 4.3 4.0 5.6 4.4 4.3 6.2 5.0 4.6 5.3
##  [253] 4.0 4.0 5.3 5.8 3.0 3.0 2.6 4.3 3.6 4.8 5.1 4.8 3.5 4.2 3.5 4.1 3.4 4.8
##  [271] 3.6 3.3 5.2 4.0 5.5 5.6 3.8 3.9 4.3 4.2 4.9 3.4 3.6 5.2 2.9 3.6 5.4 4.4
##  [289] 3.3 4.7 4.8 3.5 2.9 3.7 5.1 3.5 4.8 6.1 5.1 2.9 5.2 4.2 4.1 5.4 4.3 3.5
##  [307] 4.2 3.9 3.6 2.6 4.3 4.6 3.7 4.7 5.5 5.1 4.7 4.0 6.1 4.0 4.7 2.6 4.5 5.6
##  [325] 3.5 4.0 5.8 5.8 4.8 5.0 4.7 4.3 2.6 5.4 3.8 4.6 3.7 4.7 5.0 4.4 5.2 4.6
##  [343] 4.5 5.9 4.5 3.7 4.0 3.9 3.1 3.7 4.5 3.2 3.1 4.4 4.4 4.5 5.0 5.5 2.7 3.1
##  [361] 5.9 5.4 4.5 4.5 4.2 2.9 3.7 4.4 3.1 4.9 3.5 5.6 4.2 4.4 4.6 3.1 4.1 4.4
##  [379] 3.3 3.1 4.4 4.5 3.0 4.9 6.6 3.0 4.2 6.2 5.1 4.4 3.7 5.6 4.3 4.4 5.6 4.4
##  [397] 3.8 4.5 3.8 5.0 3.6 3.1 5.6 4.6 6.1 3.9 5.3 4.7 4.0 6.0 7.2 4.5 3.7 5.5
##  [415] 2.4 5.0 4.5 4.7 6.1 3.7 5.2 5.3 5.3 6.0 2.5 4.5 4.7 4.9 3.8 4.6 3.2 3.2
##  [433] 4.8 1.9 3.6 3.8 5.5 2.8 2.9 4.9 4.6 4.5 6.0 5.3 4.7 3.2 4.3 3.7 5.0 4.5
##  [451] 4.6 4.4 4.7 4.5 4.6 5.8 5.4 5.0 5.6 4.8 4.1 3.8 4.5 3.2 4.4 4.4 4.7 7.3
##  [469] 4.7 4.4 4.7 3.4 3.7 5.3 4.5 3.6 3.5 4.9 5.0 5.4 4.8 4.1 6.6 4.6 5.0 3.0
##  [487] 5.9 5.7 6.1 5.6 3.0 3.8 4.8 4.8 5.9 4.8 3.0 4.8 5.1 5.9 6.4 5.6 4.6 3.8
##  [505] 5.4 4.4 4.9 4.3 3.0 3.8 6.3 4.8 5.3 3.8 4.1 5.0 4.5 3.9 6.0 5.7 5.2 3.7
##  [523] 3.7 4.9 5.2 4.1 5.2 4.5 5.6 4.4 6.2 5.9 4.6 3.8 3.9 2.7 3.4 4.2 5.5 4.7
##  [541] 4.2 4.7 3.2 3.1 6.0 4.2 4.8 4.5 4.7 5.5 6.4 3.7 4.4 4.6 4.8 5.4 5.1 3.6
##  [559] 3.6 6.2 5.1 5.9 5.9 4.9 3.6 4.9 3.9 5.2 5.1 4.7 5.1 4.9 3.8 3.6 3.9 6.1
##  [577] 3.6 4.6 3.6 5.3 2.5 3.9 4.0 4.6 3.7 3.7 4.5 4.1 2.8 5.2 5.5 3.2 4.5 5.2
##  [595] 5.5 5.9 5.4 4.1 2.8 3.9 4.2 5.2 4.4 4.7 4.0 4.0 4.6 5.5 4.1 4.2 5.4 6.0
##  [613] 2.5 5.7 5.1 4.4 5.2 4.5 3.5 4.7 5.2 4.8 3.5 3.5 3.0 5.5 5.8 5.4 4.8 4.4
##  [631] 4.7 4.7 4.5 5.3 3.8 5.9 4.2 4.6 4.6 3.1 3.6 4.5 5.0 5.6 3.5 4.6 4.9 3.8
##  [649] 3.4 3.7 4.5 5.4 5.7 3.3 4.3 5.7 2.7 5.7 3.3 4.2 5.3 4.0 5.0 4.0 4.2 3.1
##  [667] 3.8 4.5 2.2 6.5 4.0 3.4 5.3 4.4 4.0 4.1 2.7 3.7 5.8 5.1 4.3 4.1 4.5 4.8
##  [685] 5.1 5.0 3.0 5.0 2.6 5.1 3.9 4.8 1.6 5.2 5.0 6.3 4.9 2.7 2.5 5.2 5.5 3.4
##  [703] 4.4 4.2 4.1 5.7 4.4 4.0 4.1 3.5 3.3 3.6 4.7 5.8 4.9 4.8 4.8 5.9 4.1 4.5
##  [721] 3.8 2.5 5.8 3.9 3.6 5.1 4.2 5.4 4.5 5.8 5.0 4.1 4.7 3.8 3.5 5.8 4.5 5.3
##  [739] 4.9 4.8 4.7 5.9 4.2 4.1 3.7 6.1 3.1 3.3 4.5 5.1 3.8 5.0 4.6 3.8 4.6 3.6
##  [757] 3.7 3.9 5.1 3.1 4.0 3.9 3.3 5.5 3.3 3.9 5.0 4.4 5.9 4.6 5.8 3.9 5.0 4.9
##  [775] 4.5 4.4 5.6 3.4 5.2 5.7 4.7 5.1 4.8 5.4 3.7 4.9 5.6 3.8 5.4 3.5 3.6 5.0
##  [793] 4.5 4.5 3.5 4.7 3.0 5.1 6.0 4.1 4.0 5.6 6.5 3.3 4.3 5.5 3.8 4.6 3.7 4.8
##  [811] 5.7 5.9 5.1 4.1 3.7 4.3 3.6 4.5 3.9 4.6 4.4 3.8 5.4 4.1 5.2 4.2 4.4 3.7
##  [829] 2.2 5.9 4.3 4.0 4.1 4.7 5.3 4.3 4.7 4.4 6.2 4.4 3.9 3.2 4.0 4.4 4.5 4.3
##  [847] 4.0 6.0 3.7 5.4 3.8 4.1 4.1 4.7 5.1 4.0 6.4 5.2 4.7 4.2 5.8 4.7 3.7 4.6
##  [865] 3.9 5.0 4.4 5.2 4.1 3.8 5.3 3.9 3.7 4.0 4.1 4.3 5.4 5.8 4.1 4.6 4.9 4.9
##  [883] 5.0 4.8 6.6 5.1 4.9 5.1 6.7 3.7 4.4 4.0 4.4 6.6 4.7 5.7 6.8 3.7 4.1 5.3
##  [901] 3.4 4.7 3.8 5.1 4.4 4.1 3.8 4.3 5.4 4.8 4.4 4.9 4.9 5.0 2.3 4.4 2.6 4.3
##  [919] 3.4 4.8 6.2 4.8 4.2 3.9 4.2 5.7 3.6 4.6 4.5 3.8 5.9 4.7 5.0 4.2 5.3 2.9
##  [937] 2.6 6.1 3.4 4.7 5.8 5.7 4.3 4.6 5.5 4.4 4.8 4.7 4.1 2.7 6.1 4.4 6.0 3.8
##  [955] 3.0 5.3 3.8 5.0 2.4 4.3 5.6 3.4 4.0 5.4 6.3 4.0 3.6 4.5 2.9 4.6 4.6 4.4
##  [973] 4.7 4.8 2.8 4.2 4.1 3.3 4.4 4.6 5.0 3.8 3.6 5.2 4.9 4.5 5.8 4.2 3.5 5.4
##  [991] 3.7 3.9 5.3 6.5 5.1 3.2 3.4 4.5 3.9 4.1
## 
## $func.thetastar
## [1] -0.0238
## 
## $jack.boot.val
##  [1]  0.45795455  0.38356164  0.23716814  0.23002915 -0.01419753 -0.07196765
##  [7] -0.17217391 -0.27353760 -0.45427807 -0.53779070
## 
## $jack.boot.se
## [1] 0.9798601
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
##    [1] 4.0 5.9 3.7 5.5 3.2 5.3 4.9 5.1 4.9 4.1 3.7 4.2 5.6 5.1 4.3 4.2 4.5 5.9
##   [19] 4.8 4.1 4.8 5.0 4.1 5.5 4.9 4.9 5.0 5.1 4.7 5.4 5.0 5.4 4.2 4.2 4.0 5.2
##   [37] 2.7 5.6 3.7 4.5 4.0 5.3 4.6 6.0 6.1 4.2 5.1 4.7 4.5 4.3 5.2 4.3 5.9 4.5
##   [55] 3.9 5.6 4.3 5.2 4.2 5.1 5.1 4.1 4.4 3.7 3.9 5.4 4.9 6.0 4.5 3.5 5.6 4.2
##   [73] 3.2 5.6 3.5 4.8 4.6 4.7 3.7 5.0 5.0 4.7 5.4 3.3 3.7 4.3 5.0 5.3 6.3 3.8
##   [91] 4.4 4.2 4.5 3.8 5.0 5.0 3.6 5.7 3.7 3.8 3.2 4.8 6.0 5.1 3.2 3.8 4.5 4.6
##  [109] 4.1 6.8 4.8 5.5 2.7 6.6 4.0 5.9 4.8 4.5 4.6 2.8 5.4 5.1 5.0 4.2 5.2 4.6
##  [127] 4.0 4.4 4.3 4.8 2.7 4.7 6.2 3.5 4.2 5.8 5.8 5.8 5.9 4.6 1.9 5.0 5.9 4.3
##  [145] 5.1 4.0 4.9 3.3 3.1 4.4 5.8 3.6 5.2 5.8 5.0 4.4 4.8 4.7 3.8 5.7 5.4 4.6
##  [163] 4.8 4.4 4.4 4.0 4.9 3.6 3.8 4.2 4.6 5.1 4.2 4.9 3.6 3.4 7.3 4.4 4.6 4.1
##  [181] 3.2 4.7 4.4 3.2 4.8 6.1 6.0 4.1 5.7 3.5 3.2 5.1 4.0 3.4 3.8 6.0 4.6 4.5
##  [199] 4.8 6.2 4.1 5.2 4.0 5.0 2.6 4.3 4.1 5.3 4.3 4.7 5.6 3.9 4.7 4.2 5.7 3.5
##  [217] 4.0 4.3 4.0 5.1 5.1 4.1 3.7 3.5 4.5 3.7 4.0 4.6 4.9 4.9 4.7 3.6 4.5 4.1
##  [235] 4.2 4.3 4.1 3.9 7.0 4.8 5.3 3.5 4.2 4.0 3.2 2.5 2.9 4.6 3.9 4.7 4.9 4.0
##  [253] 3.6 4.7 3.6 4.7 4.0 4.3 3.8 2.3 3.6 2.3 4.9 4.9 3.8 4.8 5.3 4.6 3.6 3.6
##  [271] 6.4 4.4 3.6 5.7 4.6 4.4 5.1 5.4 4.2 4.5 4.9 4.7 5.6 4.2 4.1 5.5 4.1 6.6
##  [289] 5.3 4.7 3.7 4.5 5.2 4.8 5.2 5.2 3.7 6.3 5.5 3.0 2.6 3.5 4.6 3.6 4.3 5.8
##  [307] 5.3 5.2 4.3 2.4 5.6 4.4 4.2 4.2 3.4 4.8 4.6 6.0 5.0 4.5 2.4 5.7 4.8 3.3
##  [325] 4.7 3.9 4.0 5.0 5.9 5.9 4.2 5.4 4.8 6.0 2.7 5.1 2.0 5.0 3.9 5.2 3.1 6.1
##  [343] 5.1 4.8 5.3 4.2 4.7 6.2 4.6 5.2 5.8 3.3 4.7 5.4 5.9 5.8 4.5 5.7 6.1 3.9
##  [361] 3.6 5.6 5.2 5.9 4.8 4.6 2.9 5.1 5.3 4.5 3.8 5.0 4.1 3.7 4.2 2.7 5.9 6.1
##  [379] 3.2 4.2 4.4 4.2 4.9 4.3 2.7 3.7 3.9 4.0 4.3 5.4 5.1 4.0 5.1 3.4 4.6 4.6
##  [397] 3.9 3.4 3.9 4.7 3.8 3.7 6.0 4.8 3.8 3.6 2.3 4.2 5.8 4.0 5.6 4.8 4.3 3.1
##  [415] 5.3 4.3 3.1 3.3 6.7 6.5 5.1 4.9 4.8 5.2 3.5 6.5 5.2 5.4 4.5 5.2 4.9 3.5
##  [433] 5.2 5.0 5.1 5.2 5.2 3.5 5.1 5.8 5.8 4.2 3.6 3.0 4.4 3.0 4.7 4.3 4.8 4.2
##  [451] 3.0 4.3 3.3 5.2 5.3 4.4 2.8 4.9 3.1 2.7 5.1 3.9 5.8 4.4 4.7 4.9 4.8 5.0
##  [469] 3.3 3.6 2.4 5.4 5.5 4.2 5.6 4.4 5.2 4.5 4.7 5.2 2.8 4.3 3.7 4.5 3.6 4.6
##  [487] 3.0 5.8 4.3 4.0 3.0 3.5 4.6 3.6 5.2 5.3 4.1 3.1 4.2 4.9 3.5 4.8 4.7 5.3
##  [505] 5.2 4.4 3.6 5.2 4.9 3.8 4.4 5.2 4.6 3.4 3.5 5.8 5.1 4.7 5.0 4.2 4.7 5.9
##  [523] 5.8 4.5 5.0 5.9 5.7 4.0 3.5 2.3 6.0 5.6 3.7 4.6 5.1 5.6 4.2 3.5 4.0 4.1
##  [541] 3.7 5.6 4.5 4.4 3.2 3.3 4.2 4.0 5.8 3.4 3.7 5.0 4.2 6.5 4.0 3.6 5.3 4.6
##  [559] 6.2 4.9 3.9 2.6 3.6 4.6 4.1 4.5 5.6 4.3 4.0 2.7 4.1 3.3 4.1 3.9 4.1 5.3
##  [577] 2.9 3.8 5.1 5.4 4.3 5.3 5.6 5.4 2.9 5.5 5.4 4.2 4.5 4.4 3.9 6.2 4.8 3.5
##  [595] 5.4 2.6 6.2 4.0 4.9 5.0 5.2 4.4 4.2 5.8 5.0 4.5 2.8 3.7 4.9 3.7 5.9 4.5
##  [613] 2.7 5.4 5.6 4.0 4.3 4.1 3.5 6.3 4.4 4.6 5.1 5.9 4.3 5.4 5.8 4.6 4.8 2.8
##  [631] 5.2 4.6 4.3 4.4 4.3 5.2 5.6 3.5 3.7 3.3 5.1 3.3 2.9 5.3 4.5 4.4 5.9 6.2
##  [649] 5.0 4.7 4.5 3.4 4.4 3.7 3.2 6.1 4.9 3.3 3.6 6.1 3.7 3.9 3.2 3.4 4.9 5.2
##  [667] 4.6 4.2 3.1 4.3 4.6 5.0 5.0 3.3 4.6 4.6 3.5 4.5 4.0 4.5 4.0 4.0 5.2 5.1
##  [685] 2.2 4.6 5.0 5.0 4.0 3.1 4.2 4.9 5.9 3.0 4.0 4.2 4.1 5.2 4.4 4.2 4.8 3.5
##  [703] 4.3 4.1 4.0 5.3 4.9 5.0 5.1 4.2 3.8 4.5 6.7 2.9 2.7 4.2 3.4 3.7 5.2 5.0
##  [721] 2.8 4.6 5.7 6.1 3.5 4.0 5.4 3.2 4.3 5.9 4.0 4.5 3.4 4.0 3.5 3.8 5.4 5.8
##  [739] 4.0 3.9 5.4 2.9 5.9 6.2 5.1 4.3 1.4 6.1 6.0 3.0 5.3 3.0 4.2 5.2 5.2 3.9
##  [757] 5.0 5.2 4.4 5.0 4.6 4.4 4.3 6.6 4.1 4.6 4.8 4.5 4.5 4.8 4.3 4.6 3.3 3.0
##  [775] 3.8 3.4 4.8 3.4 3.8 4.3 3.0 4.1 4.4 2.3 5.2 5.1 3.7 3.9 4.3 4.2 4.5 6.2
##  [793] 5.0 5.0 4.8 5.5 4.0 5.5 4.2 5.2 4.6 5.7 4.9 5.1 5.2 4.2 4.8 3.9 6.1 4.4
##  [811] 4.4 4.7 4.3 4.4 4.3 6.6 5.2 4.2 4.9 3.6 5.2 3.2 4.3 4.4 3.3 4.7 4.6 6.1
##  [829] 4.1 3.1 4.6 5.1 5.4 3.6 5.1 5.1 4.8 6.0 5.1 3.1 5.2 2.8 3.9 3.9 4.7 4.8
##  [847] 4.0 3.3 4.6 3.5 4.0 4.5 4.5 5.2 4.7 3.1 4.7 4.5 4.9 6.0 5.1 5.3 5.1 4.9
##  [865] 4.6 4.2 4.8 4.4 4.1 4.1 5.0 4.7 4.2 3.5 4.8 3.5 3.7 4.7 6.3 4.8 5.0 2.9
##  [883] 5.1 5.1 4.1 3.8 4.0 5.4 4.7 3.8 5.3 4.6 5.4 4.1 5.1 6.1 5.5 4.8 3.5 4.8
##  [901] 4.3 5.6 3.1 4.5 4.9 3.7 4.3 4.2 3.4 3.6 5.4 4.1 4.4 4.8 3.1 4.1 3.8 1.9
##  [919] 4.5 5.2 2.9 3.5 3.5 4.0 4.1 5.1 5.5 3.7 4.3 2.9 4.8 5.7 3.8 5.0 3.1 4.9
##  [937] 4.0 4.4 5.4 5.6 4.4 5.0 5.8 4.3 5.4 3.2 4.4 5.1 3.1 3.2 4.2 4.3 4.8 5.2
##  [955] 3.1 4.3 3.1 5.4 3.6 4.2 2.6 4.4 4.2 4.6 5.3 4.7 4.8 3.9 4.3 4.3 5.6 4.3
##  [973] 4.7 6.1 5.2 4.6 5.0 4.4 2.3 5.7 5.4 4.9 5.7 4.1 5.3 4.5 5.5 4.6 5.4 4.6
##  [991] 5.2 5.3 4.7 4.7 4.8 3.0 5.9 4.0 3.7 6.2
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.340 5.176 5.100 5.100 4.900 4.696 4.700 4.500
## 
## $jack.boot.se
## [1] 0.9496398
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
## [1] 0.7716717
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
##   3.421699   5.022619 
##  (1.461780) (2.311129)
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
## [1]  0.8833553  0.0682798 -0.9229918  0.1927211 -0.2531453  0.1012401
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
##    [1] -0.070433852  1.075251601  0.218533982  1.476959466  0.684505255
##    [6]  0.320314805  1.014042252  0.781463061  0.467875110  1.090349914
##   [11]  0.555084891  1.013473455 -0.234618857 -1.096228232  0.133348503
##   [16]  0.546673888  1.054904232 -0.383541894 -0.115175769  0.637159291
##   [21]  0.644317630 -0.446244250 -0.552474465  1.208323797 -0.123984007
##   [26]  0.734490607  0.874708594  0.516696906  0.981272807  1.776614993
##   [31]  0.826796418  0.835245862 -0.292371411 -0.115253817  0.546546132
##   [36]  1.103766011  1.160478580 -0.918375962 -0.194410819  0.521687584
##   [41]  0.342128068  0.202634958 -0.780376282  0.371357982  0.331717536
##   [46]  0.046924183  0.669906585  0.918504339  0.098593851  0.608741998
##   [51]  1.444804029  0.690711534  1.899232591  1.328809793  0.580510679
##   [56]  0.054603351  0.981366840  1.551146413  0.759070055 -0.081942326
##   [61] -0.248838673 -0.192576704  0.533416490  1.178915087  1.359084920
##   [66]  0.883311781  0.691925983  0.362542820  0.624001965  0.328942426
##   [71] -0.029087630  0.725018617  1.351649298  1.522251602  1.013778759
##   [76]  0.172406999  0.788170937  1.252507050  0.291578151 -0.692073315
##   [81]  1.017100137  0.264622607  0.972063336  0.974021974  0.327253007
##   [86]  1.983909028  1.052656810  0.812047480 -0.395035530  1.077591428
##   [91]  0.455541539  1.355733055  1.655501081 -0.369629077  0.301090942
##   [96]  0.209361376  0.916677294 -0.084482228  0.926970078  2.005637916
##  [101]  0.913144839  1.058700409  0.888076663  0.613656181  0.041831792
##  [106]  1.939859001  1.590765775  0.301384620  0.465326422  0.343179374
##  [111]  0.966165951  0.600665247 -0.136582163  0.165547683  1.016434423
##  [116]  0.707571140  0.996705692  1.108124375  1.480886566 -0.023028640
##  [121]  0.110943123  1.142257762  0.504524365 -0.018600996  1.020255437
##  [126]  0.583105326  0.890539739  1.524713957  0.728254838  0.597455487
##  [131]  0.635758849  0.493117065 -0.097565005 -0.868901734 -0.413144478
##  [136]  1.008796899 -0.010352147  1.906615947  0.591458502  0.382980883
##  [141]  1.902245779  0.833689286  0.983687053  0.972757659  0.030755117
##  [146]  0.822395414  1.284701586  0.408865684 -0.473441940 -0.003122758
##  [151]  0.979926676  1.261567876  1.032667860  0.521012217  0.146895834
##  [156] -0.192424443  0.245298503  0.545349932  0.757733521  1.080880108
##  [161] -0.367318673  0.649148745  0.493476262  1.184518172  0.468702660
##  [166]  0.545909203  0.906050151 -0.292669222  1.173390266  1.089502316
##  [171] -0.348088294 -0.134150659  1.435609138  0.965254616  1.705381686
##  [176]  1.969866102  0.466710259  0.695239935  0.051677471  1.748150138
##  [181]  0.813898501  2.292269517  0.725940203  0.769541269  1.001763348
##  [186]  0.836250217  1.351859639  1.783477941  1.098985941  0.600814297
##  [191]  0.889779008  0.192318496  1.208895714  0.516220947  1.180964517
##  [196]  1.169079079  0.504957132  0.980389086  0.653474163  0.174420107
##  [201]  0.244812183  0.670133760  0.197887348  0.642528575  1.034274585
##  [206]  0.364813210  1.788363279  0.310748238  1.488152106  1.787647973
##  [211]  0.593696170  0.341925726  0.461774627  1.157324209 -0.223680543
##  [216]  0.656960245  1.163627066  0.794446672 -0.097865221  0.259316523
##  [221]  1.004651960  1.707257685  0.162237098  0.501204090  1.140793216
##  [226]  0.776373464  0.920315202  0.255774588  0.701882096  0.135472595
##  [231]  1.965990140  0.903994293  0.786822426  1.055323059  0.474198279
##  [236]  1.372566520  0.756523818  1.482032956  1.562047276  0.085103053
##  [241]  0.216052701  1.908812320  1.098957654  0.842603109  0.386191160
##  [246] -0.534166287  0.820519814 -0.012297490  0.558587840 -0.393820824
##  [251]  0.874268747  0.327265228  0.623033167  0.665577266  0.195944152
##  [256]  2.254161813  0.980884478  0.649827261  0.575391123  0.249851006
##  [261]  0.852449214  0.631843480  0.498896776  0.391066874  0.462283040
##  [266]  0.907240501  0.623955494  2.097979578  0.446965190  0.549600481
##  [271]  0.280095776  1.345122747  0.460662214  0.112701951  0.975479911
##  [276]  0.922473966  1.242942226 -0.045101911  0.678871645  0.552815902
##  [281]  1.832955293  0.557978156  0.947246701  0.256726141  0.804905374
##  [286]  0.001216518  0.623823856  1.407772451  0.256690055  0.802360172
##  [291]  0.618272963  1.013718247  0.355002265  1.253475732  1.209117902
##  [296]  0.637309238 -0.411461991  0.004986063  0.060236797  1.580652208
##  [301] -0.259129935  0.964239424  0.794844902  1.436330321 -0.039158264
##  [306] -0.983605137  0.303148211  0.775024740  1.144296690 -0.439845551
##  [311]  0.856081872  0.169538184 -0.486580803  1.149419511  0.809893949
##  [316]  1.059627369  0.389825022  0.206665090  0.596155770  0.782945318
##  [321]  0.977919247  1.052158801  0.380512678  0.994191332  0.296176666
##  [326]  0.565496429  0.555361670  0.196836948  0.207615539  0.855376371
##  [331]  0.569308799  0.280381346  0.465569812  1.703543985  0.870686262
##  [336] -0.014796975  1.528781182 -0.692319422  0.522896424 -0.330446133
##  [341]  0.152068974  1.221935415 -0.010469492  1.212489182  0.935928939
##  [346]  1.291784918  1.592738331  0.886670578  0.400859642  0.890295814
##  [351] -0.358565740  1.022360936  1.483715953  0.928819242  1.404958604
##  [356]  1.103331608  0.568645866  0.609878362  1.065574847 -0.696422511
##  [361] -0.322458968  0.886334233  0.611896268  0.983561285  0.631016591
##  [366]  0.010143165  1.461048990 -0.315454522  0.817626749  0.888380276
##  [371]  0.282495009  1.354732590  0.611912961  0.158793757  0.831487137
##  [376]  1.816486944  0.862953955  0.225829402  0.684122363  0.093847171
##  [381] -0.303250254  0.431883907  0.457812620  0.957032983 -0.110854726
##  [386]  1.146637094  0.839857553  0.575330653  1.234833217  0.857681257
##  [391]  0.580488410  1.185230753 -0.298620746  0.666864260  0.577054169
##  [396]  0.619681582  0.624527122  0.591548779  0.408365228  0.746111354
##  [401]  0.310925053  0.223470746  0.617462184  1.196747553  0.120026328
##  [406]  0.897854387  1.226709423  0.567668490  0.962831056  1.080720695
##  [411]  0.695526276  0.744715194  0.987409313  0.025629164  0.620197968
##  [416]  0.875680334  0.747824968  0.718779208  0.312039228  0.789892046
##  [421]  0.544851758 -0.192576704  0.443058299  0.444947142  0.796538774
##  [426] -0.013623883  0.598324316 -0.687283897  0.647009348  0.116202171
##  [431]  1.442365658  0.355044452 -0.225317796  0.919669210  0.490448396
##  [436]  0.899324968  0.295631291  0.720039083  0.740320146 -0.075673226
##  [441]  0.979363795  0.950118825  1.066273203  0.365710656  1.859734399
##  [446] -0.403266315  0.657462019 -0.269695729 -0.921748852  1.081595053
##  [451]  0.875116009 -0.585464645 -0.109793695  0.950221883  1.149040473
##  [456] -0.372985082  0.065276934  0.771671675  1.195709598  0.778772648
##  [461]  0.440794523  0.862047426  1.436242858  0.127782579  0.698633201
##  [466]  0.289044260  0.902570816  0.050142918  0.441115483  0.771174818
##  [471]  0.192878572  1.614912097 -0.070156923  1.066542716  1.609263657
##  [476]  1.302204553 -0.298100390  0.965352334 -0.480418181  0.910541773
##  [481]  0.584386635  0.482987508  1.690867140  0.727759160  0.488152270
##  [486]  0.760862930  0.552277170  1.225489358  0.924436754  0.759500422
##  [491]  0.483152471  0.481825015  0.821443834  1.275819891  1.425085890
##  [496]  1.091637266  1.652611754  1.247836871  0.220165580  0.837063761
##  [501]  1.383661405  0.921295107  0.136878873  0.339170312  1.182363274
##  [506]  0.885170807  0.819511033  0.953046667  0.292561438  0.587173294
##  [511]  1.782782727  0.906285108  0.791165509  1.288264256 -0.180160471
##  [516]  0.994937630  0.332535721  1.335327531  1.015522571  0.990337101
##  [521]  0.705593558 -0.474150806  0.082978693  1.022948603  0.772644299
##  [526]  1.002009778  0.920442456  0.299405081  0.511465997  1.106010454
##  [531]  0.927097789  1.275768459  0.569307301  0.487847662  0.801219510
##  [536]  1.336362985  1.206989835  0.905896208  0.142297131  1.206048385
##  [541]  0.883592752  1.695239595  1.432426103  0.331076143  0.411159977
##  [546]  0.920847473  0.922210688 -0.266696305  1.093019992  0.331449840
##  [551]  1.778848546  0.504791577  1.139227383  1.858834128  0.694244884
##  [556]  0.322822390  0.555169780  1.100850133  0.775612286  0.647351324
##  [561]  0.587360115 -0.261204121  0.214453494  1.054835088  0.511519242
##  [566]  2.390743255  1.154394863  0.496790664  2.069646588  0.067505207
##  [571]  0.766368664  0.125215742  0.860216465  0.495179126  0.757351656
##  [576]  0.534096644  0.235130006  1.051702206  0.586426775  1.174342211
##  [581]  0.359342659  0.526758461  1.068935002  1.670056728  1.159080210
##  [586]  0.479910857  1.113015811  1.013263644  1.003393098  0.778895556
##  [591]  1.026285380  0.355670394  0.570373143  0.605289703  1.157026704
##  [596]  0.898014635  1.401377004  0.071995492  1.239329167  0.216379554
##  [601]  1.532120183  1.933991419  0.184838319  0.656217215  1.522001873
##  [606]  0.268681619  1.367886915 -0.767193442  0.214456754  0.967613841
##  [611] -0.961458453  1.247262372  0.608168005  0.126156341  0.898014635
##  [616]  0.220538977 -0.383541221 -0.008698394  0.871828087  0.171682707
##  [621]  0.032467371  0.955359050  0.626697646  0.327157896  0.440191090
##  [626]  0.680229290  0.847166322 -0.297258092  0.726800341  1.007714708
##  [631]  0.428395927  0.085702448  0.549169584  1.436888647  1.018918814
##  [636]  1.126354229  1.154136752  0.815731096  1.049290914  0.462793929
##  [641]  1.265743110  1.285313279  1.775213270  0.390055160  0.097855010
##  [646]  0.368464558  1.331950529  0.575703718  0.642528575  0.855473903
##  [651]  0.344531125  0.973674210  0.691484389  0.078690218  1.002690712
##  [656] -0.038606774  0.885608568  0.509790521  0.158465581  0.785659057
##  [661]  1.210656919  1.328349734  0.714459791  1.457898371  1.350222444
##  [666]  0.570017111  0.987278107  0.650940570  0.646259842  0.562100937
##  [671]  1.068293664  0.869991173  0.740315432  0.785254885 -0.004873772
##  [676]  0.689575341  0.551616184  0.523898489  0.356631555  0.722833278
##  [681]  1.541874724  0.179115287  1.168959907  0.935635987  0.577548562
##  [686]  0.697238438  0.424596241  0.617359717  1.339465921  0.875116009
##  [691]  0.330053100  0.900894554  0.455354687  0.106447430  1.001147712
##  [696]  0.205138997  0.943671066  2.158196431  0.225524672  0.494847660
##  [701] -0.295251375  0.656270127  1.118816783  0.142526949  0.521683005
##  [706]  0.904682811  1.101083135  1.089933300  0.155874953  0.577246212
##  [711]  0.284369129  0.090839549  0.600547869  1.232200865  1.219073548
##  [716]  0.849332821  0.752464296  1.105757840  1.025737805  0.795865784
##  [721] -0.052664236 -0.747652299  1.074822149 -0.678718453  1.341017839
##  [726]  1.011422219  1.501521378  0.389387161 -0.343836238  0.208007096
##  [731]  0.227876277  0.615083503  0.397150569 -0.246931157  0.693022669
##  [736]  0.167627453  2.098952327  0.104149217  0.506254614  1.211105057
##  [741]  1.452851331  0.348526010  0.521014329  1.090705798  0.046228337
##  [746]  1.946650788  0.843861079  0.629541537  1.970963129  0.481754235
##  [751]  0.421681872  0.493863059  0.226581143  0.127646443  0.825890448
##  [756]  1.101079576  0.648809598  0.745590413  0.946523049  0.527686970
##  [761]  0.534927601  0.677568094  0.979515023  0.156981089  0.008668096
##  [766]  1.852461236 -0.277373567  2.045725638  1.247066681  1.822470561
##  [771]  0.595483603  0.905765987  0.665944009  1.177886436 -0.464851650
##  [776]  0.754294564  0.751978949  0.839688622  0.792616894  0.210576690
##  [781]  0.310833500  0.773898609  0.870686262  0.228566742  0.410140539
##  [786]  1.355049419  0.221473389  0.373129948  0.809160515  0.009232366
##  [791]  1.437521561  1.370919768  0.467780720 -0.676997567  0.714554932
##  [796]  1.383601329  0.875095936 -0.106739334  0.290239818  1.357121058
##  [801]  0.936089957  0.259001116  0.017212525 -0.406914509  0.954760313
##  [806]  1.377154983 -0.305963005  1.746996213  1.040766060 -0.212281763
##  [811]  0.690685158  0.615094174  0.473223772  0.849173328  0.769177795
##  [816]  0.166721165  0.449409905  0.751182564  0.507608333  1.222948786
##  [821]  0.553805758  0.713657143  0.696722199  1.275872814  1.088822113
##  [826]  1.580652208  1.372911744  1.104744413  0.563284951  0.875818424
##  [831]  0.935717693  0.916114124  0.181980837  0.690007929  0.397303145
##  [836]  0.634923094  0.292496269  0.760038256  0.675764692  0.042582069
##  [841]  0.948709618 -0.017419354  0.571150015  0.461736314  0.497141525
##  [846]  0.825897648  1.163469887  0.986324052 -0.180081266  0.764919057
##  [851]  0.733644301  1.678181711  1.321022338  0.605657132 -0.155410025
##  [856]  0.847407972  0.140914783  0.172728474  1.202984013  0.950841213
##  [861]  0.103248774  1.139769200  1.094180219  1.878638018  1.219125434
##  [866]  0.142181093  0.618119556  0.732109803  0.966347945  0.293792302
##  [871]  0.626290416  1.903082619  0.962797021  2.015372371  0.090845032
##  [876]  0.548842147  1.121547110  0.634923094 -0.240084974  0.124118973
##  [881]  1.065134385 -0.491797683 -0.019363340  0.296898406  0.183288901
##  [886]  0.173903588  0.325235783  1.457228698  0.816101755  0.314724363
##  [891]  0.601853516  0.206073305 -0.222896180  1.729961131  1.088509276
##  [896]  0.915045484  0.356395594  0.520669909  0.882970882  0.734058053
##  [901]  0.097108983  1.151708830  0.877669973  0.599737300  0.345527919
##  [906]  1.681444779 -0.251906136  0.567444860  0.352998619  0.291394599
##  [911]  0.812036990  1.228048350  0.514172056  1.127424785  0.526778393
##  [916]  0.668964029  1.562233865  1.179249709  1.128763179  0.116765628
##  [921]  0.690613799  1.099769977  0.960175733  0.810870795  0.746721766
##  [926]  0.287365493  0.916271665  0.055530496  0.400828885 -0.163625795
##  [931]  1.103331608  0.340964294  1.139033418  1.381023064  0.823586442
##  [936]  0.220107524  0.004283384  0.264945464  1.724993315  1.212246379
##  [941]  0.548052218  2.126036817  1.815957212 -0.326328334 -0.388342357
##  [946]  0.524244315  0.642491022  1.250305575  1.223058130  0.474604171
##  [951]  0.150170461 -0.059783455  0.643999872  1.424753266 -0.196141717
##  [956]  0.870656275  0.416685657  0.859671310  0.438186690  0.617313341
##  [961]  0.264586368  1.594589352  0.648344155  0.883311781  0.158354653
##  [966]  1.016936945  0.482331718  1.089933300  1.758476218  1.752757529
##  [971]  0.974252726  0.662439980  0.186186763  1.378797938  0.011304426
##  [976]  0.081886716  0.344343568  1.142862416  1.562472234  0.741277856
##  [981]  0.505548301  0.949162492  1.926003831  0.202206649  0.926212065
##  [986]  0.255799139 -0.113448161 -0.285437198 -0.426596316  1.289311224
##  [991]  0.002504270  1.027534519  0.118584227  0.485822333  0.615776837
##  [996]  1.861144121  0.739198595  1.102503137  0.680229290  1.106619878
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
##   0.68126059   0.36848615 
##  (0.11652555) (0.08239422)
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
## [1]  0.78173782  0.34879077 -0.24220327 -0.04879605 -0.07042398  0.09080315
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
## [1] -0.0238
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9180176
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
## t1*      4.5 0.006706707   0.8973492
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 4 5 6 8 
## 2 1 1 1 1 2 2
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
## [1] -0.0162
```

```r
se.boot
```

```
## [1] 0.9289459
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

