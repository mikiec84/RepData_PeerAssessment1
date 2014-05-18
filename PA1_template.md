# Reproducible Research: Peer Assessment 1

```r
opts_chunk$set(echo = TRUE)
```

## Loading and preprocessing the data

```r
# working directory is set to repo as RStudio project.
data <- read.csv(file = "activity.csv")  # load data
data$date <- as.Date(data$date)  # convert format into date
```

## What is mean total number of steps taken per day?

```r
# aggregate/sum the steps for each day
dataDay <- aggregate(steps ~ date, data = data, FUN = "sum")
# histogram for the total steps per day
hist(dataDay$steps, main = "Histogram of total steps per day", xlab = "steps per day")
```

![plot of chunk histogram](figure/histogram.png) 

```r
# calculate mean
meanSteps <- mean(dataDay$steps)
# calculate median
medianSteps <- median(dataDay$steps)
```

The *mean* of the total steps taken per day is 10766.  
The *median* of the total steps taken per day is 10765.

## What is the average daily activity pattern?


```r
# calculate the average for each time intervall
dataInterval <- aggregate(steps ~ interval, data = data, FUN = "mean")
# make time sereis plot
plot(dataInterval$interval, dataInterval$steps, type = "l", main = "Time Series Plot", 
    xlab = "Interval", ylab = "Average steps per interval")
```

![plot of chunk timeseries](figure/timeseries.png) 

```r
# calculate the inverall with maximum averagesteps
maxStepsInt <- dataInterval[dataInterval$steps == max(dataInterval$steps), "interval"]
```


The interval 835 has the maximum of average steps.

## Imputing missing values

```r
# calculate number of missing values
missValues <- table(is.na(data$steps))[2]
# manipulate data and replace NAs with mean
dataMan <- data
# number intervals per day
intDay <- nrow(subset(data, day = "2012-10-01"))
dataMan[is.na(dataMan$steps), "steps"] <- meanSteps/intDay
# aggregate/sum the steps for each day
dataManDay <- aggregate(steps ~ date, data = dataMan, FUN = "sum")
# histogram for the total steps per day
hist(dataManDay$steps, main = "Histogram of total steps per day", xlab = "steps per day")
```

![plot of chunk missingValues](figure/missingValues.png) 

```r
# calculate mean
meanStepsMan <- mean(dataManDay$steps)
# calculate median
medianStepsMan <- median(dataManDay$steps)
# difference in mean
meanStepsMan - meanSteps
```

```
## [1] -1389
```

```r
# difference in median
medianStepsMan - medianSteps
```

```
## [1] -370
```

The mean of the steps from the manipulated data set is 9377.  
The median of the steps from the manipulated data set is 10395.
## Are there differences in activity patterns between weekdays and weekends?

```r
# determine weekday
dataMan$day <- weekdays(dataMan$date)
# weekday data
dataIntervalWeekday <- aggregate(steps ~ interval, data = subset(dataMan, day %in% 
    c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")), FUN = "mean")
# weekend data
dataIntervalWeekend <- aggregate(steps ~ interval, data = subset(dataMan, day %in% 
    c("Saturday", "Sunday")), FUN = "mean")
# time series plots
plot(dataIntervalWeekday$interval, dataIntervalWeekday$steps, type = "l", main = "Time Series Plot - Weekday", 
    xlab = "Interval", ylab = "Average steps per interval")
```

![plot of chunk weekdays](figure/weekdays1.png) 

```r
plot(dataIntervalWeekend$interval, dataIntervalWeekend$steps, type = "l", main = "Time Series Plot - Weekend", 
    xlab = "Interval", ylab = "Average steps per interval")
```

![plot of chunk weekdays](figure/weekdays2.png) 

