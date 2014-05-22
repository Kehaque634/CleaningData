Getting and Cleaning Data Project - Assignment

The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. You will be graded by your peers on a series of yes/no questions related to the project. You will be required to submit:

1) a tidy data set as described below,

2) a link to a Github repository with your script for performing the analysis, and

3) a code book that describes the variables, the data, and any transformations or work that you performed to clean up the data called CodeBook.md.

You should also include a README.md in the repo with your scripts. This repo explains how all of the scripts work and how they are connected.

One of the most exciting areas in all of data science right now is wearable computing - see for example this article . Companies like Fitbit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked to from the course website represent data collected from the accelerometers from the Samsung Galaxy S smartphone. A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Here are the data for the project:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

You should create one R script called run_analysis.R that does the following. Merges the training and the test sets to create one data set. Extracts only the measurements on the mean and standard deviation for each measurement.

Uses descriptive activity names to name the activities in the data set Appropriately labels the data set with descriptive activity names. Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"

if (!file.exists("Dataset.zip")) {
    download.file(fileUrl, destfile = "Dataset.zip", method = "wget")
}

Unzip the file.

if (!file.exists("UCI HAR Dataset")) {
    unzip("Dataset.zip", overwrite = TRUE)
}

read the file with white-space as delimiter

testDataset <- read.csv("./UCI HAR Dataset/test/X_test.txt", sep = "", colClasses = "numeric", 
    header = FALSE)
trainDataset <- read.csv("./UCI HAR Dataset/train/X_train.txt", sep = "", colClasses = "numeric", 
    header = FALSE)

Read the features from features.txt

featuresData <- read.csv("./UCI HAR Dataset/features.txt", sep = "", header = FALSE)

Extract the features vector from featuresData

featuresVector <- featuresData[, 2]

Add header to data.frame. Code referred from Stack-Overflow

colnames(testDataset) <- featuresVector
colnames(trainDataset) <- featuresVector

Check if the dataset is displayed correctly

# head(testDataset) head(trainDataset)

Results Look Good

Now lets add features

Features Vector

testY <- read.csv("./UCI HAR Dataset/test/y_test.txt", sep = "", header = FALSE)
trainY <- read.csv("./UCI HAR Dataset/train/y_train.txt", sep = "", header = FALSE)

Assign Header

colnames(testY) <- "activityLabel"
colnames(trainY) <- "activityLabel"

Activity Label Data

activityLabels <- read.csv("./UCI HAR Dataset//activity_labels.txt", sep = "", 
    header = FALSE)

colnames(activityLabels) <- c("activityLabel", "activity")

merge activity with labels

activityTest <- merge(testY, activityLabels)
# head(activityTest)
activityTrain <- merge(trainY, activityLabels)
# head(activityTrain)

Add activity data to test and train dataset

testDataset <- cbind(testDataset, activityTest)
trainDataset <- cbind(trainDataset, activityTrain)

Check Sample Data

# head(testDatasetwithactivity) head(trainDatasetwithactivity)

Merge test and train datasets

completeDataset <- rbind(testDataset, trainDataset)

Remove train and test datasets to save some space

# rm('testDataset', 'trainDataset')

Now, lets subset the dataset for mean and std measures only!

Use Grep for identifying the subset measures. Code referred from Stack-Overflow

completeDataset <- subset(completeDataset, select = grepl("mean|std", featuresVector))

# head(completeDataset)

write.table(completeDataset, file = "tidyDataset.txt", sep = "\t", row.names = FALSE)

subjectTest <- read.csv("./UCI HAR Dataset/test/subject_test.txt", sep = "", 
    header = FALSE)
subjectTrain <- read.csv("./UCI HAR Dataset/train/subject_train.txt", sep = "", 
    header = FALSE)
colnames(subjectTrain) <- "subject"
colnames(subjectTest) <- "subject"

testDataset <- cbind(testDataset, subjectTest)
trainDataset <- cbind(trainDataset, subjectTrain)

completeDataset1 <- rbind(testDataset, trainDataset)

Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

tidyDataset2 <- aggregate(completeDataset1[, 1:562], completeDataset1[, 563:564], 
    FUN = mean)

# head(tidyDataset2)

Write this tidy dataset

write.table(tidyDataset2, file = "tidyDataset2.txt", sep = "\t", row.names = FALSE)
