# Practical Machine Learning Project
Andres Chacon  
December 17, 2016  



## Introduction:

This is the final project from Practical Machine Learning, the 8th course of the specialization in Data Science. The project's goal is to apply the acquired knowledge to predict for an specific sample under which of the 6 categories that classify the way an exercise was performed falls, the data was collected using accelerometers placed in different parts of the body. More details can be found [here](http://groupware.les.inf.puc-rio.br/har)

## Cleaning the Data:

In this section we will read the CSV files, remove some useless data and prepare the sets for the training and crossvalidation.


```r
# Read the CSV files
raw.data <- read.csv("pml-training.csv")
raw.test <- read.csv("pml-testing.csv")

# Preview of the first rows:
str(raw.data)
```

```
## 'data.frame':	19622 obs. of  160 variables:
##  $ X                       : int  1 2 3 4 5 6 7 8 9 10 ...
##  $ user_name               : Factor w/ 6 levels "adelmo","carlitos",..: 2 2 2 2 2 2 2 2 2 2 ...
##  $ raw_timestamp_part_1    : int  1323084231 1323084231 1323084231 1323084232 1323084232 1323084232 1323084232 1323084232 1323084232 1323084232 ...
##  $ raw_timestamp_part_2    : int  788290 808298 820366 120339 196328 304277 368296 440390 484323 484434 ...
##  $ cvtd_timestamp          : Factor w/ 20 levels "02/12/2011 13:32",..: 9 9 9 9 9 9 9 9 9 9 ...
##  $ new_window              : Factor w/ 2 levels "no","yes": 1 1 1 1 1 1 1 1 1 1 ...
##  $ num_window              : int  11 11 11 12 12 12 12 12 12 12 ...
##  $ roll_belt               : num  1.41 1.41 1.42 1.48 1.48 1.45 1.42 1.42 1.43 1.45 ...
##  $ pitch_belt              : num  8.07 8.07 8.07 8.05 8.07 8.06 8.09 8.13 8.16 8.17 ...
##  $ yaw_belt                : num  -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 -94.4 ...
##  $ total_accel_belt        : int  3 3 3 3 3 3 3 3 3 3 ...
##  $ kurtosis_roll_belt      : Factor w/ 397 levels "","-0.016850",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_picth_belt     : Factor w/ 317 levels "","-0.021887",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_yaw_belt       : Factor w/ 2 levels "","#DIV/0!": 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_roll_belt      : Factor w/ 395 levels "","-0.003095",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_roll_belt.1    : Factor w/ 338 levels "","-0.005928",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_yaw_belt       : Factor w/ 2 levels "","#DIV/0!": 1 1 1 1 1 1 1 1 1 1 ...
##  $ max_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_belt          : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_belt            : Factor w/ 68 levels "","-0.1","-0.2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ min_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_belt          : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_belt            : Factor w/ 68 levels "","-0.1","-0.2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ amplitude_roll_belt     : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_pitch_belt    : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_yaw_belt      : Factor w/ 4 levels "","#DIV/0!","0.00",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ var_total_accel_belt    : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_roll_belt        : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_roll_belt           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_pitch_belt          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_pitch_belt       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_pitch_belt          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_yaw_belt            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_yaw_belt         : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_yaw_belt            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ gyros_belt_x            : num  0 0.02 0 0.02 0.02 0.02 0.02 0.02 0.02 0.03 ...
##  $ gyros_belt_y            : num  0 0 0 0 0.02 0 0 0 0 0 ...
##  $ gyros_belt_z            : num  -0.02 -0.02 -0.02 -0.03 -0.02 -0.02 -0.02 -0.02 -0.02 0 ...
##  $ accel_belt_x            : int  -21 -22 -20 -22 -21 -21 -22 -22 -20 -21 ...
##  $ accel_belt_y            : int  4 4 5 3 2 4 3 4 2 4 ...
##  $ accel_belt_z            : int  22 22 23 21 24 21 21 21 24 22 ...
##  $ magnet_belt_x           : int  -3 -7 -2 -6 -6 0 -4 -2 1 -3 ...
##  $ magnet_belt_y           : int  599 608 600 604 600 603 599 603 602 609 ...
##  $ magnet_belt_z           : int  -313 -311 -305 -310 -302 -312 -311 -313 -312 -308 ...
##  $ roll_arm                : num  -128 -128 -128 -128 -128 -128 -128 -128 -128 -128 ...
##  $ pitch_arm               : num  22.5 22.5 22.5 22.1 22.1 22 21.9 21.8 21.7 21.6 ...
##  $ yaw_arm                 : num  -161 -161 -161 -161 -161 -161 -161 -161 -161 -161 ...
##  $ total_accel_arm         : int  34 34 34 34 34 34 34 34 34 34 ...
##  $ var_accel_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_roll_arm         : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_pitch_arm        : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ avg_yaw_arm             : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ stddev_yaw_arm          : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ var_yaw_arm             : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ gyros_arm_x             : num  0 0.02 0.02 0.02 0 0.02 0 0.02 0.02 0.02 ...
##  $ gyros_arm_y             : num  0 -0.02 -0.02 -0.03 -0.03 -0.03 -0.03 -0.02 -0.03 -0.03 ...
##  $ gyros_arm_z             : num  -0.02 -0.02 -0.02 0.02 0 0 0 0 -0.02 -0.02 ...
##  $ accel_arm_x             : int  -288 -290 -289 -289 -289 -289 -289 -289 -288 -288 ...
##  $ accel_arm_y             : int  109 110 110 111 111 111 111 111 109 110 ...
##  $ accel_arm_z             : int  -123 -125 -126 -123 -123 -122 -125 -124 -122 -124 ...
##  $ magnet_arm_x            : int  -368 -369 -368 -372 -374 -369 -373 -372 -369 -376 ...
##  $ magnet_arm_y            : int  337 337 344 344 337 342 336 338 341 334 ...
##  $ magnet_arm_z            : int  516 513 513 512 506 513 509 510 518 516 ...
##  $ kurtosis_roll_arm       : Factor w/ 330 levels "","-0.02438",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_picth_arm      : Factor w/ 328 levels "","-0.00484",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_yaw_arm        : Factor w/ 395 levels "","-0.01548",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_roll_arm       : Factor w/ 331 levels "","-0.00051",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_pitch_arm      : Factor w/ 328 levels "","-0.00184",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_yaw_arm        : Factor w/ 395 levels "","-0.00311",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ max_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_arm             : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_roll_arm            : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_arm           : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_arm             : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_roll_arm      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_pitch_arm     : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ amplitude_yaw_arm       : int  NA NA NA NA NA NA NA NA NA NA ...
##  $ roll_dumbbell           : num  13.1 13.1 12.9 13.4 13.4 ...
##  $ pitch_dumbbell          : num  -70.5 -70.6 -70.3 -70.4 -70.4 ...
##  $ yaw_dumbbell            : num  -84.9 -84.7 -85.1 -84.9 -84.9 ...
##  $ kurtosis_roll_dumbbell  : Factor w/ 398 levels "","-0.0035","-0.0073",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_picth_dumbbell : Factor w/ 401 levels "","-0.0163","-0.0233",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ kurtosis_yaw_dumbbell   : Factor w/ 2 levels "","#DIV/0!": 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_roll_dumbbell  : Factor w/ 401 levels "","-0.0082","-0.0096",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_pitch_dumbbell : Factor w/ 402 levels "","-0.0053","-0.0084",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ skewness_yaw_dumbbell   : Factor w/ 2 levels "","#DIV/0!": 1 1 1 1 1 1 1 1 1 1 ...
##  $ max_roll_dumbbell       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_picth_dumbbell      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ max_yaw_dumbbell        : Factor w/ 73 levels "","-0.1","-0.2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ min_roll_dumbbell       : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_pitch_dumbbell      : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ min_yaw_dumbbell        : Factor w/ 73 levels "","-0.1","-0.2",..: 1 1 1 1 1 1 1 1 1 1 ...
##  $ amplitude_roll_dumbbell : num  NA NA NA NA NA NA NA NA NA NA ...
##   [list output truncated]
```
It shows that some of the columns seem to be partially empty.


