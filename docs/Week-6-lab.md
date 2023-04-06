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
##       [,1]      [,2]       [,3]       [,4]      [,5]      [,6]      [,7]
##  [1,]   NA 0.1709371 0.06711563 0.57658834 0.4156747 0.3209798 0.2710906
##  [2,]   NA        NA 0.59214816 0.06071145 0.4445889 0.5862503 0.5935862
##  [3,]   NA        NA         NA 0.02165050 0.1865130 0.2694430 0.2608604
##  [4,]   NA        NA         NA         NA 0.1542980 0.1145716 0.0860370
##  [5,]   NA        NA         NA         NA        NA 0.8095868 0.7492621
##  [6,]   NA        NA         NA         NA        NA        NA 0.9574847
##  [7,]   NA        NA         NA         NA        NA        NA        NA
##  [8,]   NA        NA         NA         NA        NA        NA        NA
##  [9,]   NA        NA         NA         NA        NA        NA        NA
## [10,]   NA        NA         NA         NA        NA        NA        NA
##             [,8]       [,9]      [,10]
##  [1,] 0.07317535 0.19717006 0.07098122
##  [2,] 0.74083978 0.82884182 0.71112512
##  [3,] 0.79395236 0.43047106 0.83092884
##  [4,] 0.02042102 0.06420602 0.02017867
##  [5,] 0.21858669 0.54029542 0.21006429
##  [6,] 0.33039496 0.71469642 0.31600144
##  [7,] 0.31648549 0.73064766 0.30277522
##  [8,]         NA 0.54403835 0.96022228
##  [9,]         NA         NA 0.51953327
## [10,]         NA         NA         NA
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
## 0.001780559
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
