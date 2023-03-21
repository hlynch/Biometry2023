Week 6 Lab
=============
  
Today we will do a short exercise to illustrate the permutation method of dealing with multiple comparisons.

First, we will simulate 10 groups of data (say, heights) from the *same* distribution using the normal distribution and put the data for each group in a list for easy access:


```r
data.all<-list()
for (i in 1:10)
  {
    data.all[[i]]<-rnorm(10)  #Note the double brackets for a list
  }
```

Now we can compare each group pairwise using a t.test.


```r
p.values<-matrix(ncol=10,nrow=10)
for (i in 1:9)
  {
    for (j in (i+1):10)
      {
        p.values[i,j]<-t.test(data.all[[i]],data.all[[j]])$p.value 
      }
  }
p.values
```

```
##       [,1]     [,2]        [,3]       [,4]       [,5]       [,6]      [,7]
##  [1,]   NA 0.253881 0.001121258 0.01935624 0.32839927 0.05227409 0.1766586
##  [2,]   NA       NA 0.006137106 0.09880573 0.92801617 0.29310008 0.5183442
##  [3,]   NA       NA          NA 0.31121617 0.02693443 0.05440874 0.1423989
##  [4,]   NA       NA          NA         NA 0.19319856 0.42729681 0.5201500
##  [5,]   NA       NA          NA         NA         NA 0.47189273 0.6162083
##  [6,]   NA       NA          NA         NA         NA         NA 0.9565833
##  [7,]   NA       NA          NA         NA         NA         NA        NA
##  [8,]   NA       NA          NA         NA         NA         NA        NA
##  [9,]   NA       NA          NA         NA         NA         NA        NA
## [10,]   NA       NA          NA         NA         NA         NA        NA
##             [,8]       [,9]      [,10]
##  [1,] 0.27234596 0.15705315 0.04396865
##  [2,] 0.90095073 0.66531386 0.26333426
##  [3,] 0.01697108 0.02139776 0.05053868
##  [4,] 0.16394982 0.21708369 0.42624553
##  [5,] 0.98560377 0.80251093 0.45127775
##  [6,] 0.43519308 0.57695870 0.98209182
##  [7,] 0.60623988 0.73599251 0.94385822
##  [8,]         NA 0.79761220 0.41072425
##  [9,]         NA         NA 0.54957579
## [10,]         NA         NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 7
```

We could correct this using the Bonferonni method:


```r
k<-45
new.threshold.B<-0.05/k
new.threshold.B
```

```
## [1] 0.001111111
```

```r
false.positives.B<-sum(p.values<new.threshold.B,na.rm=T)
false.positives.B
```

```
## [1] 0
```

We could correct this using the Dunn-Sidak method:


```r
k<-45
new.threshold.DS<-1-((1-0.05)^(1/k))
new.threshold.DS
```

```
## [1] 0.001139202
```

```r
false.positives.DS<-sum(p.values<new.threshold.DS,na.rm=T)
false.positives.DS
```

```
## [1] 1
```

We could correct this using the randomization method. This requires simulating data under the null hypothesis to generate a null distribution of p-values.



```r
p.values.all<-c()
min.p.values.all<-c()
for (k in 1:1000)
  {
    data.null<-list()
    for (i in 1:10)
      {
        data.null[[i]]<-rnorm(10)  #Note the double brackets for a list
      }
    p.values.null<-matrix(ncol=10,nrow=10)
    for (i in 1:9)
      {
        for (j in (i+1):10)
          {
            p.values.null[i,j]<-t.test(data.null[[i]],data.null[[j]])$p.value 
          }
      }
    p.values.all<-c(p.values.all,c(p.values.null)[!is.na(c(p.values.null))])
    min.p.values.all<-c(min.p.values.all,min(c(p.values.null)[!is.na(c(p.values.null))]))
  }
new.threshold.R<-quantile(min.p.values.all,probs=c(0.05))
new.threshold.R
```

```
##         5% 
## 0.00142655
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 1
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
