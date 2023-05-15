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
##       [,1]      [,2]      [,3]        [,4]       [,5]      [,6]       [,7]
##  [1,]   NA 0.8614014 0.5534775 0.014383381 0.26628673 0.4522989 0.67262324
##  [2,]   NA        NA 0.6087820 0.005376304 0.13588154 0.3325200 0.76109077
##  [3,]   NA        NA        NA 0.007185656 0.11748220 0.2290806 0.73150028
##  [4,]   NA        NA        NA          NA 0.08589732 0.1153557 0.00280046
##  [5,]   NA        NA        NA          NA         NA 0.8701982 0.06410619
##  [6,]   NA        NA        NA          NA         NA        NA 0.23348690
##  [7,]   NA        NA        NA          NA         NA        NA         NA
##  [8,]   NA        NA        NA          NA         NA        NA         NA
##  [9,]   NA        NA        NA          NA         NA        NA         NA
## [10,]   NA        NA        NA          NA         NA        NA         NA
##             [,8]       [,9]     [,10]
##  [1,] 0.91093321 0.37791442 0.2740871
##  [2,] 0.96563601 0.22841634 0.1706603
##  [3,] 0.62574282 0.17169335 0.1281425
##  [4,] 0.01215232 0.06564891 0.1582908
##  [5,] 0.22636545 0.82554359 0.8856384
##  [6,] 0.40103889 0.98799641 0.7904262
##  [7,] 0.78186880 0.12627745 0.1041724
##  [8,]         NA 0.32583468 0.2374191
##  [9,]         NA         NA 0.7434019
## [10,]         NA         NA        NA
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
## 0.001837583
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