```r
# This function will calculate the NA or "" ratio of a column. 
na.ratio <- function(row) {
  return(length(row[is.na(row)|row==""]) / length(row))
}

# This function will go through every column and calculate how empty it is.
ratios <- numeric()
for(row in 1:160){
  ratios[row] <- na.ratio(raw.data[,row])
}

# Check if there are columns with fewer data than others:
unique(ratios)
```

```
## [1] 0.0000000 0.9793089
```
This shows that the columns are either full or around 98% empty.

To keep the model simple and since data such as timestamps should not matter when identifying a physical activity we proceed to remove this kind of variables that do not provide meaningful information for the prediction.


```r
ratios[c(1,2,3,4,5,6,7)] = 1
pre.data <- raw.data[,ratios==0]
pre.test <- raw.test[,ratios==0]

# Then we generate the training and test sets from the pre.data:

toTrain = createDataPartition(pre.data$classe, p = 3/4, list=F)
trainSet = pre.data[toTrain,]
testSet = pre.data[-toTrain,]
```


## Modeling:

Based on previous experiences from this course will try good performing methods, will begin with boosting and random forest:

Boosting:


```r
cluster <- makeCluster(detectCores() - 1)
registerDoParallel(cluster)
fitControl <- trainControl(method = "cv",
                           number = 10,
                           allowParallel = TRUE)
start <- now()
gbm <- train(classe ~.,method = "gbm", data = trainSet, trControl = fitControl)
```

