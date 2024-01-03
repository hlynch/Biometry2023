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
##       [,1]      [,2]       [,3]      [,4]       [,5]      [,6]       [,7]
##  [1,]   NA 0.3076695 0.05598113 0.2791358 0.09190265 0.4757471 0.01193036
##  [2,]   NA        NA 0.40894678 0.9544142 0.51557210 0.8899095 0.19363588
##  [3,]   NA        NA         NA 0.4415828 0.87838403 0.4024029 0.66432485
##  [4,]   NA        NA         NA        NA 0.55175288 0.8512515 0.21413820
##  [5,]   NA        NA         NA        NA         NA 0.4884610 0.56338425
##  [6,]   NA        NA         NA        NA         NA        NA 0.22746679
##  [7,]   NA        NA         NA        NA         NA        NA         NA
##  [8,]   NA        NA         NA        NA         NA        NA         NA
##  [9,]   NA        NA         NA        NA         NA        NA         NA
## [10,]   NA        NA         NA        NA         NA        NA         NA
##             [,8]       [,9]      [,10]
##  [1,] 0.08643052 0.02676403 0.05095332
##  [2,] 0.50699476 0.23218569 0.34782100
##  [3,] 0.88107241 0.66284151 0.86712724
##  [4,] 0.54327125 0.25303131 0.37559310
##  [5,] 0.99573569 0.57310533 0.76021048
##  [6,] 0.48189175 0.24812425 0.34742493
##  [7,] 0.56050260 0.93768594 0.82873934
##  [8,]         NA 0.57172140 0.76128507
##  [9,]         NA         NA 0.80068595
## [10,]         NA         NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 2
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
##        5% 
## 0.0015234
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
