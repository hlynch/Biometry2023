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
##       [,1]      [,2]       [,3]      [,4]      [,5]      [,6]       [,7]
##  [1,]   NA 0.2026476 0.05023861 0.7396834 0.2231216 0.2807560 0.79552570
##  [2,]   NA        NA 0.41582387 0.4399535 0.8555127 0.8407876 0.09838992
##  [3,]   NA        NA         NA 0.1579368 0.2919622 0.3196016 0.01910473
##  [4,]   NA        NA         NA        NA 0.4990529 0.5466579 0.56709745
##  [5,]   NA        NA         NA        NA        NA 0.9680998 0.09756592
##  [6,]   NA        NA         NA        NA        NA        NA 0.15011951
##  [7,]   NA        NA         NA        NA        NA        NA         NA
##  [8,]   NA        NA         NA        NA        NA        NA         NA
##  [9,]   NA        NA         NA        NA        NA        NA         NA
## [10,]   NA        NA         NA        NA        NA        NA         NA
##             [,8]       [,9]     [,10]
##  [1,] 0.07167937 0.09591386 0.6864875
##  [2,] 0.46951103 0.59017904 0.5147210
##  [3,] 0.97713941 0.81731612 0.2031894
##  [4,] 0.18810795 0.24042358 0.9378175
##  [5,] 0.35224821 0.45797568 0.5820226
##  [6,] 0.37162199 0.47376012 0.6244383
##  [7,] 0.03329245 0.04483505 0.5259733
##  [8,]         NA 0.85061535 0.2341882
##  [9,]         NA         NA 0.2944742
## [10,]         NA         NA        NA
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
## 0.001599704
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
