# TidySet-Project

The code is made of 5 main parts which correspond to the required part in project description

## PART 1: Merges the training and the test sets to create one data set.

### Step 1: read subject train and test:  
#In this step I read the subject train and test data and store them in "SubjectTrain" and "SubjectTest".  
#I assumed the wirking directory is the main data folder. In my case the working directory is: C:\Users\Amin\Documents\My R\UCI HAR Dataset  

SubjectTrain <- fread(file.path(MainPath, "train", "subject_train.txt"))  
SubjectTest <- fread(file.path(MainPath, "test", "subject_test.txt"))

### step 2, read actibity train and test  
#In this step I read the activity train and test data and store them in "ActivityTrain" and "ActivityTest".  

ActivityTrain <- fread(file.path(MainPath, "train", "Y_train.txt"))  
ActivityTest <- fread(file.path(MainPath, "test", "Y_test.txt"))

### step 3, read data train and test
##In this step I read the measured train and test data and store them in "DataTrain" and "DataTest".  

DataTrain <- fread(file.path(MainPath, "train", "X_train.txt"))  
DataTest <- fread(file.path(MainPath, "test", "X_test.txt"))

### step 4, merge all data

AllSubject <- rbind(SubjectTrain, SubjectTest)  
setnames(AllSubject, "V1", "subject")  
AllActivity <- rbind(ActivityTrain, ActivityTest)  
setnames(AllActivity, "V1", "activityNum")  
AllData <- rbind(DataTrain, DataTest)  

AllData <- cbind(AllData,cbind(AllSubject,AllActivity))

## PART 2: Extracts only the measurements on the mean and standard deviation for each measurement.

### step 1, read features

Features <- fread(file.path(MainPath, "features.txt"))  
setnames(Features, names(Features), c("featureID", "featureName"))

### step 2, search for mean and std expression

Features <- Features[grepl("mean\\(\\)|std\\(\\)", featureName)]

### step 3, extract measurements on mean and standard deviation

Extract_Data <- select(AllData,Features$featureID,subject,activityNum)

## PART 3: Uses descriptive activity names to name the activities in the data set.

### step 1, read activity names

ActivityNames <- fread(file.path(MainPath, "activity_labels.txt"))

### step 2, make a new column with activity name

Extract_Data <- mutate(Extract_Data,activityName = ActivityNames$V2[AllData$activityNum])


## PART 4: Appropriately labels the data set with descriptive activity names.

### step 1, create set of currentlabels
FeaturesCode <- Features[, paste0("V", Features$featureID)]

### step 2, set new label with descriptive activity names
setnames(Extract_Data,FeaturesCode,Features$featureName)


## PART 5: Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

### step 1: combine the subjet and activity column 
TidySet <- mutate(Extract_Data,Sub_Act = paste(Extract_Data$subject,str_replace_all(Extract_Data$activityName, "_", ""),sep = "."))

### step 2: remove the old subject and activity column
TidySet <- select(TidySet,-(subject:activityName))

### step 3: calculate the mean for each category
TidySet <- t(sapply(split(TidySet,TidySet$Sub_Act), function(x) colMeans(x[,Features$featureName])))  
TidySet <- mutate(as.data.table(TidySet),Variable = names(as.data.frame(t(TidySet))))

### step 4: Prepare the tidy data
TidySet <- gather(TidySet,key = Sub_Act, value = Average, -Variable)  
TidySet <- separate(ck, col = Variable,into = c("Subject", "Activity"))
