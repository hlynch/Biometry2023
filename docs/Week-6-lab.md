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
##       [,1]      [,2]      [,3]       [,4]       [,5]       [,6]       [,7]
##  [1,]   NA 0.9928933 0.6831507 0.05559490 0.92053364 0.86043025 0.92957321
##  [2,]   NA        NA 0.6826683 0.06166043 0.92862785 0.85591950 0.93805583
##  [3,]   NA        NA        NA 0.01248092 0.62006790 0.82631954 0.60798801
##  [4,]   NA        NA        NA         NA 0.08279686 0.03581214 0.06264818
##  [5,]   NA        NA        NA         NA         NA 0.78799210 0.98683490
##  [6,]   NA        NA        NA         NA         NA         NA 0.78892841
##  [7,]   NA        NA        NA         NA         NA         NA         NA
##  [8,]   NA        NA        NA         NA         NA         NA         NA
##  [9,]   NA        NA        NA         NA         NA         NA         NA
## [10,]   NA        NA        NA         NA         NA         NA         NA
##             [,8]        [,9]      [,10]
##  [1,] 0.92995401 0.636898746 0.89831004
##  [2,] 0.92347662 0.638089504 0.89266473
##  [3,] 0.69016223 0.965640204 0.72746984
##  [4,] 0.01971137 0.007243207 0.01861311
##  [5,] 0.84339470 0.576083107 0.81455201
##  [6,] 0.90672538 0.785497080 0.93998949
##  [7,] 0.84662121 0.558099676 0.81549030
##  [8,]         NA 0.624620476 0.95937110
##  [9,]         NA          NA 0.66541800
## [10,]         NA          NA         NA
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
## 0.001843239
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
