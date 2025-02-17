[<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/banner.png" width="888" alt="Visit QuantNet">](http://quantlet.de/)

## [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/qloqo.png" alt="Visit QuantNet">](http://quantlet.de/) **SPL_HousePriceML_dataProcessing** [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/QN2.png" width="60" alt="Visit QuantNet 2.0">](http://quantlet.de/)

```yaml

Name of Quantlet: SPL_HousePriceML_dataProcessing
Published in:  'Statistic Programming Language - Student Project on ''Predicting House Prices using Machine Learning Techniques'' '
Description: 'Process the raw data for further analysis, including impute NA values, convert category variables into numeric.'
Keywords: data adjustment, scaling, price, origin, vif, function
Author: Dennis Koehn, Linxi Wang, Mingyang Li
Submitted: Sun, January 8, 2016 by Mingyang Li
Datafile: 'ames_test.csv, ames_train.csv'
Output: basic_processing.RData


```

### R Code
```r

############################ data processing ##################################

### setwd('F:/PHD/IRTG/courses/SPL/Quantlet/data_processing')
rm(list = ls())
graphics.off()

basic_preprocessing = function(X_com, y, scaler = "gaussian") {
    source("replace_ratings.R")
    source("convert_categoricals.R")
    source("impute_data.R")
    source("encode_time_variables.R")
    source("impute_outliers.R")
    source("scale_data.R")
    source("delete_nearzero_variables.R")
    X_ratings = replace_ratings(X_com)
    X_imputed = naive_imputation(X_ratings)
    X_no_outlier = data.frame(lapply(X_imputed, iqr_outlier))
    X_time_encoded = include_quarter_dummies(X_no_outlier)
    X_scaled = scale_data(X_time_encoded, scale_method = scaler)
    X_encoded = data.frame(lapply(X_scaled, cat_to_dummy))
    X_com = delect_nz_variable(X_encoded)
    # remerge train data
    idx_train = c(1:length(y))
    train = cbind(X_com[idx_train, ], y)
    test = X_com[-idx_train, ]
    return(list(train = train, X_com = X_com, test = test))  # return without id 
}

train = read.csv("ames_train.csv", header = T)
test = read.csv("ames_test.csv", header = T)

# split target variable and feature matrix
y = train[, "SalePrice"]  # target variable SalePrice
X = train[, -81]  # feature matrix without target variable
# merge test and train features to get the complete feature matrix
X_com = rbind(X, test)

# apply preprocessing
basic_data = basic_preprocessing(X_com, y)
# get the complete input matrix
X_com = basic_data$X_com
write.csv(X_com, "inputMatrix_preprocessed.csv")
# get the training data with labels
train = basic_data$train
write.csv(train,"train_preprocessed.csv")
# get the test data to be predicted in the same format
test = basic_data$test
write.csv(train,"test_preprocessed.csv")

# remove redundant variables and functions
all_var = ls()
all_var = all_var[all_var != "basic_data"]
rm(list = c(all_var, "all_var"))

### save workplace for further use
save.image("basic_processing.RData")

```

automatically created on 2018-05-28