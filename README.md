data_science
============

## HOW IT WORKS

# to run this, you must use file->change_directory->"uci har dataset"
# the dir() command must include the following files/dir for you to be
# in the correct directory, and for this script to work:
#
# > dir() GIVES AT LEAST:
# [1] "activity_labels.txt" "features.txt"        "features_info.txt"  
# [4] "readme.txt"          "test"                "train"              
# > 
#

###
### 1. Merges the training and the test sets to create one data set.
###

# get the list of features (column names) from the accompaning documentation
# there are 561 columns 1...561
col_name_table = read.table('features.txt')

# read the testing/training data, automatically filling in the column names
test  = read.table('test/x_test_150.txt',   col.names = col_name_table[,2])
train = read.table('train/x_train_160.txt', col.names = col_name_table[,2])

# read activityz underwhich each observation (row) for testing/training is performed
test_activity  = read.table('test/y_test_150.txt')
train_activity = read.table('train/y_train_160.txt')

# tack on the activity (1..6) and the source of the row (training/testing)
# onto first columns of the dataset.  Columns are now:

# DataSource ('test'/'train') | Activity(1..6) | OrigData (561 more cols)
# DataSource ('test'/'train') | Activity(1..6) | OrigData (561 more cols)
# DataSource ('test'/'train') | Activity(1..6) | OrigData (561 more cols)
# DataSource ('test'/'train') | Activity(1..6) | OrigData (561 more cols)

test  = cbind(test_act,  'test',  test)
train = cbind(train_act, 'train', train)

# label the newly added columns
names(test)[1]  = 'Activity'
names(test)[2]  = 'DataSource'
names(train)[1] = 'Activity'
names(train)[2] = 'DataSource'

# combine the datasets into one big vertical table, keeping all the
# columns aligned (R will not do the merge if not aligned anyway)
merged = rbind(test,train)

# show the dimensions
dim(merged)

###
### 2. Extracts only the measurements on the mean and standard deviation
### for each measurement. 
###

# the mean and std measurements all have the string 'mean()' and 'std()'
# in them.  Note there are other features that have to do with means and
# std, such as computing other values.  I am not including them.  You can
# add to the following list if desired.  Instructions are not clear.
# I also want the 2 columns I added, Activity & DataSource

selections = names(merged) %in% c('mean()', 'std()', 'Activity', 'DataSource')

# external step
# fgrep -e "mean()" -e "std()" features.txt > mstd
# and read that data into the vector below: c(1,2,3,4 etc.)
meanstd = c(1,2,3,4,5,6,41,42,43,44,45,46,81,82,83,84,85,86,121,122,123,124,125,126,161,162,163,164,165,166,201,202,214,215,227,228,240,241,253,254,266,267,268,269,270,271,345,346,347,348,349,350,424,425,426,427,428,429,503,504,516,517,529,530,542,543)
wanted = c(1, 2, 2 + meanstd) # icnlude activity, datasource

# final answer here, only the mean / stdmeasurements
mean_std = merged[,wanted]

###
### 3. Uses descriptive activity names to name the activities in the data set
### 4. Appropriately labels the data set with descriptive activity names. 
###

# activites are now in [,1], just replace from file:
# 1 WALKING
# 2 WALKING_UPSTAIRS
# 3 WALKING_DOWNSTAIRS
# 4 SITTING
# 5 STANDING
# 6 LAYING
x = mean_std[,1] == 1
mean_std[x, 1] = 'WALKING'
x = mean_std[,1] == 2
mean_std[x, 1] = 'WALKING_UPSTAIRS'
x = mean_std[,1] == 3
mean_std[x, 1] = 'WALKING_DOWNSTAIRS'
x = mean_std[,1] == 4
mean_std[x, 1] = 'SITTING'
x = mean_std[,1] == 5
mean_std[x, 1] = 'STANDING'
x = mean_std[,1] == 6
mean_std[x, 1] = 'LAYING'

# final result
write.csv(mean_std, 'final.csv')
