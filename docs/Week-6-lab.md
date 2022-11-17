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
##       [,1]      [,2]      [,3]       [,4]       [,5]       [,6]       [,7]
##  [1,]   NA 0.5350694 0.7807236 0.12291075 0.01910005 0.42753771 0.46588362
##  [2,]   NA        NA 0.6262206 0.21426827 0.02704164 0.79463665 0.91333362
##  [3,]   NA        NA        NA 0.09261259 0.01105591 0.47529167 0.51186693
##  [4,]   NA        NA        NA         NA 0.24280783 0.34644403 0.21516128
##  [5,]   NA        NA        NA         NA         NA 0.05109609 0.02573167
##  [6,]   NA        NA        NA         NA         NA         NA 0.85500237
##  [7,]   NA        NA        NA         NA         NA         NA         NA
##  [8,]   NA        NA        NA         NA         NA         NA         NA
##  [9,]   NA        NA        NA         NA         NA         NA         NA
## [10,]   NA        NA        NA         NA         NA         NA         NA
##            [,8]      [,9]      [,10]
##  [1,] 0.2225312 0.2698139 0.49754026
##  [2,] 0.3940432 0.4649742 0.84565362
##  [3,] 0.2305233 0.2941997 0.58061474
##  [4,] 0.8754791 0.8400310 0.42230017
##  [5,] 0.2502893 0.2574460 0.08532951
##  [6,] 0.5309021 0.5985056 0.98796602
##  [7,] 0.4137516 0.4896392 0.89904983
##  [8,]        NA 0.9601497 0.57233499
##  [9,]        NA        NA 0.62887172
## [10,]        NA        NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 4
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
## 0.002196735
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
