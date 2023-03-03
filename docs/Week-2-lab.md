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
## 1 2 3 4 5 6 8 
## 2 1 1 1 2 2 1
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
## [1] -0.0147
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
## [1] 2.70446
```

```r
UL.boot
```

```
## [1] 6.26614
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
##    [1] 4.5 3.2 2.8 4.7 3.6 4.5 4.4 4.5 6.1 4.2 5.9 5.4 5.4 6.2 4.9 4.4 3.9 4.1
##   [19] 3.9 5.8 3.4 5.5 3.4 5.5 5.6 5.8 3.9 4.2 4.4 3.7 4.3 4.2 4.7 4.8 4.3 5.8
##   [37] 4.5 5.5 4.1 5.3 4.0 5.6 5.7 4.8 3.2 4.6 6.3 4.4 4.0 4.0 4.0 4.0 4.1 5.0
##   [55] 4.9 6.0 4.9 3.1 5.4 4.3 4.2 5.4 2.9 4.6 4.3 4.4 5.3 4.8 5.8 4.1 5.4 6.0
##   [73] 4.1 4.9 4.5 4.5 4.7 3.5 6.2 4.8 5.0 4.8 4.8 5.7 4.7 3.9 5.0 4.5 5.2 5.5
##   [91] 4.8 4.2 3.6 5.1 3.3 3.2 5.8 3.6 5.1 5.9 3.4 6.6 5.6 5.9 5.9 4.0 2.4 5.2
##  [109] 4.6 3.5 4.6 4.0 3.9 3.5 4.8 2.3 3.6 4.7 2.6 4.5 4.6 3.3 4.8 3.5 3.0 4.2
##  [127] 5.2 5.0 4.3 5.3 4.9 4.0 2.6 6.4 4.9 5.1 4.0 5.6 3.9 5.2 3.8 4.1 3.1 4.4
##  [145] 3.2 4.1 5.2 3.7 4.3 6.0 5.8 4.2 3.8 2.5 4.1 4.4 5.2 4.9 4.8 5.5 4.9 4.0
##  [163] 2.9 3.3 5.2 3.7 4.3 3.9 4.9 3.8 4.8 4.1 4.7 3.6 4.1 3.6 4.5 6.2 5.1 4.1
##  [181] 3.8 4.8 3.7 4.9 3.1 2.4 5.9 5.6 3.2 4.1 3.1 4.6 4.4 6.1 4.9 6.6 5.4 4.1
##  [199] 4.4 4.9 4.6 6.1 3.1 4.6 5.6 4.7 5.3 3.4 4.5 6.3 5.4 4.0 3.1 4.3 6.1 3.2
##  [217] 4.6 6.3 3.4 3.0 5.0 5.0 2.0 4.6 5.6 4.2 5.1 4.8 5.0 4.8 4.8 3.9 4.9 5.9
##  [235] 4.0 4.1 5.1 4.8 4.2 6.8 3.8 4.0 5.1 4.9 4.1 5.2 2.8 5.2 4.0 4.5 4.5 3.7
##  [253] 3.5 5.0 4.3 2.2 3.9 2.9 4.9 5.6 5.3 3.8 5.9 4.9 3.6 4.6 3.6 4.7 3.8 4.3
##  [271] 3.5 2.3 4.2 4.7 4.8 4.2 4.6 4.9 4.2 4.1 2.9 4.7 6.1 3.9 5.0 3.7 3.5 5.2
##  [289] 5.5 3.1 4.4 5.2 4.0 2.7 5.5 3.0 4.5 4.8 4.5 4.2 6.0 5.1 4.4 4.1 3.9 4.9
##  [307] 4.8 4.0 4.8 6.2 4.2 5.0 4.7 5.3 4.0 5.0 4.8 3.2 4.1 3.4 4.5 3.2 4.9 4.8
##  [325] 5.1 5.2 4.7 4.2 4.1 5.1 4.9 4.4 3.5 6.3 4.0 5.1 4.5 4.7 4.1 4.6 4.5 5.1
##  [343] 4.8 4.5 5.2 4.7 4.7 4.3 3.7 7.0 4.9 5.0 5.1 5.2 3.2 5.0 4.3 5.7 5.1 5.0
##  [361] 4.7 4.1 5.5 4.1 4.1 4.2 5.4 4.5 5.2 4.4 4.5 4.6 5.6 5.2 4.9 3.5 7.5 3.0
##  [379] 3.3 5.4 4.5 4.6 4.8 3.8 5.0 6.5 4.3 4.4 3.9 2.6 5.3 3.1 4.3 5.0 4.0 4.7
##  [397] 4.7 4.9 4.1 3.8 4.6 4.9 4.0 5.2 4.9 4.9 4.4 5.1 4.7 4.5 4.8 5.9 3.4 3.9
##  [415] 5.3 3.8 4.8 4.5 4.1 3.6 3.5 3.9 5.1 4.6 4.2 6.6 3.5 4.7 2.1 6.0 5.4 4.6
##  [433] 4.6 3.3 2.5 6.5 4.0 4.7 6.7 3.7 4.0 4.0 4.7 4.8 4.1 3.7 5.4 4.6 3.9 3.6
##  [451] 5.7 3.8 3.9 4.1 4.6 5.0 6.1 5.7 4.1 3.9 4.3 5.0 4.8 4.7 4.5 5.1 5.9 4.8
##  [469] 4.0 5.0 4.9 4.0 5.6 2.8 5.5 5.2 4.4 4.4 3.1 3.8 4.3 4.8 4.4 3.3 3.5 5.8
##  [487] 4.6 5.0 4.0 2.7 5.1 5.3 3.7 2.7 5.7 4.4 5.6 4.4 2.7 5.7 5.1 3.4 6.0 3.7
##  [505] 5.8 3.6 3.2 4.4 5.5 4.7 5.8 5.0 4.6 5.5 3.5 1.9 4.6 2.9 4.6 4.0 6.8 4.2
##  [523] 3.4 5.8 4.6 5.0 5.4 5.4 4.6 5.7 3.7 4.8 4.2 5.1 5.4 4.1 3.9 5.6 3.5 3.2
##  [541] 5.2 4.5 4.4 2.9 6.0 5.8 3.4 2.7 3.8 4.4 5.1 3.9 4.4 5.0 3.7 5.7 4.3 4.2
##  [559] 3.6 5.2 3.5 5.3 4.9 5.0 5.6 2.7 4.0 5.4 3.2 3.3 4.9 3.2 4.8 2.8 5.0 3.8
##  [577] 3.5 4.9 4.7 2.9 3.4 3.7 4.9 4.5 3.9 3.2 3.3 5.0 5.1 3.1 5.2 2.2 3.4 3.6
##  [595] 3.0 5.9 3.9 4.1 4.5 5.4 5.6 4.5 5.4 4.4 3.8 4.2 4.2 4.0 4.2 3.0 4.1 5.5
##  [613] 3.8 6.8 3.2 6.2 4.0 5.1 3.5 4.3 6.1 3.4 3.5 5.9 3.3 3.4 5.3 4.6 4.9 4.5
##  [631] 4.5 4.8 1.8 4.4 4.5 4.7 3.3 4.7 3.7 4.7 3.6 2.4 3.7 4.1 4.3 4.7 4.2 5.7
##  [649] 3.6 6.1 4.8 3.5 4.3 3.5 3.1 4.6 5.4 3.5 3.8 4.9 4.8 4.3 5.1 4.0 4.1 3.2
##  [667] 3.7 4.6 5.4 4.2 5.4 5.8 5.5 3.2 4.6 3.4 4.1 5.3 4.4 5.5 3.1 4.8 3.1 4.5
##  [685] 5.6 2.7 4.8 3.9 4.6 5.5 4.2 4.3 3.4 3.9 3.8 5.1 4.0 5.3 3.9 4.2 5.7 5.5
##  [703] 5.0 6.0 5.7 3.7 4.6 6.1 4.4 3.8 4.1 5.3 3.9 5.5 4.8 3.9 3.5 4.5 5.5 5.4
##  [721] 5.5 5.1 2.0 2.8 5.2 4.5 4.3 3.7 5.3 2.8 5.4 5.4 4.1 4.3 5.4 4.8 4.0 4.3
##  [739] 3.6 6.3 4.2 4.1 5.1 4.0 3.9 4.7 5.8 5.9 6.5 4.2 4.0 4.3 3.5 3.0 4.1 4.2
##  [757] 4.1 4.6 3.6 4.7 3.9 5.9 5.8 4.7 4.9 4.4 5.1 6.6 4.3 4.2 3.5 4.0 4.2 4.5
##  [775] 5.0 5.4 5.1 4.6 4.6 5.8 3.5 4.9 4.4 3.6 3.2 6.1 4.6 5.6 5.3 4.2 5.7 5.5
##  [793] 4.9 4.8 3.7 4.3 4.9 6.7 3.9 3.8 3.8 4.5 4.1 2.3 4.0 6.6 3.0 4.8 4.3 4.5
##  [811] 4.8 4.9 3.7 6.0 4.8 5.1 2.4 4.7 4.5 4.6 4.5 6.7 2.7 4.2 4.5 5.6 4.8 4.6
##  [829] 5.9 4.1 4.2 4.5 3.2 3.9 4.0 4.1 5.4 5.0 6.2 4.0 4.9 5.3 5.9 3.9 5.0 5.0
##  [847] 5.2 3.4 3.1 3.8 4.8 4.6 2.7 4.7 3.8 4.4 4.8 4.1 4.3 3.9 4.1 4.2 4.9 4.5
##  [865] 3.1 4.7 5.2 3.7 3.0 2.7 5.2 3.6 5.7 3.5 4.5 5.6 4.7 5.2 3.2 4.6 6.0 5.0
##  [883] 4.5 4.8 4.5 4.3 4.0 3.7 4.8 6.7 4.6 3.6 6.8 4.0 4.2 4.5 4.0 4.3 4.4 4.0
##  [901] 5.3 3.8 5.6 5.3 4.8 4.3 4.4 4.6 4.2 4.5 5.5 4.0 3.0 5.2 3.9 3.7 4.0 3.3
##  [919] 3.3 5.0 3.4 4.2 3.9 3.2 6.6 4.4 3.3 4.0 4.0 4.3 3.1 4.9 4.1 4.2 4.5 5.7
##  [937] 5.4 3.6 5.0 4.0 4.9 5.6 4.5 3.9 4.3 4.0 4.6 4.3 4.9 2.6 3.1 3.3 5.2 2.7
##  [955] 4.8 3.5 6.6 6.1 5.3 3.8 4.6 3.8 4.8 4.6 4.8 6.0 6.2 4.9 4.2 4.2 5.7 4.2
##  [973] 5.3 4.5 6.4 5.3 4.9 5.5 4.7 3.2 3.0 3.8 3.1 4.4 4.4 3.2 3.6 5.4 5.4 3.0
##  [991] 3.2 5.3 3.6 4.8 6.2 4.8 2.5 2.8 4.2 4.3
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
##   2.7   6.3
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
##    [1] 2.6 3.2 3.9 3.2 4.2 4.2 4.0 4.9 2.9 5.8 3.6 4.3 4.2 5.8 5.0 4.9 5.2 6.7
##   [19] 4.3 4.6 4.0 5.5 5.2 4.3 4.4 5.4 4.6 4.8 3.2 4.1 3.9 3.4 4.2 4.9 4.1 3.7
##   [37] 4.0 3.7 4.2 4.5 4.2 4.0 2.9 3.4 3.3 5.8 2.6 3.5 6.3 3.7 5.1 5.1 4.7 5.1
##   [55] 3.8 4.1 3.9 6.4 4.7 4.4 5.6 5.8 3.1 4.9 3.6 5.4 4.9 4.1 4.0 5.2 3.8 3.0
##   [73] 3.4 5.6 4.5 5.1 4.1 5.5 4.8 3.6 4.2 3.9 5.3 4.5 6.3 5.1 4.8 4.9 4.4 3.4
##   [91] 4.5 4.2 4.4 5.2 4.2 5.4 4.0 4.9 3.8 5.5 4.8 3.1 4.9 5.5 4.8 2.7 2.4 3.5
##  [109] 4.9 3.2 7.3 4.2 2.9 4.1 5.4 4.1 4.5 2.8 4.1 4.9 3.5 2.8 3.4 3.3 4.7 4.6
##  [127] 4.2 3.7 3.4 4.7 6.0 3.9 4.9 4.0 3.9 2.9 2.6 5.8 5.8 5.4 5.4 5.5 4.3 4.0
##  [145] 4.2 5.4 4.5 4.4 1.9 2.9 4.5 4.7 3.8 5.1 4.2 4.3 4.3 3.9 3.0 4.8 3.8 5.0
##  [163] 2.8 4.8 5.3 5.5 4.8 3.3 4.5 3.9 4.3 6.4 5.2 3.1 4.7 4.7 3.6 5.2 3.3 3.8
##  [181] 5.4 4.3 4.8 5.7 4.3 4.4 5.3 3.8 4.7 4.4 4.1 3.7 3.5 4.0 4.0 3.8 4.1 4.2
##  [199] 5.4 4.5 6.1 3.4 5.0 3.5 5.8 4.0 5.8 5.1 4.9 4.7 4.7 4.6 5.1 5.7 4.7 4.5
##  [217] 3.8 5.1 4.9 3.1 4.2 4.7 6.7 5.0 3.2 5.7 5.4 4.8 4.0 3.8 4.9 4.2 5.5 3.9
##  [235] 4.0 5.3 4.5 4.0 4.1 3.6 3.8 5.1 3.4 3.5 3.9 4.0 4.5 3.8 4.8 4.2 4.7 4.5
##  [253] 3.1 4.1 4.6 5.2 3.9 4.3 4.4 4.2 5.4 4.8 4.9 3.9 5.0 5.7 4.0 4.6 4.4 2.8
##  [271] 5.4 2.4 4.7 5.0 4.7 2.9 3.2 3.6 3.8 4.7 4.5 4.5 4.0 4.0 4.2 5.9 4.9 5.9
##  [289] 3.2 4.1 3.3 4.1 5.0 4.2 4.9 4.2 4.8 2.3 4.8 4.8 4.4 5.1 5.1 6.9 4.9 3.0
##  [307] 5.4 4.4 4.2 4.0 3.0 4.2 3.9 4.9 4.2 4.0 3.9 4.4 5.0 5.2 6.1 3.8 5.1 2.7
##  [325] 5.3 4.7 5.7 3.8 5.2 4.7 5.0 2.7 4.0 5.0 4.9 4.1 4.5 5.6 5.2 2.5 5.2 4.7
##  [343] 4.4 3.8 4.0 3.2 5.0 4.7 4.6 5.3 4.7 4.2 3.9 3.9 4.3 5.5 4.5 4.6 4.5 4.1
##  [361] 5.2 5.2 2.7 4.7 4.5 4.3 4.3 4.2 4.3 3.4 4.0 4.7 3.1 3.4 3.5 5.5 4.8 4.9
##  [379] 4.3 5.5 3.7 2.4 4.8 5.0 5.3 2.4 4.4 4.7 4.1 2.3 5.3 4.2 3.3 3.4 4.4 6.3
##  [397] 5.0 4.0 5.9 2.5 4.9 5.3 5.8 5.4 4.9 3.6 5.7 5.2 4.9 5.8 6.3 4.2 3.4 3.5
##  [415] 4.5 5.4 6.9 6.1 4.1 4.4 4.4 4.0 5.0 5.2 3.3 3.6 6.0 5.6 3.8 5.2 2.4 4.5
##  [433] 4.6 5.0 4.7 4.7 5.5 4.2 4.3 5.3 6.2 5.1 4.6 4.8 4.9 3.8 4.6 5.9 3.2 3.5
##  [451] 6.3 4.0 2.8 4.8 6.3 3.8 5.8 5.3 5.1 5.5 3.9 5.0 4.2 3.5 5.4 4.1 3.9 3.1
##  [469] 3.9 3.2 5.8 4.2 3.9 3.9 4.0 2.9 4.5 5.2 2.6 3.3 4.6 3.5 4.9 5.6 5.4 5.3
##  [487] 4.0 4.2 5.1 5.1 4.7 5.5 4.1 3.7 6.8 5.6 5.2 5.2 5.1 4.2 6.1 4.7 3.3 4.0
##  [505] 4.1 2.8 3.8 5.6 5.9 4.6 3.6 5.6 4.5 6.3 5.9 4.1 4.9 4.3 3.5 5.1 6.0 4.0
##  [523] 5.1 3.5 2.6 6.4 5.2 5.2 3.3 4.3 3.6 4.9 2.8 5.8 5.5 4.7 3.2 4.2 5.4 3.6
##  [541] 3.7 5.7 3.9 5.5 2.4 5.4 3.7 5.9 4.9 5.7 3.6 1.8 4.7 3.0 6.0 3.2 5.7 4.8
##  [559] 4.7 5.1 3.9 3.0 3.7 4.5 5.1 4.3 3.4 4.8 5.0 3.9 4.1 3.5 4.0 4.2 3.4 4.0
##  [577] 4.5 5.1 5.3 4.0 4.4 4.3 4.7 3.7 4.3 5.4 4.8 5.2 1.4 5.0 4.5 4.3 5.0 4.6
##  [595] 2.5 3.0 3.8 3.6 5.1 3.9 5.1 4.9 3.6 3.0 4.7 3.2 6.3 3.7 3.2 5.3 4.1 4.7
##  [613] 4.1 5.5 5.7 4.4 5.0 6.2 6.9 3.2 5.6 2.8 4.6 4.0 2.4 4.5 5.6 4.4 5.8 4.9
##  [631] 4.9 4.6 4.7 4.7 5.9 4.5 4.8 5.0 5.0 2.6 3.4 3.5 5.2 5.1 3.9 5.6 2.8 5.4
##  [649] 2.9 4.7 6.4 3.8 4.0 4.0 4.0 3.0 5.7 4.8 4.3 4.0 4.1 3.7 3.6 5.2 4.4 4.4
##  [667] 5.2 6.9 4.7 4.7 5.0 2.9 2.9 4.6 4.0 2.6 3.6 4.3 5.5 4.0 5.6 5.8 5.7 5.1
##  [685] 5.9 4.7 5.7 4.5 3.2 5.2 4.3 5.1 4.6 4.6 4.4 5.7 3.7 4.4 4.3 4.8 4.1 3.2
##  [703] 2.4 3.9 5.2 4.6 3.6 4.1 4.7 3.5 4.3 4.3 7.0 2.4 4.2 5.3 5.6 6.0 4.0 4.9
##  [721] 2.9 4.0 4.6 5.4 2.6 4.7 5.0 2.8 4.3 5.1 5.4 2.9 3.8 2.9 2.7 4.6 4.3 4.9
##  [739] 4.7 5.0 6.0 4.8 5.9 5.5 5.2 6.7 3.3 5.9 4.8 3.8 4.5 4.5 5.7 4.1 4.5 3.9
##  [757] 4.3 4.5 3.2 4.8 3.2 4.2 5.1 5.3 5.6 4.0 5.6 4.9 3.6 4.0 4.5 4.7 3.9 4.9
##  [775] 6.0 4.1 4.0 5.2 4.3 4.8 4.3 7.0 4.2 5.2 3.6 3.9 3.6 4.9 4.0 3.9 5.1 4.4
##  [793] 3.9 3.7 5.1 4.5 4.9 4.8 4.6 4.2 6.4 4.3 3.1 4.5 3.7 5.5 4.4 5.0 3.8 3.9
##  [811] 4.8 3.0 5.3 4.9 2.9 3.0 4.7 3.6 4.0 4.8 4.0 4.8 3.1 5.4 3.7 4.5 4.5 4.1
##  [829] 3.5 3.1 5.0 5.0 4.6 4.5 4.0 4.2 2.6 4.2 4.1 4.9 3.6 6.1 2.7 4.7 4.2 4.6
##  [847] 6.5 3.9 4.1 3.9 5.0 4.2 4.2 5.7 4.9 3.5 2.6 5.9 4.2 3.2 4.9 4.0 3.8 5.8
##  [865] 4.8 4.7 4.3 3.8 3.7 4.9 4.0 4.1 4.6 3.6 4.7 3.9 5.3 4.1 3.9 4.4 4.9 4.0
##  [883] 4.0 3.3 5.2 3.3 5.4 5.2 4.6 4.3 4.8 4.6 4.3 3.5 3.0 5.2 5.6 5.8 4.4 3.9
##  [901] 5.2 5.7 5.1 4.7 5.7 3.9 4.9 3.1 3.1 3.4 4.1 4.0 3.7 5.4 5.5 4.6 3.6 3.5
##  [919] 3.6 3.5 4.7 5.6 4.6 5.2 4.6 4.5 2.8 4.3 4.9 5.8 4.9 4.7 4.4 3.8 3.8 4.1
##  [937] 5.1 4.3 4.9 3.9 6.0 3.4 6.4 3.9 3.9 2.3 2.9 6.2 5.7 6.3 3.9 6.1 6.6 4.6
##  [955] 3.9 3.9 4.5 4.8 3.6 3.3 4.3 4.9 4.0 5.0 6.0 5.2 3.4 3.0 4.6 4.7 4.8 5.1
##  [973] 5.3 3.8 2.6 4.5 3.5 4.1 3.8 4.5 3.5 4.6 4.9 5.7 4.0 2.7 6.2 5.5 3.8 5.6
##  [991] 3.1 3.5 4.5 4.1 4.8 4.2 2.8 4.3 3.7 5.2
## 
## $func.thetastar
## [1] -0.0608
## 
## $jack.boot.val
##  [1]  0.48734568  0.34881657  0.20142450  0.07162534 -0.00801105 -0.08668555
##  [7] -0.25610465 -0.28289086 -0.44225352 -0.55798969
## 
## $jack.boot.se
## [1] 0.9662997
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
##    [1] 5.6 4.7 4.5 3.9 2.1 6.1 4.6 5.6 3.1 4.2 5.4 4.4 3.3 5.3 5.3 3.4 5.0 4.5
##   [19] 4.2 6.1 3.9 4.3 4.5 3.3 5.0 5.4 4.0 5.3 7.2 2.6 5.4 5.1 5.3 3.9 4.0 4.4
##   [37] 6.5 6.8 3.4 6.4 5.3 4.0 3.4 4.9 3.0 3.5 5.3 4.7 2.3 3.3 5.2 4.2 3.8 4.7
##   [55] 3.5 5.5 5.5 3.9 2.9 4.4 4.2 5.5 4.4 5.5 3.6 5.9 5.4 3.3 4.1 4.7 3.8 5.2
##   [73] 4.1 4.4 3.6 2.3 5.4 4.0 3.9 4.9 4.7 3.7 4.7 2.9 4.2 5.0 5.4 5.4 2.7 6.1
##   [91] 3.1 4.7 4.5 4.4 4.7 3.4 5.0 6.1 5.1 4.9 3.8 4.6 4.3 4.5 4.6 3.9 4.2 4.1
##  [109] 4.8 4.4 3.7 3.6 4.3 6.0 4.6 2.7 3.9 4.9 5.1 5.7 3.9 5.8 4.2 5.1 3.8 3.4
##  [127] 5.3 4.7 4.6 4.7 3.5 5.2 5.4 2.7 2.1 4.8 4.5 2.9 4.7 4.0 4.5 5.0 4.0 6.6
##  [145] 5.1 5.3 2.9 4.1 5.5 4.6 3.9 2.0 5.3 5.4 4.7 5.3 5.0 4.3 5.9 6.9 4.5 3.2
##  [163] 4.2 5.4 4.3 4.7 2.5 5.4 2.7 5.3 4.7 4.5 3.5 3.7 4.6 5.1 4.7 5.4 3.3 3.8
##  [181] 5.4 4.4 5.4 3.8 4.1 4.4 5.9 3.0 5.7 5.7 5.3 3.7 5.3 4.3 3.7 3.7 4.3 3.2
##  [199] 5.1 4.8 6.4 5.4 4.8 3.6 3.0 4.3 4.2 3.4 4.9 4.0 4.7 4.6 4.2 4.1 6.4 4.1
##  [217] 4.4 5.5 4.3 4.8 4.2 3.2 5.9 6.3 5.7 3.5 5.0 3.9 3.4 1.8 4.9 3.8 4.5 5.7
##  [235] 5.5 4.6 3.3 4.6 2.5 4.3 4.5 3.3 5.8 3.9 4.1 3.0 4.9 3.0 6.0 5.7 5.7 2.0
##  [253] 4.6 4.8 3.6 3.5 4.9 4.6 4.4 4.5 3.9 2.5 4.1 4.9 3.7 7.1 4.4 5.5 5.1 4.1
##  [271] 3.0 6.2 3.5 4.8 3.7 3.6 4.6 4.8 4.7 4.5 4.0 3.6 3.9 4.4 4.8 2.3 3.8 4.3
##  [289] 3.7 5.4 4.5 4.4 5.7 5.0 4.9 3.2 3.8 4.8 5.4 5.1 3.5 5.4 3.9 4.1 4.1 5.1
##  [307] 4.0 4.2 5.3 4.1 3.5 4.0 3.7 3.2 3.6 4.5 5.0 2.8 4.9 5.2 6.4 2.2 2.8 4.9
##  [325] 5.0 5.2 4.5 5.2 4.3 5.7 5.6 4.0 3.8 5.4 5.6 5.0 6.1 5.0 3.1 2.9 4.0 5.0
##  [343] 5.9 4.2 6.2 4.7 5.1 5.0 3.7 4.4 4.1 4.7 4.7 3.4 4.7 3.0 4.8 4.6 4.9 4.1
##  [361] 4.3 4.9 4.3 5.2 4.2 4.7 5.0 3.3 3.7 4.8 3.6 4.7 5.1 5.1 6.4 5.2 5.7 4.2
##  [379] 5.3 3.1 5.2 3.2 3.4 4.8 4.9 4.4 5.8 5.4 4.4 6.0 4.7 5.2 3.0 4.4 4.6 5.5
##  [397] 2.4 4.5 4.4 4.6 5.3 3.3 3.8 3.9 3.5 5.1 4.8 4.3 2.9 3.7 4.0 3.4 3.3 4.2
##  [415] 5.9 2.7 4.2 3.1 4.0 5.9 5.1 3.3 2.8 4.6 3.2 6.8 4.2 5.3 4.6 4.9 6.8 4.9
##  [433] 3.3 4.2 4.4 4.7 5.7 3.8 4.7 4.5 4.2 4.5 3.0 5.8 4.8 5.9 2.7 5.4 4.4 2.8
##  [451] 5.4 6.1 4.1 4.2 4.8 6.0 4.2 4.3 4.9 2.5 4.4 4.7 3.9 4.5 4.8 4.8 3.6 5.3
##  [469] 5.5 3.9 6.3 4.7 4.3 5.8 4.5 4.6 2.3 4.1 4.2 4.2 5.6 5.4 4.4 2.5 3.4 3.6
##  [487] 5.5 6.3 3.3 4.9 5.3 6.7 2.5 3.4 5.8 3.8 5.5 4.0 4.2 4.8 3.7 3.2 5.7 5.4
##  [505] 6.8 4.4 4.5 5.8 4.5 4.9 2.5 4.6 3.4 4.1 4.7 5.3 4.5 5.0 4.6 4.5 3.7 4.9
##  [523] 4.2 6.0 5.2 5.4 4.1 3.4 4.1 5.0 4.1 4.9 3.2 4.3 5.6 1.6 3.5 4.1 5.1 4.8
##  [541] 6.7 4.4 4.9 3.6 5.2 5.0 4.3 4.8 4.4 5.4 3.7 3.7 2.1 3.3 5.6 3.9 4.8 4.8
##  [559] 4.6 6.2 5.0 3.3 3.8 5.9 4.7 4.3 4.3 4.5 4.9 4.0 4.8 4.6 4.3 5.4 3.3 2.6
##  [577] 5.8 3.6 4.0 4.6 3.9 5.7 6.0 5.0 4.7 4.8 4.8 5.1 5.0 4.1 3.3 4.8 5.4 4.3
##  [595] 4.3 3.6 4.7 2.6 3.9 2.6 4.3 5.0 4.6 4.4 5.9 6.2 4.5 4.1 4.3 4.9 4.2 6.3
##  [613] 3.7 6.4 4.2 3.7 5.2 4.0 4.0 4.7 4.2 5.1 5.9 4.4 4.2 3.6 4.3 4.4 5.6 5.6
##  [631] 5.2 4.9 4.6 4.5 4.1 5.8 5.7 4.6 5.2 3.9 4.9 4.0 5.6 3.5 3.8 4.5 2.9 3.7
##  [649] 5.6 3.5 4.1 5.0 3.6 5.1 6.0 5.2 3.6 5.6 3.2 4.1 4.4 5.4 3.9 5.2 3.9 4.2
##  [667] 3.1 3.9 4.2 5.0 4.6 4.2 5.4 4.9 4.1 5.6 6.3 4.7 4.2 5.1 3.3 3.6 5.3 2.8
##  [685] 4.4 5.4 4.7 3.6 4.2 3.5 5.7 4.2 4.5 4.5 4.0 6.2 3.0 5.1 4.8 4.3 5.0 4.7
##  [703] 3.8 5.3 4.4 3.9 3.8 3.1 5.9 5.1 5.2 4.5 4.7 4.4 4.4 3.8 4.6 4.7 3.9 5.4
##  [721] 4.1 4.8 2.7 3.0 3.9 4.8 4.3 4.5 4.4 4.6 5.0 4.6 4.7 4.3 3.9 4.5 5.2 5.1
##  [739] 4.6 4.4 6.5 4.5 4.9 4.3 5.3 3.4 4.5 3.4 4.8 5.1 4.9 3.9 4.1 3.1 5.6 5.2
##  [757] 4.8 3.8 6.3 4.9 3.4 3.5 3.6 4.9 4.5 6.1 5.1 3.3 4.5 4.7 5.0 4.8 6.3 3.6
##  [775] 4.0 4.8 2.6 5.8 3.3 3.5 3.2 3.9 5.3 2.8 4.4 7.3 4.7 5.0 2.3 5.3 4.8 3.3
##  [793] 4.3 5.1 4.7 3.8 4.8 4.4 3.2 3.9 5.2 4.5 5.7 6.3 4.6 5.6 3.1 4.0 4.1 4.4
##  [811] 4.5 5.2 4.9 5.2 3.8 3.8 4.7 2.8 4.2 6.2 3.3 3.9 4.4 4.9 4.5 2.7 4.8 4.6
##  [829] 7.5 4.7 4.6 5.6 2.9 4.4 4.2 3.6 5.8 4.2 3.9 4.7 4.5 3.2 5.3 5.2 6.0 4.5
##  [847] 5.2 4.3 4.0 4.0 5.9 3.5 4.2 6.0 3.3 5.3 4.6 4.1 4.4 4.0 6.5 5.2 5.1 6.3
##  [865] 4.9 4.3 3.4 4.9 3.7 2.6 4.3 4.4 3.6 4.2 6.1 4.3 4.3 4.1 4.6 4.3 4.3 5.7
##  [883] 4.7 6.2 4.5 4.6 4.8 5.1 3.8 6.1 2.7 4.5 4.0 5.0 5.7 5.3 4.4 5.3 4.1 5.5
##  [901] 3.2 4.7 6.3 4.0 4.8 3.7 3.9 5.9 3.3 4.8 4.9 4.6 5.0 4.6 4.9 4.1 3.0 5.4
##  [919] 3.8 3.1 4.4 5.3 4.1 3.6 3.5 2.9 3.5 5.0 5.7 4.4 4.2 3.8 6.1 5.0 4.2 3.9
##  [937] 4.2 5.4 4.7 4.8 3.7 4.1 4.2 3.9 5.0 4.2 4.0 3.6 4.6 5.7 4.6 4.1 5.6 5.4
##  [955] 5.9 5.1 4.2 4.5 6.1 4.3 3.1 5.6 4.6 5.5 4.7 3.0 4.4 4.9 1.9 5.6 3.1 3.6
##  [973] 3.4 3.4 4.0 4.3 3.5 3.9 4.6 4.6 4.0 5.3 5.3 4.0 4.5 4.4 4.9 4.1 4.6 5.2
##  [991] 4.6 3.8 3.4 5.6 4.1 6.7 3.9 4.4 5.0 4.6
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.4 5.4 5.3 5.2 5.0 5.0 4.8 4.8 4.5 4.5
## 
## $jack.boot.se
## [1] 0.9623409
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
## [1] -0.3304605
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
##   3.591528   7.681666 
##  (1.537423) (3.529277)
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
## [1]  0.8337218  0.6322725  2.0951598  0.7274038  1.0736227 -0.8386403
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
##    [1] -0.0572687746  0.2305885760  0.1954494554 -1.0560589745  0.3819793070
##    [6] -0.2719052130 -0.0477615486 -0.2053859066 -0.0802159251 -0.4356979695
##   [11] -1.2276455018 -0.4716091232 -1.0338619125 -0.9720318894 -0.7389708383
##   [16] -0.2540868407 -0.0651021683 -0.2683934339  0.0914149204 -0.4492657808
##   [21] -0.3932365587 -0.9717362122 -0.0720054655 -0.0875386177  0.1308965054
##   [26] -0.8496967120 -0.5330714302 -1.2765546573 -0.2325647182  0.6582079855
##   [31] -0.2392705937 -0.1141616272 -0.2504468955  0.0805569322 -0.5986902823
##   [36] -0.1097781493 -0.3054881011  0.0232601688 -0.5613172864 -0.3936137752
##   [41] -0.1610982764 -0.3294541584  0.0334468432  0.3545462990 -0.7045445781
##   [46]  1.1266367879  0.6863385635 -0.7421049668 -0.9968213884 -0.2843223776
##   [51]  0.2385277656 -0.7270971548 -0.4939410794  0.3752931737  0.0257391948
##   [56] -0.3604426908  0.0128189970 -0.2168731898 -0.5505446090 -0.7370815893
##   [61] -0.2512687662 -0.2622854528 -0.0969507654  0.7801966379 -1.2172273771
##   [66] -1.1590335776 -0.6449682358 -0.2391950264 -0.8462755859 -0.1589833734
##   [71]  0.0689614703 -0.0900577417 -0.6755266916  0.2242490409 -0.8698838614
##   [76] -0.2243880270 -0.7205134975 -0.0942768970  0.1072550852 -0.3372343020
##   [81] -0.0278998637 -0.0824806157 -0.5416294785 -0.7202325957  0.3279259897
##   [86] -0.8955027450 -0.0483426711 -0.3502977891  0.2769072597 -0.1383310163
##   [91]  0.9476861441 -0.1414184060 -0.7709250273 -0.3854156509 -0.6113113215
##   [96] -0.9406377876 -0.9171088512 -0.3559011377 -0.3937879176  0.5488246101
##  [101]  0.1940445075  0.0139971834 -1.4283542742  0.1890361226 -0.4721416114
##  [106]  0.0438076258 -0.4495713681 -0.6373436107 -0.0343794669 -0.2232685515
##  [111]  0.1003430759 -0.5618163920 -1.5188459905  0.1075277841 -1.8196476163
##  [116]  0.0820641601 -0.8994210837 -1.3513129549 -0.8778332358  0.2101223337
##  [121]  0.1724606827 -0.3056225106  0.0731420318 -0.0741090240 -0.3717324380
##  [126] -0.1685336100 -1.2510794216 -0.5680253071 -0.4628410307  0.1859011943
##  [131] -1.1373868784 -0.6834732785  0.3964085215 -0.6065917509 -0.1220184202
##  [136] -0.2727497048 -0.4786542255 -0.7651082828 -0.8309693992 -0.8029050135
##  [141] -0.9056383745 -1.9256518043 -0.2104219597 -0.3436533364 -0.2333259193
##  [146] -0.3859670889 -0.0066582961 -0.2627541637 -0.5479474987 -0.5560812870
##  [151]  0.2686390100 -0.6277163583 -0.8923985393 -0.2786551996  0.1858750042
##  [156] -0.6835978814 -0.8346446017 -0.9959514216 -0.7196598765 -0.1130609057
##  [161] -1.3715344271 -0.7210241145 -1.7438884676  0.0021388447 -0.3248304456
##  [166]  0.3096254422 -0.3268916131 -0.1028131944 -0.2786551996 -1.1563993316
##  [171] -0.6935564501 -0.5203598853  0.5123335365 -0.5169980687  0.0582337290
##  [176] -0.4327717743  0.4665255005  0.2585357941 -0.9741018172 -1.8168261631
##  [181] -1.1888804440 -0.6804835655  0.1404883517  0.2561293917 -0.5929466564
##  [186]  0.8448996538 -0.7822851744  0.1444748200 -0.2045333981  0.7374428384
##  [191]  1.1038213228 -0.2141781743 -0.1551068500  0.2938262842 -0.5175342193
##  [196] -0.2485892991 -1.0236283084  0.2970836827 -0.7116645686 -0.1326101416
##  [201] -0.7022849979  0.2039177235 -0.6982405169 -1.4131400200 -0.3224635404
##  [206] -0.3537749835 -0.8968719301 -0.8772466577 -0.3436533364 -0.3328066647
##  [211] -0.7353203551 -0.3207755618 -1.0267024062 -0.0277575006 -0.0363596795
##  [216]  0.0401912742 -0.0810720172 -0.5114776648 -0.8970681086 -1.6585094519
##  [221] -0.0833453809  0.0628078674 -0.0844395982 -0.6467330132 -0.9459554166
##  [226]  0.5473938714 -1.0246356169 -0.4289109160  0.1763076112 -0.2245058822
##  [231] -0.7531256859 -0.1413239038  0.7627951544 -0.5007981755  0.1516496293
##  [236] -0.8981093407 -0.3573427831 -1.2500868177 -0.2481647875 -0.2419066622
##  [241]  0.2240800625 -0.5168963014  0.8064194586 -0.5112629770 -0.1102732299
##  [246] -0.5815569065 -0.1131287508 -0.6772243076 -0.9127958214 -0.2428649356
##  [251] -0.4021359375 -0.2452597355 -0.4004500598  0.8460406065 -1.1807409578
##  [256]  0.0699109662  0.2725258207 -0.0624216695 -1.1955576844 -0.3038506000
##  [261] -0.3536987533 -0.2589650918  0.0320350525  0.1237853499  0.3303688981
##  [266] -0.8227797594  0.2879101073 -0.5806302586 -0.2865023703 -1.3901208594
##  [271] -0.9999135718 -0.2101028689 -0.3423593673 -0.8281985664 -2.0126493478
##  [276] -0.5176819894  0.2324237249 -0.8446256333  0.0316316916  0.1051303199
##  [281] -0.0765610430  0.1404883517  2.6524458733  0.0303996172  0.0942642427
##  [286]  0.0488719303 -0.6991054051 -0.2510552258  0.4767766821 -1.5867552010
##  [291] -0.6978477730 -0.6037954384  0.3936141928 -0.0537510951 -0.8464759053
##  [296] -0.2898443879 -0.6698774147 -1.0026392692  0.0581035550 -0.1047372216
##  [301]  0.0206888714 -0.9217031348  0.1812303854  0.3919064058 -0.5413582502
##  [306] -0.2510739146 -1.1788912382 -0.2935567024  0.6509111721 -0.3432147990
##  [311] -0.8640090341 -0.1232263219 -0.2892409551  0.1763076112 -1.0951143846
##  [316] -0.7805346801 -0.0005809412  0.0387904434 -0.8889550491 -0.3971264820
##  [321]  0.3205627028 -0.5778531795  0.5447023164 -0.8433320186 -0.4844594679
##  [326]  0.2767427641 -0.0221726942  0.1855760980  0.1924314961 -0.7155999227
##  [331] -0.2493317833  0.1210238720 -0.1090905819 -0.6871101147 -1.2826613195
##  [336] -0.8318841894 -0.4291603397 -0.7041652234  0.0213306708 -0.4579919971
##  [341] -1.3161171707  0.1952522659 -0.7619605067 -0.1934327766 -0.0685745070
##  [346]  0.0472066090  0.4322399095 -0.1654593559 -0.6754342410 -0.3235107159
##  [351] -1.0188201827 -0.3457974560 -0.6603561161 -0.6023913806  0.2250077570
##  [356]  0.3072320671 -0.2885661999  0.3706477063 -0.1899936257 -0.0199165497
##  [361] -1.2494040378 -0.4039540758 -0.2632338463 -0.3306738933  0.2206521295
##  [366] -1.1883401903 -0.6388244613 -0.1038241932  1.4506195731 -0.1475117464
##  [371]  0.3238552543 -0.5791448404  0.1717841819  1.0348560515 -0.9917037290
##  [376]  0.2354470447 -0.3589395640 -0.1382869967 -0.9847706637 -0.4039748665
##  [381] -0.3273090859 -0.9613198842 -0.6229711492 -0.1695551606 -0.1818915294
##  [386] -0.2171641093 -0.2508958608 -0.6761377974 -0.1770845258 -0.2438804052
##  [391] -0.6194125641 -0.5128390616  0.0925506301  0.0633148810  0.1939821068
##  [396] -0.1032123827 -0.4727878043 -1.2211387736 -0.5227424980 -0.4100870822
##  [401] -0.1005646159 -0.9086247807 -0.1601318841 -0.6197158098  0.1924410972
##  [406] -1.0353419254  0.5906812131 -0.9652565286 -1.0681516038 -1.3174517615
##  [411] -0.2632208994 -2.0200228776  0.3924317364 -0.4762177553  0.2942518009
##  [416] -0.5845207330 -0.3659422052  0.5211354034 -0.3251839995 -0.0841582454
##  [421] -0.3307987675 -0.7154013335  0.2013825970 -0.8830515988 -1.1223062880
##  [426] -0.6483442073 -0.4306548434 -0.8198494997 -0.5290366558  0.2847269155
##  [431]  0.6047862818 -0.8202151146 -0.4941444558 -0.5437413296 -0.6942752930
##  [436] -0.2376689461  0.1392109676 -0.3495030967  0.0567893928 -0.3477717377
##  [441] -0.5220311858 -0.2678541391 -0.5179462383  0.1915776591  0.2826021884
##  [446] -0.1797097034 -0.6647553982 -0.5566403050 -0.3713410431 -1.1860738696
##  [451] -0.5505446090 -0.9148686405 -1.1256405085 -0.9070136616 -0.4974339219
##  [456] -0.3239953409 -0.4503452862 -0.1675074922 -0.5313483731  0.0196545533
##  [461] -0.6183458468  0.0970922276 -0.7584405852 -0.6512410965 -0.5078567664
##  [466] -0.7319061351  0.5396473406 -0.6936021317 -1.1937522924 -0.2215220607
##  [471]  0.8403289675 -0.2171004018 -0.5137297874 -0.4059414952 -0.5999111143
##  [476] -0.4799955410  0.3732322872  0.3690570883 -0.2137397203 -1.9148606206
##  [481]  0.5037282953 -0.5988185352  0.1632345070 -0.1507685464 -0.3398452072
##  [486] -0.2700982920  0.3709742316 -0.4931003508 -0.4608768991 -0.4424301023
##  [491]  0.0438076258 -0.5420036877 -0.7334104643  0.5425345729 -0.3605962236
##  [496] -0.7538793956 -0.1727845279  0.1221630366 -0.7199563520 -0.1400291288
##  [501] -0.9866071698 -1.1223485801  0.5262149270 -0.2530993390 -0.4481294059
##  [506] -1.1223658268 -0.5908209249 -1.0580899948 -0.6243963369  0.1255059576
##  [511] -0.4252390246 -0.0859485490 -0.2162352182 -0.4557744731 -0.4917481698
##  [516] -0.3122627482 -0.2888111608 -0.0193239364 -0.5850048267  0.3822823741
##  [521] -0.8455073039  0.5241915343 -0.5117542034 -1.2790404081 -0.8544817581
##  [526] -0.4000357793 -0.0461515630 -0.1844206564  0.2589907010 -1.1752203903
##  [531] -0.6321018726  0.0259285004 -0.8171646670 -0.9262612375 -0.2506605994
##  [536] -0.2314568397 -0.2078375424 -0.3243614087 -0.7771055606 -0.9459745960
##  [541]  0.0374540724 -1.0143593536 -0.4329315023  0.9550818217  0.1117135455
##  [546] -0.1089352270 -0.5849599195 -0.5085835037  0.0165076560 -0.6634111447
##  [551] -0.3723510997 -1.1936394566 -0.1057439657 -0.3108485220 -0.4746583879
##  [556]  0.0184347638 -0.1050560200 -0.8188955450  0.0865677472 -0.7573084903
##  [561]  0.0549069046  0.0942642427 -1.1377359390  0.0205269720 -0.6783858807
##  [566] -0.0704882561 -0.3283836488 -0.3972726541 -0.0915255585  0.2646263660
##  [571] -0.5258651616 -0.9489502370 -0.1391005524  0.1782919227 -0.0722851894
##  [576]  0.3712237080 -0.4007215722  0.1821519866 -1.1161052893 -0.4658810530
##  [581] -0.7853555968 -0.6158447767  0.0069780157 -0.3040696110 -1.0084751109
##  [586] -0.2846581899 -0.2296477166 -0.1376983784 -0.8787062520 -0.5218710753
##  [591] -0.5537612323  0.4066062537 -0.1487066965  0.5101186246 -0.4916137712
##  [596] -1.1036157561 -0.2407058866  0.7975198673 -0.4732327984 -0.1749139472
##  [601] -0.8419807056 -0.0406393011 -0.5256261493  0.0801944375 -0.6066269477
##  [606] -0.3087193618 -0.7523734922 -0.5551001345  0.1009074507  0.1658028236
##  [611] -0.3647554891 -0.7315777136 -0.6170636199  0.2790598041 -0.8723599519
##  [616]  0.1060063366  0.4615087359  0.5430250706 -1.0815573789  0.4478289292
##  [621]  0.5343840714 -0.1859603422  0.3937319609  0.2080912135 -0.0489040763
##  [626]  0.4455437834 -0.7152113403 -0.3395624974  0.3733663291  0.4316402737
##  [631]  0.2108792024 -1.4538900635 -0.0210372743 -1.5489424330  0.0847402364
##  [636] -0.3674253801 -1.0586151321  0.4838562162 -0.4946162926  0.4335243679
##  [641] -0.1107851018 -0.9682123273 -1.6249238088  0.1724441916 -0.8787062520
##  [646] -0.4063281727 -0.1382742028 -1.3338597602  0.1831406214 -0.5932404492
##  [651]  0.2375736163 -0.5195499289 -0.1889405176 -0.5852005462 -0.9828539055
##  [656] -0.2352603464  0.6752888687 -0.5959363439 -1.1032110458  0.0560505162
##  [661]  0.1553023945  0.6835731061 -0.3305900553 -0.1017744271 -0.9059533233
##  [666] -0.2687497464 -0.7742450220 -0.1832748821 -0.6121066755 -0.9288021759
##  [671]  0.1860010358 -0.4339564259 -0.4539604203  0.1042470833 -0.1236832122
##  [676] -0.0143381949 -0.9405793350 -0.8614652184 -0.7645816018 -0.1463681393
##  [681] -0.8419807056 -0.1686711345 -0.3066200462 -0.2178252378 -0.0352185114
##  [686]  0.8294810911 -0.6760689873  0.2199371558 -0.2758422233 -0.2323175340
##  [691] -1.0697512275  0.0261661880  0.0698835869  0.2007143156 -0.2123874351
##  [696] -0.9615238649  0.2137325122 -0.5975819896 -0.4167052993 -0.6812256379
##  [701] -1.1302571632 -0.1947429814 -0.3861151090 -0.8835489781 -1.4037285195
##  [706] -1.1882129960 -0.6831012591 -0.5957674408  0.1018093095  0.1317989562
##  [711]  0.2318649142 -0.6143464622  0.0936142719 -0.4577586383  0.1025345108
##  [716]  0.2640514243  0.1452549307 -0.0437042269 -0.4522630177 -1.0489249737
##  [721] -1.3491502681 -0.0702138982 -0.4486861479 -0.1764694765 -0.7878592436
##  [726] -0.7230124365 -1.7878254994  0.1240137691  0.0546022804 -0.4854443508
##  [731] -0.4785558417  0.1796631938 -0.2467984246 -0.9745518987 -0.1998223384
##  [736] -0.2459232635 -0.0863632219  0.1456859274 -0.4848356730 -0.4854355173
##  [741] -0.8125883990 -0.3793839803 -1.0687576248 -1.3223853787 -0.2491939652
##  [746] -0.6864852358 -0.2014822758 -0.5046833976 -0.3274299937  0.0528472850
##  [751] -0.0208445699 -0.4758603637 -0.6439740526 -0.0402352674 -0.8936524356
##  [756] -0.0371645820 -1.1943881902 -0.1377401873 -0.8318841894  0.3337168796
##  [761] -0.6903591385 -1.3493664791  0.0072900510 -0.3243067008  0.2109125042
##  [766] -1.4557579335 -0.2993769940 -0.8394095297  0.4866964079  0.3783966327
##  [771] -0.7013066349  0.0423518720 -0.8227797594  0.2979291711 -0.7920403306
##  [776] -0.8825851064 -0.6801138427 -0.1253906723 -0.8700273149 -0.1394235194
##  [781] -1.0089700180 -0.6798803807 -0.1450898524 -0.9294378018 -0.5321430536
##  [786] -0.8159720663 -0.1280449724 -0.5797664391  0.6766335095 -0.3516141377
##  [791]  0.2320590465 -0.2007450716 -0.8011200800 -0.7549024532 -0.8589907033
##  [796] -0.5450652503 -0.0698373399  0.2125353143 -1.0743322337  0.0916638307
##  [801]  0.0467210157  0.4305891084 -1.2354893924 -0.8248011834 -0.4008847193
##  [806] -0.5634029687  0.0255860393 -1.0192180068 -0.6371272984 -0.1391107525
##  [811]  0.0663731546 -0.0013806407 -1.2593035028 -0.0011592662  0.3851150663
##  [816] -0.2634606472  0.3352204615 -0.6899707550 -0.1257507081 -0.0319927008
##  [821] -0.1022471642  0.1150281273  0.2260983302 -0.2382095296 -0.7925022634
##  [826] -0.7916798642  0.0801083151  0.1154326600  0.4338978892  0.0691315937
##  [831]  0.2596239097 -0.2020865263 -0.2939543937 -0.0519395529 -0.1138439285
##  [836] -0.1617995981 -0.7708790370 -0.3514821880 -0.7965364880 -0.0695522854
##  [841] -0.3141590477  0.2098884731  0.2196352281 -0.5307108595 -0.1164619659
##  [846]  0.3410843643 -0.3917775896 -0.4776367681 -0.1609347497 -0.4619377460
##  [851] -0.3069053723 -0.1709667618  0.1348289671 -0.4317867581 -1.7621638395
##  [856] -0.2294142257 -0.4294211533  0.0232601688 -0.8139167108  0.2880239343
##  [861] -0.3947243710 -0.7026871049 -0.0121480403 -1.3104306942 -0.5034421652
##  [866] -0.2659020210 -0.2379134578 -1.6222545795  0.5057184308  0.3982124837
##  [871] -0.4085320160 -0.7027255200 -1.1949268237 -1.1547090128 -1.4587785715
##  [876] -0.5893166649 -0.7759546934 -0.1484339823 -0.6740701174 -0.4657485099
##  [881] -0.4635238921 -0.0086774580 -0.2755199544 -0.3439481195 -0.6638527659
##  [886] -0.8594193195 -0.0978647637 -0.5797958061  0.5968689370 -0.2566211670
##  [891]  0.0784071276 -0.3015541821  0.1567378980  0.0179608187  0.4316588269
##  [896]  0.4042308640 -0.8311708714 -0.6928848183  0.0358651990 -0.9723417952
##  [901]  0.4537108193 -0.0304747388 -0.1876764696 -0.8034021025 -0.0776614209
##  [906]  0.2927540616  0.0866348724 -0.5502915837 -0.2471222753 -0.3998776335
##  [911] -0.5128390616 -0.2280325482  0.5259276468 -0.0875793987  0.3825891098
##  [916] -0.2673127143 -0.0768189420 -0.2903881351 -1.0864487355 -0.4262676790
##  [921]  0.0280935309 -0.9911763916 -0.7989616010  0.0099562104 -0.2684269146
##  [926] -0.1237414516  0.1381693126 -0.7307117335  0.0906853905 -1.1578083807
##  [931] -0.4070761846 -0.8320316904 -0.5348146268  1.1294234051 -0.7370135493
##  [936]  0.1726051047  0.0564170684 -0.5889596039 -0.5201500992 -0.5410658471
##  [941] -0.1358432869  0.0971736887  0.1418877429 -0.6044743582 -1.3130488032
##  [946] -0.1066664878  0.0241821348  0.9406418067  0.4660020584 -0.1677407605
##  [951]  0.1237838910 -0.3977967754  0.3454802619  0.3581230625 -0.2761882286
##  [956]  0.4437884025 -0.2849385734 -0.6025143897 -1.4981922917 -0.3788813562
##  [961]  0.7723047122 -0.2657644010 -0.5701979077 -0.6026463360 -0.7387384421
##  [966] -0.3082045372 -0.5954082958 -0.5243768795  0.4216949533  0.2562860630
##  [971]  1.3807156489  0.6238490795  0.2955687315 -0.9245002588  0.4347554946
##  [976]  0.3070589383 -1.0866853585 -1.1145704723  0.0825459801 -0.3689928608
##  [981]  0.0773595090  0.2431454819  0.1350349908 -1.0017362996 -0.4639285031
##  [986] -1.2213830114 -0.1235728566 -0.3712223713  0.4477549169 -0.7055943156
##  [991] -0.5162345293 -0.5845643452  0.2959983160 -1.6410780712 -0.3221231944
##  [996] -0.5222503832 -0.7443102389  0.2551788992 -0.7722475106 -0.1216595421
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
##   0.46754319   0.21123564 
##  (0.06679857) (0.04722990)
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
## [1] -1.05834185  0.69078813  0.03799134  0.21923211  0.70804248  0.61761591
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
## [1] -0.0093
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9293477
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
## t1*      4.5 -0.04094094   0.8987608
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 4 5 6 8 
## 1 1 1 1 3 1 2
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
## [1] -0.015
```

```r
se.boot
```

```
## [1] 0.9133944
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

