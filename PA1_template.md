Reproducible Data Assignment
----------------------------

Loading and preprocessing the data
==================================

    library(dplyr)
    library(ggplot2)
    library(tidyr)

Reading Data

    setwd("C:/Users/shaban/Desktop/Coursera/R_projects/Course2_week2/")

    # read data -----
    features <- read.csv("activity.csv", header = TRUE)

    t1 <- features %>%
            select(steps:interval) %>%
            mutate(date = as.Date(date, format = "%Y-%m-%d"))

What is mean total number of steps taken per day?
=================================================

Calculate the total number of steps taken per day and make a histogram
of the total number of steps taken each day

    t2 <- t1 %>%
            group_by(date) %>%
            summarise(steps = sum(steps, na.rm = FALSE))

    ggplot(t2, aes(date, steps))+
            geom_col() + 
            xlab("Dates Range for Steps Collection")+
            ylab("Total Steps per Day")  

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

What is the mean total number of steps taken per day?

    mean(t2$steps, na.rm = TRUE) 

    ## [1] 10766.19

What is the median total number of steps taken per day?

    median(t2$steps, na.rm = TRUE) 

    ## [1] 10765

What is the average daily activity pattern?
===========================================

    t3 <- t1 %>%
            group_by(interval) %>%
            summarise(steps = mean(steps, na.rm = TRUE))

    ggplot(t3, aes(interval, steps))+
            geom_line() + 
            xlab("5 min interval")+
            ylab("Mean Steps")  

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)

Which 5-minute interval, on average across all the days in the dataset,
contains the maximum number of steps?

    maxInterval <- t3 %>%
            filter(steps == max(steps)) %>%
            select(interval)

    paste(maxInterval)

    ## [1] "835"

Imputing missing values
=======================

Calculate and report the total number of missing values in the dataset
(i.e. the total number of rows with NAs)

    sum(is.na(t1$steps))

    ## [1] 2304

Devise a strategy for filling in all of the missing values in the
dataset. The strategy does not need to be sophisticated. For example,
you could use the mean/median for that day, or the mean for that
5-minute interval, etc.

Create a new dataset that is equal to the original dataset but with the
missing data filled in.

    new_data <- t1 %>%
            left_join(select(t3, interval, steps), by=c("interval" = "interval")) %>%
            mutate(stepsNew = ifelse(is.na(steps.x), steps.y, steps.x)) %>%
            select(-c(steps.x, steps.y))

Make a histogram of the total number of steps taken each day and
Calculate and report the mean and median total number of steps taken per
day. Do these values differ from the estimates from the first part of
the assignment? What is the impact of imputing missing data on the
estimates of the total daily number of steps?

    t4 <- new_data %>%
            group_by(date) %>%
            summarise(steps = sum(stepsNew))

    ggplot(t4, aes(date, steps))+
            geom_col() + 
            xlab("Dates Range for Steps Collection")+
            ylab("Total Steps per Day") 

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-10-1.png)

What is the mean total number of steps taken per day?

    mean(t4$steps) 

    ## [1] 10766.19

What is the median total number of steps taken per day?

    median(t4$steps, na.rm = TRUE) 

    ## [1] 10766.19

Are there differences in activity patterns between weekdays and weekends?
-------------------------------------------------------------------------

Create a new factor variable in the dataset with two levels - "weekday"
and "weekend" indicating whether a given date is a weekday or weekend
day.

    t5 <- new_data %>%
            mutate(dayofweek = ifelse(as.POSIXlt(date)$wday == 6 | 
                                              as.POSIXlt(date)$wday == 0,"weekend","weekday"))

Make a panel plot containing a time series plot (i.e. type = "l") of the
5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis). See the
README file in the GitHub repository to see an example of what this plot
should look like using simulated data.

    t6 <- t5 %>%
            group_by(interval, dayofweek) %>%
            summarise(stepsNew = mean(stepsNew))

    ggplot(t6, aes(interval, stepsNew))+
            geom_line() + 
            facet_wrap(~dayofweek, nrow=2) +
            xlab("5 min interval")+
            ylab("Mean Steps")  

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-14-1.png)
