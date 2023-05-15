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
## 0 1 5 6 7 8 
## 1 1 2 2 2 2
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
## [1] 0.0208
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
## [1] 2.745503
```

```r
UL.boot
```

```
## [1] 6.296097
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
##    [1] 4.0 5.4 4.1 5.6 5.6 4.5 4.5 5.7 3.5 4.9 4.7 3.3 4.4 3.4 4.1 4.3 4.0 3.1
##   [19] 5.8 3.8 4.2 3.5 4.0 5.6 1.7 3.9 5.9 5.0 4.0 5.9 4.7 3.7 3.9 3.5 3.0 4.8
##   [37] 5.3 5.9 4.6 3.6 3.2 3.5 5.2 3.6 3.1 4.0 4.6 5.5 5.1 4.2 4.0 2.9 4.2 5.8
##   [55] 4.2 3.9 4.6 4.4 6.1 4.6 5.5 5.5 5.0 5.6 4.1 3.7 4.3 3.3 5.7 5.8 4.0 5.0
##   [73] 6.2 4.1 3.5 4.8 2.4 5.8 4.1 5.6 4.0 4.0 4.5 4.0 4.3 3.6 4.4 4.1 5.6 2.7
##   [91] 4.4 3.6 6.3 4.9 5.0 4.3 5.5 5.2 4.9 6.2 4.8 5.5 5.6 3.7 2.3 3.4 4.5 5.3
##  [109] 5.1 5.5 3.3 4.5 2.7 4.0 5.0 4.6 5.5 3.9 5.2 3.6 3.7 5.0 4.2 4.0 5.8 4.4
##  [127] 5.1 4.4 3.7 5.3 4.1 4.5 4.9 4.8 3.6 5.0 0.8 4.1 4.4 4.4 4.1 3.7 4.7 5.6
##  [145] 4.1 3.0 4.2 4.8 4.5 5.8 3.8 4.5 4.0 6.2 5.7 4.8 4.9 3.7 4.9 5.8 4.1 4.6
##  [163] 3.8 4.2 3.2 3.7 4.7 3.0 3.3 4.7 5.2 4.0 2.9 4.6 3.9 5.3 3.5 5.1 4.0 5.6
##  [181] 4.0 4.9 4.8 5.2 3.1 2.6 5.0 5.5 4.4 3.1 5.4 5.3 5.5 3.8 5.4 6.1 3.9 5.8
##  [199] 6.6 5.4 5.4 6.0 4.6 3.3 2.8 3.4 5.8 3.3 4.7 3.7 5.0 5.6 5.0 4.6 4.4 4.2
##  [217] 4.1 4.4 5.8 4.2 6.2 5.2 4.1 6.3 3.7 4.4 3.8 3.6 4.8 5.3 4.3 5.9 3.4 4.6
##  [235] 2.4 5.9 2.9 5.3 3.4 3.2 4.3 3.9 3.9 4.7 3.5 4.6 3.1 4.6 4.4 4.6 4.3 4.2
##  [253] 5.2 4.9 4.8 4.3 4.7 5.7 3.4 4.7 3.5 4.1 4.5 4.1 4.5 4.1 4.1 3.6 4.6 3.6
##  [271] 3.8 5.4 3.6 4.7 3.5 4.0 3.8 5.9 4.4 3.5 5.1 3.9 4.4 5.5 4.1 5.3 4.3 6.1
##  [289] 3.4 3.7 5.1 4.6 5.1 5.0 4.6 4.5 4.1 6.4 4.2 4.0 5.4 6.6 4.5 4.2 3.8 4.7
##  [307] 4.3 3.8 3.8 5.2 4.0 3.6 4.1 4.7 6.1 3.6 4.0 3.5 4.9 3.3 4.9 3.8 3.8 5.0
##  [325] 4.9 5.1 4.0 5.4 4.0 5.6 5.0 5.4 2.7 4.1 5.6 4.4 5.1 4.8 3.4 4.7 4.4 3.9
##  [343] 3.8 3.2 3.9 3.7 6.8 3.6 3.3 4.6 4.3 5.4 4.4 4.2 4.9 4.2 3.6 5.3 4.0 5.7
##  [361] 3.8 4.2 2.8 3.7 3.8 4.5 3.2 4.8 3.7 4.9 6.4 4.9 3.0 4.8 4.0 3.8 3.5 4.1
##  [379] 4.2 5.5 3.8 3.8 5.4 4.5 4.3 4.1 6.8 5.4 7.1 4.1 4.8 3.9 5.5 4.2 5.0 3.9
##  [397] 4.6 4.0 4.9 6.0 3.8 2.7 5.2 4.2 6.1 4.5 3.9 3.6 5.1 4.7 5.2 4.0 3.6 5.7
##  [415] 4.9 4.4 4.9 3.7 5.5 4.5 5.0 5.1 3.3 6.3 5.8 5.8 5.1 6.7 3.5 5.6 2.6 3.5
##  [433] 4.7 6.0 5.2 4.2 4.9 4.7 5.5 5.6 4.5 6.2 2.4 5.4 3.7 3.9 3.3 5.8 4.9 5.2
##  [451] 6.2 4.4 3.3 5.0 4.4 4.6 4.0 4.2 2.9 3.7 5.0 6.1 4.9 5.5 4.2 4.2 6.2 4.2
##  [469] 4.9 4.5 4.7 5.9 3.8 4.7 4.4 5.4 5.1 4.9 4.3 4.8 3.8 4.9 3.6 4.2 4.1 3.9
##  [487] 4.1 3.2 3.9 5.8 2.5 3.1 4.4 5.6 4.8 3.7 4.7 3.5 5.3 4.6 4.8 4.0 4.8 3.3
##  [505] 4.1 5.0 6.6 5.3 3.5 3.5 5.6 7.1 4.6 3.3 4.4 4.1 4.4 3.7 4.7 6.2 4.1 4.6
##  [523] 3.3 3.5 4.4 3.0 4.3 4.2 4.2 5.9 4.2 6.1 5.3 3.5 5.1 5.6 4.3 4.2 4.9 4.9
##  [541] 5.8 3.5 5.9 3.5 5.7 2.6 4.6 3.5 4.2 4.7 4.5 5.7 4.8 3.8 4.4 2.8 4.5 3.1
##  [559] 4.8 3.8 4.2 4.2 4.0 4.2 5.3 4.2 4.5 3.9 4.0 3.7 3.1 5.1 5.8 3.3 4.2 5.6
##  [577] 4.5 4.3 5.3 4.2 5.4 3.1 3.6 4.7 4.1 6.1 5.0 6.6 5.3 4.8 5.6 4.9 4.1 5.6
##  [595] 4.0 3.4 5.0 4.8 4.8 3.1 4.3 3.0 3.9 6.6 3.7 4.9 5.4 4.0 4.9 4.1 4.1 4.1
##  [613] 4.0 5.4 4.5 4.2 3.2 5.1 4.1 2.7 4.3 4.2 5.6 4.5 6.8 3.5 4.9 3.6 3.5 4.7
##  [631] 4.3 4.8 3.4 5.0 4.8 5.7 4.0 5.5 4.0 4.9 4.1 3.4 4.7 4.4 5.2 4.7 3.7 4.3
##  [649] 5.0 4.4 4.6 5.2 5.2 6.5 5.8 5.5 3.3 5.2 3.6 5.2 4.8 3.6 5.2 4.7 2.7 5.5
##  [667] 3.8 4.2 5.8 4.5 4.1 3.8 2.3 4.7 2.9 5.1 4.0 4.2 4.6 4.2 4.8 5.0 5.2 3.1
##  [685] 3.7 4.4 4.7 4.1 4.0 4.5 4.1 3.9 3.0 5.0 6.6 4.4 4.6 4.2 5.9 5.0 5.3 2.7
##  [703] 6.2 4.1 4.3 4.5 5.2 2.6 3.0 4.5 4.8 3.1 5.0 4.7 4.8 3.1 4.2 2.2 3.2 4.8
##  [721] 3.6 3.4 3.7 5.3 4.2 5.4 4.3 3.5 4.5 4.2 4.4 6.4 5.5 2.8 5.8 6.6 2.5 5.1
##  [739] 3.6 5.1 5.4 3.7 5.0 4.8 5.3 4.1 3.0 4.7 5.6 2.7 6.1 6.3 3.8 6.5 4.0 4.1
##  [757] 4.9 4.2 3.9 3.7 3.9 5.8 3.8 4.3 2.3 5.9 4.6 5.8 4.1 2.6 3.7 3.0 4.4 4.4
##  [775] 4.4 5.5 4.7 3.5 3.5 4.2 4.8 5.3 5.8 3.4 6.3 3.3 4.3 3.9 2.5 4.4 4.8 4.5
##  [793] 5.5 4.8 5.0 4.3 5.0 4.3 4.3 4.4 4.0 6.2 3.9 5.2 5.1 3.5 2.0 4.1 5.8 5.7
##  [811] 5.3 5.1 5.3 5.0 4.3 3.9 4.4 4.8 3.0 4.0 4.0 4.3 5.2 4.3 4.9 3.6 4.3 5.5
##  [829] 2.4 3.9 2.8 3.5 4.6 5.6 3.6 3.4 4.0 5.7 3.3 5.2 4.3 3.5 4.8 4.2 3.9 5.5
##  [847] 5.5 4.2 3.5 5.3 5.6 4.5 4.1 5.2 3.5 5.1 6.3 3.5 3.6 4.0 3.2 5.4 3.9 4.5
##  [865] 4.8 5.4 5.2 3.5 5.3 4.6 5.2 5.3 4.0 5.7 5.1 6.8 5.6 5.3 3.8 3.8 3.9 4.3
##  [883] 3.6 4.0 4.4 4.6 3.8 5.4 3.9 4.8 4.4 5.0 5.5 3.7 4.6 3.5 3.7 4.8 4.2 3.5
##  [901] 5.9 4.9 6.3 5.3 3.9 2.8 2.7 2.8 4.8 6.3 4.3 3.7 4.8 5.7 4.8 5.2 5.2 4.5
##  [919] 4.6 5.3 2.5 5.0 3.7 5.3 5.6 4.7 4.9 4.9 2.7 4.9 4.3 3.6 3.5 5.0 2.7 4.2
##  [937] 4.6 3.4 3.0 4.8 3.9 4.9 5.0 3.7 2.8 6.6 2.5 4.0 4.4 4.3 4.0 3.6 5.2 4.8
##  [955] 4.4 4.7 4.7 4.2 4.7 5.7 6.6 2.7 5.5 3.9 2.4 4.4 3.0 3.3 3.9 4.8 4.3 6.1
##  [973] 3.6 4.3 4.4 4.1 5.8 3.2 5.7 4.8 5.6 4.0 2.9 3.5 3.6 5.2 3.6 5.8 3.8 3.0
##  [991] 4.0 3.4 5.1 6.1 4.1 5.0 3.1 5.5 5.7 3.7
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
##    [1] 4.5 5.9 6.2 5.3 4.6 5.4 3.9 4.6 3.9 2.5 5.2 4.4 5.1 5.8 3.9 3.7 5.0 3.4
##   [19] 4.8 2.3 6.1 3.4 5.8 4.9 5.9 3.3 4.7 6.1 3.9 5.2 3.8 4.6 5.2 3.9 4.0 5.7
##   [37] 6.4 5.1 5.9 6.0 3.9 3.6 4.2 4.8 4.8 4.4 5.4 5.2 4.6 4.9 5.0 2.9 5.6 4.6
##   [55] 4.7 5.3 5.4 4.0 5.7 6.8 5.8 6.0 6.0 4.7 4.4 4.6 6.5 3.6 4.1 4.9 4.7 4.7
##   [73] 4.0 4.2 5.5 5.2 5.0 5.3 2.1 4.7 6.4 5.1 4.4 4.5 6.0 3.8 4.2 2.2 5.7 3.8
##   [91] 4.4 4.2 3.6 5.1 6.0 5.3 4.9 5.1 4.2 5.2 6.1 4.8 3.1 3.1 4.8 6.2 4.4 5.1
##  [109] 3.9 4.9 4.9 4.7 3.7 5.2 5.8 4.3 4.6 3.9 4.6 3.7 3.4 3.2 3.5 4.1 6.3 3.6
##  [127] 4.2 4.6 3.5 2.9 4.2 3.4 5.4 5.3 2.2 5.0 5.4 4.9 5.3 5.5 3.9 4.4 4.4 3.1
##  [145] 3.8 5.2 5.3 4.5 6.6 6.3 5.6 5.2 3.1 5.2 3.9 4.7 4.7 5.4 4.9 2.9 5.7 4.5
##  [163] 4.7 4.5 4.6 3.6 4.5 5.0 4.8 4.3 5.3 5.0 4.1 4.2 4.7 4.6 3.5 5.3 5.3 4.7
##  [181] 5.0 5.6 3.6 3.6 4.0 5.9 5.3 4.1 5.4 5.4 5.2 2.9 6.2 4.9 4.9 4.4 4.8 3.2
##  [199] 4.2 5.3 4.4 4.1 4.3 6.4 4.0 2.1 5.0 3.2 4.3 3.5 5.0 4.0 4.3 6.4 3.0 4.6
##  [217] 3.6 4.4 3.8 3.9 5.1 5.4 4.6 4.2 5.3 3.8 4.0 4.8 5.3 4.0 4.4 3.6 4.0 5.1
##  [235] 4.7 5.0 3.9 5.1 4.0 4.4 4.2 4.2 7.1 4.5 3.1 3.8 6.1 6.2 4.5 4.9 5.8 4.6
##  [253] 5.2 3.5 4.1 4.8 4.0 3.9 3.7 5.1 5.2 5.0 3.9 4.5 4.5 6.3 4.8 1.9 5.2 6.2
##  [271] 5.5 5.8 4.0 6.5 3.5 5.0 3.7 3.9 4.1 4.4 3.6 4.6 5.1 3.9 4.7 5.8 5.4 3.0
##  [289] 6.1 4.8 5.4 4.7 4.5 4.0 4.5 4.3 4.0 5.3 4.1 5.1 3.2 4.0 3.9 5.0 4.3 4.5
##  [307] 5.8 5.4 4.5 3.2 6.1 4.6 5.1 5.5 5.9 4.5 4.8 4.0 3.4 5.0 3.7 3.4 5.3 3.2
##  [325] 3.0 6.3 5.6 3.0 4.6 5.1 4.4 5.3 4.3 4.0 5.2 4.5 3.2 5.5 5.4 3.5 4.3 6.6
##  [343] 5.7 3.8 3.4 4.5 4.5 4.2 5.9 4.3 4.6 5.4 4.7 4.2 4.5 5.6 3.1 4.9 5.0 4.8
##  [361] 6.0 3.6 4.3 3.4 5.2 4.4 4.9 3.1 3.9 4.3 5.5 4.2 3.6 5.8 3.8 5.2 4.8 4.7
##  [379] 5.8 4.6 5.0 4.5 5.2 4.6 5.8 3.5 4.1 5.1 4.2 5.0 5.0 6.1 5.1 4.7 5.4 4.8
##  [397] 5.3 2.9 4.7 6.3 4.3 5.0 5.0 3.3 5.0 4.7 4.3 3.9 5.1 4.4 4.9 4.3 4.1 5.4
##  [415] 4.1 4.1 4.4 6.1 3.8 3.8 5.3 4.4 3.4 2.4 5.1 3.3 4.0 4.8 3.6 4.7 3.7 4.0
##  [433] 2.4 4.2 4.5 4.5 6.0 3.2 4.7 4.7 5.0 5.5 5.1 3.0 4.9 5.8 4.3 5.2 4.6 4.5
##  [451] 3.5 4.5 4.5 3.9 4.6 4.5 3.5 4.5 4.5 5.0 4.7 5.3 3.9 4.9 6.0 6.0 5.4 6.1
##  [469] 4.1 4.5 3.6 5.1 4.7 5.9 4.4 4.2 2.6 5.5 2.5 5.6 4.2 3.0 6.4 3.8 4.9 4.2
##  [487] 4.8 3.1 5.6 4.5 3.3 3.9 4.1 3.1 5.5 5.4 5.0 2.9 4.2 3.9 3.3 4.6 3.6 3.6
##  [505] 3.8 3.9 5.9 4.4 5.3 4.4 5.3 5.2 5.2 5.6 3.0 4.6 3.1 5.3 4.0 5.7 4.4 3.4
##  [523] 4.5 6.3 5.0 5.6 6.5 4.6 5.0 4.5 6.1 4.6 5.4 4.1 3.8 5.1 5.0 4.4 5.6 4.0
##  [541] 5.8 4.7 4.6 4.5 3.7 4.3 5.1 3.1 5.2 3.6 4.8 3.7 3.4 4.1 6.5 3.8 4.7 5.1
##  [559] 5.0 4.0 4.9 5.8 5.6 4.4 3.8 5.2 5.7 4.3 5.4 4.1 5.0 3.6 4.8 3.8 3.6 3.8
##  [577] 3.7 6.0 5.3 5.0 3.3 4.0 5.0 4.1 6.2 3.9 5.2 3.9 5.8 6.3 4.9 2.8 4.6 4.4
##  [595] 3.1 4.8 6.8 3.6 5.1 4.9 4.3 6.6 6.5 4.3 4.2 3.7 4.8 4.4 5.9 4.2 3.0 4.9
##  [613] 4.7 7.2 5.5 5.3 3.0 3.5 3.3 5.7 5.8 3.8 3.9 4.5 5.0 5.1 4.4 4.1 3.8 5.1
##  [631] 4.2 5.2 2.9 3.3 4.0 5.1 3.0 5.3 4.1 4.6 5.6 5.1 4.2 3.7 5.2 3.2 5.9 3.7
##  [649] 3.2 4.7 6.8 5.8 4.6 4.4 3.4 3.1 5.4 5.0 4.1 4.6 5.6 6.2 6.0 4.4 5.0 3.3
##  [667] 4.8 4.4 4.5 3.4 4.3 7.3 5.8 3.6 5.3 4.2 4.0 3.8 4.7 4.8 3.0 4.3 3.5 5.3
##  [685] 3.3 5.5 4.0 4.3 4.8 5.2 3.5 4.7 4.6 4.8 6.9 5.4 3.7 4.5 2.9 3.2 4.2 4.3
##  [703] 6.5 5.1 3.7 5.1 3.1 4.9 4.4 3.1 3.5 3.6 4.1 3.9 5.0 4.2 3.7 4.5 5.1 4.0
##  [721] 5.7 6.6 4.9 4.6 5.0 2.9 4.5 4.4 4.8 3.9 4.7 4.8 4.2 3.0 6.3 4.3 3.2 4.9
##  [739] 5.1 4.3 4.0 5.0 4.6 4.3 5.7 4.1 4.2 4.4 3.5 3.1 4.8 5.0 5.1 2.8 4.2 4.5
##  [757] 5.4 4.5 3.8 3.1 4.9 4.5 7.2 3.3 5.1 4.6 2.7 4.0 5.7 4.5 5.1 6.5 2.2 5.0
##  [775] 4.9 5.1 5.6 5.2 2.9 5.4 4.8 4.0 5.0 4.2 2.6 4.5 5.4 3.8 5.4 4.1 4.4 4.3
##  [793] 3.4 5.7 3.1 3.9 4.9 5.2 6.0 3.3 3.2 3.9 4.7 6.3 5.3 4.2 4.5 5.1 4.9 4.0
##  [811] 5.4 4.5 5.1 4.0 5.4 4.7 4.1 4.1 3.6 5.2 5.5 5.3 4.8 6.0 5.7 5.7 5.6 5.4
##  [829] 4.8 4.8 3.3 4.2 7.3 2.4 4.4 5.2 3.8 4.0 5.3 6.4 4.1 5.6 4.0 3.8 3.9 4.0
##  [847] 4.8 4.0 5.1 4.4 4.3 6.3 3.7 4.2 5.0 4.0 4.7 4.8 3.5 5.0 5.3 5.0 5.5 5.2
##  [865] 6.0 3.6 3.2 4.6 5.6 4.4 4.5 5.1 4.4 3.3 4.0 4.2 6.0 4.8 3.7 4.3 2.5 3.0
##  [883] 3.2 5.0 5.0 5.0 2.6 4.6 5.5 4.2 4.2 4.3 4.7 5.2 2.2 3.2 5.1 5.1 4.4 3.6
##  [901] 4.2 3.8 5.1 4.1 4.5 4.5 3.9 4.1 4.0 3.2 5.8 5.0 4.8 4.3 4.3 3.0 5.2 3.2
##  [919] 5.6 4.6 4.3 5.2 4.8 4.3 5.4 5.5 5.9 4.9 6.5 5.0 5.8 3.7 4.8 4.0 4.5 4.3
##  [937] 5.7 3.1 2.8 5.0 5.6 5.3 4.7 5.5 5.0 5.9 4.9 4.4 4.5 4.7 3.4 3.8 3.8 3.4
##  [955] 5.0 4.9 5.3 3.8 4.1 3.4 4.2 5.1 4.1 4.8 4.5 5.3 3.9 5.5 3.9 4.5 4.0 4.7
##  [973] 2.9 4.6 5.6 3.6 3.9 5.4 3.6 6.0 5.6 3.8 4.8 4.7 4.0 4.7 4.2 5.1 5.0 4.5
##  [991] 5.1 5.0 5.6 4.3 3.7 4.2 4.4 4.1 5.1 3.6
## 
## $func.thetastar
## [1] 0.0766
## 
## $jack.boot.val
##  [1]  0.591643454  0.433593750  0.397707736  0.332930514  0.128070175
##  [6] -0.009756098 -0.068985507 -0.240934066 -0.312990937 -0.463934426
## 
## $jack.boot.se
## [1] 1.011838
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
##    [1] 4.5 4.0 4.3 3.7 3.6 4.2 5.0 4.2 5.1 4.3 3.2 5.1 2.3 4.1 4.4 6.4 3.6 3.9
##   [19] 5.7 4.8 3.4 5.0 4.3 3.4 4.3 4.6 2.6 4.6 2.4 3.9 4.3 4.7 3.6 5.2 2.8 4.3
##   [37] 2.7 3.4 4.5 4.3 3.7 4.4 3.9 3.4 5.3 4.5 3.5 5.1 6.5 4.7 6.1 3.1 5.4 4.7
##   [55] 5.5 4.7 5.1 2.8 5.2 4.5 4.6 4.4 5.4 4.3 4.2 4.8 3.2 4.1 3.6 3.5 4.0 5.2
##   [73] 2.6 5.1 4.2 6.4 3.7 4.4 3.4 4.5 4.7 5.8 4.1 4.6 4.2 4.2 2.6 3.4 4.2 5.2
##   [91] 3.4 5.2 4.5 5.2 5.0 2.9 5.0 4.1 4.7 6.2 3.6 3.9 4.6 4.6 4.1 5.2 2.9 4.6
##  [109] 4.4 5.0 3.3 3.8 3.5 5.3 3.9 3.5 4.3 3.9 3.6 5.6 4.7 4.2 4.4 4.8 5.6 5.2
##  [127] 4.3 6.4 3.1 4.6 5.4 3.1 4.9 4.3 4.2 5.0 4.2 5.4 6.2 4.8 5.8 4.4 4.0 4.1
##  [145] 6.3 3.5 6.7 4.7 4.7 3.9 3.7 4.6 3.9 6.1 5.0 4.5 3.9 5.8 1.9 5.9 4.7 5.5
##  [163] 5.7 3.9 5.1 4.5 5.4 5.0 3.3 2.9 3.3 4.7 5.4 5.4 5.4 4.0 4.4 5.5 3.3 3.4
##  [181] 4.7 4.2 5.0 4.1 4.3 3.7 4.4 5.7 4.8 2.6 4.4 4.7 4.3 5.4 4.8 4.5 3.8 4.8
##  [199] 4.9 4.3 4.4 3.4 6.0 3.4 5.5 6.8 2.2 2.8 4.1 5.1 6.5 5.1 4.3 5.1 3.6 5.1
##  [217] 3.4 6.1 6.3 4.1 3.8 4.7 5.1 3.7 5.8 4.4 4.8 4.3 4.6 2.3 5.3 3.7 4.7 4.2
##  [235] 3.1 3.0 3.6 5.5 3.8 4.5 5.3 4.2 4.8 5.3 5.6 5.1 2.8 4.2 6.3 4.8 5.0 3.9
##  [253] 4.7 4.5 4.8 3.0 4.6 5.4 4.1 5.8 3.9 3.0 3.3 5.0 3.9 3.0 3.6 4.9 4.5 5.0
##  [271] 4.3 5.1 3.7 3.7 5.0 3.8 5.2 6.3 4.9 3.8 5.5 4.2 3.8 4.6 3.9 3.3 7.9 3.7
##  [289] 5.2 3.5 5.9 4.9 4.6 6.0 5.6 4.4 4.2 3.7 3.7 4.8 4.1 4.6 4.8 4.7 3.4 3.8
##  [307] 4.0 4.4 3.9 4.2 4.8 4.4 5.3 3.3 3.0 4.3 3.8 5.0 5.6 5.6 5.3 5.2 6.6 4.6
##  [325] 2.9 4.5 4.8 5.5 6.1 4.7 3.5 5.7 2.7 2.6 4.6 4.8 3.1 4.8 3.9 3.6 4.8 5.3
##  [343] 4.9 4.7 6.3 3.9 4.6 4.0 5.9 5.2 4.2 5.9 4.9 2.7 3.2 3.8 4.7 6.2 3.6 4.2
##  [361] 4.1 4.1 4.7 3.0 4.5 4.0 5.0 4.2 6.2 4.7 4.9 5.2 5.1 5.9 4.4 3.2 6.1 5.4
##  [379] 4.1 3.5 5.3 4.1 4.8 4.4 6.4 5.3 5.3 5.0 4.1 4.4 4.4 5.0 3.5 5.3 4.2 5.7
##  [397] 3.4 4.6 4.7 4.6 3.8 3.8 4.3 3.6 4.3 3.9 6.3 3.4 5.1 3.9 4.4 4.9 4.2 5.6
##  [415] 5.1 4.9 5.6 4.8 4.7 4.6 6.2 5.0 4.1 2.7 5.2 4.0 4.5 4.8 6.2 4.6 5.0 5.9
##  [433] 3.5 2.8 5.0 3.3 5.9 4.1 4.0 4.0 3.1 3.6 4.0 4.8 5.7 5.0 4.1 3.9 2.8 5.2
##  [451] 5.4 3.1 5.3 3.1 4.5 4.8 2.4 3.2 3.9 4.1 2.1 3.4 3.0 5.0 3.8 5.4 5.1 3.9
##  [469] 5.2 3.9 6.2 4.1 4.1 5.6 6.3 4.8 4.2 3.9 5.1 4.1 4.7 3.3 5.6 4.7 3.6 6.2
##  [487] 3.8 4.8 3.8 5.9 4.1 5.1 6.0 4.1 5.8 4.3 4.5 4.8 4.1 4.5 5.3 3.9 5.1 3.3
##  [505] 3.2 4.6 4.0 4.9 5.6 2.9 3.4 5.2 4.5 4.6 5.9 5.9 7.2 3.7 4.1 5.7 3.8 4.1
##  [523] 6.1 4.2 3.7 3.5 5.2 5.1 3.4 4.5 6.3 3.1 4.7 5.7 4.9 4.8 4.2 6.1 3.7 4.9
##  [541] 4.1 6.0 4.0 4.5 5.3 5.1 4.7 5.1 5.3 5.1 5.2 4.7 4.4 5.3 4.2 4.3 6.7 4.5
##  [559] 2.9 4.0 4.2 5.4 4.6 4.2 4.1 4.1 5.7 4.7 4.4 5.2 4.9 4.5 3.7 4.8 4.4 4.6
##  [577] 4.2 3.9 5.7 4.6 4.3 5.5 3.7 4.9 3.5 4.6 4.9 5.0 3.6 3.2 3.3 4.8 3.6 3.4
##  [595] 5.3 4.8 4.3 4.7 4.5 5.7 4.8 5.3 4.2 4.9 5.3 4.4 4.6 3.6 4.9 3.9 4.1 4.9
##  [613] 4.4 4.6 5.5 4.8 4.8 3.3 6.3 3.7 3.6 5.5 5.0 5.7 4.2 4.7 5.0 5.4 4.2 4.2
##  [631] 5.4 4.0 5.0 4.6 3.8 4.5 4.8 5.2 4.6 5.7 4.0 5.6 4.8 2.7 4.6 3.8 5.1 4.8
##  [649] 3.9 4.8 5.8 4.0 2.8 3.1 5.4 3.7 3.4 6.4 4.0 4.8 4.5 4.2 4.6 5.4 4.0 5.5
##  [667] 5.0 5.2 3.8 5.2 5.4 4.8 4.1 3.6 5.4 2.2 3.7 5.3 4.6 4.4 5.1 3.6 5.0 3.7
##  [685] 4.1 3.9 5.4 4.0 5.1 3.3 5.4 3.0 4.5 2.8 3.8 4.5 4.3 4.7 4.5 5.7 5.8 3.7
##  [703] 5.2 4.4 4.6 3.7 3.6 7.0 4.0 5.2 4.3 3.9 5.2 2.4 4.9 6.1 4.2 5.1 4.9 5.0
##  [721] 3.3 4.8 4.9 3.2 4.6 3.0 4.4 4.6 4.7 5.1 5.6 5.5 3.7 6.1 4.6 5.5 5.3 4.8
##  [739] 2.1 5.4 4.0 4.4 3.7 3.8 4.5 5.1 2.5 4.4 6.0 3.9 4.3 3.3 4.8 4.6 3.6 5.5
##  [757] 4.3 5.2 5.9 5.5 5.3 5.1 3.8 6.3 4.6 4.2 3.7 4.3 5.2 4.4 4.2 4.1 4.1 4.7
##  [775] 5.8 4.9 4.9 4.0 7.0 4.5 3.7 5.1 5.3 4.8 3.5 4.9 3.3 3.8 5.7 4.2 4.8 6.1
##  [793] 4.3 3.6 2.9 2.8 3.4 5.2 4.3 5.0 5.5 3.3 5.6 4.2 5.5 3.9 3.3 4.2 4.9 4.4
##  [811] 4.2 2.4 3.4 4.6 4.3 5.1 4.3 4.1 3.5 3.9 4.4 3.5 2.4 5.1 5.8 3.9 3.6 5.8
##  [829] 6.1 5.5 5.8 4.9 5.0 3.4 5.3 3.3 4.9 3.8 4.3 5.1 5.1 6.0 3.2 5.5 3.7 4.1
##  [847] 3.2 4.6 7.0 3.8 5.1 4.8 2.2 5.5 3.3 4.5 5.5 3.2 3.0 4.1 4.9 5.8 5.3 3.4
##  [865] 6.2 5.1 5.3 4.9 6.0 5.5 4.8 5.4 4.6 5.8 4.1 5.5 5.4 5.2 4.3 3.5 5.6 4.2
##  [883] 4.2 6.6 4.1 4.1 4.6 4.1 4.4 6.3 4.2 4.3 3.3 5.4 3.3 4.3 6.9 4.8 6.0 3.5
##  [901] 5.1 5.2 4.9 4.5 3.9 4.4 4.4 4.7 4.6 4.5 3.0 4.5 4.8 3.5 3.5 3.8 4.5 4.2
##  [919] 5.7 3.7 4.8 5.4 3.3 4.1 4.0 4.9 4.8 3.7 5.9 4.0 5.0 4.2 5.6 7.0 4.5 3.0
##  [937] 3.2 3.9 5.5 4.9 3.9 4.6 5.2 3.9 3.4 4.8 3.6 4.6 4.2 3.4 3.6 4.0 4.1 5.4
##  [955] 4.2 5.4 4.2 4.3 4.4 2.6 3.7 5.3 5.3 5.0 4.8 4.5 4.6 6.2 5.9 3.3 4.2 4.6
##  [973] 4.8 4.1 5.8 5.1 4.1 4.9 4.8 4.3 5.8 5.3 5.2 4.7 4.6 4.2 5.4 5.0 6.1 3.3
##  [991] 5.6 4.5 4.1 6.5 4.6 4.2 3.4 4.7 3.7 3.7
## 
## $func.thetastar
##   72% 
## 5.028 
## 
## $jack.boot.val
##  [1] 5.428 5.400 5.372 5.300 5.100 4.944 4.900 4.900 4.600 4.500
## 
## $jack.boot.se
## [1] 0.9463041
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
## [1] 0.9424666
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
##   2.3398964   4.9417620 
##  (0.9810192) (2.3100493)
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
## [1] 1.34841791 0.21275984 0.70906231 0.35717086 1.19126321 0.03984109
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
##    [1] -1.902988061  0.146967901  0.556780871  0.832724558  0.671399317
##    [6]  0.893461658  0.971439950  1.134878208  1.753347127  0.237361913
##   [11]  1.254646033 -0.262672298  0.167827755  0.815672213  0.473051760
##   [16]  0.671321246  1.219753576  1.493180473  0.830718261  0.840744629
##   [21]  1.064750416 -0.247986176  0.869173211  0.388962249  0.713186009
##   [26]  1.079891671  1.089764783  0.754190387  0.297366602  1.123321073
##   [31]  0.504676932  0.045275076  1.041340949  0.815338570  0.753475156
##   [36] -0.402189009  1.040446377  0.573456982  1.017907991 -0.884315580
##   [41]  0.779583445  1.176275012 -0.030630891 -0.269168472  0.274290979
##   [46]  1.012489111  0.583981751 -0.047001859 -0.717798381  0.090560293
##   [51]  0.470523572  0.218578627  1.001664082  1.331634095  0.633987679
##   [56]  1.926796715  0.353835693  0.419016246  0.535480324  1.098712675
##   [61]  0.696546048  0.389315309  1.354828328  1.234648292  0.194994335
##   [66]  0.750160007  0.032830923  0.864943072  0.410381385  1.437425023
##   [71]  0.640343997  0.514829654  1.117690762  1.154526493  0.845696927
##   [76]  0.494826065 -0.928877097 -0.555481616  0.612156725 -0.001132829
##   [81]  0.494048442  0.476266454  0.686347747  1.067562601 -0.243701187
##   [86] -0.562185741  1.108823082  1.142950886 -0.849461872 -1.137867497
##   [91] -0.025864938  0.558472602 -0.356151492  0.920947626 -0.171739947
##   [96] -0.663109456  0.883011205  1.080033612 -0.089571177  0.936388175
##  [101]  0.952064492  0.244162563  1.703550832  0.624730678  1.576503428
##  [106] -0.400818819  0.067524169  0.093927055  0.165230865  0.701068183
##  [111]  1.468920848  0.147245703  0.827635910  1.052654560 -0.277412587
##  [116]  0.691643002  1.025724732  1.072429711  2.018597505  1.141850173
##  [121]  1.034097744  0.179583621  0.609163429  0.042832663  1.601487917
##  [126] -0.474602518  1.833098082 -0.173468546 -0.278019446  0.626586058
##  [131]  1.129675090  0.965636474  0.533307257  0.865652454  1.468965184
##  [136]  0.307682448  0.713533154 -0.518585595 -1.436605563  0.552076463
##  [141]  0.885191804 -0.406848359 -1.406798135 -0.899077408  0.463970292
##  [146]  0.489318337  0.152595348 -0.542627499  0.490577310  0.767948897
##  [151]  0.538991021  0.883148941  1.173872346 -0.427282336  0.724270788
##  [156] -0.244676372  1.477971639  0.515740625  0.517015459  0.629304536
##  [161]  1.036549019 -0.307381952  0.744924088 -0.057220210  0.552848529
##  [166] -0.904179799  0.987277616  0.596584153 -0.085085037  1.260142445
##  [171]  2.013494923 -0.107056612 -0.227097158  0.479560383  0.182272898
##  [176] -0.431548766  1.751657293  0.453261577  0.536325996 -0.131153892
##  [181]  0.085356395  0.496498525  1.547327919  0.850925559  0.272031892
##  [186] -0.141884590  0.049501603  0.196901992  0.563997167  0.118656106
##  [191] -0.116859522  1.362507980  0.575480511 -0.431345729 -0.494354542
##  [196]  0.954721554  0.662293617  0.905534296  1.044021899  1.308938349
##  [201]  0.138217889  0.034092915  0.544710680  1.097956949  0.513484958
##  [206]  0.037465769  0.718889941 -0.541164029  0.827451636  1.141850173
##  [211]  0.126848929  0.479367167  0.620930289  0.256027119  0.423726725
##  [216] -0.425172164  0.274639476 -0.138405040  0.920451747  1.488658151
##  [221]  1.429120308  0.161725469  0.821018833 -0.579186757  1.059212936
##  [226]  0.579331362  0.574586890  0.682904368 -0.009793724  1.159530975
##  [231]  1.754084171  0.782788985  0.255389542  0.121909777  0.076786485
##  [236] -0.965316901 -0.059407343  1.388516288  0.269792816  0.870198277
##  [241]  0.468381397  0.825767507  0.800144906 -1.341263276  0.791644735
##  [246] -0.025235607  0.643722318  1.243404777 -0.036039741  1.027825402
##  [251]  0.774092196  0.459960097  0.125606069 -0.041240293  0.267701323
##  [256]  1.030159204  0.683698551  0.031119245  1.774976829  0.259548156
##  [261]  0.736992382  0.255736587  1.141999306  0.590665565 -0.364465841
##  [266]  1.155043068 -0.278833787  1.070792243 -0.820816356  1.401504806
##  [271] -0.538080558  0.631684817  0.255128852  0.929421143  0.708759414
##  [276]  0.159433024  0.947000373  1.406095562  0.173151164  0.288500590
##  [281]  1.044876884  0.754383872  1.658170082 -0.662200535  1.450362839
##  [286]  0.213319628 -0.201078772  0.713281902  0.717590383  0.938686634
##  [291]  1.312435482  1.019398467  0.319923452  1.352309734  0.573471258
##  [296]  1.487335325  0.733325231 -0.445751254 -0.107783374  1.107785025
##  [301]  1.232351705  1.052009495  0.365433354  0.279428559  1.079209720
##  [306]  1.123968743  1.543968667 -0.382791274  1.362013630  0.642273707
##  [311]  0.982732865  0.288174519  0.740599728  0.868636836  0.011856506
##  [316]  1.173341240  0.139170436  0.601198557  1.077352813  0.617624963
##  [321]  0.712789745  1.224989885  0.113052504  0.400977452  1.676006425
##  [326]  1.191429219  1.434475704  0.143990520 -0.028859351  0.763991527
##  [331]  1.863916333  0.752528241  0.110092268  0.609262062  1.181360689
##  [336]  0.374166429  1.623281877  0.963200932  0.458253247  0.935581575
##  [341]  0.538656421  0.613642868  0.242246440  0.906009668  0.842341021
##  [346]  0.736658613  0.912713717  0.609666181  0.801480708  0.859214456
##  [351] -0.143690748  0.742546557  1.079512655 -0.292602895  0.898330862
##  [356] -0.019678315 -0.434955253  0.189339195  0.838742658 -0.109578073
##  [361]  0.650446529  0.732580391  0.045208118  1.124279205  1.343541185
##  [366]  0.013873947  1.454482292  0.049647116  0.934857502  1.746402433
##  [371] -1.305704699  1.359425067 -0.667447322  0.922061442  0.710460199
##  [376]  1.354977322  0.507591620 -0.297403011  1.642557558 -0.210959687
##  [381]  0.922090168  0.906387851  0.453335482  0.416857642  0.447406430
##  [386]  0.726761290  0.068268746  1.006116006  0.966608077  0.213924264
##  [391]  0.212661021  1.198203205  0.345473285  0.061042737  0.911198109
##  [396]  0.222423318 -0.257696490 -0.826618890  1.620725864  0.064606874
##  [401]  0.474056426  0.765793249  1.261408364 -0.304594361  0.014812097
##  [406]  0.494559927  1.460773304  0.417599622  0.730909824  0.334451595
##  [411]  0.947000373  1.120650056  0.872924743  0.175644916 -0.072859421
##  [416]  0.920333849  1.424928630  0.526328464  1.249563907  0.724344629
##  [421]  0.903757064  0.994352371  0.236018196 -0.102999602  0.789145179
##  [426]  1.269724089  1.484499784  0.651856329  0.675855456  0.798400792
##  [431]  0.498567003  1.264123564  0.426326419  1.060659558  1.707049609
##  [436]  0.672085646  1.398667904  0.612626136 -1.099825699  0.143087119
##  [441]  0.075888165  0.947992596  0.334568222  1.197150132  1.367089700
##  [446]  1.446574666  1.123080931  0.611028348  0.633237877  0.123921120
##  [451]  0.006305911  0.138028069  0.075865329  0.241906805  0.497029159
##  [456]  0.354613763  1.052344955  2.535331484  0.130499261  1.364795382
##  [461] -0.121646655  0.777485471  1.712056171  1.261050526 -0.051856309
##  [466]  0.800448940 -0.433957033  1.028795720  0.588019689 -0.201861452
##  [471]  0.636485083  0.940807926  0.833052034  1.672970220  0.859004844
##  [476]  1.000723118  0.556744719  0.925675066  0.839857382  1.262361421
##  [481]  0.871273720  0.819690100 -0.106532575  1.082390798 -0.073560647
##  [486]  0.455805430  0.722670637  0.871126116  0.952005734  1.411942147
##  [491]  0.400496290  0.693537396  0.150130117  0.796627641  0.520753400
##  [496]  0.271301201  1.338558456  1.626569916  0.777485471  1.216404334
##  [501] -0.161953599  1.327636159  0.416027987 -1.081053510  0.903151034
##  [506]  1.356524614 -0.068220154  1.268775721  1.030386972  1.452061235
##  [511] -0.133637946  0.326158761 -0.062201984  0.332381085 -0.168961750
##  [516]  0.520329266  0.942923254  0.930439545  0.346063653  0.878926964
##  [521]  0.191299575  0.504482436  0.229662133  0.968770393  0.202550354
##  [526]  0.415027813  0.503245585  0.949630700  0.924171652 -0.971708693
##  [531]  0.432187342  0.844436962  0.634526799 -0.051141105  0.449871700
##  [536]  0.758580209  0.482352048  1.199068521  0.592196518 -0.209148818
##  [541]  1.388627324  1.315106282 -0.140196615 -0.087963377 -0.747930870
##  [546]  0.152458217  0.598222783 -0.107104403  0.436444258 -0.108937667
##  [551] -0.291221912  0.491217506  1.337308229  0.205465556  0.372452847
##  [556]  0.831849172 -0.409213295  0.813458872  0.266639896  1.041358640
##  [561]  0.761248906  0.151902541 -0.051856309  1.421915232  0.684745924
##  [566]  0.028946534  1.673661873  0.514722772  1.017875736  0.261248602
##  [571]  1.036358380  0.761799051  0.935449764  0.944277158 -0.607442684
##  [576]  0.313892636  0.851767983  1.260386627  0.629036200 -0.336660431
##  [581]  1.186903815  0.267286070  0.494812225  0.943557308  0.478335979
##  [586]  1.347901086  0.748324632 -0.310181389  1.224751265  1.400485910
##  [591]  0.492150590 -0.337287022  0.860824963  1.099979233 -0.201904458
##  [596]  1.007733158  0.688465837  1.235262382  0.671958805  0.018763649
##  [601]  0.701347425  1.270255381  1.399349080 -0.052688935  0.117624598
##  [606]  0.977266614 -0.369029421  0.344122505  0.404159154  1.120799276
##  [611]  0.539672944  1.042354425 -0.568575567  1.490228316  0.200533556
##  [616]  0.727623551 -0.374613947  0.378239087  0.480298392  0.614453539
##  [621] -0.233437428 -0.400962977  0.777215123  0.738160263 -0.270896626
##  [626]  0.296352905  0.900234587  0.736658613  1.115563358  0.753475156
##  [631]  0.146556721  0.489933947 -0.583788779  0.123393316  0.400317417
##  [636]  0.737006853 -0.175435491  0.697078756  0.869876397  1.499747883
##  [641]  0.472267632  0.537198083  0.776058701 -0.937168671  0.175163227
##  [646]  1.219646256  0.348186287  1.240850435 -0.401934979 -0.018063799
##  [651]  0.467977678  0.453335482  0.407042024  0.903516467  1.062599835
##  [656]  0.761090805  1.050607027  0.790561978  0.498144200 -0.652439466
##  [661]  0.322946223 -0.648217299  0.490568497  0.711518832  0.301537125
##  [666]  1.633632377  0.182706385  0.690719396  0.473966395  0.860042233
##  [671]  1.105422515  1.159096856 -0.063085168  0.900596090 -0.141731899
##  [676]  0.740975244  1.641089475  0.490068114  1.183443867  1.301179034
##  [681]  1.411916960  0.856390535  1.358113211  0.788694219  0.384048832
##  [686]  0.321187620  0.092633163  1.416818871  0.669444041  0.317493329
##  [691]  0.475309229  0.734761095  0.075493111  0.643165664 -0.285430685
##  [696]  0.587435635 -0.820616899  0.904588826  0.057064333 -0.264874661
##  [701]  0.811139079  0.657180074  0.348673300  0.492051877  1.388608986
##  [706]  0.480490834 -0.053829343  0.427691979 -0.726511903  0.455222529
##  [711]  0.280827830  1.063392069  1.525900285  0.527830483  0.855871010
##  [716]  0.517931915 -0.271017776  0.397294436  0.913378187  0.336936336
##  [721]  0.720452296  0.572859599  1.038122386  1.302845901 -0.317859605
##  [726] -0.057370752  0.346939875 -0.479767108 -0.472814708  1.116024484
##  [731]  0.354373314  0.763860495  0.614570025  0.432620400  0.868779701
##  [736] -1.118149617  0.942453097  0.382080420  0.603618717  0.705977550
##  [741]  0.904076627  0.390272346  1.154009285  0.733435163  0.738476353
##  [746]  0.665003649  0.332531458  0.612560381  0.645651147  0.716350130
##  [751] -0.101145072  0.955561019  0.887611553  0.213749781  0.146531142
##  [756]  0.424643306  0.627984576  1.540609711  0.490186713  0.918048528
##  [761]  0.489046979 -0.061198651 -0.119106703  0.472100428  0.522099355
##  [766]  0.612008374  0.750359336  0.514611852  1.791858006  0.901698268
##  [771] -0.534850218  0.833052034 -0.731181098  0.737690084  0.656422031
##  [776] -0.357667964  2.267922595  1.037107647  0.645912020  0.766722304
##  [781]  0.030323830  0.503291397  0.878408709  0.912460977  0.060925705
##  [786]  0.493200292 -0.455905642  0.801480062  0.269021216  0.866999838
##  [791]  0.359461894  1.056593044  0.548553058  1.160346670  0.428147964
##  [796] -0.604903647 -0.670472441  0.460057970  0.556780871  0.474678164
##  [801]  0.938716297  0.108157682 -0.590070756  1.754084171  0.924171652
##  [806] -0.620088333  0.183478505  1.010091820  1.057787737  0.462582762
##  [811]  0.819120508  0.362797789  0.944019524  0.693610090  1.525445095
##  [816]  0.668965783 -0.464600447  1.066057952  1.242456183  1.546878487
##  [821] -0.411450693  0.809819104  2.044583455  0.990528997  1.963554939
##  [826]  0.465751730  0.196957439  1.216602647  0.739110372  0.354440622
##  [831]  0.979027298 -0.432221181  1.660513914 -0.528880725  0.798115599
##  [836]  1.026095419  0.799734230  1.244264430  0.963756046  1.258388899
##  [841]  1.312298447 -0.006565159 -0.453628828  0.700381805 -0.225803261
##  [846]  0.725319017  0.202932591 -0.001143902  0.613728608  1.680403158
##  [851]  1.057466086 -0.282903397 -0.255802849  1.336470497  0.973621582
##  [856] -0.033618017  1.172363172  1.018644994 -0.131492613  0.846160822
##  [861]  1.129361342  0.503204239  1.223733488 -0.438125342 -0.513666225
##  [866] -0.288204038  0.507698670  1.350869917  0.949365027  0.991197699
##  [871]  0.368086480 -0.031266801 -0.053162517  0.226317914  1.098280127
##  [876]  0.878792276 -0.249204539  0.069819508  1.587842989  0.879902396
##  [881]  0.754709476  1.405527441  0.259322847  0.727506636  1.983770474
##  [886]  0.136979051  0.809707169 -0.304837026  1.019307983  0.579672996
##  [891]  0.819419185  0.613250369  0.869526568  0.467998115  0.217631276
##  [896] -0.107783374  1.064765100  0.078040355  0.881924069  0.588835721
##  [901]  1.401686840  0.139106271  0.920602734  0.642720654 -0.579203347
##  [906]  0.733876244 -1.845031980  0.949781191  0.274339373  0.496690039
##  [911] -0.177609305  0.463966062 -0.580839138  1.173341240  0.774433743
##  [916]  0.985674810  0.678200423 -0.099694022  0.587783662  1.411840265
##  [921]  0.076939391  1.028290827  0.215096730  0.614232582  0.747528930
##  [926]  1.030307970 -0.576195913  0.972355711  1.287296691  0.752893471
##  [931]  0.700718349  0.358265738  0.616305828  1.434196632  0.679931675
##  [936] -0.138922797  0.865275184  0.560479481  0.093951970  1.390149969
##  [941]  1.070485121  0.815338570  0.375423373  0.424631104  0.233734669
##  [946]  1.095473089  1.537581348  0.952764984  0.601236087  0.361945166
##  [951]  0.237540179 -0.102036419  1.272332977 -0.418064680  0.338828900
##  [956]  0.009498583  1.439967767  1.130256289  0.936599140  1.172383632
##  [961]  1.171278066  0.735025591  0.714197684  0.815499743 -0.466727666
##  [966]  0.454823564  1.388010848  0.441078997  0.958878729 -0.318611049
##  [971]  0.561651019  1.327681526  0.966798930  0.949254489 -1.290992460
##  [976]  0.658647890  0.924125047  0.824514668  0.788055737  0.854104095
##  [981]  0.660638920  0.448785832  0.357003790  1.036755913 -0.015678863
##  [986]  1.132835305  0.512133035  1.138843869  0.660026125 -0.046354380
##  [991] -0.424054633  0.363699299  1.125562177  1.391002980  1.271663418
##  [996]  0.841695555  0.638572589  0.513666183 -0.119861771  0.680589004
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
```

```r
fit2
```

```
##       mean          sd    
##   0.47349415   0.30849060 
##  (0.09755329) (0.06897739)
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
## [1] -0.06868874  0.46801058 -0.71433702  1.90840429  0.16637209  0.56687207
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
## [1] -0.0166
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8924241
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
## t1*      4.5 -0.01291291   0.8716323
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 6 7 9 
## 1 3 1 1 2 1 1
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
## [1] 0.0141
```

```r
se.boot
```

```
## [1] 0.8959319
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

