# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Unpack and load activity monitoring data. Convert date column to Date type. Create a separate data set with missing values filled in. Calculate aggregations by date and 5-min interval.

```r
require(lattice);
```

```
## Loading required package: lattice
```

```r
options(scipen = 1, digits = 2);
unzip("activity.zip");
data <- read.csv("activity.csv", stringsAsFactors=FALSE);
data$date <- as.Date(data$date);
n <- dim(data[data$date == data[1, "date"],])[1] # number of 5-min intervals in a day

steps_per_day <- aggregate(steps ~ date, data, sum);
steps_per_interval <- aggregate(steps ~ interval, data, mean);
mean_steps_per_day <- mean(steps_per_day$steps);
median_steps_per_day <- median(steps_per_day$steps);

data[is.na(data$steps),"steps"] <- median_steps_per_day/n
data$day <- as.factor(ifelse(weekdays(data$date) %in% c("Saturday","Sunday"), "weekend", "weekday"));

steps_per_day2 <- aggregate(steps ~ date, data, sum);
steps_per_interval2 <- aggregate(steps ~ (interval+day), data, mean);
```

## What is mean total number of steps taken per day?


```r
with(steps_per_day, histogram(steps, main="Total number of steps in a day"));
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 
Mean number of steps per day: 10766.19
Median number of steps per day: 10765


## What is the average daily activity pattern?


```r
with(steps_per_interval, xyplot(steps ~ interval, type="l"));
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 

```r
max_interval <- steps_per_interval[order(steps_per_interval$steps, decreasing=T)[1],"interval"]
```

Maximum number of steps on average observed during interval: 835

## Imputing missing values

Thre are 0 missing values in the dataset.
Missing values are replaced with median number of steps per day divided by number of 5-min intervals in a day.

```r
with(steps_per_day2, histogram(steps, main="Total number of steps in a day"));
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

```r
mean_steps_per_day2 <- mean(steps_per_day2$steps);
median_steps_per_day2 <- median(steps_per_day2$steps);
```
Mean number of steps per day: 10766.03
Median number of steps per day: 10765


## Are there differences in activity patterns between weekdays and weekends?

```r
with(steps_per_interval2, xyplot(steps~interval|day, type="l", layout=c(1, 2)));
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 
