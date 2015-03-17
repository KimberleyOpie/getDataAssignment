#Code book

##Funciton 1 - mergeData
- The code reads the following files with the following manipulations:
1. Column bin the following:
  1. Row bind subject_test and subject_train text files
  2. Row bind y_test and y_train text files
  3. Row bind the first six columns of the fixed width format x_test and x_train text files

- The second column (y_test and y_train) are changed to named activities in file activity_lables.txt by using mapvariables funciton from plyr.  
- The column are named Subject, Activity and the remaining 6 columns are named using the orginal column names for those variables foun din features.txt with any non-character removed

This with be the first requested dataset

##Function 2 - tidyData
- The results from the above function is grouped by subject and activity
- The means for each subject and activiy is generated using the summarize function from dplyr