```
## Iter   TrainDeviance   ValidDeviance   StepSize   Improve
##      1        1.6094             nan     0.1000    0.2374
##      2        1.4598             nan     0.1000    0.1610
##      3        1.3587             nan     0.1000    0.1263
##      4        1.2791             nan     0.1000    0.1072
##      5        1.2115             nan     0.1000    0.0951
##      6        1.1529             nan     0.1000    0.0697
##      7        1.1074             nan     0.1000    0.0714
##      8        1.0628             nan     0.1000    0.0684
##      9        1.0202             nan     0.1000    0.0605
##     10        0.9832             nan     0.1000    0.0560
##     20        0.7587             nan     0.1000    0.0282
##     40        0.5322             nan     0.1000    0.0110
##     60        0.4056             nan     0.1000    0.0059
##     80        0.3250             nan     0.1000    0.0043
##    100        0.2674             nan     0.1000    0.0030
##    120        0.2251             nan     0.1000    0.0027
##    140        0.1903             nan     0.1000    0.0011
##    150        0.1768             nan     0.1000    0.0018
```

```r
stop <- now()
stopCluster(cluster)
registerDoSEQ()
elapsed <- as.period(interval(start,stop))
paste("Processing took",round(elapsed))
```

```
## [1] "Processing took 6M 6S"
```


```r
# Accuracy against the test set:
predictions <- predict(gbm, testSet)
confusionMatrix(testSet$classe,predictions)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1371   17    6    1    0
##          B   31  883   33    1    1
##          C    0   30  813   11    1
##          D    0    2   21  776    5
##          E    2    8   10    6  875
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9621          
##                  95% CI : (0.9563, 0.9672)
##     No Information Rate : 0.2863          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.952           
##  Mcnemar's Test P-Value : 0.001002        
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9765   0.9394   0.9207   0.9761   0.9921
## Specificity            0.9931   0.9834   0.9896   0.9932   0.9935
## Pos Pred Value         0.9828   0.9305   0.9509   0.9652   0.9711
## Neg Pred Value         0.9906   0.9856   0.9827   0.9954   0.9983
## Prevalence             0.2863   0.1917   0.1801   0.1621   0.1799
## Detection Rate         0.2796   0.1801   0.1658   0.1582   0.1784
## Detection Prevalence   0.2845   0.1935   0.1743   0.1639   0.1837
## Balanced Accuracy      0.9848   0.9614   0.9551   0.9846   0.9928
```

```r
# Accuracy against the training test:
predictions.training <- predict(gbm, trainSet)
confusionMatrix(trainSet$classe,predictions.training)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 4143   26   11    3    2
##          B   51 2751   43    3    0
##          C    0   45 2496   23    3
##          D    2    4   63 2331   12
##          E    2   21   15   27 2641
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9758          
##                  95% CI : (0.9732, 0.9782)
##     No Information Rate : 0.2852          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9694          
##  Mcnemar's Test P-Value : 1.229e-11       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9869   0.9663   0.9498   0.9765   0.9936
## Specificity            0.9960   0.9918   0.9941   0.9934   0.9946
## Pos Pred Value         0.9900   0.9659   0.9723   0.9664   0.9760
## Neg Pred Value         0.9948   0.9919   0.9891   0.9954   0.9986
## Prevalence             0.2852   0.1934   0.1786   0.1622   0.1806
## Detection Rate         0.2815   0.1869   0.1696   0.1584   0.1794
## Detection Prevalence   0.2843   0.1935   0.1744   0.1639   0.1839
## Balanced Accuracy      0.9915   0.9791   0.9719   0.9850   0.9941
```

