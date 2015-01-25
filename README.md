### Introduction

In order to run the script `run_analysis.R` make sure that the Samsung data with folder name `UCI HAR Dataset` downloaded from [here](http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones) is in your working directory. To perform all the 5 steps `dplyr` library is required.

	library(dplyr)

### Step 1

Load all the required files into data frames.

	testSubject <- read.csv(".//UCI HAR Dataset//test//subject_test.txt", header=FALSE, sep="")
	testLabel <- read.csv(".//UCI HAR Dataset//test//y_test.txt", header=FALSE, sep="")
	testSet <- read.csv(".//UCI HAR Dataset//test//X_test.txt", header=FALSE, sep="")
	trainSubject <- read.csv(".//UCI HAR Dataset//train//subject_train.txt", header=FALSE, sep="")
	trainLabel <- read.csv(".//UCI HAR Dataset//train//y_train.txt", header=FALSE, sep="")
	trainSet <- read.csv(".//UCI HAR Dataset//train//X_train.txt", header=FALSE, sep="")

Merge the training and the test sets to create one data set.

	subject <- rbind(testSubject,trainSubject)
	label <- rbind(testLabel,trainLabel)
	set <- rbind(testSet,trainSet)

### Step 2

Load the list of all features into data frame `f`.

	f <- read.csv(".//UCI HAR Dataset//features.txt", header=FALSE, sep="")

Add three new boolean columns `mean`, `std` and `bodyBody` in `f` data frame to identify measurements on the mean, standard deviation and the pattern "bodyBody" for each measurement respectively. It is being noticed that the variable name with the pattern "bodyBody" is not being described in the source data README file, hence it is being discarded from tidy data.

	f <- mutate(f,mean = grepl("mean\\(\\)",V2), std = grepl("std\\(\\)",V2), bodyBody = grepl("BodyBody",V2))

In a new variable `setIndexes` collect the column index numbers that are measurements on either the mean or the standard deviation.

	setIndexes <- f[(f$mean | f$std) & f$bodyBody == FALSE,1]

Now with the help of `setIndexes` keep only the required variables in `set` data frame.

	set <- set[,setIndexes]

### Step 3

Load the activity labels into `l` data frame.

	l <- read.csv(".//UCI HAR Dataset//activity_labels.txt", header=FALSE, sep="")

Both data frames `l` and `label` contains activity code.

	> head(label)
	  V1
	1  5
	2  5
	3  5
	4  5
	5  5
	6  5
	> head(l)
	  V1                 V2
	1  1            WALKING
	2  2   WALKING_UPSTAIRS
	3  3 WALKING_DOWNSTAIRS
	4  4            SITTING
	5  5           STANDING
	6  6             LAYING

Taking advantage of the common variable `V1` create a new column `V2` in `label` data frame that contains the description of the activity code. 

	label <- merge(label, l)

Rename columns `V1` to `activityCode` and `V2` to `activity` in `label` data frame.

	label <- rename(label, activityCode = V1, activity = V2)

Using column binding we add descriptive activity names to name the activities in `set` data frame.

	set <- cbind(label,set)

### Step 4

