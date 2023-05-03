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
##       [,1]      [,2]        [,3]       [,4]        [,5]       [,6]       [,7]
##  [1,]   NA 0.9683769 0.002333992 0.22826961 0.834078364 0.02178405 0.03024542
##  [2,]   NA        NA 0.001625628 0.20326531 0.859527511 0.01735018 0.02419194
##  [3,]   NA        NA          NA 0.03382694 0.002950486 0.36637158 0.24846836
##  [4,]   NA        NA          NA         NA 0.186529798 0.21354900 0.28548451
##  [5,]   NA        NA          NA         NA          NA 0.02070400 0.02830301
##  [6,]   NA        NA          NA         NA          NA         NA 0.82754874
##  [7,]   NA        NA          NA         NA          NA         NA         NA
##  [8,]   NA        NA          NA         NA          NA         NA         NA
##  [9,]   NA        NA          NA         NA          NA         NA         NA
## [10,]   NA        NA          NA         NA          NA         NA         NA
##             [,8]        [,9]     [,10]
##  [1,] 0.35259562 0.629116516 0.2155975
##  [2,] 0.32833206 0.588848445 0.1947444
##  [3,] 0.06933555 0.002568683 0.0782807
##  [4,] 0.92635522 0.392919100 0.8799439
##  [5,] 0.28921875 0.504413731 0.1765550
##  [6,] 0.26179677 0.035056104 0.3272493
##  [7,] 0.33166071 0.049505017 0.4174426
##  [8,]         NA 0.549154912 0.8294466
##  [9,]         NA          NA 0.3590867
## [10,]         NA          NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 13
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
## 0.001786721
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 1
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
