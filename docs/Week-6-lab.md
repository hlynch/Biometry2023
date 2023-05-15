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
##       [,1]      [,2]       [,3]      [,4]       [,5]      [,6]      [,7]
##  [1,]   NA 0.1432913 0.02747484 0.1149197 0.03340216 0.4638126 0.4932202
##  [2,]   NA        NA 0.25952853 0.8290895 0.54525732 0.5016386 0.6199275
##  [3,]   NA        NA         NA 0.3633728 0.40949445 0.1126986 0.1802694
##  [4,]   NA        NA         NA        NA 0.76524031 0.4057583 0.5202274
##  [5,]   NA        NA         NA        NA         NA 0.2051553 0.3461090
##  [6,]   NA        NA         NA        NA         NA        NA 0.9505012
##  [7,]   NA        NA         NA        NA         NA        NA        NA
##  [8,]   NA        NA         NA        NA         NA        NA        NA
##  [9,]   NA        NA         NA        NA         NA        NA        NA
## [10,]   NA        NA         NA        NA         NA        NA        NA
##            [,8]      [,9]     [,10]
##  [1,] 0.6525700 0.2306572 0.2322332
##  [2,] 0.4242505 0.9056358 0.9065631
##  [3,] 0.1054658 0.2610682 0.2627959
##  [4,] 0.3480705 0.7616714 0.7630710
##  [5,] 0.1965660 0.5289849 0.5319006
##  [6,] 0.8413463 0.6227613 0.6236488
##  [7,] 0.8154552 0.7157340 0.7161010
##  [8,]        NA 0.5246565 0.5255456
##  [9,]        NA        NA 0.9995408
## [10,]        NA        NA        NA
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
##          5% 
## 0.001748475
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