With the help of `f` data frame that contains the list of all variable features name appropriately change the column names of `set` data frame with descriptive variable names.

	names(set)[names(set)=="V1"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==1,2]))))
	names(set)[names(set)=="V2"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==2,2]))))
	names(set)[names(set)=="V3"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==3,2]))))
	names(set)[names(set)=="V4"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==4,2]))))
	names(set)[names(set)=="V5"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==5,2]))))
	names(set)[names(set)=="V6"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==6,2]))))
	names(set)[names(set)=="V41"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==41,2]))))
	names(set)[names(set)=="V42"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==42,2]))))
	names(set)[names(set)=="V43"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==43,2]))))
	names(set)[names(set)=="V44"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==44,2]))))
	names(set)[names(set)=="V45"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==45,2]))))
	names(set)[names(set)=="V46"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==46,2]))))
	names(set)[names(set)=="V81"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==81,2]))))
	names(set)[names(set)=="V82"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==82,2]))))
	names(set)[names(set)=="V83"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==83,2]))))
	names(set)[names(set)=="V84"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==84,2]))))
	names(set)[names(set)=="V85"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==85,2]))))
	names(set)[names(set)=="V86"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==86,2]))))
	names(set)[names(set)=="V121"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==121,2]))))
	names(set)[names(set)=="V122"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==122,2]))))
	names(set)[names(set)=="V123"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==123,2]))))
	names(set)[names(set)=="V124"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==124,2]))))
	names(set)[names(set)=="V125"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==125,2]))))
	names(set)[names(set)=="V126"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==126,2]))))
	names(set)[names(set)=="V161"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==161,2]))))
	names(set)[names(set)=="V162"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==162,2]))))
	names(set)[names(set)=="V163"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==163,2]))))
	names(set)[names(set)=="V164"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==164,2]))))
	names(set)[names(set)=="V165"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==165,2]))))
	names(set)[names(set)=="V166"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==166,2]))))
	names(set)[names(set)=="V201"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==201,2]))))
	names(set)[names(set)=="V202"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==202,2]))))
	names(set)[names(set)=="V214"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==214,2]))))
	names(set)[names(set)=="V215"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==215,2]))))
	names(set)[names(set)=="V227"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==227,2]))))
	names(set)[names(set)=="V228"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==228,2]))))
	names(set)[names(set)=="V240"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==240,2]))))
	names(set)[names(set)=="V241"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==241,2]))))
	names(set)[names(set)=="V253"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==253,2]))))
	names(set)[names(set)=="V254"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==254,2]))))
	names(set)[names(set)=="V266"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==266,2]))))
	names(set)[names(set)=="V267"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==267,2]))))
	names(set)[names(set)=="V268"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==268,2]))))
	names(set)[names(set)=="V269"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==269,2]))))
	names(set)[names(set)=="V270"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==270,2]))))
	names(set)[names(set)=="V271"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==271,2]))))
	names(set)[names(set)=="V345"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==345,2]))))
	names(set)[names(set)=="V346"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==346,2]))))
	names(set)[names(set)=="V347"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==347,2]))))
	names(set)[names(set)=="V348"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==348,2]))))
	names(set)[names(set)=="V349"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==349,2]))))
	names(set)[names(set)=="V350"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==350,2]))))
	names(set)[names(set)=="V424"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==424,2]))))
	names(set)[names(set)=="V425"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==425,2]))))
	names(set)[names(set)=="V426"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==426,2]))))
	names(set)[names(set)=="V427"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==427,2]))))
	names(set)[names(set)=="V428"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==428,2]))))
	names(set)[names(set)=="V429"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==429,2]))))
	names(set)[names(set)=="V503"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==503,2]))))
	names(set)[names(set)=="V504"] <- as.character(gsub(")","",gsub("\\(","",gsub("-","",f[f$V1==504,2]))))

### Step 5

In `subject` data frame rename column `V1` to  

	subject <- rename(subject, subject = V1)

Add the columns of `subject` data frame to `set` data frame.

	set <- cbind(subject,set)

