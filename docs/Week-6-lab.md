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
##  [1,]   NA 0.2874377 0.2188985 0.3770851 0.5922552 0.73182344 0.6785410
##  [2,]   NA        NA 0.8643921 0.7209325 0.6125649 0.16824386 0.2167845
##  [3,]   NA        NA        NA 0.7803086 0.6426705 0.08744336 0.1906009
##  [4,]   NA        NA        NA        NA 0.8225927 0.19826453 0.2803119
##  [5,]   NA        NA        NA        NA        NA 0.39398472 0.4163501
##  [6,]   NA        NA        NA        NA        NA         NA 0.8630668
##  [7,]   NA        NA        NA        NA        NA         NA        NA
##  [8,]   NA        NA        NA        NA        NA         NA        NA
##  [9,]   NA        NA        NA        NA        NA         NA        NA
## [10,]   NA        NA        NA        NA        NA         NA        NA
##            [,8]      [,9]     [,10]
##  [1,] 0.7880195 0.9286628 0.5720382
##  [2,] 0.4320859 0.3306755 0.5989281
##  [3,] 0.4055855 0.3112145 0.6206798
##  [4,] 0.5845050 0.4352719 0.8134108
##  [5,] 0.7885713 0.5984870 0.9992311
##  [6,] 0.5496715 0.8628303 0.3662967
##  [7,] 0.5425704 0.7769061 0.4010829
##  [8,]        NA 0.7608394 0.7780756
##  [9,]        NA        NA 0.5853088
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
## 0.001826658
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
