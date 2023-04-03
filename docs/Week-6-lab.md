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
##  [1,]   NA 0.3437991 0.9188185 0.7722820 0.5692722 0.6299542 0.09640988
##  [2,]   NA        NA 0.4954506 0.2302615 0.6767458 0.6216497 0.38508262
##  [3,]   NA        NA        NA 0.7373787 0.7211207 0.7734679 0.17357093
##  [4,]   NA        NA        NA        NA 0.3970988 0.4472711 0.06302769
##  [5,]   NA        NA        NA        NA        NA 0.9327152 0.21474935
##  [6,]   NA        NA        NA        NA        NA        NA 0.19387806
##  [7,]   NA        NA        NA        NA        NA        NA         NA
##  [8,]   NA        NA        NA        NA        NA        NA         NA
##  [9,]   NA        NA        NA        NA        NA        NA         NA
## [10,]   NA        NA        NA        NA        NA        NA         NA
##             [,8]       [,9]     [,10]
##  [1,] 0.11156466 0.15978680 0.4900726
##  [2,] 0.50056562 0.77865898 0.7828020
##  [3,] 0.21801603 0.32838935 0.6439292
##  [4,] 0.06938346 0.09338702 0.3382764
##  [5,] 0.27054858 0.42559819 0.8896829
##  [6,] 0.24224098 0.37792277 0.8260143
##  [7,] 0.78394423 0.45504304 0.2659502
##  [8,]         NA 0.60485512 0.3406749
##  [9,]         NA         NA 0.5379967
## [10,]         NA         NA        NA
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
## 0.001619214
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
