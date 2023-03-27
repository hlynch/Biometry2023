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
## 0 1 2 5 6 7 8 
## 1 2 2 1 2 1 1
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
## [1] 0.0555
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
## [1] 2.762227
```

```r
UL.boot
```

```
## [1] 6.348773
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.4
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
##    [1] 4.9 4.9 6.0 3.2 4.6 4.8 3.7 3.5 5.2 5.5 5.7 4.8 4.4 4.9 4.7 4.2 5.4 4.6
##   [19] 5.4 4.5 4.0 6.8 4.1 5.0 5.0 3.9 3.9 6.9 3.9 5.8 5.4 3.9 4.4 4.6 5.6 3.9
##   [37] 3.2 4.1 4.4 4.0 5.9 2.0 5.0 4.3 3.1 4.5 4.6 4.7 4.7 4.3 4.7 3.2 4.5 5.3
##   [55] 3.9 4.9 4.3 5.2 4.4 4.9 3.5 4.1 4.0 3.8 5.1 4.5 4.5 6.1 3.4 6.3 5.3 4.9
##   [73] 3.3 5.0 5.0 5.1 4.7 5.2 5.3 4.4 3.6 4.8 4.4 5.1 4.3 3.7 4.9 4.8 5.3 4.7
##   [91] 4.2 4.1 4.4 6.1 3.9 2.6 4.7 3.6 5.0 5.9 4.5 4.7 4.4 4.9 3.6 4.7 4.4 6.0
##  [109] 3.3 5.0 4.5 4.3 3.5 4.1 4.9 4.9 3.5 5.7 4.3 4.6 3.8 4.0 3.5 3.7 5.9 4.1
##  [127] 2.9 4.9 5.3 4.7 5.4 4.6 4.6 3.1 4.7 5.7 4.2 4.4 3.2 4.1 5.0 3.2 4.7 4.8
##  [145] 3.7 3.0 3.0 4.9 4.2 3.1 4.5 3.9 4.8 4.1 4.8 4.8 3.6 3.3 5.0 4.8 4.6 3.4
##  [163] 5.5 5.6 3.7 3.5 3.9 4.9 3.8 3.8 6.2 5.3 4.4 2.8 6.3 4.3 4.5 4.4 4.3 4.6
##  [181] 4.5 3.4 5.1 4.6 3.6 3.8 3.1 6.0 4.1 3.8 3.5 7.4 4.4 3.6 4.6 5.6 3.6 3.3
##  [199] 4.4 4.1 4.2 5.3 4.3 6.5 2.7 3.4 4.8 5.2 4.0 4.3 6.0 4.0 5.2 5.7 5.0 3.7
##  [217] 3.7 3.0 3.4 3.7 3.5 3.7 4.4 4.7 4.3 3.6 3.4 3.3 4.3 5.8 4.3 5.1 5.1 2.6
##  [235] 5.0 6.2 4.3 3.8 4.3 3.4 8.2 6.7 3.9 2.6 3.6 4.4 3.9 4.9 4.5 4.4 5.7 3.9
##  [253] 4.1 4.3 5.1 4.6 4.0 5.5 3.8 3.8 3.4 5.5 5.5 4.6 4.7 5.5 4.0 4.4 3.8 3.8
##  [271] 4.0 4.2 2.7 4.1 4.4 4.8 5.7 5.3 4.8 4.2 5.0 3.8 3.9 4.4 5.2 3.8 4.7 4.7
##  [289] 5.2 3.9 5.2 5.2 4.1 5.1 3.7 5.7 4.9 4.1 5.5 4.9 4.2 5.4 4.7 4.8 4.1 5.2
##  [307] 3.3 3.2 4.1 4.1 4.6 4.6 3.7 4.7 5.5 4.3 4.0 3.3 5.0 4.1 5.4 5.3 5.5 4.9
##  [325] 2.9 4.4 4.5 3.3 3.4 4.4 5.5 6.5 4.2 4.5 3.8 3.2 7.5 5.2 4.9 5.5 4.1 2.8
##  [343] 4.5 3.6 6.0 6.8 3.6 4.7 4.6 4.9 5.0 4.6 4.5 5.4 3.7 4.0 4.2 3.6 4.4 4.7
##  [361] 2.9 5.5 3.4 5.7 4.3 4.7 5.1 3.2 4.0 3.6 4.3 3.4 5.4 4.7 4.9 3.9 3.4 4.6
##  [379] 3.4 4.9 4.1 4.1 4.0 4.8 3.8 4.8 4.4 4.0 4.6 5.0 3.7 2.4 3.9 4.5 5.1 2.7
##  [397] 3.1 4.2 2.8 2.5 3.7 4.6 4.2 5.0 3.3 4.1 2.5 4.3 6.6 5.1 3.3 3.5 2.8 5.6
##  [415] 4.2 5.9 4.5 4.4 4.0 3.9 4.3 3.8 4.4 6.0 3.7 4.1 3.9 5.3 4.5 4.5 3.9 4.3
##  [433] 4.4 4.8 4.8 4.2 4.1 3.2 2.6 6.6 5.1 4.9 3.8 5.2 4.9 3.8 4.3 5.2 5.4 4.7
##  [451] 4.3 6.0 5.0 5.4 3.7 5.4 4.5 5.3 4.7 3.7 4.2 3.6 6.2 4.1 2.9 5.0 4.2 4.5
##  [469] 5.5 5.7 4.9 5.1 3.3 4.7 4.5 4.5 3.7 4.6 4.7 3.8 5.7 6.5 4.7 3.4 5.7 4.3
##  [487] 4.0 5.0 5.4 5.2 4.4 4.1 5.1 3.8 5.5 4.4 4.2 4.6 4.1 4.8 4.8 3.7 5.6 3.5
##  [505] 3.8 3.9 3.2 3.6 3.4 5.6 4.6 3.0 3.5 4.1 6.2 6.2 5.6 4.9 4.5 4.5 3.7 3.5
##  [523] 5.5 4.3 4.4 4.8 4.6 4.4 5.3 4.2 3.4 3.8 3.7 4.8 4.1 4.0 5.1 5.3 4.8 4.1
##  [541] 5.1 3.2 3.7 5.1 4.9 4.5 5.1 5.7 6.1 4.4 5.8 2.9 4.4 4.5 5.3 5.1 4.5 4.8
##  [559] 6.1 4.6 4.9 4.5 5.3 5.9 4.6 3.9 4.7 4.6 4.4 4.9 5.1 4.7 4.3 5.3 4.5 5.3
##  [577] 6.5 4.5 5.0 4.8 4.2 5.5 5.5 6.0 3.4 6.3 4.4 3.1 5.3 5.2 2.8 6.5 4.1 5.7
##  [595] 3.0 3.5 4.2 2.8 3.4 3.7 3.9 3.8 5.6 4.7 3.4 4.6 5.4 7.0 4.7 3.2 5.3 4.4
##  [613] 4.1 4.2 3.7 3.6 3.1 3.3 5.2 3.4 5.1 4.2 4.6 6.1 4.5 3.7 5.3 4.8 4.2 4.5
##  [631] 3.7 6.0 6.4 3.4 3.0 5.6 5.0 4.3 4.8 5.4 6.2 4.5 5.1 5.4 4.4 4.7 4.3 5.3
##  [649] 4.2 5.6 5.3 3.6 4.6 4.3 4.6 4.9 4.1 6.8 5.8 4.3 1.6 4.2 5.2 3.7 4.5 2.7
##  [667] 4.9 5.0 5.0 5.0 3.1 5.0 3.7 3.3 4.5 5.2 4.4 3.4 2.5 5.6 5.6 5.5 5.0 3.3
##  [685] 3.0 3.7 3.5 5.8 3.3 4.6 5.3 4.3 4.3 3.2 3.8 4.2 4.4 4.8 3.0 5.1 3.5 4.2
##  [703] 3.2 4.7 5.7 4.8 5.4 6.2 4.6 4.4 5.1 4.3 4.7 4.7 5.0 4.5 3.2 4.3 5.6 3.5
##  [721] 5.4 3.7 5.1 5.4 3.7 7.6 4.9 5.5 4.3 2.0 3.8 3.3 4.4 5.8 3.3 5.0 3.5 4.1
##  [739] 4.5 5.1 5.7 3.7 3.0 3.9 3.0 4.1 2.6 4.4 4.8 4.5 5.6 3.3 4.4 5.7 4.0 4.6
##  [757] 2.7 4.0 5.8 4.7 3.9 4.7 4.6 4.6 4.9 4.2 2.6 5.9 4.4 5.5 3.3 5.0 4.9 4.6
##  [775] 4.3 3.6 6.0 4.3 4.0 4.9 3.8 5.7 5.4 4.4 5.6 3.8 3.5 4.6 5.6 4.8 5.5 4.0
##  [793] 6.0 5.1 6.3 3.6 3.1 5.1 4.2 3.6 3.9 4.9 2.8 5.4 5.8 2.8 4.1 4.7 3.3 5.5
##  [811] 3.6 3.9 4.1 3.8 3.9 3.6 5.4 4.2 4.6 4.7 3.1 4.8 4.7 4.8 4.4 5.3 3.8 4.2
##  [829] 4.0 5.0 3.8 3.7 4.1 4.4 3.5 3.6 4.1 4.7 3.7 3.6 4.3 3.1 5.0 5.1 2.8 4.1
##  [847] 4.6 4.3 6.6 4.8 4.6 3.7 4.2 3.5 4.6 5.0 6.6 6.3 5.0 4.7 4.3 6.9 4.9 2.8
##  [865] 3.2 4.6 3.3 6.2 5.0 4.2 5.1 6.2 3.3 4.0 5.4 5.3 3.0 4.2 4.0 3.9 5.2 4.0
##  [883] 3.2 6.8 2.2 4.8 3.6 5.2 5.0 3.7 4.0 4.7 4.5 4.3 5.1 6.7 4.2 4.2 4.5 5.7
##  [901] 4.9 6.2 4.8 4.6 3.8 4.0 5.2 3.7 4.0 4.9 5.6 4.5 5.1 3.6 4.1 3.1 6.0 4.4
##  [919] 5.6 2.5 4.4 3.5 4.2 3.6 3.7 3.6 3.9 6.0 4.4 4.6 6.0 3.2 4.5 3.3 6.2 3.2
##  [937] 3.2 4.1 4.4 3.8 4.7 5.2 4.1 4.0 3.9 5.1 5.2 5.3 4.9 4.6 5.1 5.3 3.8 4.5
##  [955] 4.9 4.9 4.4 3.9 4.0 5.0 3.5 5.7 4.0 5.0 4.3 4.7 4.3 3.9 4.4 5.5 4.7 3.9
##  [973] 3.7 4.2 5.0 4.7 5.0 2.5 3.8 6.5 6.0 5.0 4.4 5.1 4.7 4.4 6.1 4.8 5.3 5.3
##  [991] 3.3 5.1 5.0 5.0 2.4 5.1 6.1 4.7 3.0 4.2
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
##    [1] 5.2 4.6 4.3 4.0 4.7 5.4 4.0 3.1 4.7 4.9 6.0 5.6 3.9 4.6 4.7 3.7 3.9 4.8
##   [19] 4.3 5.6 5.1 3.5 5.9 5.2 3.4 4.0 4.5 5.1 3.5 4.8 3.7 3.4 5.3 3.7 6.7 3.3
##   [37] 4.6 5.3 3.8 3.7 4.3 3.7 4.1 5.1 5.4 6.0 6.5 3.4 4.8 4.7 4.0 2.3 4.7 4.2
##   [55] 4.9 5.3 4.4 4.6 4.7 5.1 3.1 4.8 2.8 5.3 3.1 3.8 3.4 6.1 4.0 3.7 4.6 4.9
##   [73] 3.4 4.9 5.2 4.3 4.9 3.8 5.1 5.1 4.6 4.0 4.7 3.4 3.4 6.2 4.5 6.7 4.1 4.5
##   [91] 4.8 3.8 4.3 3.4 3.9 3.1 4.9 6.2 3.8 4.8 4.7 4.6 4.3 4.0 4.7 4.5 4.8 4.3
##  [109] 4.7 4.1 4.4 4.1 4.4 4.0 2.9 4.1 4.1 3.6 4.1 4.6 5.0 3.2 5.6 4.3 5.5 4.2
##  [127] 5.7 4.7 2.9 3.0 5.7 4.0 4.4 7.1 3.9 3.6 5.4 4.7 5.3 4.6 5.0 5.7 5.0 4.3
##  [145] 4.9 5.5 3.8 5.9 4.6 6.9 5.7 5.4 5.2 4.1 3.8 4.9 6.3 3.6 3.2 5.2 5.3 5.2
##  [163] 3.6 3.5 4.4 4.6 2.6 5.1 4.3 4.4 6.3 5.3 6.6 4.7 3.7 5.8 5.1 3.7 3.5 4.8
##  [181] 2.6 3.7 3.7 6.5 4.7 4.4 4.6 4.0 4.5 5.3 4.2 4.6 3.5 4.7 5.7 3.6 5.2 4.7
##  [199] 3.2 4.2 4.5 4.0 3.4 3.6 4.1 5.7 3.5 4.2 4.5 3.2 4.0 5.7 5.6 5.1 4.1 5.0
##  [217] 4.4 4.5 4.9 3.6 4.0 3.0 3.8 3.4 5.9 4.5 5.1 4.1 5.7 4.6 4.0 5.4 5.1 5.4
##  [235] 4.3 3.1 4.1 4.8 4.6 4.8 5.3 2.9 3.8 4.1 4.6 5.1 4.0 5.1 4.2 4.4 4.5 2.6
##  [253] 4.6 4.7 4.8 5.0 4.9 4.1 3.7 5.6 5.9 3.7 3.5 2.7 4.0 5.2 4.6 5.0 5.6 5.2
##  [271] 4.1 3.0 5.0 4.2 4.1 6.6 4.4 4.7 3.7 3.5 3.6 5.0 6.4 4.6 4.5 4.0 5.4 5.0
##  [289] 4.5 4.0 3.8 3.5 5.2 6.3 5.0 3.6 4.6 4.7 5.8 5.0 4.5 3.7 4.9 4.4 4.5 4.8
##  [307] 4.8 4.6 3.2 3.4 3.6 3.2 4.1 4.2 5.3 4.6 4.1 4.2 4.5 5.0 2.7 3.0 3.9 4.0
##  [325] 3.3 4.4 6.6 5.2 6.7 5.2 4.2 5.7 5.0 3.7 4.0 4.3 4.9 4.8 5.2 7.1 3.5 4.4
##  [343] 4.6 4.9 4.3 4.7 5.4 4.0 5.3 4.0 4.3 5.0 5.7 4.1 5.0 4.5 3.8 3.0 5.6 4.4
##  [361] 5.4 4.7 3.7 4.8 4.2 5.7 4.0 4.6 4.3 2.9 2.9 5.5 3.9 4.3 5.7 4.1 4.3 5.3
##  [379] 4.6 3.5 5.0 5.5 4.7 4.4 4.3 5.4 5.2 4.2 4.1 4.4 5.1 4.3 3.6 3.5 3.3 4.9
##  [397] 4.7 4.1 2.8 3.5 6.7 4.6 3.6 3.3 3.6 5.3 5.1 4.6 5.6 5.4 4.3 4.0 6.3 3.8
##  [415] 4.0 3.0 4.3 5.5 5.4 4.0 5.8 4.3 3.7 3.1 2.8 3.3 4.5 4.7 6.3 4.7 3.9 6.7
##  [433] 5.2 3.8 5.3 3.9 4.1 4.7 4.4 4.5 4.0 4.2 4.6 4.4 4.3 2.9 3.4 4.4 4.7 4.8
##  [451] 5.4 3.1 3.4 5.2 6.0 3.9 6.3 2.2 5.2 4.5 3.5 3.5 2.4 5.0 5.0 4.6 6.0 4.9
##  [469] 7.1 5.6 3.5 4.3 3.6 5.2 5.4 6.2 4.2 3.4 4.4 5.8 5.4 3.8 5.0 3.7 3.1 5.5
##  [487] 4.2 5.4 3.6 4.5 5.6 5.9 4.9 5.3 4.0 5.4 6.0 5.1 3.7 4.3 5.0 4.7 5.3 5.5
##  [505] 6.6 3.5 5.3 4.0 3.6 3.8 5.0 3.8 5.3 4.8 3.2 4.6 4.2 5.3 5.1 4.2 3.3 4.9
##  [523] 4.0 5.4 5.7 5.8 4.8 4.0 4.1 6.2 4.6 2.4 4.7 5.5 5.2 4.0 5.1 2.7 4.1 3.0
##  [541] 3.3 2.4 3.1 4.8 5.4 4.6 4.4 5.1 3.9 4.0 5.8 3.9 4.3 3.5 4.6 6.3 5.0 5.4
##  [559] 4.1 2.9 3.8 3.3 5.1 4.1 5.6 5.4 4.8 4.5 5.2 3.1 3.5 4.6 4.5 4.9 6.3 3.7
##  [577] 5.6 4.1 4.8 5.0 4.4 3.4 5.2 4.2 3.8 6.8 4.1 4.6 5.3 4.2 5.0 4.0 5.6 3.8
##  [595] 4.4 4.6 4.6 4.4 2.9 4.5 3.8 3.9 4.5 4.2 3.9 5.2 3.2 5.3 5.1 5.6 4.6 4.4
##  [613] 4.3 4.8 4.8 5.4 4.0 4.3 3.5 2.7 3.6 4.4 3.1 4.9 2.8 4.3 3.3 4.5 4.1 4.5
##  [631] 3.5 3.9 4.6 5.4 4.8 4.0 5.4 5.1 3.8 3.4 4.9 3.6 4.4 5.5 4.7 5.4 4.5 3.2
##  [649] 3.4 6.3 5.1 3.7 3.7 6.0 3.4 4.2 5.7 4.6 6.5 3.3 5.7 4.5 3.6 5.2 6.0 4.7
##  [667] 4.0 5.2 5.6 4.6 5.0 4.4 4.0 3.7 3.9 5.5 4.8 5.2 5.2 5.5 5.4 4.9 4.0 4.6
##  [685] 3.2 3.6 4.3 2.9 4.9 3.8 4.1 3.4 5.0 4.3 5.6 5.4 5.6 3.0 4.7 3.9 4.2 5.2
##  [703] 3.3 4.9 3.8 4.9 4.5 4.9 4.9 4.0 4.7 4.8 4.8 5.7 3.7 2.9 2.0 4.5 5.6 4.8
##  [721] 4.7 4.2 3.9 5.0 4.5 2.5 4.8 3.2 3.4 5.4 7.3 3.0 4.6 4.5 6.2 4.0 5.8 1.9
##  [739] 4.7 5.6 4.1 5.5 4.9 3.6 6.0 3.9 5.0 5.5 4.4 4.3 5.1 5.2 4.6 4.5 5.9 4.2
##  [757] 4.6 3.6 4.9 5.1 5.0 5.6 4.2 4.0 6.2 4.1 4.1 4.8 3.5 3.9 6.0 4.5 3.5 3.7
##  [775] 3.4 4.0 2.9 5.1 4.6 5.3 4.4 5.0 5.3 5.9 4.5 4.3 3.3 4.4 4.7 4.5 4.4 3.4
##  [793] 4.5 4.2 5.0 3.8 4.6 2.5 4.9 5.5 5.7 4.1 5.6 3.9 3.3 3.0 5.2 3.0 4.5 4.9
##  [811] 5.1 3.0 5.6 4.5 5.9 4.6 4.0 4.5 5.3 4.9 4.0 4.0 4.7 3.4 4.2 4.8 3.8 4.4
##  [829] 5.5 5.3 5.5 5.0 5.5 3.8 5.8 3.5 3.0 5.4 5.0 4.0 4.8 4.5 6.4 5.3 4.7 4.4
##  [847] 4.9 5.6 3.7 3.8 4.2 4.1 4.8 4.2 4.8 5.9 5.1 5.2 4.2 5.1 3.3 5.0 5.6 2.7
##  [865] 4.4 2.9 4.8 5.8 3.4 4.5 4.1 5.4 6.0 4.3 4.5 4.1 5.2 3.2 5.5 5.4 5.0 3.9
##  [883] 2.5 3.9 4.8 3.7 5.2 5.1 5.2 5.5 4.9 3.3 4.2 5.0 4.4 4.5 5.6 3.2 5.4 4.0
##  [901] 6.0 5.3 4.7 2.9 4.7 4.7 3.8 4.1 5.5 3.0 3.5 2.3 4.5 3.7 5.2 3.5 4.3 5.3
##  [919] 4.4 5.4 5.9 4.3 3.6 3.8 4.5 5.4 3.2 5.3 5.3 5.4 4.9 5.1 5.6 4.9 4.7 5.7
##  [937] 4.3 5.2 3.7 5.5 4.5 4.1 3.2 5.2 3.8 4.2 4.1 6.2 4.3 5.2 3.6 3.6 3.3 6.2
##  [955] 5.7 5.1 5.5 4.9 4.3 3.6 3.8 4.3 5.9 5.0 3.6 5.8 4.2 4.5 4.9 5.8 4.9 3.2
##  [973] 3.5 5.3 4.0 4.0 5.5 2.8 4.1 4.6 4.8 4.6 3.6 3.9 3.7 5.5 4.0 3.5 3.8 5.0
##  [991] 4.7 4.4 5.1 3.9 4.4 4.1 4.2 4.4 4.7 3.8
## 
## $func.thetastar
## [1] 0.0081
## 
## $jack.boot.val
##  [1]  0.53859649  0.39030303  0.28005780  0.10859599  0.06130952 -0.01828255
##  [7] -0.11925466 -0.19393064 -0.34432432 -0.49766764
## 
## $jack.boot.se
## [1] 0.9249469
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
##    [1] 3.6 4.2 5.3 5.4 5.1 4.8 4.5 4.6 3.6 2.9 5.2 6.1 5.0 4.5 4.3 4.6 5.9 3.4
##   [19] 5.6 4.4 4.7 4.2 3.2 6.1 5.1 4.3 4.4 4.0 3.8 6.1 3.2 5.0 5.5 4.8 5.6 4.0
##   [37] 5.8 3.2 4.0 3.1 4.3 1.9 4.1 4.1 3.5 3.5 4.2 5.2 3.7 4.3 5.1 6.5 2.8 3.6
##   [55] 3.5 4.3 3.8 4.5 4.4 4.9 5.8 4.4 7.2 4.3 3.4 5.2 4.8 5.1 4.1 4.6 4.8 5.8
##   [73] 6.7 4.0 4.6 4.2 3.8 6.3 3.7 3.1 5.1 4.0 4.6 4.1 4.9 5.1 4.1 4.6 3.7 4.0
##   [91] 5.5 4.0 4.1 1.9 3.6 5.0 4.4 3.2 4.8 4.7 6.6 5.8 4.6 5.4 3.1 3.9 5.1 4.0
##  [109] 3.3 2.1 5.1 4.1 5.2 5.0 4.3 3.0 5.6 4.6 4.4 4.6 4.1 4.4 4.8 4.9 5.9 4.7
##  [127] 4.1 4.8 5.8 3.2 3.9 4.9 4.0 3.7 4.0 3.5 4.4 5.6 5.1 4.3 4.6 5.0 5.0 4.4
##  [145] 5.5 4.5 5.2 4.4 4.5 4.9 3.7 2.4 5.0 5.1 7.0 4.2 5.8 3.7 3.7 5.4 4.2 5.0
##  [163] 5.1 2.9 3.9 6.8 4.0 4.3 4.6 4.8 3.9 4.7 4.5 4.6 5.0 4.8 3.6 4.5 4.6 3.3
##  [181] 4.1 4.3 4.4 4.1 4.4 5.3 5.3 3.6 2.7 5.4 5.4 5.1 4.7 5.8 4.7 4.2 4.7 4.8
##  [199] 3.3 3.7 5.1 4.4 5.4 4.0 3.6 5.1 5.4 3.7 5.0 4.8 5.1 4.6 3.2 3.1 3.4 5.9
##  [217] 4.9 2.1 3.1 3.9 5.0 5.7 3.9 4.6 5.1 5.9 4.5 4.3 3.0 4.6 3.5 3.0 5.2 3.8
##  [235] 4.7 4.2 2.7 5.3 3.4 3.4 4.7 2.9 5.9 4.9 3.3 4.6 4.3 4.7 2.9 4.7 3.9 5.0
##  [253] 2.6 4.8 6.0 7.1 5.0 5.0 5.3 3.5 2.5 5.1 5.3 5.0 4.9 5.3 4.1 4.7 5.3 2.8
##  [271] 4.6 4.7 4.2 4.5 3.5 5.7 5.9 5.2 4.5 3.8 5.3 3.9 5.7 5.8 5.4 4.8 3.7 4.7
##  [289] 3.2 4.0 6.2 6.6 4.9 2.6 2.8 7.1 2.0 5.5 4.5 5.9 5.6 4.4 3.5 4.3 5.0 3.7
##  [307] 6.0 6.1 4.3 6.3 3.9 2.8 4.6 3.6 5.1 5.2 4.7 3.3 3.8 3.7 4.1 3.9 5.2 5.8
##  [325] 6.2 6.0 4.8 3.7 4.7 5.6 3.6 3.9 4.3 4.8 3.8 3.0 3.8 2.7 4.9 5.4 4.7 3.3
##  [343] 4.3 4.9 4.5 5.2 3.9 4.8 5.3 4.2 5.1 6.0 5.4 5.9 3.6 5.2 4.3 4.7 5.2 3.4
##  [361] 3.8 5.7 6.2 5.5 5.8 5.4 4.7 4.7 2.4 3.9 3.0 3.9 6.9 6.2 4.9 4.3 5.4 6.3
##  [379] 4.3 6.3 3.3 4.1 5.1 4.9 3.4 6.0 5.7 4.4 4.2 4.0 4.3 5.8 4.4 2.4 5.7 3.3
##  [397] 4.1 4.5 4.6 4.3 5.4 3.9 6.6 3.8 3.9 5.0 4.2 6.0 4.7 3.6 4.1 4.2 4.9 5.0
##  [415] 5.5 4.2 5.6 3.6 5.3 4.5 2.8 4.5 2.9 3.3 4.4 4.3 5.1 4.7 5.8 4.5 4.2 4.6
##  [433] 4.9 3.7 4.4 4.4 6.0 4.4 5.4 4.2 4.7 4.8 4.5 3.3 3.0 3.1 5.2 4.5 3.9 5.7
##  [451] 4.0 5.3 5.0 3.9 5.7 4.4 5.2 3.8 4.4 4.0 5.1 4.0 5.9 3.5 1.9 4.6 5.1 3.2
##  [469] 5.5 4.2 5.7 5.8 5.6 4.0 2.4 3.9 4.0 4.3 5.0 5.1 5.3 6.7 3.7 5.3 4.3 3.4
##  [487] 4.8 4.7 4.9 3.8 4.8 5.3 6.0 2.4 5.2 4.9 3.6 3.6 4.3 5.0 3.5 5.2 4.8 6.4
##  [505] 4.5 5.8 3.6 3.4 5.4 4.2 5.9 5.0 2.9 5.0 4.3 4.7 3.6 4.6 5.1 4.8 4.1 4.8
##  [523] 4.9 3.9 3.5 4.7 6.1 5.9 4.2 4.6 3.8 3.8 5.0 4.5 5.3 6.0 3.9 3.9 4.3 3.5
##  [541] 4.3 3.3 5.5 5.1 3.8 6.0 4.0 5.6 4.2 3.5 4.2 4.9 4.3 5.0 4.4 5.3 5.0 3.9
##  [559] 4.8 5.7 3.2 3.8 3.0 4.5 3.5 5.3 5.3 5.0 5.4 5.0 4.0 4.1 3.6 4.4 3.8 5.6
##  [577] 3.3 4.9 3.9 5.6 5.0 5.1 5.2 4.6 5.5 5.1 3.8 4.7 4.0 4.1 5.1 4.7 3.3 5.4
##  [595] 6.4 3.0 5.7 4.6 3.9 4.6 4.6 3.3 4.4 3.8 5.1 5.0 5.7 4.3 5.5 4.7 5.7 4.4
##  [613] 3.2 3.4 4.2 3.1 3.9 4.0 4.0 2.4 4.8 4.3 5.1 4.3 4.8 3.9 5.1 2.7 3.6 5.1
##  [631] 3.9 5.1 2.9 6.1 5.9 5.3 4.4 4.7 6.0 4.3 6.8 5.3 4.7 3.9 5.7 4.1 6.3 3.4
##  [649] 3.4 4.5 4.3 4.7 6.0 5.3 5.5 3.9 3.1 4.3 4.5 4.9 4.0 4.2 4.6 3.2 3.6 4.9
##  [667] 4.9 4.3 5.1 4.4 4.7 2.7 3.8 4.5 2.7 5.7 4.4 4.6 4.7 6.4 5.9 5.1 4.3 4.0
##  [685] 3.3 3.5 2.1 4.9 3.6 5.3 3.9 4.3 4.8 5.4 3.4 5.2 4.6 5.4 5.5 4.1 4.8 4.4
##  [703] 4.3 4.1 4.7 4.6 4.8 4.4 4.2 5.0 4.4 3.6 3.8 4.2 3.4 4.4 5.7 4.8 6.4 6.0
##  [721] 3.7 2.9 3.7 3.1 4.3 3.4 4.1 3.4 4.9 5.2 6.2 4.7 4.6 5.3 4.5 4.7 5.2 2.6
##  [739] 5.0 2.9 4.2 4.6 4.2 5.1 3.1 5.4 5.5 5.6 5.0 3.6 3.4 5.8 3.4 3.7 3.0 4.7
##  [757] 5.4 3.7 4.6 5.2 4.6 5.7 3.6 3.5 5.3 6.1 5.1 5.9 6.4 4.6 4.1 5.5 3.1 3.9
##  [775] 4.0 4.5 6.8 5.9 3.4 4.4 5.0 3.8 3.3 5.3 4.8 3.9 5.8 4.3 3.9 4.1 4.1 4.4
##  [793] 3.5 4.3 4.9 4.8 4.2 3.1 5.1 5.5 4.7 3.4 4.6 4.6 4.1 6.0 5.8 4.8 5.7 4.2
##  [811] 2.9 2.6 4.0 5.1 3.8 2.7 4.4 4.2 6.6 4.9 4.2 5.7 4.1 5.0 4.4 4.4 4.2 5.0
##  [829] 5.1 4.0 5.1 4.1 4.4 5.4 3.8 4.3 5.4 5.3 6.3 3.8 2.2 5.5 5.5 4.7 4.1 4.2
##  [847] 4.9 4.0 5.3 3.7 4.8 3.9 4.0 4.7 4.2 3.8 5.1 2.9 2.5 4.2 4.6 2.7 5.3 4.7
##  [865] 5.2 6.3 4.8 4.9 3.6 5.3 3.5 2.8 3.7 3.9 4.4 6.5 5.9 3.8 4.5 4.1 2.7 6.9
##  [883] 4.7 4.2 3.2 4.3 4.3 5.7 5.3 2.7 3.5 5.8 3.7 4.4 4.3 3.3 4.3 2.4 4.9 4.3
##  [901] 4.1 3.5 4.1 4.1 4.0 5.1 4.8 4.7 4.5 4.2 3.5 3.0 4.4 5.8 4.6 4.3 5.0 4.4
##  [919] 4.9 3.5 5.5 4.9 5.9 5.1 4.1 4.3 5.1 5.1 4.8 4.7 5.7 5.8 4.3 4.5 3.9 4.4
##  [937] 5.6 3.1 5.4 6.2 5.0 4.6 4.6 6.1 4.9 3.8 4.9 3.8 6.2 6.3 4.8 3.6 4.0 5.7
##  [955] 5.2 5.2 4.8 5.6 4.8 3.3 5.0 3.3 3.4 4.4 4.6 5.3 4.2 5.2 4.6 4.4 4.1 3.9
##  [973] 4.7 4.0 4.9 5.5 2.8 3.2 5.9 3.1 3.6 4.6 3.2 4.5 4.8 5.5 3.4 3.7 2.8 5.3
##  [991] 5.5 5.0 3.5 5.9 4.3 4.2 3.8 5.6 5.5 3.8
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.4 5.5 5.3 5.3 5.2 5.1 4.9 4.8 4.7 4.4
## 
## $jack.boot.se
## [1] 0.9967949
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
## [1] 1.139365
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
##   12.229747   18.689347 
##  ( 5.396359) ( 8.418034)
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
## [1] 1.6455655 0.1805276 0.9173699 0.6101449 0.8144002 0.8072664
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
##    [1] -0.193710749  2.101068748  0.626177805  0.103038075 -0.896033262
##    [6] -0.254479011  1.350458456  0.736285454  1.236108440  1.052887937
##   [11]  1.016978545  1.289609471  0.047056999  1.529786377  1.043038832
##   [16]  0.583287346 -0.377371337  0.615425497  0.866287029  1.631726285
##   [21]  0.387129863  0.907088199  0.920759653  0.067152551  1.506812849
##   [26]  0.830473837 -0.846920161  1.157368815  0.907722692  0.680796698
##   [31]  0.021531447  1.492448424  0.873622551  1.083157758  0.778298708
##   [36] -1.007147214  1.092701381  1.776765827  0.291494378  0.854434267
##   [41]  2.331020187  1.689276093  1.082001305 -0.445844231  1.247865958
##   [46] -0.287973884  1.662909265 -0.231748955  1.662909265  1.480738271
##   [51]  0.027816087  0.781323485 -0.131870297  0.364371140 -0.604419276
##   [56] -0.565850239  1.200828891  0.911778807  1.260010197  0.393159221
##   [61]  0.966509423 -0.072707595 -0.607175752  1.271341998  1.534648791
##   [66]  0.516800747 -0.314228389  1.103437427  1.328237660 -1.112525643
##   [71] -0.323997400  0.624693769  1.766747239  0.491885197  1.072649760
##   [76] -1.513957102 -0.032837371  0.269509101  1.474915763  0.481828204
##   [81]  1.182563687 -0.222335402 -0.622942541  0.875319081  1.302950312
##   [86]  0.796395929  1.022483627 -0.085822575  0.767925480 -0.040377075
##   [91] -0.778035111  1.031624561  1.068739876  0.044634035  1.199535606
##   [96] -0.964918052  1.508613194  0.091774880  1.614672360  0.916483365
##  [101] -0.311988386  0.693193039  0.963367596  1.925501716  1.355714691
##  [106]  0.197047904  0.608480432 -0.934330376  0.503884021  1.085591184
##  [111] -0.389446877  1.527741406  0.674510069  1.045036258 -1.659402521
##  [116]  0.915525692  1.037353981 -0.036405866 -0.567461486  0.491895965
##  [121]  1.495948555  1.011989950  1.297078195  1.052950952  0.006597699
##  [126]  1.089617360  0.372585247 -1.138138447  1.159601797 -0.299226002
##  [131] -0.273489211  1.862932620  0.649710984 -0.366367509  0.370876704
##  [136]  1.020643128  1.386465685  1.384419794  1.479957310 -0.294049089
##  [141]  0.443367949  0.031360123  1.659129357  0.022547920 -0.449500634
##  [146]  0.505094884 -1.196347233 -0.656383329  1.387160949  0.784277097
##  [151]  1.140322476 -0.226777512 -0.310765078  0.540743196  0.897665540
##  [156]  0.176831367  1.724339920  0.852159136  0.582695707  0.831030443
##  [161]  0.385439419  1.987936549  1.055663080  1.027495659  0.833653047
##  [166]  0.634571090  1.308274373  0.712273528  0.736471923  0.840432947
##  [171]  0.915525692  0.567758556  0.942618670 -0.038823731  0.877898685
##  [176]  1.473088366  1.570442680 -0.822200631 -0.368751344 -0.186253955
##  [181]  0.613507765  0.954972314  0.182495475  1.190143604  0.898867984
##  [186]  1.495887584 -0.580679321  0.198579583  1.132399079  1.495094576
##  [191]  1.006482776  1.865366768  1.135282085  0.875523242  1.174082892
##  [196] -0.437997784  0.732808719  0.788775791  0.982406923 -0.262469806
##  [201]  1.108681339  1.335256432  0.754079619  0.976529459  0.909502299
##  [206]  1.343792203 -1.082664093 -0.850704002  0.452009552  1.084179688
##  [211]  1.558412105  1.481044235  1.157745787  1.017967138  1.443814035
##  [216]  0.362448084  0.803838558  0.994675021  1.944009720  1.326967675
##  [221]  1.103222237  1.286769073  1.262647546  0.926552338  0.761467839
##  [226]  1.130839481  1.935985776 -0.533643664  0.219771156  1.609520713
##  [231]  0.998767453  0.309450531 -0.939747257  1.087520241  0.812966888
##  [236]  0.562911626 -0.510930972  1.580561734  0.094909290  1.096480391
##  [241]  0.582177253  0.701133789  0.462029352  0.758423441  1.064006794
##  [246]  0.902431994 -1.096968666 -0.616183228  0.725347909  1.282737885
##  [251]  0.817685840  1.293797388  0.894257260 -0.183726653  0.007062193
##  [256] -0.696660043  1.206434454  0.809947902  0.930736938  1.462772454
##  [261] -0.399002458 -1.525292474  0.875609785  1.481044235  1.442208952
##  [266] -0.220289436  1.466729178  1.236716213  0.321063702 -0.692330523
##  [271]  1.486939045 -0.297058703  1.200023465 -0.688405056  0.124300271
##  [276] -0.170841476 -0.605418063  0.623040856  1.245901691  0.993219480
##  [281]  1.486570764  1.316009108 -0.378930121  0.902155950  1.214637488
##  [286]  0.711621529  1.137228295  0.894433451  1.021487518  0.968601317
##  [291]  1.402007769  1.576747429 -0.337107501 -0.116805153  0.603924008
##  [296]  1.089888833  1.735019184  0.822524098 -0.597054324  0.171108468
##  [301] -0.011691581  1.260010197  0.519072542  1.271287718  0.686763680
##  [306]  0.209953665  0.011353740  0.867736023  0.045777127  1.696885295
##  [311]  0.351239595 -0.033178315  1.527364951 -0.414842903  1.462753208
##  [316] -0.508868525 -0.280257829  1.724439913  1.049235624  1.236348903
##  [321]  0.039097310  1.326992048  1.193617046  1.934797254  1.144306577
##  [326]  2.436606857  1.745366209  1.516091751  1.303541443  1.298768597
##  [331]  1.453031630 -0.795178739  1.247490312 -0.369529082  1.922771298
##  [336]  0.755640057 -0.548701138  0.968589343  1.118117170 -0.379470088
##  [341]  1.326315661 -0.782924601  1.198673933  1.645804248  1.004795315
##  [346]  1.154405888  1.368827304  1.010059353 -0.335828068 -0.829550935
##  [351]  2.101068748  1.260554585  1.045832008  1.396419518  0.714083840
##  [356]  0.023106283  1.116446661  0.947291597  1.447637464  0.985253245
##  [361] -0.056495319 -0.354087471  1.334262016  1.425082742  0.458863297
##  [366]  0.097378742  1.127674131 -0.790616470  0.980973863  0.488223028
##  [371]  1.185174301  0.634289555 -0.926501922  1.699646205  1.677447103
##  [376]  0.379687216  1.193562427 -0.303533757  1.375522064 -0.594132405
##  [381] -0.230298305 -0.104419492  1.085591184  1.277744333 -0.171328901
##  [386]  1.801093206 -0.491377490  1.058655040  0.324647922 -0.336036039
##  [391] -0.020494910  1.644944617  1.140655230 -0.352205528  0.798521700
##  [396]  1.150425027  1.209313037  0.884379353 -0.312905111  0.643975018
##  [401]  0.719753647  0.962036934  0.639561100 -0.368147571  0.751295824
##  [406]  1.061727478  1.221766243  0.578071803  1.424403386 -1.062971333
##  [411] -0.639887592  0.699043320 -0.473606907  1.003187112  1.141516790
##  [416] -1.197575859  1.836525602 -0.519992065 -0.261399103  0.910972340
##  [421] -0.481504529 -0.246298080 -0.693716496  0.185641082  1.140563373
##  [426]  0.349496645  0.304661315 -1.033485832  1.117851732 -1.350775569
##  [431] -1.138542546 -0.502204529  0.128495115 -0.636989509  0.205287938
##  [436]  0.810811014 -1.138138447  0.751295824 -0.402478896 -0.497559993
##  [441]  0.862644093  1.069983898  0.939408941 -0.427603204  1.829632365
##  [446] -0.247446430  0.425513349  0.692933732  0.004096430  1.042752238
##  [451]  0.453213744 -0.131582034  0.650825588  1.140322476 -0.494735489
##  [456]  0.782867979 -0.573610412  0.968601317  1.341848270 -0.289388372
##  [461] -0.622833041  0.731822236  1.497764265  1.189033131  1.134992791
##  [466] -0.003669670  1.122573156  1.518752599 -0.821124282  1.331949751
##  [471] -0.626124527  1.118117170  1.109389256  1.754555258  1.269368752
##  [476] -0.089578598  0.954417630  0.427768699  0.010678708  0.939867734
##  [481]  1.102884418  1.086689392  0.881819590  1.000981245  0.913085285
##  [486] -0.255395525  1.528122452  1.601078430  0.465714445  1.068739876
##  [491]  0.974100290  0.428425843  0.892701587  0.443019534 -0.300851933
##  [496] -0.631543653 -0.538001748  1.271162919  0.925841991  1.653150419
##  [501]  1.439920885 -0.449077600 -0.782957289  1.555356868 -0.246913474
##  [506] -0.690550112  0.600558952 -0.525557922  1.380402176  1.930876018
##  [511] -0.165632255  0.723454746  0.460486166 -0.601698413  0.659773312
##  [516]  1.407099834  1.061366310 -0.364575271  1.758738281 -0.434869994
##  [521]  0.868415228  1.880436027 -0.243476797  0.755640057  0.114159426
##  [526]  0.078071208  0.700412102  1.535034572  1.878069962 -0.169434866
##  [531] -0.152704276  1.457558056  1.239335208 -0.007047764  0.523234997
##  [536] -0.184401263  0.150321689  1.270386178 -0.396171164  0.921738255
##  [541] -0.938915350 -0.673701064 -0.448292742 -0.093463430 -0.232802234
##  [546]  1.330730735  0.608002656  0.894205758 -0.092192728 -0.496726974
##  [551]  1.547185693 -1.239761123  0.293842194  0.637456641  0.759890044
##  [556] -0.208477081  0.744517089  0.153105478  0.510810022 -0.551999700
##  [561]  0.816037577  1.945932884  0.873133031  2.039601471  1.243306659
##  [566]  0.659209268 -0.951661631  0.826755566  1.025177268 -0.120827676
##  [571]  1.721452218  1.271382600 -0.225543718 -0.908754007 -0.262427175
##  [576]  0.006067682  1.452793219  0.059167397  0.717101600 -0.683790940
##  [581]  1.051959132  0.684870747  0.140294579 -0.923903626 -1.796414631
##  [586]  0.790508354 -0.083472140  0.911006822  0.133399205  0.186581638
##  [591] -0.033687537  1.039814434  0.860235420  1.122305290  1.666941812
##  [596]  1.882697374  0.735303237  1.057825328 -0.270028573  0.478123575
##  [601]  1.149792919  1.028581410  0.650957175  0.395474490  1.223271951
##  [606] -0.505623297 -0.855683905  1.556861744  1.457444192 -0.749777776
##  [611]  1.438448601  1.215488807  1.940323405  1.336192454  1.000248442
##  [616]  1.017754266 -0.684547301 -0.264572369 -0.515775679  1.106885853
##  [621] -0.144591974  1.113794083  1.076865311  1.396956875 -0.881137474
##  [626] -0.518157220 -0.473847385  0.628647378 -0.607300210  0.834969695
##  [631]  0.076249701  1.493387298 -1.320455484  1.267282816  0.129506803
##  [636]  0.297537699  0.144676203 -0.538833703  1.187135777  1.198824584
##  [641]  1.158876431  0.138276904  1.210722096  0.557927037  1.545219755
##  [646]  0.921137575  1.811366412  2.053267970 -0.880849354 -1.356930462
##  [651] -0.402478896  0.925020158  1.375868933 -0.275066143  0.091421724
##  [656]  1.161265996  0.640453253 -0.536430923 -0.042367942 -0.358052432
##  [661]  0.664186444  1.017750293  1.016539801  0.900457812  0.963814220
##  [666]  0.915320948 -0.827576682  1.031097216 -1.249547333  0.963367596
##  [671]  1.323987440  0.794712303  0.474905974  0.876368814  1.765977015
##  [676]  2.009582532  1.193899456  0.461034435 -0.235211488 -0.963095890
##  [681]  1.900371944  0.134094678 -1.341263111  1.190640801  1.087520241
##  [686] -0.867190798  0.493841110  1.011774833  1.443096693 -0.471027231
##  [691] -0.911280606  1.325346658  0.442391620  1.124821172  1.310100754
##  [696]  0.727948328  1.316157482  1.209537773  1.096485509  0.446638756
##  [701] -0.263960629  0.474458724  0.702908658  1.115018508  0.607332140
##  [706]  1.019585843  0.094014179  1.705852317  0.125097298 -0.121701074
##  [711]  0.612411135  0.378539401 -0.501965071  0.636200129  1.598664639
##  [716]  1.433353301  1.513374363  1.104658507 -0.669405672  0.031362411
##  [721] -0.220496062 -0.197607571  0.908286911  0.598875784 -1.428596241
##  [726]  1.133344698  1.223875297 -0.130799112  1.365412708  0.005584587
##  [731]  1.126749014 -0.111443731  1.144969275 -0.866896701  0.989616807
##  [736]  1.928492984  0.624415254  1.334782961  1.600718003  1.274666981
##  [741] -0.249962274  0.535053522  0.867462428  0.537629926  0.495420849
##  [746]  0.937105633  1.045241509 -0.606269463  1.068328710  0.621122016
##  [751] -0.416169144  0.476415907  1.145283353  1.667522790 -0.872221173
##  [756]  0.728203767  1.427898294  1.490042852  0.867593554  0.964297719
##  [761]  0.269509101  0.794002580  1.946540978 -0.796652066 -0.551999700
##  [766]  0.883491656  0.554574451 -0.306674166  0.241207398 -0.738958428
##  [771]  0.951391641  1.259320093  0.472967142  0.931231621  1.137083900
##  [776]  1.382176909  0.639922191 -0.191961614  1.598697253  1.552951572
##  [781]  0.951291768  0.720835118  0.781170968  1.332737000  1.200425773
##  [786] -0.305584995  1.494695856 -0.289070705 -0.741681173  0.159252743
##  [791]  1.254929614  0.890237731  0.608371498  1.200825839 -0.187373420
##  [796]  0.648353233  0.726977952 -0.597532084  1.123058950  0.508249283
##  [801]  1.906801991 -0.274190809  0.215133046  1.331926232  1.109421393
##  [806]  0.799633173  0.751496170  0.222400427  1.004547874  0.985503819
##  [811]  1.343864313  0.663196293  0.985285269  0.184548122  1.453004242
##  [816] -0.044635339  1.605302022  1.026816621 -0.159080813 -0.252880271
##  [821] -0.084159228  1.125273422  0.982999174  0.929877978  1.527231794
##  [826] -0.982744834  0.575710103  1.535424471  0.215680060 -0.499531350
##  [831] -0.220496062  1.401792711 -0.945764582 -0.436452349 -0.839726895
##  [836]  0.952808808  0.912413834  1.284725571  0.781658851  0.025585771
##  [841]  1.466093381  0.297236288  1.240099945  1.065308739  0.834034953
##  [846] -0.337107501  1.266845476 -0.613773566  0.140501271  1.128937717
##  [851]  0.775171802  0.189366036  0.835131419  1.376486998  1.297645529
##  [856]  1.915893529  1.012689941  0.933611584  1.280434113  0.375149646
##  [861]  0.953489766  0.904899753  1.677690831  0.390057778  1.246646549
##  [866]  1.640014620  0.636608631  0.586671793  1.432916142  0.295301214
##  [871] -0.032059453  1.284725571  1.191923859  0.929873615  0.402715264
##  [876]  1.309658160  2.379047891  1.100699768  0.870861362  1.484532566
##  [881]  1.029654036  1.071048780  0.272358947  1.511075867  1.648063332
##  [886]  1.880157947  1.630117011  0.004569113  1.023733514  0.148316272
##  [891]  1.602317722  0.102251699  1.337812903  0.831023853 -0.578895170
##  [896]  1.346699298  0.745843131 -0.918350992 -0.003830567  0.101035593
##  [901] -0.209556679  1.264983613  0.965277043  0.567552675  0.884002754
##  [906]  0.749504578  0.838342172  0.045610482  0.634820322  0.669390299
##  [911]  0.760443240  0.373850946  0.714658900 -0.004667476  1.202233761
##  [916]  0.974404504  1.079946563  0.003822266 -1.191675704  0.658062892
##  [921]  0.903558617  1.349932102  0.339308508  0.988677321 -0.063927882
##  [926] -0.536086813 -0.365482407 -0.606693043  0.015838748 -0.639887592
##  [931]  1.205162739  1.336258214  0.280306210 -0.176198689  1.568778910
##  [936]  0.568340937  0.983610850  1.430399587  1.387129449  1.912347956
##  [941]  0.771842600  0.908529047  0.930653182  0.560000584 -0.133785752
##  [946]  0.348218109  0.701794250  0.039150817  1.481044235  1.644944617
##  [951]  1.238040476  0.095478463  0.860456025  1.070194206  1.441128646
##  [956]  1.228718312  0.599243932 -0.243671438  0.850964703 -0.687473499
##  [961]  0.032006653  0.874717398 -1.073655622  1.034919337  1.230877452
##  [966]  0.514632727  1.430592750  0.401592303  0.637262047 -0.757777165
##  [971] -0.274899173 -0.601791791  0.803764858  0.491895965 -1.314017237
##  [976]  0.703884290  1.517053479  1.272260965  0.003701889  1.026734923
##  [981] -1.248468865 -1.140963509  1.086988422 -0.606661007  0.568268636
##  [986]  0.252358187  0.828147066  0.951291768  0.996721199  0.564945308
##  [991]  0.519065717  0.675051789  0.875207079  1.487409199  0.603087243
##  [996] -0.388458635  0.080560316  1.455197013 -0.173492257  1.591603373
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
##   0.65434426   0.19774253 
##  (0.06253168) (0.04421028)
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
## [1]  0.7327219  0.5783680 -0.1351552  0.6109525  0.4523793  0.2768072
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
## [1] -0.0249
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9037847
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
## t1*      4.5 0.03853854   0.9328272
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 5 6 7 8 9 
## 1 1 1 3 1 2 1
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
## [1] 0.0232
```

```r
se.boot
```

```
## [1] 0.9010181
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

