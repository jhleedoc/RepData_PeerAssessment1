---
title: "PA1_template"
author: "Edoctor"
date: "2017년 6월 20일"
output: html_document
---


### Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. `read.csv()`)

2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
URL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
destfile <- "activity.zip"
download.file(URL, destfile)
file <- unzip(destfile)
activity <- read.csv(file, head = TRUE, sep = ",")
```

### What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in
the dataset.

1. Make a histogram of the total number of steps taken each day

```r
library(ggplot2)
library(doBy)
hist_steps <- summaryBy(steps ~ date, data = activity, FUN = sum)
head(hist_steps)
```

```
##         date steps.sum
## 1 2012-10-01        NA
## 2 2012-10-02       126
## 3 2012-10-03     11352
## 4 2012-10-04     12116
## 5 2012-10-05     13294
## 6 2012-10-06     15420
```

```r
ggplot(data = hist_steps, aes(x = steps.sum)) + geom_histogram(binwidth = 2500, fill = "green", colour = "black") + 
        ggtitle("Total number of steps per day")+
        xlab("Total number of steps") +
        ylab("Frequency")
```

```
## Warning: Removed 8 rows containing non-finite values (stat_bin).
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png)

2. Calculate and report the **mean** and **median** total number of steps taken per day

```r
mean(hist_steps$steps, na.rm = T)
```

```
## [1] 10766.19
```

```r
median(hist_steps$steps, na.rm = T)
```

```
## [1] 10765
```

### What is the average daily activity pattern?

1. Make a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
activity_interval <- summaryBy(steps ~ interval, data = activity, FUN = mean, na.rm = TRUE)
head(activity_interval)
```

```
##   interval steps.mean
## 1        0  1.7169811
## 2        5  0.3396226
## 3       10  0.1320755
## 4       15  0.1509434
## 5       20  0.0754717
## 6       25  2.0943396
```

```r
ggplot(data = activity_interval, aes(x = interval, y = steps.mean)) + 
        geom_line(colour = "blue") +
        ggtitle("Average number of steps taken")+
        xlab("The 5-minute interval") +
        ylab("Average of steps taken across all days")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png)

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
max_number <- activity_interval$interval[which.max(activity_interval$steps.mean)]
max_number
```

```
## [1] 835
```

### Imputing missing values

Note that there are a number of days/intervals where there are missing
values (coded as `NA`). The presence of missing days may introduce
bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with `NA`s)

```r
sum(is.na(activity))
```

```
## [1] 2304
```

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
new_dataset <- activity
new_dataset$steps[is.na(new_dataset$steps)] <- mean(new_dataset$steps, na.rm = T)
sum(is.na(new_dataset))
```

```
## [1] 0
```

4. Make a histogram of the total number of steps taken each day and Calculate and report the **mean** and **median** total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
new_data_his <- summaryBy(steps ~ date, data = new_dataset, FUN = sum)
head(new_data_his)
```

```
##         date steps.sum
## 1 2012-10-01  10766.19
## 2 2012-10-02    126.00
## 3 2012-10-03  11352.00
## 4 2012-10-04  12116.00
## 5 2012-10-05  13294.00
## 6 2012-10-06  15420.00
```

```r
ggplot(data = new_data_his, aes(x = steps.sum)) + geom_histogram(binwidth = 2500, fill = "yellow", colour = "black") + 
        ggtitle("Total number of steps per day(after missing valuses are imputed)")+
        xlab("Total number of steps") +
        ylab("Frequency")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png)

```r
mean(new_data_his$steps.sum)
```

```
## [1] 10766.19
```

```r
median(new_data_his$steps.sum)
```

```
## [1] 10766.19
```
* I imputed missing values with mean. So after replacing the mean is the same but the median is different.

### Are there differences in activity patterns between weekdays and weekends?

For this part the `weekdays()` function may be of some help here. Use
the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```r
activity$day_type <- weekdays(as.Date(activity$date))
activity$day_type <- ifelse(activity$day_type == "토요일" | activity$day_type =="일요일", "Weekend", "Weekday")
mean_type <- summaryBy(steps ~ interval + day_type, data = activity, FUN = mean, na.rm = T)
```


1. Make a panel plot containing a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using **simulated data**:


```r
library(lattice)
xyplot(steps.mean ~ interval | day_type, data = mean_type, layout = c(1,2), type = "l", main= " Average number of steps", ylab = "Average number of Steps")
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png)

