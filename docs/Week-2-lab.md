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
## 1 2 3 4 5 6 7 
## 1 2 1 1 2 2 1
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
## [1] 0.0629
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
## [1] 2.742635
```

```r
UL.boot
```

```
## [1] 6.383165
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
##    [1] 5.8 3.9 4.3 3.5 4.9 7.2 5.9 4.2 4.6 3.9 5.7 3.0 5.1 5.3 3.5 3.6 4.4 5.4
##   [19] 5.3 4.3 4.7 3.7 5.8 5.2 3.9 4.5 4.5 4.4 4.4 4.6 2.9 5.7 3.6 4.1 3.6 6.4
##   [37] 4.3 6.3 3.3 4.5 3.1 3.8 4.7 5.1 4.0 2.9 4.2 3.0 5.3 4.9 4.5 4.3 3.3 4.4
##   [55] 5.2 4.7 4.4 4.1 5.3 4.5 4.4 5.2 4.2 5.5 3.8 4.5 5.2 4.8 5.6 4.5 6.7 5.7
##   [73] 3.5 6.6 2.6 3.7 5.5 4.2 3.2 3.9 3.8 3.9 2.9 3.2 3.1 4.4 4.3 4.8 4.8 5.2
##   [91] 3.6 5.1 5.4 4.1 4.8 4.0 6.0 4.3 4.9 5.2 4.7 4.6 4.9 4.7 5.1 2.7 4.1 3.5
##  [109] 3.7 4.8 4.0 3.5 3.6 3.8 5.5 4.6 3.9 4.1 2.7 3.8 4.8 4.7 3.9 4.0 3.6 2.9
##  [127] 4.2 4.3 6.1 4.6 5.4 6.0 4.2 3.3 4.3 4.8 4.0 6.4 5.3 4.5 4.7 5.4 4.4 5.2
##  [145] 5.7 5.6 4.4 4.0 3.6 4.9 4.9 4.7 4.7 5.6 3.1 5.0 4.1 2.7 4.7 6.1 4.9 4.3
##  [163] 4.4 3.8 3.3 5.0 4.3 4.8 4.9 5.6 3.8 5.4 5.2 5.1 6.2 3.9 4.7 3.6 4.5 5.0
##  [181] 5.0 5.4 4.4 4.9 4.8 5.2 4.6 4.4 4.3 5.6 4.7 4.9 4.8 4.1 3.6 4.3 5.9 3.8
##  [199] 5.9 4.6 4.6 6.0 4.1 4.8 3.8 4.1 4.3 4.9 4.8 5.2 2.7 5.3 4.9 4.0 4.0 4.6
##  [217] 4.0 4.1 3.9 5.1 4.7 5.3 5.1 4.8 5.1 2.7 4.3 3.1 4.1 3.7 6.2 4.5 2.4 4.5
##  [235] 4.7 5.9 4.4 4.5 4.5 2.7 4.9 5.0 3.9 4.3 5.5 4.0 4.6 5.2 5.0 4.3 2.7 4.4
##  [253] 3.9 5.6 4.2 3.7 3.1 4.7 5.1 4.3 5.8 4.4 4.8 5.5 4.6 4.3 2.8 4.4 3.1 4.7
##  [271] 4.6 3.6 4.4 4.7 3.3 4.3 4.9 3.3 7.3 4.0 5.1 5.6 3.4 2.9 4.5 3.8 5.5 5.1
##  [289] 4.3 6.3 5.3 5.3 5.1 5.5 4.1 5.8 5.6 3.1 5.8 4.5 2.9 4.8 4.5 2.1 6.6 6.1
##  [307] 4.4 2.1 4.8 3.5 2.8 3.3 5.5 4.2 3.6 3.7 5.1 3.9 5.5 4.1 3.4 4.0 4.6 6.0
##  [325] 3.9 4.4 4.7 3.3 5.0 4.7 4.4 5.0 3.7 4.0 4.5 2.5 3.1 3.5 4.4 3.7 4.9 4.3
##  [343] 4.7 5.0 3.2 3.9 4.7 2.8 5.4 6.2 4.0 5.5 1.7 4.5 4.1 3.4 4.6 3.0 4.6 2.8
##  [361] 4.2 4.0 4.8 3.4 5.9 5.3 5.1 4.9 3.8 3.1 4.3 3.0 4.8 4.6 5.3 4.5 3.7 3.9
##  [379] 6.1 4.4 3.6 5.6 5.4 3.7 3.4 2.7 4.5 5.9 4.7 6.1 6.3 4.9 3.4 6.0 4.2 2.9
##  [397] 5.8 5.1 6.0 4.3 4.7 4.9 3.9 4.8 2.8 3.4 3.7 2.7 4.4 5.3 5.6 5.0 5.0 4.5
##  [415] 4.4 4.4 3.8 6.9 3.6 5.0 3.4 4.2 5.4 4.5 3.7 4.8 4.5 5.4 4.9 3.3 4.2 3.3
##  [433] 3.4 5.1 3.3 4.9 4.2 4.3 5.6 4.8 4.8 5.3 4.3 5.3 4.1 4.2 4.3 6.3 3.6 4.0
##  [451] 3.5 3.8 5.2 3.7 2.7 5.8 4.3 5.2 2.8 4.7 4.5 4.7 2.8 6.3 4.6 6.3 4.3 4.7
##  [469] 3.8 4.9 3.1 2.8 4.2 4.8 2.4 5.1 4.2 3.9 4.1 4.3 3.4 4.6 4.1 6.6 5.0 2.6
##  [487] 3.2 4.6 4.6 2.9 3.8 5.5 5.0 1.0 4.2 4.9 4.7 6.5 6.5 3.9 5.0 3.2 3.6 4.9
##  [505] 5.8 4.2 3.3 4.9 4.5 4.2 3.2 4.5 6.1 6.0 4.2 4.4 3.3 3.9 6.8 4.5 4.7 3.4
##  [523] 3.6 4.3 3.1 4.0 3.3 5.1 5.6 4.5 4.3 4.8 5.3 4.9 3.9 4.4 6.1 3.5 4.7 5.9
##  [541] 6.0 5.1 4.2 5.5 3.8 5.6 5.1 6.6 4.2 3.9 2.7 6.1 5.3 4.9 2.6 5.3 3.2 4.3
##  [559] 5.4 4.2 3.8 4.3 4.7 3.8 6.0 3.5 4.2 4.5 4.2 4.6 5.8 4.8 5.0 4.5 3.7 3.2
##  [577] 5.1 5.9 5.1 3.3 5.6 6.0 3.0 4.5 4.2 4.2 4.0 4.1 4.9 5.0 4.4 4.8 4.9 3.9
##  [595] 5.4 3.4 4.5 4.0 3.4 5.0 4.2 5.1 4.7 3.2 3.8 3.5 3.2 3.9 3.1 6.2 3.9 2.6
##  [613] 3.4 3.8 3.6 5.6 3.3 3.5 5.3 5.2 5.3 4.4 4.2 4.2 4.7 3.5 3.5 4.9 4.5 5.5
##  [631] 5.5 5.9 5.5 3.5 3.3 4.0 3.4 5.2 4.5 3.5 4.6 4.3 4.8 3.8 4.2 5.7 4.6 5.3
##  [649] 4.9 4.0 3.8 6.4 4.1 4.5 3.6 3.8 3.5 4.9 3.5 3.8 5.4 3.5 5.5 3.5 5.1 3.7
##  [667] 4.0 3.6 5.5 2.8 3.7 3.4 4.9 4.5 3.9 3.4 5.0 4.2 3.7 4.4 4.1 4.8 3.8 4.7
##  [685] 5.9 3.9 6.1 4.7 4.9 4.9 3.0 5.1 6.5 3.6 4.7 5.7 5.3 4.1 3.4 5.0 5.0 4.7
##  [703] 5.4 4.4 4.2 3.9 3.9 4.7 4.3 4.5 5.1 3.0 4.2 4.9 5.2 3.4 4.3 5.1 4.6 4.7
##  [721] 4.9 4.9 4.9 5.7 5.6 3.9 4.1 3.4 6.2 6.9 4.7 5.6 2.4 5.2 4.8 4.6 3.1 6.2
##  [739] 3.0 3.7 3.4 5.7 4.2 3.7 5.4 4.5 4.0 4.2 2.1 4.5 3.9 3.7 4.1 6.4 4.8 4.3
##  [757] 5.2 4.1 6.1 4.2 4.0 4.2 5.0 5.5 4.1 4.6 4.7 5.7 5.6 6.8 4.8 5.0 4.1 5.0
##  [775] 4.4 5.2 4.4 2.7 4.1 4.7 5.4 5.8 5.2 3.9 5.2 4.1 4.5 4.1 5.8 6.0 3.7 3.6
##  [793] 4.7 3.7 4.8 4.0 5.5 2.4 4.5 5.9 5.2 3.7 4.0 3.5 4.6 5.1 5.4 4.8 5.8 3.6
##  [811] 4.7 5.5 5.0 3.7 4.5 6.0 4.3 4.3 5.3 3.3 3.2 2.8 6.8 4.8 4.0 4.0 4.6 3.5
##  [829] 5.3 5.1 4.4 5.1 4.2 5.4 5.4 4.7 5.5 3.5 4.2 4.7 5.1 5.7 4.2 3.0 4.7 6.4
##  [847] 4.0 5.8 5.1 3.6 5.2 4.3 4.4 3.8 5.1 6.4 4.2 3.8 4.1 4.1 4.7 4.7 4.8 2.4
##  [865] 5.3 4.0 4.1 4.5 5.0 4.6 3.1 2.8 3.9 3.7 4.4 4.0 5.7 3.1 3.4 5.6 5.1 4.9
##  [883] 2.7 4.5 6.1 5.5 4.5 4.8 2.9 3.1 3.5 4.5 6.5 2.8 4.7 5.9 4.4 2.5 5.2 4.0
##  [901] 4.7 4.8 4.6 3.9 4.3 3.3 3.5 4.0 4.3 3.7 3.7 6.5 2.7 4.7 5.6 3.6 3.8 4.6
##  [919] 2.9 1.7 3.6 5.4 3.8 3.7 3.2 4.1 5.2 4.0 4.6 3.0 5.4 3.4 4.7 4.0 5.1 4.6
##  [937] 4.6 4.5 6.3 4.7 4.9 5.1 4.3 4.3 4.2 4.0 5.1 5.3 3.5 7.1 4.3 3.6 4.3 4.8
##  [955] 3.6 3.6 5.7 3.7 4.7 4.4 2.5 3.2 4.5 4.6 3.2 3.0 5.6 4.5 2.5 5.6 4.5 4.7
##  [973] 4.8 5.3 4.7 4.0 4.7 5.7 4.8 4.6 4.0 5.0 5.7 3.0 6.2 4.8 5.0 4.7 4.6 4.6
##  [991] 5.2 3.4 5.2 5.8 6.6 6.3 3.8 5.2 3.2 3.1
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
## 2.7000 6.3025
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
##    [1] 5.3 3.0 3.9 4.3 3.1 2.4 3.8 5.3 3.7 4.5 3.0 4.2 3.3 5.1 3.9 3.6 4.2 5.6
##   [19] 4.5 5.9 4.7 5.3 5.6 4.6 5.5 3.5 5.5 5.0 4.5 4.5 3.1 5.1 4.3 3.9 3.7 4.4
##   [37] 2.9 5.2 3.8 4.2 5.4 3.4 4.6 5.7 2.9 5.5 4.1 4.2 4.6 4.7 4.3 3.7 7.5 4.1
##   [55] 4.8 3.4 4.1 4.5 5.1 4.0 3.7 4.7 3.2 4.8 3.5 5.0 4.3 4.4 4.6 5.3 4.7 3.5
##   [73] 4.2 5.2 3.9 4.5 4.5 5.1 5.6 3.5 4.2 4.7 5.0 5.2 4.0 4.6 4.5 6.0 4.0 3.6
##   [91] 6.1 4.6 5.2 4.3 5.0 3.4 3.9 6.2 4.5 3.6 4.8 5.0 3.5 4.5 3.6 2.5 4.8 4.4
##  [109] 2.6 4.2 3.6 5.8 5.1 4.9 5.6 3.2 5.9 4.3 4.8 3.6 3.1 3.4 5.4 4.8 5.3 4.6
##  [127] 4.9 2.2 1.9 5.1 4.5 5.8 4.5 2.8 4.0 4.2 4.1 4.3 2.7 3.2 4.7 4.5 5.4 3.6
##  [145] 5.7 4.9 3.8 5.6 5.2 3.1 5.7 3.2 5.8 3.5 5.3 6.0 5.4 3.4 6.0 5.1 4.0 5.9
##  [163] 4.9 4.5 5.8 4.4 5.2 3.9 5.5 5.2 4.9 4.0 6.0 3.0 3.6 4.0 2.9 4.5 3.0 4.4
##  [181] 4.6 5.6 4.2 5.3 4.8 5.8 3.4 5.0 4.3 4.2 3.9 3.7 3.3 3.7 3.5 4.1 5.4 2.7
##  [199] 3.6 3.7 5.3 4.3 4.9 5.0 4.0 5.0 4.5 3.5 5.0 4.5 3.0 5.2 3.8 3.7 3.7 4.8
##  [217] 5.4 4.2 6.4 4.0 4.1 4.1 5.3 4.9 4.6 5.8 4.6 5.0 6.1 5.3 3.2 4.9 4.8 4.0
##  [235] 5.4 4.6 4.9 2.9 6.9 3.9 5.4 3.8 4.6 3.9 4.9 3.5 6.2 5.6 4.7 5.6 4.4 4.3
##  [253] 3.2 3.9 4.7 4.2 4.2 5.0 4.7 4.7 4.3 3.4 4.7 6.0 5.0 3.9 4.7 4.2 4.6 5.0
##  [271] 3.3 3.3 4.0 4.3 3.5 3.9 3.5 4.3 5.5 4.2 5.6 5.4 4.1 3.4 4.8 4.2 4.6 5.2
##  [289] 5.6 4.9 4.9 4.9 3.7 5.1 4.4 3.1 5.9 3.1 5.4 5.7 4.6 2.7 5.4 4.5 6.9 5.4
##  [307] 2.2 5.4 3.6 4.1 4.5 4.9 5.8 5.8 6.0 4.1 4.4 3.7 2.7 4.2 6.0 6.4 5.4 4.6
##  [325] 3.0 5.5 3.3 4.1 3.8 4.6 5.1 5.0 3.5 4.3 5.5 5.0 5.5 2.4 5.0 3.9 4.0 3.1
##  [343] 6.9 4.9 4.5 5.8 5.6 4.2 4.4 4.1 4.3 3.8 5.8 4.4 4.2 5.3 6.1 6.0 3.9 5.3
##  [361] 4.3 4.9 5.3 5.1 4.7 4.5 3.7 5.6 3.7 4.5 5.2 3.4 5.5 5.6 3.2 5.2 5.4 4.1
##  [379] 5.2 4.3 5.7 7.1 4.3 3.3 3.7 5.2 3.6 5.3 4.5 4.1 7.0 5.6 6.0 3.8 5.2 5.0
##  [397] 5.8 6.0 4.3 4.1 4.5 5.4 4.3 4.9 5.7 5.2 4.0 4.7 3.8 4.1 3.3 4.8 3.5 5.4
##  [415] 4.6 4.9 4.1 5.6 6.9 4.3 3.8 4.5 3.6 5.3 4.2 3.7 5.2 4.7 3.9 5.4 5.4 4.3
##  [433] 4.0 4.2 2.5 5.0 4.4 3.0 4.3 4.8 5.2 4.4 4.7 4.8 4.1 4.8 3.6 3.7 2.9 2.7
##  [451] 4.3 5.6 4.8 4.5 5.1 5.1 4.1 2.3 5.9 3.2 5.4 5.2 4.9 5.6 4.9 5.6 4.4 3.4
##  [469] 5.9 4.6 5.5 3.7 4.6 4.3 5.3 5.5 4.2 3.8 5.3 4.2 4.0 5.0 3.7 4.4 4.2 4.7
##  [487] 4.0 4.1 4.8 5.2 4.5 4.0 2.9 5.5 4.2 4.8 3.9 5.0 3.5 4.5 4.7 2.6 4.5 4.8
##  [505] 3.3 4.7 7.4 5.7 2.4 6.3 6.1 4.9 4.4 4.1 5.1 2.4 3.1 5.1 4.8 3.8 4.0 4.6
##  [523] 4.8 4.1 5.6 3.2 4.2 4.3 4.5 5.7 5.4 4.3 3.4 4.8 5.4 3.6 5.2 4.2 3.8 5.4
##  [541] 4.7 4.8 4.5 4.5 3.4 4.9 6.1 3.5 5.0 3.8 5.7 5.3 4.1 4.8 2.9 5.2 4.8 3.4
##  [559] 4.2 4.4 3.7 4.8 3.4 4.0 4.6 4.6 3.7 4.8 3.4 3.6 3.5 4.5 6.3 5.0 3.3 5.1
##  [577] 2.2 5.4 4.2 5.4 4.1 2.6 5.2 5.1 4.7 6.2 4.6 3.5 4.7 6.1 4.4 3.6 4.9 5.8
##  [595] 4.7 4.2 5.4 4.3 5.1 5.2 6.0 3.3 6.4 4.1 5.0 3.9 4.3 4.6 3.9 5.0 4.8 5.9
##  [613] 4.6 5.1 5.1 4.1 3.8 5.2 4.7 4.1 5.9 3.2 4.9 4.0 4.9 5.5 3.6 4.0 3.7 3.9
##  [631] 6.5 5.7 4.6 5.0 4.6 2.5 1.7 5.1 5.4 4.0 4.1 4.5 5.5 4.9 5.1 3.6 3.9 4.3
##  [649] 5.1 5.3 4.1 3.9 5.0 4.9 2.8 3.6 7.0 4.7 5.2 5.4 3.4 5.9 2.3 3.9 4.5 4.9
##  [667] 6.3 5.0 4.4 3.2 4.9 5.2 4.6 5.0 5.9 4.9 4.7 4.4 4.8 5.7 6.3 4.9 5.1 4.3
##  [685] 5.4 5.6 3.9 5.7 4.4 5.5 4.7 4.9 5.8 4.2 4.0 3.6 5.4 4.6 5.1 6.0 4.0 2.9
##  [703] 5.3 4.6 6.5 2.8 4.9 3.5 4.4 4.5 4.1 4.8 3.9 5.0 5.7 5.3 3.4 5.5 4.3 4.1
##  [721] 4.6 3.9 2.9 4.3 4.2 5.5 5.0 5.5 4.8 5.9 5.1 6.6 3.2 5.6 5.4 4.4 5.9 3.1
##  [739] 4.6 6.2 5.0 5.4 4.2 6.8 5.5 3.8 4.4 5.4 4.2 3.5 4.1 3.8 5.5 4.4 6.0 4.2
##  [757] 5.8 4.8 4.2 4.8 4.5 4.9 3.8 5.8 4.7 4.6 4.5 4.7 4.5 4.3 4.6 5.5 4.5 3.3
##  [775] 5.5 4.9 4.5 5.6 5.6 6.1 6.3 5.7 4.7 5.2 4.8 3.2 4.3 4.2 5.4 3.7 3.2 5.2
##  [793] 5.4 4.5 5.1 5.1 5.1 4.0 4.3 3.7 5.8 5.2 4.5 5.4 6.7 3.3 5.1 4.0 4.4 2.2
##  [811] 3.0 5.3 5.6 4.8 4.6 6.6 4.5 5.3 5.6 3.6 3.3 5.1 5.3 3.8 3.6 3.9 3.8 5.1
##  [829] 4.2 6.4 5.4 4.0 4.2 4.2 4.4 3.0 5.1 3.2 3.0 6.6 3.8 4.2 3.1 4.9 5.3 4.3
##  [847] 3.9 6.5 3.4 5.4 5.0 2.7 5.9 4.2 4.0 5.6 6.3 5.3 4.1 5.1 5.4 5.6 3.6 5.2
##  [865] 5.2 4.5 5.9 5.6 3.8 5.5 3.9 4.0 4.3 4.3 2.7 4.5 4.4 3.4 4.4 4.1 6.0 3.8
##  [883] 3.7 3.4 4.5 2.2 5.8 4.9 3.6 4.3 3.0 4.0 4.8 2.6 6.2 4.6 5.4 4.6 5.7 4.5
##  [901] 5.7 3.2 4.9 3.7 3.9 3.7 3.5 3.2 5.0 2.7 3.7 5.7 4.4 6.8 4.5 7.3 3.5 4.7
##  [919] 5.1 4.5 5.2 3.9 3.8 4.7 4.9 5.4 4.4 4.8 4.9 3.3 3.7 3.7 5.8 3.4 5.1 4.1
##  [937] 4.0 3.5 4.9 5.2 5.0 5.2 4.2 4.6 6.7 6.0 4.3 5.1 4.4 5.2 5.3 5.9 5.4 3.8
##  [955] 5.0 4.3 5.1 6.6 4.0 5.4 4.1 3.8 4.5 6.2 4.4 2.9 4.8 7.5 3.5 3.8 3.3 5.9
##  [973] 5.0 4.9 2.5 4.8 4.9 6.0 3.7 5.6 3.1 5.2 4.6 3.7 4.5 5.8 3.4 3.3 5.1 3.1
##  [991] 3.7 4.9 3.8 3.5 5.5 3.6 3.5 5.2 5.1 5.7
## 
## $func.thetastar
## [1] 0.0625
## 
## $jack.boot.val
##  [1]  0.62132964  0.42931507  0.38260870  0.24460227  0.10877193  0.01424501
##  [7] -0.18005319 -0.30000000 -0.33816568 -0.45736196
## 
## $jack.boot.se
## [1] 1.042248
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
##    [1] 4.2 3.9 5.8 6.6 3.1 4.7 5.2 5.4 4.1 5.4 5.3 4.8 4.1 4.6 4.5 3.3 4.7 3.1
##   [19] 4.0 4.2 4.4 5.2 3.6 3.9 4.3 5.6 4.3 4.1 5.9 4.1 3.8 4.8 5.4 4.2 3.3 4.7
##   [37] 2.5 4.7 5.8 5.0 3.0 3.9 5.0 4.1 4.8 6.5 3.8 3.5 4.4 3.9 4.3 5.1 3.6 5.2
##   [55] 3.2 4.6 6.2 4.1 4.8 4.4 6.3 4.3 3.4 4.8 6.6 4.7 6.2 4.1 4.9 6.0 6.0 5.9
##   [73] 4.4 4.4 4.5 4.9 4.7 3.3 6.4 6.0 3.9 5.9 4.3 5.6 4.1 5.3 5.4 3.6 4.4 4.8
##   [91] 4.5 4.1 5.6 5.2 4.9 4.7 3.9 3.6 3.7 4.6 5.1 3.6 5.1 3.8 4.3 5.0 4.3 4.5
##  [109] 5.3 5.0 3.4 3.6 3.5 4.1 5.6 5.3 3.2 3.6 5.2 6.0 5.7 4.0 4.7 5.5 4.5 3.5
##  [127] 4.6 3.7 5.4 3.7 4.1 3.6 4.6 4.4 4.2 4.8 5.2 4.5 5.3 5.0 4.5 5.0 3.0 4.3
##  [145] 3.8 4.7 4.9 4.6 3.0 5.8 4.5 5.8 4.4 4.2 5.5 3.9 5.5 4.6 5.1 4.1 4.1 3.7
##  [163] 6.6 3.4 4.4 5.8 3.9 3.6 2.9 2.4 4.9 4.2 3.2 3.6 4.5 3.8 3.9 4.5 4.7 3.6
##  [181] 3.7 3.5 4.1 4.2 5.4 5.3 2.8 3.7 2.8 5.1 3.4 3.8 6.0 5.3 5.7 5.1 4.8 4.4
##  [199] 2.7 5.1 5.5 3.3 5.3 4.2 4.1 5.1 5.1 4.8 5.1 4.0 3.0 4.1 3.9 5.5 5.9 5.7
##  [217] 4.2 4.7 4.7 4.1 4.2 3.1 4.8 3.7 3.6 4.5 5.7 3.6 4.2 4.6 3.9 4.5 4.1 3.6
##  [235] 4.2 3.8 3.2 4.8 6.2 4.2 6.5 4.7 3.6 3.3 2.0 3.8 3.1 4.8 4.6 6.3 3.7 4.7
##  [253] 4.5 5.5 3.9 4.6 4.9 3.6 5.0 3.9 4.5 5.2 3.9 6.2 3.4 5.6 5.2 4.4 4.3 5.1
##  [271] 6.1 5.7 4.3 4.3 5.0 4.3 4.7 5.6 4.1 4.2 4.5 5.2 3.3 4.2 5.3 6.4 2.2 3.5
##  [289] 5.0 4.3 4.7 4.5 3.3 6.3 2.8 3.8 4.1 5.5 5.2 5.0 4.4 2.9 2.8 4.8 4.5 5.5
##  [307] 5.2 5.4 3.7 6.2 5.9 4.0 3.3 4.8 4.6 5.0 3.5 5.3 3.2 5.9 5.1 3.7 4.6 4.1
##  [325] 4.3 4.4 2.7 4.5 5.1 4.6 4.0 4.2 4.8 3.6 3.1 3.9 5.2 4.7 4.8 3.5 3.8 3.6
##  [343] 5.2 4.5 4.3 6.3 3.9 5.2 3.5 4.0 4.9 4.9 2.5 4.4 3.7 5.5 3.2 3.8 4.5 2.6
##  [361] 3.7 5.4 3.8 4.9 3.9 3.8 3.6 3.9 4.4 4.8 5.6 4.3 5.9 5.1 4.4 6.4 2.6 4.2
##  [379] 5.4 2.1 5.0 4.8 4.4 5.2 5.1 3.1 5.2 5.2 3.5 4.6 4.4 4.9 5.2 2.9 3.2 5.0
##  [397] 4.1 4.8 4.0 4.5 3.2 5.1 5.0 5.7 3.2 5.5 3.3 5.5 4.9 5.1 4.2 5.6 5.1 5.0
##  [415] 4.4 4.9 3.8 2.9 4.6 4.3 4.0 3.8 3.3 4.3 4.6 5.2 3.3 3.5 6.1 3.5 4.0 4.9
##  [433] 2.6 4.6 3.9 5.5 4.7 4.8 3.7 4.3 5.0 4.0 3.8 3.8 4.1 5.9 5.5 3.4 6.4 4.4
##  [451] 3.7 4.9 6.8 5.2 4.8 2.9 4.4 5.5 4.9 4.1 5.5 4.0 2.4 4.9 3.8 4.6 5.6 5.1
##  [469] 3.3 4.5 4.5 3.7 3.4 5.3 4.5 5.3 4.6 5.0 4.6 5.3 5.8 5.3 3.6 6.6 3.2 4.8
##  [487] 5.5 4.0 5.4 5.1 4.8 5.5 3.5 4.5 3.4 5.2 2.8 4.8 5.1 4.7 4.3 4.5 4.7 3.5
##  [505] 4.9 4.2 6.2 4.7 4.3 5.0 4.4 5.5 5.6 3.8 3.4 4.6 3.6 3.3 3.8 2.7 3.1 5.1
##  [523] 3.6 3.9 4.4 6.0 4.5 4.1 3.2 4.8 3.6 5.0 4.0 2.9 2.9 4.7 3.4 5.1 4.9 5.4
##  [541] 3.2 5.3 5.5 6.3 4.8 4.2 7.1 4.0 4.4 3.4 4.0 5.8 4.5 2.8 5.0 5.3 3.9 3.2
##  [559] 4.3 5.1 3.5 3.6 4.3 5.9 3.8 4.8 4.7 4.6 3.2 5.2 5.8 5.6 3.7 4.8 4.1 4.7
##  [577] 4.9 4.9 4.0 3.3 5.2 4.1 3.5 3.4 5.7 4.1 4.6 5.4 4.3 5.4 5.5 4.2 3.5 4.3
##  [595] 4.6 5.2 4.1 5.3 3.4 3.9 4.4 5.2 6.0 4.7 3.6 2.5 4.1 2.7 4.6 4.1 4.2 4.6
##  [613] 5.2 4.3 4.6 3.6 5.0 5.3 4.5 5.5 3.8 3.2 4.8 4.7 4.4 3.7 5.0 3.6 4.6 4.6
##  [631] 4.1 3.8 5.4 5.3 4.7 5.3 4.2 4.4 4.9 3.1 4.2 3.9 4.4 3.9 5.5 4.2 4.4 4.2
##  [649] 4.1 3.8 3.2 3.8 5.1 4.9 4.7 3.6 6.1 5.3 4.4 5.7 3.0 4.6 4.2 4.3 6.2 3.7
##  [667] 5.0 5.6 5.5 5.7 4.6 3.9 5.0 5.4 3.9 5.0 4.7 2.8 3.1 4.9 3.8 4.2 6.1 4.3
##  [685] 3.3 4.6 4.3 3.9 4.2 2.5 4.6 6.8 3.6 3.5 4.4 3.8 4.5 5.0 6.1 6.0 5.2 4.5
##  [703] 5.7 4.1 4.2 4.5 5.3 4.3 5.0 4.6 4.5 4.0 3.9 6.5 5.2 3.8 2.9 3.6 3.9 2.4
##  [721] 4.3 5.3 4.0 4.1 3.7 3.5 4.2 4.1 6.8 4.8 5.5 4.4 4.0 5.9 3.3 3.5 3.9 3.8
##  [739] 4.1 5.7 4.3 5.1 3.4 3.6 6.8 5.6 5.4 4.5 6.3 3.9 4.2 4.4 4.0 3.5 4.6 3.4
##  [757] 5.4 4.1 4.9 3.7 4.1 4.1 4.3 4.7 5.2 4.7 4.2 4.3 2.9 3.1 3.9 3.7 3.9 4.7
##  [775] 3.1 4.2 4.6 6.2 4.2 5.4 4.4 5.2 4.5 4.7 3.1 5.9 3.6 5.4 5.2 5.8 4.8 6.1
##  [793] 4.9 5.9 4.9 3.3 4.5 4.9 5.8 4.9 4.5 5.4 4.2 5.0 4.8 3.3 4.8 5.0 3.3 2.4
##  [811] 3.5 3.5 5.3 5.5 4.8 5.5 5.2 3.4 4.2 4.3 5.3 4.1 4.6 3.9 4.1 5.9 4.4 4.6
##  [829] 5.4 5.4 4.6 5.2 5.9 2.8 3.4 3.8 3.5 5.0 3.6 5.7 4.0 4.0 5.8 3.5 4.5 3.7
##  [847] 5.5 3.2 5.4 3.0 5.5 5.1 3.2 4.1 5.4 5.0 5.1 4.4 3.8 4.0 4.6 4.6 3.7 4.6
##  [865] 5.7 4.1 4.6 3.4 4.1 3.9 4.7 3.2 3.4 3.9 3.1 3.8 6.0 4.3 4.2 4.2 4.5 4.4
##  [883] 5.0 6.2 3.4 5.7 4.3 5.0 4.1 4.0 4.3 4.8 4.7 3.9 5.0 3.8 4.2 4.7 5.6 3.8
##  [901] 6.1 4.0 4.8 4.5 4.8 3.0 4.2 5.7 2.6 2.4 5.2 4.5 4.9 4.3 3.6 3.0 4.2 5.4
##  [919] 5.1 3.4 5.5 4.2 5.5 4.9 5.4 4.6 3.6 4.3 4.2 3.0 4.3 2.8 4.8 3.2 5.3 3.8
##  [937] 4.8 4.9 4.2 4.6 5.2 1.8 5.2 3.0 5.8 4.6 6.1 5.1 3.5 6.2 2.5 2.9 4.0 6.7
##  [955] 5.4 6.8 5.9 3.1 4.0 4.4 4.6 5.8 5.1 5.1 3.3 4.4 4.3 3.5 4.9 5.5 3.9 5.7
##  [973] 3.9 6.3 4.0 5.3 4.7 5.7 4.9 3.4 4.8 3.5 5.0 4.5 4.6 5.7 4.5 4.1 4.4 3.3
##  [991] 5.1 4.2 3.5 4.9 3.7 4.5 4.1 3.3 5.9 4.6
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.3 5.3 5.1 5.1 4.9 4.8 4.6 4.4
## 
## $jack.boot.se
## [1] 1.023523
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
## [1] 0.5176478
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
##   2.960880   5.627340 
##  (1.256714) (2.602743)
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
## [1]  0.8209875  1.0176188  0.4230330 -0.4253440  0.2876001  1.4131148
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
##    [1] -0.0233425951  1.1000768690  0.0186669092  0.6142207218  1.1798220922
##    [6]  0.0890714785 -0.2859253552 -0.0508555129  0.4034787137  0.5613610088
##   [11]  1.4286530278 -0.1333810907 -0.1145895666  0.0118383798 -0.0789069368
##   [16]  0.4116666839 -0.1707241924  0.2491739956  1.0560556334  0.1423454285
##   [21]  0.1601070385  0.7512058089  0.1803470599  0.2124730565 -0.0331856735
##   [26]  0.0639133527  0.5788029377  0.8131533803  0.3175754444 -0.6719855283
##   [31]  1.1134096627 -0.1021696954  0.1891903466  1.1355034040 -0.3567541366
##   [36]  0.1289952473  0.4234604582 -0.0349258201  0.8278037804  0.3146393604
##   [41] -0.2127811559  0.8502996978  1.1976122784  0.6775639966  1.4015919937
##   [46]  0.5798666063  1.0267525451  0.0772230190  1.4791001140  0.7063036212
##   [51]  0.8232552262  0.5861674276  0.1440396515  1.8654665735 -0.4209801557
##   [56]  0.3356020091  0.8352924331 -0.0857328668  0.6414675491 -0.9281645808
##   [61]  0.3602567195  0.8605867841  0.0029610016  0.6231391096  0.8946163956
##   [66] -0.1456107086 -0.1083806698  0.2610536375  0.2665994555  0.1820861555
##   [71]  0.0667015992  0.1932484224  0.1991832863  0.6481806574  0.5815443658
##   [76]  0.3146058282  1.4926212920  0.2998993302  0.2428568778  0.7674290088
##   [81]  0.8004768236 -0.2574995563  0.1263578446 -0.2536356342  0.3201382035
##   [86] -0.0344410498  0.1047622592  0.5260057148  0.4274986088  1.1959788510
##   [91]  0.5336362156  0.0714508062  0.1564280748  0.1007426831 -0.0790622195
##   [96] -0.3444962107 -0.3385802521 -1.7319392354 -0.4379597068  0.5421641737
##  [101]  0.7821046476  0.9488030016  1.0469424992  0.2826840752  0.3085189001
##  [106]  0.1758979957 -0.3085439902 -0.5047888278  0.2023101170 -0.0713509775
##  [111] -0.2266001791 -1.0223813709  1.0362120089  0.3190053838 -0.1124215852
##  [116]  0.4043834907  1.2051674725  1.3823378287  0.5088967933  1.2876267580
##  [121]  0.7869694986  0.5786842917  0.5922586477  0.4270764757  0.7158681129
##  [126]  0.2698774708  0.2128755793  0.8784102659 -0.0927082563  0.4914911646
##  [131]  0.8897479073  0.5810673149  0.2087481727  0.3905963682 -0.4588516201
##  [136]  0.7429507279  1.6417936640 -0.0440435779  0.2555491143  0.2520431756
##  [141] -0.3815426397  0.7040079108  0.1117401986  0.1348303410  0.4644486691
##  [146] -0.2390933478  0.0415705576  0.4083888223  0.8418774207  0.2177459121
##  [151]  0.1501094987  0.8700862005  0.4091450073  0.4210605631 -0.1383553330
##  [156]  0.3816295084  0.9998953172  1.1591789030  1.1489248098 -0.3152097093
##  [161]  0.0491613019  1.1912675961  1.9057397247  1.2599525473  0.1105806240
##  [166]  0.4780530312  0.7249969972  0.4926320369  0.5180395803  0.0183712993
##  [171]  0.9497628105  0.5231864231  0.7564715601  1.1410836603 -0.6790558331
##  [176]  0.2971430676  0.1553942566  0.1087815575  0.1814580815  0.2088817416
##  [181]  0.2941563187  0.4355090822  1.4994971010  0.4281989885 -0.2076277534
##  [186]  0.9797323200 -0.0813961314  0.1184647826 -0.4209025147  0.1043658550
##  [191]  0.3329201153  0.7258568504  0.1253593448  0.3764096761  1.1143168456
##  [196]  0.5376798733  1.6458836689  0.4642550308 -0.0863632320  0.7793941464
##  [201] -0.4258544786  1.3083599216  0.9331453294  0.0310044647  0.5496958648
##  [206] -0.1140913908  0.6245483587 -0.0418488746 -0.2258088284  0.2944009913
##  [211]  1.4965895486  0.5414624396 -0.1241039711  0.0878157322  0.9505514095
##  [216]  0.1864807580 -0.2966509086 -0.0754068102 -0.0917553157  0.5088347372
##  [221]  0.5942233655  0.0299139163  1.3981154430  0.1253593448  0.3878300548
##  [226]  0.1131386128 -1.2720210713  0.9617358973  1.3989244299  1.1064260950
##  [231]  0.3718940503  0.7159435944  0.6752780039  0.1980006275  1.3672351689
##  [236] -0.7356949513  0.9571646738  0.9151676576 -0.2608430616  0.3205768775
##  [241]  0.1928552745  0.6744960283  0.0342507168  0.4821532909  0.9570876133
##  [246]  0.1052652240  0.2695231126 -0.0916777917 -0.0775975978 -1.0087052573
##  [251] -0.5670727516  1.1118731483  0.8708623363  0.1689448792  0.6222226530
##  [256]  0.3559228875 -1.1656576347  0.6665890786  0.1377057047  0.4375804860
##  [261]  0.5219358470 -0.0440345824 -0.1150770815  0.5313262725  0.9188309645
##  [266]  0.2268057022 -0.2055767485  0.9206074155  0.7223131746  0.8836843731
##  [271]  1.2708974168 -0.0690968649  0.5747721206  0.7727008642  2.4548910365
##  [276]  2.4186674742  2.4550943750  0.3064793610  0.9907649359  0.4596805234
##  [281]  0.3420452112  0.4434039872 -0.3453310648 -0.2041496527  0.8529142698
##  [286]  1.4228305472  0.9094459544 -0.5113465409  0.2247400598  1.8769892848
##  [291]  0.8015617575  0.6470764033  0.7709994325  0.8248556664  0.5908744338
##  [296]  0.3376722701  0.5595924394  0.2467005814  0.5431023302  0.7155658543
##  [301]  0.5061311010  1.0178592255  0.3575443379  0.4657621369  1.0554085603
##  [306]  0.0680871793  0.0779804834 -0.0880377102  1.2413197023  0.5337364184
##  [311]  0.1623290782  1.5804940196  0.8198939146  0.5875348439  0.0388979198
##  [316]  1.2798265399  0.4439655728  0.7186866443  0.6930287697  0.3244539839
##  [321] -0.2780791152  0.5039233198  1.0617759946  0.2384436083  0.7907621950
##  [326]  0.0975761713  0.8352924331  0.2264293423  0.0127252459 -0.1382961500
##  [331] -0.0315670002  0.8142637817  0.7795196707  1.3907500887 -0.0073249188
##  [336]  0.1110978155 -0.2251365815  0.5419427194 -0.3827331613  0.4588314048
##  [341] -0.1020682926  0.1501094987  0.8841006550  1.0864239819  0.9709940487
##  [346] -0.4600079643 -0.5347413441  0.4390075137  0.0660512419  1.9157017369
##  [351]  0.2800189342  0.5648737920  0.0004191127  0.2158950753  0.6825823134
##  [356]  0.2565331804  0.0074471636  0.8284617105  1.1544444350  0.5744455348
##  [361]  0.4808401203  0.6858312529 -0.0683148422  0.6578804850 -0.1425514958
##  [366] -0.1977699182 -0.7503009909  0.4243625929  0.3636488792 -0.1226453740
##  [371]  0.7112186757  1.2404395098  0.8500541188  1.7642077075 -0.3834856056
##  [376]  0.5991094184  0.3340572874  1.2516534094  0.0247811989  0.4745144029
##  [381]  0.4821639035  0.5926121832  0.8502564644  0.1193705357  0.3571697412
##  [386]  0.7528379690  1.4328700184  0.6117933195  0.0829487607  0.0351693811
##  [391]  1.4388974468  0.5862451636  0.0549449842  0.6067532776 -0.1603849255
##  [396] -0.3019060495  1.1063454519  0.4440803615  0.2101671095  0.0532688546
##  [401] -0.1972686842  0.0350141384  0.3951852148  0.2704576467  0.5223082962
##  [406]  1.0636321814  1.2591692335 -0.0624027616  1.2436075014  0.1275223018
##  [411]  0.5017754702 -0.0830919243 -0.1781493517  0.3289726872  0.5991094184
##  [416]  0.4196060842  0.5774024730  0.4409139339  0.5092999245  0.8648058724
##  [421] -0.2290350100  0.1828805128  0.0485313624 -0.1285398872  0.2688325659
##  [426]  0.7309528544  0.4752709278  0.1825925094  0.4657344350  0.3200511383
##  [431]  1.6809364818 -0.6139370198  0.8201837183  0.0335103936  0.5607588386
##  [436] -0.7820456331  0.0733083191  0.1693387746  0.1961312461 -0.3825602254
##  [441] -0.0877136016  0.4404369675 -0.1169865111  0.3575087494  0.3506004713
##  [446]  0.3356482806  0.2069340553  0.6384576516  1.2275655032  0.1748598124
##  [451] -0.3438295020 -0.1984886772  0.2733468325  0.1880530593  0.8237107670
##  [456]  1.4988925133  0.3949806974  0.5988271825  0.7013531225  0.4644348667
##  [461]  0.0811807923 -0.1535283818 -0.3997601361  1.0567355755  0.6022685139
##  [466] -0.0579011935  1.3571276116  0.2226591859 -0.1177759234  0.9705929801
##  [471]  0.0487868824  0.1206763031 -0.2766183855  0.5555304131 -0.3238300184
##  [476]  0.6090281415 -0.0427399161 -0.4693799150  0.7886299611  0.4099149614
##  [481] -0.0403596582  0.1074822474  0.8760598645  0.5172790868 -0.1900224181
##  [486]  0.0288711461  0.3213991752  0.6318791215 -0.5385722718  0.2741944104
##  [491]  1.8545537445  0.3268204599  0.4145970983  1.8843132119 -1.9228610044
##  [496]  0.5161989830  0.2367448746  0.6133940274  0.0926197480 -0.3366832325
##  [501]  0.7668737824  0.0780936000  1.0370042207  0.8664611203  0.6223907869
##  [506]  0.9030788949  0.3039632658  0.6478293644 -0.2144585411 -0.3604925563
##  [511] -0.2609411289  0.4630122330  2.4317325670  0.0162258437  1.3948742660
##  [516]  0.8227435019 -0.0101692762  0.4255029970  0.5241771672 -0.3305834123
##  [521]  0.1473708459 -0.0554394534  0.7930783470  0.4300065157  0.4886549408
##  [526]  0.5387629311  0.9653322183  0.1129473161  0.3925425957 -0.1784513612
##  [531]  0.0330882071  0.3649377734 -0.4378146611  0.6497523139  0.3865996638
##  [536]  1.4088344894  0.8317882675  0.4945824596 -0.5429396238 -0.5888225408
##  [541]  0.9680222551  0.7399296020  0.2060879669  0.5349505882  0.4306417821
##  [546]  0.2869510477  0.2548753116  0.5991094184  1.0900447548  0.5309271002
##  [551] -0.0552389585 -0.0892149681 -0.0501606196  0.2569690518  0.2061132710
##  [556]  0.3811911756  1.0010972567 -0.3368101285  0.0721587075  0.4475689252
##  [561]  0.2213327479  1.7528601177 -0.1077135644 -0.2518007560  0.6220486813
##  [566]  0.2456777227  0.1295032321  1.0668650878  0.7059002593  0.4939785839
##  [571]  0.3386542308  0.3385947581  0.6815571006  0.8458961330  0.8818571901
##  [576] -0.0827508835 -0.0503549501 -0.1001645263 -0.4799422263  1.2313592373
##  [581]  0.5686403568  0.7945887033 -0.2419419156  0.8267488971  0.2320514804
##  [586]  0.6646248035  0.1929482432 -0.5159112846  0.8128538381  0.0364309608
##  [591]  0.1175338019  1.0132452939  0.4946487831  0.3021811113 -0.6755084479
##  [596]  0.9411310870  1.6291255467  0.8892416803 -0.2357432174 -0.1183500164
##  [601]  0.3259855893  0.3964392025 -0.1454216883  0.3418957842  1.1904659339
##  [606]  1.1284785737 -0.1585813245  1.1235220607  1.4332900013 -0.1038608506
##  [611]  2.0538282737  0.1135094192  0.5831394493  1.5152010363  1.0606287070
##  [616]  2.3208438169  0.1838256325  0.1428352919  0.5904848990  1.6392398998
##  [621]  0.1965795483  0.0428151819  0.2014415261  0.5938667958  0.2241711019
##  [626] -0.1831721097  0.7657808512  0.0979924542 -0.5510506812  0.2574376598
##  [631] -0.2038729653  0.0739555154 -0.2273923545  0.5198485911  0.0063312264
##  [636]  0.2413056554  0.8528263915  0.4186910394 -1.3163430220 -0.4209970616
##  [641] -0.1452041832  0.1512817019  1.0964544780  0.6282329834  1.3084213025
##  [646]  0.8375882812  0.0550936159  0.4506195712  0.3640230887  0.7907343965
##  [651]  0.5852015514  0.2333651635  0.0254353427  0.8284617105  1.1722394769
##  [656]  0.5059257794  1.0337921962  0.0222912813  1.2583430286 -0.2239246387
##  [661]  0.5794947015 -0.0094646676  0.2846152660  0.2343245227 -0.2555626210
##  [666]  0.1742269121  0.0683712082  0.3469779701  0.0040576657  0.7051431282
##  [671]  0.0127616280 -0.1676586800  0.4595747770  0.3894065358  0.9462371713
##  [676]  0.0512760445  0.5857086620  0.6591594046 -0.0764740613  0.5556468504
##  [681]  0.5797209538  0.6310310262  0.0570341013  1.3976037171 -0.5291805812
##  [686] -0.0316817369  0.0194333691 -0.4417433616 -0.8040716953  0.6783068889
##  [691]  0.3213539383  0.0244833020  1.0373314320 -0.2930568629  0.0763693578
##  [696]  0.7347666579  0.1221856734  0.9374794815 -0.1685099173  0.5310691350
##  [701] -0.6460644881  0.4004596794  1.4307545505  0.9933135799  0.5973479993
##  [706]  0.3324450368 -0.2134112652  0.0888924427 -0.3668600329  0.7411166126
##  [711]  0.9679935947  0.0272934700 -0.0697748170  0.1617667784  0.6035673698
##  [716] -0.3075113453  0.3155364995 -0.1309074892  0.5579506508 -0.4220287258
##  [721]  0.5960932974  0.1999570414 -0.0313018298 -0.1398417854  0.0972297128
##  [726]  0.0744220738  0.4594503218  0.7721025398  1.8449439745  0.7090375487
##  [731]  0.4907260314  0.5804567358  0.6945414722  0.0877711605  0.6410734118
##  [736]  0.3662030946 -0.3545506565  0.1279693974  1.2498139678  0.7586676711
##  [741]  0.1183786822  0.4926320369 -0.2348136575  0.0141785931 -0.3030746226
##  [746]  0.9366066451  0.3190184633  0.2647196525  1.3137520976  1.1684818878
##  [751]  0.2699528215  0.0217335818  1.0611404237  0.5953289052  0.1082664566
##  [756]  0.3081123610  1.1374013231  0.8046756720 -0.3857981321  0.5149614817
##  [761]  0.7561683249  1.2187596177  0.5795969430  1.0903859822 -0.2003478150
##  [766]  0.5408466254  0.1398753608 -0.2822029099  0.8703385738  0.1315294561
##  [771]  0.1732292714 -0.3555500445  0.0863431476  0.2864541345  0.7880048325
##  [776]  0.5082960146  0.3686299996  0.9603791672  0.0668364086  0.0055715282
##  [781] -0.0532070825 -0.0361195991 -0.1230208335  0.1829087177  0.3120382858
##  [786]  0.3378348589  0.1306219560  0.2435303780  0.5176478023  0.6481238384
##  [791]  0.2481526933  0.7240119703 -0.1276984641  0.1880182679  0.6971561207
##  [796] -0.1182561438  0.4185014697  0.3553688009  0.3296364956  0.2421937545
##  [801]  1.4667142731 -0.0479845579  0.1004279002  1.4565193639  0.4644606883
##  [806]  0.7351401573  0.6846485767  0.9105886904  0.4650374259  0.9366500283
##  [811] -0.2011273293  1.3302545543 -0.2168197821  1.3262524863 -0.1304142856
##  [816]  0.0775600199  1.1737433809  0.4287837047  0.1461313362  1.1304731227
##  [821] -0.0880963148  0.7512401528  1.3325417071  0.3771635710  0.9590647793
##  [826]  0.4121658159 -0.0618780362 -0.3738527705  0.6416479199  1.4136078813
##  [831] -0.0864349827  0.6458590948  0.9273846257  1.0123226892  1.1431045330
##  [836]  0.5405301524  0.2392008172  0.2587803653  0.0382379866  1.0305532165
##  [841]  0.2570250025 -0.0265893055 -0.0231358902  0.2650295353  0.6546319591
##  [846]  0.4138348775 -1.1833228912  0.9272131338  0.0871837595  0.5366765810
##  [851]  0.7400668854  1.0313086207  0.0152211422  0.2502319167  0.6484964989
##  [856] -0.0910480975  0.8389821984  0.1714749095  0.1807735783  0.2226014046
##  [861] -0.2875817342  0.5351988620  0.0923251276  0.4004285484  0.5668857384
##  [866] -0.1693475690  1.0507745262 -0.0971214352  0.8013138842  0.0391812150
##  [871]  1.3518244928 -0.0359867586  0.4822619768 -0.0258522427  0.2777785587
##  [876]  0.3551594938 -0.7864638750  0.2031617397  0.1280452986  0.5041124119
##  [881]  0.0069384009 -0.1009658939  0.2889766777  0.8989078344  0.3919860319
##  [886]  0.4022843719 -0.3244106885  0.3344819088 -0.1019802008 -0.2241853148
##  [891]  1.4470537266  0.0368278003 -0.4649799264 -0.0050125281 -0.7505511275
##  [896]  0.3761016780  0.5584992551  0.0072202235  0.5537955671 -1.2645840152
##  [901]  1.3005789430  0.0587344666 -0.6935759137  0.4781786920  0.2178673091
##  [906] -0.2179524108  0.9890459836  0.9105315889 -0.1316104979  0.2008460393
##  [911]  0.6448581250  1.5560111925  0.5598087183  0.3857634834  0.7250105479
##  [916]  0.3903129891  0.7708814740  0.4801487670 -0.0344899698  0.2062549240
##  [921]  0.6201285400  0.5656817365  0.6472128981  0.6355146335 -0.0261153323
##  [926]  0.5458797742  0.6271771452  1.1413616903  1.1243828941  0.8566560880
##  [931] -0.5903856287  1.1064038325  0.6046744511  0.8109936376  0.9329680025
##  [936] -0.1329574425  0.4435683407  0.7407950230  0.4614259281  0.8459881303
##  [941]  0.8087098092  0.4366292273  0.2928081034  0.0898768331  0.2895929445
##  [946]  1.0638134304  1.4365891622  0.0603168519 -0.3500407442  0.6074366120
##  [951] -0.2449472089  0.0648188167  0.2600169697  1.0388985375  0.3887303740
##  [956]  0.3789263367 -0.1016254481  0.1282376837  0.9759011379 -0.5338630722
##  [961] -0.0701429458  0.3387318724  0.2060879669  0.7040346022 -0.4087164000
##  [966]  0.9134037435  0.4954286626  1.2759449409  0.0443942083 -0.1772571886
##  [971]  0.0323761289  0.6535257671  0.8502549404 -0.6081135154  0.1020265064
##  [976]  0.4276664414  1.2617447969  0.4634811804  0.4167420046  0.2191673041
##  [981]  1.2802453898 -0.0308739959  0.8720811652  0.8141395005  0.2478618542
##  [986]  0.3717845503  1.6394789238  0.4008135765  0.2264293423  0.2214459805
##  [991]  0.5956078467  0.2992464721  0.7629624844  1.1558779756 -0.0397560576
##  [996] -0.5670727516 -0.1591022652 -0.0503641541  0.4385729371  1.1416809694
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
```

```r
fit2
```

```
##       mean          sd    
##   0.52615974   0.28292824 
##  (0.08946976) (0.06326208)
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
## [1]  0.34564972  0.43492362 -1.02027693  0.28651676  1.21756410 -0.09871132
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
## [1] 0.0517
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9093757
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
## t1*      4.5 0.01391391   0.8922381
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 4 8 9 
## 1 1 3 1 1 2 1
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
## [1] -0.0113
```

```r
se.boot
```

```
## [1] 0.9098957
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

