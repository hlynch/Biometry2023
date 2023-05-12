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
##       [,1]      [,2]      [,3]      [,4]       [,5]       [,6]       [,7]
##  [1,]   NA 0.8526407 0.9068346 0.4181392 0.08239370 0.79941791 0.94760652
##  [2,]   NA        NA 0.9599131 0.2396483 0.03518692 0.92699188 0.91008799
##  [3,]   NA        NA        NA 0.3502788 0.06625380 0.90139499 0.95773413
##  [4,]   NA        NA        NA        NA 0.24246446 0.23009072 0.37140924
##  [5,]   NA        NA        NA        NA         NA 0.03510193 0.06961217
##  [6,]   NA        NA        NA        NA         NA         NA 0.85298177
##  [7,]   NA        NA        NA        NA         NA         NA         NA
##  [8,]   NA        NA        NA        NA         NA         NA         NA
##  [9,]   NA        NA        NA        NA         NA         NA         NA
## [10,]   NA        NA        NA        NA         NA         NA         NA
##            [,8]       [,9]      [,10]
##  [1,] 0.8708467 0.60731759 0.50889641
##  [2,] 0.7292858 0.67782028 0.56235992
##  [3,] 0.7880784 0.68124378 0.57842666
##  [4,] 0.5816730 0.21746487 0.16098806
##  [5,] 0.1469205 0.04520337 0.03112155
##  [6,] 0.6871722 0.73469874 0.61982538
##  [7,] 0.8227821 0.64419166 0.54223736
##  [8,]        NA 0.53369136 0.44817848
##  [9,]        NA         NA 0.90133259
## [10,]        NA         NA         NA
```

Now we can see how many of these p.values are "significant". We know these are false positives, because all the data were generated from the same distribution.


```r
false.positives<-sum(p.values<0.05,na.rm=T)
false.positives
```

```
## [1] 4
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
## 0.002023407
```

```r
false.positives.R<-sum(p.values<new.threshold.R,na.rm=T)
false.positives.R
```

```
## [1] 0
```

If you were to do this experiment (all of the code in the preceding clock) 100 times, you should get at least 1 false positive 5 times, since we have set the threshold such that we have a 5% chance that the smallest p-value in that set of 45 comparisons will be smaller than the threshold we set.
