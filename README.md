# TidySet-Project

The code is made of 5 main parts which correspond to the required part in project description  

## PART 1: Merges the training and the test sets to create one data set.

### Step 1: read subject train and test:  

In this step I read the subject train and test data and store them in "SubjectTrain" and "SubjectTest".  
I assumed the wirking directory is the main data folder. In my case the working directory is: C:\Users\Amin\Documents\My R\UCI HAR Dataset  

SubjectTrain <- fread(file.path(MainPath, "train", "subject_train.txt"))  
SubjectTest <- fread(file.path(MainPath, "test", "subject_test.txt"))

### step 2, read actibity train and test  
In this step I read the activity train and test data and store them in "ActivityTrain" and "ActivityTest".  

ActivityTrain <- fread(file.path(MainPath, "train", "Y_train.txt"))  
ActivityTest <- fread(file.path(MainPath, "test", "Y_test.txt"))

### step 3, read data train and test  

In this step I read the measured train and test data and store them in "DataTrain" and "DataTest".  

DataTrain <- fread(file.path(MainPath, "train", "X_train.txt"))  
DataTest <- fread(file.path(MainPath, "test", "X_test.txt"))

### step 4, merge all data  

In this step I first combine the rows of each train and test data types and store them in "AllSubject", "AllActivity" and "AllData". Next I combine all the columns of the previous variables and store the merged data in "AllData".  

AllSubject <- rbind(SubjectTrain, SubjectTest)  
setnames(AllSubject, "V1", "subject")  
AllActivity <- rbind(ActivityTrain, ActivityTest)  
setnames(AllActivity, "V1", "activityNum")  
AllData <- rbind(DataTrain, DataTest)  

AllData <- cbind(AllData,cbind(AllSubject,AllActivity))

## PART 2: Extracts only the measurements on the mean and standard deviation for each measurement.

### step 1, read features  

In this step I read the feautres and store it in "Features".  

Features <- fread(file.path(MainPath, "features.txt"))  
setnames(Features, names(Features), c("featureID", "featureName"))  

### step 2, search for mean and std expression  

In this step I filter the complete feature list by selecting the feastures which have mean and std in their expression. The results is stored in "Features".  

Features <- Features[grepl("mean\\(\\)|std\\(\\)", featureName)]  

### step 3, extract measurements on mean and standard deviation  

In this step the new data according to the new features are selected from "AllData" and stored in "Extract_Data".  

Extract_Data <- select(AllData,Features$featureID,subject,activityNum)  

## PART 3: Uses descriptive activity names to name the activities in the data set.

### step 1, read activity names  

In this step I read the activity label file and store it in "ActivityNames".  

ActivityNames <- fread(file.path(MainPath, "activity_labels.txt"))  

### step 2, make a new column with activity name  

In this step I insert the "ActivityNames" as a new coluomn to the "Extract_Data"  

Extract_Data <- mutate(Extract_Data,activityName = ActivityNames$V2[AllData$activityNum])  


## PART 4: Appropriately labels the data set with descriptive activity names.

### step 1, create set of currentlabels  

In this step I prepare the string vector which contains the default feaure names of the data in "Extract_Data".  

FeaturesCode <- Features[, paste0("V", Features$featureID)]  

### step 2, set new label with descriptive activity names  

In this step I replace the previous data label with the one from "Features$featureName" variable.  

setnames(Extract_Data,FeaturesCode,Features$featureName)  


## PART 5: Creates a second, independent tidy data set with the average of each variable for each activity and each subject.

### step 1: combine the subjet and activity column  

In this step I fist combine the subject and activity name column into one single column. My goal is to be able to group all the data in "Extract_Data" into subgroups with unique sunject-avtivity identity. The new column is named "Sub_Act".  

TidySet <- mutate(Extract_Data,Sub_Act = paste(Extract_Data$subject,str_replace_all(Extract_Data$activityName, "_", "_"),sep = "-"))  

### step 2: remove the old subject and activity column  

In this step I remove the previous separated subject and activityname column as they now appear toether in Sub_Act column.  

TidySet <- select(TidySet,-(subject:activityName))  

### step 3: calculate the mean for each category  

In this step I calculate the mean of each subject-activity subgroup and subject-activity and store it in "TidySet". Next I convert the class of "TidySet" from matrix to data table. Since the variable names are missed in the conversion, I added them as a new column.  

TidySet <- t(sapply(split(TidySet,TidySet$Sub_Act), function(x) colMeans(x[,Features$featureName])))  
TidySet <- mutate(as.data.table(TidySet),Sub_Act = names(as.data.frame(t(TidySet))))

### step 4: Prepare the tidy data  

In the last step, I create a tidy data by gathering the column and finally separating the Sub_Act column into two separate columns.

TidySet <- gather(TidySet,key = Variable, value = Average, -Sub_Act)  
TidySet <- separate(TidySet, col = Sub_Act,into = c("Subject", "Activity"),sep = "-")  
TidySet$Subject<-as.numeric(TidySet$Subject)
