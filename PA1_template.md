# Reproducible Research: Peer Assessment 1



## Loading and preprocessing the data


```r
if (!file.exists("activity.csv")) { unzip("activity.zip") }
data <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?

As a first step, we will compute the number f steps for each day.


```r
steps_per_day <- tapply(data$steps, data["date"], sum, na.rm=TRUE)
```

Let's take a look at a histogram of total steps per day:


```r
hist(steps_per_day, col="green", breaks=10, xlab="steps per day")
```

![](figure/unnamed-chunk-3-1.png)<!-- -->

Computing the mean:

```r
mean(steps_per_day, na.rm=TRUE)
```

```
## [1] 9354.23
```

Computing the median:

```r
median(steps_per_day, na.rm=TRUE)
```

```
## [1] 10395
```

## What is the average daily activity pattern?


```r
avg_steps_per_interval <- tapply(data$steps, data$interval, mean, na.rm=TRUE)
plot(names(avg_steps_per_interval), avg_steps_per_interval, type="l", xlab="interval", ylab="average steps", col="blue")
```

![](figure/unnamed-chunk-6-1.png)<!-- -->


```r
names(which.max(avg_steps_per_interval))
```

```
## [1] "835"
```

## Imputing missing values


```r
sum(is.na(data$steps))
```

```
## [1] 2304
```


```r
sum(is.na(data$date))
```

```
## [1] 0
```


```r
sum(is.na(data$interval))
```

```
## [1] 0
```


```r
data$steps_fixed <- ifelse(is.na(data$steps), avg_steps_per_interval[as.character(data$interval)], data$steps)
```


```r
steps_per_day_fixed <- tapply(data$steps_fixed, data["date"], sum)
hist(steps_per_day_fixed, col="green", breaks=10, xlab="steps per day")
```

![](figure/unnamed-chunk-12-1.png)<!-- -->

Computing the mean:

```r
mean(steps_per_day_fixed)
```

```
## [1] 10766.19
```

Computing the median:

```r
median(steps_per_day_fixed)
```

```
## [1] 10766.19
```

## Are there differences in activity patterns between weekdays and weekends?


```r
data$day_type <- factor(ifelse(weekdays(as.Date(data$date)) %in% c("Sunday", "Saturday"), "weekend", "weekday"))
```


```r
data_splitted <- split(data, data$day_type)
steps_weekend <- tapply(data_splitted$weekend$steps_fixed, data_splitted$weekend$interval, mean)
steps_weekday <- tapply(data_splitted$weekday$steps_fixed, data_splitted$weekday$interval, mean)
par(mfrow=c(2,1))
y_limits <- c(min(steps_weekend, steps_weekday), c(max(steps_weekend, steps_weekday)))
plot(names(steps_weekend), steps_weekend, type="l", xlab="interval", ylab="average steps", col="blue", main="weekend", ylim=y_limits)
plot(names(steps_weekday), steps_weekday, type="l", xlab="interval", ylab="average steps", col="blue", main="weekday", ylim=y_limits)
```

![](figure/unnamed-chunk-16-1.png)<!-- -->
