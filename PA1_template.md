---
title: "Reproducible Research: Peer Assessment 1"
output:
  html_document:
    keep_md: true
---

*Requires dplyr, pander and ggplot2 packages*

```r
require(dplyr)
require(ggplot2)
require(knitr)
```


## Loading and preprocessing the data
1. Show any code that is needed to Load the data (i.e. read.csv())


```r
activity <- read.csv(file = "activity.csv")
```

2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
activity$date <- as.Date.character(x = activity$date, format = "%Y-%m-%d")
```


## What is mean total number of steps taken per day?
For this part of the assignment, you can ignore the missing values in the dataset.


```r
## saves Data frame with NA Values for later filling
activityNA <- subset(x = activity, is.na(activity$steps))

activity <- activity[complete.cases(activity),]

byDay <- activity %>%
        group_by(date) %>%
        summarise(total.steps = sum(steps),
                  total.mean = mean(steps, na.rm = T),
                  total.median = median(steps[steps>0], na.rm = T))
```


1. Make a histogram of the total number of steps taken each day


```r
## using ggplot2
qplot(data = byDay, x = date, y = total.steps, geom = "col", 
      xlab = "Date", ylab = "Total Steps", main = "Total steps taken each Day")
```

![](PA1_template_files/figure-html/histPlot-1.png)<!-- -->


2. Calculate and report the mean and median total number of steps taken per day
  
  
  *has been calculated with the dplyr function*

```r
knitr::kable(byDay[,c(1,3,4)], format = "markdown")
```



|date       | total.mean| total.median|
|:----------|----------:|------------:|
|2012-10-02 |  0.4375000|         63.0|
|2012-10-03 | 39.4166667|         61.0|
|2012-10-04 | 42.0694444|         56.5|
|2012-10-05 | 46.1597222|         66.0|
|2012-10-06 | 53.5416667|         67.0|
|2012-10-07 | 38.2465278|         52.5|
|2012-10-09 | 44.4826389|         48.0|
|2012-10-10 | 34.3750000|         56.5|
|2012-10-11 | 35.7777778|         35.0|
|2012-10-12 | 60.3541667|         46.0|
|2012-10-13 | 43.1458333|         45.5|
|2012-10-14 | 52.4236111|         60.5|
|2012-10-15 | 35.2048611|         54.0|
|2012-10-16 | 52.3750000|         64.0|
|2012-10-17 | 46.7083333|         61.5|
|2012-10-18 | 34.9166667|         52.5|
|2012-10-19 | 41.0729167|         74.0|
|2012-10-20 | 36.0937500|         49.0|
|2012-10-21 | 30.6284722|         48.0|
|2012-10-22 | 46.7361111|         52.0|
|2012-10-23 | 30.9652778|         56.0|
|2012-10-24 | 29.0104167|         51.5|
|2012-10-25 |  8.6527778|         35.0|
|2012-10-26 | 23.5347222|         36.5|
|2012-10-27 | 35.1354167|         72.0|
|2012-10-28 | 39.7847222|         61.0|
|2012-10-29 | 17.4236111|         54.5|
|2012-10-30 | 34.0937500|         40.0|
|2012-10-31 | 53.5208333|         83.5|
|2012-11-02 | 36.8055556|         55.5|
|2012-11-03 | 36.7048611|         59.0|
|2012-11-05 | 36.2465278|         66.0|
|2012-11-06 | 28.9375000|         52.0|
|2012-11-07 | 44.7326389|         58.0|
|2012-11-08 | 11.1770833|         42.5|
|2012-11-11 | 43.7777778|         55.0|
|2012-11-12 | 37.3784722|         42.0|
|2012-11-13 | 25.4722222|         57.0|
|2012-11-15 |  0.1423611|         20.5|
|2012-11-16 | 18.8923611|         43.0|
|2012-11-17 | 49.7881944|         65.5|
|2012-11-18 | 52.4652778|         80.0|
|2012-11-19 | 30.6979167|         34.0|
|2012-11-20 | 15.5277778|         58.0|
|2012-11-21 | 44.3993056|         55.0|
|2012-11-22 | 70.9270833|         65.0|
|2012-11-23 | 73.5902778|        113.0|
|2012-11-24 | 50.2708333|         65.5|
|2012-11-25 | 41.0902778|         84.0|
|2012-11-26 | 38.7569444|         53.0|
|2012-11-27 | 47.3819444|         57.0|
|2012-11-28 | 35.3576389|         70.0|
|2012-11-29 | 24.4687500|         44.5|


## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)


```r
byTime <- activity %>%
        group_by(interval) %>%
        summarise(total.steps = sum(steps),
                  mean.steps = mean(steps, na.rm = T))

## using R base plotting 
with(data = byTime, plot(x = interval, y = mean.steps, type = 'l', 
                         main = "Average Steps taken Each 5 min interval", 
                         xlab = "Five Minute Interval", 
                         ylab = "Mean Steps"))
```

![](PA1_template_files/figure-html/byTime-1.png)<!-- -->

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
max.steps <- subset(x = byTime, mean.steps == max(byTime$mean.steps))
```

The **835**  five minute interval contains te maximum number of steps with **10927** steps and mean **206.1698113**.

## Imputing missing values
1. Calculate and report the total number of missing values in the dataset  
*caculated by the activityNA <- subset(x = activity, is.na(activity$steps)) code*

**Total rows with NA values is: 2304**

2. Devise a strategy for filling in all of the missing values in the dataset.


