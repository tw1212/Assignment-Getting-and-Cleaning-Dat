
The code expects file getdata%2Fprojectfiles%2FUCI HAR Dataset.zip to be in the working directory.
If it is not found, getdata%2Fprojectfiles%2FUCI HAR Dataset.zip will be downloaded from
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

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

##
##  Code
##

##
## Download the data file
##
        
        ## Downlad the file if it is not in the working directory
        datasetFile_zipped <- paste0(getwd(),"/getdata%2Fprojectfiles%2FUCI HAR Dataset.zip")
        
        if (!file.exists(datasetFile_zipped)) {
                dataURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
                download.file(dataURL, destfile = datasetFile_zipped)
                
                fileAlreadyExists <- FALSE
        } else {
                fileAlreadyExists <- TRUE
        }

##
## Load needed libraries
##
        require(readr)        # needed for: read_table2       
        require(dplyr)        # needed for: bind_rows   
        require(data.table)   # needed for: data.table, setkeyv
        
##
## Read the data from the zipped file
##

Nothing special here.  I am just loading the data by unzipping the required file 
and then using read_table2 to create a data frame

## Read features from UCI HAR Dataset/features.txt
    features_file <- unzip (datasetFile_zipped,"UCI HAR Dataset/features.txt")
    features <- read_table2(features_file,col_names = FALSE)

    > head(features,3)
    # A tibble: 3 x 2
         X1                X2
      <int>             <chr>
    1     1 tBodyAcc-mean()-X
    2     2 tBodyAcc-mean()-Y
    3     3 tBodyAcc-mean()-Z

## Read activities from UCI HAR Dataset/activity_labels.txt
    activity_labels_file <- unzip (datasetFile_zipped,"UCI HAR Dataset/activity_labels.txt")
    activity_labels <- read_table2(activity_labels_file,col_names = FALSE)
    
    > head(activity_labels,3)
    # A tibble: 3 x 2
         X1                 X2
      <int>              <chr>
    1     1            WALKING
    2     2   WALKING_UPSTAIRS
    3     3 WALKING_DOWNSTAIRS
        
##
## Read subjects, feature set and labels from the test and training sets
##

## Subjects - test
    test_subject_file <- unzip (datasetFile_zipped,"UCI HAR Dataset/test/subject_test.txt")
    test_subject <- read_table2(test_subject_file,col_names = FALSE)

## variables - test
    test_set_file <- unzip (datasetFile_zipped,"UCI HAR Dataset/test/X_test.txt")
    test_set <- read_table2(test_set_file,col_names = FALSE)

## labels - test
    test_labels_file <- unzip (datasetFile_zipped,"UCI HAR Dataset/test/y_test.txt")
    test_labels <- read_table2(test_labels_file,col_names = FALSE)

## subjects - training
    training_subject_file <- unzip (datasetFile_zipped,"UCI HAR Dataset/train/subject_train.txt")
    training_subject <- read_table2(training_subject_file,col_names = FALSE)

## variables - training
    training_set_file <- unzip (datasetFile_zipped,"UCI HAR Dataset/train/X_train.txt")
    training_set <- read_table2(training_set_file,col_names = FALSE)

## labels - training
    training_labels_file <- unzip (datasetFile_zipped,"UCI HAR Dataset/train/y_train.txt")
    training_labels <- read_table2(training_labels_file,col_names = FALSE)
        

##        
## Add column names
##


## add varaible names
    colnames(test_set) <-features$X2
    colnames(training_set) <-features$X2

## add subject name
    names(test_subject)[1] <- "subject"
    names(training_subject)[1] <- "subject"

## add id and activity column names
    names(activity_labels) <- c("id","activity")

## add labels column name along with the activity column names, they will be used later to match activity id to activity description
    names(test_labels)<- "id"
    names(training_labels)<- "id"

##        
## Extract from the variable columns only those that have the string -mean( or -std( in their names
##

## select all features that have the string -mean( or -std( in their names
    features_selected <- grep("-mean\\(|-std\\(",features$X2)
    test_set_selected <- test_set[,names(test_set) %in% features$X2[features_selected]]
    training_set_selected <- training_set[,names(training_set) %in% features$X2[features_selected]]

##
## Add the activity labels in front of the variables
##
        
## first match activity id to activity name
    test_labels[["id"]] <-  activity_labels[ match(test_labels[['id']], activity_labels[['id']] ) , 'activity']
    training_labels[["id"]] <-  activity_labels[ match(training_labels[['id']], activity_labels[['id']] ) , 'activity']


## now add the activity column called activity 
    test_combined <- cbind(test_labels$id,test_set_selected)
    training_combined <- cbind(training_labels$id,training_set_selected)

##
## Add subjects in front of activity  
##
        
    test_combined <- cbind(test_subject,test_combined)
    training_combined <- cbind(training_subject,training_combined)

##
## merge the test and training sets together
##
    data_set_all <-bind_rows(test_combined,training_combined)
        
##
## Provide more descriptive variable names
##

    names(data_set_all) <- gsub("^t","Time",names(data_set_all))            # replace the letter t at the start with Time
    names(data_set_all) <- gsub("^f","Frequency",names(data_set_all))       # replace the letter f at the start with Frequency
    names(data_set_all) <- gsub("-|\\(\\)","",names(data_set_all))          # remove dashes and parentheses
    names(data_set_all) <- gsub("Acc","Accelerometer",names(data_set_all))  # replace Acc with Accelerometer
    names(data_set_all) <- gsub("Gyro","Gyroscope",names(data_set_all))     # replace Gyro with Gyroscope
    names(data_set_all) <- gsub("Mag","Magnitude",names(data_set_all))      # replace Mag with Magnitude
    names(data_set_all) <- gsub("BodyBody","Body",names(data_set_all))      # replace the string BodyBody with Body
    names(data_set_all) <- gsub("mean","Mean",names(data_set_all))          # replace the string mean with Mean
    names(data_set_all) <- gsub("std","StandardDeviation",names(data_set_all)) # replace the string std with StandardDeviation

##        
## Create and save a summary table
##

    summary_table <- data.table(data_set_all)

## set the table keys in order to summarise the table
    keycols <- c("activity","subject")
    setkeyv(summary_table,keycols)

## calculate the mean of variables by activity and subject
    summary_table <-summary_table[,lapply(.SD,mean),by=key(summary_table)]

## save the summary table to a csv file
    write.table(summary_table,file = "SummaryTable.txt",row.names = FALSE)


##
##  Clean up
##

## Delete the data file if we downloaded it
    if(!fileAlreadyExists)
        file.remove(datasetFile_zipped)

## Delete the directory where the files were unzipped
    if (dir.exists("UCI HAR Dataset"))
        unlink("UCI HAR Dataset", recursive = TRUE)        

## Clear Environment
    rm(list=ls())

## detach libraries
    detach("package:readr", unload=TRUE)
    detach("package:dplyr", unload=TRUE)
    detach("package:data.table", unload=TRUE)
       
