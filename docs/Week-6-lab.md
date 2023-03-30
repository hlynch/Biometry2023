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
##       [,1]      [,2]      [,3]      [,4]      [,5]       [,6]      [,7]
##  [1,]   NA 0.7912567 0.9920615 0.7646228 0.2243567 0.04859761 0.2610302
##  [2,]   NA        NA 0.7971365 0.9819450 0.3771764 0.12093463 0.3859881
##  [3,]   NA        NA        NA 0.7713434 0.2256573 0.04851772 0.2626694
##  [4,]   NA        NA        NA        NA 0.2954146 0.06241182 0.3307538
##  [5,]   NA        NA        NA        NA        NA 0.52965064 0.9037368
##  [6,]   NA        NA        NA        NA        NA         NA 0.6921746
##  [7,]   NA        NA        NA        NA        NA         NA        NA
##  [8,]   NA        NA        NA        NA        NA         NA        NA
##  [9,]   NA        NA        NA        NA        NA         NA        NA
## [10,]   NA        NA        NA        NA        NA         NA        NA
##             [,8]      [,9]     [,10]
##  [1,] 0.03240681 0.1510678 0.2556519
##  [2,] 0.07950788 0.2589255 0.3900259
##  [3,] 0.03237737 0.1518756 0.2572609
##  [4,] 0.04157751 0.1969053 0.3277137
##  [5,] 0.36915446 0.7517986 0.9419656
##  [6,] 0.73809973 0.8069825 0.6337260
##  [7,] 0.52247021 0.8734641 0.9619542
##  [8,]         NA 0.6037762 0.4664850
##  [9,]         NA        NA 0.8273371
## [10,]         NA        NA        NA
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
## 0.001782606
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
