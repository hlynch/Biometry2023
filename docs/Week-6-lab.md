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
##       [,1]      [,2]      [,3]      [,4]      [,5]       [,6]       [,7]
##  [1,]   NA 0.8153629 0.6027564 0.1269862 0.8263365 0.30979407 0.92694703
##  [2,]   NA        NA 0.4806631 0.1078208 0.6729709 0.49743061 0.86885656
##  [3,]   NA        NA        NA 0.2957056 0.7898569 0.12363146 0.51567003
##  [4,]   NA        NA        NA        NA 0.2207514 0.01314331 0.08663708
##  [5,]   NA        NA        NA        NA        NA 0.24533336 0.75073405
##  [6,]   NA        NA        NA        NA        NA         NA 0.31445658
##  [7,]   NA        NA        NA        NA        NA         NA         NA
##  [8,]   NA        NA        NA        NA        NA         NA         NA
##  [9,]   NA        NA        NA        NA        NA         NA         NA
## [10,]   NA        NA        NA        NA        NA         NA         NA
##            [,8]       [,9]     [,10]
##  [1,] 0.8534249 0.36135482 0.9974720
##  [2,] 0.6707714 0.52251419 0.8121065
##  [3,] 0.6748299 0.17401946 0.5870731
##  [4,] 0.1135197 0.02915157 0.1135110
##  [5,] 0.9361811 0.29043900 0.8186009
##  [6,] 0.1660601 0.95634181 0.2915947
##  [7,] 0.7539989 0.37608502 0.9264824
##  [8,]        NA 0.24312928 0.8429907
##  [9,]        NA         NA 0.3493732
## [10,]        NA         NA        NA
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
##          5% 
## 0.001855265
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
