# Getting and Cleaning Data
Course Assignment

The data was obtained from https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip


Tidying the data:

Features
The features file "UCI HAR Dataset/features.txt" contains 561 features.  These are the variable names that I added 
as column names to the test and training data sets (test_set, training_set).  
I created features_selected by removing all features that did not contain strings "mean" and "std" in their name.

Subjects
Each data set has a subject file that identifies per row from which sujbect the data was collected.  I changed the 
column name in the test and train subject sets to "subject".

Activities
"UCI HAR Dataset/activity_labels.txt" contains all activities performed but the subjects.  It contains two columns: an id and activity description column.  I changed the column names in activity_labels to "id" and "activity", respectively.
I also changed the column names in test and training labels to "id".   

Data sets:
This applies to both test and train set but I will only describe what I did to the test set.

<ol>
<li>I added all the features as column names then I selected only those columns that are in features_selected (only mean and standard deviation columns)</li>
<li>I replaced all ids in the test_labels with activity names from activity_labels by matching the ids</li>
<li>I added test_labels in front of the test set</li>
<li>I added the subject column in front of the activity column</li>
<li>I merged both test and training sets into data_set_all</li>
</ol>

Better names for variables
I tried to improve the variable names by:
<ol>
<li>replacing the letter t at the start of the name with Time</li>
<li>replacing the letter f at the start of the name with Frequency</li>
<li>removing dashes and parentheses</li>
<li>replacing Acc with Accelerometer</li>
<li>replacing Gyro with Gyroscope</li>
<li>replacing Mag with Magnitude</li>
<li>replacing the string BodyBody with Body (I noticed that a few names had the word Body repeating)</li>
<li>replacing the string mean with Mean</li>
<li>replacing the string std with StandardDeviation</li>
</ol>

Here is a couple befroe and after variable names
fBodyBodyGyroJerkMag-std() -> FrequencyBodyGyroscopeJerkMagnitudeStandardDeviation
tBodyAcc-mean()-Y -> TimeBodyAccelerometerMeanY

Summarizing the data
I created a data.table called summary table and added "activity" and "subject" as keys then I summarized the mean of all variables by activity and subject.  Here is output that shows the first six subjects while Laying (I am sure they meant Lying down but I did not change the name of the activity)

    > head(summary_table)
      activity subject TimeBodyAccelerometerMeanX TimeBodyAccelerometerMeanY TimeBodyAccelerometerMeanZ 
   
    1:   LAYING       1                  0.2215982                -0.04051395                 -0.1132036
    2:   LAYING       2                  0.2813734                -0.01815874                 -0.1072456
    3:   LAYING       3                  0.2755169                -0.01895568                 -0.1013005
    4:   LAYING       4                  0.2635592                -0.01500318                 -0.1106882
    5:   LAYING       5                  0.2783343                -0.01830421                 -0.1079376
    6:   LAYING       6                  0.2486565                -0.01025292                 -0.1331196
