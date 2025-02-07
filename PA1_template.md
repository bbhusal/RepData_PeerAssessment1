# Reproducible Research-Project 1
Bikram Bhusal  
28 July, 2019

## Introduction

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement - a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

The data for this assignment can be downloaded from the course web site and the variables included in this dataset are:

steps: Number of steps taking in a 5-minute interval (missing values are coded as NA)  
date: The date on which the measurement was taken in YYYY-MM-DD format  
interval: Identifier for the 5-minute interval in which measurement was taken  

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

## Loading and preprocessing the data



```{r,echo = TRUE}
activity<-read.csv("activity.csv",header=TRUE)
str(activity)
```

## What is mean total number of steps taken per day?
Calculating the total number of steps taken per day
```{r,echo = TRUE}
steps_perday<-tapply(activity$steps,activity$date, FUN=sum)
head(steps_perday)
```
Making the histogram of total steps per day

```{r,echo = TRUE}
hist(steps_perday,col = "yellow",main="Total Steps Per Day",  xlab="Total Steps",ylab = "Frequency")
```
![GitHub Logo](/images/logo.png)
Format: ![Alt Text](https://github.com/bbhusal/RepData_PeerAssessment1/blob/master/Rplot1.png)

Calculating  the mean and median of the total number of steps taken per day
```{r,echo = TRUE}
Mean_PerDay<-mean(steps_perday,na.rm = T)
Median_PerDay<-median(steps_perday,na.rm = T)
```
```{r,echo = TRUE}
Mean_PerDay
Median_PerDay
```
## What is the average daily activity pattern?
Making a time series plot of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
```{r,echo = TRUE}
steps_PerInt<-tapply(activity$steps,activity$interval,mean,na.rm=TRUE)
plot(as.numeric(names(steps_PerInt)),steps_PerInt,main="Average Daily Activity",xlab="Interval",ylab="Steps",type="l",col="red")
```
![GitHub Logo](/images/logo.png)
Format: ![Alt Text](https://github.com/bbhusal/RepData_PeerAssessment1/blob/master/Rplot2.png)



Figuring out Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps ?
```{r,echo = TRUE}
maxA_Interval<-names(which.max(steps_PerInt))
maxA_steps<-max(steps_PerInt)
``` 
```{r,echo = TRUE}
maxA_Interval
```  
```{r,echo = TRUE}
maxA_steps
```  

Thus 835th interval contains the maximum number of steps(206.1698).

## Imputing missing values
Calculating the total number of missing values in the dataset (i.e. the total number of rows with NAs)
```{r,echo = TRUE}
sum(is.na(activity$steps))
```  

Here,calculating the mean of average steps per day across all the data(without NA's) and replace all NAs by this:
```{r,echo = TRUE}
avg_steps<-tapply(activity$steps,activity$date,mean,na.rm=TRUE)
ma<-mean(avg_steps,na.rm = TRUE)
nas<-which(is.na(activity$steps))
l<-length(nas)
## Replacing NAs by mean
for(i in 1:l){
  activity[nas[i],1]=ma
}
```  
Checking weather NAs are successfully replaced? Also see how our new dataset look like.
```{r,echo = TRUE}
sum(is.na(activity$steps))
```  
```{r,echo = TRUE}
str(activity)
```  
Yes NAs are successfully replaced my mean

Now Making a histogram of the total number of steps taken each day:
```{r,echo = TRUE}
steps_2perday<-tapply(activity$steps,activity$date, FUN=sum)
hist(steps_2perday,col = "6",main="Total Steps Per Day",  xlab="Total Steps",ylab = "Frequency")
```
![GitHub Logo](/images/logo.png)
Format: ![Alt Text](https://github.com/bbhusal/RepData_PeerAssessment1/blob/master/Rplot3.png)


And Calculating the mean and median total number of steps taken per day. 
```{r,echo = TRUE}
Mean_2PerDay<-mean(steps_2perday,na.rm = T)
Median_2PerDay<-median(steps_2perday,na.rm = T)
```
```{r,echo = TRUE}
Mean_2PerDay
```
```{r,echo = TRUE}
Median_2PerDay
```
Thus mean and median of the new dataset are 107.66 and 10766.19 respectivelly.

##Are there differences in activity patterns between weekdays and weekends?
In this section, we will use dplyr package .
```{r,echo = TRUE}
library(dplyr)
```
Now creating a new variable in the dataset named "day" that shows the day of the week in terms of weekday or weekend

```{r,echo = TRUE}
activity$day<-ifelse(weekdays(as.Date(activity$date))=="Saturday"|weekdays(as.Date(activity$date))=="Sunday","weekend","weekday")
activity$day<-as.factor(activity$day)
str(activity)
```
Plotting the weekday and weekend data in separate graphs:
```{r,echo = TRUE}
act_wknd<-subset(activity,as.character(activity$day)=="weekend")
act_wkdy<-subset(activity,as.character(activity$day)=="weekday")
steps_wknd<-with(act_wknd,tapply(steps,interval,mean,na.rm=T))
steps_wkdy<-with(act_wkdy,tapply(steps,interval,mean,na.rm=T))
int_wknd<-unique(act_wknd$interval)
int_wkdy<-unique(act_wkdy$interval)
new_wknd<-data.frame(cbind(steps_wknd,int_wknd))
new_wkdy<-data.frame(cbind(steps_wkdy,int_wkdy))
par(mfrow=c(2,1),mar=c(4,4,2,1))
plot(new_wknd$int_wknd,new_wknd$steps_wknd,type = "l",xlab = "Intervals",
     ylab = "Average Steps",main = "Weekend",col="4")
plot(new_wkdy$int_wkdy,new_wkdy$steps_wkdy,type = "l",xlab = "Intervals",
     ylab = "Average Steps",main = "Weekday",col="4")
```
![GitHub Logo](/images/logo.png)
Format: ![Alt Text](https://github.com/bbhusal/RepData_PeerAssessment1/blob/master/Rplot4.png)



From these it is clear that the average steps over the weekends show higher pattern than that of the weekend.
