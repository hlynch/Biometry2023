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
##       [,1]      [,2]      [,3]      [,4]      [,5]      [,6]       [,7]
##  [1,]   NA 0.9460375 0.9107938 0.6075560 0.8265793 0.9169243 0.14997501
##  [2,]   NA        NA 0.8626203 0.6371354 0.8683702 0.9588662 0.11304423
##  [3,]   NA        NA        NA 0.5781913 0.7662021 0.8482388 0.27233144
##  [4,]   NA        NA        NA        NA 0.7813877 0.7290007 0.07492297
##  [5,]   NA        NA        NA        NA        NA 0.9280131 0.12949097
##  [6,]   NA        NA        NA        NA        NA        NA 0.20120373
##  [7,]   NA        NA        NA        NA        NA        NA         NA
##  [8,]   NA        NA        NA        NA        NA        NA         NA
##  [9,]   NA        NA        NA        NA        NA        NA         NA
## [10,]   NA        NA        NA        NA        NA        NA         NA
##            [,8]      [,9]      [,10]
##  [1,] 0.6827715 0.6927898 0.49400621
##  [2,] 0.6128203 0.6117545 0.51668628
##  [3,] 0.8240946 0.8491004 0.49339102
##  [4,] 0.3636119 0.3517669 0.97714622
##  [5,] 0.5468764 0.5470532 0.71347689
##  [6,] 0.6584205 0.6702791 0.66483967
##  [7,] 0.2343820 0.1537930 0.01785955
##  [8,]        NA 0.9437625 0.21060310
##  [9,]        NA        NA 0.16218872
## [10,]        NA        NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 1
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
## [1] 0
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
##          5% 
## 0.001963611
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
