---
title: "week 4 Code book"
author: "James Foster"
date: "2023-01-30"
output: html_document
editor_options: 
  chunk_output_type: console
---

This is the accompanying codebook for the getting and cleaning data module.

1.  The neccesary packages and dataset is downloaded, unzipped, and extracted into the working directory

```{packages <- c("data.table", "reshape2")}
sapply(packages, require, character.only=TRUE, quietly=TRUE)
path <- getwd()
url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(url, file.path(path, "dataFiles.zip"))
unzip(zipfile = "dataFiles.zip")
}

```

activities and the features.txt table are read into a activities and features dataframe based on the category functions and activities

```{features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n","functions"))}
activities <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))}

```

The train and test datasets are imported based on their function and the code that corresponds with their activity

```{subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")}
x_test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names = features$functions)
y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")
x_train <- read.table("UCI HAR Dataset/train/X_train.txt", col.names = features$functions)
y_train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")}
```

These tables are merged and bound using the cbind function

```{X <- rbind(x_train, x_test)}
Y <- rbind(y_train, y_test)
Subject <- rbind(subject_train, subject_test)
Merged_Data <- cbind(Subject, Y, X)}
```

2.  This data is the cleaned and the essential data of code/activity, the mean, and std dev is selected into a new dataframe "tidydata"

```{TidyData <- Merged_Data %>% select(subject, code, contains("mean"), contains("std"))}
```

3.  The column names of the tidy dataset are then renamed to make them more meaningful and consistent.

```{TidyData$code <- activities[TidyData$code, 2]}
names(TidyData)[2] = "activity"
names(TidyData)<-gsub("Acc", "Accelerometer", names(TidyData))
names(TidyData)<-gsub("Gyro", "Gyroscope", names(TidyData))
names(TidyData)<-gsub("BodyBody", "Body", names(TidyData))
names(TidyData)<-gsub("Mag", "Magnitude", names(TidyData))
names(TidyData)<-gsub("^t", "Time", names(TidyData))
names(TidyData)<-gsub("^f", "Frequency", names(TidyData))
names(TidyData)<-gsub("tBody", "TimeBody", names(TidyData))
names(TidyData)<-gsub("-mean()", "Mean", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("-std()", "STD", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("-freq()", "Frequency", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("angle", "Angle", names(TidyData))
names(TidyData)<-gsub("gravity", "Gravity", names(TidyData))}

```

4.  The final dataset is then plotted with the mean of the activites grouped by subject into a new text file

```{FinalData <- TidyData %>%}
  group_by(subject, activity) %>%
  summarise_all(funs(mean))
write.table(FinalData, "FinalData.txt", row.name=FALSE)}
```
