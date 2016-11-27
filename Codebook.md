
# Code book  

This file describes the variables in the run_analysis.R. In case one variable is re-assigned more than one time, the variable name is indexed as the appearence of the variable in code. For example AllData (1), AllData (2).

### PART 1:

| Variable           | Description        |
| -------------------|:------------------:|
|MainPath           | The working directory      |
|SubjectTrain           | The data in subject_train.txt           | 
|SubjectTest      |  The data in subject_test.txt           |
|ActivityTrain      |  The data in Y_train.txt           |
|ActivityTest      |  The data in Y_test.txt           |
|DataTrain      |  The data in X_train.txt           |
|DataTest      |  The data in X_test.txt          |
|AllSubject      |  Made of binding SubjectTrain and SubjectTest rows            |
|AllActivity      |  Made of binding ActivityTrain and ActivityTest rows           |
|AllData (1)     |  Made of binding DataTrain and DataTest rows        |
|AllData (2)     |  Made of binding AllSubject, AllActivity, AllData columns        |


### PART 2:

| Variable           | Description        |
| -------------------|:------------------:|
|Features (1)           | The data in features.txt     |
|Features (2)         | Filtered data of "Features" with the condition of having "mean" or "std" in the name of feature         | 
|Extract_Data      |  Filtered data of "AllData (2)" with condition of feature names selected in "Features (2)"           |

### PART 3:

| Variable           | Description        |
| -------------------|:------------------:|
|ActivityNames           | activity_labels.txt     |
|Extract_Data      | with the activity name column added to the "Extract_Data" in Part 2   |

### PART 4:

| Variable           | Description        |
| -------------------|:------------------:|
|FeaturesCode           | The string vector of "Extract_Data" variable names    |

### PART 5:
| Variable           | Description        |
| -------------------|:------------------:|
|TidySet (1)           | with a new "Sub-Act" column from combining "Subject" and "ActivityName" coloumn in "Extract_Data" in Part 3 |
|TidySet (2)           | with deleted "Subject" and "ActivityName" columns from "TidyData (1)" |
|TidySet (3)           | with average value for each "Sub_Act" value from "TidyData (2)" |
|TidySet (4)           | with a new "Sub_Act" column added to "TidyData (3)" |
|TidySet (5)           | with combined coluomns from "TidyData (4)" to make variables into one column|
|TidySet (6)           | with separated "Sub_Act" column from "TidyData (5)" into to columns of "Subject" and "Activity"|
