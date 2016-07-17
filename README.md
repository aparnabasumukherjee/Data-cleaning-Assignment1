# Data-cleaning-Assignment1
creating a tidy data-set using the Samsung mobile data for shoes


library(data.table)

library(dplyr)

#Read Supporting Metadata#
 
directory <- setwd("D:/R learning/dataclean/UCI HAR Dataset")

#download variable names for features#
 
featureNames <- read.table("D:/R learning/dataclean/UCI HAR Dataset/features.txt" ,,header = FALSE)

activityLabels <- read.table("D:/R learning/dataclean/UCI HAR Dataset/activity_labels.txt", header = FALSE)
 
#Read training data

subjectTrain <- read.table("D:/R learning/dataclean/UCI HAR Dataset/train/subject_train.txt", header = FALSE)

activityTrain <- read.table("D:/R learning/dataclean/UCI HAR Dataset/train/y_train.txt", header = FALSE)

featuresTrain <- read.table("D:/R learning/dataclean/UCI HAR Dataset/train/X_train.txt", header = FALSE)


#Read test data

subjectTest <- read.table("D:/R learning/dataclean/UCI HAR Dataset/test/subject_test.txt", header = FALSE)

activityTest <- read.table("D:/R learning/dataclean/UCI HAR Dataset/test/y_test.txt", header = FALSE)

featuresTest <- read.table("D:/R learning/dataclean/UCI HAR Dataset/test/X_test.txt", header = FALSE)

#Merge the training and the test sets to create one data set

subject <- rbind(subjectTrain, subjectTest)

activity <- rbind(activityTrain, activityTest)

features <- rbind(featuresTrain, featuresTest)

#Look at the properties of the above datasets

str(subject)

str(features)

str(activity)

#set names to variables

names(subject)<-c("subject")

names(activity)<- c("activity")

names(features)<- featureNames$V2

#Merge columns to get the data frame Data for all data

Data <- cbind(subject, activity, features )

#Extracts only the measurements on the mean and standard deviation for each measurement

subFeaturesNames<-featureNames$V2[ grep("mean\\(\\)|std\\(\\)", featureNames$V2)]

selectedNames<-c(as.character(subFeaturesNames), "subject", "activity" )

Data1<-subset(Data,select=selectedNames)

#Uses descriptive activity names to name the activities in the data set

names(activityLabels) <- c("activity", "levels")

merge = merge(Data1, activityLabels, by = 'activity')

#Appropriately labels the data set with descriptive variable names.
##prefix t is replaced by time
##Acc is replaced by Accelerometer
##Gyro is replaced by Gyroscope
##prefix f is replaced by frequency
##Mag is replaced by Magnitude
##BodyBody is replaced by Body

names(merge)<-gsub("^t", "time", names(merge))

names(merge)<-gsub("^f", "frequency", names(merge))

names(merge)<-gsub("Acc", "Accelerometer", names(merge))

names(merge)<-gsub("Gyro", "Gyroscope", names(merge))

names(merge)<-gsub("Mag", "Magnitude", names(merge))

names(merge)<-gsub("BodyBody", "Body", names(merge))



#creates a second data set with the average of each variable for each activity and each subject.

library(plyr)

Data2<-aggregate(. ~subject + activity, merge, mean)

Data2<-Data2[order(Data2$subject,Data2$activity),]

write.table(Data2, file = "tidydata.txt",row.name=FALSE)

#Prouduce Codebook
install.packages("knitr")
library(knitr)
knit2html("codebook.Rmd")
