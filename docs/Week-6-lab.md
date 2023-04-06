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
##  [1,]   NA 0.5541293 0.19077130 0.5023008 0.3064208 0.7843780 0.65516665
##  [2,]   NA        NA 0.07924211 0.2060763 0.1308017 0.4107313 0.85438152
##  [3,]   NA        NA         NA 0.3478871 0.7429309 0.3156914 0.08520757
##  [4,]   NA        NA         NA        NA 0.5647239 0.7554150 0.23035067
##  [5,]   NA        NA         NA        NA        NA 0.4748520 0.14535105
##  [6,]   NA        NA         NA        NA        NA        NA 0.48299293
##  [7,]   NA        NA         NA        NA        NA        NA         NA
##  [8,]   NA        NA         NA        NA        NA        NA         NA
##  [9,]   NA        NA         NA        NA        NA        NA         NA
## [10,]   NA        NA         NA        NA        NA        NA         NA
##            [,8]       [,9]      [,10]
##  [1,] 0.6221336 0.45294667 0.64270523
##  [2,] 0.2647402 0.89477302 0.84075574
##  [3,] 0.2665273 0.05342967 0.07127117
##  [4,] 0.7985205 0.14021263 0.19393898
##  [5,] 0.4477943 0.09114233 0.12575922
##  [6,] 0.8884794 0.32701034 0.46523401
##  [7,] 0.3037519 0.73971638 0.99272415
##  [8,]        NA 0.18581752 0.26562815
##  [9,]        NA         NA 0.71992308
## [10,]        NA         NA         NA
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
##       5% 
## 0.001641
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