We got a 96.1% accuracy, which even though it is high enough for this project, might not be the best choice.

Random Forest:


```r
cluster <- makeCluster(detectCores() - 1)
registerDoParallel(cluster)
fitControl <- trainControl(method = "cv",
                           number = 10,
                           allowParallel = TRUE)
start <- now()
randomForest <- train(classe ~.,method = "rf", data = trainSet, trControl = fitControl)
stop <- now()
stopCluster(cluster)
registerDoSEQ()
elapsed <- as.period(interval(start,stop))
paste("Processing took",round(elapsed))
```

```
## [1] "Processing took 19M 59S"
```


```r
# Accuracy against the test set:
predictions <- predict(randomForest, testSet)
confusionMatrix(testSet$classe,predictions)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1394    1    0    0    0
##          B    4  938    7    0    0
##          C    0    7  844    4    0
##          D    0    0    8  796    0
##          E    0    0    2    6  893
## 
## Overall Statistics
##                                           
##                Accuracy : 0.992           
##                  95% CI : (0.9891, 0.9943)
##     No Information Rate : 0.2851          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9899          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9971   0.9915   0.9803   0.9876   1.0000
## Specificity            0.9997   0.9972   0.9973   0.9980   0.9980
## Pos Pred Value         0.9993   0.9884   0.9871   0.9900   0.9911
## Neg Pred Value         0.9989   0.9980   0.9958   0.9976   1.0000
## Prevalence             0.2851   0.1929   0.1756   0.1644   0.1821
## Detection Rate         0.2843   0.1913   0.1721   0.1623   0.1821
## Detection Prevalence   0.2845   0.1935   0.1743   0.1639   0.1837
## Balanced Accuracy      0.9984   0.9944   0.9888   0.9928   0.9990
```

```r
# Accuracy against the training test:
predictions.training <- predict(randomForest, trainSet)
confusionMatrix(trainSet$classe,predictions.training)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 4185    0    0    0    0
##          B    0 2848    0    0    0
##          C    0    0 2567    0    0
##          D    0    0    0 2412    0
##          E    0    0    0    0 2706
## 
## Overall Statistics
##                                      
##                Accuracy : 1          
##                  95% CI : (0.9997, 1)
##     No Information Rate : 0.2843     
##     P-Value [Acc > NIR] : < 2.2e-16  
##                                      
##                   Kappa : 1          
##  Mcnemar's Test P-Value : NA         
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            1.0000   1.0000   1.0000   1.0000   1.0000
## Specificity            1.0000   1.0000   1.0000   1.0000   1.0000
## Pos Pred Value         1.0000   1.0000   1.0000   1.0000   1.0000
## Neg Pred Value         1.0000   1.0000   1.0000   1.0000   1.0000
## Prevalence             0.2843   0.1935   0.1744   0.1639   0.1839
## Detection Rate         0.2843   0.1935   0.1744   0.1639   0.1839
## Detection Prevalence   0.2843   0.1935   0.1744   0.1639   0.1839
## Balanced Accuracy      1.0000   1.0000   1.0000   1.0000   1.0000
```

As expected the random forest has better accuracy than boosting, since it has a 99.3% it is considered good enough for this project purposes.  

## Prediction:


```r
predict(randomForest, pre.test)
```

```
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

```r
predict(gbm, pre.test)
```

```
##  [1] C A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

Predictions using both methods gave the same results:


```r
as.data.frame(matrix(predict(randomForest, pre.test),nrow = 1, dimnames = list(NULL, as.character(1:20))))
```

```
##   1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
## 1 B A B A A E D B A  A  B  C  B  A  E  E  A  B  B  B
```


## Conclusion:

This is a clear example of why we often hear that the model is not necessarily the main factor in machine learning but the data itself, due to the abundant data we had to training the model we managed to achieve very high accuracy without having to tweak the models a lot.

