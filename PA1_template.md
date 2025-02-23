<!-- rmarkdown v1 -->
---
title: "PA1_template.Rmd"
author: "Courtney Rowlands"
date: "9/17/2019"
output: 
  html_document:
    keep_md: yes
pdf_document: default
---






```r
## Loading and preprocessing the data
## Load the data (i.e. `read.csv()`)
f_activity <- read.csv("data/activity.csv",sep=",",na.strings="NA")
f_activity$date <- as.Date(f_activity$date)
```

```r
## What is mean total number of steps taken per day?

totalSteps <- aggregate(steps~date,f_activity,sum)
```


```r
## Make a histogram of the total number of steps taken each day
histogram(totalSteps$steps,breaks=20,main="Total Steps (per day)",
     xlab="Steps",ylab="Frequency",col=c("000000","#1C1C1C","#383838",
                                         "#555555","#717171","#8D8D8D","#AAAAAA","#C6C6C6","#E2E2E2","#FFFFFF"))
```

![plot of chunk stepsHistogram](figure/stepsHistogram-1.png)


```r
## Calculate and report the **mean** and **median** total number of steps taken per day
meanSteps <- mean(totalSteps$steps)
print(meanSteps) 
```

```
## [1] 10766.19
```

```r
medianSteps <- median(totalSteps$steps)
print(medianSteps) 
```

```
## [1] 10765
```

```r
## What is the average daily activity pattern?
dailyPattern <- aggregate(steps~interval,f_activity,mean)
```

```r
## Make a time series plot (i.e. `type = "l"`) of the 5-minute interval (x-axis) and the 
## average number of steps taken, averaged across all days (y-axis)
plot(dailyPattern$interval,dailyPattern$steps,type="l", 
     main="Total Steps (5-minute intervals)",xlab="5-Minute Interval", ylab="Steps")
```

![plot of chunk timeSeriesPlot](figure/timeSeriesPlot-1.png)


```r
## Which 5-minute interval, on average across all the days in the dataset, contains the 
## maximum number of steps?
maxInterval <- dailyPattern[which.max(dailyPattern$steps),1]
print(maxInterval) #[1] 835
```

```
## [1] 835
```


```r
## Calculate and report the total number of missing values in the dataset (i.e. the total 
## number of rows with `NA`s)
totalMissing <- sum(!complete.cases(f_activity))
print(totalMissing) #[1] 2304
```

```
## [1] 2304
```


```r
## Devise a strategy for filling in all of the missing values in the dataset. The strategy 
## does not need to be sophisticated. For example, you could use the mean/median for that day, 
## or the mean for that 5-minute interval, etc.
newActivityData <- f_activity
retrieveNA <- is.na(newActivityData$steps)
meanInterval <- tapply(newActivityData$steps,newActivityData$interval,mean,na.rm=TRUE)
newActivityData$steps[retrieveNA] <- meanInterval[as.character(newActivityData$interval[retrieveNA])]

## Create a new dataset that is equal to the original dataset but with the missing data filled in.
newSteps <- aggregate(steps~date,newActivityData,sum)
newMeanSteps <- mean(newSteps$steps)
print(newMeanSteps) #[1] 10766.19
```

```
## [1] 10766.19
```

```r
newMedianSteps <- median(newSteps$steps)
print(newMedianSteps) #[1] 10766.19
```

```
## [1] 10766.19
```


```r
## Make a histogram of the total number of steps taken each day and Calculate and report the **mean** 
## and **median** total number of steps taken per day. Do these values differ from the estimates 
## from the first part of the assignment? What is the impact of imputing missing data on the estimates 
## of the total daily number of steps?
histogram(newSteps$steps,breaks=20,main="Total Steps (per day)",
     xlab="Steps",ylab="Frequency",col=c("000000","#1C1C1C","#383838",
                                         "#555555","#717171","#8D8D8D","#AAAAAA","#C6C6C6","#E2E2E2","#FFFFFF"))
```

![plot of chunk newStepsHistogram](figure/newStepsHistogram-1.png)
  

```r
## Are there differences in activity patterns between weekdays and weekends?
## Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" 
## indicating whether a given date is a weekday or weekend day.
weekdays <- c("Monday","Tuesday","Wednesday","Thursday","Friday")
newActivityData$weekend = as.factor(ifelse(is.element(weekdays(as.Date(newActivityData$date)),
                                                  weekdays),"Weekday","Weekend"))
determinePattern <- aggregate(steps~interval+weekend,newActivityData,mean)


## Make a panel plot containing a time series plot (i.e. `type = "l"`) of the 5-minute interval 
## (x-axis) and the average number of steps taken, averaged across all weekday days or weekend 
## days (y-axis). 
xyplot(determinePattern$steps ~ determinePattern$interval|determinePattern$weekend, 
     main="Steps (5-minute intervals)",xlab="Interval",ylab="Steps",layout=c(1,2),type="spline",
     col=c("#1C1C1C","#383838"))
```

![plot of chunk weekend](figure/weekend-1.png)

