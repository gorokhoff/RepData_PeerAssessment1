# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

1. Unzip and load data

```r
unzip("activity.zip")
ds<-read.csv("activity.csv")
summary(ds)
```

```
##      steps                date          interval     
##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
##  Median :  0.00   2012-10-03:  288   Median :1177.5  
##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
##  NA's   :2304     (Other)   :15840
```

Explore for NA values and remove them

```r
tds<-na.omit(ds)
```

## What is mean total number of steps taken per day?


Summarizing total number of steps per day and make histogram

```r
library(sqldf)
t1<-sqldf("select sum(steps) as step_sum, date from tds group by date")
hist(t1$step_sum, col = "red", xlab = "Total steps per day", main = "Steps per day")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 


The mean value is 1.0766189\times 10^{4} and median is 10765

## What is the average daily activity pattern?

Average daily activity are on the next plot


```r
t2<-sqldf("select avg(steps) as step_avg, interval from tds group by interval")
mint<-t2[t2$step_avg == max(t2$step_avg),]$interval
plot(t2$interval, t2$step_avg,  type = 'l', xlab = '5-minute interval', ylab = 'Average steps',
     main = 'Average number of steps taken, averaged across all days')
```

![](PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

And the maximum average value of steps are in 835 interval

## Imputing missing values
There are some missing values in data. At first we just calculate them:

```r
colSums(is.na(ds))
```

```
##    steps     date interval 
##     2304        0        0
```

And second, fill in with some values


## Are there differences in activity patterns between weekdays and weekends?

To explore this difference we have to convert Date column from our dataset to Date format. Then we extract dayweeks from date and create weekday/weekend indicator


```r
tds$date<-as.Date(tds$date)
tds$wd<-format(tds$date,'%w')
tds$weekday <- ifelse((tds$wd  == 6 | tds$wd  == 0), 'weekend', 'weekday' )
tds$weekday <- as.factor(tds$weekday)
```
