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
## 0 3 4 8 9 
## 2 2 2 2 2
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
## [1] 0.0062
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
## [1] 2.77584
```

```r
UL.boot
```

```
## [1] 6.23656
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.1
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
##    [1] 3.8 3.4 4.5 4.4 4.5 5.1 4.3 5.7 4.6 3.3 3.9 5.8 6.4 4.6 4.5 3.5 5.1 5.7
##   [19] 3.9 5.3 5.0 3.8 5.1 4.9 3.4 5.4 3.2 5.1 5.2 5.6 5.0 2.9 4.3 2.9 4.5 3.0
##   [37] 4.3 3.9 5.8 4.3 5.4 4.6 4.7 4.3 4.3 5.8 5.0 4.4 5.4 3.9 5.3 2.9 4.0 5.2
##   [55] 3.6 6.2 4.5 4.2 3.9 3.6 4.0 5.4 5.9 4.4 4.4 5.5 4.8 3.6 5.3 3.2 6.1 4.5
##   [73] 6.1 2.7 4.9 3.4 5.2 3.5 5.3 6.0 6.0 5.0 6.7 4.7 3.0 4.6 3.7 4.3 4.4 3.7
##   [91] 4.8 3.5 5.2 4.4 4.8 3.9 5.1 5.9 4.3 3.5 5.2 4.1 4.8 4.3 5.2 5.0 4.3 3.0
##  [109] 4.2 4.2 5.1 5.5 5.9 5.3 4.9 4.5 3.7 4.6 3.8 4.5 3.5 3.2 3.6 4.9 5.7 5.0
##  [127] 4.9 4.9 4.4 3.4 4.2 4.4 3.0 3.3 4.7 3.3 5.1 5.0 3.5 5.6 3.3 4.6 4.4 3.3
##  [145] 4.9 3.8 4.9 3.7 4.9 5.2 4.7 4.5 5.1 3.8 5.2 3.8 3.1 4.3 6.6 4.8 4.8 5.3
##  [163] 2.9 4.7 5.7 4.8 4.6 4.3 4.8 3.3 3.8 5.9 3.9 5.9 5.2 5.6 3.0 4.7 4.9 3.3
##  [181] 4.2 4.0 3.8 5.4 4.6 3.8 3.9 5.5 3.8 3.0 5.6 6.1 6.5 3.7 4.9 4.7 5.9 4.7
##  [199] 6.7 4.6 2.5 4.4 5.0 4.0 5.2 3.4 4.1 5.0 5.7 5.7 2.6 3.5 5.7 5.8 5.9 4.5
##  [217] 5.4 3.5 5.5 4.2 4.3 3.9 5.7 4.1 5.7 3.4 3.5 4.7 3.7 6.9 4.8 3.4 4.6 6.5
##  [235] 2.7 4.3 3.9 3.0 3.1 4.2 3.7 5.8 5.5 3.2 4.5 5.0 5.6 4.3 3.9 4.6 3.0 6.1
##  [253] 3.8 4.8 5.4 4.7 4.3 4.1 3.9 4.8 5.2 3.7 4.5 4.5 4.8 3.9 3.7 4.2 4.0 3.1
##  [271] 6.0 5.7 5.3 2.9 5.5 5.7 6.0 4.9 4.3 4.1 4.4 4.7 4.0 4.3 5.0 5.7 4.2 5.0
##  [289] 4.6 5.3 4.7 4.0 4.1 5.4 4.6 3.7 6.4 3.5 5.9 3.8 5.1 4.2 4.1 4.3 5.9 5.6
##  [307] 5.8 3.9 3.8 4.2 4.5 3.5 5.4 4.6 5.5 4.4 5.1 3.7 4.6 5.8 4.1 5.1 3.9 4.2
##  [325] 4.4 3.7 3.6 4.5 4.4 5.4 3.3 3.5 3.7 3.8 4.5 5.8 4.4 3.0 4.7 3.8 5.5 6.2
##  [343] 4.1 3.2 3.2 2.9 3.9 4.1 3.8 4.1 4.4 4.3 5.8 2.4 4.5 3.3 6.1 5.0 5.2 3.0
##  [361] 4.8 4.7 2.8 4.1 3.7 4.1 6.0 4.7 4.5 3.2 4.9 3.6 6.1 3.9 4.4 5.5 3.7 3.7
##  [379] 4.0 4.4 4.8 3.6 4.7 4.3 3.9 3.6 4.7 4.1 4.9 5.2 3.9 5.1 4.5 4.8 6.0 4.3
##  [397] 4.5 6.1 3.6 5.6 5.1 5.0 4.4 5.2 4.4 3.5 4.7 5.3 4.0 3.8 5.9 4.1 2.8 4.9
##  [415] 3.8 5.9 5.7 5.3 5.1 4.0 3.7 4.3 4.9 5.9 3.8 4.4 4.9 5.4 4.3 4.5 4.1 6.0
##  [433] 5.4 4.1 3.8 4.3 4.9 5.0 3.8 6.2 4.1 4.1 3.9 4.7 5.4 4.6 4.8 4.8 4.8 5.4
##  [451] 4.6 3.0 5.2 5.1 4.4 3.2 4.2 3.9 6.4 6.2 4.5 5.5 5.4 4.2 3.9 5.1 3.6 2.9
##  [469] 3.5 4.4 4.6 6.0 4.2 4.3 5.8 3.4 4.9 4.7 4.0 4.5 3.7 5.1 5.8 5.7 4.6 3.7
##  [487] 4.0 2.5 5.1 3.6 4.7 4.4 1.8 4.9 4.8 3.9 3.0 5.1 4.9 4.4 5.0 4.5 5.2 4.0
##  [505] 3.4 5.1 6.3 4.3 4.7 4.3 4.8 4.7 5.7 4.8 5.0 4.9 5.5 4.7 4.7 5.5 5.3 3.1
##  [523] 3.7 5.8 5.3 3.7 5.6 4.0 3.6 6.1 3.8 3.5 3.5 3.1 4.6 3.3 4.5 5.3 3.5 5.0
##  [541] 4.7 3.7 4.7 4.2 4.5 5.9 3.5 3.5 5.2 3.7 4.0 3.8 6.4 5.4 3.2 4.4 4.4 4.9
##  [559] 3.3 4.9 5.6 4.8 4.9 4.0 3.9 4.6 3.9 4.1 5.7 5.1 4.7 3.7 6.1 6.4 4.7 3.9
##  [577] 3.8 5.9 5.9 3.4 5.4 4.6 5.7 4.2 5.4 4.6 5.1 3.9 3.8 5.3 3.6 3.5 3.6 4.8
##  [595] 3.7 4.2 4.3 4.6 5.4 5.0 8.1 4.7 2.8 5.3 3.8 5.7 4.3 5.1 6.2 3.7 4.4 3.4
##  [613] 4.7 3.9 4.3 4.7 4.6 6.5 5.4 5.2 3.0 4.2 4.2 5.2 5.0 5.1 5.0 5.4 5.0 4.6
##  [631] 4.3 3.7 5.6 4.7 5.5 5.3 3.8 4.3 4.6 4.2 4.7 5.1 4.4 2.7 3.4 5.6 4.7 2.5
##  [649] 3.9 6.3 5.7 5.3 5.6 5.0 4.7 5.3 5.2 3.7 4.5 3.2 4.3 3.6 4.2 4.1 3.6 3.3
##  [667] 2.5 5.4 3.8 6.0 5.6 3.8 3.5 3.9 4.1 4.8 4.2 5.5 5.1 4.1 5.2 2.9 4.2 4.1
##  [685] 5.0 5.0 3.4 4.4 5.1 5.0 5.5 4.1 5.1 5.1 4.9 4.8 5.7 4.7 4.2 5.3 5.1 5.3
##  [703] 4.8 4.4 5.2 3.5 3.2 5.1 5.5 3.6 4.5 4.2 4.0 5.3 4.3 3.7 5.3 3.9 4.5 4.9
##  [721] 3.4 4.0 5.1 5.7 3.6 6.3 4.6 5.1 5.1 4.4 2.8 4.3 4.6 4.7 5.6 5.4 4.1 3.1
##  [739] 4.7 2.9 5.5 5.3 3.1 5.2 4.4 6.0 3.5 5.8 3.9 4.7 4.3 4.3 4.6 4.2 2.7 3.4
##  [757] 3.6 4.9 5.5 5.0 5.2 4.9 4.6 4.4 3.7 2.7 4.6 4.6 4.1 4.8 3.7 5.4 4.2 2.5
##  [775] 4.4 2.6 5.7 4.3 5.9 4.9 3.7 4.0 5.1 5.3 4.6 3.1 5.3 3.4 4.7 2.8 3.4 4.0
##  [793] 5.0 6.2 3.4 6.1 3.6 3.4 6.2 4.0 3.9 5.5 4.3 3.8 3.6 5.0 5.3 4.0 3.7 3.5
##  [811] 5.8 4.1 3.9 5.1 3.9 5.1 6.0 4.7 5.3 4.4 5.5 5.8 4.5 6.3 5.0 3.4 6.5 2.8
##  [829] 4.3 3.4 5.0 4.7 4.8 3.5 4.5 5.2 5.3 5.6 3.9 5.3 5.9 4.8 4.9 4.7 5.2 6.3
##  [847] 3.8 4.4 5.9 2.9 4.5 5.3 4.0 6.0 3.9 3.0 5.9 3.3 4.9 4.9 3.6 4.6 4.5 5.9
##  [865] 3.9 4.7 4.0 4.2 4.5 3.9 4.8 4.2 5.5 4.4 4.9 3.7 4.6 5.9 5.8 5.7 5.5 5.4
##  [883] 5.4 2.9 5.0 2.9 4.4 4.7 4.6 4.6 4.8 4.5 3.6 3.3 5.8 5.3 4.6 4.9 5.2 3.3
##  [901] 4.2 4.7 5.0 4.6 4.6 4.1 5.2 3.0 5.5 5.3 4.1 3.8 6.8 4.4 5.1 4.3 3.3 4.9
##  [919] 3.2 5.1 3.7 5.3 3.8 3.7 5.4 3.5 4.2 2.9 3.2 3.7 5.7 6.2 4.5 5.2 3.7 4.6
##  [937] 4.6 4.1 4.3 5.0 3.2 4.7 4.2 4.3 4.8 3.6 3.8 3.9 4.3 4.2 3.6 4.3 5.0 3.4
##  [955] 3.3 4.7 5.3 4.2 5.3 2.6 3.9 2.8 5.2 3.9 3.6 3.9 5.3 4.9 4.5 4.4 4.3 4.8
##  [973] 4.6 4.3 3.2 5.2 4.4 4.4 4.0 4.5 4.0 4.7 4.7 5.5 4.4 4.7 4.3 4.2 5.9 3.5
##  [991] 4.9 4.6 4.0 5.0 5.0 5.4 5.6 3.3 3.4 4.0
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
##   2.9   6.2
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
##    [1] 4.4 4.8 3.8 3.9 3.5 4.7 4.3 3.3 4.9 3.5 5.0 5.2 4.5 3.2 4.7 4.7 4.6 3.0
##   [19] 4.1 4.4 5.8 3.5 6.0 4.4 4.0 4.1 3.2 4.0 4.3 4.3 5.2 5.2 4.8 4.7 2.3 5.1
##   [37] 4.4 4.9 2.8 4.6 4.7 4.1 4.0 4.8 3.5 6.0 5.1 3.7 4.8 5.8 5.5 4.1 5.1 5.8
##   [55] 5.1 5.6 3.9 3.9 3.0 4.7 5.1 3.6 2.5 4.6 3.8 4.7 5.7 5.5 5.0 5.7 4.1 2.6
##   [73] 5.6 5.2 4.7 4.2 3.3 4.3 4.8 4.7 3.9 2.5 4.4 4.0 4.3 3.0 4.5 4.3 4.1 5.7
##   [91] 6.6 4.2 5.1 4.3 4.1 4.4 5.3 5.3 4.0 4.2 4.0 4.9 4.4 4.3 4.1 2.4 5.0 5.3
##  [109] 3.7 3.4 3.6 2.8 4.7 3.6 5.1 4.5 5.3 3.7 5.1 5.3 4.0 5.7 3.6 4.7 5.2 3.8
##  [127] 5.4 5.4 5.2 5.4 4.2 5.0 2.8 5.8 2.7 5.5 4.9 4.1 3.4 5.3 4.6 4.8 4.2 3.4
##  [145] 4.6 5.9 5.1 5.1 3.5 5.0 6.2 4.7 3.6 4.5 5.4 3.2 4.3 3.8 4.4 4.6 4.0 5.5
##  [163] 4.8 4.4 4.9 6.3 3.6 4.3 4.8 4.3 5.4 3.6 4.2 3.1 4.3 5.3 3.9 3.8 4.2 5.2
##  [181] 4.1 5.4 4.5 4.7 3.7 5.4 3.9 3.9 4.9 5.2 5.8 3.9 5.2 4.8 3.3 4.0 3.3 4.2
##  [199] 4.6 4.9 6.2 4.6 3.9 5.3 5.4 4.4 4.1 3.2 3.6 5.1 3.6 2.9 3.2 4.2 5.6 4.4
##  [217] 3.9 2.9 4.3 6.6 5.0 5.9 6.0 3.7 5.9 5.5 3.8 2.7 3.6 4.1 6.1 3.5 4.0 3.6
##  [235] 3.3 3.9 5.1 5.0 4.5 4.1 3.6 4.7 4.7 4.7 3.9 4.7 5.1 4.5 3.7 5.6 2.9 3.0
##  [253] 4.8 3.4 3.7 3.0 5.4 4.9 4.1 3.4 4.7 5.1 5.7 3.7 3.8 3.8 5.1 4.4 4.8 3.2
##  [271] 5.7 4.8 3.5 5.2 5.4 3.7 3.0 5.4 3.6 5.7 6.1 4.5 3.0 4.5 2.9 4.5 3.3 3.7
##  [289] 4.1 2.3 3.9 4.9 6.2 4.5 5.7 5.4 4.8 3.7 4.5 4.3 6.0 4.8 5.3 5.1 5.0 4.7
##  [307] 4.3 6.1 5.2 3.2 4.6 2.4 4.4 3.2 5.8 5.2 5.2 3.7 2.8 4.6 6.3 3.5 5.4 5.2
##  [325] 3.2 4.6 4.6 3.7 6.1 4.9 4.3 4.3 5.4 6.2 4.2 4.4 4.5 5.5 5.0 4.3 4.8 3.7
##  [343] 3.9 2.8 6.8 4.0 4.5 4.8 3.7 3.5 4.0 3.7 4.9 2.5 3.5 4.5 3.1 4.4 5.5 2.7
##  [361] 4.6 3.7 5.8 4.3 5.5 3.4 5.3 4.6 4.4 4.0 3.1 3.9 7.1 4.8 5.7 5.1 4.6 4.9
##  [379] 5.6 5.5 5.4 4.2 4.4 3.2 5.0 4.6 6.2 4.8 5.2 3.4 4.6 4.6 5.9 3.4 3.6 3.1
##  [397] 4.0 4.4 4.3 5.6 5.8 3.9 6.9 4.9 5.0 4.0 4.4 5.4 5.4 4.4 5.3 3.8 2.2 5.5
##  [415] 4.2 5.9 4.7 5.3 4.1 6.8 3.3 4.9 5.8 2.7 4.7 4.0 6.8 4.9 5.1 4.1 3.6 4.1
##  [433] 4.8 3.4 3.2 4.0 5.7 4.6 4.2 4.6 3.1 3.9 3.2 4.0 5.8 6.2 3.2 4.4 4.6 3.4
##  [451] 4.0 2.8 5.9 6.5 2.7 4.3 3.9 3.2 3.9 3.6 5.7 5.2 3.5 2.8 4.0 3.8 3.4 5.3
##  [469] 2.4 4.3 4.3 4.6 2.9 3.8 1.9 4.8 3.9 5.7 3.5 5.3 5.5 5.4 2.7 3.9 5.9 5.7
##  [487] 4.1 4.9 4.8 4.0 5.0 4.8 5.0 3.6 3.2 5.8 4.4 4.3 3.7 4.9 3.7 5.1 4.0 3.3
##  [505] 4.5 4.6 4.2 4.0 5.8 4.5 4.6 3.3 4.8 3.5 3.2 3.5 4.8 4.5 3.5 5.0 2.2 3.8
##  [523] 3.7 5.6 3.5 4.4 4.6 6.3 4.1 3.2 3.5 4.4 5.6 5.1 3.1 5.1 4.9 3.0 3.7 5.9
##  [541] 5.5 3.8 4.7 4.9 4.0 4.3 4.5 3.9 3.8 4.4 5.4 5.2 4.0 3.1 5.1 4.9 4.5 5.2
##  [559] 4.9 5.3 4.0 2.8 4.0 4.1 5.2 4.6 5.1 6.3 5.3 5.4 4.0 4.2 3.2 5.0 3.5 2.9
##  [577] 6.4 4.6 4.4 4.9 3.4 4.2 4.2 4.2 4.8 3.7 6.3 3.7 5.7 4.8 4.3 5.9 4.6 3.6
##  [595] 5.4 3.8 6.3 4.7 5.8 4.0 5.0 6.3 4.2 5.7 4.6 3.1 4.2 5.7 4.3 4.7 4.4 5.4
##  [613] 4.8 4.9 4.7 2.6 4.2 4.7 2.8 2.7 5.9 5.2 2.8 5.7 3.5 3.5 1.7 3.7 3.6 4.0
##  [631] 3.6 4.2 4.9 7.0 5.1 5.1 3.8 3.5 4.9 5.4 4.3 2.8 4.5 4.2 5.3 2.9 5.1 3.3
##  [649] 5.3 4.3 5.7 5.9 5.6 3.9 2.9 5.3 6.6 4.8 4.8 4.3 5.6 3.3 4.6 4.4 2.1 4.9
##  [667] 3.0 4.6 4.1 5.2 5.1 5.0 4.2 4.5 6.5 4.1 4.0 4.0 5.6 3.9 5.0 5.4 3.6 5.1
##  [685] 3.5 2.5 6.6 4.1 4.0 3.6 4.5 2.4 2.7 3.8 4.8 4.9 5.4 4.2 4.2 5.0 4.8 4.8
##  [703] 4.1 4.6 4.5 4.7 4.5 5.0 3.7 3.6 5.4 5.2 4.8 5.1 4.8 2.9 4.1 3.4 2.7 4.8
##  [721] 3.7 6.5 3.6 4.2 3.9 4.7 2.2 4.6 4.4 4.5 5.1 4.9 5.4 4.5 4.0 4.0 3.8 4.8
##  [739] 6.0 2.8 5.7 5.8 3.0 3.2 2.5 5.4 5.1 4.6 4.7 4.7 4.3 5.3 4.9 7.2 4.5 6.0
##  [757] 4.0 5.2 3.6 5.1 3.4 5.7 5.3 4.1 5.4 4.0 5.5 3.9 4.1 4.5 5.3 5.8 5.3 4.8
##  [775] 4.7 5.7 4.2 5.7 4.9 5.8 2.4 5.1 5.7 4.4 5.3 3.8 6.0 4.4 5.3 5.5 4.3 3.5
##  [793] 4.0 4.3 3.6 4.6 4.1 2.9 4.0 5.9 4.7 5.0 5.0 4.7 5.0 4.3 5.7 4.5 4.1 3.0
##  [811] 5.0 3.7 2.7 4.1 4.6 4.9 4.2 5.5 4.6 2.4 5.1 5.4 3.7 4.0 3.8 4.2 5.4 3.5
##  [829] 3.7 5.8 5.4 3.4 3.9 3.9 5.4 5.2 2.9 4.8 3.6 5.7 4.8 3.8 6.4 4.1 3.6 4.6
##  [847] 5.2 3.6 3.8 4.2 6.2 4.8 4.9 4.6 4.5 5.1 5.7 5.1 3.8 4.9 3.6 4.1 4.6 6.0
##  [865] 3.8 3.6 4.3 6.1 5.7 4.5 4.4 4.2 5.2 2.8 5.0 5.7 3.6 2.4 3.6 4.4 4.8 4.3
##  [883] 3.8 5.1 4.9 4.2 4.4 5.3 4.1 2.6 5.1 5.2 3.4 4.6 3.5 4.8 4.1 4.7 3.7 4.8
##  [901] 5.1 4.6 3.3 4.3 4.3 3.8 2.8 2.5 4.9 4.2 2.7 3.6 3.9 3.9 5.6 4.8 5.7 3.8
##  [919] 3.4 6.5 4.4 5.2 4.0 3.4 4.6 6.0 3.7 2.8 4.5 6.4 4.7 4.7 4.3 4.9 3.6 4.6
##  [937] 3.3 4.1 5.6 3.2 5.2 3.5 5.8 5.4 5.2 3.2 3.2 3.8 5.6 3.2 5.0 7.1 3.8 4.7
##  [955] 3.9 4.0 5.3 5.8 4.1 3.7 4.2 4.1 5.2 3.4 3.1 3.7 4.3 3.8 4.2 5.0 3.8 5.1
##  [973] 4.2 5.0 5.1 4.3 5.5 3.7 4.1 4.9 5.0 4.2 4.4 3.6 5.4 5.0 4.3 4.5 5.3 5.1
##  [991] 3.8 4.5 6.8 3.4 4.8 4.9 5.4 3.9 4.4 3.3
## 
## $func.thetastar
## [1] -0.0445
## 
## $jack.boot.val
##  [1]  0.46204819  0.33017751  0.24756447  0.23896848 -0.04213483 -0.07842566
##  [7] -0.20488506 -0.34712991 -0.50512821 -0.50785340
## 
## $jack.boot.se
## [1] 0.9985197
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
##    [1] 6.7 5.5 5.7 4.8 5.2 5.1 3.9 4.6 4.1 3.5 6.3 3.6 4.5 3.7 4.2 4.6 6.6 5.2
##   [19] 5.7 4.6 3.7 4.7 5.1 5.0 3.5 4.5 4.2 5.7 3.8 3.4 5.9 3.8 5.7 4.0 4.3 5.1
##   [37] 4.8 5.3 3.2 5.6 5.1 4.8 5.7 4.1 3.5 5.9 3.2 3.4 3.8 2.4 5.5 4.6 3.0 4.5
##   [55] 2.6 4.7 6.7 4.1 5.9 4.0 5.2 5.6 6.4 4.6 3.3 4.3 4.6 3.5 6.4 3.0 5.8 4.9
##   [73] 4.8 4.6 5.0 5.6 6.2 4.6 6.4 3.3 5.3 4.2 6.5 5.2 3.1 4.7 6.1 5.9 3.9 4.8
##   [91] 5.3 4.5 4.7 3.4 3.7 4.7 6.2 4.8 4.4 3.6 3.2 5.5 5.1 5.0 4.3 5.7 2.1 4.9
##  [109] 4.5 4.9 4.0 3.8 3.3 4.4 2.9 5.3 3.3 5.1 4.5 5.5 4.5 4.9 5.5 5.1 2.6 4.3
##  [127] 3.8 4.8 2.9 4.7 4.0 3.1 5.7 4.5 4.0 4.7 4.3 3.5 5.1 4.4 4.3 4.8 4.4 3.4
##  [145] 5.1 5.3 4.6 3.6 3.6 4.2 5.0 3.6 3.8 2.6 4.1 3.6 3.9 5.4 3.0 2.5 4.6 5.1
##  [163] 3.2 4.2 3.9 5.1 4.5 5.4 5.1 6.0 4.7 5.3 2.4 4.1 4.5 5.1 4.9 6.3 4.8 4.6
##  [181] 6.0 4.6 3.3 5.7 3.2 4.4 6.3 5.2 4.4 5.9 4.1 5.4 4.2 5.8 4.9 4.4 5.5 4.6
##  [199] 5.2 4.1 5.3 4.7 4.2 5.5 4.2 3.3 4.3 2.4 3.1 5.8 5.2 5.4 3.7 3.8 4.9 3.0
##  [217] 4.2 4.2 4.0 3.9 3.9 5.3 6.1 5.8 4.4 4.8 4.0 5.7 4.8 2.5 4.8 3.9 3.1 4.1
##  [235] 4.0 2.6 6.3 5.5 5.3 3.4 3.2 4.4 2.9 4.7 5.8 3.5 5.0 4.5 3.4 4.5 4.7 5.2
##  [253] 5.0 3.0 4.7 3.9 4.3 3.0 5.2 3.2 4.0 4.6 5.0 4.9 3.7 4.7 4.3 4.2 4.8 4.9
##  [271] 4.8 5.0 3.6 5.1 3.0 5.4 6.2 5.2 4.7 3.7 5.4 2.3 5.0 4.8 4.7 4.7 3.0 3.8
##  [289] 4.6 4.8 3.6 5.0 3.6 5.8 4.2 3.3 4.3 4.7 4.8 3.2 3.1 4.9 5.0 5.0 4.9 6.1
##  [307] 4.6 3.8 5.1 2.3 5.0 5.9 4.1 3.3 4.1 3.5 3.9 5.1 4.4 2.9 5.2 5.6 3.6 4.1
##  [325] 4.9 5.0 5.3 4.2 3.5 3.7 4.4 6.5 3.0 4.3 4.6 2.6 5.2 5.4 2.5 4.0 6.0 4.9
##  [343] 4.9 5.1 4.6 3.9 6.4 4.3 4.6 5.1 4.7 4.4 6.3 3.3 4.3 5.1 4.3 4.6 4.0 3.8
##  [361] 4.2 5.1 3.9 4.1 5.2 5.2 4.0 5.7 4.6 3.8 4.3 3.3 4.7 3.7 5.5 4.3 4.9 4.3
##  [379] 5.2 4.8 5.3 6.6 4.1 4.9 4.7 5.6 5.8 3.1 6.3 2.8 4.9 5.2 7.3 4.4 4.5 5.2
##  [397] 4.0 3.2 5.0 3.2 6.5 5.2 4.3 4.9 4.2 6.3 4.4 4.7 4.4 4.7 5.2 3.5 4.9 7.9
##  [415] 4.7 4.9 6.0 4.7 4.7 4.5 4.7 4.1 3.6 5.5 3.6 4.4 5.2 4.9 3.0 3.5 2.6 5.8
##  [433] 3.7 5.7 4.1 4.3 4.3 3.7 3.9 3.2 7.2 2.7 4.3 4.1 3.1 4.0 4.0 4.9 4.0 4.2
##  [451] 5.9 4.3 3.3 3.2 4.1 4.3 5.3 4.1 5.1 4.2 4.2 3.8 5.3 4.6 4.3 2.4 4.8 3.5
##  [469] 3.6 5.7 5.1 5.2 5.2 4.6 3.8 6.2 4.4 5.5 3.2 3.5 4.1 5.9 5.6 4.8 4.8 4.1
##  [487] 4.7 4.6 3.9 4.4 3.7 3.4 4.2 4.3 3.2 5.4 6.2 4.7 5.1 3.8 4.3 5.5 4.2 3.4
##  [505] 3.4 3.3 3.0 4.2 3.4 4.7 6.0 4.8 4.4 4.3 5.3 2.9 2.9 5.5 4.9 6.5 3.8 5.5
##  [523] 3.6 5.0 4.4 3.6 4.3 4.7 4.9 3.5 4.8 4.4 5.3 3.3 3.4 4.1 5.1 3.3 4.6 5.5
##  [541] 2.3 5.5 4.8 5.6 3.8 4.6 3.8 6.1 3.9 5.8 4.5 5.0 5.4 4.9 5.9 4.6 4.2 5.1
##  [559] 4.3 5.2 3.8 3.5 3.8 2.4 2.8 2.6 6.0 3.6 3.9 5.9 5.0 4.8 4.6 4.3 3.9 4.3
##  [577] 4.0 3.8 3.1 5.8 4.8 4.4 2.6 4.8 4.1 3.8 4.8 4.9 6.5 3.4 5.1 5.7 5.4 5.6
##  [595] 5.0 4.3 4.2 5.0 3.5 5.4 4.6 5.1 3.2 5.6 3.6 4.0 4.5 7.1 4.1 4.2 5.1 3.6
##  [613] 3.7 4.4 4.8 3.0 3.6 2.7 2.7 5.2 3.4 4.2 2.7 4.5 4.9 3.7 5.8 3.5 3.6 4.1
##  [631] 5.7 4.2 3.5 3.2 6.5 4.9 4.3 3.6 2.6 5.3 5.5 5.0 4.8 4.0 3.2 3.5 3.4 4.7
##  [649] 5.9 6.3 4.9 3.6 5.6 4.2 4.0 4.9 4.9 4.8 4.2 5.5 4.2 4.4 4.4 2.2 5.5 4.1
##  [667] 3.1 4.3 3.8 4.2 5.0 4.3 3.5 4.1 4.4 3.3 3.6 3.5 4.2 4.4 5.2 4.5 3.5 3.2
##  [685] 4.7 5.8 5.2 4.0 3.0 4.6 4.1 4.1 3.6 5.4 4.5 4.8 2.9 4.7 3.3 4.2 4.6 5.2
##  [703] 3.7 4.6 4.0 4.1 5.1 6.3 4.5 2.9 3.7 6.3 4.6 4.8 3.3 4.6 4.8 5.7 3.7 3.2
##  [721] 3.4 4.5 3.1 5.0 5.9 5.3 3.7 5.7 5.4 4.2 5.0 5.1 2.8 4.2 3.8 4.3 3.2 4.8
##  [739] 4.2 4.1 3.9 4.2 4.3 5.3 4.4 4.4 5.0 3.6 4.1 2.7 4.9 5.5 5.5 5.6 6.1 4.5
##  [757] 4.1 4.9 4.2 4.7 5.6 4.7 5.6 5.4 4.1 4.3 4.4 4.3 5.4 5.8 4.6 4.8 5.3 5.1
##  [775] 5.4 3.7 4.2 5.6 4.7 3.9 4.8 3.9 5.2 2.7 4.8 3.6 4.7 4.7 4.1 3.7 3.3 5.1
##  [793] 3.6 3.9 3.8 2.5 4.0 5.5 5.1 3.7 4.2 4.4 5.2 5.4 4.8 3.5 5.0 5.1 4.3 3.0
##  [811] 5.6 4.6 4.8 4.1 4.1 4.5 4.5 4.2 4.6 4.0 5.6 4.7 4.6 4.9 2.8 4.6 5.9 3.7
##  [829] 4.0 4.7 3.2 4.2 3.9 3.4 6.5 4.4 3.8 3.9 4.7 4.0 4.3 5.0 4.9 3.0 3.9 4.4
##  [847] 4.6 4.0 4.8 6.1 6.0 5.9 4.3 6.4 2.3 4.9 4.3 4.4 4.2 4.7 5.8 4.7 4.7 4.5
##  [865] 5.4 6.3 5.4 4.0 3.5 4.9 5.9 5.1 4.8 4.9 3.6 4.4 4.5 2.8 4.4 4.9 4.4 5.1
##  [883] 4.6 4.9 6.8 4.3 4.0 3.2 4.7 2.3 5.8 5.7 5.4 4.8 4.3 3.5 4.4 4.8 4.9 5.6
##  [901] 4.7 5.8 4.5 3.0 4.0 4.1 3.5 3.2 3.9 4.6 4.7 4.3 4.9 4.6 3.6 2.9 4.1 4.4
##  [919] 3.4 3.6 3.4 1.6 4.1 4.5 5.2 4.2 3.6 3.9 5.1 4.5 5.6 5.3 3.3 4.0 6.4 3.5
##  [937] 4.0 4.8 5.1 3.6 5.3 4.8 4.9 6.3 6.4 2.9 3.1 5.1 6.2 3.7 5.9 4.1 3.0 5.4
##  [955] 5.5 4.3 4.0 5.6 4.6 3.9 5.0 4.8 4.5 4.3 4.4 4.1 3.2 4.1 5.2 3.8 5.0 4.3
##  [973] 3.8 4.3 6.3 4.0 5.9 6.7 5.3 2.4 4.7 5.0 5.4 3.5 4.3 4.5 5.4 3.9 3.5 3.8
##  [991] 3.8 4.3 4.9 5.3 5.7 4.5 4.7 3.5 5.8 5.8
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.42 5.50 5.20 5.20 5.10 4.90 4.80 4.70 4.60 4.40
## 
## $jack.boot.se
## [1] 1.027543
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
## [1] -1.066861
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
##   4.497639   7.159457 
##  (1.941412) (3.269583)
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
## [1]  0.3444345  1.7045609  0.8475298  1.1143255 -0.1914404  0.5444422
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
##    [1] -1.953002836 -1.819561975 -1.870001820 -1.098411323 -2.541024211
##    [6] -0.698659519 -0.635102818 -0.744956693 -1.440900697 -2.145168214
##   [11] -1.295786406 -0.480120452 -0.955330682 -1.146676362 -0.745752422
##   [16] -2.419982570 -0.468793824 -0.970221538 -0.791079043 -0.331537029
##   [21] -1.261718716 -0.634937513 -1.073036865 -1.877481366 -1.262640849
##   [26] -1.049726623 -0.863009341 -0.950835660 -0.636706180 -0.969480057
##   [31] -1.854891333 -1.606746856 -2.433668983 -0.976564378 -0.845449165
##   [36] -0.564373098 -1.550716449 -1.775870543 -0.569126044 -1.143645388
##   [41] -0.905910122 -0.286028403 -1.055465135 -2.284025753 -1.386488472
##   [46] -2.214493676 -1.717258071 -1.292731160 -0.975775093 -0.641512081
##   [51] -0.110144576 -0.441445711 -0.399940736 -0.396779232 -1.249824335
##   [56] -0.917673898 -1.403144068 -0.775072267 -0.167330997 -0.669485899
##   [61] -0.795221013 -1.289782115 -0.889783061 -0.256189409 -1.481277470
##   [66] -1.315233511 -0.858368974 -0.048688509 -2.181313779 -0.627163965
##   [71] -0.449900769 -0.544319855 -0.640637962 -0.777287185 -1.375386543
##   [76] -1.264219866 -2.382602604 -1.467226178 -2.143970178 -1.034211998
##   [81] -0.231855058 -0.506345104 -1.703646671 -1.667760509 -0.864017975
##   [86] -0.344676620 -0.243021224 -0.837938160 -1.610142109 -0.038194836
##   [91] -2.466734996 -0.454330297 -0.389838419 -1.719909037 -0.815264429
##   [96] -1.084635430 -0.163334755 -2.459778995 -0.353888440 -1.632975131
##  [101] -2.626950467 -1.544175287 -1.628016103 -0.782156075 -0.780265783
##  [106] -0.398416732 -1.047501972 -0.780116983 -1.258182988 -0.735735891
##  [111] -1.738495600 -1.341595021 -0.741708414 -0.759280188  0.082817244
##  [116]  0.191637974 -1.203625257 -0.611346244 -0.607158637 -0.969264654
##  [121] -1.434458093 -1.255525491 -1.127621266 -1.221614173 -0.939617579
##  [126] -0.520894886 -0.780184266 -2.318399515 -0.569078202 -0.226936695
##  [131] -0.966404481 -0.092843447 -0.443943935 -0.403875759 -1.428713236
##  [136] -0.448946458 -0.798346208  0.017208733 -0.654758143 -0.759140565
##  [141] -0.822808799 -1.992233637 -1.048471156 -1.067206970 -2.193723905
##  [146] -0.604347692 -0.800959552 -0.775187164 -0.480016095 -0.384450227
##  [151] -0.976002455 -0.839631949 -0.382041670 -1.316606393 -0.466847550
##  [156] -2.192940672 -1.864601228 -0.599098233 -0.884751148 -1.328700177
##  [161] -1.406621858 -0.902088762 -0.242804776 -0.833329318 -1.358853477
##  [166] -0.220286453 -0.594514621 -2.365389241 -1.327976017 -0.976937687
##  [171] -0.678906439 -0.288674422 -0.066969840 -2.469596195 -1.083777890
##  [176]  0.301189455 -1.350848505  0.194832921 -0.618468704 -0.478434266
##  [181] -1.375732776 -1.154412085  0.066106605 -1.778906435 -0.816612949
##  [186] -0.368849742 -2.141038200 -1.421976368 -0.398470585 -0.699307145
##  [191] -0.954243493 -1.601425479 -0.611361094 -1.074473315 -0.826781760
##  [196] -1.465068956 -1.573922426 -1.465615512 -0.648405861 -1.400782601
##  [201] -0.252989114 -1.387062083 -1.350397922 -1.069144089 -0.585389043
##  [206] -0.334693016 -0.594144658 -0.366815393 -0.867915630 -1.363571458
##  [211] -0.348238730 -0.030564678 -0.870313266 -0.911059723 -1.238412470
##  [216] -0.896928097 -1.563530236 -1.335119533 -0.265517802 -0.891050812
##  [221] -0.673723376 -1.101654386 -0.392049244 -1.491394626 -1.588903302
##  [226] -0.521424910 -0.232355250 -1.522112868 -1.078783771 -1.915231899
##  [231] -2.353798562 -1.475340171 -0.908117905 -0.285162479 -0.513488708
##  [236] -1.826379889 -1.834381409 -1.660880297 -2.222177794 -1.282816081
##  [241] -0.358644155 -1.298039459 -0.286028403 -0.941257420 -0.526610636
##  [246] -1.001147504 -0.641708498 -0.480016095 -0.428519729 -1.069098191
##  [251] -0.951290524 -2.152049435 -1.167914836 -0.442467789 -1.395340720
##  [256] -1.578575195 -0.832147499 -1.703688935 -1.576450806 -1.893254744
##  [261] -1.360569476 -0.591444894 -1.040099328 -0.832644683 -0.621506080
##  [266] -0.176440013 -0.568772105 -0.776956800 -0.943033836 -1.716980338
##  [271] -1.662179449 -0.008000689 -1.042346555 -0.863149797 -0.884013642
##  [276] -0.358462438 -1.065523613 -0.138041390 -0.721453925 -0.040531934
##  [281] -1.337828196 -1.141396961 -0.465197527 -0.450418913 -1.393395569
##  [286] -1.744983146 -0.106834176 -0.928596218 -1.734451624 -0.813514039
##  [291] -0.843130437 -1.122365331 -1.455217379 -0.360309705 -1.008860432
##  [296] -2.376260730 -0.015025853 -0.933763869 -0.660091178 -1.057588095
##  [301] -2.240139806 -0.527699164 -1.595979634 -0.082115214 -1.824735697
##  [306] -2.204635028 -2.381422937 -1.386822556 -1.055851445 -0.932201777
##  [311] -1.392092710 -0.662805969 -1.242973844 -0.991092231 -1.554024273
##  [316] -0.381166182 -0.812743086 -0.843350767 -0.415187536 -0.818473535
##  [321] -1.582424568 -0.398321075 -0.099270832 -0.402116741 -1.971106215
##  [326] -1.896003502 -0.407420146 -1.618157569 -0.680946629 -0.558392034
##  [331] -1.659733003 -1.283641050 -0.920434065 -1.150564150 -0.481466217
##  [336] -1.139405981 -0.378612640 -0.159530837 -0.735746215 -0.760421950
##  [341] -0.746346479 -1.700292655 -2.130978191 -0.309316233 -1.056400326
##  [346] -0.501197857 -1.372419502 -0.794333111 -1.485595499 -1.468505671
##  [351] -0.975062274 -0.677233721 -2.591529486 -0.105865555 -0.884995523
##  [356] -1.877546407 -1.133905569 -1.352076825 -0.992384939 -1.487495778
##  [361] -2.036997251 -1.773218752 -0.622806030 -0.987525862 -1.557953685
##  [366] -1.701113156 -1.853854148 -1.310155128 -1.458015755 -0.166471551
##  [371] -1.852767925 -1.471364882 -0.638362282 -1.516285151 -0.990446292
##  [376] -1.511119634 -1.361323040 -2.195606910 -1.608398295 -1.373694128
##  [381] -1.737607866 -0.341699098 -0.800931516 -1.522112868 -0.269322466
##  [386] -1.165190262  0.547163061 -2.388570896 -1.384251671 -2.310509017
##  [391]  0.075035607 -0.028129981 -2.231126045 -0.384729889 -1.537183325
##  [396] -0.235546965 -0.746465990 -1.555303039 -0.645873895 -1.339690815
##  [401] -0.387077865 -1.986010782 -0.255981372 -1.091149293 -1.068775241
##  [406] -1.128122543 -0.416844202 -0.870892919 -0.634508381 -1.100982600
##  [411] -0.807204279 -0.155564043 -0.923665138 -0.200306630 -0.874849397
##  [416] -0.367270338 -0.725015294 -1.273408117 -1.113109941 -0.772928516
##  [421] -1.278962645 -0.586821259 -0.324507744 -0.612357151 -2.209979423
##  [426] -1.083918347 -0.582829678 -0.392993104 -1.206343663 -0.440029763
##  [431] -1.018077224 -0.929791287  0.303740953 -1.464480096 -0.241837916
##  [436] -1.414696294 -0.744934261 -2.204686453 -1.066861221 -1.488069179
##  [441] -1.011786182 -1.136897295 -1.268904572 -0.817819261 -0.848336644
##  [446] -1.074033777 -0.028900147 -1.395705731 -0.247910769 -2.080263196
##  [451] -0.244615560 -0.688147493 -1.316510805 -0.890922166 -0.790619313
##  [456] -0.962331246 -1.902502161 -0.421946782 -0.320517836 -0.801888365
##  [461] -0.160933683 -1.093439503 -1.161188768 -1.972121205 -1.032073616
##  [466] -1.648830994 -0.063107257 -1.605358795 -0.762131806 -0.771910302
##  [471] -0.392289359 -1.033296735 -1.337234144  0.020992673 -1.436642359
##  [476] -2.182723307 -0.266359650 -1.231243670 -1.005326202 -1.238065923
##  [481] -0.377313680 -1.487637914 -0.589758019  1.469367597 -1.225375077
##  [486] -0.507013461 -0.854117060 -0.203296945 -0.814319262 -0.256872509
##  [491] -0.886433929 -1.385107851 -2.403337671 -0.708624468 -2.086613358
##  [496] -1.383636174  0.098047400 -0.424030978 -0.863742185 -0.976554323
##  [501] -0.824686323 -0.390606593 -2.235094685 -1.503707960 -2.333052226
##  [506] -0.871522312 -1.128122543 -0.321467648 -0.994841691 -1.051658922
##  [511] -0.464049985 -1.704872262 -0.949375041 -0.642343932 -1.544616648
##  [516] -0.442174236 -1.100982600 -0.356945800 -1.046141391 -0.283838822
##  [521] -0.673766349 -2.116577368 -1.367310845 -0.887160852 -0.819879407
##  [526] -1.461135147 -1.104547461 -0.126167496 -0.617731921 -1.902299133
##  [531] -0.474745204 -0.902727119 -0.362225656 -0.461508879 -0.445254217
##  [536] -1.410186773 -0.654621345 -0.869378777 -2.155621744 -0.753671259
##  [541] -0.606373015 -0.213825074 -1.048053776 -1.315233511 -2.188637100
##  [546] -0.583609547 -0.473137825 -0.636550177  0.638187301 -0.635290479
##  [551] -1.269794755 -0.482773781 -0.795641258 -1.337367611 -0.225672082
##  [556] -0.590032810 -0.627454004 -0.998664461 -1.147389409 -1.830336969
##  [561] -0.202101660 -0.685766060 -1.789308950 -1.154617484 -0.240414001
##  [566] -0.846934026 -2.354627131 -1.236234534 -0.058376360 -1.506011790
##  [571] -0.387615074 -0.859455020 -0.929364774  0.674311283 -0.607975458
##  [576] -0.671281549 -0.764944713 -1.569039507 -0.440631720 -1.675721638
##  [581] -0.906954615 -0.836210218 -1.907588967 -0.726203793 -2.231803722
##  [586] -2.368078490 -1.721010988  0.098489890 -1.046482979 -0.585296529
##  [591] -0.522731379 -0.077319746 -1.413546088 -0.850929272 -0.242016041
##  [596] -0.239390016 -0.990775103 -1.497727638 -1.035315263 -1.152436838
##  [601] -0.880829532 -1.641161976 -1.421396370 -1.814828686 -1.407577639
##  [606] -1.580169913 -1.380584320 -0.990580622 -0.180806342 -2.220493657
##  [611] -0.955137330 -0.615346056 -1.163909784 -1.660507877 -1.289153488
##  [616] -1.829943143  0.241912654 -0.919784564 -1.070501316 -0.290612023
##  [621] -1.193571381 -0.251488927 -0.802880459 -1.360087576 -1.334555346
##  [626] -0.198975204 -2.144104435 -1.268100505 -1.411810888 -2.236208922
##  [631] -0.415568339 -0.605993870 -1.716685768 -1.299617140 -1.383499916
##  [636] -0.402690149 -0.929274440 -0.829385230 -1.299642182 -0.806214501
##  [641] -0.856034441 -0.907340057 -0.458623360 -1.379538500 -0.804893038
##  [646] -0.536433504 -1.148955705 -0.865695534 -0.936271885 -0.222020586
##  [651] -1.382367798 -0.409604785 -0.638685095 -0.815254419 -1.289546880
##  [656] -1.561824813 -1.372406466 -0.508460243 -1.237163041 -1.348539686
##  [661] -0.930139331 -0.516794295 -1.735316202 -1.499096231 -1.526551289
##  [666] -0.403265026 -1.580384154 -0.907609756 -2.602590349 -1.287190968
##  [671] -1.054205270 -1.311214045 -0.882883083 -0.444553338 -1.167562429
##  [676] -0.150842323 -1.618250136 -0.191387681 -0.296395713 -1.242144709
##  [681] -2.220280941 -0.891752763 -1.126535442 -0.747431311 -1.523081326
##  [686] -2.434372861 -1.270800430 -1.239377149 -2.344115475 -0.249093642
##  [691] -1.402675186 -2.187032587 -0.281744907 -0.012279278 -0.047159572
##  [696] -1.083929804 -1.605203081 -1.894245508 -1.404324089 -1.161005011
##  [701] -2.223618187 -2.349564961 -0.794435620 -0.298235638 -1.690470371
##  [706] -0.695452221 -0.888244711 -0.536282538 -1.359356402 -0.781122945
##  [711] -0.887259578 -0.140637192 -0.730419475 -1.586410534 -1.441571721
##  [716] -0.867699495 -1.881434569 -0.934111772 -0.269159586 -0.946604684
##  [721]  0.131355523 -0.442253940 -0.159777485 -0.167487785 -0.848918277
##  [726] -0.951416408 -1.743796111 -0.392686882 -1.442552010 -0.605746693
##  [731] -0.925484101 -0.416633014 -0.630759111 -0.430790424 -0.285826941
##  [736] -1.442026369 -0.813832104 -2.459149568 -1.547785026 -2.613497096
##  [741] -0.028643747 -1.313431089 -1.886549792 -0.535983022 -1.568504401
##  [746] -1.306181630 -1.308597130 -1.081574339 -1.376093330 -0.767190610
##  [751] -0.401269203 -1.894634390 -0.254296662 -0.942923439 -0.834848596
##  [756] -1.267468240 -2.180547635 -0.392721303 -1.063667393 -1.511859557
##  [761] -0.611937645 -0.679976192 -0.839341588 -1.851333317 -0.722281303
##  [766] -1.127265463 -1.153534643 -1.573723744 -0.792315479 -1.374307499
##  [771] -0.829529728 -0.391994496 -1.420114521 -0.795436272 -0.044009379
##  [776] -0.660238092 -1.609432702 -1.832345488 -1.728798890 -0.648399030
##  [781] -0.746085401 -1.344082660 -1.892914897 -2.035597744 -1.363871335
##  [786] -0.957070764 -1.846326431 -0.546511116 -1.879947978 -0.832991548
##  [791] -0.534662209  0.246687947 -1.149979297 -0.705242894 -1.094471870
##  [796] -2.469329192  0.454041253 -1.048389314 -0.928445427 -0.800959552
##  [801] -1.666472858 -0.541678867 -2.325250596 -0.637033870 -0.503190459
##  [806] -1.267731483 -0.855901273 -0.459624476 -1.671730200 -2.218638238
##  [811] -1.339163628 -0.372048156 -1.316640921 -0.935743122 -1.033940087
##  [816] -0.802348399 -1.917728129 -0.070398708 -0.842239197 -0.014817578
##  [821] -0.195021503 -0.652809018 -0.484103268 -1.078451037 -2.376728910
##  [826] -0.257143340 -0.427020999 -1.345793814 -0.933299929 -0.221384618
##  [831] -0.950722036 -2.193344419 -0.910194011 -0.017552213 -0.041148686
##  [836] -1.439960564 -0.045303480 -1.122365331 -1.073409040 -0.747623010
##  [841] -0.803239106 -1.292829121 -0.805740570 -1.138329789 -0.936502638
##  [846] -0.815831330  0.149549382 -0.767066258 -0.718844386 -0.640108914
##  [851] -2.117301366 -0.746553344 -0.568517044 -0.360820121 -1.622023643
##  [856] -0.537807685 -0.612000511 -0.461424672 -0.535475668 -1.066517015
##  [861] -0.686562280 -1.736247404 -0.807445172 -1.112802850 -1.444509484
##  [866] -1.790531313 -1.465751153 -1.716321699 -1.167933803 -0.827222613
##  [871] -0.707770164 -1.139026528 -1.157662057 -1.109218163 -1.770267344
##  [876] -0.205670853 -1.595533791 -0.975678093 -0.664045883 -0.803482417
##  [881] -1.144387874 -1.052186669 -0.579760126 -1.373008777 -0.798995256
##  [886] -1.172592957 -1.512486640 -0.988737662 -0.210217483 -0.834044986
##  [891] -0.442258356 -0.872724854 -0.560259417 -0.898358211 -1.796696694
##  [896] -0.223817010 -1.118307999 -0.859747959 -1.069345250 -1.082751117
##  [901] -0.792452587  0.103609505 -0.554566303 -0.605720333 -2.362049657
##  [906] -0.986530061 -0.936743435 -0.816096992 -1.042318413 -1.474477233
##  [911] -1.430635374 -0.302647167 -0.370385942 -0.638847838 -0.557971954
##  [916] -2.270174420 -0.860081667 -0.571333342 -0.768388365 -1.217804790
##  [921] -0.505009244 -1.701526008 -0.474236111 -1.774373413 -1.243359438
##  [926] -0.088939230 -1.608135029 -1.691110810 -1.113297090 -0.523289093
##  [931] -0.377266033 -1.442026369 -0.912061280 -0.493920367 -0.789406395
##  [936] -1.462637213 -2.583571748 -1.136791625 -1.825909688 -1.170002382
##  [941] -1.348246303 -1.731559975 -1.110742014 -0.784312646  0.138862503
##  [946] -1.881625653 -2.009195815 -0.250326663 -1.064105075 -1.740788138
##  [951] -0.975094355 -1.316773104 -1.837508916 -0.200960473 -1.363883713
##  [956] -1.372387803 -0.664823799 -2.487797357 -0.824094339 -1.873524206
##  [961] -2.334847026 -0.201783558 -0.291575791  0.133279128 -1.158120543
##  [966] -0.407343985  0.160385209 -0.466268162 -2.232620288 -1.385781251
##  [971] -0.283405733 -0.688488299 -2.239091776 -1.772524463 -1.131670696
##  [976] -1.648830994 -1.001261457 -1.360869524 -1.703386335 -1.783173775
##  [981] -0.394123760 -1.772057311 -0.364427350 -1.587992568 -0.802134748
##  [986]  0.357691689 -0.508238940 -0.646650771 -1.524612527 -0.323617168
##  [991] -0.282708500 -0.458069864 -0.911434736 -0.330591596 -1.351303320
##  [996] -1.073676059 -1.059394466 -1.375949730 -1.351798836 -1.154598926
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
##   0.62820821   0.22881088 
##  (0.07235635) (0.05115997)
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
## [1] -0.597259338  0.004432921 -0.918726874 -0.357112190  0.175446565
## [6]  0.648364444
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
## [1] -0.015
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9057458
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
## t1*      4.5 -0.01661662   0.8957952
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 4 6 7 
## 3 1 1 2 1 2
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
## [1] -0.0541
```

```r
se.boot
```

```
## [1] 0.8940148
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

