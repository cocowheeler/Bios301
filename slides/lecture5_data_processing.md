Data Processing in R
====================

Presenter Notes
===============

This will be a *hands-on* lecture!

---

Data Frames
===========

Data frames are the default data structure in R for manipulating large, multi-column, heterogeneous datasets.

Recall that in data frames, data are organized into rows and columns, with rows
representing individual observational units and columns representing the variables
for each.

There are several functions that are important for being able to use data frames effectively for processing your data.

---

Sample Dataset: HAART
=====================

Our sample database is some de-identified data for Highly Active Antiretroviral Therapy (HAART) patients. The data file, `haart.csv` is located in the `datasets` folder on the GitHub repository.

Here is what are first 4 lines (header + 3 data rows) of the table:

    !r
    "male","age","aids","cd4baseline","logvl","weight","hemoglobin","init.reg",
        "init.date","last.visit","death","date.death","event","followup","lfup","pid"
    1,25,0,NA,NA,NA,NA,"3TC,AZT,EFV","2003-07-01","2007-02-26",0,NA,0,365,0,1
    1,49,0,143,NA,58.0608,11,"3TC,AZT,EFV","2004-11-23","2008-02-22",0,NA,0,365,
        0,2
    1,42,1,102,NA,48.0816,1,"3TC,AZT,EFV","2003-04-30","2005-11-21",1,"2006-01-11",
        0,365,0,3

Since this data is in csv format, it can be easily imported to R with `read.csv`:

    !r
    > haart <- read.csv("haart.csv")


---

HAART data frame
================

You can examine the first few lines of a large data frame using the `head` function:

    !r
    > head(haart)
      male age aids cd4baseline logvl  weight hemoglobin    init.reg  init.date
    1    1  25    0          NA    NA      NA         NA 3TC,AZT,EFV 2003-07-01
    2    1  49    0         143    NA 58.0608         11 3TC,AZT,EFV 2004-11-23
    3    1  42    1         102    NA 48.0816          1 3TC,AZT,EFV 2003-04-30
    4    0  33    0         107    NA 46.0000         NA 3TC,AZT,NVP 2006-03-25
    5    1  27    0          52     4      NA         NA 3TC,D4T,EFV 2004-09-01
    6    0  34    0         157    NA 54.8856         NA 3TC,AZT,NVP 2003-12-02
      last.visit death date.death event followup lfup pid
    1 2007-02-26     0       <NA>     0      365    0   1
    2 2008-02-22     0       <NA>     0      365    0   2
    3 2005-11-21     1 2006-01-11     0      365    0   3
    4 2006-05-05     1 2006-05-07     1       43    0   4
    5 2007-11-13     0       <NA>     0      365    0   5
    6 2008-02-28     0       <NA>     0      365    0   6

Similarly, `tail` gives the last several lines of the data frame.

---

Data Frame Structure
====================

The function `str` reveals the structure of the data frame, including the number of variables (columns) and observations (rows), as well as the data types of each column:

    !r
    > str(haart)
    'data.frame':	1000 obs. of  12 variables:
     $ male       : int  1 1 1 0 1 0 0 1 1 1 ...
     $ age        : num  25 49 42 33 27 34 39 31 52 23 ...
     $ aids       : int  0 0 1 0 0 0 0 0 0 1 ...
     $ cd4baseline: int  NA 143 102 107 52 157 65 NA NA 3 ...
     $ logvl      : num  NA NA NA NA 4 ...
     $ weight     : num  NA 58.1 48.1 46 NA ...
     $ hemoglobin : num  NA 11 1 NA NA NA 11 NA NA NA ...
     $ initreg    : Factor w/ 47 levels "3TC,ABC,AZT",..: 10 10 10 17 19 17 17 10 1 27 ...
     $ initdate   : Factor w/ 681 levels "1/1/02","1/1/03",..: 508 150 384 319 635 188 278 675 581 484 ...
     $ lastvisit  : Factor w/ 509 levels "","1/10/05","1/10/08",..: 195 189 103 352 90 201 220 167 86 468 ...
     $ death      : int  0 0 1 1 0 0 0 0 0 1 ...
     $ datedeath  : Factor w/ 113 levels "","1/11/06","1/22/04",..: 1 1 2 70 1 1 1 1 1 107 ...

