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
## 0 3 4 6 8 9 
## 2 1 2 2 2 1
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
## [1] -0.0307
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
## [1] 2.642883
```

```r
UL.boot
```

```
## [1] 6.295717
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
##    [1] 4.9 4.4 3.4 6.2 2.3 4.3 3.8 5.0 5.7 5.3 6.7 4.3 3.8 4.5 6.0 3.4 4.9 3.9
##   [19] 4.8 4.0 4.3 3.1 3.4 4.5 5.1 4.4 3.4 4.0 3.7 5.4 3.3 3.1 4.9 5.4 4.6 3.4
##   [37] 5.3 5.1 3.1 3.4 4.2 4.7 4.6 3.7 5.5 4.2 4.8 4.0 4.8 4.5 3.7 5.3 5.0 5.1
##   [55] 3.9 4.5 3.2 3.7 5.1 2.8 5.2 4.6 3.7 3.6 6.0 4.8 4.3 3.7 3.4 5.0 5.1 5.1
##   [73] 5.1 2.9 5.4 4.9 3.5 6.3 4.3 4.1 3.9 4.3 3.8 3.4 4.8 5.6 5.9 6.2 5.2 4.5
##   [91] 3.3 3.4 4.9 4.9 4.5 5.1 4.0 3.0 3.9 4.8 5.8 4.3 3.6 4.0 3.7 4.9 5.2 5.9
##  [109] 4.3 3.1 5.1 4.1 4.8 4.1 3.9 5.0 5.2 4.1 5.6 5.2 4.4 5.0 4.3 5.1 3.4 4.1
##  [127] 4.5 2.8 4.9 3.6 3.2 4.8 4.4 4.3 5.1 4.0 4.8 3.2 5.4 3.6 5.2 6.1 4.2 4.2
##  [145] 5.2 3.4 3.4 3.7 3.8 3.9 3.6 2.4 4.1 3.4 5.6 5.0 3.8 3.3 3.7 3.8 4.5 3.4
##  [163] 3.7 5.3 4.2 5.5 6.8 4.5 5.4 4.4 5.1 3.6 5.6 5.3 4.6 5.6 4.8 2.9 4.8 4.4
##  [181] 3.8 5.2 4.5 4.4 5.7 4.0 5.2 3.4 4.4 5.6 3.1 4.8 5.8 5.2 4.5 4.4 3.5 3.5
##  [199] 4.2 5.1 4.7 4.9 4.7 4.9 3.3 4.3 4.0 3.3 5.2 3.6 4.8 4.0 4.3 5.9 3.3 4.5
##  [217] 3.2 4.4 4.6 4.0 5.2 4.0 5.0 4.4 2.2 3.4 5.5 3.7 4.7 5.5 3.5 4.0 3.7 4.4
##  [235] 2.6 4.1 4.2 4.1 4.4 5.0 4.1 4.0 5.2 5.0 5.9 4.5 4.2 3.8 5.3 5.0 4.4 4.8
##  [253] 5.8 5.9 3.6 4.4 3.5 4.2 4.4 5.7 5.8 5.7 5.9 4.1 3.6 4.7 5.9 4.9 6.4 5.2
##  [271] 4.0 4.7 5.7 5.2 3.6 4.5 3.1 3.9 3.7 4.5 4.7 4.3 4.7 5.8 3.7 4.3 4.9 4.0
##  [289] 3.3 3.5 4.5 2.7 4.7 4.9 2.4 4.5 6.4 5.4 3.1 6.0 5.0 3.9 4.9 5.4 4.2 6.4
##  [307] 3.6 4.5 4.1 4.8 4.9 3.8 5.3 4.7 4.4 4.1 4.9 5.8 3.8 6.1 4.5 3.3 4.4 5.4
##  [325] 4.6 4.8 5.4 5.4 3.2 4.4 5.9 3.8 4.7 5.1 4.6 3.9 3.7 5.1 4.8 3.8 6.4 4.4
##  [343] 3.7 4.7 4.4 3.8 4.6 4.4 3.8 5.2 5.4 4.9 4.6 6.5 4.6 3.4 5.2 4.8 3.3 3.0
##  [361] 5.3 4.3 2.9 3.5 4.6 5.5 4.2 4.4 5.0 5.0 4.0 5.6 4.6 5.7 4.9 4.0 6.4 5.1
##  [379] 3.8 5.5 4.9 6.6 3.9 6.1 3.6 3.8 4.0 4.2 4.3 4.0 5.1 5.0 4.2 3.2 4.2 4.8
##  [397] 3.5 5.6 3.6 4.3 3.3 5.3 3.7 6.4 4.6 2.9 5.3 3.4 3.7 5.2 4.0 5.5 5.5 3.6
##  [415] 5.6 4.8 4.2 4.9 4.3 5.2 5.3 5.4 4.4 2.6 4.2 4.1 4.0 4.8 4.6 5.0 4.7 5.5
##  [433] 3.8 3.8 3.6 5.9 4.2 3.3 3.0 5.4 2.6 4.1 6.0 5.2 2.8 2.6 5.8 5.3 5.2 4.9
##  [451] 3.6 3.1 4.0 3.3 5.4 5.8 3.4 5.0 4.6 4.8 4.3 5.6 5.4 4.5 3.6 4.0 3.3 3.7
##  [469] 4.0 5.3 4.0 5.2 5.5 3.5 4.2 3.5 5.7 5.0 5.5 3.5 5.3 4.0 4.3 3.9 5.3 4.1
##  [487] 4.8 3.3 5.2 4.8 4.4 4.3 4.9 5.2 5.2 3.9 5.0 4.9 4.5 2.0 4.4 5.6 4.5 4.8
##  [505] 5.4 4.1 5.2 4.8 4.4 3.7 4.0 6.1 5.1 4.6 4.8 3.7 4.4 4.8 4.6 4.2 4.5 2.9
##  [523] 4.2 5.3 4.2 3.8 3.6 5.0 3.1 5.6 6.9 4.3 4.8 4.1 4.3 4.1 2.9 3.0 5.8 5.9
##  [541] 5.4 5.9 5.8 3.0 5.5 3.8 6.2 6.0 5.5 5.4 3.3 4.3 4.5 3.7 3.5 4.6 5.2 4.0
##  [559] 4.8 4.3 4.3 4.7 5.7 6.2 5.0 5.6 4.4 4.5 4.0 4.4 1.8 4.2 4.0 3.3 3.6 5.8
##  [577] 4.2 4.3 4.0 4.8 4.8 5.0 6.4 5.3 3.4 5.1 4.9 4.2 4.0 4.4 4.1 4.6 3.9 4.9
##  [595] 3.0 4.9 5.1 4.9 5.9 4.9 3.6 4.2 3.9 4.3 3.0 5.8 5.1 3.0 2.0 2.9 4.8 5.5
##  [613] 4.9 3.7 5.0 4.5 4.2 5.2 5.9 3.9 3.6 3.5 4.1 3.3 3.5 2.8 4.4 7.0 3.9 4.3
##  [631] 5.5 5.0 4.2 3.3 5.1 3.7 3.5 3.5 4.5 5.1 4.3 5.6 3.4 4.3 5.5 4.5 5.1 4.3
##  [649] 3.3 3.8 3.9 4.9 4.8 4.2 6.3 4.4 5.1 4.9 3.9 2.7 3.6 4.4 3.9 4.2 4.4 2.6
##  [667] 6.4 5.2 5.4 4.6 3.3 4.0 4.1 4.1 3.0 5.8 5.2 3.5 4.8 4.1 4.4 4.9 3.5 4.6
##  [685] 3.6 4.0 4.7 6.2 3.2 6.0 6.6 4.6 4.6 4.6 4.5 3.1 6.8 3.6 5.6 4.1 4.8 4.2
##  [703] 3.8 4.1 4.6 5.3 3.8 4.3 2.9 4.6 3.4 4.7 4.8 4.5 4.1 3.2 5.5 4.8 6.1 4.4
##  [721] 5.0 4.8 5.0 4.1 5.8 3.5 5.5 4.4 4.5 3.9 4.9 4.1 3.4 5.5 5.1 2.6 4.2 5.2
##  [739] 4.4 5.3 4.0 4.3 4.6 5.7 4.4 4.5 5.6 5.3 5.3 5.0 3.1 5.3 4.1 5.1 3.8 4.0
##  [757] 3.3 3.0 3.2 4.3 3.4 4.7 5.7 3.3 2.6 4.9 4.5 5.3 4.9 4.5 4.7 4.8 3.8 4.8
##  [775] 4.1 4.2 3.3 4.8 3.7 4.8 4.0 6.7 5.2 4.1 6.1 3.4 5.2 5.6 3.5 4.7 7.1 4.9
##  [793] 4.2 3.7 4.0 4.6 3.5 4.4 5.4 4.9 3.8 4.1 6.5 5.1 5.1 4.9 4.0 5.8 2.3 5.0
##  [811] 3.8 3.0 4.0 5.2 4.7 3.4 4.2 3.4 5.0 3.8 5.0 5.3 4.5 4.4 5.1 3.8 3.6 4.5
##  [829] 3.6 5.0 6.3 4.5 4.6 4.3 5.1 5.0 4.3 3.3 3.5 4.2 3.5 5.3 4.2 4.9 3.5 6.7
##  [847] 4.2 3.7 4.1 4.4 4.9 3.1 3.4 2.1 4.8 5.7 5.0 4.9 5.1 4.3 3.5 5.0 3.5 4.8
##  [865] 3.3 6.8 5.8 5.2 3.8 4.1 4.2 4.7 3.7 4.6 3.1 4.1 5.4 4.3 3.0 5.7 3.8 4.7
##  [883] 4.9 4.0 4.3 5.7 4.0 4.4 5.9 4.5 4.1 4.0 4.6 3.5 4.3 5.0 7.1 3.8 5.0 4.9
##  [901] 4.4 4.7 4.5 5.4 4.3 5.3 5.3 6.2 3.8 4.9 5.1 3.2 3.6 4.4 5.1 4.5 4.7 3.1
##  [919] 4.5 4.3 4.2 5.6 3.8 4.5 4.4 4.0 3.7 3.9 4.5 3.2 3.4 2.6 3.6 6.3 2.5 3.9
##  [937] 4.7 2.8 3.9 3.8 4.6 3.6 6.0 3.7 4.0 4.1 5.1 4.8 4.1 4.0 3.4 2.8 4.0 3.8
##  [955] 4.9 3.9 5.7 3.8 3.2 3.0 5.3 3.8 4.5 4.1 3.9 4.7 4.3 5.1 5.6 4.3 5.1 5.0
##  [973] 4.7 4.1 2.7 5.2 4.6 5.4 5.6 4.1 5.9 4.4 4.6 4.6 4.4 4.7 6.3 3.8 5.0 2.5
##  [991] 5.0 3.6 4.8 4.0 4.6 4.4 5.5 3.7 5.8 5.7
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
##    [1] 5.6 4.2 4.1 6.0 5.7 4.9 4.8 4.3 3.2 4.1 5.3 2.5 5.6 5.3 4.3 4.5 3.8 3.7
##   [19] 5.6 3.2 4.2 2.9 4.5 3.6 4.1 3.9 4.2 5.4 3.6 5.5 5.1 5.0 2.9 3.1 5.4 3.3
##   [37] 5.8 4.9 4.5 3.7 4.5 5.0 5.0 5.5 5.3 4.3 6.2 5.1 4.7 5.1 3.6 5.3 5.0 3.7
##   [55] 3.2 4.4 5.4 7.0 3.4 3.1 4.0 5.5 5.8 3.4 3.7 5.4 5.5 1.8 5.7 4.3 5.4 4.1
##   [73] 3.5 4.5 4.9 2.9 5.2 3.5 5.0 5.3 4.7 5.3 3.8 5.4 2.7 5.4 4.3 3.2 4.2 3.5
##   [91] 4.0 4.6 3.3 4.6 5.6 6.1 3.2 6.2 3.4 5.5 5.0 5.2 3.0 3.5 2.9 4.5 3.3 3.7
##  [109] 5.0 4.2 4.6 4.3 4.4 4.6 5.7 4.8 4.2 5.6 3.5 3.9 4.7 5.6 5.2 4.5 4.2 3.3
##  [127] 4.9 4.5 6.2 3.7 2.7 4.0 4.1 5.6 4.7 5.5 5.0 5.0 4.7 6.7 5.6 5.0 3.5 4.1
##  [145] 3.5 4.5 4.8 4.5 3.6 5.7 4.5 4.6 5.0 5.6 4.7 3.1 6.3 4.3 4.7 5.1 4.7 4.4
##  [163] 4.5 5.7 4.7 3.9 5.3 3.8 5.3 3.4 3.8 4.8 4.9 3.1 3.2 3.5 6.7 4.3 4.6 4.3
##  [181] 5.7 3.6 4.1 3.9 3.6 3.9 3.7 4.1 4.9 5.7 5.9 5.1 4.0 4.4 4.2 4.9 4.2 4.8
##  [199] 5.0 4.3 3.7 3.3 3.5 3.1 4.1 5.8 5.7 5.0 3.4 5.1 4.0 3.8 4.9 4.2 4.7 4.8
##  [217] 4.6 4.1 1.8 5.0 4.4 3.9 4.3 2.4 3.3 5.5 3.5 4.7 4.4 4.7 5.0 5.5 3.7 2.3
##  [235] 5.1 4.6 4.0 3.7 2.8 4.4 5.0 5.1 5.5 4.2 5.0 4.6 5.9 5.4 4.5 3.6 3.8 4.5
##  [253] 4.3 3.7 5.0 6.1 4.2 4.3 3.6 4.7 3.3 5.0 4.8 2.1 4.3 4.4 5.9 4.1 2.7 3.8
##  [271] 4.6 5.5 4.5 4.7 4.7 5.0 3.3 3.7 4.5 4.9 3.3 4.3 3.8 5.7 3.7 5.1 5.6 3.9
##  [289] 4.2 4.5 3.6 3.2 4.5 3.8 5.6 3.1 5.1 4.5 5.0 5.5 4.9 3.5 3.9 5.7 4.4 3.3
##  [307] 4.9 5.5 3.0 5.3 4.5 5.1 5.0 4.5 4.7 2.8 5.3 4.2 3.8 4.1 4.4 4.1 4.2 3.0
##  [325] 5.8 5.6 4.5 2.5 3.2 4.0 4.0 3.5 4.0 4.5 3.9 3.4 3.8 4.1 4.4 3.0 4.7 5.4
##  [343] 5.3 5.1 3.9 4.2 5.7 4.3 4.2 5.6 5.9 2.6 4.1 3.7 4.2 4.2 5.3 3.5 4.0 5.7
##  [361] 4.7 4.6 5.9 4.2 5.5 6.1 5.1 4.2 4.8 3.5 5.6 2.7 4.3 5.1 3.6 3.3 5.7 2.8
##  [379] 4.3 4.5 5.7 4.7 4.1 4.3 4.9 4.6 5.9 4.2 4.8 4.7 5.2 5.5 5.9 5.6 4.8 6.4
##  [397] 3.7 4.3 2.5 5.2 5.0 4.0 5.8 3.4 3.2 5.3 4.5 3.0 3.0 4.5 4.7 5.8 6.1 2.6
##  [415] 4.9 2.7 5.3 6.1 4.3 5.7 5.4 4.8 4.5 3.2 2.6 5.7 3.4 4.2 5.3 5.7 4.0 4.9
##  [433] 4.7 4.5 4.1 4.9 3.7 5.2 5.7 4.0 3.1 5.1 4.2 4.2 4.3 4.6 5.0 3.2 5.5 4.0
##  [451] 3.3 3.4 5.0 4.4 4.4 4.5 4.0 4.9 4.7 5.4 3.6 4.5 5.3 4.7 4.0 4.6 5.7 4.8
##  [469] 5.5 5.1 5.1 4.1 4.0 5.3 5.6 4.2 5.4 4.6 4.8 4.4 4.5 4.3 4.2 5.9 4.8 2.7
##  [487] 4.7 5.1 5.2 5.1 4.0 4.4 3.1 2.7 3.9 4.1 5.1 6.5 4.9 2.6 4.7 4.6 3.9 3.9
##  [505] 3.6 3.9 2.6 4.3 3.5 3.3 4.0 3.8 4.8 4.0 2.8 4.3 4.9 5.7 4.6 3.8 5.6 4.0
##  [523] 5.5 6.0 4.1 3.9 3.4 4.9 3.6 4.0 4.7 6.1 4.6 4.1 4.6 5.7 3.6 5.3 6.1 4.1
##  [541] 4.5 4.3 4.1 4.8 3.9 4.0 6.1 4.7 4.4 4.1 4.0 5.7 3.7 3.9 4.9 3.0 4.2 5.2
##  [559] 3.7 4.4 4.5 4.6 4.7 5.6 6.4 4.7 5.2 5.1 4.4 4.1 4.9 6.1 5.2 4.1 2.4 3.4
##  [577] 3.8 4.0 2.9 4.1 4.8 3.8 6.4 4.3 5.0 4.2 4.9 4.9 4.0 2.7 5.1 5.0 4.3 3.9
##  [595] 4.4 4.9 4.8 3.8 5.0 5.1 3.8 3.8 5.2 4.1 5.2 4.2 3.5 4.3 3.9 5.7 4.8 3.5
##  [613] 5.9 4.4 5.6 3.1 6.5 4.3 4.1 3.0 4.4 5.1 3.9 4.4 6.1 2.3 5.0 2.5 6.3 4.4
##  [631] 5.1 5.1 6.1 4.5 5.3 3.8 2.7 2.7 3.3 5.7 4.8 3.9 6.6 3.9 4.0 4.3 4.7 4.8
##  [649] 3.4 3.9 3.1 5.1 5.2 4.9 6.2 3.3 2.3 4.6 5.0 4.1 4.2 3.5 5.2 3.7 3.7 2.6
##  [667] 4.6 3.7 5.1 4.4 4.5 5.1 4.6 2.9 4.4 3.6 3.5 4.4 3.9 4.8 3.7 4.2 4.3 5.1
##  [685] 4.9 5.0 5.4 4.9 4.3 3.9 5.4 5.0 4.4 5.2 4.8 3.8 5.5 2.1 3.7 3.9 4.4 4.7
##  [703] 4.4 6.6 3.6 3.3 5.2 4.0 5.0 5.0 4.5 5.7 4.5 4.6 5.2 4.4 4.3 6.4 5.2 5.2
##  [721] 3.4 4.6 4.8 2.6 4.6 5.2 2.3 5.0 5.6 4.4 2.7 3.6 4.7 4.4 5.0 3.5 3.3 4.5
##  [739] 3.8 4.3 4.4 5.0 4.4 4.5 3.9 5.2 3.6 4.4 2.9 5.1 3.5 2.9 4.2 3.2 3.8 3.9
##  [757] 2.7 3.9 6.5 5.3 5.7 3.9 3.9 7.0 4.8 5.7 4.0 6.3 6.1 3.0 3.5 5.7 4.9 3.1
##  [775] 4.7 4.4 2.8 2.8 5.4 3.3 5.6 5.9 4.0 3.8 4.2 4.4 4.4 4.2 3.3 6.1 3.6 5.6
##  [793] 4.8 3.1 3.1 4.5 5.9 3.7 3.9 4.9 4.6 4.5 5.8 4.8 4.8 4.9 3.2 4.0 6.4 5.1
##  [811] 4.2 4.7 5.1 5.5 4.9 4.7 3.6 3.0 4.1 3.8 5.9 4.1 4.8 5.1 4.9 5.8 4.7 4.3
##  [829] 3.8 5.0 4.4 4.7 4.7 4.0 3.5 5.5 3.9 4.1 6.4 4.6 5.7 4.3 3.2 3.9 2.8 3.0
##  [847] 3.6 4.6 6.7 4.9 4.2 3.2 2.6 5.1 4.4 4.0 5.2 4.1 5.5 4.0 4.0 5.7 4.4 5.6
##  [865] 5.2 5.4 3.1 3.8 5.3 3.3 2.2 5.1 4.6 5.4 5.9 5.7 4.0 3.8 5.6 5.3 4.3 5.9
##  [883] 5.3 3.3 3.5 5.3 3.8 5.4 4.4 6.0 4.8 5.4 3.9 4.9 4.8 5.0 4.2 3.5 5.2 4.7
##  [901] 4.6 4.1 3.9 4.2 2.9 4.2 4.7 2.9 3.4 4.3 4.2 3.9 4.5 4.6 5.3 4.3 4.8 3.5
##  [919] 3.5 5.7 5.0 3.2 5.2 3.8 2.5 4.3 5.6 3.9 4.4 3.3 3.6 5.6 4.9 4.5 4.0 5.2
##  [937] 4.7 4.3 4.3 3.7 3.9 6.0 4.9 4.7 5.6 2.5 5.9 5.4 4.3 5.7 4.4 5.5 4.5 5.9
##  [955] 3.8 4.8 5.1 4.4 4.2 3.2 2.7 5.0 3.1 4.3 4.3 3.0 3.7 3.8 5.8 3.3 4.1 4.1
##  [973] 4.9 3.6 4.8 3.9 5.4 3.7 3.6 5.5 4.5 5.3 3.3 4.6 3.4 5.9 3.5 5.5 3.5 4.7
##  [991] 6.1 4.2 5.0 5.0 3.8 5.0 4.9 3.3 3.4 3.7
## 
## $func.thetastar
## [1] -0.0498
## 
## $jack.boot.val
##  [1]  0.46715116  0.27977528  0.23548387  0.10414201 -0.01107784 -0.09765396
##  [7] -0.18488372 -0.36814621 -0.39864499 -0.59174041
## 
## $jack.boot.se
## [1] 0.9577756
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
##    [1] 5.2 4.1 4.9 4.0 5.0 5.2 5.1 4.1 2.8 4.7 5.6 3.6 5.1 3.7 5.8 4.8 4.2 4.1
##   [19] 5.0 5.7 4.9 3.2 4.2 5.1 5.2 2.8 4.1 3.5 3.3 3.7 5.1 2.8 4.1 2.9 3.8 5.5
##   [37] 5.1 4.6 3.9 5.2 4.9 3.3 4.8 4.5 3.8 5.3 5.3 4.5 2.5 6.1 5.3 4.3 5.1 4.2
##   [55] 4.4 3.9 3.3 3.3 4.4 2.6 4.6 3.9 4.5 2.6 5.1 5.8 6.4 2.2 3.8 4.7 3.2 3.5
##   [73] 5.9 5.1 3.4 4.2 5.6 5.4 3.5 5.9 4.7 4.0 4.1 3.6 5.7 4.2 4.5 3.4 4.6 4.1
##   [91] 2.9 4.1 3.6 4.4 5.5 6.1 5.5 6.0 4.3 4.4 5.2 6.0 4.4 3.8 5.3 2.6 4.7 4.1
##  [109] 4.3 3.9 3.0 3.8 5.1 5.4 3.9 4.6 5.4 3.8 3.7 3.9 5.9 4.1 4.6 3.9 3.6 4.2
##  [127] 4.6 4.3 4.5 2.7 4.4 4.8 5.9 2.8 3.9 4.2 3.3 2.8 3.7 5.9 4.5 4.7 5.2 2.4
##  [145] 5.0 4.3 4.6 2.3 6.1 3.7 5.1 4.3 4.2 4.9 4.3 4.8 3.9 4.2 3.3 5.8 3.9 4.7
##  [163] 5.5 5.3 5.2 5.3 4.2 5.3 4.8 6.2 4.6 5.5 2.8 5.5 3.0 3.5 5.1 4.7 5.1 6.0
##  [181] 5.4 3.4 5.4 5.1 5.2 5.4 3.4 5.2 3.9 3.8 5.1 4.3 4.0 4.6 4.2 4.7 4.2 5.5
##  [199] 5.4 5.4 3.3 3.1 3.7 3.3 4.6 3.4 4.0 4.8 5.1 4.7 4.0 3.8 4.1 4.3 3.6 4.3
##  [217] 5.1 5.9 4.9 6.7 4.7 6.9 4.8 4.4 4.9 3.9 2.7 3.8 4.2 4.1 2.4 5.1 4.5 3.3
##  [235] 3.7 4.0 5.7 4.1 4.9 3.3 2.8 6.1 4.8 3.2 6.0 3.4 5.6 5.4 2.3 5.5 4.1 6.1
##  [253] 4.5 5.7 4.7 4.2 4.5 4.8 4.1 2.4 2.6 3.8 4.8 5.7 4.4 4.5 5.1 4.0 6.7 5.4
##  [271] 3.3 4.9 4.8 4.6 4.8 4.5 3.2 3.0 4.3 3.1 3.5 3.1 4.8 4.3 3.2 4.6 4.4 5.1
##  [289] 5.2 2.0 5.1 4.2 4.1 5.1 5.5 4.9 4.5 3.7 5.1 3.1 3.7 5.2 4.9 4.3 4.3 3.7
##  [307] 4.4 4.2 4.5 4.1 4.3 4.6 5.2 3.0 4.6 4.1 6.0 6.2 4.1 4.9 4.9 4.8 6.4 4.5
##  [325] 5.1 5.0 3.8 4.4 4.3 3.5 4.3 3.5 5.3 4.9 4.8 6.6 6.0 4.1 4.2 5.1 3.9 4.4
##  [343] 6.3 3.4 3.0 4.0 4.7 4.0 5.7 6.6 4.3 4.7 4.4 3.0 5.1 4.5 3.8 5.0 4.3 4.3
##  [361] 4.9 3.4 5.2 3.1 3.7 4.3 4.3 3.4 4.8 3.2 5.5 6.2 5.8 4.2 4.1 3.3 3.9 4.0
##  [379] 6.2 4.6 4.4 4.3 3.7 5.7 6.2 5.6 3.7 5.1 3.8 4.9 3.9 5.6 4.1 4.4 5.2 4.0
##  [397] 4.4 4.5 3.8 5.3 4.7 3.1 4.1 4.8 5.5 5.4 4.8 4.6 4.1 4.8 4.0 3.2 4.5 4.6
##  [415] 3.8 4.9 4.7 6.8 3.7 4.0 3.4 3.6 4.0 6.4 4.8 2.7 4.5 5.7 3.3 4.1 3.6 4.8
##  [433] 5.1 4.5 3.1 3.2 4.7 4.6 4.0 3.5 4.7 4.1 4.5 5.1 4.3 4.2 3.9 3.1 3.7 4.9
##  [451] 6.2 5.4 4.1 5.5 3.1 4.2 4.8 4.5 3.6 3.7 5.4 3.3 4.4 4.5 3.9 5.2 5.3 3.8
##  [469] 3.7 3.0 4.3 4.6 4.7 4.0 5.7 4.5 3.4 4.4 3.3 4.9 5.4 5.4 2.8 4.9 4.6 5.6
##  [487] 4.9 5.0 3.3 4.6 3.9 5.7 4.2 5.5 4.2 3.1 5.0 3.3 5.0 4.5 4.1 4.4 6.4 5.4
##  [505] 5.5 3.5 3.8 5.2 5.7 4.9 3.5 3.6 4.9 5.2 4.8 4.3 2.4 5.9 4.2 4.8 5.0 6.6
##  [523] 2.9 3.1 2.7 4.9 4.0 5.5 5.8 6.3 4.6 5.9 3.2 3.4 3.8 4.2 4.5 2.5 3.9 3.9
##  [541] 4.2 4.5 5.0 4.1 5.6 4.9 4.8 4.3 5.0 4.4 5.4 3.6 5.7 3.3 4.9 3.5 5.8 5.6
##  [559] 5.5 4.0 3.8 6.7 3.8 4.8 6.0 3.7 3.3 5.4 5.0 3.9 3.8 4.8 3.3 5.2 5.1 4.2
##  [577] 4.5 5.1 5.5 4.6 4.2 4.6 6.3 5.1 3.9 4.5 3.2 5.9 4.6 4.1 5.7 2.2 4.2 5.5
##  [595] 6.3 3.4 3.7 4.5 4.2 5.0 4.3 3.8 4.2 4.5 2.9 4.8 5.9 4.2 3.1 7.3 2.8 4.1
##  [613] 5.7 4.2 4.1 5.5 4.8 6.0 4.7 4.9 5.4 5.2 3.3 3.7 4.6 3.7 5.5 4.2 4.6 4.0
##  [631] 5.7 4.4 4.0 4.5 3.9 3.2 4.4 3.7 4.6 4.7 4.6 4.4 4.5 6.2 4.8 3.0 5.2 4.3
##  [649] 4.7 4.1 5.0 4.8 4.9 3.9 4.3 4.2 4.3 6.6 5.5 4.6 4.9 5.0 3.5 5.7 5.0 5.4
##  [667] 6.3 3.7 3.9 5.8 4.1 6.4 5.8 6.0 5.1 4.2 5.9 4.5 5.0 5.5 5.1 5.8 3.5 4.0
##  [685] 5.5 4.5 5.1 4.0 4.3 4.4 4.7 4.5 5.5 3.3 6.1 4.3 4.3 3.5 4.1 4.9 6.4 5.1
##  [703] 3.8 4.8 3.3 6.2 3.1 3.4 3.9 5.7 5.0 4.6 4.6 5.9 3.9 4.2 4.4 6.3 4.7 5.2
##  [721] 5.4 4.0 4.9 5.0 3.8 2.0 4.6 4.5 2.7 3.3 4.6 4.1 4.6 4.4 4.6 2.5 4.4 4.8
##  [739] 4.6 3.8 5.6 3.1 3.0 5.2 4.4 5.5 6.0 3.8 5.0 4.4 4.0 4.3 5.6 2.9 4.3 3.7
##  [757] 3.8 5.3 6.0 3.9 4.7 5.3 6.8 3.0 5.0 3.7 2.7 4.5 4.1 4.9 3.1 5.5 5.1 3.8
##  [775] 3.7 5.3 5.2 4.8 6.4 5.4 3.9 4.9 3.6 4.9 5.1 2.6 4.7 5.7 4.4 5.0 4.4 3.6
##  [793] 3.1 4.8 4.2 4.2 3.9 4.2 3.8 4.1 4.2 4.4 4.6 4.3 4.2 2.4 3.1 4.6 4.5 5.2
##  [811] 4.3 4.0 5.1 3.4 4.6 5.1 5.4 5.7 5.5 5.6 3.7 4.8 3.7 3.2 3.7 4.5 3.0 4.7
##  [829] 3.9 5.2 5.1 3.5 3.7 5.6 5.3 5.4 4.5 5.4 4.2 4.4 3.2 4.4 4.7 4.2 3.1 5.1
##  [847] 5.7 5.1 6.5 4.5 3.3 4.6 2.6 4.7 4.5 1.6 3.9 5.6 3.5 5.1 4.3 5.6 2.8 5.2
##  [865] 4.8 3.8 3.9 3.0 5.8 5.9 5.6 6.4 4.8 4.2 3.0 4.1 3.8 4.6 4.2 6.1 5.5 3.0
##  [883] 6.4 3.7 3.9 5.5 4.1 2.0 6.0 4.1 5.0 3.1 5.8 5.9 3.3 3.1 3.6 6.1 4.8 3.5
##  [901] 2.9 5.3 4.7 5.0 4.4 4.9 3.7 2.4 4.5 5.3 5.5 2.8 5.8 4.6 4.1 4.4 5.3 5.1
##  [919] 5.7 2.8 4.3 4.4 3.4 4.6 2.7 4.7 5.2 4.1 4.4 4.7 4.9 2.5 3.8 4.5 4.2 6.2
##  [937] 4.5 4.8 3.4 3.6 5.3 5.6 5.8 5.3 4.0 4.5 4.0 3.4 4.0 4.7 5.0 4.2 4.9 2.8
##  [955] 3.8 3.3 5.1 4.1 3.4 5.5 4.8 5.5 5.3 5.3 4.4 3.2 5.2 3.5 4.2 5.2 3.9 5.8
##  [973] 4.0 2.7 6.3 3.6 4.5 6.0 5.8 2.5 3.5 3.6 5.5 4.5 5.2 3.0 5.3 3.1 4.5 4.5
##  [991] 5.6 5.1 5.4 3.3 4.4 5.8 3.8 4.8 5.0 3.1
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.4 5.3 5.1 4.9 4.9 4.7 4.6 4.4
## 
## $jack.boot.se
## [1] 1.08
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
## [1] 0.4515997
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
##    5.895624   12.835881 
##  ( 2.565473) ( 5.830318)
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
## [1] 0.8649799 0.3654621 0.7761127 0.6160971 0.8188522 0.1158060
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
##    [1] -0.016565192 -0.123138237  0.083047912  0.795301765  0.807803234
##    [6]  0.801127576  0.461712819  0.584663900  0.506984140  0.186569693
##   [11]  0.782451853 -0.256945351  0.161355727  0.080875514  0.884788667
##   [16]  0.831934365 -0.478510783 -0.331567816  0.265820712  0.528138940
##   [21] -0.205920171 -0.055340509 -0.050925587  1.188543830 -0.069877623
##   [26]  0.713601580  0.456148582  0.648276379 -0.816419500  0.108283248
##   [31]  1.103900764  0.315299272  0.373135358  0.133511692  0.910699692
##   [36] -0.039188587  0.680398155  0.632698419  0.587582406  0.413260483
##   [41]  0.030392599  1.179551138  0.529408367  0.467805414  0.900757246
##   [46]  0.006783036  0.286237105  1.262084768 -0.045920973  0.987917936
##   [51]  0.650927383  0.805267504  0.262523465  0.484299837  0.522614937
##   [56] -0.159220993  0.812162387  0.135204198 -0.019542642  1.538745879
##   [61]  0.120531233  0.867338386  0.090752694  1.877468359  0.415002007
##   [66] -0.531522783  0.356402570  0.356370802  0.748555610  0.459633732
##   [71]  0.029953803 -0.059959479 -0.151823181  0.838327492  0.464811703
##   [76] -0.340795264  0.314409210  0.255015768 -0.030394257  0.119688166
##   [81]  0.429640395  0.167695267  0.808436485  0.010004591  0.372956539
##   [86]  0.706608398  0.516626298  0.508933211  0.387859650  0.381074273
##   [91]  0.408013384  0.641246169  0.808650981 -0.174822050  0.852719248
##   [96] -0.399822446 -0.269693896 -1.002564756  1.010579901  0.351164260
##  [101]  0.342087174 -0.002722279  0.211142392  0.437593639  0.929530358
##  [106] -0.257858967  0.649617200 -0.056573278  1.242565773 -0.349471061
##  [111] -0.005315343  0.200290314  0.611653397  0.099977676  0.194832433
##  [116]  0.598046680  1.021175668  0.173881614 -0.077066759  0.270559684
##  [121]  0.072662030  0.691927078  0.240582530  0.381133649  0.692838513
##  [126]  1.000541252  0.351387896  0.846177665  0.899276670  0.132268700
##  [131] -0.490917775 -0.153793087  0.522389717 -0.153754365  1.892190278
##  [136]  1.118310293  0.431975597  0.705080709  0.349112548  0.383478464
##  [141]  0.733763254  0.391943822  0.041195586  0.319652366  0.638032008
##  [146]  0.261857660  0.358841741  0.263669891  0.848934218  1.338166754
##  [151] -0.035671789  0.290526169 -0.059886580 -0.084691980  0.664703472
##  [156]  0.907633238  0.608631327  0.107834450  0.129242089  0.537306184
##  [161]  0.333756877  0.754639850  0.981857699  0.946035210  0.563945546
##  [166]  1.177127596  0.537630357  0.379067447  0.576077822  0.433705039
##  [171]  0.330281469 -0.154037253  0.824435299  0.314409210  0.324479910
##  [176]  0.345477730  0.335578776  0.630557668  0.521295482  0.229926773
##  [181]  0.973011796  0.133995595  1.258113722 -0.679713636  0.234943935
##  [186]  0.438573659 -0.222588057 -0.147480924  0.835060148  0.208253292
##  [191]  0.229569058  0.163825480  1.055684432 -0.107223890  0.215634937
##  [196]  0.356734612  0.661378786  0.695420029 -0.408831058 -0.467787317
##  [201]  0.431238836  0.439783849  1.294885288  0.148964199 -0.697297706
##  [206] -0.623618142 -0.049664886  0.729479175  0.665233867  0.561319002
##  [211]  0.975913652  0.206921667  0.204124582  0.678415629 -0.122175309
##  [216]  0.371471255  0.122487567 -0.139911910 -0.220175916  0.882034046
##  [221]  0.667704108  1.229177433  1.039329074  0.197217078 -0.083900529
##  [226]  1.419298142  0.616846375  0.296588543  0.767196009  0.727663020
##  [231]  0.024038859  0.177970838  0.853454785 -0.207065999  0.350288810
##  [236]  0.189166502 -0.056679667  1.169248913  1.430394340  0.142973839
##  [241]  0.495063865 -0.331567816 -0.465190865  0.814839979 -0.602457102
##  [246]  0.606378895  0.626589677  0.787358911  0.140629124  0.046253270
##  [251]  0.032602097 -0.218784014  0.350692715  0.808436485  0.508619537
##  [256]  0.158487165  0.750076250  0.209751231  0.749804637  0.280489838
##  [261]  0.322759898  0.150071682  1.078342369  0.708028344  0.180220917
##  [266]  0.036554077  0.413577988  0.040048068  0.762336585 -0.628344105
##  [271]  0.660806932  0.870627697  0.476661923  1.014577559  0.942447525
##  [276]  0.735640981  0.097931422  0.750277326 -0.003047512  0.136188790
##  [281]  0.670892713  0.260829181  0.227205708  0.206477504 -0.132616475
##  [286]  0.280729522  0.417184450  0.610044691  0.980416197 -0.132685325
##  [291]  1.200715994  0.482531126  1.052134441 -0.187851317  0.038299104
##  [296] -0.207287042  0.601033842  0.563771897  0.701511055 -0.726014142
##  [301]  0.682816100  0.207922752  0.850908782 -0.475136811  0.778445249
##  [306]  0.586344148 -0.062541313 -0.055889107  0.272142556  1.179830641
##  [311]  0.724771051  0.378191967 -0.042194963  0.347306008  0.052004045
##  [316]  0.075152287  0.450100975  0.028519095  0.385401658  0.022397474
##  [321] -0.054022077  0.092888653  0.512587000  0.264344103  0.567082432
##  [326]  0.215040321  0.990773436  0.520550504  0.030802270  1.557385990
##  [331]  0.965252719  0.337000662  0.317194152  0.539330314 -0.088906677
##  [336]  0.289044723  0.029688317  0.837752933  0.423462424 -0.194359916
##  [341]  1.056021720  0.314105395 -0.018290438  0.068453400  1.133866386
##  [346]  0.349065855  0.340547793  0.460568945  0.092704204  0.335973042
##  [351]  0.097882933  0.559220343  0.242060425  0.508131994 -0.069474554
##  [356] -0.403766561 -0.166568451 -0.415152284  0.050999923 -0.033589344
##  [361]  0.388880263  0.797808452  0.785682741  0.138704829  0.061108355
##  [366] -0.637311557 -0.603288451 -0.046049653  0.572643272  0.273595916
##  [371]  0.758869099  0.096606730  0.585922584 -0.004835491  0.132884862
##  [376]  0.663427498  0.388023074  0.406169071  0.670454479  0.568680979
##  [381]  0.757454415  0.701532561  0.421213998 -0.143221859  0.542679800
##  [386]  0.885913848 -0.728792149  0.164583186  0.774506281  0.296482322
##  [391] -0.601815727  0.685532069  1.130193955  1.072352135  0.082014015
##  [396]  0.586462506  0.474302155  0.166100663  0.261140658  0.662924743
##  [401]  0.647298841 -0.001375351  0.954279103 -0.628553235  0.842776361
##  [406]  0.264822312  0.747851991  0.751655749  0.971171842 -0.441915023
##  [411]  0.126630513  0.504728297  0.544838655  0.226394350  0.255283489
##  [416]  0.096023044  0.140058898  1.517642553  0.045689665 -0.220023250
##  [421]  0.610498129  0.534241779  0.366390875  0.727109002  0.052812423
##  [426]  0.053150902  0.836626069  0.741089135  0.097045636  0.504170470
##  [431]  0.943291807  0.498023537  0.262695711  0.174313827  0.488570202
##  [436]  1.961365279  0.259232363  0.144581219  0.778577211  0.266219195
##  [441]  0.384544832  0.124335087 -0.059462860  0.098688012  0.442675261
##  [446]  0.485536582  0.682544389  0.362939726  1.028346999 -0.102963099
##  [451]  0.437316530  0.603709985  0.203065183  0.071081753  0.283793358
##  [456] -0.067101405  0.214293940  0.067463476 -0.483379040  0.337597501
##  [461]  1.006400011  0.005356697  0.258100221 -0.074875653  0.321876021
##  [466]  0.696115314  0.092450726 -0.436372618  0.802660604  0.093377969
##  [471]  0.981616634  0.409354485 -0.197062724 -0.015351847  0.443287272
##  [476]  0.378492208  0.721781902  0.196060579  1.127344696  0.456118396
##  [481]  0.623515608  0.357163785 -0.327709295 -0.582290671  0.251329340
##  [486]  0.306957417  0.836927746  0.937512191 -0.261788237 -0.511361237
##  [491]  0.421078644  1.078716594  0.027794123  0.508201110 -0.190408426
##  [496]  0.363174813 -0.274947279  0.155799867  0.306683929  0.185029954
##  [501]  0.592549017  0.598346495 -0.269371316  0.417853912  0.004737945
##  [506]  0.323241318  0.551671888  0.169224913  0.234255555  0.398760554
##  [511]  0.181312228 -0.118858617  0.393010197 -0.236916973  0.542585600
##  [516]  0.254606832 -0.716150468  0.420414301  0.891422001 -0.277907530
##  [521]  0.478620617  0.572443008  0.203290186  0.881381085 -0.261026917
##  [526]  0.631740887  0.902030035  0.874415335 -0.121175709 -0.300609958
##  [531]  0.004633622  0.789372847 -0.024267676  1.242250068  0.112539100
##  [536]  0.960465502  1.225688519  0.480544485  0.091435425  1.193200806
##  [541]  0.986732166 -0.173909579  1.156418428  0.347005527 -0.176047048
##  [546] -0.064616847  0.692996001  0.105193951 -0.182740625  0.146950929
##  [551]  0.318704654  0.583279749  0.466838870  0.432517547  0.184808268
##  [556]  0.683479679  0.449066593  1.018226395 -0.092663997  0.328637395
##  [561] -0.164139507  0.349065855  1.040884212  0.344726249  0.155831391
##  [566]  0.548644704  0.558088655  0.174392126  0.163374309  0.869000651
##  [571]  0.917100048  0.713471472  0.134936006 -0.011300701 -0.135595813
##  [576]  0.194428519  0.589585372  0.051027715  0.593736349  1.031416389
##  [581]  0.896556054  0.354542581  0.893896620  0.117033276  1.042764923
##  [586]  0.456298350  0.483835974  0.656333828  0.734446902 -0.342972383
##  [591] -0.291630049  0.762418951 -0.133301735  0.366101676  0.496036541
##  [596] -0.166452642  0.854226283  0.178079628  1.082688704  0.324899112
##  [601] -0.047339400  0.079811026  0.406385943 -0.264271561  0.143048775
##  [606]  0.695529289  0.688570187 -0.217923310 -0.419361413 -0.134636367
##  [611]  0.668250053 -0.222605153 -0.271379889 -0.054630316 -0.290902150
##  [616]  0.889530130  0.696961371  0.107026257  0.204304658  0.561815823
##  [621]  0.451599664  0.116776898  0.499275447  1.102861559  0.980416197
##  [626]  1.228020031  0.424282199  0.180262766  0.736731567  0.513988669
##  [631]  0.154093432  0.369118506  0.088547080  0.186312619  0.379914088
##  [636] -0.140058887  0.544998426 -0.160838081  0.426455965  0.480600490
##  [641]  1.316394253  0.690788463  0.643272948  0.278886582  1.576342592
##  [646] -0.321798526  0.411776598  0.108193067  0.904228108  1.132232094
##  [651]  0.855190993  0.809328876 -0.783118441 -0.376859416  0.174394690
##  [656]  0.202625984  0.537508827  0.668899735  1.987396730 -0.397531915
##  [661]  0.111197226  1.364823944  0.326558439  1.127105387 -0.074969456
##  [666]  0.168014099 -0.048488751  0.959550051  0.756685559 -0.163975909
##  [671]  0.218632830  0.521517084 -0.217332280  1.118117873  0.868674265
##  [676]  0.352214265  0.887008933 -0.078379022  0.567206648 -0.138005332
##  [681]  0.981615446  0.780176596  0.491549507  1.025929160 -0.298844893
##  [686]  0.967902795  1.144734936  0.029271590  0.501435333  0.619084546
##  [691]  0.258592648  1.000727662  0.257931288  0.498427750  0.256072833
##  [696]  0.178079628  0.667734781  0.276554616 -0.337372742 -0.154336289
##  [701]  0.041363596  0.057139119  0.794674065  0.402517039  0.589259412
##  [706]  0.676124152  1.021528520  0.705006606  0.238492165 -0.294840956
##  [711]  0.050957608 -0.047039618  0.560189359 -0.017140508  0.694210265
##  [716]  0.631244079  0.214801726  0.129361641 -0.300891442 -0.209505846
##  [721]  0.744685351  0.327398870  0.220153804 -0.436117414  0.483011113
##  [726]  0.354384848  0.057334855 -0.421538026 -0.311032927  0.575638745
##  [731]  0.322390508  0.811254733 -0.250709899  0.236093990 -0.431136968
##  [736]  0.261336185  0.757187821  0.670454479 -0.349146381  0.556378909
##  [741]  0.212301561  0.983111781  0.453310489  0.257212646  0.464893847
##  [746] -0.088611876  0.102734373  0.057496340  1.727466216  0.822613851
##  [751]  0.540825093  0.490367027  0.543022922  0.597833905  0.379164006
##  [756]  0.532395420 -0.202961924  0.744548351  1.178083883  0.149188249
##  [761]  0.338264951  0.232431236  0.728645618  0.853086328  1.155088963
##  [766]  0.556099459  0.041620036  0.624298898  0.142794640  0.627806520
##  [771]  0.618418907  0.738694113 -0.379558769  0.105032659  0.905646381
##  [776]  0.460894939 -0.430167033  1.459725920  0.422801344  1.033853157
##  [781]  0.012171028  1.030597864  0.829422173  0.106367164  0.461272940
##  [786]  1.052706372  0.638886219  0.421992107  0.484299837 -0.159050619
##  [791]  0.543160619  0.482531126  0.525285686 -0.269131841  0.441363774
##  [796]  0.265189369  0.641061462  0.808925156  1.020259390 -0.095109439
##  [801]  0.618882652  1.024103761 -0.093263418  0.952371566 -0.355202505
##  [806] -0.106424923  0.600622917  0.086618740 -0.109472076  0.463981570
##  [811]  0.074189871  0.374938927 -0.342108759  0.887955701  0.129450100
##  [816] -0.057735017 -0.045379707  0.517072954  0.224486835  0.480155150
##  [821]  0.431246092  0.409494166  0.267685347 -0.227015625  0.972621168
##  [826]  0.534290881  0.228742390  0.358116488 -0.525829692  0.394549489
##  [831]  0.112700120  0.500383787  0.547589672  0.499206636  0.556032695
##  [836]  0.335877613  1.039473700 -0.558156564 -0.353132323  1.331196881
##  [841]  0.140886107  0.839350978  0.684024136  0.268185229  0.981616634
##  [846]  0.507235532  0.329255246  0.123876197 -0.352191668  0.092208634
##  [851] -0.058680764  1.018779126  0.797763413  0.551987456  0.558358391
##  [856] -0.329131457  0.564781902  0.398551695 -0.223631634  0.914002449
##  [861]  0.660806932  0.456118396  0.480029269  0.205401240 -0.140612950
##  [866]  0.534338149  1.443453314  0.389927202  0.279417594  0.629971961
##  [871]  0.100724612  0.258735683  0.244916528  0.909155159  0.643109866
##  [876] -0.240538512  0.547094230  0.641373423  0.987780765  0.252335225
##  [881]  0.241960115  0.291718024  0.308392874  1.336250679  0.136696488
##  [886] -0.437735996  0.156465483  0.341320002  0.265579304  0.194832433
##  [891] -0.238149291 -0.027275487  0.359595398 -0.422862633  0.766899170
##  [896] -0.352568912  0.996143667 -0.416843059 -0.614698630  0.847699393
##  [901]  0.056001019  0.012824872  0.332863356  0.432617043  1.134115999
##  [906]  0.827977546  0.785160481  0.682342480  0.339924277  0.660535789
##  [911] -0.346382561  1.229827432  0.457869481 -0.022134997  0.085477721
##  [916]  0.167456067  0.379288182  1.101994556  0.761673112  0.974235457
##  [921]  0.491113399 -0.049615619  0.631127922  0.034429786  0.347501177
##  [926]  0.407812011 -0.178435758  0.380544305 -0.129236431 -0.490168389
##  [931]  0.268576965 -0.137419387  0.511126578  0.089588852  0.966721434
##  [936]  0.127403195  0.331072363 -0.467384099 -0.140234281  0.681404211
##  [941]  0.440942769  0.701081855  0.758853958 -0.467190670 -0.832420185
##  [946]  1.617256722  0.313215236  0.181049194 -0.221281376  0.600572065
##  [951]  0.335446052 -0.155101475  0.216198337  0.402403246  0.750680200
##  [956]  0.993026419  1.300022439  0.914002449  0.327882196  0.215216644
##  [961]  0.124336967  0.087134586  0.383535701  0.132048642  0.550184560
##  [966]  0.107029134  0.064809276  0.748624275  0.278779909 -0.024121049
##  [971]  0.256822624  0.309478867  0.573910791  1.393812362  0.472354911
##  [976]  0.527841191  0.981213027  0.774523538  0.216961841  0.243759326
##  [981] -0.012188108 -0.141841783  0.175705572  0.543370346  0.415111306
##  [986]  0.592042297 -0.346699423  0.523262430 -0.316374588  1.127119337
##  [991]  0.371660028  0.771608214 -0.326506942  0.217190615  1.461681781
##  [996]  0.898970880  0.321023728 -0.271969451  0.387416631  0.093098554
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
##   0.45930575   0.18855640 
##  (0.05962677) (0.04215758)
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
## [1] -0.422633565  0.561063459 -0.008617863 -1.039586770  1.053927872
## [6]  0.052028853
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
## [1] 0.0062
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9195364
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
## t1*      4.5 0.06606607     0.90948
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 5 6 8 
## 1 1 3 3 1 1
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
## [1] 0.0154
```

```r
se.boot
```

```
## [1] 0.9123679
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

