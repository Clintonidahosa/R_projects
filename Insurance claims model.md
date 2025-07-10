🚗 Insurance Claim Modeling – On the Road Car Insurance
The Head of Data at On the Road Car Insurance requested a machine learning pipeline to investigate customer data and identify the feature most predictive of whether a customer will make a claim.

🎯 Goals
Clean the data and remove missing values and the id column.

Build Logistic Regression models for each feature and find the one with the highest accuracy.

Output a data frame best_feature_df with the best feature and its accuracy.




✅ R Code
# Load libraries
install.packages(c("visdat", "ggfortify"))
library(readr)
library(dplyr)
library(glue)
library(yardstick)
library(visdat)
library(ggfortify)
library(ggplot2)

# Read and inspect data
data <- read.csv("car_insurance.csv")
vis_miss(data)
colSums(is.na(data))

# Clean missing values and select relevant columns
new_data2 <- data %>%
  mutate(
    credit_score = ifelse(is.na(credit_score), mean(credit_score, na.rm = TRUE), credit_score),
    annual_mileage = ifelse(is.na(annual_mileage), mean(annual_mileage, na.rm = TRUE), annual_mileage)
  ) %>%
  select(age, driving_experience, income, credit_score, vehicle_ownership, annual_mileage, outcome)

# Split data
set.seed(124)
sample_index <- sample(seq_len(nrow(new_data2)), size = 0.8 * nrow(new_data2))
training_data <- new_data2[sample_index, ]
test_data <- new_data2[-sample_index, ]

# Logistic Regression Function
LGM <- function(formula){
  model <- glm(formula, training_data, family = binomial)
  prediction <- test_data %>%
    mutate(
      pred_outcome = ifelse(predict(model, test_data, type = "response") > 0.5, 1, 0),
      actual = as.factor(outcome),
      predicted = as.factor(pred_outcome)
    )
  confusion <- conf_mat(prediction, truth = actual, estimate = predicted)
  accuracy_result <- accuracy(prediction, truth = actual, estimate = predicted)
  return(accuracy_result)
}

# Generate formula for each feature
vec_colnames <- setdiff(colnames(new_data2), "outcome")
vec <- paste0("outcome~", vec_colnames)

# Apply LGM function and collect results
feature <- list()
for(i in 1:length(vec)){
  feature[[i]] <- LGM(as.formula(vec[i]))
}

final_result <- bind_rows(feature)

# Extract best feature
best_feature_dfs <- final_result %>%
  select(!.estimator) %>%
  slice_max(.estimate, n = 1) %>%
  mutate(.metric = vec_colnames[which.max(final_result$.estimate)]) %>%
  rename(best_accuracy = .estimate, best_feature = .metric)

best_feature_df <- data.frame(best_feature_dfs)
best_feature_df