Several columns were imported as a `Factor` type by default.

---

Factors
=======

The `factor` type represents variables that are categorical, or nominal. That is, they are not ordinal or rational. We can think of factors as variables whose value is one of a set of labels, with no intrinsic label relative to the other labels. Examples include:

* party membership: Republican, Democrat, Independent
* zip code
* gender
* nationality

For example, let's look at the `initreg` variable in the HAART dataset, which shows the initial drug regimen for each patient:

    !r
    > str(haart$initreg)
     Factor w/ 47 levels "3TC,ABC,AZT",..: 10 10 10 17 19 17 17 10 1 27 ...

This shows each drug combination has a label and a unique number for each combination. These numbers, however, have no intrinsic order.

---

Factors
=======

Levels may be defined that may not actually be present in the data. For example, let's generate some random data between 0 and 4, and turn it into a factor:

    !r
    > (x <- as.factor(rbinom(20, 4, 0.5)))
     [1] 3 1 2 2 1 1 2 4 1 4 2 2 3 0 2 2 2 0 1 3
    Levels: 0 1 2 3 4
    
We can redefine the levels to contain the value 5, even though it is not present:

    !r
    > levels(x) <- 0:5
    > table(x)
    x
    0 1 2 3 4 5 
    2 5 8 3 2 0 
    
However, we cannot assign values to a factor variable that is not already among the levels:

    !r
    > x[4] <- 17
    Warning message:
    In `[<-.factor`(`*tmp*`, 4, value = 17) :
      invalid factor level, NAs generated
    
---

Indexing
========

If you recall from the data structures lecture, we can *index* values from a data frame in a variety of ways. For example, if we want the ages of the first 50 observations:

    !r
    > haart$age[1:50]
     [1] 25.00000 49.00000 42.00000 33.00000 27.00000 34.00000 39.00000 31.00000
     [9] 52.00000 23.00000 49.40726 43.00000 42.00000 30.82272 37.00000 43.00000
    [17] 35.00000 33.85079 38.00000 41.00000 35.00000 39.60575 32.00000 57.00000
    [25] 29.00000 41.00000 27.00000 54.00000 42.00000 49.79877 38.00000 22.00000
    [33] 32.00000 36.00000 52.00000 31.01164 32.00000 37.00000 23.88501 32.00000
    [41] 28.00000 19.00000 29.00000 32.00000 47.52361 61.44559 50.00000 42.00000
    [49] 48.00000 24.00000
    # or equivalently ...
    > haart[[2]][1:50]
    
Multiple columns can be indexed using a vector of column names:

    !r
    > x <- haart[c("male", "age", "death")]
    > head(x)
      male age event
    1    1  25     0
    2    1  49     0
    3    1  42     0
    4    0  33     1
    5    1  27     0
    
---

Indexing
========

We can also extract particular rows according to the value of one or more variables. For example, if we are interested in the above columns for males:

    > y <- x[x$male==1,]
    > head(y)
      male age death
    1    1  25     0
    2    1  49     0
    3    1  42     0
    5    1  27     0
    8    1  31     0
    9    1  52     0

We could have combined the previous two  operations into a single call that subsets
the specified columns that correspond to male :

    > y <- haart[haart$male==1, c("male", "age", "death")]
    > head(y)
      male age death
    1    1  25     0
    2    1  49     0
    3    1  42     0
    5    1  27     0
    8    1  31     0
    9    1  52     0

---

Modifying and Creating Variables
================================

Suppose now we wish to create a derived variable, based on the values of one or more variables in the data frame. For example, we might want to refer to the number of days between the first visit (`init.date`) and the
last (`last.visit`). 

Recall from the lecture on date-time variables that in order to efficiently calculate temporal variables, we need to convert the date fields from character strings to `POSIXct` or `POXIXlt` objects.

    > haart$last.visit <- as.POSIXct(haart$last.visit)
    > haart$init.date <- as.POSIXct(haart$init.date)
    > haart$date.death <- as.POSIXct(haart$date.death)