```r
## fill NA values with the Average Steps taken Each 5 min interval
## merging the NA data with the by interval data mean steps.

filledActivity <- merge(x = byTime[c(3, 1)], y = activityNA[c(2,3)])
names(filledActivity) <- c("interval", "steps", "date")
```

3. Create a new dataset that is equal to the original dataset but with the missing data filled in

```r
## binds the Filled Data Set With the original Data
newActivity <- bind_rows(activity, filledActivity)
newActivity <- arrange(.data = newActivity, newActivity$date)
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. 


```r
newByDay <- newActivity %>%
        group_by(date) %>%
        summarise(total.steps = sum(steps),
                  total.mean = mean(steps, na.rm = T),
                  total.median = median(steps[steps>0], na.rm = T))

## uses ggplot2
ggplot(data = newByDay, aes(x = date, y = total.steps))+
        geom_bar(stat="identity", fill="steelblue")+
        theme_minimal()+
        labs(title = "Total steps taken each Day")+
        labs(x = "Date", y = "Total Steps" )
```

![](PA1_template_files/figure-html/newData-1.png)<!-- -->

```r
knitr::kable(newByDay[,c(1,3,4)], format = "markdown")
```



|date       | total.mean| total.median|
|:----------|----------:|------------:|
|2012-10-01 | 37.3825996|     37.45283|
|2012-10-02 |  0.4375000|     63.00000|
|2012-10-03 | 39.4166667|     61.00000|
|2012-10-04 | 42.0694444|     56.50000|
|2012-10-05 | 46.1597222|     66.00000|
|2012-10-06 | 53.5416667|     67.00000|
|2012-10-07 | 38.2465278|     52.50000|
|2012-10-08 | 37.3825996|     37.45283|
|2012-10-09 | 44.4826389|     48.00000|
|2012-10-10 | 34.3750000|     56.50000|
|2012-10-11 | 35.7777778|     35.00000|
|2012-10-12 | 60.3541667|     46.00000|
|2012-10-13 | 43.1458333|     45.50000|
|2012-10-14 | 52.4236111|     60.50000|
|2012-10-15 | 35.2048611|     54.00000|
|2012-10-16 | 52.3750000|     64.00000|
|2012-10-17 | 46.7083333|     61.50000|
|2012-10-18 | 34.9166667|     52.50000|
|2012-10-19 | 41.0729167|     74.00000|
|2012-10-20 | 36.0937500|     49.00000|
|2012-10-21 | 30.6284722|     48.00000|
|2012-10-22 | 46.7361111|     52.00000|
|2012-10-23 | 30.9652778|     56.00000|
|2012-10-24 | 29.0104167|     51.50000|
|2012-10-25 |  8.6527778|     35.00000|
|2012-10-26 | 23.5347222|     36.50000|
|2012-10-27 | 35.1354167|     72.00000|
|2012-10-28 | 39.7847222|     61.00000|
|2012-10-29 | 17.4236111|     54.50000|
|2012-10-30 | 34.0937500|     40.00000|
|2012-10-31 | 53.5208333|     83.50000|
|2012-11-01 | 37.3825996|     37.45283|
|2012-11-02 | 36.8055556|     55.50000|
|2012-11-03 | 36.7048611|     59.00000|
|2012-11-04 | 37.3825996|     37.45283|
|2012-11-05 | 36.2465278|     66.00000|
|2012-11-06 | 28.9375000|     52.00000|
|2012-11-07 | 44.7326389|     58.00000|
|2012-11-08 | 11.1770833|     42.50000|
|2012-11-09 | 37.3825996|     37.45283|
|2012-11-10 | 37.3825996|     37.45283|
|2012-11-11 | 43.7777778|     55.00000|
|2012-11-12 | 37.3784722|     42.00000|
|2012-11-13 | 25.4722222|     57.00000|
|2012-11-14 | 37.3825996|     37.45283|
|2012-11-15 |  0.1423611|     20.50000|
|2012-11-16 | 18.8923611|     43.00000|
|2012-11-17 | 49.7881944|     65.50000|
|2012-11-18 | 52.4652778|     80.00000|
|2012-11-19 | 30.6979167|     34.00000|
|2012-11-20 | 15.5277778|     58.00000|
|2012-11-21 | 44.3993056|     55.00000|
|2012-11-22 | 70.9270833|     65.00000|
|2012-11-23 | 73.5902778|    113.00000|
|2012-11-24 | 50.2708333|     65.50000|
|2012-11-25 | 41.0902778|     84.00000|
|2012-11-26 | 38.7569444|     53.00000|
|2012-11-27 | 47.3819444|     57.00000|
|2012-11-28 | 35.3576389|     70.00000|
|2012-11-29 | 24.4687500|     44.50000|
|2012-11-30 | 37.3825996|     37.45283|
  
Do these values differ from the estimates from the first part of the assignment? **YES**   

What is the impact of imputing missing data on the estimates of the total daily number of steps? Main diference is that it adds data to `2012-10-01, 2012-10-08, 2012-11-01, 2012-11-04, 2012-11-09, 2012-11-10, 2012-11-14, 2012-11-30` days

## Are there differences in activity patterns between weekdays and weekends?
For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.


```r
## adds weekday collumn
newActivity$weekday <- weekdays(abbreviate = T, newByDay$date)
## adds factor
newActivity$period <- factor(
        ifelse(newActivity$weekday %in% c("sáb", "dom"), "weekend", "weekday"))
```

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis).


```r
qplot(x = interval, y = steps, data = newActivity, facets = period~., 
      geom = "line")
```

![](PA1_template_files/figure-html/weekdaysPlot-1.png)<!-- -->

