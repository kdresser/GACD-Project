GaCD - Course Project
========================================================

## Subset and analyse Samsung S3 Motion Data.

### Overview

Data were collected from subjects' Galaxy S3 smartphones while doing various activities.  A full description of the raw data is at:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

The source data (taken on May 25, 2014) are at:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

## Part 1.

### Take a subset of the raw data.  Do some tidying.

```
library(stats)
library(plyr)
```

### Read the raw data.

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
  001,002,003,004,005,006,041,042,043,044,045,046,081,082,083,084,085,086,
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

### Catenate test and training data.

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

### Catenate columns into final dataset 1.

```
combinedSubset <- cbind(subjects, activitiesFactors, subsetFeatures)
```

### Output the combinedSubset (.csv as .txt).

```
write.csv(combinedSubset, 'combinedSubset.txt')
```

## Part 2.

### From combinedDataset, make a new dataset: groupedAverages.

The means and standard deviations are averaged by group, and the grouping is by Subject and Activity.

```
groupedAverages <- ddply(combinedSubset, .(Subject, Activity), colwise(mean))
```

### Output groupedAverages (.csv as .txt).

```
write.csv(groupedAverages, 'groupedAverages.txt')
```

###