# Machine Learning Algortithm  -- Practical Machine Learning
========================================================

We will try to construct a macine learning algorithm to predict future outcomes on the test data. 
I am using Random Forest Modeling, and got over 99% accuracy on my training set of 70% of the total data. All my submitted test cases were succesful.

## Loading and cleaning Data

```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```
 
```r
testBulk <- read.csv("pml-testing.csv", na.strings = c("NA", ""))
trainBulk <- read.csv("pml-training.csv", na.strings = c("NA", ""))
NAs <- apply(trainBulk, 2, function(x) {
    sum(is.na(x))
})
cleanTrain <- trainBulk[, which(NAs == 0)]
cleanTest <- testBulk[, which(NAs == 0)]
```


## Building data sets for training and cross validation. 
### Using 70% for training and 30% for Cross Validation. None generated for testing since that set is already provided.

```r
trainIndex <- createDataPartition(y = cleanTrain$classe, p = 0.7, list = FALSE)
trainSet <- cleanTrain[trainIndex, ]
crossValidationSet <- cleanTrain[-trainIndex, ]
# Removing variables that have time, or names in it, also new_window.
# Columns 1..6
removeIndex <- as.integer(c(1, 2, 3, 4, 5, 6))
trainSet <- trainSet[, -removeIndex]
testSet <- cleanTest[, -removeIndex]
```


## Training...

```r
mytrControl = trainControl(method = "cv", number = 4)
modelFit <- train(trainSet$classe ~ ., data = trainSet, method = "rf", trControl = mytrControl)
```

```
## Loading required package: randomForest
## randomForest 4.6-7
## Type rfNews() to see new features/changes/bug fixes.
```

```r
modelFit
```

```
## Random Forest 
## 
## 13737 samples
##    53 predictors
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (4 fold) 
## 
## Summary of sample sizes: 10301, 10303, 10303, 10304 
## 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy  Kappa  Accuracy SD  Kappa SD
##   2     1         1      0.003        0.003   
##   30    1         1      0.001        0.002   
##   50    1         1      8e-04        0.001   
## 
## Accuracy was used to select the optimal model using  the largest value.
## The final value used for the model was mtry = 27.
```



## Calculation the errors using the Cross Validation Set.

```r
predicted <- predict(modelFit, crossValidationSet)
SampleError <- sum(predicted == crossValidationSet$classe)/nrow(crossValidationSet)
```

So the Out of Sample Error we get is: 0.9981


## Generating data for the prediction vector for the Assigment Submission

```r
answers <- predict(modelFit, testSet)
```

