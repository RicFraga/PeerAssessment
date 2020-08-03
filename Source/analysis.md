This document is made as requested by Roger D. Peng as part of the
Coursera course **Reproducible Research**. I’m going to explain the
procedure I followed in order to answer to the assessment’s questions.

Reading the data
----------------

As a way of organizing the project files, I designed a folder named
**Data** in which I always store the data that is going to be used in
the project.

    data <- read.csv('/home/ricardo/Escritorio/PeerAssessment/Data/activity.csv')
    head(data)

    ##   steps       date interval
    ## 1    NA 2012-10-01        0
    ## 2    NA 2012-10-01        5
    ## 3    NA 2012-10-01       10
    ## 4    NA 2012-10-01       15
    ## 5    NA 2012-10-01       20
    ## 6    NA 2012-10-01       25

What is mean total number of steps taken per day?
-------------------------------------------------

The first question we are going to answer in this question is: *how many
steps were taken by day?* This can be seen in the next histogram.

![](analysis_files/figure-markdown_strict/total%20steps-1.png)

There were days in which no data was registered for the number of steps,
these were treated as if no steps were taken that day.  
The second question answered in this section is *what is the mean and
median of the numbers of steps taken each day?*

    ##          date       mean median
    ## 1  2012-10-01  0.0000000     NA
    ## 2  2012-10-02  0.4375000      0
    ## 3  2012-10-03 39.4166667      0
    ## 4  2012-10-04 42.0694444      0
    ## 5  2012-10-05 46.1597222      0
    ## 6  2012-10-06 53.5416667      0
    ## 7  2012-10-07 38.2465278      0
    ## 8  2012-10-08  0.0000000     NA
    ## 9  2012-10-09 44.4826389      0
    ## 10 2012-10-10 34.3750000      0
    ## 11 2012-10-11 35.7777778      0
    ## 12 2012-10-12 60.3541667      0
    ## 13 2012-10-13 43.1458333      0
    ## 14 2012-10-14 52.4236111      0
    ## 15 2012-10-15 35.2048611      0
    ## 16 2012-10-16 52.3750000      0
    ## 17 2012-10-17 46.7083333      0
    ## 18 2012-10-18 34.9166667      0
    ## 19 2012-10-19 41.0729167      0
    ## 20 2012-10-20 36.0937500      0
    ## 21 2012-10-21 30.6284722      0
    ## 22 2012-10-22 46.7361111      0
    ## 23 2012-10-23 30.9652778      0
    ## 24 2012-10-24 29.0104167      0
    ## 25 2012-10-25  8.6527778      0
    ## 26 2012-10-26 23.5347222      0
    ## 27 2012-10-27 35.1354167      0
    ## 28 2012-10-28 39.7847222      0
    ## 29 2012-10-29 17.4236111      0
    ## 30 2012-10-30 34.0937500      0
    ## 31 2012-10-31 53.5208333      0
    ## 32 2012-11-01  0.0000000     NA
    ## 33 2012-11-02 36.8055556      0
    ## 34 2012-11-03 36.7048611      0
    ## 35 2012-11-04  0.0000000     NA
    ## 36 2012-11-05 36.2465278      0
    ## 37 2012-11-06 28.9375000      0
    ## 38 2012-11-07 44.7326389      0
    ## 39 2012-11-08 11.1770833      0
    ## 40 2012-11-09  0.0000000     NA
    ## 41 2012-11-10  0.0000000     NA
    ## 42 2012-11-11 43.7777778      0
    ## 43 2012-11-12 37.3784722      0
    ## 44 2012-11-13 25.4722222      0
    ## 45 2012-11-14  0.0000000     NA
    ## 46 2012-11-15  0.1423611      0
    ## 47 2012-11-16 18.8923611      0
    ## 48 2012-11-17 49.7881944      0
    ## 49 2012-11-18 52.4652778      0
    ## 50 2012-11-19 30.6979167      0
    ## 51 2012-11-20 15.5277778      0
    ## 52 2012-11-21 44.3993056      0
    ## 53 2012-11-22 70.9270833      0
    ## 54 2012-11-23 73.5902778      0
    ## 55 2012-11-24 50.2708333      0
    ## 56 2012-11-25 41.0902778      0
    ## 57 2012-11-26 38.7569444      0
    ## 58 2012-11-27 47.3819444      0
    ## 59 2012-11-28 35.3576389      0
    ## 60 2012-11-29 24.4687500      0
    ## 61 2012-11-30  0.0000000     NA

As you can see, the median value is not that helpful because of all the
NAs and 0s on the data, still, the mean is indeed helpful and this
numbers compliment really good the histogram previously shown.

What is the average daily activity pattern?
-------------------------------------------

In order to observe this question I’m presenting a time series plot of
the 5 minute interval and the average numbers of steps taken averaged
across all days.

![](analysis_files/figure-markdown_strict/mean%20steps%20by%20day-1.png)

Continuing with this section I’ll show what’s the interval with the
greatest mean of steps.

    ##     interval     mean
    ## 104      835 206.1698

Imputing missing values
-----------------------

