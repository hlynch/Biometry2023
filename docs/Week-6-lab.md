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
##       [,1]      [,2]      [,3]      [,4]      [,5]      [,6]       [,7]
##  [1,]   NA 0.8100915 0.5230061 0.5457884 0.7886901 0.5154659 0.20264703
##  [2,]   NA        NA 0.4332909 0.7709228 0.6473668 0.4206624 0.17573129
##  [3,]   NA        NA        NA 0.2673854 0.7166319 0.8990121 0.62723172
##  [4,]   NA        NA        NA        NA 0.4275526 0.2140797 0.07738096
##  [5,]   NA        NA        NA        NA        NA 0.7648305 0.36139599
##  [6,]   NA        NA        NA        NA        NA        NA 0.46546013
##  [7,]   NA        NA        NA        NA        NA        NA         NA
##  [8,]   NA        NA        NA        NA        NA        NA         NA
##  [9,]   NA        NA        NA        NA        NA        NA         NA
## [10,]   NA        NA        NA        NA        NA        NA         NA
##             [,8]       [,9]      [,10]
##  [1,] 0.06087990 0.42152120 0.81856705
##  [2,] 0.06630862 0.60479850 0.97310097
##  [3,] 0.39630903 0.21472808 0.41770303
##  [4,] 0.01603732 0.77482505 0.71735511
##  [5,] 0.17223649 0.33645345 0.64253273
##  [6,] 0.20331548 0.17878330 0.38930910
##  [7,] 0.72521789 0.06945379 0.14951739
##  [8,]         NA 0.02143732 0.04229613
##  [9,]         NA         NA 0.55253839
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
## 0.001725785
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
