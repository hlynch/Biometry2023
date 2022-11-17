Week 1 Lab
========================================================

In lab today, we will cover just a few of the basic elements of using R. If you are not already fluent with R, you should work through all of Logan Chapter 1, as there are many important elements covered in that chapter that we will not have time to go through in lab. I will assume everyone is using RStudio to run R - exercises and associated code will be written accordingly.

For the purposes of lab, we will be entering all of the commands directly on the command line at the prompt. In general, however, you should get into the habit of writing scripts with all of your code. This will allow you to save your work and go back and easily use code that you have written in the past. When you are doing actual analyses for publication, it is essential that you have all of your code in well commented scripts that could be understood by another researcher in your field. All of your analysis should be fully reproducible long after the paper is published.

Using R like a calculator
-----------------------------------

R can be used like a basic calculator with commands entered at the R prompt '>'


```r
3+5
```

```
## [1] 8
```

```r
3*5
```

```
## [1] 15
```

(The "##" is the html editor's mechanism for indicating R output. The [1] is something that actually appears in the R output. It indicates that what you see is the first element of the output and in this case it can be ignored.)

While R is fairly clever about the order of operations


```r
3*5+7
```

```
## [1] 22
```

it is good practice to be explicit


```r
(3*5)+7
```

```
## [1] 22
```

Notice that the following two expressions are equivalent


```r
4^(1/2)
```

```
## [1] 2
```

```r
sqrt(4)
```

```
## [1] 2
```

The former expression uses the `^` to signify an exponent, whereas the latter uses the built-in R function sqrt() for the square-root.

Note the difference between


```r
-4^(1/2)
```

```
## [1] -2
```

and


```r
(-4)^(1/2)
```

```
## [1] NaN
```

In the first instance, R does the sqrt() and THEN applied the negative, whereas in the second case, it is trying to take the square-root of a negative number and it spits back NaN for 'Not a Number'.

To use scientific notation, use `e`. 


```r
2.2e3
```

```
## [1] 2200
```

For large numbers, R automatically uses scientific notation although the threshold for scientific notation is something you can change using the R function 'options' (advanced use only - don't worry about it for now).


```r
2.2e3*5e5
```

```
## [1] 1.1e+09
```


The basic data structures in R
-----------------------------------

There are several basic types of data structures in R.

1. **VECTORS**: One-dimensional arrays of numbers, character strings, or logical values (T/F)
2. **FACTORS**: One-dimensional arrays of factors (Stop - Let's discuss factors)
3. **DATA FRAMES**: Data tables in which the various columns may be of different type 
4. **MATRICES**:  In R, matrices can only be 2-dimensional, anything higher dimension is called an array (see below). Matrix elements are typically (but not necessarily) numerical, but the key difference from a data frame is that every element has to have the same type. Some functions, like the transpose function t(), only work on matrices.
5. **ARRAYS**: higher dimensional matrices are called arrays in R
6. **LISTS**: lists can contain any type of object as list elements. You can have a list of numbers, a list of matrices, a list of characters, etc., or any combination of the above.

Vectors:

Vectors can be column vectors or row vectors but we are almost always talking about column vectors which are defined with a `c()`. One example of a vector would be a sequence of numbers. There are many ways to generate sequences in R. Lets say you want to define an object x as the following sequence of numbers (1,2,3,4,5,6,7)

You could do this this long way


```r
x<-c(1,2,3,4,5,6,7)
```


Notice here I have used the `<-` to "assign" the column vector (hence "c") of values to the variable x. I could also do this using

```
x<-1:7
```

or I could explicitly use the seq() function as follows

```
x<-seq(from=1,to=7)
```

The sequence function actually has three inputs, but I have left the last off because the default is that you want to step in increments of 1. The full version would be

```
x<-seq(from=1,to=7,by=1)
```

Make sure this works by printing out the value for x

```
x
```

Try changing it up a little with


```r
x<-seq(from=1,to=16,by=3)
```

STOP: Spend a few minutes making vectors and using some of the basic R commands. What happens if you pass a vector to one of R's built-in functions?

R can do a host of logical operations.


```r
x<7
```

```
## [1]  TRUE  TRUE FALSE FALSE FALSE FALSE
```

We can turn that into a binary vector in at least two ways


```r
as.numeric(x<7)
```

```
## [1] 1 1 0 0 0 0
```

or


```r
1*(x<7)
```

```
## [1] 1 1 0 0 0 0
```

The former forces R to return the values of x as a numerical vector, and by default False maps to 0 and True to 1. The latter version does the same thing, by multiplying the logical vector by a number. This trick comes in handy *all the time*.

For example, if you want to know how many values of x are less than 7, you can simply do the following


```r
sum(as.numeric(x<7))
```

```
## [1] 2
```

You can also ask which elements satisfy certain criteria. In other words, you can type


```r
which(x<7)
```

```
## [1] 1 2
```

This is telling you that the first and second elements of the vector are less than 7.

We can take a random set of numbers


```r
y<-c(4,8,6,3,6,9,2)
```

and sort them


```r
sort(y)
```

```
## [1] 2 3 4 6 6 8 9
```

or reverse sort them


```r
rev(sort(y))
```

```
## [1] 9 8 6 6 4 3 2
```


To pull up the help file for the R command 'sort':

```
?sort
```

STOP: Let's take this opportunity to go through all the elements of an R help file.

We can also print out the rank of each value


```r
rank(y)
```

```
## [1] 3.0 6.0 4.5 2.0 4.5 7.0 1.0
```

Notice that ties got averaged. 

Elements of vectors in R are addressed using [] as follows

First lets make a vector z


```r
z<-seq(from=1,to=15,by=2) 
```

We can find the 4th element by simply typing


```r
z[4]
```

```
## [1] 7
```

or we can find the 3rd and 4th elements by typing


```r
z[c(3,4)]
```

```
## [1] 5 7
```

In this more complicated case, we create a vector of the indices we want, and feed that into the brackets.

We can do the opposite as well, instead of pulling out a set of elements you want, you can excise a set of elements and print everything else. In other words, if you wanted all the elements BUT element 3, you would use the minus sign


```r
z[-3]
```

```
## [1]  1  3  7  9 11 13 15
```

Factors:

To explore factors, we will use the dataset Prestige.csv I posted on Blackboard. To simply everything that follows, I will set the working directory to my own folder for this week's lab. This will allow me to reference files within this folder without the entire file name.

Load the data


```r
Prestige<-read.csv("~/Dropbox/Biometry/Week 1 Preliminary Material/Week 1 Lab/Prestige.csv")
```

We can look at the entire data set by typing the name at the command prompt, but we can also just look at the first few lines using the 'head' function


```r
head(Prestige)
```

```
##                     X education income women prestige census type
## 1  gov.administrators     13.11  12351 11.16     68.8   1113 prof
## 2    general.managers     12.26  25879  4.02     69.1   1130 prof
## 3         accountants     12.77   9271 15.70     63.4   1171 prof
## 4 purchasing.officers     11.42   8865  9.11     56.8   1175 prof
## 5            chemists     14.62   8403 11.68     73.5   2111 prof
## 6          physicists     15.64  11030  5.13     77.6   2113 prof
```

or the last few lines using the 'tail' function


```r
tail(Prestige)
```

```
##                   X education income women prestige census type
## 97  train.engineers      8.49   8845  0.00     48.9   9131   bc
## 98      bus.drivers      7.58   5562  9.47     35.9   9171   bc
## 99     taxi.drivers      7.93   4224  3.59     25.1   9173   bc
## 100    longshoremen      8.37   4753  0.00     26.1   9313   bc
## 101     typesetters     10.00   6462 13.58     42.2   9511   bc
## 102     bookbinders      8.55   3617 70.87     35.2   9517   bc
```

We can also get the dimensions of the data set using the 'dim' function


```r
dim(Prestige)
```

```
## [1] 102   7
```

or use the 'length' function to figure out the length of one of the columns


```r
length(Prestige[,1])
```

```
## [1] 102
```

Note: Depending on your version of R and operating system, and how you loaded the dataset Prestige, R may not be holding Prestige as a simple dataframe, and if so, it may tell you that the length(Prestige[,1])=1. In this case, we need to tell R that it should force it into a data.frame (more generally in programming, this is called 'casting') and we can do this as


```r
Prestige<-as.data.frame(Prestige)
```

What this does is take the object Prestiage and change it into a data.frame and then assign that to the variable Prestige (i.e. it replaces Prestiage with the new object). Now try the 'length' command above again and it should yield a more sensible result.

Factors are character labels which take fixed values. First just look at the data.


```r
Prestige
```

```
##                             X education income women prestige census type
## 1          gov.administrators     13.11  12351 11.16     68.8   1113 prof
## 2            general.managers     12.26  25879  4.02     69.1   1130 prof
## 3                 accountants     12.77   9271 15.70     63.4   1171 prof
## 4         purchasing.officers     11.42   8865  9.11     56.8   1175 prof
## 5                    chemists     14.62   8403 11.68     73.5   2111 prof
## 6                  physicists     15.64  11030  5.13     77.6   2113 prof
## 7                  biologists     15.09   8258 25.65     72.6   2133 prof
## 8                  architects     15.44  14163  2.69     78.1   2141 prof
## 9             civil.engineers     14.52  11377  1.03     73.1   2143 prof
## 10           mining.engineers     14.64  11023  0.94     68.8   2153 prof
## 11                  surveyors     12.39   5902  1.91     62.0   2161 prof
## 12                draughtsmen     12.30   7059  7.83     60.0   2163 prof
## 13        computer.programers     13.83   8425 15.33     53.8   2183 prof
## 14                 economists     14.44   8049 57.31     62.2   2311 prof
## 15              psychologists     14.36   7405 48.28     74.9   2315 prof
## 16             social.workers     14.21   6336 54.77     55.1   2331 prof
## 17                    lawyers     15.77  19263  5.13     82.3   2343 prof
## 18                 librarians     14.15   6112 77.10     58.1   2351 prof
## 19     vocational.counsellors     15.22   9593 34.89     58.3   2391 prof
## 20                  ministers     14.50   4686  4.14     72.8   2511 prof
## 21        university.teachers     15.97  12480 19.59     84.6   2711 prof
## 22    primary.school.teachers     13.62   5648 83.78     59.6   2731 prof
## 23  secondary.school.teachers     15.08   8034 46.80     66.1   2733 prof
## 24                 physicians     15.96  25308 10.56     87.2   3111 prof
## 25              veterinarians     15.94  14558  4.32     66.7   3115 prof
## 26   osteopaths.chiropractors     14.71  17498  6.91     68.4   3117 prof
## 27                     nurses     12.46   4614 96.12     64.7   3131 prof
## 28              nursing.aides      9.45   3485 76.14     34.9   3135   bc
## 29           physio.therapsts     13.62   5092 82.66     72.1   3137 prof
## 30                pharmacists     15.21  10432 24.71     69.3   3151 prof
## 31        medical.technicians     12.79   5180 76.04     67.5   3156   wc
## 32         commercial.artists     11.09   6197 21.03     57.2   3314 prof
## 33        radio.tv.announcers     12.71   7562 11.15     57.6   3337   wc
## 34                   athletes     11.44   8206  8.13     54.1   3373 <NA>
## 35                secretaries     11.59   4036 97.51     46.0   4111   wc
## 36                    typists     11.49   3148 95.97     41.9   4113   wc
## 37                bookkeepers     11.32   4348 68.24     49.4   4131   wc
## 38           tellers.cashiers     10.64   2448 91.76     42.3   4133   wc
## 39         computer.operators     11.36   4330 75.92     47.7   4143   wc
## 40            shipping.clerks      9.17   4761 11.37     30.9   4153   wc
## 41                file.clerks     12.09   3016 83.19     32.7   4161   wc
## 42               receptionsts     11.04   2901 92.86     38.7   4171   wc
## 43              mail.carriers      9.22   5511  7.62     36.1   4172   wc
## 44              postal.clerks     10.07   3739 52.27     37.2   4173   wc
## 45        telephone.operators     10.51   3161 96.14     38.1   4175   wc
## 46                 collectors     11.20   4741 47.06     29.4   4191   wc
## 47            claim.adjustors     11.13   5052 56.10     51.1   4192   wc
## 48              travel.clerks     11.43   6259 39.17     35.7   4193   wc
## 49              office.clerks     11.00   4075 63.23     35.6   4197   wc
## 50          sales.supervisors      9.84   7482 17.04     41.5   5130   wc
## 51      commercial.travellers     11.13   8780  3.16     40.2   5133   wc
## 52               sales.clerks     10.05   2594 67.82     26.5   5137   wc
## 53                   newsboys      9.62    918  7.00     14.8   5143 <NA>
## 54  service.station.attendant      9.93   2370  3.69     23.3   5145   bc
## 55           insurance.agents     11.60   8131 13.09     47.3   5171   wc
## 56       real.estate.salesmen     11.09   6992 24.44     47.1   5172   wc
## 57                     buyers     11.03   7956 23.88     51.1   5191   wc
## 58               firefighters      9.47   8895  0.00     43.5   6111   bc
## 59                  policemen     10.93   8891  1.65     51.6   6112   bc
## 60                      cooks      7.74   3116 52.00     29.7   6121   bc
## 61                 bartenders      8.50   3930 15.51     20.2   6123   bc
## 62          funeral.directors     10.57   7869  6.01     54.9   6141   bc
## 63                babysitters      9.46    611 96.53     25.9   6147 <NA>
## 64                 launderers      7.33   3000 69.31     20.8   6162   bc
## 65                   janitors      7.11   3472 33.57     17.3   6191   bc
## 66         elevator.operators      7.58   3582 30.08     20.1   6193   bc
## 67                    farmers      6.84   3643  3.60     44.1   7112 <NA>
## 68               farm.workers      8.60   1656 27.75     21.5   7182   bc
## 69       rotary.well.drillers      8.88   6860  0.00     35.3   7711   bc
## 70                     bakers      7.54   4199 33.30     38.9   8213   bc
## 71             slaughterers.1      7.64   5134 17.26     25.2   8215   bc
## 72             slaughterers.2      7.64   5134 17.26     34.8   8215   bc
## 73                    canners      7.42   1890 72.24     23.2   8221   bc
## 74            textile.weavers      6.69   4443 31.36     33.3   8267   bc
## 75          textile.labourers      6.74   3485 39.48     28.8   8278   bc
## 76            tool.die.makers     10.09   8043  1.50     42.5   8311   bc
## 77                 machinists      8.81   6686  4.28     44.2   8313   bc
## 78        sheet.metal.workers      8.40   6565  2.30     35.9   8333   bc
## 79                    welders      7.92   6477  5.17     41.8   8335   bc
## 80               auto.workers      8.43   5811 13.62     35.9   8513   bc
## 81           aircraft.workers      8.78   6573  5.78     43.7   8515   bc
## 82         electronic.workers      8.76   3942 74.54     50.8   8534   bc
## 83         radio.tv.repairmen     10.29   5449  2.92     37.2   8537   bc
## 84      sewing.mach.operators      6.38   2847 90.67     28.2   8563   bc
## 85             auto.repairmen      8.10   5795  0.81     38.1   8581   bc
## 86         aircraft.repairmen     10.10   7716  0.78     50.3   8582   bc
## 87         railway.sectionmen      6.67   4696  0.00     27.3   8715   bc
## 88         electrical.linemen      9.05   8316  1.34     40.9   8731   bc
## 89               electricians      9.93   7147  0.99     50.2   8733   bc
## 90       construction.foremen      8.24   8880  0.65     51.1   8780   bc
## 91                 carpenters      6.92   5299  0.56     38.9   8781   bc
## 92                     masons      6.60   5959  0.52     36.2   8782   bc
## 93             house.painters      7.81   4549  2.46     29.9   8785   bc
## 94                   plumbers      8.33   6928  0.61     42.9   8791   bc
## 95     construction.labourers      7.52   3910  1.09     26.5   8798   bc
## 96                     pilots     12.27  14032  0.58     66.1   9111 prof
## 97            train.engineers      8.49   8845  0.00     48.9   9131   bc
## 98                bus.drivers      7.58   5562  9.47     35.9   9171   bc
## 99               taxi.drivers      7.93   4224  3.59     25.1   9173   bc
## 100              longshoremen      8.37   4753  0.00     26.1   9313   bc
## 101               typesetters     10.00   6462 13.58     42.2   9511   bc
## 102               bookbinders      8.55   3617 70.87     35.2   9517   bc
```

Notice that the third column assigns a type of professional status to the different occupations. We can have R list all those by printing just the last column. We do that by using the $ followed by the name of that column:


```r
Prestige$type
```

```
##   [1] "prof" "prof" "prof" "prof" "prof" "prof" "prof" "prof" "prof" "prof"
##  [11] "prof" "prof" "prof" "prof" "prof" "prof" "prof" "prof" "prof" "prof"
##  [21] "prof" "prof" "prof" "prof" "prof" "prof" "prof" "bc"   "prof" "prof"
##  [31] "wc"   "prof" "wc"   NA     "wc"   "wc"   "wc"   "wc"   "wc"   "wc"  
##  [41] "wc"   "wc"   "wc"   "wc"   "wc"   "wc"   "wc"   "wc"   "wc"   "wc"  
##  [51] "wc"   "wc"   NA     "bc"   "wc"   "wc"   "wc"   "bc"   "bc"   "bc"  
##  [61] "bc"   "bc"   NA     "bc"   "bc"   "bc"   NA     "bc"   "bc"   "bc"  
##  [71] "bc"   "bc"   "bc"   "bc"   "bc"   "bc"   "bc"   "bc"   "bc"   "bc"  
##  [81] "bc"   "bc"   "bc"   "bc"   "bc"   "bc"   "bc"   "bc"   "bc"   "bc"  
##  [91] "bc"   "bc"   "bc"   "bc"   "bc"   "prof" "bc"   "bc"   "bc"   "bc"  
## [101] "bc"   "bc"
```

Notice that in addition to just listing that column, R also tells you what all the factor values are. We can do this with numerical values too, but be careful because R will interpret the numerical values as characters:

IMPORTANT: By default, R will rank factors alphabetically. R will do this also when doing modeling and it is almost never what you want. In this case, you likely want to think of the factors arranged as bc$<$wc$<$prof. To do this you:


```r
Prestige$type<-factor(Prestige$type,levels=c("bc","wc","prof"))
levels(Prestige$type)
```

```
## [1] "bc"   "wc"   "prof"
```

Data frames:

R has a special object called a dataframe which is, as the name suggests, designed to hold data. Unlike a matrix, in which all the elements have to be the same type (typically numbers), dataframes are more like spreadsheets - each column can be its own datatype. So you can have a column of numbers associated with a second column of treatment types (character).
Let's make a data frame to play around with, which we will make the ranking of the top three girls and boys names for 2010.



```r
our.data.frame<-data.frame(rank=seq(1:3),boys.names=c("Jacob","Ethan","Michael"), girls.names=c("Isabella","Sophia","Emma"))
our.data.frame
```

```
##   rank boys.names girls.names
## 1    1      Jacob    Isabella
## 2    2      Ethan      Sophia
## 3    3    Michael        Emma
```

Now while I would encourage everyone to use the command line at all times, its worth pointing out that R does have a very basic data editor. To change a value in our.data.frame using the data editor, use the command 'fix'

```
fix(our.data.frame)
```

Note that the command 'edit' *looks* like it should do the same thing but it does not. In fact, 'edit' does not change the original data frame but it makes a changed copy which must be assigned another name. In the following example, the changes are stored in new.data.frame.

```
new.data.frame<-edit(our.data.frame)
```

NOTE: R allows you to 'attach' a dataframe to a workspace so that you can refer to the individual columns without having to type in the name of the dataframe. I think this is terrible practice and makes your code impossible to read by your future self.

Matrices:

You make a matrix as follows (here we populate the matrix with a sequence from 1:12):


```r
test.matrix<-matrix(1:12,nrow=3,ncol=4)
test.matrix
```

```
##      [,1] [,2] [,3] [,4]
## [1,]    1    4    7   10
## [2,]    2    5    8   11
## [3,]    3    6    9   12
```

Notice that in general, you do not need to include the label names for input parameters to functions. This gives the same answer:


```r
test.matrix<-matrix(1:12,3,4)
test.matrix
```

```
##      [,1] [,2] [,3] [,4]
## [1,]    1    4    7   10
## [2,]    2    5    8   11
## [3,]    3    6    9   12
```

but I *highly* suggest leaving all labels for clarity of coding.

R indexes matrices by ROW THEN COLUMN. So, for example, try


```r
test.matrix[2,3]
```

```
## [1] 8
```

R has all the functions you could ever want for matrix algebra, such as transposing:


```r
trans.test.matrix<-t(test.matrix)
```

See what happens when you try

```
1-test.matrix
```

Notice that R automatically translates the 1 into a matrix of 1s such that the calculation makes sense.


Arrays:

Arrays are just higher dimensional matrices and since we will not use them much, I won't get into details here.

Lists:

A list is a one-dimensional structure of potentially heterogeneous data types.


```r
list.1<-list(data=seq(1,15),mat1=test.matrix,mat2=trans.test.matrix)
```

We can reference elements of the list by name


```r
list.1$data
```

```
##  [1]  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15
```

or by position


```r
list.1[[2]]
```

```
##      [,1] [,2] [,3] [,4]
## [1,]    1    4    7   10
## [2,]    2    5    8   11
## [3,]    3    6    9   12
```

Notice that list indexing requires double brackets.


Writing functions in R
-----------------------------------


Using R, you are not limited to functions that have been written for you, you can write your own functions! The basic template is straightforward:


```r
square<-function(x)
  {
		x*x
	}
```

We can use our function now as follows:


```r
square(5)
```

```
## [1] 25
```

You can also have more than one argument for an R function:
 

```r
product<-function(x,y)
  {
  	x*y
	}

product(3,5)
```

```
## [1] 15
```

A few notes about using R. What makes R special is not the base package but the "Contributed packages" which make up the bulk of R's utility. We will be using a variety of these contributed packages along the way, so you need to feel comfortable downloading them from the web. I have posted a handout on Blackboard to cover this.

Writing loops and if/else
-----------------------------------

The R language is very good at doing operations on vectors or matrices, and when possible, this is always the preferred method of doing an operation mulitple times. However, sometimes this is not possible and you have to write a loop to perform some operation on elements taken one at a time.

There are two different kinds of loops in R. A 'for loop' executes once for each step through the looping index.

The basic syntax for a 'for loop' in R is:


```r
for (i in 1:6) {
  print(i)
}
```

```
## [1] 1
## [1] 2
## [1] 3
## [1] 4
## [1] 5
## [1] 6
```

The indexing variable does not need to be called "i", it could be anything. What follows the "in" can be any sequence of numbers; they need not be consecutive. What appears inside the brackets is the chunk of code that will be executed at each iteration. This code can, but need not, actually use the indexing variable. Another example illustrating these points is:


```r
for (blah in c(1,3,5,6)) {
  print(4+blah)
}
```

```
## [1] 5
## [1] 7
## [1] 9
## [1] 10
```

A 'while loop' is open ended; it will execute the loop indefinitely until the 'while' condition is no longer met.

The basic syntax for a 'while loop' in R is:


```r
i=1
while(i <= 8) {
    y <- i*i
    i <- i + 1 # What would happen if we left this line out?
    print(y)
    }
```

```
## [1] 1
## [1] 4
## [1] 9
## [1] 16
## [1] 25
## [1] 36
## [1] 49
## [1] 64
```

Sometimes, you want to make R check some condition before executing a command. An 'if' statement will check a statement and execute a chunk of code if the statement evaluates to TRUE. If the statement evaluates to FALSE, the code is simply skipped. An if/else statement allows a second chunk of code to be executed as an alternative to the first. The syntax for each is as follows:


```r
a<-3
if (a<4) print("Hello")
```

```
## [1] "Hello"
```


```r
if (a < 4) {
  print("Hello")
  } else print("Goodbye")
```

```
## [1] "Hello"
```

```r
if (a > 4) {
  print("Hello")
  } else print("Goodbye")
```

```
## [1] "Goodbye"
```

(A short diversion) Bias in estimators
-----------------------------------

Now we will stop for a short digression about how to calculate the population variance (i.e. the variance assuming the data I have is from the entire population) and how to estimate the sample variance (i.e. the variance assuming what I have is a *sample* from the population, and I want to infer the variance of the underlying but unknown population), since we can now use R to convince ourselves that the naive estimator for variance is biased. 

The population variance is the variance of a population which, by definition, means that every single individual of interest has been measured. Remember, in this case there is no inference going on. When we have measured every single individual of interest, all we can do (statistically) is describe that population. The population variance describes the variation in the quantity of interest *for that population you have completely sampled*).

$$
\sum^{n}_{i=1}{\frac{(Y_{i}-\bar{Y})^{2}}{n}}
$$

The sample variance answers the question "If this data I have come from a larger population, and I want to use these data to estimate the population variance in that larger population, what is the best unbiased estimator for that (unknown) population variance?" The formula for the sample variance (think of it like "the estimate of the variance from the sample"):

$$
\sum^{n}_{i=1}{\frac{(Y_{i}-\bar{Y})^{2}}{n-1}}
$$

We can see this in practice using a little simulation. Type the following into an R script and run it in R:


```r
n.iter<-100
data<-rnorm(n.iter,0,2)
sum<-0
for (j in 1:length(data))
{
  sum<-sum+((data[j]-mean(data))*(data[j]-mean(data)))
}
population.variance<-sum/length(data)
sample.variance<-sum/(length(data)-1)
```

What is the ratio of the sample variance to the population variance? Are either close to what we know the true variance to be? What happens if we change n.iter to 1000? Do the values get closer to the correct value? What happens to the ratio of the sample variance to the population variance?

What does the R function var() give you?

Lesson #6: Some practice writing R code
-----------------------------------
  
We will be using a cloud-seeding dataset from: Simpson, Alsen, and Eden. (1975). A Bayesian analysis of a multiplicative treatment effect in weather modification. Technometrics 17, 161-166. The data consist of data on the amount of rainfall (in acre-feet) from unseeded clouds vs. those seeded with silver nitrate.

Here and throughout I have assumed the data resides in a folder called Biometry on a thumb drive called TEACHING drive but you will have to change the code according to your own file structure. (I am using a Mac, but getting the pathname correct I have included a .txt file and a .csv file to show you the differences in inputting your data:
  
Method 1:
  

```r
cloud.data<-read.table("~/Dropbox/Biometry/Week 1 Preliminary Material/Week 1 Lab/clouds.txt")
```

Notice that that doesn't work because the headers have become part of the data.


```r
cloud.data<-read.table("~/Dropbox/Biometry/Week 1 Preliminary Material/Week 1 Lab/clouds.txt", header=T)
```

Remember that we need to add the "header=T" or it will assume the headers are actually the first line of data.

Method 2:


```r
cloud.data<-read.table("~/Dropbox/Biometry/Week 1 Preliminary Material/Week 1 Lab/clouds.csv", header=T)
```

This doesn't work because R does know what the delimiter is. You have to specify the delimiter:


```r
cloud.data<-read.table("~/Dropbox/Biometry/Week 1 Preliminary Material/Week 1 Lab/clouds.csv", header=T,sep=",")
```

or use the command 'read.csv' which automatically assumes its comma delimited.


```r
cloud.data<-read.csv("~/Dropbox/Biometry/Week 1 Preliminary Material/Week 1 Lab/clouds.csv", header=T)
```

There are two ways to refer to the first column of data. Because we have column headers, we can refer to them by name using the "$" as follows:


```r
cloud.data$Unseeded_Clouds
```

```
##  [1] 1202.6  830.1  372.4  345.5  321.2  244.3  163.0  147.8   95.0   87.0
## [11]   81.2   68.5   47.3   41.1   36.6   29.0   28.6   26.3   26.1   24.4
## [21]   21.7   17.3   11.5    4.9    4.9    1.0
```

but we can also just ask for a specific column of the data, in this case the first column


```r
cloud.data[,1]
```

```
##  [1] 1202.6  830.1  372.4  345.5  321.2  244.3  163.0  147.8   95.0   87.0
## [11]   81.2   68.5   47.3   41.1   36.6   29.0   28.6   26.3   26.1   24.4
## [21]   21.7   17.3   11.5    4.9    4.9    1.0
```

Note that you can always print the data using just the name


```r
cloud.data
```

```
##    Unseeded_Clouds Seeded_Clouds
## 1           1202.6        2745.6
## 2            830.1        1697.8
## 3            372.4        1656.0
## 4            345.5         978.0
## 5            321.2         703.4
## 6            244.3         489.1
## 7            163.0         430.0
## 8            147.8         334.1
## 9             95.0         302.8
## 10            87.0         274.7
## 11            81.2         274.7
## 12            68.5         255.0
## 13            47.3         242.5
## 14            41.1         200.7
## 15            36.6         198.6
## 16            29.0         129.6
## 17            28.6         119.0
## 18            26.3         118.3
## 19            26.1         115.3
## 20            24.4          92.4
## 21            21.7          40.6
## 22            17.3          32.7
## 23            11.5          31.4
## 24             4.9          17.5
## 25             4.9           7.7
## 26             1.0           4.1
```

or, if its easier, can use the data editor as described above.

Lets calculate the variance of each treatment. For now, I will do this step-by-step, defining intermediate variables along the way. For simplicity, I redefine the two columns worth of data as "A" and "B":


```r
A<- cloud.data$Unseeded_Clouds
mean.A<-mean(A)
diff.from.mean.A<- A-mean.A
n.A<-length(A)  # Here I am just calculating the sample size to use in next line
s2.A<-sum(diff.from.mean.A^2)/(n.A-1)
s2.A
```

```
## [1] 77521.26
```

Redo the calculation for the Seeded clouds to get "s2.B".

We could have saved ourselves a lot of effort by using the R function "var":


```r
s2.A<-var(A)
s2.A
```

```
## [1] 77521.26
```

Is the variance for the Seeded clouds the same as the Unseeded clouds? How close (to equal) is close enough? What is the null hypothesis?

A few final notes
-----------------------------------

I mentioned at the outset that all of your code should be kept in a script (some kind of text file; it could be a .R file but it could be a simple .txt file) and that your code should be clearly commented. Comments can be added to code using the # sign. For example


```r
a<-3+5  #This is a comment
```

everything after the # is not executed by R and is simply for your use in understanding the code.

**Short digression on brackets and good coding practices.