As we’ve seen, there are a lot of NAs values that may bias our
measurements means and medians. Let’s look at how many NA values are
there in the data set.

    len <- length(data$steps)
    print(paste0('Total of observations in the data set: ', len))

    ## [1] "Total of observations in the data set: 17568"

    nas <- sum(is.na(data$steps))
    print(paste0('Total of NA values in the data set: ', nas))

    ## [1] "Total of NA values in the data set: 2304"

    perc <- nas * 100 / len
    print(paste0(paste0('Percentage of NA values: ', perc), '%'))

    ## [1] "Percentage of NA values: 13.1147540983607%"

To fill in the NA values, we were told to create a strategy for imputing
this values. We are going to implement a strategy in which we fill the
gaps with the mean for that 5-minute interval.

    nas_ind <- which(is.na(data$steps))

    for(index in nas_ind){
      row_index_frame <- which(frame$interval == data[index, 'interval'])
       data$steps[index] <- frame[row_index_frame, 'mean']
    }

    print(head(data))

    ##       steps       date interval
    ## 1 1.7169811 2012-10-01        0
    ## 2 0.3396226 2012-10-01        5
    ## 3 0.1320755 2012-10-01       10
    ## 4 0.1509434 2012-10-01       15
    ## 5 0.0754717 2012-10-01       20
    ## 6 2.0943396 2012-10-01       25

As we can see from the head from the data, there are now no NA values!
Now let’s re-compute the histogram of the total of steps taken each day,
as well as the mean and median and we’ll see how different it looks.

![](analysis_files/figure-markdown_strict/imputed%20histogram-1.png)

    ##          date       mean   median
    ## 1  2012-10-01 37.3825996 34.11321
    ## 2  2012-10-02  0.4375000  0.00000
    ## 3  2012-10-03 39.4166667  0.00000
    ## 4  2012-10-04 42.0694444  0.00000
    ## 5  2012-10-05 46.1597222  0.00000
    ## 6  2012-10-06 53.5416667  0.00000
    ## 7  2012-10-07 38.2465278  0.00000
    ## 8  2012-10-08 37.3825996 34.11321
    ## 9  2012-10-09 44.4826389  0.00000
    ## 10 2012-10-10 34.3750000  0.00000
    ## 11 2012-10-11 35.7777778  0.00000
    ## 12 2012-10-12 60.3541667  0.00000
    ## 13 2012-10-13 43.1458333  0.00000
    ## 14 2012-10-14 52.4236111  0.00000
    ## 15 2012-10-15 35.2048611  0.00000
    ## 16 2012-10-16 52.3750000  0.00000
    ## 17 2012-10-17 46.7083333  0.00000
    ## 18 2012-10-18 34.9166667  0.00000
    ## 19 2012-10-19 41.0729167  0.00000
    ## 20 2012-10-20 36.0937500  0.00000
    ## 21 2012-10-21 30.6284722  0.00000
    ## 22 2012-10-22 46.7361111  0.00000
    ## 23 2012-10-23 30.9652778  0.00000
    ## 24 2012-10-24 29.0104167  0.00000
    ## 25 2012-10-25  8.6527778  0.00000
    ## 26 2012-10-26 23.5347222  0.00000
    ## 27 2012-10-27 35.1354167  0.00000
    ## 28 2012-10-28 39.7847222  0.00000
    ## 29 2012-10-29 17.4236111  0.00000
    ## 30 2012-10-30 34.0937500  0.00000
    ## 31 2012-10-31 53.5208333  0.00000
    ## 32 2012-11-01 37.3825996 34.11321
    ## 33 2012-11-02 36.8055556  0.00000
    ## 34 2012-11-03 36.7048611  0.00000
    ## 35 2012-11-04 37.3825996 34.11321
    ## 36 2012-11-05 36.2465278  0.00000
    ## 37 2012-11-06 28.9375000  0.00000
    ## 38 2012-11-07 44.7326389  0.00000
    ## 39 2012-11-08 11.1770833  0.00000
    ## 40 2012-11-09 37.3825996 34.11321
    ## 41 2012-11-10 37.3825996 34.11321
    ## 42 2012-11-11 43.7777778  0.00000
    ## 43 2012-11-12 37.3784722  0.00000
    ## 44 2012-11-13 25.4722222  0.00000
    ## 45 2012-11-14 37.3825996 34.11321
    ## 46 2012-11-15  0.1423611  0.00000
    ## 47 2012-11-16 18.8923611  0.00000
    ## 48 2012-11-17 49.7881944  0.00000
    ## 49 2012-11-18 52.4652778  0.00000
    ## 50 2012-11-19 30.6979167  0.00000
    ## 51 2012-11-20 15.5277778  0.00000
    ## 52 2012-11-21 44.3993056  0.00000
    ## 53 2012-11-22 70.9270833  0.00000
    ## 54 2012-11-23 73.5902778  0.00000
    ## 55 2012-11-24 50.2708333  0.00000
    ## 56 2012-11-25 41.0902778  0.00000
    ## 57 2012-11-26 38.7569444  0.00000
    ## 58 2012-11-27 47.3819444  0.00000
    ## 59 2012-11-28 35.3576389  0.00000
    ## 60 2012-11-29 24.4687500  0.00000
    ## 61 2012-11-30 37.3825996 34.11321

What a change! Everything looks a lot useful now (of course, if we
assume our imputing strategy is good enough to generate better insight
from the data).

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

![](analysis_files/figure-markdown_strict/week%20days-1.png)