Now we can subtract the later date from the earlier to get the time elapsed
between visits:

    > (haart$last.visit - haart$init.date)[1:50]
    Time differences in secs
     [1] 115434000 102470400  80874000   3538800 100918800 133833600 129164400
     [8] 171680400  70851600   7257600  21942000  37846800  77065200  42253200
    [15]   7344000    432000  56761200  45795600 154137600  68688000 107139600
    ...

---

Modifying and Creating Variables
================================

However, given the context of the data, we are probably interested in days elapsed between visits, rather than seconds. 

    > difftime(haart$last.visit, haart$init.date, units="days")[1:50]
    Time differences in days
     [1] 1336.04167 1186.00000  936.04167   40.95833 1168.04167 1549.00000
     [7] 1494.95833 1987.04167  820.04167   84.00000  253.95833  438.04167
    [13]  891.95833  489.04167   85.00000    5.00000  656.95833  530.04167
    ...

Even easier, since we are only interested in days, is to convert the dates to `Date` objects, which ignores time information:

    > (haart$time.diff <- as.Date(haart$last.visit) - as.Date(haart$init.date))
    Time differences in days
     [1] 1336 1186  936   41 1168 1549 1495 1987  820   84  254  438  892  489   85
    [16]    5  657  530 1784  795 1240  405 1090 1302  241 1270  413   21  286  710
    [31] 1110  265 1098 1051 1372  565  944  105  821 1169  405   24 1120 1272  693
    ...
    
---

Binning Data
============

Another common operation is the creation of variable categories from raw
values. The built-in function `cut` discretizes variables based on boundary values of the implied groups:

    > haart$age_group <- cut(haart$age, c(min(haart$age),30,50,max(haart$age)))

This creates a group for each group of ages in (18,30], (30, 50], and
(50, 89]:

    > table(haart$age_group)

     (0,30]  (30,50] (50,Inf] 
        1167     3021      442

If we wanted to use less than (rather than less than or equal to), we
could have specified `right=FALSE` to move the boundary values into the
upper group:

    > table(cut(haart$age, c(min(haart$age),30,50,max(haart$age)), 
    + right=FALSE))

    [18,30) [30,50) [50,89) 
       1011    3115     502
       
Presenter Notes
===============

For example, perhaps we want to classify subjects into age
groups, with those 30 or younger in the youngest group, those over 30
but no older than 50 in the middle group, and those over 50 in the
oldest group. 

---

Text Processing
===============

Often data will contain relevant information in the form of text that must be processed so that it can be used quantitatively, or appropriately displayed in a table or figure. Text is represented by the `character` type in R:

    !r
    > word <- 'foobar'
    > class(word)
    [1] "character"
    
Even though R considers text to be a vector of characters, indexing and other functions do not work the same way with characters:

    !r
    > word[1]
    [1] "foobar"
    > length(word)
    [1] 1
    
---

Text Processing
===============

R provides a separate set of functions to process text.

    !r
    > nchar(word)
    [1] 6
    > substr(word, 1, 3)
    [1] "foo"
    > substr(word, 3, 5)
    [1] "oba"
    
Not only can character strings be indexed, but they can be split up according to patterns in the text:

    !r
    > sentence <- "R provides a separate set of functions to process text"
    > (words <- strsplit(sentence, " "))
    [[1]]
     [1] "R"         "provides"  "a"         "separate"  "set"       "of"       
     [7] "functions" "to"        "process"   "text"     
     
This is useful for analysis of text, where individual words need to be counted, compared or evaluated. Note that this operation is reversible!

    !r
    > paste(unlist(words), collapse=" ")
    [1] "R provides a separate set of functions to process text"
    
---

Changing Case
=============

Character vectors can be changed to lower and upper case using the `tolower` and `toupper` functions:

    !r
    > toupper(word)
    [1] "FOOBAR"

Using these functions, you can create a custom function to convert to "title case":

    !r
    titlecase <- function(str) {
        str <- tolower(str)
        substr(str,1,1) <- toupper(substr(str,1,1))
        str
    }
    
    > titlecase(word)
    [1] "Foobar"
    
