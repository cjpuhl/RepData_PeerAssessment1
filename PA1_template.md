---
title: "Reproducible Research: Peer Assessment 1"
output:
  html_document:
    keep_md: true
---

*Requires dplyr, rmarkdown, knitr and ggplot2 packages*

```r
require(dplyr)
require(knitr)
require(ggplot2)
require(rmarkdown)
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
paged_table(byDay[,c(1,3,4)])
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["date"],"name":[1],"type":["date"],"align":["right"]},{"label":["total.mean"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["total.median"],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"2012-10-02","2":"0.4375000","3":"63.0"},{"1":"2012-10-03","2":"39.4166667","3":"61.0"},{"1":"2012-10-04","2":"42.0694444","3":"56.5"},{"1":"2012-10-05","2":"46.1597222","3":"66.0"},{"1":"2012-10-06","2":"53.5416667","3":"67.0"},{"1":"2012-10-07","2":"38.2465278","3":"52.5"},{"1":"2012-10-09","2":"44.4826389","3":"48.0"},{"1":"2012-10-10","2":"34.3750000","3":"56.5"},{"1":"2012-10-11","2":"35.7777778","3":"35.0"},{"1":"2012-10-12","2":"60.3541667","3":"46.0"},{"1":"2012-10-13","2":"43.1458333","3":"45.5"},{"1":"2012-10-14","2":"52.4236111","3":"60.5"},{"1":"2012-10-15","2":"35.2048611","3":"54.0"},{"1":"2012-10-16","2":"52.3750000","3":"64.0"},{"1":"2012-10-17","2":"46.7083333","3":"61.5"},{"1":"2012-10-18","2":"34.9166667","3":"52.5"},{"1":"2012-10-19","2":"41.0729167","3":"74.0"},{"1":"2012-10-20","2":"36.0937500","3":"49.0"},{"1":"2012-10-21","2":"30.6284722","3":"48.0"},{"1":"2012-10-22","2":"46.7361111","3":"52.0"},{"1":"2012-10-23","2":"30.9652778","3":"56.0"},{"1":"2012-10-24","2":"29.0104167","3":"51.5"},{"1":"2012-10-25","2":"8.6527778","3":"35.0"},{"1":"2012-10-26","2":"23.5347222","3":"36.5"},{"1":"2012-10-27","2":"35.1354167","3":"72.0"},{"1":"2012-10-28","2":"39.7847222","3":"61.0"},{"1":"2012-10-29","2":"17.4236111","3":"54.5"},{"1":"2012-10-30","2":"34.0937500","3":"40.0"},{"1":"2012-10-31","2":"53.5208333","3":"83.5"},{"1":"2012-11-02","2":"36.8055556","3":"55.5"},{"1":"2012-11-03","2":"36.7048611","3":"59.0"},{"1":"2012-11-05","2":"36.2465278","3":"66.0"},{"1":"2012-11-06","2":"28.9375000","3":"52.0"},{"1":"2012-11-07","2":"44.7326389","3":"58.0"},{"1":"2012-11-08","2":"11.1770833","3":"42.5"},{"1":"2012-11-11","2":"43.7777778","3":"55.0"},{"1":"2012-11-12","2":"37.3784722","3":"42.0"},{"1":"2012-11-13","2":"25.4722222","3":"57.0"},{"1":"2012-11-15","2":"0.1423611","3":"20.5"},{"1":"2012-11-16","2":"18.8923611","3":"43.0"},{"1":"2012-11-17","2":"49.7881944","3":"65.5"},{"1":"2012-11-18","2":"52.4652778","3":"80.0"},{"1":"2012-11-19","2":"30.6979167","3":"34.0"},{"1":"2012-11-20","2":"15.5277778","3":"58.0"},{"1":"2012-11-21","2":"44.3993056","3":"55.0"},{"1":"2012-11-22","2":"70.9270833","3":"65.0"},{"1":"2012-11-23","2":"73.5902778","3":"113.0"},{"1":"2012-11-24","2":"50.2708333","3":"65.5"},{"1":"2012-11-25","2":"41.0902778","3":"84.0"},{"1":"2012-11-26","2":"38.7569444","3":"53.0"},{"1":"2012-11-27","2":"47.3819444","3":"57.0"},{"1":"2012-11-28","2":"35.3576389","3":"70.0"},{"1":"2012-11-29","2":"24.4687500","3":"44.5"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


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
paged_table(newByDay[,c(1,3,4)])
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["date"],"name":[1],"type":["date"],"align":["right"]},{"label":["total.mean"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["total.median"],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"2012-10-01","2":"37.3825996","3":"37.45283"},{"1":"2012-10-02","2":"0.4375000","3":"63.00000"},{"1":"2012-10-03","2":"39.4166667","3":"61.00000"},{"1":"2012-10-04","2":"42.0694444","3":"56.50000"},{"1":"2012-10-05","2":"46.1597222","3":"66.00000"},{"1":"2012-10-06","2":"53.5416667","3":"67.00000"},{"1":"2012-10-07","2":"38.2465278","3":"52.50000"},{"1":"2012-10-08","2":"37.3825996","3":"37.45283"},{"1":"2012-10-09","2":"44.4826389","3":"48.00000"},{"1":"2012-10-10","2":"34.3750000","3":"56.50000"},{"1":"2012-10-11","2":"35.7777778","3":"35.00000"},{"1":"2012-10-12","2":"60.3541667","3":"46.00000"},{"1":"2012-10-13","2":"43.1458333","3":"45.50000"},{"1":"2012-10-14","2":"52.4236111","3":"60.50000"},{"1":"2012-10-15","2":"35.2048611","3":"54.00000"},{"1":"2012-10-16","2":"52.3750000","3":"64.00000"},{"1":"2012-10-17","2":"46.7083333","3":"61.50000"},{"1":"2012-10-18","2":"34.9166667","3":"52.50000"},{"1":"2012-10-19","2":"41.0729167","3":"74.00000"},{"1":"2012-10-20","2":"36.0937500","3":"49.00000"},{"1":"2012-10-21","2":"30.6284722","3":"48.00000"},{"1":"2012-10-22","2":"46.7361111","3":"52.00000"},{"1":"2012-10-23","2":"30.9652778","3":"56.00000"},{"1":"2012-10-24","2":"29.0104167","3":"51.50000"},{"1":"2012-10-25","2":"8.6527778","3":"35.00000"},{"1":"2012-10-26","2":"23.5347222","3":"36.50000"},{"1":"2012-10-27","2":"35.1354167","3":"72.00000"},{"1":"2012-10-28","2":"39.7847222","3":"61.00000"},{"1":"2012-10-29","2":"17.4236111","3":"54.50000"},{"1":"2012-10-30","2":"34.0937500","3":"40.00000"},{"1":"2012-10-31","2":"53.5208333","3":"83.50000"},{"1":"2012-11-01","2":"37.3825996","3":"37.45283"},{"1":"2012-11-02","2":"36.8055556","3":"55.50000"},{"1":"2012-11-03","2":"36.7048611","3":"59.00000"},{"1":"2012-11-04","2":"37.3825996","3":"37.45283"},{"1":"2012-11-05","2":"36.2465278","3":"66.00000"},{"1":"2012-11-06","2":"28.9375000","3":"52.00000"},{"1":"2012-11-07","2":"44.7326389","3":"58.00000"},{"1":"2012-11-08","2":"11.1770833","3":"42.50000"},{"1":"2012-11-09","2":"37.3825996","3":"37.45283"},{"1":"2012-11-10","2":"37.3825996","3":"37.45283"},{"1":"2012-11-11","2":"43.7777778","3":"55.00000"},{"1":"2012-11-12","2":"37.3784722","3":"42.00000"},{"1":"2012-11-13","2":"25.4722222","3":"57.00000"},{"1":"2012-11-14","2":"37.3825996","3":"37.45283"},{"1":"2012-11-15","2":"0.1423611","3":"20.50000"},{"1":"2012-11-16","2":"18.8923611","3":"43.00000"},{"1":"2012-11-17","2":"49.7881944","3":"65.50000"},{"1":"2012-11-18","2":"52.4652778","3":"80.00000"},{"1":"2012-11-19","2":"30.6979167","3":"34.00000"},{"1":"2012-11-20","2":"15.5277778","3":"58.00000"},{"1":"2012-11-21","2":"44.3993056","3":"55.00000"},{"1":"2012-11-22","2":"70.9270833","3":"65.00000"},{"1":"2012-11-23","2":"73.5902778","3":"113.00000"},{"1":"2012-11-24","2":"50.2708333","3":"65.50000"},{"1":"2012-11-25","2":"41.0902778","3":"84.00000"},{"1":"2012-11-26","2":"38.7569444","3":"53.00000"},{"1":"2012-11-27","2":"47.3819444","3":"57.00000"},{"1":"2012-11-28","2":"35.3576389","3":"70.00000"},{"1":"2012-11-29","2":"24.4687500","3":"44.50000"},{"1":"2012-11-30","2":"37.3825996","3":"37.45283"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
  
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

