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
##       [,1]      [,2]      [,3]       [,4]      [,5]      [,6]      [,7]
##  [1,]   NA 0.1979987 0.2379463 0.71401377 0.8049936 0.4411493 0.8358035
##  [2,]   NA        NA 0.9043691 0.03322582 0.1951905 0.4444312 0.1987332
##  [3,]   NA        NA        NA 0.05024695 0.2495210 0.5394043 0.2504788
##  [4,]   NA        NA        NA         NA 0.4442431 0.1327150 0.4878039
##  [5,]   NA        NA        NA         NA        NA 0.5208424 0.9675742
##  [6,]   NA        NA        NA         NA        NA        NA 0.5098212
##  [7,]   NA        NA        NA         NA        NA        NA        NA
##  [8,]   NA        NA        NA         NA        NA        NA        NA
##  [9,]   NA        NA        NA         NA        NA        NA        NA
## [10,]   NA        NA        NA         NA        NA        NA        NA
##            [,8]      [,9]     [,10]
##  [1,] 0.5480082 0.6407341 0.3449574
##  [2,] 0.3493915 0.2520457 0.7197405
##  [3,] 0.4309120 0.3235907 0.8091437
##  [4,] 0.2101435 0.2713459 0.1106222
##  [5,] 0.6681262 0.7951663 0.3955928
##  [6,] 0.8307775 0.6764307 0.7561440
##  [7,] 0.6493036 0.7697818 0.3893524
##  [8,]        NA 0.8490233 0.6274392
##  [9,]        NA        NA 0.5063505
## [10,]        NA        NA        NA
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
## 0.001915972
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
