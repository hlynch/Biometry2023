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
## 0 4 5 7 8 9 
## 2 1 2 2 1 2
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
## [1] 0.0099
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
## [1] 2.719951
```

```r
UL.boot
```

```
## [1] 6.299849
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
##    [1] 4.2 4.1 2.7 4.8 4.4 4.9 5.5 4.6 4.2 5.0 4.9 4.7 5.7 3.2 2.6 4.6 4.0 3.9
##   [19] 4.7 4.6 5.0 5.0 4.1 4.3 5.3 4.9 4.9 4.1 5.7 5.4 5.3 5.2 5.8 5.5 6.8 4.6
##   [37] 5.0 5.1 3.5 5.2 4.2 4.8 5.4 3.8 5.2 3.2 4.1 5.2 3.7 3.6 3.9 4.7 5.1 4.9
##   [55] 2.4 5.1 3.9 4.4 4.9 3.1 5.7 5.2 4.4 5.8 5.0 4.2 4.6 4.4 4.1 4.7 4.0 5.7
##   [73] 5.2 6.1 5.4 4.1 4.0 3.8 4.1 4.9 5.2 3.6 2.8 4.4 5.2 3.2 3.4 5.4 4.8 4.0
##   [91] 4.8 3.1 2.8 5.0 4.1 3.3 4.5 3.7 4.0 4.8 4.7 4.9 4.3 4.7 3.7 2.9 3.9 3.9
##  [109] 3.9 2.3 3.2 3.8 4.9 5.7 5.4 6.0 3.7 4.6 3.2 4.5 5.3 5.0 4.0 4.6 3.4 4.0
##  [127] 4.8 5.7 4.2 3.8 6.8 4.1 4.3 3.8 4.7 4.1 4.8 3.3 6.2 6.1 3.5 3.2 4.5 4.6
##  [145] 3.9 3.5 2.6 4.0 5.7 4.2 5.2 4.2 4.3 4.1 4.2 4.1 6.3 5.6 4.6 3.9 5.6 5.2
##  [163] 5.2 3.9 4.8 3.9 4.8 4.6 4.3 4.7 3.6 3.8 3.7 5.2 5.0 4.2 3.6 4.8 4.8 5.3
##  [181] 6.4 2.9 2.7 4.8 4.5 3.9 5.9 3.8 5.6 3.6 4.8 3.4 3.6 5.5 4.7 4.4 3.6 5.8
##  [199] 5.7 3.8 4.2 3.8 4.3 3.3 4.1 4.0 5.7 3.8 5.1 3.6 3.5 3.7 5.6 4.4 6.8 5.1
##  [217] 5.2 5.4 3.6 4.9 5.2 3.1 4.9 3.7 3.3 6.2 4.5 4.6 4.9 4.7 4.0 4.3 5.1 5.0
##  [235] 4.8 5.1 3.7 5.6 5.1 4.3 5.4 3.7 4.2 2.6 2.9 3.8 4.8 4.2 3.2 4.4 3.2 5.3
##  [253] 3.7 3.9 3.4 5.1 4.8 6.4 4.5 4.9 5.7 4.3 6.4 3.6 3.9 4.5 4.1 3.4 4.7 4.9
##  [271] 3.3 5.6 4.2 4.2 3.7 4.0 4.3 4.6 4.9 4.5 5.7 3.8 3.0 3.0 4.3 4.1 4.8 5.6
##  [289] 4.4 4.8 4.4 4.2 4.6 4.2 5.7 4.2 4.7 4.3 5.0 4.1 5.2 4.5 4.6 6.6 5.5 5.6
##  [307] 3.9 4.5 4.4 5.3 4.7 4.7 3.6 4.1 5.8 3.2 5.4 5.3 4.6 4.0 4.2 3.4 3.5 4.7
##  [325] 5.6 2.8 6.4 5.3 3.2 3.3 4.4 2.8 7.1 4.9 6.1 5.7 4.5 4.6 4.1 3.8 4.4 3.5
##  [343] 4.0 3.6 3.0 4.9 4.5 3.9 5.2 4.4 3.1 4.9 5.6 5.7 3.6 4.2 3.7 4.1 3.7 3.1
##  [361] 6.7 5.2 3.4 3.7 2.2 5.5 5.3 4.7 3.2 5.6 5.2 4.3 5.3 3.6 2.6 4.4 4.1 3.5
##  [379] 5.0 5.8 4.6 4.4 4.2 3.9 4.2 4.9 5.1 3.4 6.4 4.5 3.7 4.9 3.6 5.4 4.8 5.1
##  [397] 4.8 2.8 5.5 5.8 3.8 3.8 4.1 4.1 4.1 4.4 3.7 4.6 4.9 3.8 2.9 3.8 3.8 4.6
##  [415] 4.1 3.9 5.2 2.4 3.9 6.3 5.4 4.0 6.3 2.8 4.0 5.4 4.9 4.5 4.5 4.8 4.5 3.9
##  [433] 4.1 5.0 4.2 3.5 3.5 5.5 5.7 4.2 5.8 5.3 4.2 3.9 3.6 5.6 5.2 6.6 5.7 6.1
##  [451] 4.1 4.0 3.5 3.9 4.1 4.2 3.7 4.4 4.2 5.0 4.5 6.4 4.8 4.0 6.0 3.8 4.5 3.1
##  [469] 4.4 4.8 4.5 3.9 2.6 4.4 3.4 2.0 3.7 4.9 5.4 4.9 3.7 3.1 4.7 5.3 2.1 4.3
##  [487] 5.2 5.2 4.7 5.0 3.8 4.0 3.0 3.4 5.2 5.8 5.1 7.3 4.8 4.5 2.9 4.3 6.0 3.0
##  [505] 3.5 4.6 4.7 5.7 4.8 4.1 6.1 4.7 4.7 4.0 5.9 5.7 4.7 5.2 4.8 3.4 5.3 5.2
##  [523] 4.5 4.7 3.9 3.0 3.5 3.9 2.9 4.2 2.3 5.9 3.0 5.3 1.7 5.6 2.9 4.4 3.8 4.8
##  [541] 4.0 6.4 4.0 4.6 4.8 4.9 5.0 6.1 4.2 3.5 5.6 4.9 4.7 3.6 3.8 4.2 5.0 2.9
##  [559] 4.2 4.6 4.5 4.7 4.2 4.7 5.9 3.4 4.6 3.3 3.7 3.3 4.2 5.1 3.7 4.7 3.9 5.1
##  [577] 5.4 5.3 4.4 4.8 5.0 4.8 4.0 4.6 5.2 4.6 1.8 5.3 4.9 5.1 4.6 5.1 4.9 4.0
##  [595] 3.9 3.5 5.5 5.2 4.8 3.8 5.1 3.9 3.3 5.4 5.1 4.7 4.4 4.7 5.1 3.9 3.5 3.1
##  [613] 3.2 4.8 5.1 4.4 3.0 4.9 4.7 5.1 5.3 4.9 5.8 3.1 6.0 5.5 3.2 3.6 2.7 3.1
##  [631] 4.8 2.7 5.3 4.0 3.5 3.3 6.0 5.3 4.4 3.6 4.6 4.5 5.4 5.0 5.9 3.2 4.9 3.8
##  [649] 3.0 3.9 3.6 4.8 4.3 4.7 4.5 4.5 4.7 4.7 4.2 5.5 3.9 4.6 3.7 4.0 3.8 5.0
##  [667] 2.3 4.6 5.8 3.8 3.6 4.8 4.2 4.0 5.9 5.3 4.4 4.7 4.2 3.3 5.4 5.1 4.8 4.7
##  [685] 5.5 5.5 5.7 6.3 4.1 5.6 5.7 5.2 4.2 3.6 4.3 3.3 5.2 5.3 4.1 5.3 4.4 2.1
##  [703] 3.7 4.7 3.6 3.8 5.5 4.8 6.3 5.9 3.9 4.4 4.2 5.2 5.3 5.4 6.0 3.7 3.5 4.3
##  [721] 5.3 4.0 3.6 4.2 3.2 5.5 4.0 4.5 4.2 4.7 4.1 5.3 5.8 4.2 7.2 4.7 5.7 4.3
##  [739] 3.5 4.3 4.0 5.3 5.4 3.2 5.5 5.4 3.1 4.3 3.1 4.3 3.9 4.7 5.1 3.8 4.4 3.8
##  [757] 4.7 4.8 3.7 4.4 4.6 3.3 2.9 4.4 3.6 5.5 3.6 4.3 5.1 3.6 3.9 3.2 3.1 3.9
##  [775] 4.1 5.6 3.9 5.4 4.7 3.7 4.7 3.6 4.1 5.4 4.0 2.9 3.6 6.1 4.3 3.5 5.7 3.4
##  [793] 4.4 4.8 3.6 4.3 4.1 3.7 4.6 5.9 4.7 5.4 4.5 4.5 2.4 5.1 5.6 3.0 4.9 3.9
##  [811] 3.5 4.8 5.4 5.1 5.1 5.6 4.6 6.1 4.3 3.8 4.6 5.3 4.7 5.4 4.3 4.7 7.2 3.8
##  [829] 2.4 3.5 5.6 5.4 3.5 4.6 2.8 4.0 4.0 6.2 3.4 3.9 3.6 3.7 4.3 4.9 4.8 3.6
##  [847] 5.1 3.2 4.0 5.3 5.2 4.3 5.7 5.8 5.3 3.7 4.3 2.7 5.4 6.2 4.5 4.1 4.7 6.0
##  [865] 6.0 3.5 3.4 3.5 4.7 3.2 4.3 3.4 2.6 2.6 3.8 5.1 4.3 4.9 5.3 5.7 4.7 5.1
##  [883] 4.4 4.6 4.5 3.6 5.1 4.4 4.4 4.7 4.4 4.2 4.8 4.1 5.4 6.0 4.5 4.7 3.0 5.4
##  [901] 2.9 4.8 3.0 5.5 3.5 4.3 5.5 5.1 5.1 2.8 5.5 5.6 3.9 4.9 3.3 4.0 3.4 5.2
##  [919] 4.8 6.0 4.0 5.4 3.8 3.7 4.8 4.4 5.1 4.3 3.9 5.2 5.0 4.3 3.9 2.9 4.1 4.4
##  [937] 4.8 4.0 3.7 6.1 3.5 4.5 4.3 3.8 4.8 5.9 4.1 5.3 5.0 5.1 2.3 4.7 4.1 4.6
##  [955] 4.5 5.4 3.9 3.8 4.2 5.3 5.3 5.4 5.0 4.4 5.9 4.0 5.2 4.8 4.2 4.7 3.7 4.6
##  [973] 4.7 5.3 4.8 4.6 5.7 6.1 3.8 4.0 4.8 4.2 4.2 5.7 4.7 5.2 3.5 5.4 6.0 6.0
##  [991] 4.2 5.4 3.2 3.4 4.4 6.5 4.0 4.8 4.8 5.2
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
##   2.7   6.2
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
##    [1] 2.9 5.5 4.7 3.0 4.2 4.7 3.8 4.1 4.9 3.0 5.8 3.6 3.8 5.1 3.6 4.6 6.4 4.6
##   [19] 4.2 4.2 3.0 4.6 5.1 4.1 4.8 3.3 3.1 4.3 6.2 6.3 5.7 3.7 5.2 4.9 4.6 5.9
##   [37] 5.2 5.5 4.9 5.0 2.4 3.7 4.3 6.1 4.6 5.6 4.8 4.4 5.3 4.7 5.4 5.1 5.7 4.7
##   [55] 4.7 5.2 6.0 3.4 4.8 2.9 3.5 4.6 3.9 3.0 2.9 5.1 4.0 6.2 4.3 4.9 4.0 5.2
##   [73] 4.7 4.1 3.9 4.1 2.7 5.5 4.2 4.7 5.2 4.1 3.9 3.5 5.2 3.8 4.2 4.2 4.0 5.3
##   [91] 4.8 3.2 3.9 3.0 4.9 4.0 5.1 4.3 5.7 5.0 5.5 6.2 6.6 4.1 4.9 5.2 4.6 5.8
##  [109] 4.9 6.5 6.1 3.8 4.3 5.4 6.5 2.0 4.2 3.1 3.8 4.4 6.3 4.5 4.5 4.6 3.4 5.6
##  [127] 3.8 3.3 4.7 4.3 4.9 3.8 3.3 5.0 2.1 4.7 4.8 3.7 6.4 5.6 4.7 5.3 5.4 3.0
##  [145] 3.7 3.4 5.2 3.2 4.9 5.1 3.0 5.0 3.5 3.6 4.6 3.7 4.7 4.0 5.1 4.2 3.7 3.7
##  [163] 4.4 5.2 5.2 3.5 4.1 2.9 3.4 4.9 3.7 4.9 3.6 5.6 4.4 4.2 4.1 3.7 2.5 3.4
##  [181] 5.1 3.8 3.5 3.6 4.7 5.4 5.2 5.7 4.2 4.5 5.5 4.2 4.1 4.4 4.7 5.0 4.2 5.9
##  [199] 3.5 4.3 3.7 4.7 3.7 5.5 3.1 3.3 3.1 4.3 5.0 5.6 5.2 4.5 5.0 4.5 6.0 3.7
##  [217] 4.5 5.7 4.9 3.5 5.3 4.2 3.3 5.9 6.1 4.8 4.1 5.2 3.8 3.3 4.8 5.4 4.1 6.6
##  [235] 4.6 4.3 6.2 4.9 5.6 3.7 3.6 5.0 4.1 4.2 2.4 6.6 4.2 2.9 4.2 5.7 4.6 6.5
##  [253] 5.8 3.1 4.3 5.1 3.6 5.1 3.3 6.0 5.1 4.6 3.2 3.8 5.1 3.5 2.2 5.2 5.2 4.1
##  [271] 5.3 5.2 4.6 4.7 6.2 4.5 4.3 5.4 5.1 3.8 5.0 5.5 5.3 4.1 3.2 5.5 4.2 4.0
##  [289] 3.2 4.6 5.5 4.5 3.9 4.4 4.7 5.4 4.2 4.5 7.1 4.3 4.5 4.8 5.9 4.3 5.4 3.1
##  [307] 4.8 3.5 3.8 4.8 5.3 3.3 3.7 4.1 4.4 5.2 3.1 3.7 4.5 4.9 4.0 3.9 4.0 3.0
##  [325] 4.6 3.7 4.7 4.6 6.3 5.0 4.3 3.0 5.1 3.7 4.6 3.8 4.4 6.1 4.4 2.3 3.7 3.5
##  [343] 5.7 3.8 6.2 4.2 4.2 4.4 4.4 2.3 4.9 4.8 5.0 5.4 4.1 3.8 5.5 4.4 5.5 4.2
##  [361] 3.6 3.7 3.8 3.4 6.2 4.7 4.2 2.5 4.3 4.6 4.9 5.1 4.4 5.1 2.6 4.8 4.1 5.5
##  [379] 5.4 4.6 3.9 5.7 3.4 4.9 4.8 5.5 5.4 3.5 4.5 4.7 2.4 6.2 2.4 4.4 6.4 3.5
##  [397] 3.9 5.6 5.4 5.3 6.1 4.3 3.7 5.4 3.5 5.6 2.3 4.2 4.1 4.5 5.5 4.3 3.8 4.5
##  [415] 4.3 3.2 3.3 5.9 4.2 5.8 5.1 4.0 4.5 6.1 3.2 4.7 5.9 5.4 5.5 7.1 4.0 5.0
##  [433] 4.2 2.4 4.4 4.1 6.9 5.3 4.0 4.4 4.8 5.1 4.0 6.1 3.7 2.7 4.8 3.5 5.2 4.2
##  [451] 4.1 3.5 4.6 4.0 5.9 4.9 3.0 4.9 3.4 3.7 3.8 4.2 3.3 5.7 5.2 5.4 4.7 2.9
##  [469] 4.3 3.4 3.8 3.5 4.2 5.0 4.2 5.1 3.5 3.9 3.1 3.9 4.5 4.6 4.9 3.3 5.5 4.6
##  [487] 4.6 5.1 3.2 3.3 4.7 3.6 4.1 3.7 4.5 5.1 5.2 6.2 4.4 3.4 4.7 4.5 3.2 3.9
##  [505] 4.6 3.9 4.3 4.3 4.9 5.8 5.3 6.2 2.7 4.0 4.0 5.2 5.4 4.3 5.5 4.1 5.1 4.5
##  [523] 4.5 2.6 4.9 4.2 3.8 4.9 3.1 4.9 4.8 4.7 4.0 4.4 5.7 5.9 4.6 4.5 4.3 3.8
##  [541] 3.5 5.0 3.4 3.5 6.1 5.1 5.7 6.4 6.1 3.3 4.0 2.4 5.1 3.7 4.8 4.3 4.8 5.0
##  [559] 4.7 4.0 4.0 4.2 4.4 3.7 4.4 4.0 5.4 3.7 3.7 3.9 3.3 5.8 3.5 6.0 4.3 5.5
##  [577] 4.8 4.5 3.7 4.8 5.6 1.7 3.4 5.3 3.4 4.2 4.4 5.9 6.3 5.6 5.7 4.5 5.6 3.9
##  [595] 5.6 5.0 4.8 4.6 5.2 4.6 2.9 3.0 3.4 5.3 6.4 3.5 4.1 2.8 3.8 3.4 2.5 5.8
##  [613] 5.8 4.7 2.6 5.7 4.4 2.7 3.8 5.0 4.7 5.8 2.9 5.9 4.3 4.4 5.2 3.1 6.6 6.2
##  [631] 4.6 4.0 3.1 4.9 4.2 4.6 3.2 4.1 4.5 6.1 4.4 3.6 4.1 5.0 4.4 3.7 3.6 3.2
##  [649] 5.8 5.2 2.6 5.1 5.0 3.4 5.2 4.2 4.5 6.3 3.4 4.5 3.6 4.2 4.8 4.1 4.9 4.7
##  [667] 4.9 4.1 5.8 4.2 5.4 4.4 5.1 3.5 5.3 5.3 3.5 5.1 6.1 4.0 5.9 5.2 4.1 4.3
##  [685] 3.4 5.2 4.7 3.4 4.2 3.8 4.3 4.1 5.6 5.1 3.7 2.8 4.9 4.7 4.8 2.8 3.1 3.5
##  [703] 5.7 5.0 4.7 4.8 4.5 4.4 3.1 4.1 4.4 2.9 5.0 5.7 3.9 3.5 4.3 3.6 5.1 4.6
##  [721] 4.2 5.3 4.0 4.5 5.1 3.7 4.1 4.1 4.2 3.8 4.9 5.0 6.1 4.1 6.6 4.4 6.0 5.5
##  [739] 4.6 4.9 5.6 3.7 4.7 5.5 3.5 4.4 3.7 4.1 5.1 2.7 5.2 4.5 4.2 4.5 3.6 4.0
##  [757] 2.9 3.6 3.4 2.5 4.1 2.7 4.4 4.4 5.4 5.5 4.7 5.4 4.5 5.3 4.6 4.2 4.4 3.9
##  [775] 4.5 4.4 4.9 5.3 5.9 5.3 4.5 5.3 3.4 5.0 4.7 4.8 4.2 4.2 4.7 4.5 5.7 3.5
##  [793] 4.2 4.6 3.4 4.3 6.6 5.5 4.9 4.6 4.3 5.0 4.3 6.0 4.4 4.7 4.0 4.1 4.6 3.7
##  [811] 4.0 3.5 3.8 4.4 3.6 4.8 5.3 4.2 4.6 3.9 5.0 4.2 4.1 4.9 5.7 6.3 3.9 3.9
##  [829] 3.2 4.4 4.3 3.2 3.7 3.7 4.7 5.4 4.6 3.4 5.3 4.2 3.5 4.8 4.8 4.3 5.4 6.2
##  [847] 4.6 2.9 4.9 4.8 4.8 5.8 3.0 4.2 5.3 4.3 4.1 4.6 4.7 5.2 4.8 5.6 3.7 5.2
##  [865] 5.7 3.3 4.0 3.1 3.3 3.1 4.9 4.6 4.7 4.3 4.3 5.2 4.4 5.8 3.8 5.8 7.0 3.7
##  [883] 6.5 5.6 4.8 6.0 4.1 4.3 5.7 3.9 5.8 3.8 5.4 3.3 4.5 3.3 3.7 4.1 2.6 3.0
##  [901] 5.6 3.6 4.8 4.4 5.9 5.0 4.6 4.9 4.0 2.9 3.9 4.6 4.5 3.9 3.3 4.3 4.9 3.7
##  [919] 4.3 3.9 2.9 5.0 4.9 3.6 4.3 4.1 5.3 4.4 4.4 4.7 3.5 4.7 3.9 4.4 3.3 6.0
##  [937] 4.6 4.1 4.1 5.2 5.1 5.3 3.6 5.1 5.3 4.9 5.0 3.5 5.4 4.9 5.4 5.3 4.4 5.5
##  [955] 5.3 2.6 4.1 6.3 3.6 3.3 5.5 5.8 3.2 3.5 4.9 6.1 5.1 4.1 5.0 6.4 4.0 3.8
##  [973] 5.3 3.8 4.5 4.2 4.6 5.0 4.1 5.1 4.3 4.9 4.0 4.2 4.3 4.9 4.4 3.6 5.6 5.9
##  [991] 5.7 3.2 4.3 4.6 4.6 5.1 4.2 4.3 4.1 4.9
## 
## $func.thetastar
## [1] -0.0163
## 
## $jack.boot.val
##  [1]  0.50984615  0.37150685  0.26339286  0.16702703 -0.04157303  0.02782875
##  [7] -0.23196481 -0.22845745 -0.41705202 -0.52076023
## 
## $jack.boot.se
## [1] 0.9711186
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
##    [1] 5.4 3.9 4.4 4.0 5.6 4.4 4.1 4.5 3.1 6.0 3.5 3.4 5.3 5.2 3.5 4.0 4.2 3.5
##   [19] 4.1 7.2 5.3 4.2 4.6 5.2 5.2 4.9 4.1 3.5 4.3 3.6 3.1 5.7 5.7 4.2 2.5 4.2
##   [37] 4.7 6.5 6.3 4.6 5.2 3.8 4.8 2.9 4.5 5.1 3.9 4.7 3.2 4.5 4.0 6.5 4.0 5.3
##   [55] 4.5 4.2 5.1 4.7 5.2 6.5 5.7 5.8 3.8 3.0 4.5 4.3 5.7 4.6 5.4 4.8 3.8 3.5
##   [73] 4.3 4.1 4.1 5.6 5.0 3.7 3.3 4.4 5.3 4.5 4.3 4.2 4.7 3.2 5.9 3.3 3.9 4.8
##   [91] 3.9 5.1 3.9 3.6 5.3 5.4 4.0 3.7 4.6 3.5 4.6 4.2 6.0 3.1 4.0 3.9 4.7 5.4
##  [109] 6.2 4.5 4.5 4.6 4.2 2.7 4.3 5.0 2.1 4.9 4.1 4.6 5.3 4.2 5.4 3.6 4.9 4.9
##  [127] 3.6 4.9 5.3 3.8 5.5 4.9 4.8 5.0 3.7 3.3 5.7 6.4 5.0 4.3 5.3 5.1 3.8 4.2
##  [145] 6.1 4.5 4.4 3.9 4.0 4.4 5.3 5.8 4.0 3.0 5.6 4.0 6.4 4.8 6.4 4.3 4.6 4.4
##  [163] 4.3 3.7 4.9 4.5 3.6 5.1 5.7 5.9 4.6 4.5 5.2 4.9 4.4 3.9 5.2 4.7 4.2 4.5
##  [181] 2.9 5.3 4.4 4.0 4.7 5.8 4.9 5.0 3.6 4.4 3.7 4.2 5.1 3.5 5.2 4.2 3.9 4.6
##  [199] 3.8 2.1 4.4 1.9 4.5 3.4 3.7 4.2 3.9 4.8 4.8 3.9 4.6 4.9 2.5 3.2 3.4 4.1
##  [217] 2.9 4.6 4.1 4.4 2.6 5.2 4.5 4.3 3.8 3.7 3.6 3.2 3.6 4.7 4.9 4.6 3.8 4.5
##  [235] 3.0 3.7 2.7 5.0 4.3 3.5 2.6 3.8 5.2 3.8 4.9 4.9 4.5 6.2 4.3 4.2 4.3 2.5
##  [253] 4.2 7.2 5.4 5.0 5.1 3.8 4.5 4.7 5.6 5.6 6.0 4.1 5.7 3.4 5.3 4.1 4.6 5.8
##  [271] 3.5 5.7 3.2 4.7 3.7 3.1 4.5 2.9 5.6 5.1 3.0 5.9 4.5 6.0 4.6 4.4 4.0 5.3
##  [289] 4.4 4.1 4.7 6.3 2.5 2.5 4.5 3.8 5.2 5.6 4.2 4.0 4.7 3.7 5.9 4.2 4.1 3.5
##  [307] 3.0 3.4 5.2 4.6 2.2 3.9 4.2 3.6 3.8 4.9 4.7 4.4 5.6 4.4 3.8 4.7 5.1 4.3
##  [325] 4.7 4.7 4.1 3.1 4.4 4.4 4.2 4.9 5.6 5.4 4.0 4.8 4.7 3.2 5.3 4.3 3.6 4.5
##  [343] 3.8 4.9 3.7 5.2 2.9 5.5 5.7 5.0 3.0 5.7 4.8 3.5 4.0 4.5 5.5 2.9 4.8 6.3
##  [361] 4.1 4.9 4.6 4.1 5.2 4.6 4.9 4.4 4.4 4.5 4.4 3.3 5.1 4.7 4.6 5.8 5.1 3.8
##  [379] 4.7 4.5 5.7 3.1 5.5 5.8 3.6 4.1 5.1 5.1 6.7 4.3 6.4 5.2 3.9 4.7 5.0 4.5
##  [397] 4.5 3.8 5.5 5.0 4.0 5.0 5.0 5.4 4.0 5.7 4.1 3.9 4.1 6.4 4.2 3.1 5.6 3.9
##  [415] 5.2 4.8 5.2 3.5 5.0 4.5 3.9 5.0 4.4 4.6 4.6 4.1 5.6 5.7 6.1 3.9 4.7 4.8
##  [433] 4.5 5.8 4.3 2.7 5.7 4.7 5.2 2.9 5.0 3.2 5.1 4.8 4.1 3.2 5.2 4.7 5.2 5.2
##  [451] 4.1 4.9 4.9 4.2 5.4 5.6 3.7 4.9 5.2 4.6 4.2 4.5 3.1 3.2 3.7 5.9 4.1 3.3
##  [469] 4.6 5.5 3.5 4.3 4.9 3.1 4.7 3.4 2.6 3.5 4.1 3.9 1.6 4.4 4.2 5.3 4.1 4.5
##  [487] 2.5 3.6 5.2 5.3 4.0 4.1 4.8 4.4 4.3 5.6 4.9 4.2 7.2 4.6 4.8 6.7 3.8 2.6
##  [505] 4.7 4.3 5.7 5.0 4.9 2.5 4.9 3.7 2.8 4.8 4.8 5.2 4.7 4.8 5.5 5.2 5.4 4.6
##  [523] 5.3 3.3 3.7 5.6 2.8 6.0 4.9 3.6 3.4 3.1 2.9 3.7 5.6 3.3 4.7 4.5 5.5 5.0
##  [541] 6.2 3.1 6.0 3.6 4.5 2.9 5.2 4.8 4.5 5.3 5.2 4.0 4.9 4.4 4.9 5.0 4.9 4.3
##  [559] 3.9 4.4 5.4 3.9 4.6 4.7 4.7 5.2 4.6 4.2 4.2 3.9 5.1 3.7 6.6 4.4 6.3 4.7
##  [577] 4.7 4.9 5.1 4.7 2.1 4.4 3.7 6.3 4.0 4.0 4.6 3.4 4.0 5.6 4.8 5.2 3.3 5.6
##  [595] 5.1 4.9 4.4 3.8 5.1 4.5 6.4 4.6 3.8 5.3 4.3 3.7 5.4 3.8 3.2 3.7 4.1 5.1
##  [613] 2.7 3.8 3.3 5.8 5.0 4.7 4.0 3.8 5.0 3.3 5.7 3.3 5.3 5.3 5.2 4.0 4.7 4.8
##  [631] 2.6 4.4 4.2 3.2 4.2 3.0 5.5 4.7 3.9 4.7 4.8 5.2 3.6 3.6 5.4 4.2 5.3 4.3
##  [649] 5.0 6.0 4.3 3.6 4.9 4.5 4.7 5.1 4.9 4.9 4.1 4.4 3.2 3.2 4.5 2.6 4.5 3.8
##  [667] 5.2 4.4 4.3 3.9 4.9 5.8 4.8 4.9 5.2 4.2 5.3 5.5 4.0 3.8 3.4 3.9 5.0 4.3
##  [685] 4.2 5.2 4.7 4.6 4.3 4.2 4.3 4.4 4.1 5.4 4.6 5.5 3.8 4.6 4.4 4.8 5.4 3.8
##  [703] 4.7 4.9 3.0 5.7 4.3 4.3 4.3 4.4 3.1 4.7 3.1 5.3 5.7 6.0 6.0 3.5 4.7 4.4
##  [721] 3.7 5.3 3.0 4.9 3.9 5.7 4.7 4.9 4.7 4.2 3.9 6.0 2.9 3.5 5.6 5.1 5.2 4.2
##  [739] 4.1 3.7 3.7 2.5 6.7 3.5 4.5 4.3 2.9 3.8 4.7 5.4 4.1 4.6 5.4 3.6 5.5 5.2
##  [757] 6.2 4.7 4.1 3.6 4.6 5.4 3.9 3.9 4.5 5.5 4.4 4.5 6.0 4.8 4.7 4.2 3.8 6.1
##  [775] 3.3 5.1 3.6 4.7 4.0 3.7 5.2 5.7 4.4 5.1 4.0 2.4 3.7 4.6 4.8 5.2 5.1 5.0
##  [793] 4.3 6.6 2.5 3.8 3.0 4.0 4.9 4.1 4.6 5.9 4.1 5.1 3.6 5.0 4.2 4.6 5.0 3.0
##  [811] 6.3 4.1 3.1 3.2 3.2 4.4 3.7 4.0 3.3 5.0 4.7 4.7 4.1 4.6 4.6 4.0 5.2 3.7
##  [829] 2.7 5.6 3.9 5.2 3.9 5.0 4.8 4.6 3.3 4.1 5.0 3.2 5.5 4.8 4.6 3.8 4.2 5.4
##  [847] 3.0 4.9 3.4 3.7 6.8 4.8 5.2 4.0 4.5 3.7 2.7 3.7 5.0 5.7 4.7 3.1 4.4 5.1
##  [865] 5.0 4.7 4.9 5.3 4.7 6.0 3.8 4.7 4.9 4.9 3.2 3.9 4.7 5.0 2.7 3.6 3.5 3.8
##  [883] 4.5 2.5 5.1 4.0 4.4 3.7 3.3 5.4 2.4 4.0 4.6 4.1 3.6 3.5 3.5 4.1 5.5 4.5
##  [901] 4.8 4.3 3.7 4.4 4.0 3.6 5.2 4.8 4.1 5.0 3.8 5.3 3.9 3.7 3.7 3.7 3.8 2.5
##  [919] 4.0 3.9 4.6 4.1 3.6 4.6 5.2 4.6 5.2 5.1 5.4 3.7 6.2 4.3 4.6 3.5 6.5 4.4
##  [937] 4.2 3.5 5.7 4.6 3.6 4.3 5.9 5.7 4.6 3.6 4.5 5.5 6.3 5.0 4.6 3.6 4.4 3.2
##  [955] 4.7 4.0 5.2 5.9 3.4 4.5 5.3 2.4 3.7 4.2 4.0 5.5 5.2 4.9 6.0 5.8 3.0 5.1
##  [973] 6.3 4.6 5.2 4.7 4.3 5.6 3.3 5.1 5.4 5.9 3.1 5.2 3.4 5.0 4.3 3.9 4.2 3.8
##  [991] 4.2 4.4 4.6 3.7 5.6 3.2 4.0 5.3 4.5 5.1
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.400 5.200 5.100 5.100 4.824 4.800 4.700 4.600 4.400
## 
## $jack.boot.se
## [1] 0.9666885
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
## [1] 0.9610736
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
##   3.214583   6.022148 
##  (1.369576) (2.776906)
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
## [1]  0.3479911  0.3692371  1.2275353  0.9275886  0.2468274 -0.4246277
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
##    [1]  0.351182168 -0.348523696  0.702922454  0.448423106  0.283047956
##    [6]  0.170015772  0.316328916  0.080075138  1.007540006  0.741500246
##   [11]  1.834432652  0.871045555  0.594904041  0.941428752  1.190129725
##   [16]  1.174304413  1.473954714  0.466240233  1.360346076  1.100700604
##   [21] -0.709813661  0.808513867  1.677487358 -0.520628819  0.419010882
##   [26]  0.698945777  0.431933731  0.582851557  0.037674573  1.822333942
##   [31]  0.577862184  0.521164600  0.597389704  1.047514645  0.363826154
##   [36]  2.120245463  1.829831700  1.363695306  1.043045401 -0.938099229
##   [41]  0.073174162  0.740084575  1.337972683 -0.242070269  0.127346098
##   [46]  0.502538462 -0.253207568 -0.062690562  1.380305773  1.144561370
##   [51]  0.629882607  0.457198329  0.438836244  0.902443503  1.476368414
##   [56]  0.543577004 -0.192665126  0.456922922  0.511392254  0.203288839
##   [61]  0.166046489  0.965823606  0.080778976  1.144235356  0.344221452
##   [66]  1.195107430  1.351489584  0.934766663  0.516829565  0.648434200
##   [71] -0.339564809  1.397198962  0.476190735  1.063859520 -0.113934817
##   [76]  0.716164127 -0.401291065  1.224837337  0.323451816  0.550565605
##   [81]  0.290756308  0.742798223  0.051020457  0.366409607  0.994771092
##   [86]  0.896117497  0.392237030  0.003231037  0.780921490  0.720598756
##   [91]  0.194944398  0.359735498  0.712316591  0.858742623  0.629343955
##   [96]  0.626921259  0.327800934  0.601284991  0.348191938  0.952481500
##  [101]  1.560021284  0.647547522  0.479621034  0.806792605  0.583752656
##  [106] -0.051829323 -0.457809061  0.865886033  0.547032489  0.986747952
##  [111]  0.142888332  1.105945863  0.988466428  0.805997345  0.904246976
##  [116]  0.004308050 -0.602254063  1.044201860 -0.031684431  0.646017616
##  [121]  1.323467583  0.517411556  1.400794416  1.223881785  0.733901204
##  [126]  0.386035486  0.800419167  1.734546406  0.549777238  0.635811607
##  [131]  0.237070205  1.106085103  0.969510452  1.355819670  1.355708430
##  [136]  0.912256717  0.701237554  1.045374083  1.706860611  0.495613426
##  [141]  1.242487519 -0.068817677  0.952763546  0.422154896  0.461274171
##  [146]  0.317658147  1.333624561  1.019929967  1.246217747  0.551171271
##  [151]  1.105945863  0.118418952  1.418706504  0.343241416  0.359765747
##  [156]  1.485965295  0.627893471  1.450398252  0.810446034  0.604003115
##  [161]  0.633831018  0.435397565  1.052350669  2.063674046  0.791510313
##  [166]  0.108006500  1.091871937  0.695938290  0.874438456  0.098775700
##  [171]  0.405982584  1.169952568  0.253890945  1.039411753  0.586783751
##  [176]  0.606489241  0.120820620  1.204627678  0.720359150  0.613940269
##  [181]  1.322823455  1.052980017  0.933870586 -0.191940550  1.254112476
##  [186] -0.731418716  0.646745400  1.500798196 -0.094094447  1.439834049
##  [191]  0.493004894  0.139885063  1.457298591  1.190658756  0.808680015
##  [196]  0.413140125  0.693819611 -0.076188647  0.128483426  0.526890205
##  [201]  1.089687195  0.420419973 -0.018272374 -0.087617420  1.357646101
##  [206]  0.590918943  0.749269373  0.421617845  0.183884378  0.234211432
##  [211]  0.158293626  1.830237417  0.926311336  1.144919696  0.824503102
##  [216]  0.492780504  1.656191736  0.861718919  0.955540590 -0.007955180
##  [221] -0.575828547  1.134219423 -0.493947995  0.975259221  0.934523186
##  [226]  0.633012612  1.571856986  0.459818014  1.247614276  0.290124857
##  [231]  0.765913179  0.400614529  0.547122645 -0.222286882  0.890324700
##  [236] -0.172371008  1.008840154  0.917670768  0.359735498  1.416507746
##  [241]  0.865385615  1.399357998 -0.152312851 -0.871676277 -0.129243148
##  [246]  0.815623363  0.648629266  1.049310151  1.384070647  1.513551407
##  [251]  0.519103045 -0.285064709  0.606982358  0.732330774 -0.018662042
##  [256] -0.291469906  0.838985962  0.419010882  0.219731908  0.222385136
##  [261]  0.568640379  2.004933835  1.213872099  0.723173951  0.372918401
##  [266]  1.469212484  0.608471057  0.942555876  0.037496680  0.782046349
##  [271]  0.900736652 -0.439430870  1.023168027  0.744455610  0.115272866
##  [276] -0.046430887 -0.716602922  0.070160463  0.677146801  0.158985362
##  [281] -0.009209108  0.320680131  0.607368970  1.493520330  0.900718617
##  [286]  0.835109820  0.955646620  0.323542935 -0.566418916 -0.220803351
##  [291]  1.764304007  0.395988602  1.071509102  0.495171102  1.162847599
##  [296]  0.557477307  0.671586877  0.631111403  0.407354676  0.875008428
##  [301]  0.282990268  0.944374967 -0.646186583  0.758519169  0.765812290
##  [306]  0.469644813  0.800368047  0.192742785  1.615561683  0.401517274
##  [311]  0.886413119  1.443013522  0.109109674  0.328616899  1.235682813
##  [316]  0.105492927  0.446637916  0.560153409  0.762612184  0.415939027
##  [321]  1.190129725  0.809091925  1.133807607  2.283743924  0.905046722
##  [326]  0.007872164 -0.415745216  0.025773576 -0.059899283  0.892461395
##  [331]  0.989725805 -1.168108483 -0.150811512  0.545172564 -1.002955155
##  [336] -0.621411431  0.369389735  0.881550630  0.741149516  0.774784480
##  [341]  1.111240125  0.685787030  0.597412505  0.406801273  1.550096516
##  [346]  1.396505287  0.938236292  0.011703322  0.098463875  0.468231305
##  [351]  1.086413601  0.919467688  0.470945482  0.678875053  1.553460098
##  [356]  0.538483589  0.251702121  0.194662237  0.162191725  0.418729816
##  [361]  0.577862184  0.767236482  0.286130269  0.986174298  1.150334308
##  [366]  0.900030291  0.721456389  1.844539419 -0.218474550  0.753275377
##  [371]  0.763665992  0.237054019  0.317217347  0.712061601  0.073480035
##  [376]  1.723285194  1.052565904  0.349377287  0.285539151  0.845556639
##  [381]  1.395642766  0.105744430  0.510266601  0.440431293  0.205429840
##  [386]  0.902169540 -0.408776132  1.270299209  0.594970855 -0.077632602
##  [391]  1.047391617  0.394031097  0.352519695  2.036850156  0.522753331
##  [396]  0.406992875 -0.188662149  1.914203211  1.496844585  0.601284991
##  [401]  0.954787805  0.283373275  1.172936490  0.712650586  0.705205774
##  [406]  1.099050590  1.547042263  0.045499091  0.294721603  0.231588168
##  [411]  0.719664412  0.515049226  0.179454886  0.908205780  0.759754574
##  [416] -0.067395456  1.639874238  0.782927210  0.814971774  0.172639779
##  [421]  0.425129035  1.194830216  0.893030582  1.104519667  0.845596012
##  [426]  1.530214298  0.194944398  1.158456780  0.773960194  1.863065515
##  [431]  1.124683877  1.028498942 -0.552370217  1.534617166  1.345682484
##  [436]  0.413015784  0.663534520  0.432749030  0.153289290  0.700736141
##  [441]  1.077866209 -0.214443886  0.177491036  0.565102054  0.954845701
##  [446]  1.948300484  1.080050674  0.601033647  0.691339051  0.617176201
##  [451]  1.295150217  1.927196603  0.555238710  0.879883190  1.315954844
##  [456] -0.372145239  1.440951327  0.685095357  0.806792605  0.602335739
##  [461]  0.445797583  0.132584615  1.502245984  1.174832261  1.062436157
##  [466]  0.731018097  0.630658248 -0.464738856  0.339043273  1.039609613
##  [471]  1.042993843  0.207262831  1.136252975  1.274533384  1.321142468
##  [476]  0.563317375  1.337284415 -0.104960586  0.103687005  0.542212156
##  [481]  0.700915377  0.174674099  0.421162615  0.709204264  0.881219404
##  [486]  1.236061984  1.345682484  0.989929558  0.690362273  0.709107849
##  [491]  1.214032696  1.225090958  1.361584364  1.207930101 -0.018158871
##  [496]  0.466797755  1.226585251  0.338086957  0.302685734  0.866703181
##  [501]  0.047527403  0.197763579  1.079378174  0.579806832  0.253003168
##  [506] -0.149686645  1.032937462  1.445948620  1.255180701  0.287687086
##  [511]  0.864955679  0.797500188  1.220260775  0.662493341  1.255916101
##  [516]  0.740083631 -0.508564243  1.119312293  0.772721168  0.058902759
##  [521]  0.508019646  0.339569235  0.039137694  1.948661061 -0.133359588
##  [526]  1.564351655  0.020343737 -0.239382052  1.253322069  0.343210963
##  [531]  0.723382967  0.231464750  1.480144026  0.210855122 -0.339365392
##  [536] -0.354736011  1.188564745  0.876658752 -0.303640927  1.382739142
##  [541] -0.037085752  0.351348252  0.307459924  0.669321768  0.282719931
##  [546]  0.135442151  0.517385501  0.036739298  0.124519262  1.273784870
##  [551]  0.381079049  0.039329413  1.110652633  1.679151401  1.292501312
##  [556]  0.584186664 -0.181228922 -0.480785562  0.108230671  0.590587701
##  [561]  0.489868879  0.225446030  0.532752397  1.676701461  0.959391840
##  [566] -0.072547583  0.348616024  1.054348792  1.282205214 -0.777213944
##  [571]  0.041785967  1.131609563  0.569721566 -0.096404015  0.474843345
##  [576]  0.223791921  0.915426478  1.160293883  0.627204084  0.735385882
##  [581]  0.935841864  1.198099516  0.671292483  0.803479055  0.608092441
##  [586]  1.152528322 -0.678060594  1.032596519  1.242120227  0.696081151
##  [591] -0.344498342  0.047588129  1.849230294  0.670850371  0.372246512
##  [596]  0.589126950 -0.024951887 -0.293394184 -0.012559378  0.738414672
##  [601]  1.630591994  1.083066455  1.071243647  1.023329685 -0.032695549
##  [606]  1.247090315  0.966479075  0.724847576  0.521273061  1.407961080
##  [611]  0.648019349  0.517385501  0.097062222  1.214743649  0.505341244
##  [616]  0.968382919  0.445635659  1.525199615  1.742989386  0.514188083
##  [621]  1.055390451  0.513760365  0.984802275  1.408850139  1.221198440
##  [626]  1.177921018  1.404144805 -0.199983569  0.039863319  0.629343955
##  [631]  0.411399135  0.286721990  0.480813517  0.403398121  0.606789016
##  [636] -0.023013992  0.702287161  1.064406792  0.617693706  1.206215711
##  [641]  1.788576128  0.351274342  0.406801273  0.505476728 -0.476493780
##  [646]  0.186492691  0.995909945  0.482144437  0.746946355 -0.005874845
##  [651] -0.587677567 -0.434065524  0.591372234  0.840258478  0.881550630
##  [656]  1.222757014  1.186435628  0.442579224  1.087800670  0.191243837
##  [661]  0.410978220  0.921561668  0.083044389  1.402943641  0.782490026
##  [666]  1.781975454  0.870677373  0.377537086  0.423097724  1.216374669
##  [671] -0.514108087  1.267080473  1.144561370  0.329567447  0.377213552
##  [676]  0.771727559  0.947476603  0.378319198  0.533261801  1.023953531
##  [681]  0.309541864  0.499913133  1.523812314  0.632299260  1.009352350
##  [686]  0.641197732  0.767272369  0.473902986  0.728703363  1.399357998
##  [691]  0.718171018  0.508039158  0.615812091  0.334008728  1.091603302
##  [696]  0.694293338  0.825881964  0.798444834  0.160032278  0.570787920
##  [701]  1.175852384  1.364011010  1.129956642  0.324764889  1.156046484
##  [706]  1.262058215  1.605154795  0.265904184  0.121331398  1.144776121
##  [711]  0.010928729  0.905026719  0.118307082  0.934766663 -0.104484265
##  [716] -0.001112464  0.410898950  1.056465478 -0.257726665  0.504745134
##  [721]  1.966321340  0.335249815  0.155321169  0.850037580  0.524939914
##  [726] -0.268337079  0.847279425  0.466787798  1.753618644  0.905146090
##  [731]  0.335908861  0.647547522  1.119165019  0.806619702  0.524573797
##  [736] -0.105398834  0.385552521  0.593794286  1.614835718  1.002686856
##  [741]  0.177491036  2.083229639  0.286453636  1.305202447 -0.107362224
##  [746]  0.639649544  0.706602024  0.641203134  0.620802886 -0.224516532
##  [751]  0.346119214  0.890038684  1.439895574  0.652814316  0.238064344
##  [756]  0.670950639  2.524534190  0.799528690  0.984912599  0.667390477
##  [761] -0.005033994  0.522702418 -0.336681938  0.391982666 -0.063525153
##  [766]  0.991045692  1.790253707  0.395443445  0.023226800  1.022801555
##  [771]  1.149740088  0.697583125  1.390039479  0.670814076 -0.027964517
##  [776]  0.419745514  0.797936376  1.444886443  0.302204421  0.437710973
##  [781]  1.749460695  0.554482541  0.286162432  0.683651486  0.292250945
##  [786]  1.086226256  0.961483273  0.656962328 -0.813749573  1.671893570
##  [791]  0.388170399  0.602679115  1.132629466  0.785559499  1.187094170
##  [796]  2.024884589  0.952844387  0.499196298  0.732330774 -0.755317393
##  [801] -0.011826264  1.235260252  1.110693885  0.938528821  0.125591451
##  [806]  0.802358436  1.249251984  0.586918052  1.193090702  0.603308088
##  [811]  0.532960503  1.055403360  1.438316797  0.379378369  0.455408598
##  [816]  0.313330241  0.067078663  1.253776629 -0.118762574  0.890842658
##  [821]  0.883136603  0.608672063 -0.529677417  0.306331586 -0.501450810
##  [826]  1.016347095  1.167677355  0.963131534  0.358996267  0.619847768
##  [831]  0.026172440  1.569325559  0.912950120  0.372679924  0.747854608
##  [836]  1.885450497  0.791461129 -0.636040097  0.734823306  1.146278044
##  [841]  0.443300678  0.943655057  1.283618127  0.094362650  0.738670062
##  [846]  1.103684032  1.561965871  1.227657358  0.744569733  0.775122451
##  [851] -0.204488356  0.490445144  1.202637053  0.076753493  0.341506601
##  [856]  0.183884378  1.077737557  0.703206289  0.533577244  0.602459101
##  [861]  1.095671498  1.105360119  0.737080959  0.457795459  1.388921027
##  [866]  1.090346136  0.819067909  0.708758154  1.776372065 -0.055348315
##  [871] -0.098211636  0.427060734  0.188919664  0.792285178  0.755618487
##  [876]  0.082704440  0.913384043  0.648578557  1.170778868  1.579632866
##  [881]  1.396512372  0.714332059 -0.223281038  0.838630774  1.029845746
##  [886]  0.827842373  0.706715466  0.536228721  1.092640139  1.212467604
##  [891]  0.370251379  0.863248046  0.484179901  0.487519140  0.109350769
##  [896]  0.052635317  2.006154612  0.628636783  0.243489400  2.332165057
##  [901]  0.645775926 -0.009209108  1.437600445  0.146781763  0.834451816
##  [906]  0.634757577  0.951982368  0.714332059  0.639754604  0.801756324
##  [911]  1.091603302  0.738273467  0.655368959  0.665582294  1.312346153
##  [916]  0.629413997  0.751867110  1.364619178  0.896617703  0.692168089
##  [921]  0.621308075  0.104801436  0.309617654  1.405152247 -0.204934902
##  [926]  0.462498955  0.968118418  1.203138633  0.433803029  0.151503214
##  [931]  0.837741092 -1.296933156 -0.106878614  1.105158830  0.068280166
##  [936]  0.142988520 -0.267525422  1.226981038  1.036796564 -0.297934571
##  [941]  1.202052153  0.768800035  0.692918921  0.951337645  0.593950451
##  [946] -0.031822006  0.385973176  0.223598931  0.724623566 -0.447861118
##  [951]  1.959743474  1.063859520 -0.042060061 -0.292892791  0.685274853
##  [956]  0.499707648  0.201897772  0.026702505  0.237823238  0.819301654
##  [961]  0.377331191  1.290818013  0.663530531  0.945221536  0.351797096
##  [966]  0.734041565  0.758082316  1.280412131  0.351168936  1.170526438
##  [971]  1.847474324  0.358298881  0.790294968  1.156427749 -0.295400644
##  [976]  0.891258747  1.000541080 -0.437574116  0.769543307  0.289963962
##  [981] -0.485918689 -0.030285053  1.650676786  0.987691816  0.300000534
##  [986]  0.321942848  1.030169026  0.912323831  1.035781276 -0.058719635
##  [991]  0.181147759  0.726366519  0.734399022  0.634840747  1.096704404
##  [996]  0.815760140  0.844725862  0.080384792  0.943037619  0.505322183
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
##   0.53379327   0.30814518 
##  (0.09744406) (0.06890042)
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
## [1]  0.11662546  0.30283563 -0.28549847  0.89180876 -0.02484787 -0.91284556
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
## [1] 0.0202
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9197706
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
## t1*      4.5 -0.00950951   0.8787286
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 3 6 8 9 
## 2 1 5 1 1
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
## [1] -0.0104
```

```r
se.boot
```

```
## [1] 0.9214016
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