The `chartr` function translates characters to their corresponding pair in a text string:

    !r
    > (rna <- chartr('atcg', 'uagc', 'aagcgtctac'))
    [1] "uucgcagaug"
    
---

String Matching
===============

The function `charmatch` looks for unique matches for the elements of its first argument among those of its second.

If there is a single exact match or no exact match and a unique
partial match then the index of the matching value is returned; if
multiple exact or multiple partial matches are found then ‘0’ is
returned and if no match is found then NA is returned.

    !r
    > words
    [[1]]
     [1] "R"         "provides"  "a"         "separate"  "set"       "of"       
     [7] "functions" "to"        "process"   "text"   
    > charmatch('fun', unlist(words))
    [1] 7
    > charmatch('foo', unlist(words))
    [1] NA
    > charmatch('pr', unlist(words))
    [1] 0
 
---

Text Processing in Action
=========================

   

Now lets look at another variable that requires special treatment. The
field `init.reg` describes the initial drug regimens of each individual,
and is imported by default as a `factor`. However, each entry is in fact
a list of drugs, and is difficult to interpret as a factor per se. There
are two approaches to making this variable more usable.

First, the type of the variable can be changed to something more
sensible, such as a list. This requires a handful of steps; first, we
will convert the variable to a `character` type, and assign it
(temporarily) to an external variable:

    > init.reg <- as.character(haart$init.reg)

The reason that we do this is to take advantage of the `strsplit`
function, which takes two primary arguments, a character vector that we
wish to split up and a character string that we want to use as a
delimiter for splitting. In our case, we do the following:

    > haart$init.reg.list <- strsplit(init.reg, ",")
    > head(haart$init.reg.list)
    [[1]]
    [1] "3TC" "AZT" "EFV"

    [[2]]
    [1] "3TC" "AZT" "EFV"

    [[3]]
    [1] "3TC" "AZT" "EFV"

    [[4]]
    [1] "3TC" "AZT" "NVP"

    [[5]]
    [1] "3TC" "D4T" "EFV"

    [[6]]
    [1] "3TC" "AZT" "NVP"

Now you can see that the variable `init.reg.list` is a list, each
element of which can in turn contain an arbitrary number of elements.
So, it can accommodate regimens of different combinations of drugs. How
can we use this? Perhaps we want to know all the patients that have D4T
as part of their regimens. We can use an `apply` function to search the
`init.reg.list` variable to see if it contains the value `D4T`. For
this, we make list of the `%in%` operator, which returns `TRUE` if the
value on the left hand side of the operator is contained in the vector
on the right hand side, or `FALSE` otherwise. Using this in a function
passed to `sapply`, we get a list of `TRUE` and `FALSE` values, which
can be used to index the rows that contain D4T in their regimens:

    > haart.D4T <- haart[sapply(haart$init.reg.list, function(x) 
    + 'D4T' %in% x), ]
    > head(haart.D4T)
       male      age aids cd4baseline    logvl weight hemoglobin    init.reg  init.date last.visit death
    5     1 27.00000    0          52 4.000000     NA         NA 3TC,D4T,EFV 2004-09-01 2007-11-13     0
    16    0 43.00000    1          49       NA     NA    3.00000 3TC,D4T,NVP 2004-06-07 2004-06-12     1
    18    1 33.85079    1           4       NA     64   12.00000 3TC,D4T,EFV 2004-10-12 2006-03-26     0
    25    0 29.00000   NA          25 4.463878     44         NA 3TC,D4T,EFV 2006-08-15 2007-04-13     0
    30    0 49.79877    1         207       NA     36   10.66667 3TC,D4T,EFV 2004-03-05 2006-02-13     0
    38    0 37.00000    1          NA       NA     NA   12.80000 3TC,D4T,NVP 2007-09-05 2007-12-19     0
       date.death event followup lfup pid male_factor init.reg.list
    5        <NA>     0      365    0   5        Male 3TC, D4T, EFV
    16 2004-06-12     1        5    0  16      Female 3TC, D4T, NVP
    18       <NA>     0      365    0  18        Male 3TC, D4T, EFV
    25       <NA>     0      241    0  25      Female 3TC, D4T, EFV
    30       <NA>     0      365    0  30      Female 3TC, D4T, EFV
    38       <NA>     0      105    0  38      Female 3TC, D4T, NVP

