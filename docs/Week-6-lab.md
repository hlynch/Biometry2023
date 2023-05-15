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
##  [1,]   NA 0.4875846 0.3418530 0.17308491 0.4450920 0.9041752 0.7631657
##  [2,]   NA        NA 0.1889814 0.83666806 0.8959531 0.6425554 0.6340783
##  [3,]   NA        NA        NA 0.03128448 0.1165039 0.4379116 0.2351476
##  [4,]   NA        NA        NA         NA 0.6329075 0.4356092 0.3154464
##  [5,]   NA        NA        NA         NA        NA 0.6732055 0.6450185
##  [6,]   NA        NA        NA         NA        NA        NA 0.9292374
##  [7,]   NA        NA        NA         NA        NA        NA        NA
##  [8,]   NA        NA        NA         NA        NA        NA        NA
##  [9,]   NA        NA        NA         NA        NA        NA        NA
## [10,]   NA        NA        NA         NA        NA        NA        NA
##              [,8]      [,9]      [,10]
##  [1,] 0.011004341 0.4324456 0.14741499
##  [2,] 0.248486051 0.9260155 0.64367009
##  [3,] 0.001895468 0.1176569 0.03486578
##  [4,] 0.140048147 0.6829335 0.69859516
##  [5,] 0.084683847 0.9629674 0.45617178
##  [6,] 0.097360245 0.6530268 0.33028245
##  [7,] 0.027262690 0.6220553 0.24161128
##  [8,]          NA 0.1048520 0.40527552
##  [9,]          NA        NA 0.49402027
## [10,]          NA        NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 5
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
## 0.001950759
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 1
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
