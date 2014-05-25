Getting and Cleaning Data - Course Project
========================================================

## Subset Samsung S3 Motion Data.

### Overview

Data were collected from subjects' Galaxy S3 smartphones while doing various activities.  A full description of the data is at:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

The source data (taken on May 25, 2014) is at:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

### The steps taken to select a subset of the data were:

### Load required libraries.

```
library(stats)
library(plyr)
```

### Read raw data.

```
subjectsTest <- read.table('subject_test.txt')
subjectsTrain <- read.table('subject_train.txt')

featuresTest <- read.table('X_test.txt', sep='', row.names=NULL, colClasses=c('numeric'), strip.white=TRUE)
featuresTrain <- read.table('X_train.txt', sep='', row.names=NULL, colClasses=c('numeric'), strip.white=TRUE)

activitiesTest <- read.table('y_test.txt')
activitiesTrain <- read.table('y_train.txt')
```

### Define a features column selector.

```
subsetFeaturesColNums <- c(
  1,2,3,4,5,6,41,42,43,44,45,46,81,82,83,84,85,86,
  121,122,123,124,125,126,161,162,163,164,165,166,
  201,202,214,215,227,228,240,241,
  253,254,266,267,268,269,270,271,
  345,346,347,348,349,350,424,425,426,427,428,429,
  503,504,516,517,529,530,542,543
  )
```  

### Define names for the selected feature columns.

```
subsetFeaturesColNames <- c(
  'tBodyAccMeanX',
  'tBodyAccMeanY',
  'tBodyAccMeanZ',
  'tBodyAccStdX',
  'tBodyAccStdY',
  'tBodyAccStdZ',
  'tGravityAccMeanX',
  'tGravityAccMeanY',
  'tGravityAccMeanZ',
  'tGravityAccStdX',
  'tGravityAccStdY',
  'tGravityAccStdZ',
  'tBodyAccJerkMeanX',
  'tBodyAccJerkMeanY',
  'tBodyAccJerkMeanZ',
  'tBodyAccJerkStdX',
  'tBodyAccJerkStdY',
  'tBodyAccJerkStdZ',
  'tBodyGyroMeanX',
  'tBodyGyroMeanY',
  'tBodyGyroMeanZ',
  'tBodyGyroStdX',
  'tBodyGyroStdY',
  'tBodyGyroStdZ',
  'tBodyGyroJerkMeanX',
  'tBodyGyroJerkMeanY',
  'tBodyGyroJerkMeanZ',
  'tBodyGyroJerkStdX',
  'tBodyGyroJerkStdY',
  'tBodyGyroJerkStdZ',
  'tBodyAccMagMean',
  'tBodyAccMagStd',
  'tGravityAccMagMean',
  'tGravityAccMagStd',
  'tBodyAccJerkMagMean',
  'tBodyAccJerkMagStd',
  'tBodyGyroMagMean',
  'tBodyGyroMagStd',
  'tBodyGyroJerkMagMean',
  'tBodyGyroJerkMagStd',
  'fBodyAccMeanX',
  'fBodyAccMeanY',
  'fBodyAccMeanZ',
  'fBodyAccStdX',
  'fBodyAccStdY',
  'fBodyAccStdZ',
  'fBodyAccJerkMeanX',
  'fBodyAccJerkMeanY',
  'fBodyAccJerkMeanZ',
  'fBodyAccJerkStdX',
  'fBodyAccJerkStdY',
  'fBodyAccJerkStdZ',
  'fBodyGyroMeanX',
  'fBodyGyroMeanY',
  'fBodyGyroMeanZ',
  'fBodyGyroStdX',
  'fBodyGyroStdY',
  'fBodyGyroStdZ',
  'fBodyAccMagMean',
  'fBodyAccMagStd',
  'fBodyBodyAccJerkMagMean',
  'fBodyBodyAccJerkMagStd',
  'fBodyBodyGyroMagMean',
  'fBodyBodyGyroMagStd',
  'fBodyBodyGyroJerkMagMean',
  'fBodyBodyGyroJerkMagStd'  
  )
  ```

### Select feature subsets.

```
subsetFeaturesTest <- featuresTest[, subsetFeaturesColNums]
subsetFeaturesTrain <- featuresTrain[, subsetFeaturesColNums]
```

### Catenate test and train data.

```
activities <- rbind(activitiesTest, activitiesTrain)
subsetFeatures <- rbind(subsetFeaturesTest, subsetFeaturesTrain)
subjects <- rbind(subjectsTest, subjectsTrain)
```

### Convert activities to factors.

```
activityLevels = c(
  1, 2, 3, 4, 5, 6
  )

activityNames = c(
  'Walking', 
  'WalkingUpstairs', 
  'WalkingDownstairs', 
  'Sitting', 
  'Standing', 
  'Laying'
  )

activitiesFactors <- activities
activitiesFactors$V1 <- factor(activities$V1, activityLevels, activityNames)
```

### Add column names.

```
colnames(subjects) <- 'Subject'
colnames(activitiesFactors) <- 'Activity'
colnames(subsetFeatures) <- subsetFeaturesColNames
```

### Catenate columns into final dataset.

```
combinedSubset <- cbind(subjects, activitiesFactors, subsetFeatures)
```

### Make a new dataset of grouped averages.
I.e, the means and standard deviations are averaged by group.
Grouping is by Subject and Activity.

```
groupedAverages <- ddply(combinedSubset, .(Subject, Activity), colwise(mean))
```

### Output (.csv as .txt).

```
write.csv(groupedAverages, 'groupedAverages.txt')
```

###