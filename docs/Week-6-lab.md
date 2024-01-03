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
##       [,1]      [,2]      [,3]       [,4]       [,5]      [,6]      [,7]
##  [1,]   NA 0.3255757 0.7086271 0.57954528 0.43144765 0.9118805 0.6981586
##  [2,]   NA        NA 0.3694197 0.08941138 0.05581251 0.1856303 0.5575073
##  [3,]   NA        NA        NA 0.24341762 0.15108506 0.5133836 0.9147926
##  [4,]   NA        NA        NA         NA 0.78296107 0.5825642 0.3231851
##  [5,]   NA        NA        NA         NA         NA 0.4072593 0.2253100
##  [6,]   NA        NA        NA         NA         NA        NA 0.5689093
##  [7,]   NA        NA        NA         NA         NA        NA        NA
##  [8,]   NA        NA        NA         NA         NA        NA        NA
##  [9,]   NA        NA        NA         NA         NA        NA        NA
## [10,]   NA        NA        NA         NA         NA        NA        NA
##            [,8]      [,9]     [,10]
##  [1,] 0.9560539 0.9956048 0.7049995
##  [2,] 0.2055253 0.2041398 0.1079624
##  [3,] 0.6131607 0.5881808 0.3067241
##  [4,] 0.4188541 0.4701470 0.8095419
##  [5,] 0.2716421 0.3133071 0.5960271
##  [6,] 0.8163456 0.8745398 0.7347258
##  [7,] 0.6616782 0.6360606 0.4013193
##  [8,]        NA 0.9418654 0.5399863
##  [9,]        NA        NA 0.6025488
## [10,]        NA        NA        NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 0
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
## 0.001441466
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