Finally in `set` data frame we now have subject and activity data along with the all the mean and standard deviation measurement variables. Proceed to create a second, independent tidy data set with the average of each variable for each activity and each subject

	activitySubject <- group_by(set, activity, subject)
	summSet <- summarize(activitySubject, tBodyAccmeanX=mean(tBodyAccmeanX), tBodyAccmeanY=mean(tBodyAccmeanY), tBodyAccmeanZ=mean(tBodyAccmeanZ), tBodyAccstdX=mean(tBodyAccstdX), tBodyAccstdY=mean(tBodyAccstdY), tBodyAccstdZ=mean(tBodyAccstdZ), tGravityAccmeanX=mean(tGravityAccmeanX), tGravityAccmeanY=mean(tGravityAccmeanY), tGravityAccmeanZ=mean(tGravityAccmeanZ), tGravityAccstdX=mean(tGravityAccstdX), tGravityAccstdY=mean(tGravityAccstdY), tGravityAccstdZ=mean(tGravityAccstdZ), tBodyAccJerkmeanX=mean(tBodyAccJerkmeanX), tBodyAccJerkmeanY=mean(tBodyAccJerkmeanY), tBodyAccJerkmeanZ=mean(tBodyAccJerkmeanZ), tBodyAccJerkstdX=mean(tBodyAccJerkstdX), tBodyAccJerkstdY=mean(tBodyAccJerkstdY), tBodyAccJerkstdZ=mean(tBodyAccJerkstdZ), tBodyGyromeanX=mean(tBodyGyromeanX), tBodyGyromeanY=mean(tBodyGyromeanY), tBodyGyromeanZ=mean(tBodyGyromeanZ), tBodyGyrostdX=mean(tBodyGyrostdX), tBodyGyrostdY=mean(tBodyGyrostdY), tBodyGyrostdZ=mean(tBodyGyrostdZ), tBodyGyroJerkmeanX=mean(tBodyGyroJerkmeanX), tBodyGyroJerkmeanY=mean(tBodyGyroJerkmeanY), tBodyGyroJerkmeanZ=mean(tBodyGyroJerkmeanZ), tBodyGyroJerkstdX=mean(tBodyGyroJerkstdX), tBodyGyroJerkstdY=mean(tBodyGyroJerkstdY), tBodyGyroJerkstdZ=mean(tBodyGyroJerkstdZ), tBodyAccMagmean=mean(tBodyAccMagmean), tBodyAccMagstd=mean(tBodyAccMagstd), tGravityAccMagmean=mean(tGravityAccMagmean), tGravityAccMagstd=mean(tGravityAccMagstd), tBodyAccJerkMagmean=mean(tBodyAccJerkMagmean), tBodyAccJerkMagstd=mean(tBodyAccJerkMagstd), tBodyGyroMagmean=mean(tBodyGyroMagmean), tBodyGyroMagstd=mean(tBodyGyroMagstd), tBodyGyroJerkMagmean=mean(tBodyGyroJerkMagmean), tBodyGyroJerkMagstd=mean(tBodyGyroJerkMagstd), fBodyAccmeanX=mean(fBodyAccmeanX), fBodyAccmeanY=mean(fBodyAccmeanY), fBodyAccmeanZ=mean(fBodyAccmeanZ), fBodyAccstdX=mean(fBodyAccstdX), fBodyAccstdY=mean(fBodyAccstdY), fBodyAccstdZ=mean(fBodyAccstdZ), fBodyAccJerkmeanX=mean(fBodyAccJerkmeanX), fBodyAccJerkmeanY=mean(fBodyAccJerkmeanY), fBodyAccJerkmeanZ=mean(fBodyAccJerkmeanZ), fBodyAccJerkstdX=mean(fBodyAccJerkstdX), fBodyAccJerkstdY=mean(fBodyAccJerkstdY), fBodyAccJerkstdZ=mean(fBodyAccJerkstdZ), fBodyGyromeanX=mean(fBodyGyromeanX), fBodyGyromeanY=mean(fBodyGyromeanY), fBodyGyromeanZ=mean(fBodyGyromeanZ), fBodyGyrostdX=mean(fBodyGyrostdX), fBodyGyrostdY=mean(fBodyGyrostdY), fBodyGyrostdZ=mean(fBodyGyrostdZ), fBodyAccMagmean=mean(fBodyAccMagmean), fBodyAccMagstd=mean(fBodyAccMagstd))

Write the final data set to a file name `summarizedSet.txt` in the working directory.

	write.table(summSet, file="summarizedSet.txt", row.name=FALSE)
