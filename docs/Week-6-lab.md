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
##       [,1]      [,2]       [,3]      [,4]      [,5]       [,6]      [,7]
##  [1,]   NA 0.7384199 0.19597179 0.3101956 0.3718102 0.12874592 0.3196045
##  [2,]   NA        NA 0.05225848 0.1230533 0.1601481 0.03226415 0.1598339
##  [3,]   NA        NA         NA 0.8029907 0.6880092 0.71341698 0.9367326
##  [4,]   NA        NA         NA        NA 0.8890285 0.57090651 0.9056029
##  [5,]   NA        NA         NA        NA        NA 0.47826212 0.8128005
##  [6,]   NA        NA         NA        NA        NA         NA 0.7232576
##  [7,]   NA        NA         NA        NA        NA         NA        NA
##  [8,]   NA        NA         NA        NA        NA         NA        NA
##  [9,]   NA        NA         NA        NA        NA         NA        NA
## [10,]   NA        NA         NA        NA        NA         NA        NA
##             [,8]      [,9]     [,10]
##  [1,] 0.27935186 0.6998751 0.4227205
##  [2,] 0.08178507 0.3689274 0.2236988
##  [3,] 0.72741124 0.2240396 0.7273319
##  [4,] 0.96288178 0.4039138 0.8994467
##  [5,] 0.90981325 0.4959317 0.9970209
##  [6,] 0.48027976 0.1365973 0.5344841
##  [7,] 0.86677861 0.4184791 0.8269049
##  [8,]         NA 0.3452523 0.9202087
##  [9,]         NA        NA 0.5636463
## [10,]         NA        NA        NA
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
## 0.001783955
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
