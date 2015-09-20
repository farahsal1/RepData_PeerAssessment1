---
title: "Peer Assessment 1"
author: "Farah Salahuddin"
date: "September 20, 2015"
output: html_document
---



#Peer Assessment 1
##Loading and transforming the data


```r
 setwd("C:/Users/farah_2/Desktop/ReproResearch")
 getwd()
```

```
## [1] "C:/Users/farah_2/Desktop/ReproResearch"
```

```r
data<-read.csv("activity.csv", sep=",", header=TRUE)
data$date<-as.Date(data$date)
```



##Mean number of steps taken per day
Ignoring missing values of the data set, this code calculates the total number of steps taken per day, using aggregate() function. 
It then goes on to create a histogram of the total number of steps taken per day.


```r
q1<-aggregate(steps~date,data=data,sum)
hist(q1$steps, main="Total Number of Steps Taken per day", xlab="Steps", ylab="Frequency", col="red")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2-1.png) 

This is the solution for the third part related to total number of steps per day. Here mean and median of the total number of steps taken per day is calculated.

```r
m<-mean(q1$steps)
print(m)
```

```
## [1] 10766.19
```

```r
n<-median(q1$steps)
print(n)
```

```
## [1] 10765
```
It can be seen that mean is different from the median here.

##Average Daily Activity Pattern
Here, first the average number of steps across all days were calculated for each given interval using the aggregate() function. Then a time series plot was created which plotted these average step values against their respective intervals.



```r
q2<-aggregate(steps~interval, data=data, mean)
plot(q2$interval, q2$steps,main="Average number of steps by interval", type="l", xlab="Intervals",ylab="Average Steps")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4-1.png) 

I then found out the interval corresponding to the maximum number of steps taken.


```r
q2$interval[q2$step==max(q2$step)]
```

```
## [1] 835
```

##Imputing missing values
The first part related to this task called for calculating the total number of rows with missing values. These are calculated here:


```r
q3<-data[is.na(data)==TRUE,]
nrow(q3)
```

```
## [1] 2304
```

The strategy for filling in the missing values is to replace those with the mean number of steps across all days, for a given interval. It goes like this: I used the aggregate() function to calculate the mean number of steps across all days for each interval (calculated earlier), I then went on to merge this new data set with the original data such that there is additional column for each interval showing the mean value.
I then used the ifelse() function to create a third variable which would pick and choose either the actual number of steps in the data set or the mean value in the additional column, based on whether there was an NA or not. In this way, all the rows with missing values were replaced with mean of the steps across all days per interval.

Here is the code:

```r
newdf<-merge(data,q2,by="interval")
newdf$steps<-ifelse(is.na(newdf$steps.x)==TRUE,newdf$steps.y,newdf$steps.x)
newdf<-newdf[,c("interval","steps","date")]
```

The fourth part of this task is for calculating the mean and median of the total number of steps per day. There is also a histogram showing total number of steps taken per day based on new data, which does not have any missing values.

```r
g2<-aggregate(steps~date,newdf,sum)
hist(g2$steps, main="Total Number of Steps Taken per day", xlab="Steps", ylab="Frequency", col="red")
```

![plot of chunk unnamed-chunk-8](figure/unnamed-chunk-8-1.png) 

```r
m2<-mean(g2$steps)
print(m2)
```

```
## [1] 10766.19
```

```r
n2<-median(g2$steps)
print(n2)
```

```
## [1] 10766.19
```
We can see that both mean and median are same here, and the same as the mean of the dataset with missing values. 

##Differences in activity patterns between weekdays and weekends
The last part creates a factor variable separating dates into weekdays or weekends.

```r
data$w<-weekdays(data$date,abbreviate=FALSE)
data$weekend<-ifelse(data$w=="Saturday","Weekend",
		ifelse(data$w=="Sunday","Weekend",
			"Weekday"))
data$weekend<-as.factor(data$weekend)
```

Then, a panel plot is created showing the average number of steps across all days per interval, separated by weekday and weekend.

```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.1.3
```

```r
g<-ggplot(data,(aes(interval,steps)))
g+geom_line(size=0.5, col="red")+facet_grid(weekend~.)+stat_summary(fun.steps="mean",geom="line")+ggtitle("Average number of steps taken,averaged across weekdays and weekend days")
```

```
## Warning: Removed 1728 rows containing missing values (stat_summary).
```

```
## Warning: Removed 576 rows containing missing values (stat_summary).
```

```
## Warning: Removed 2 rows containing missing values (geom_path).
```

![plot of chunk unnamed-chunk-10](figure/unnamed-chunk-10-1.png) 


knit2html()
