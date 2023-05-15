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
## 0 1 6 7 8 9 
## 3 1 2 1 1 2
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
## [1] 0.0503
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
## [1] 2.820342
```

```r
UL.boot
```

```
## [1] 6.280258
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
##    [1] 6.6 4.3 5.1 5.5 5.0 4.9 4.9 5.7 4.1 5.3 3.8 5.5 4.5 6.2 2.4 2.3 5.3 5.5
##   [19] 5.4 3.5 4.4 4.6 3.8 6.0 5.5 6.1 6.2 4.4 3.7 3.2 4.6 5.1 5.1 3.3 5.8 3.8
##   [37] 4.5 4.4 5.0 4.5 4.5 4.6 4.7 4.9 4.6 5.0 3.9 4.5 5.7 4.5 4.6 5.4 4.7 4.9
##   [55] 3.6 4.2 5.9 4.3 3.2 4.9 4.0 4.9 4.6 3.7 4.9 3.2 3.4 5.1 6.3 3.7 6.0 3.0
##   [73] 5.3 3.3 3.9 4.7 6.7 3.3 3.4 4.5 3.0 5.4 3.7 4.5 4.3 3.7 5.6 5.7 3.2 2.4
##   [91] 4.2 4.5 5.0 5.5 3.9 4.7 3.7 4.1 4.5 5.8 4.8 3.5 4.9 4.7 4.8 4.3 4.0 3.9
##  [109] 5.6 4.3 5.6 2.9 5.2 4.1 4.7 3.8 6.3 5.7 4.2 4.9 4.2 5.3 3.7 4.5 4.4 4.7
##  [127] 5.4 5.4 5.6 3.2 4.2 4.8 5.5 3.1 5.4 5.5 5.5 4.0 4.1 4.2 5.4 6.0 5.0 5.9
##  [145] 6.0 5.6 2.8 4.5 4.6 5.7 5.4 2.7 5.8 5.9 4.6 3.7 3.7 3.2 4.2 4.7 4.1 4.7
##  [163] 3.3 5.0 4.6 6.0 4.4 5.6 5.6 5.2 4.9 3.2 4.8 6.4 5.0 3.9 4.9 5.6 6.3 4.5
##  [181] 4.0 2.5 3.6 6.3 2.8 6.1 4.2 3.5 4.6 5.4 4.2 3.8 5.2 4.2 4.1 5.7 5.7 5.1
##  [199] 4.5 5.9 4.5 4.0 5.3 4.6 6.0 6.4 5.9 4.2 4.2 1.9 5.3 4.0 4.2 4.0 4.3 4.5
##  [217] 4.2 5.6 4.4 5.0 5.4 4.6 3.2 4.4 5.6 5.0 4.9 3.8 5.1 5.8 5.3 5.7 4.9 4.6
##  [235] 6.2 5.6 5.0 5.0 5.0 4.0 5.2 4.6 3.9 3.9 5.9 4.7 4.2 5.3 4.5 5.0 5.4 3.7
##  [253] 4.2 5.7 3.5 3.0 5.5 4.7 6.2 4.5 4.2 5.5 4.5 4.4 4.5 3.4 4.8 3.6 4.9 6.7
##  [271] 4.9 3.4 5.1 4.2 5.5 3.9 3.9 3.9 4.2 4.8 5.2 3.1 5.3 5.0 4.0 3.7 4.4 3.5
##  [289] 5.1 3.9 4.7 5.2 2.2 4.4 4.1 4.6 6.8 4.9 5.3 4.1 4.6 3.3 5.0 5.1 4.9 4.0
##  [307] 4.5 4.2 3.2 3.8 3.8 5.8 5.2 4.4 2.8 5.0 5.5 3.1 5.0 4.6 4.2 6.0 3.8 3.0
##  [325] 3.2 6.0 3.6 5.2 4.8 3.9 4.6 4.5 6.3 5.6 4.8 3.6 2.8 4.7 2.6 5.4 4.6 4.0
##  [343] 3.8 3.6 4.7 4.0 3.7 4.3 4.2 3.5 5.1 6.9 4.3 3.6 3.5 4.7 4.8 4.5 4.6 4.4
##  [361] 5.4 4.5 4.6 3.7 5.1 4.5 4.2 4.4 2.8 6.1 5.2 4.5 3.9 4.5 4.7 5.2 4.2 3.8
##  [379] 3.9 5.4 3.9 3.1 4.7 3.8 3.8 4.9 4.6 4.0 4.2 3.0 4.9 2.9 3.2 5.1 5.6 3.9
##  [397] 3.8 5.4 5.3 5.3 3.7 4.6 5.8 4.5 4.1 4.8 5.7 4.6 3.8 6.2 4.2 5.7 4.1 4.8
##  [415] 4.2 5.4 3.8 4.2 3.5 3.7 4.1 4.5 5.2 2.2 3.5 3.9 2.7 4.2 5.3 3.5 2.6 4.9
##  [433] 5.2 5.0 5.8 5.2 5.4 5.2 3.9 3.7 4.5 4.0 4.5 3.6 5.3 3.3 5.7 3.7 3.7 5.2
##  [451] 5.3 4.0 3.7 4.4 3.8 4.2 5.3 5.1 4.7 3.8 4.6 5.0 3.8 5.5 4.4 4.8 5.7 4.2
##  [469] 4.8 5.0 5.5 4.5 3.7 4.0 4.3 4.4 3.2 4.7 4.4 4.8 4.3 3.9 4.9 3.7 4.2 3.6
##  [487] 4.5 4.8 3.7 3.5 4.0 2.9 3.8 4.4 3.4 5.2 4.3 3.4 4.6 3.1 4.9 3.3 5.4 3.6
##  [505] 6.0 3.9 4.4 6.4 3.7 4.3 3.9 3.1 4.1 4.6 4.8 5.5 4.0 3.1 5.1 4.7 4.5 4.5
##  [523] 4.5 5.0 5.1 3.3 3.8 5.5 5.0 4.4 4.9 6.3 5.6 5.0 4.9 2.7 3.5 5.4 4.5 5.4
##  [541] 4.3 5.0 4.3 4.8 4.5 3.7 6.3 5.4 2.8 4.7 5.1 4.8 3.9 5.3 4.8 6.3 5.3 3.8
##  [559] 2.3 5.8 5.1 5.9 3.9 4.2 4.3 3.8 4.7 3.3 4.7 3.1 4.5 4.5 5.0 5.0 4.7 2.0
##  [577] 5.1 1.6 3.9 3.5 4.1 3.5 4.1 4.4 5.3 3.9 5.1 3.3 3.3 4.1 3.4 4.4 3.1 3.8
##  [595] 4.7 3.8 5.2 5.0 5.2 4.1 3.4 5.2 4.4 3.8 3.9 4.2 4.1 5.8 5.1 3.9 4.6 3.4
##  [613] 4.5 4.5 5.1 5.8 4.6 3.4 6.7 4.3 4.4 5.3 4.8 5.7 3.3 7.2 3.8 5.0 4.2 4.1
##  [631] 4.7 5.7 4.6 4.8 3.8 6.1 3.3 3.8 4.5 4.9 3.8 4.2 4.1 3.8 5.4 4.8 3.8 4.8
##  [649] 6.3 2.4 4.7 4.8 5.2 5.9 3.6 4.2 2.8 5.5 4.2 5.5 4.2 3.8 5.3 4.7 5.8 3.4
##  [667] 4.9 3.5 4.4 3.0 2.7 5.1 4.5 5.3 4.5 4.6 4.0 4.2 4.2 3.7 3.8 3.4 5.7 4.5
##  [685] 5.6 5.3 4.2 5.0 3.4 4.3 4.3 4.5 3.5 6.0 4.3 3.1 5.6 3.9 4.5 4.7 3.5 4.6
##  [703] 4.2 4.4 4.4 5.2 5.3 5.7 5.7 5.0 3.9 6.3 5.7 2.6 4.9 3.7 6.1 4.7 4.2 3.5
##  [721] 3.3 5.4 2.3 5.0 4.7 4.2 5.9 3.5 5.4 4.3 4.5 4.9 4.6 2.7 5.1 4.7 5.1 4.3
##  [739] 6.8 4.3 4.9 3.8 4.6 4.2 4.5 4.3 4.6 5.4 5.0 3.7 3.1 4.0 3.4 5.1 4.2 4.0
##  [757] 5.0 4.5 4.0 4.3 5.0 4.9 4.3 5.3 3.2 5.1 3.9 5.4 4.7 4.8 5.9 4.0 5.2 4.3
##  [775] 4.4 4.9 3.7 5.5 4.2 4.4 5.9 5.5 4.2 3.9 5.1 6.3 4.9 4.6 4.1 4.0 3.7 4.9
##  [793] 4.3 4.0 4.3 3.8 4.5 4.0 5.3 4.3 6.6 3.1 4.4 3.3 5.4 5.3 3.5 5.4 5.7 5.2
##  [811] 5.2 6.4 4.8 4.4 3.7 5.1 4.1 3.7 6.7 5.0 5.2 5.9 3.8 4.1 3.0 5.0 4.5 4.1
##  [829] 5.9 4.3 5.1 4.3 4.0 3.6 3.9 5.3 3.7 4.1 2.7 4.8 6.4 4.4 4.2 4.1 3.5 5.2
##  [847] 4.3 5.0 4.7 4.0 4.8 2.9 3.5 5.6 3.5 4.8 5.2 4.8 4.8 4.5 3.5 4.3 4.5 4.2
##  [865] 4.4 4.5 3.4 3.9 4.3 3.9 4.0 3.8 4.8 4.6 5.9 3.5 4.8 3.4 4.2 6.2 3.9 3.9
##  [883] 2.0 3.7 4.0 4.5 4.3 4.8 4.3 5.1 4.7 3.2 4.4 5.0 4.0 4.0 4.0 5.0 5.5 2.1
##  [901] 3.3 2.9 2.7 5.4 4.1 5.0 4.5 5.1 3.9 3.3 4.6 3.8 3.8 4.3 5.2 4.6 3.8 4.6
##  [919] 4.5 4.6 4.5 4.7 5.0 5.6 3.6 5.6 5.1 4.6 4.8 2.9 3.8 4.6 3.9 2.8 3.6 4.5
##  [937] 2.9 3.6 5.0 3.2 5.6 4.7 4.4 3.4 4.7 4.7 5.7 3.4 5.2 5.2 4.0 4.8 6.2 4.5
##  [955] 4.8 5.1 4.6 3.7 4.8 4.3 4.5 5.1 4.0 6.5 4.1 6.0 4.6 4.8 5.9 5.2 5.0 5.8
##  [973] 3.5 4.7 4.4 5.5 5.1 5.2 3.5 4.5 5.1 4.1 2.8 3.2 3.7 4.4 3.9 3.9 4.6 4.5
##  [991] 5.2 6.3 3.2 2.9 5.0 5.1 2.6 5.7 3.5 4.2
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
##    [1] 5.1 4.5 3.0 3.9 4.8 4.1 4.6 3.6 5.7 4.4 5.0 6.2 5.1 3.6 5.1 4.2 5.3 4.4
##   [19] 4.2 4.7 4.8 6.9 4.8 4.4 4.3 4.3 2.9 4.1 5.5 5.5 5.6 3.8 4.0 4.1 4.5 4.2
##   [37] 5.8 4.2 4.2 4.0 5.0 4.1 3.6 4.4 3.3 4.5 4.3 4.9 5.9 3.7 3.2 4.0 4.9 2.8
##   [55] 4.6 5.5 4.7 4.6 6.2 3.8 4.1 4.5 5.6 5.3 1.9 5.1 4.0 5.1 4.0 3.9 3.8 3.8
##   [73] 4.9 3.6 6.1 3.8 3.0 6.5 5.7 2.9 5.4 3.9 6.4 5.0 5.5 5.9 5.1 4.5 4.7 4.4
##   [91] 3.3 5.4 4.2 3.8 4.7 5.0 4.1 4.6 4.2 3.9 5.0 4.3 4.4 4.4 4.7 6.7 4.3 3.5
##  [109] 7.2 3.8 5.2 4.9 4.5 3.6 4.6 4.3 6.1 2.9 3.9 3.5 4.0 3.9 3.6 6.0 3.8 2.1
##  [127] 3.3 4.3 4.4 4.7 4.1 3.8 3.1 4.0 5.4 5.7 4.3 3.6 5.0 3.4 5.0 5.8 4.6 3.9
##  [145] 3.2 3.5 4.0 3.9 3.7 3.7 3.5 4.1 4.4 6.2 3.8 3.0 4.2 5.2 5.9 3.4 3.5 4.1
##  [163] 4.4 4.6 4.6 4.2 4.7 3.7 4.0 4.9 5.2 4.8 3.8 4.7 5.4 4.3 4.7 5.6 4.9 2.6
##  [181] 6.0 3.1 4.8 5.9 5.1 4.8 5.3 4.4 4.8 4.6 5.1 4.7 5.5 4.1 5.6 4.3 4.2 3.7
##  [199] 5.7 5.1 6.0 5.8 3.8 6.2 4.1 3.4 3.7 5.7 6.2 3.5 4.0 4.7 4.2 5.4 3.7 4.7
##  [217] 4.4 5.2 3.6 3.6 6.7 4.3 4.8 3.9 3.1 4.7 5.1 4.5 6.3 3.0 4.8 4.3 4.1 4.3
##  [235] 3.1 3.7 3.9 3.9 4.5 5.3 5.9 3.2 3.6 5.4 4.0 5.7 3.7 3.6 3.3 4.0 3.9 5.1
##  [253] 3.8 5.8 3.5 2.3 3.2 3.9 4.9 6.1 2.7 4.9 5.6 5.6 4.2 4.9 5.3 4.3 3.6 5.5
##  [271] 5.0 4.2 4.4 4.9 4.0 5.1 3.3 5.5 4.0 4.1 4.8 4.5 5.2 5.2 4.9 5.3 5.0 5.7
##  [289] 3.5 4.9 4.5 3.0 5.1 4.0 2.7 5.2 3.6 5.8 4.8 4.5 2.6 6.2 5.4 5.7 5.0 4.8
##  [307] 2.4 5.9 5.0 4.4 5.1 5.5 4.9 5.5 6.1 5.7 5.2 5.2 6.3 5.4 5.3 4.1 5.6 3.9
##  [325] 4.2 4.5 4.3 4.3 3.6 4.7 6.7 5.9 5.3 5.8 3.7 5.4 3.6 5.5 4.0 3.1 5.1 4.9
##  [343] 4.4 6.0 2.4 4.3 5.5 4.4 4.7 3.9 4.8 4.2 3.8 5.4 4.1 5.2 4.0 5.2 4.1 5.5
##  [361] 6.7 4.6 3.6 6.3 5.2 4.7 4.6 4.2 3.8 3.7 5.2 2.8 4.0 3.3 4.1 5.4 5.5 2.5
##  [379] 5.1 5.5 5.6 5.0 5.2 5.7 4.9 3.4 3.9 4.3 4.4 5.3 4.8 3.6 5.8 5.0 3.3 5.2
##  [397] 3.7 6.9 5.3 4.7 4.2 3.6 5.6 5.1 3.8 3.6 4.2 4.7 4.2 5.7 3.7 4.9 5.4 3.5
##  [415] 4.1 5.0 3.4 4.0 4.5 6.6 3.7 4.1 4.5 4.5 5.7 4.4 4.2 3.4 5.5 4.2 3.1 3.0
##  [433] 4.6 3.0 5.1 3.0 4.9 3.9 2.0 4.2 5.2 5.7 4.7 4.8 5.6 3.4 5.2 4.6 3.6 3.5
##  [451] 5.2 4.9 4.6 4.2 3.5 4.2 4.8 4.7 5.7 4.3 4.8 3.8 3.3 5.1 5.7 3.3 5.0 3.2
##  [469] 3.7 5.9 4.3 6.1 3.8 4.5 2.5 5.0 5.3 4.3 2.9 2.7 7.1 3.4 6.6 3.9 3.8 4.5
##  [487] 6.0 3.9 5.8 5.1 4.4 3.0 4.6 5.0 4.5 5.1 4.6 5.5 3.9 5.3 6.1 5.2 4.7 4.9
##  [505] 4.5 4.3 3.7 4.9 3.9 4.8 4.9 5.4 3.9 5.5 4.2 4.4 4.4 5.2 4.4 4.6 3.1 6.1
##  [523] 5.0 3.5 3.9 4.3 4.0 5.8 4.9 3.7 6.2 5.7 4.5 5.8 3.3 4.5 4.4 3.2 3.5 4.3
##  [541] 4.0 4.2 4.2 5.9 4.3 5.0 4.7 4.9 3.9 5.6 4.8 5.6 4.5 4.2 3.6 5.5 4.3 5.6
##  [559] 4.7 5.6 5.7 5.9 3.1 4.5 6.2 6.0 4.7 3.2 5.3 4.2 3.7 3.8 4.7 4.3 4.8 4.3
##  [577] 4.5 5.3 4.9 4.7 4.6 4.7 5.4 5.4 2.5 4.0 4.3 5.4 4.0 4.0 4.3 4.0 4.6 4.9
##  [595] 5.2 4.3 5.5 4.9 4.9 5.8 5.6 4.4 3.7 4.4 6.3 3.4 4.4 5.1 4.8 2.9 4.1 4.9
##  [613] 4.9 5.6 5.8 5.6 4.1 5.1 3.2 4.8 4.6 3.7 3.7 3.8 3.9 5.4 4.1 3.3 5.2 4.5
##  [631] 5.3 3.3 4.5 3.8 5.5 3.8 3.1 5.1 3.5 4.5 3.8 4.1 5.8 5.8 4.4 3.7 4.1 5.3
##  [649] 5.1 5.0 4.2 3.6 2.2 4.2 5.2 4.3 4.9 4.7 3.1 4.5 4.1 4.5 4.4 4.3 4.5 4.6
##  [667] 3.6 4.4 6.1 4.0 3.0 4.2 5.7 3.6 5.7 4.9 4.4 4.8 5.8 3.4 3.1 3.4 4.0 4.1
##  [685] 5.4 4.9 4.4 4.9 5.8 6.7 4.0 5.6 3.3 5.1 4.6 4.9 3.4 5.6 5.6 4.9 4.6 5.1
##  [703] 4.1 4.7 4.4 4.9 4.9 4.7 4.7 4.3 5.0 4.0 5.5 6.5 4.9 5.3 3.3 4.1 3.6 3.4
##  [721] 4.7 4.6 4.6 5.9 3.7 4.8 4.3 4.1 4.2 4.9 5.4 4.5 4.2 2.6 4.6 4.9 4.4 5.2
##  [739] 5.0 5.4 4.2 3.5 4.4 6.3 4.7 3.2 4.2 5.0 5.5 5.8 5.2 3.5 4.0 4.6 6.2 5.3
##  [757] 5.0 3.8 6.3 3.4 3.4 3.3 4.2 5.6 3.2 3.3 3.2 3.8 4.5 6.7 4.4 3.2 6.0 4.4
##  [775] 4.0 5.1 4.4 5.0 5.8 5.5 3.9 4.9 3.9 5.3 4.0 4.4 4.7 5.2 4.4 4.3 5.5 4.8
##  [793] 5.3 3.6 6.1 4.7 5.0 4.8 3.9 4.8 3.6 4.4 6.3 3.8 4.9 3.3 5.6 5.4 5.0 3.3
##  [811] 4.2 4.5 4.1 4.6 3.4 5.8 2.6 6.1 4.8 5.9 5.8 5.2 5.4 2.3 5.4 5.1 4.5 3.2
##  [829] 3.4 4.9 3.6 4.3 5.5 4.7 2.6 4.2 2.6 4.3 3.7 4.8 5.2 3.9 5.3 4.5 4.8 6.1
##  [847] 5.7 5.5 5.1 5.2 4.0 3.2 3.6 5.0 3.5 4.2 4.1 5.2 3.1 2.7 4.0 3.4 3.2 4.4
##  [865] 4.1 6.1 4.8 6.7 5.5 6.2 5.2 3.3 3.4 3.3 3.0 4.4 4.6 5.4 4.3 3.6 4.3 5.9
##  [883] 4.0 3.8 4.8 4.2 5.5 5.2 5.1 3.1 2.6 5.1 5.4 5.4 4.2 7.0 5.8 5.9 4.9 4.2
##  [901] 4.9 4.1 2.8 3.8 5.1 3.8 5.0 3.9 3.9 4.3 4.3 5.7 3.9 5.5 5.2 4.4 5.0 5.7
##  [919] 5.0 2.6 4.7 5.3 3.7 4.7 5.3 6.6 4.0 5.2 5.3 3.3 4.1 4.8 4.4 4.5 5.9 4.7
##  [937] 3.3 5.5 4.0 5.0 2.4 4.0 3.9 5.3 3.3 3.8 6.8 5.8 4.7 4.3 4.3 4.3 5.6 3.1
##  [955] 6.7 3.8 4.2 3.5 5.5 2.8 4.6 4.1 3.2 5.9 5.5 6.0 3.6 4.7 5.1 4.6 5.0 3.3
##  [973] 3.6 6.2 5.2 4.5 4.3 5.1 5.1 5.2 3.8 3.5 4.1 5.9 3.5 5.1 4.5 4.3 4.8 3.6
##  [991] 4.7 5.0 4.4 3.8 3.3 4.3 4.9 6.0 6.1 4.8
## 
## $func.thetastar
## [1] 0.0491
## 
## $jack.boot.val
##  [1]  0.54915730  0.50872093  0.29495798  0.20554017  0.12671958  0.01170213
##  [7] -0.11752874 -0.23455657 -0.38731988 -0.42809668
## 
## $jack.boot.se
## [1] 0.9858597
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
##    [1] 3.8 4.8 5.4 4.0 5.0 4.8 4.3 5.1 5.1 4.0 4.7 3.5 4.7 4.7 5.3 5.2 4.0 5.7
##   [19] 2.8 4.8 7.6 3.6 5.5 6.4 3.3 5.2 3.8 6.1 4.9 4.8 3.7 4.9 4.2 5.2 4.6 5.0
##   [37] 4.8 5.1 3.1 4.6 2.8 3.7 3.0 3.5 4.3 5.2 4.0 6.2 4.2 6.4 4.8 4.8 4.6 4.4
##   [55] 3.9 4.2 4.1 4.2 5.9 3.8 3.9 4.7 4.3 3.7 3.2 4.2 4.7 5.9 3.8 3.7 3.7 3.4
##   [73] 4.7 2.5 4.3 2.8 4.7 5.6 6.5 4.9 5.1 4.8 4.8 4.6 6.0 4.2 5.9 3.8 3.8 4.2
##   [91] 5.4 5.7 3.8 4.2 4.2 3.7 6.8 5.3 5.6 2.5 5.9 3.6 5.1 4.1 3.7 5.1 2.4 5.0
##  [109] 5.2 3.8 4.5 4.9 5.6 6.3 4.0 3.8 4.2 4.2 5.1 4.1 4.7 5.0 3.3 3.8 4.8 2.8
##  [127] 3.1 4.6 4.7 4.9 4.2 4.5 4.1 4.4 3.9 4.0 3.6 4.6 3.2 4.7 5.0 5.4 4.3 4.4
##  [145] 3.7 4.1 4.9 4.0 5.5 4.1 3.4 5.3 2.7 5.0 4.3 4.3 3.0 4.0 4.4 4.3 4.3 4.3
##  [163] 4.3 5.6 4.4 4.9 5.5 4.8 4.4 4.3 5.1 3.1 3.6 4.5 3.9 4.9 3.4 4.5 5.5 3.5
##  [181] 2.7 4.2 4.7 3.9 3.6 4.1 4.6 5.2 5.3 4.6 4.3 4.3 3.6 4.7 5.1 4.4 4.2 5.3
##  [199] 4.2 4.0 4.6 3.1 3.9 5.0 4.6 5.2 3.8 4.2 4.4 4.0 4.6 3.4 4.8 2.9 5.7 4.5
##  [217] 3.7 4.9 5.0 4.3 2.7 5.5 5.3 5.4 5.1 5.1 5.1 5.6 5.7 4.2 3.3 1.4 4.9 5.4
##  [235] 5.1 4.4 5.1 5.7 4.9 4.1 3.2 4.2 5.4 4.1 4.2 5.5 2.7 3.7 3.2 3.7 4.4 4.4
##  [253] 3.5 5.4 3.4 4.6 4.8 5.5 3.7 3.4 4.7 5.7 3.0 3.5 4.0 4.3 3.3 6.0 5.3 3.8
##  [271] 4.5 5.6 5.5 5.0 3.5 5.2 4.8 3.8 4.4 5.0 4.3 5.0 3.7 4.2 4.5 4.7 4.8 2.5
##  [289] 4.9 4.9 3.0 5.0 4.2 4.9 6.0 4.1 3.3 4.7 5.6 2.8 5.0 5.0 5.2 4.9 4.5 4.0
##  [307] 5.2 5.1 4.3 2.9 4.3 5.1 5.2 2.9 4.6 4.0 4.4 3.6 5.3 3.7 5.4 4.0 5.5 4.6
##  [325] 3.9 4.9 5.1 4.5 5.1 4.0 2.3 5.4 5.3 7.1 3.5 5.8 4.3 2.2 3.4 5.0 5.2 2.2
##  [343] 2.9 4.3 4.1 3.8 4.2 5.8 4.4 4.8 6.3 5.0 4.2 3.3 5.8 3.9 5.0 4.5 4.2 5.8
##  [361] 3.5 4.6 4.9 5.1 5.5 4.5 4.4 4.2 4.9 4.3 6.5 4.2 5.0 4.5 3.7 3.0 4.0 6.6
##  [379] 4.4 3.3 4.2 5.1 5.7 5.9 4.0 4.4 4.2 3.5 4.0 4.7 5.2 5.2 4.4 5.1 4.0 5.2
##  [397] 6.0 5.2 3.5 4.0 3.1 4.5 5.5 4.8 5.9 4.0 3.1 4.1 5.8 5.7 3.1 3.4 4.0 4.5
##  [415] 5.6 4.4 6.2 6.4 4.4 4.5 4.9 4.3 4.9 4.5 4.8 4.1 5.5 4.4 3.8 5.1 5.0 5.3
##  [433] 5.8 4.4 5.4 4.8 3.7 6.4 6.2 4.8 6.1 4.1 3.9 4.1 4.9 4.6 4.6 4.2 5.2 3.0
##  [451] 4.9 5.2 2.9 3.4 3.3 2.7 4.1 5.3 4.1 4.9 4.5 5.7 3.9 6.0 3.8 3.3 3.9 2.1
##  [469] 4.1 4.7 6.0 5.8 4.3 5.6 4.2 4.5 3.9 5.5 4.8 2.7 5.2 5.0 5.1 5.3 4.6 3.8
##  [487] 5.3 4.1 3.9 4.6 4.3 5.1 3.1 4.3 2.0 2.5 4.8 2.9 5.2 4.1 5.2 3.6 4.0 4.9
##  [505] 4.6 4.3 3.5 4.7 7.2 3.8 5.8 4.8 5.8 4.3 5.2 4.5 5.5 5.1 3.7 5.1 3.8 5.7
##  [523] 4.2 3.6 3.8 4.1 5.6 4.6 3.2 3.7 2.9 4.1 4.4 3.9 3.8 4.8 6.7 3.8 5.1 6.0
##  [541] 4.5 5.0 3.4 3.9 5.5 6.2 4.0 4.7 5.1 4.6 4.8 6.0 4.1 3.9 4.2 6.2 5.3 5.7
##  [559] 4.8 4.4 6.0 6.5 3.4 2.4 4.0 4.8 4.8 5.3 4.2 4.8 5.0 5.3 3.9 5.0 3.1 4.2
##  [577] 5.4 4.6 3.5 4.4 4.2 5.9 4.0 4.7 4.8 4.4 4.6 5.8 4.5 3.8 4.1 4.9 4.2 4.9
##  [595] 3.7 3.0 5.4 5.0 3.5 3.1 2.6 3.7 3.1 4.0 5.6 5.3 5.5 5.8 2.4 2.8 5.0 4.5
##  [613] 2.8 5.1 5.0 4.5 3.4 5.2 3.1 5.2 4.5 4.2 4.8 4.0 4.1 4.9 4.9 5.7 3.7 4.5
##  [631] 5.3 3.8 4.8 4.9 4.8 6.6 5.1 6.2 3.6 3.6 4.9 4.4 4.1 5.0 2.7 4.2 5.6 5.0
##  [649] 4.3 4.2 4.7 3.6 4.8 5.1 5.1 4.1 5.0 5.5 5.8 5.3 4.7 5.5 4.3 3.3 5.7 4.9
##  [667] 4.6 4.0 5.0 4.6 5.0 2.3 5.8 3.8 5.3 6.0 4.7 4.7 5.6 5.0 6.1 4.7 4.7 2.5
##  [685] 4.4 4.3 3.2 3.4 3.0 4.7 4.5 3.5 3.9 4.6 5.9 3.5 3.4 5.0 6.1 4.2 5.2 4.2
##  [703] 4.9 2.9 7.0 4.4 3.4 4.1 3.8 3.6 5.2 3.9 3.8 3.5 3.9 4.7 4.2 3.8 4.1 3.3
##  [721] 3.9 5.5 4.0 4.1 3.2 5.4 4.6 4.3 4.3 2.9 5.5 4.2 3.6 5.4 5.4 2.9 3.9 4.6
##  [739] 4.2 4.0 4.4 5.8 6.5 4.3 5.0 5.3 6.3 4.3 5.3 5.3 4.6 5.7 5.5 4.3 3.4 5.7
##  [757] 5.5 5.0 3.9 6.7 3.5 5.5 4.4 5.4 3.3 3.4 4.2 5.7 5.3 5.9 4.8 3.5 4.0 4.1
##  [775] 4.4 5.1 2.8 3.3 4.5 5.8 5.5 4.4 4.5 4.9 5.9 4.6 5.7 4.4 5.0 5.4 4.1 3.5
##  [793] 5.3 4.3 5.5 6.3 2.9 3.7 6.0 4.4 3.2 4.7 3.6 5.1 4.2 3.8 4.5 4.8 4.8 2.9
##  [811] 4.5 4.7 4.8 4.3 4.6 4.3 5.2 5.1 4.3 3.9 5.7 3.1 4.0 5.2 3.9 4.4 4.2 5.1
##  [829] 3.7 3.4 5.6 4.1 4.9 5.7 5.0 5.4 4.4 3.0 4.0 3.6 4.7 5.4 4.0 3.8 5.6 3.2
##  [847] 4.1 2.7 4.8 2.9 3.8 2.6 5.1 4.9 2.8 4.5 5.0 4.2 4.2 3.9 4.5 5.9 2.4 3.3
##  [865] 4.0 3.0 3.7 5.1 5.4 5.5 5.0 5.0 5.0 4.7 5.8 2.8 5.0 5.3 4.3 5.0 3.5 3.6
##  [883] 4.6 5.2 4.3 5.0 4.5 3.5 4.4 5.1 4.5 5.6 5.3 5.6 3.0 4.9 3.4 3.4 4.8 6.4
##  [901] 4.1 3.5 6.7 4.7 4.1 4.2 3.8 4.5 6.2 4.7 5.5 5.7 3.5 4.2 4.9 3.7 4.9 3.5
##  [919] 5.2 5.2 4.7 3.5 4.4 5.6 3.7 3.5 5.8 4.8 4.3 4.8 4.7 6.1 4.5 4.5 2.8 5.5
##  [937] 3.5 6.0 4.8 6.1 5.4 5.3 4.6 4.2 4.7 4.5 4.6 5.1 3.8 4.8 4.2 4.7 4.7 4.4
##  [955] 4.2 4.2 5.4 4.5 5.4 5.5 6.2 6.1 3.8 4.4 4.5 4.7 3.6 4.9 3.7 4.0 5.1 4.9
##  [973] 5.7 4.2 5.1 4.8 4.4 4.3 4.8 4.8 3.4 5.9 3.1 4.4 4.7 3.4 3.2 4.5 4.1 3.5
##  [991] 4.4 4.1 4.8 6.3 4.6 3.4 5.0 4.9 3.2 3.7
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.500 5.300 5.100 5.112 5.000 4.900 4.800 4.700 4.600
## 
## $jack.boot.se
## [1] 0.8514767
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
## [1] 0.5585594
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
##   1.5923620   2.1378449 
##  (0.6511639) (1.0253257)
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
## [1]  0.858518858  0.158605593  1.134333468 -0.427246166  1.843655918
## [6] -0.006099283
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
##    [1]  0.627218175  0.086890386  0.869227619  0.428115636  0.637884091
##    [6]  0.177451238  0.316779516  0.939109391  0.633650437  0.431540382
##   [11]  0.121356150  0.488112545  1.046171419  1.184365869  0.987131732
##   [16]  0.611623076  0.811309366  0.421235279  0.116967951 -0.132933967
##   [21]  0.648274041  0.639491009  0.414652465  1.105637480  0.220872770
##   [26]  0.814861004  0.612798242  1.263443662 -0.003343424  1.386081066
##   [31]  0.276108088  0.593275314  0.090173785  0.486898974  1.254836930
##   [36]  1.118011247  1.088782639  0.053153166  0.538685135  0.320449299
##   [41]  0.192795868  0.748181711  0.979109067  0.119746654 -0.700985488
##   [46]  0.593844440  1.666727815 -0.760344721  0.817567531  1.399888283
##   [51]  0.020869408 -0.320101695  0.485385947  0.038819622  0.968033359
##   [56] -0.391131989  0.409577841  0.406872048  0.120649142  0.778921792
##   [61]  0.297054401  0.183079493  1.064033771  0.775733882  0.281495569
##   [66]  0.121285163  0.434227674  0.174983121  0.767447938  0.946433901
##   [71]  0.230004181  0.551158518 -0.430808294  0.978475949  1.164059925
##   [76] -0.086911060  0.085183286  1.324898405  1.048151922  0.188250519
##   [81] -0.189247865  0.953517425  0.867369009  0.010803848 -0.297751479
##   [86] -0.043394828  0.227185544  0.391096278  0.780044100  0.519521947
##   [91] -0.033273749  0.544842225  0.876256321  0.331104482 -0.757890331
##   [96]  0.349896042  0.402029293  0.724412850  0.189895768 -0.315593043
##  [101]  1.949293045  0.803865001  0.026663749  0.488535886  0.446637233
##  [106]  0.461403833  0.692166104  0.565771163  0.002255582  0.914171440
##  [111]  0.078491105 -0.155813115  0.292977123  0.442583238  0.386191514
##  [116] -0.159263281  0.146845118  1.408472714  0.015021957  0.561788273
##  [121]  0.802976586  0.334513439  0.743974856 -0.058573501  0.915553238
##  [126]  0.260392412  0.631171176  1.106267045  0.565305990  1.324759055
##  [131] -0.160858716  0.647966387  0.094509033  0.505737270  0.862880694
##  [136]  0.703469329  0.498018459  0.132724700  0.333589936  1.473046302
##  [141]  0.312480721  0.349764581 -1.426549331  1.268773313  0.689883476
##  [146]  0.293239382  0.426902125  0.307931802  0.076300259  0.263128424
##  [151]  0.291167840  0.879240241 -0.345850508  0.964677421  0.856480755
##  [156]  0.450045260  0.163527775  0.304207796  0.829339086  0.434254891
##  [161] -0.310756042  0.927162717  0.073578233  0.257012396  0.520635097
##  [166]  0.781203095  1.042504108  0.124177785  0.822375927 -0.084919959
##  [171]  0.739790995 -0.198340069 -0.216301295  1.059761027  0.255285226
##  [176]  0.845985880 -0.091562188  0.549242625 -0.295343246 -0.039296161
##  [181]  0.070142146  0.028895165  0.949529076  0.170816777 -0.092257445
##  [186]  0.040588754 -0.295036066  0.600106124  0.526020676  0.050984746
##  [191]  0.416222085  0.213115295 -0.248314031  0.107508467  0.489024254
##  [196] -0.463235303  0.400099079  0.627051737  0.150490559  0.430316481
##  [201]  0.235479953  1.353609293  0.158817026  0.079567338 -0.235211787
##  [206] -0.099208457  0.101776377 -0.009741197 -0.410588305  0.149179705
##  [211]  0.288923435 -0.050772912 -0.216920995  0.315300810  0.273009424
##  [216]  0.675598605  1.062509744  0.109565987  0.915003090 -0.426775540
##  [221]  0.200127320  0.370941040  0.668432125  0.426658472  0.979107399
##  [226]  0.323263426  0.471889036  0.409577841 -0.299379278  0.462736103
##  [231] -0.041243664  0.290659014 -0.171541456  0.164488496  0.929239508
##  [236]  0.318523101 -1.197117342  0.392320585 -0.244876560  1.774620376
##  [241]  0.178116500 -0.123008096  0.985012602  0.546987224 -0.009603696
##  [246] -0.101215264  0.781199799 -0.202227673  0.420618084 -0.335456188
##  [251]  0.134405632  0.398491046  0.971962111  0.077300590  0.196784842
##  [256]  0.172594726  0.438884811  0.275220367 -0.050553067  1.476938141
##  [261] -0.179128581  0.413261327 -0.282933248 -0.084103769  0.253642283
##  [266]  0.334892821  1.001320324  1.044908408  1.040187751  0.145826867
##  [271]  0.497259098  0.301282500  0.350228050  1.104517480  0.220728666
##  [276]  1.261226873  0.224779824  0.455676395  0.557922524 -0.019743634
##  [281]  0.541828171  0.847400723  0.693239556 -0.310951223  0.255809556
##  [286]  0.270193529  1.362348467  0.662712549  0.471554496  0.624631339
##  [291]  0.103618803  0.704536746  0.164952896  0.344869276  0.721433100
##  [296] -0.188462270  0.658109764  0.455676395 -1.198242199  0.805031394
##  [301] -0.785810955 -0.040422492  0.422150574  0.621960910  0.243836989
##  [306]  0.281396739  0.895292719 -0.370675244  1.116623740 -0.828042027
##  [311]  0.675124034  0.986930303  0.734724857  0.827709706 -0.107997529
##  [316]  1.108739597  0.441959872  0.461317187 -0.044677199 -0.886498758
##  [321]  1.103085756  0.488112545  0.831758363  0.169780374  0.118561911
##  [326]  0.296473581 -0.537050785 -0.115902968  0.370557081  1.002800798
##  [331] -0.055289168  0.686485107  1.122352655  0.737705890  0.684333457
##  [336]  0.195219659 -0.388929404  1.304780260  0.648267743  0.261313866
##  [341]  0.101813228  0.178775289  0.054689540 -0.243661824  0.071649365
##  [346]  0.906259620  0.341458690  1.285926691  0.367638652  1.103085756
##  [351]  0.616426229  0.513698581  0.520309946  0.501177344  0.133752469
##  [356]  0.428959738  0.640629615  0.095319804  0.500998334 -0.069913375
##  [361]  0.239945191  0.840330272  0.849488336  0.195747438  0.059781307
##  [366]  0.511529672  1.408472714  0.287468805 -0.388731629  1.375999595
##  [371]  0.510623039  0.690018018  0.181724797 -0.512868569 -0.306717742
##  [376]  0.490107347  0.881712328 -0.233336777  0.417221932  0.028989692
##  [381]  0.412196400  0.612751261  0.709292724  0.297399120  0.455439277
##  [386]  0.177489290  0.561559305  0.029126354 -0.416358210  0.106998045
##  [391]  0.337393491  0.534373947  0.155702518 -0.864053429  0.303923352
##  [396]  0.423856485  0.627633382  1.271998176  0.731013816  0.690264404
##  [401]  0.521226839  1.307615271 -0.250388148  0.389014726  0.134195055
##  [406]  0.834120186  0.623759445  0.633804970  0.302323321  0.597359655
##  [411]  1.651694098  0.013477005  0.626354223  0.227881653  0.684274480
##  [416]  1.144787413 -0.045557418  1.043573040  0.500292597  0.767226844
##  [421]  0.471889036  0.608082350  0.682359077  0.288824651  0.226757998
##  [426]  0.648148520 -0.175749623  0.253559298  0.072060766  1.143259301
##  [431]  0.658511681 -0.005782987 -0.113641433 -0.143017685  0.231892088
##  [436]  0.261484712  0.407066303  0.286303202 -0.656380314  0.598376079
##  [441]  1.082615711  0.304992747  0.627157384  0.458080857 -0.130411660
##  [446]  0.385553220  0.727984770 -0.413392589  1.129630116  0.654987746
##  [451] -0.396200644  0.124751564  0.721791606  0.619329006  0.153706412
##  [456]  0.421332586  0.401087902 -0.060786732  0.384989316  0.186191650
##  [461]  0.255260285 -0.219196399  0.304207796  0.656107001  0.642065648
##  [466]  0.364583747 -0.168381129  0.207849783  1.076249359  0.280858983
##  [471]  0.516766563  0.547195959  0.153989294  0.835199140  0.184296554
##  [476]  0.474155751 -0.026434378  0.208681363  0.647517157  0.445872727
##  [481]  0.262412495  0.843147116 -0.292976309 -0.409761068 -0.388359672
##  [486]  0.824012089  0.445054380 -0.127745656 -0.229788291  0.909197758
##  [491]  0.381721085  0.762961908  2.102785173  0.518657409 -0.029944734
##  [496]  0.192708115  2.426406653  0.198975747  0.460531938  0.664803636
##  [501]  1.210275760  1.113864444 -0.077712228  0.481514369  0.396062517
##  [506]  0.900442281  0.113647924  0.557306651  0.140311192 -0.353757148
##  [511] -0.204961158  0.949617885  0.189823507 -0.174991494  0.709072303
##  [516] -0.520979335  0.562162363  0.595169999  0.573525565  0.525743778
##  [521]  1.358247304 -0.082631073  0.572271636 -0.011615747  1.162417831
##  [526] -0.265682709 -0.369066383  0.188374006 -0.276806936  0.796774682
##  [531]  0.713196871  0.644899057  0.452163351  0.747245300  0.475158407
##  [536]  0.733160692  0.193643400  0.805498305 -0.827729268  0.087135565
##  [541]  0.164224426 -0.270139588  0.348918209  0.432750862  0.920707889
##  [546]  0.286220539  0.226757998  0.640757001  0.527541648  1.057396867
##  [551] -0.531357814  0.211835377  0.121259804 -0.158385369  0.422889075
##  [556] -0.011634110  0.058758436  0.446197682  0.810461468  0.448652062
##  [561] -0.038655253  0.470701216  0.889482885  0.045461547  0.409661954
##  [566]  0.668298849 -0.003756536 -1.111225321  1.128792797  0.168681970
##  [571]  0.488749018  0.497484328  0.207786295  0.112787553  0.428051640
##  [576]  1.017527186 -0.148949766  0.488998015 -0.029251344  0.214674735
##  [581]  0.387792713  0.750949543  0.247916857  0.424112862  0.272530119
##  [586]  1.304510238  0.393561431  0.381552485  0.740173310  0.652781303
##  [591]  0.698647908  0.274198924  0.496814863 -0.538192875 -0.193048082
##  [596] -0.423227177  0.623324023 -0.131371106  0.750165768  0.072060766
##  [601]  0.556476309  0.710660171  0.265297314  0.285771560 -0.289088214
##  [606]  0.142737020 -0.046515604  0.711512742 -0.440599998  0.516995996
##  [611]  0.745519257  0.261216907 -0.033722044  1.448252840  1.433427628
##  [616]  0.627042162  0.614539265  0.667161674  0.294623715  0.146922994
##  [621]  0.125302434  0.015605502  0.599445960  0.495720357  0.878284346
##  [626]  0.814528252 -0.022286308  1.143305015  0.331456953  0.527760449
##  [631]  1.379498436  0.770395240  0.128334693 -0.177299579  0.573039207
##  [636]  0.315399782  0.669430436 -0.060234600  0.272554875  1.238987480
##  [641]  0.838339855  0.929941035  1.169443878  0.315497121  0.614539265
##  [646] -0.826846326 -0.603441838 -0.064201785  0.816316580  1.158529280
##  [651]  0.163346519  0.157610767  0.751599465 -0.374514994  0.192550667
##  [656]  0.401443761  0.302125908  0.081448286  0.375831032 -0.017065930
##  [661]  0.507889216  0.738038643  0.702473595 -0.302216486  0.809369734
##  [666]  0.498927575  0.127137801  1.006832252  0.951572867  0.126240377
##  [671] -0.115132966  0.711585863  1.051540241  0.264951863  0.619719625
##  [676]  0.225114665  0.487838781 -0.673012763  0.114879645  0.267101231
##  [681] -0.110599745  0.093714255  1.055324786  0.267420622  0.759006043
##  [686] -0.249846560  0.512416557 -0.008437074 -0.004922382  0.084858818
##  [691]  0.493478633  0.825888378  1.470047773  0.499596227  0.448942854
##  [696]  0.071658507 -0.388997100  0.400255881  1.013252433  0.665942390
##  [701]  1.045208093 -0.022980008  0.247211217  0.494280648  0.679549496
##  [706]  0.591725311  0.734154850  0.630628269  1.372596563 -0.426912421
##  [711]  0.052345209  0.214363903  0.461290071  0.224779824  0.208368679
##  [716]  0.853775410  0.535096373  0.953223720  0.207744722  0.861946432
##  [721] -0.017794387  0.869479299  0.819498791  1.091948322  0.881032634
##  [726]  0.918805585  1.271226836  0.152222865  0.100589082  0.101726672
##  [731]  1.352781649  1.254938404 -0.018178117  0.720752610  0.368749224
##  [736]  0.632533915 -0.041920396 -0.134325787  0.073106070  0.730743895
##  [741] -0.322430131  0.103952220  0.215855454 -0.004762626  0.284163417
##  [746]  0.084815097  0.182962139  0.655082097  0.402834754  0.538985960
##  [751]  0.462760367  0.389378288  0.376579592 -0.061385678  1.170709941
##  [756]  0.160349543  0.304174162  0.612282464  0.629320961  0.096407038
##  [761]  0.642778825 -0.082660188  0.213856977  0.257224859  0.534101700
##  [766]  0.188205840  0.404022477  0.357976627  0.357485604  0.482676820
##  [771]  1.129630116  0.159566102  0.581936678  0.678467032  0.118743135
##  [776]  1.386176045  0.109836731 -0.063466941  0.577998756  0.572568805
##  [781]  0.174691183 -0.562039487 -0.489473849  0.495570696  0.156135199
##  [786]  0.943919175  0.700608617 -0.605016943  0.572403754  0.363470497
##  [791]  0.890181932  1.299408995  0.390547019 -0.182226957  0.638656746
##  [796]  0.939211240  0.382478046  0.579195470  1.408472714  1.492401724
##  [801]  0.350410958  0.912282261  0.511479848  0.790579835  0.750727499
##  [806]  0.084240564 -0.349003015  0.927798890 -0.944419169  0.236378342
##  [811]  0.210032107  0.359068905 -0.483361193 -0.563781570 -0.253050448
##  [816]  0.405049196 -0.167150884  0.697783462 -0.092843577  0.546026211
##  [821]  0.171679463  0.544654640  0.314422437  0.380467968  0.047416039
##  [826]  0.281964489  0.505801242 -0.191267536  0.116452015 -0.256438526
##  [831]  0.519103781  0.267274712  0.892998698  0.515786867  0.126098586
##  [836]  1.909349893 -0.222951232  0.913123082  0.757575552  0.480146823
##  [841]  0.194092535  1.227504875 -0.175099261 -0.071110330  0.232298030
##  [846]  0.347174528  0.444573941  0.206039559  0.306179634  0.353407623
##  [851]  0.281495569 -0.241220356 -0.071138669  0.578148754  0.725156214
##  [856]  0.829381298  0.347924398  0.037125165  0.268314461  1.645825383
##  [861]  0.256225807  1.118858458  0.578916878  0.359562851  0.886313210
##  [866]  0.478065281 -0.035399345  0.434227674 -0.441241519  0.793886081
##  [871]  0.157911092  0.237162982  1.560003000  0.148602147 -0.030565452
##  [876]  1.087798671  1.036467274 -0.099164433 -0.007804907 -0.051061950
##  [881]  0.050315185  0.358943054  0.709847479  0.546596953  0.501039198
##  [886] -0.008257405 -0.151839134  1.184869138  0.494280648  0.097862433
##  [891]  0.393041896  1.127703246  0.460703620 -0.208752011  0.469648698
##  [896] -0.064140221  0.380593544 -0.687069900  0.977390191  0.219032536
##  [901] -0.008529594  0.332144015 -0.853708218  0.410401406  0.422009826
##  [906]  0.476363496  0.005215683  0.339142404  0.507889216  0.874209930
##  [911] -0.292330842  0.094505279 -0.323071638 -0.021372721  0.431394851
##  [916] -0.299595064  0.732665642 -0.517133029  0.318007051 -0.458073293
##  [921]  0.054305148  0.814654758  0.385065248  1.048426138 -0.031392170
##  [926]  0.140992502  1.055865450  0.709064197  0.396003796  0.557272094
##  [931] -0.075614601  0.359008643  0.677008517  0.286713403  0.832199544
##  [936]  1.453276325  0.592218761  0.021643541 -0.186205182  0.153989294
##  [941]  0.606905194  1.054335125  1.014305161 -0.102120083  0.466390753
##  [946]  0.272393760 -0.208779931  0.311437048 -0.416264462  0.479688077
##  [951]  0.350001384  0.257516662  0.579243486  0.005081938  0.690594459
##  [956] -0.018126687  0.008224043  0.201086072  0.454547999 -0.935135278
##  [961]  0.478953554  0.226390706 -0.178517867  0.702663800  0.338155344
##  [966]  1.710067338  0.642708695  0.448560855  0.623861974  1.265043170
##  [971]  1.004900250 -0.718553197  0.625449641  0.687986585  0.577568797
##  [976]  0.746463800 -0.171861447  0.641954010  0.419454494 -0.512860543
##  [981]  0.172544413  1.799110082 -0.163611896  0.609240048  0.233340051
##  [986] -0.076518958  0.580794371 -0.747313549  0.228626721  1.030712558
##  [991]  0.648344326 -0.093855682 -0.404575594 -0.391796457  0.296582870
##  [996]  0.477336809  1.003944107  0.420294882  1.478132239 -0.050277480
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
##      mean         sd    
##   0.7448442   0.5277087 
##  (0.1668761) (0.1179975)
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
## [1] -0.325332856 -0.454701303  0.134072520  0.674885575 -0.516149815
## [6] -0.002406158
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
## [1] -0.0928
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9089303
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
## t1*      4.5 -0.007007007   0.9044152
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 4 6 7 9 
## 2 1 1 1 2 2 1
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
## [1] 0.001
```

```r
se.boot
```

```
## [1] 0.8660306
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

