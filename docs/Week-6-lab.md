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
##       [,1]      [,2]      [,3]       [,4]      [,5]       [,6]       [,7]
##  [1,]   NA 0.4934452 0.3346802 0.28844311 0.4881007 0.63186284 0.57406172
##  [2,]   NA        NA 0.1438605 0.08796659 0.1737314 0.77562103 0.77615182
##  [3,]   NA        NA        NA 0.77605226 0.6196893 0.17502474 0.14796995
##  [4,]   NA        NA        NA         NA 0.7088971 0.08407462 0.03676328
##  [5,]   NA        NA        NA         NA        NA 0.20236422 0.13673071
##  [6,]   NA        NA        NA         NA        NA         NA 0.97311922
##  [7,]   NA        NA        NA         NA        NA         NA         NA
##  [8,]   NA        NA        NA         NA        NA         NA         NA
##  [9,]   NA        NA        NA         NA        NA         NA         NA
## [10,]   NA        NA        NA         NA        NA         NA         NA
##            [,8]       [,9]      [,10]
##  [1,] 0.5286458 0.17819633 0.36929202
##  [2,] 0.2430635 0.06606597 0.11845418
##  [3,] 0.7496343 0.78251133 0.69679865
##  [4,] 0.9007700 0.49549094 0.84575259
##  [5,] 0.9071609 0.37672601 0.84720688
##  [6,] 0.3007988 0.07606112 0.12426467
##  [7,] 0.2628260 0.05798552 0.06591856
##  [8,]        NA 0.53672078 0.99276504
##  [9,]        NA         NA 0.43089467
## [10,]        NA         NA         NA
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
## 0.001943309
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
