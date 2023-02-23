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
##       [,1]       [,2]      [,3]      [,4]      [,5]       [,6]      [,7]
##  [1,]   NA 0.05585522 0.2779571 0.0409094 0.1921348 0.06402685 0.2542907
##  [2,]   NA         NA 0.2210817 0.8878596 0.4704949 0.81402514 0.3559999
##  [3,]   NA         NA        NA 0.1648196 0.6639654 0.21520562 0.8319045
##  [4,]   NA         NA        NA        NA 0.3854228 0.90691020 0.2841267
##  [5,]   NA         NA        NA        NA        NA 0.39835167 0.8408554
##  [6,]   NA         NA        NA        NA        NA         NA 0.3116927
##  [7,]   NA         NA        NA        NA        NA         NA        NA
##  [8,]   NA         NA        NA        NA        NA         NA        NA
##  [9,]   NA         NA        NA        NA        NA         NA        NA
## [10,]   NA         NA        NA        NA        NA         NA        NA
##             [,8]      [,9]     [,10]
##  [1,] 0.02053329 0.1846535 0.1612245
##  [2,] 0.35283986 0.8424781 0.3908812
##  [3,] 0.06832168 0.4956193 0.6519322
##  [4,] 0.40850137 0.7579677 0.3065098
##  [5,] 0.14189379 0.7220628 0.9570124
##  [6,] 0.51655317 0.7092860 0.3421931
##  [7,] 0.10629675 0.6122529 0.8627741
##  [8,]         NA 0.3407210 0.1134243
##  [9,]         NA        NA 0.6773034
## [10,]         NA        NA        NA
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
##         5% 
## 0.00198341
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
