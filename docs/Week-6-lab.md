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
##       [,1]     [,2]       [,3]       [,4]      [,5]      [,6]      [,7]
##  [1,]   NA 0.162472 0.95741553 0.11670919 0.6866210 0.4008876 0.2445330
##  [2,]   NA       NA 0.08255402 0.98728644 0.3312385 0.5069605 0.9651420
##  [3,]   NA       NA         NA 0.03152211 0.5924662 0.2664332 0.1734254
##  [4,]   NA       NA         NA         NA 0.2780498 0.4449278 0.9717616
##  [5,]   NA       NA         NA         NA        NA 0.6942329 0.4236180
##  [6,]   NA       NA         NA         NA        NA        NA 0.6079449
##  [7,]   NA       NA         NA         NA        NA        NA        NA
##  [8,]   NA       NA         NA         NA        NA        NA        NA
##  [9,]   NA       NA         NA         NA        NA        NA        NA
## [10,]   NA       NA         NA         NA        NA        NA        NA
##            [,8]       [,9]     [,10]
##  [1,] 0.8824922 0.73293777 0.7481639
##  [2,] 0.2237929 0.04395005 0.2064917
##  [3,] 0.8191635 0.67846129 0.6279568
##  [4,] 0.1752972 0.01164080 0.1348682
##  [5,] 0.8033523 0.41517171 0.8888397
##  [6,] 0.5093109 0.15153839 0.5358738
##  [7,] 0.3097083 0.11324677 0.3160061
##  [8,]        NA 0.61292799 0.8844849
##  [9,]        NA         NA 0.3984744
## [10,]        NA         NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 3
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
## 0.001816858
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
