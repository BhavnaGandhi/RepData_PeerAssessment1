# Reproducible Research: Peer Assessment 1


```r
## Loading and preprocessing the data
data <- read.csv(file="/R_work/activity.csv")
steps_by_day <- aggregate(steps~date, data, sum)
hist(steps_by_day$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-1.png)<!-- -->

```r
## What is mean total number of steps taken per day?
rmean <- mean(steps_by_day$steps)
rmedian <- median(steps_by_day$steps)
steps_by_interval <- aggregate(steps ~ interval, data, mean)
plot(steps_by_interval$interval,steps_by_interval$steps, type="l", xlab="Interval", ylab="Number of Steps",main="Average Number of Steps per Day by Interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-2.png)<!-- -->

```r
## What is the average daily activity pattern?
max_interval <- steps_by_interval[which.max(steps_by_interval$steps),1]
incomplete <- sum(!complete.cases(data))

## Imputing missing values
imputed_data <- transform(data, steps = ifelse(is.na(data$steps), steps_by_interval$steps[match(data$interval, steps_by_interval$interval)], data$steps))
imputed_data[as.character(imputed_data$date) == "2012-10-01", 1] <- 0
steps_by_day_i <- aggregate(steps ~ date, imputed_data, sum)
hist(steps_by_day_i$steps, main = paste("Total Steps Each Day"), col="blue", xlab="Number of Steps")

## Are there differences in activity patterns between weekdays and weekends?
hist(steps_by_day$steps, main = paste("Total Steps Each Day"), col="red", xlab="Number of Steps", add=T)
legend("topright", c("Imputed", "Non-imputed"), col=c("blue", "red"), lwd=10)
rmean.i <- mean(steps_by_day_i$steps)
rmedian.i <- median(steps_by_day_i$steps)
mean_diff <- rmean.i - rmean
med_diff <- rmedian.i - rmedian
total_diff <- sum(steps_by_day_i$steps) - sum(steps_by_day$steps)
weekdays <- c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")
imputed_data$dow = as.factor(ifelse(is.element(weekdays(as.Date(imputed_data$date)),weekdays), "Weekday", "Weekend"))
steps_by_interval_i <- aggregate(steps ~ interval + dow, imputed_data, mean)
library(lattice)
```

![](PA1_template_files/figure-html/unnamed-chunk-1-3.png)<!-- -->

```r
xyplot(steps_by_interval_i$steps ~ steps_by_interval_i$interval|steps_by_interval_i$dow, main="Average Steps per Day by Interval",xlab="Interval", ylab="Steps",layout=c(1,2), type="l")
```

![](PA1_template_files/figure-html/unnamed-chunk-1-4.png)<!-- -->
