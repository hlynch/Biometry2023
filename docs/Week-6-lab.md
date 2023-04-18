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
##       [,1]      [,2]      [,3]       [,4]       [,5]        [,6]       [,7]
##  [1,]   NA 0.7846873 0.6043401 0.12485796 0.09017907 0.180913032 0.83559998
##  [2,]   NA        NA 0.4376842 0.06640635 0.04595455 0.273606902 0.94509606
##  [3,]   NA        NA        NA 0.37410572 0.29851993 0.083483186 0.47226662
##  [4,]   NA        NA        NA         NA 0.85580390 0.004062084 0.07462104
##  [5,]   NA        NA        NA         NA         NA 0.002497899 0.05176553
##  [6,]   NA        NA        NA         NA         NA          NA 0.24193553
##  [7,]   NA        NA        NA         NA         NA          NA         NA
##  [8,]   NA        NA        NA         NA         NA          NA         NA
##  [9,]   NA        NA        NA         NA         NA          NA         NA
## [10,]   NA        NA        NA         NA         NA          NA         NA
##            [,8]       [,9]     [,10]
##  [1,] 0.2853778 0.57801441 0.5548304
##  [2,] 0.1637580 0.42589540 0.4193102
##  [3,] 0.6855982 0.94546341 0.8860103
##  [4,] 0.5138713 0.45335634 0.5548432
##  [5,] 0.3945529 0.37381098 0.4738635
##  [6,] 0.0107580 0.09335496 0.1096100
##  [7,] 0.1832647 0.45762027 0.4478820
##  [8,]        NA 0.76917008 0.8634538
##  [9,]        NA         NA 0.9386791
## [10,]        NA         NA        NA
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
## 0.001908634
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
