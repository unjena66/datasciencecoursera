This is for the course project of "Getting and Cleaning Data" under "Data Science Specialization"
made by Jongsoo Han

The purpose of the courser project is to build a tidy dataset from 
the data collected from the accelerometers from the Samsung Galaxy S smartphone.

I will follow these 5 steps but not exactly.
1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement. 
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names. 
5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

## *********Step 1****************
# 1. Merges the training and the test sets to create one data set.
read train and test data

    testDat <- read.table('test/X_test.txt')
    trainDat <- read.table('train/X_train.txt')
 merge two datasets

    totalDat <- rbind(testDat, trainDat)

## *********Step 2****************
# 2. Extracts only the measurements on the mean and standard deviation for each measurement. 
Read info about columns(features) 

    feat <- read.table("features.txt")
    meas <- feat$V2

See what columns are composed of mean and std dataset
 
    featMS <- grepl('mean|std',feat$V2 )
    totalDatMS <- totalDat[,featMS] # Extract mean and std columns

## *********Step 3****************
# 3. Uses descriptive activity names to name the activities in the data set
read train and test label

    testLabel <- read.table('test/y_test.txt')
    trainLabel <- read.table('train/y_train.txt')

merge two datasets
 
    totalLab <- rbind(testLabel, trainLabel)

merge activity label to the extracted data

    total <- cbind(totalDatMS, totalLab)

name the activity label column
    colnames(total)[80] = "actLab"

match activities to the number factors
1 WALKING /2 WALKING_UPSTAIRS /3 WALKING_DOWNSTAIRS /4 SITTING /5 STANDING /6 LAYING

    totalAct <- 
            mutate(total, actLab = 
               ifelse(actLab ==1, 'WALKING',
                     ifelse(actLab==2, 'WALKING_UPSTAIRS',
                             ifelse(actLab ==3, 'WALKING_DOWNSTAIRS', 
                                    ifelse(actLab ==4, 'SITTING', 
                                            ifelse(actLab ==5, 'STANDING', 
                                                  ifelse(actLab ==6, 'LAYING',NA)))))))

## *********Step 4****************
# 4. Appropriately labels the data set with descriptive variable names. 
extract feature names with mean/std
    
    featMeanStd<-feat$V2[featMS]

names each column with descriptive variable names
 
    colnames(total) = featMeanStd
    colnames(total)[80] = "actLab"

## *********Step 5****************
# 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

    testSubj <- read.table('test/subject_test.txt')
    trainSubj <- read.table('train/subject_train.txt')

merge two datasets
 
    totalSubj <- rbind(testSubj, trainSubj)

merge subject info to the extracted data

    totalAct <- cbind(totalAct, totalSubj)

name the subject column

    colnames(totalAct)[81] = 'subject'

melt the extracted data 

    totalMelt <- melt(totalAct, id=c('actLab','subject'))

group by two id - activity label/ subject

    subject <- group_by(totalMelt, actLab, subject)

average of each variable for each activity and each subject.

    sum <- summarize(subject, Mean = mean(value))

sum

