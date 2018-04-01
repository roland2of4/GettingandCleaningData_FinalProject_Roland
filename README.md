# GettingandCleaningData_FinalProject_Roland
Coursera GettingandCleaningData_FinalProject_Roland

##Merges the training and the test sets to create one data set for each type of info
##Combine all the Subject data, then Activity data, then Features data, ending up with 3 tables
dataSubject <- rbind(dataSubjectTrain, dataSubjectTest)
dataActivity<- rbind(dataActivityTrain, dataActivityTest)
dataFeatures<- rbind(dataFeaturesTrain, dataFeaturesTest)
## give the columns names
names(dataSubject)<-c("subject")
names(dataActivity)<- c("activity")
names(dataFeatures)<- dataFeaturesNames$V2
## merge columns into one dataframe
Data<- cbind(dataSubject, dataActivity, dataFeatures)
#find only column names containing "mean()" and "std()" in them. Now below has 68 vars.
namesMeanStdOnly <- grep("mean\\(\\)|std\\(\\)",dataFeaturesNames$V2,value=TRUE)
## put back together with SubjectActivity
namesSubjectActivity<-c(as.character(namesMeanStdOnly), "subject", "activity" )
Data<-subset(Data,select=namesSubjectActivity)
## replace data activity numbers with labels
factorizeActivityLabels <- factor(Data$activity)
levels(factorizeActivityLabels) <- activity_labels[,2]
Data$activity <- factorizeActivityLabels
## replace abbreviated names with full names
names(Data)<-gsub("^t", "time", names(Data))
names(Data)<-gsub("^f", "frequency", names(Data))
names(Data)<-gsub("Acc", "Accelerometer", names(Data))
names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
names(Data)<-gsub("Mag", "Magnitude", names(Data))
names(Data)<-gsub("BodyBody", "Body", names(Data))

## make a tidy data set. First factorize subject, then apply mean, then arrange column order, then write table. 
Data$subject <- as.factor(Data$subject)
Data2<-aggregate(. ~subject + activity, Data, mean)
Data2<-Data2[order(Data2$subject,Data2$activity),]
write.table(Data2, file = "tidydata.txt",row.name=FALSE)