Another (slightly more complicated) way to transform `init.reg` is to
break it into multiple columns of indicators, which specify whether each
drug is in that individual's regimen. The first step here is to obtain a
unique list of all the drugs in all the regimens. Recall the function
`unlist`, which takes all the list elements and concatenates them
together. We can use this to get a non-unique vector of drugs:

    > unlist(haart$init.reg.list)[1:25]
     [1] "3TC" "AZT" "EFV" "3TC" "AZT" "EFV" "3TC" "AZT" "EFV" "3TC" "AZT" "NVP" "3TC" "D4T" "EFV" "3TC"
    [17] "AZT" "NVP" "3TC" "AZT" "NVP" "3TC" "AZT" "EFV" "3TC"

Now, we use the function `unique` to extract the unique items within
this vector, which comprises a list of all the drugs:

    > (all_drugs <- unique(unlist(haart$init.reg.list)))
     [1] "3TC"         "AZT"         "EFV"         "NVP"         "D4T"         "ABC"         "DDI"        
     [8] "IDV"         "LPV"         "RTV"         "SQV"         "FTC"         "TDF"         "DDC"        
    [15] "NFV"         "T20"         "ATV"         "FPV"         "TPV"         "DLV"         "HIDROXIUREA"
    [22] "APV"

Now that we have all the drugs, we want a logical vector for each drug
that identifies its inclusion for each individual. We have already seen
how to do this, for D4T:

    > sapply(haart$init.reg.list, function(x) 'D4T' %in% x)[1:25]
     [1] FALSE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE
    [17] FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE

What we need to do now is generalize this by writing a function that
performs this operation for each drug in turn. This can be done in one
line:

    > for (drug in all_drugs) sapply(haart$init.reg.list, 
    + function(x) drug %in% x)

Notice that when you run this function, nothing is returned. This is
because we have not assigned the resulting vectors to variables, nor
have we specified that they be printed to the screen. Lets turn them
into a data frame of their own. We can use the function `cbind`, which
stands for "column bind", concatenating vectors together column-wise.
Lets create an empty vector to hold these, then include `cbind` in the
loop, adding each logical vector as it is created:

    > reg_drugs <- c()
    > for (drug in all_drugs) reg_drugs <- cbind(reg_drugs,
    + sapply(haart$init.reg.list, function(x) drug %in% x))
    > head(reg_drugs)
         [,1]  [,2]  [,3]  [,4]  [,5]  [,6]  [,7]  [,8]  [,9] [,10] [,11] [,12] [,13] [,14] [,15] [,16]
    [1,] TRUE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [2,] TRUE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [3,] TRUE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [4,] TRUE  TRUE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [5,] TRUE FALSE  TRUE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
    [6,] TRUE  TRUE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
         [,17] [,18] [,19] [,20] [,21] [,22]
    [1,] FALSE FALSE FALSE FALSE FALSE FALSE
    [2,] FALSE FALSE FALSE FALSE FALSE FALSE
    [3,] FALSE FALSE FALSE FALSE FALSE FALSE
    [4,] FALSE FALSE FALSE FALSE FALSE FALSE
    [5,] FALSE FALSE FALSE FALSE FALSE FALSE
    [6,] FALSE FALSE FALSE FALSE FALSE FALSE

Turning this into a data frame is as simple as a call to `data.frame`,
using `all_drugs` as a set of column labels:

    > reg_drugs.df <- data.frame(reg_drugs)
    > names(reg_drugs.df) <- all_drugs

Of course, we really want these variables to be part of our full data
set, so we can again use `cbind` to merge them into a single data frame:

    > haart_merged <- cbind(haart, reg_drugs.df)

---

Missing Values
==============



---

Subsetting
==========

---

Merging Data Frames
===================



---

Attaching Data Frames
=====================

---

The `apply` Functions
=====================

---

Sorting
=======

