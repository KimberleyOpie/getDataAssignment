# getDataAssignment
Getting and Cleaning Data Peer Reviewed Assignment

The following script contains two functions that produce the two requested datasets.

1. mergeData takes the zip url as the attribute and returns the first requested dataset. Usage: named_data<-mergeData("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip")

2. tidyData takes the the dataset returned from mergeData and returns the second requested dataset. Usage: 
tidyData(named_data)

############################### Code ######################################

library(plyr)
library(dplyr,quietly=T,warn.conflicts=F)

mergeData <-function(url){
  #Merges the training and the test sets to create one data set.  
  #Extracts only the measurements on the mean and standard deviation for each measurement.  
  temp <- tempfile()
  download.file(url,temp)
  
  sfiles<-c("UCI HAR Dataset/test/subject_test.txt","UCI HAR Dataset/train/subject_train.txt")
  yfiles<-c("UCI HAR Dataset/test/y_test.txt","UCI HAR Dataset/train/y_train.txt")
  xfiles<-c("UCI HAR Dataset/test/X_test.txt","UCI HAR Dataset/train/X_train.txt")
  data<-cbind(do.call("rbind", lapply(sfiles, function(x) read.table(unz(temp,x), stringsAsFactors = FALSE,sep=" ",colClasses = ("integer"),header=FALSE))),
              do.call("rbind", lapply(yfiles, function(x) read.table(unz(temp,x), stringsAsFactors = FALSE,sep=" ",colClasses = c("integer"),header=FALSE))),
              do.call("rbind", lapply(xfiles, function(x) read.fwf(unz(temp,x), stringsAsFactors = FALSE,colClasses = c(rep("double", 6),"NULL"),header=FALSE,widths=c(rep(16,6),8720)))))
  
  #Uses descriptive activity names to name the activities in the data set    
  data[,2]<-mapvalues(data[,2],unique(data[,2]),read.table(unz(temp,"UCI HAR Dataset/activity_labels.txt"), sep=" ",stringsAsFactors = FALSE,colClasses = c("NULL","character"),header=FALSE)[1:6,])
  
  #Appropriately labels the data set with descriptive variable names.
  names(data)<-c("Subject","Activity",gsub("[^a-zA-z]","",read.table(unz(temp,"UCI HAR Dataset/features.txt"), sep=" ",stringsAsFactors = FALSE,colClasses = c("NULL","character"),header=FALSE)[1:6,]))
  
  #unlink the zip file connection
  unlink(temp)
  #return data
  data
}

tidyData<-function(data){
  #From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
  data %>%
    group_by(Subject,Activity) %>% 
    summarize(tBodyAccmeanX = mean(tBodyAccmeanX, na.rm = TRUE),tBodyAccmeanY = mean(tBodyAccmeanY, na.rm = TRUE),tBodyAccmeanZ = mean(tBodyAccmeanZ, na.rm = TRUE),tBodyAccstdX = mean(tBodyAccstdX, na.rm = TRUE),tBodyAccstdY = mean(tBodyAccstdX, na.rm = TRUE),tBodyAccstdZ = mean(tBodyAccstdX, na.rm = TRUE))  
}


named_data<-mergeData("https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip")

tidyData(named_data)

################################## end code ##########################################
