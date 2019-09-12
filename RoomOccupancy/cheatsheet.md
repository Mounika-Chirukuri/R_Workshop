# R Workshop | Predict Room Occupancy

The below markdown file consists of commands and code snippets that will help you complete the lab - Predict Room Occupancy.

## Code Snippets

### Set the working directory
```
setwd("D:/Occupancy-detection-data-master/Occupancy-detection-data-master")
```
### Install packages
```
#Install the packages which are necessary

install.packages("randomForest")
install.packages("caret")
install.packages("e1071")
install.packages("lubridate") 
```
### Import libraries
```
#importing the required libraries which are necessary for our R application
library(randomForest)
library(caret)
library(e1071)
library(lubridate) 
```

### load("occupancy_last.RData")  
```
save.image(file="occupancy_last.RData")
```

### Read the Data set 
```
datatraining <- read.table("RoomOccupancyDataset.txt",header=TRUE,sep=",")
```
### Test Data set
```
#Retrieving the rows after 6000 row from the data set  
datatesting= datatraining [6001:8143,]
```
### Train Data set
```
#Retrieving the top 6000 rows from the data set  
datatraining= datatraining [1:6000,]
```

### Reviewing the data types
```
str(datatraining)
str(datatesting)
```

### Convert the occupancy column (the column which need to be predicted) to factor type  
```
datatraining$Occupancy <- as.factor(datatraining$Occupancy)
datatesting$Occupancy  <- as.factor(datatesting$Occupancy)
```

### Formatting the date class for all the files 
```
datatraining$date <-as.POSIXct(datatraining$date,tz="UTC") 
datatesting$date <- as.POSIXct(datatesting$date,tz="UTC") 
```

### Reviewing the data types again 
```
str(datatraining)
str(datatesting)
set.seed(1234)
```

### Apply an ML model
```
#Applying random forest algorithm on the train data set excluding light column 

ModelRF_ALL_b_CO22 <- train(Occupancy~.-date-Light,method="rf",data=datatraining)
ModelRF_ALL_b_CO22
```

### Plotting the model 
```
plot(ModelRF_ALL_b_CO22)
ModelRF_ALL_b_CO22$finalModel
```

### Plotting the importance of top columns 
```
varImp(ModelRF_ALL_b_CO22)
plot(varImp(ModelRF_ALL_b_CO22,scale=TRUE))
```
### Confusion matrix
``` 
#Calculate the confusion matrix based on train and test data 

set.seed(1234)
confusionMatrix(datatraining$Occupancy,predict(ModelRF_ALL_b_CO22,datatraining))
set.seed(1234)
```

### Create Occupancy_test column and dump the output to this column 
```
datatesting$Occupancy_test=predict(ModelRF_ALL_b_CO22,datatesting)
```

### Finding the accuracy of the model 
```
sum(datatesting$Occupancy==predict(ModelRF_ALL_b_CO22,datatesting))/dim(datatesting)[1]*100
```

### Writing the resultant output to a csv file 
```
write.csv(datatesting, file = "out.csv", row.names = FALSE)
```
