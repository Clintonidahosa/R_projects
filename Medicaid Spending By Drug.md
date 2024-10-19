# MEDICAID SPENDING BY DRUG R PROJECT
***
## Installing Relevant Packages
### To ensure that all necessary packages are installed, run the following code:

```r
required_packages <- c("dplyr", "ggplot2", "tidyr", "reshape2", "hunspell", "caret", "randomForest")  # Added caret and randomForest

# Check which packages are not installed
packages_to_install <- required_packages[!(required_packages %in% installed.packages()[,"Package"])]

# Install the missing packages
if(length(packages_to_install)) {
  install.packages(packages_to_install)
}
```
## Loading Relevant Libraries
### Load the required libraries for data manipulation, visualization, and machine learning:
```
library(dplyr)
library(hunspell)
library(ggplot2)
library(tidyr)
library(reshape2)
library(caret)  # For machine learning
library(randomForest)  # For random forest model
```
## Dataset Importation
### Import the dataset from GitHub with an extended timeout due to large data:
```
# Set the timeout to 300 seconds
options(timeout = 300)  # Dataset takes a long time to load 
url <- "https://raw.githubusercontent.com/Clintonidahosa/R_projects/refs/heads/main/DSD_MCD_RY24_P06_V20_D22_BGM.csv"
drugdata <- read.csv(url)
```

***
# Data Cleaning and Inspection
## Visual Inspection
### Perform a visual inspection of the dataset:
```
View(drugdata)
```
## Missing Data
### Check for missing data in each column:
```
missing_data <- colSums(is.na(drugdata))
View(missing_data)  # NA values present but not cleaned because they are relevant for Exp. Analysis.
```
## Data Types
### Check the data types of each column:
```
col_type <- sapply(drugdata, class)
View(col_type)  # Column types are appropriate
```
## Duplicate Rows
### Check for duplicate rows:
```
duplicate_rows <- drugdata[duplicated(drugdata), ]
View(duplicate_rows)  # There are no duplicate rows
```
## Spelling Errors in Gnrc_Name
### Check for spelling errors in the Gnrc_Name column:
```
grouped_data <- group_by(drugdata, Brnd_Name, Mftr_Name)  # Group Brnd_Name and Mftr_Name columns
filtered_data <- filter(grouped_data, n() > 1 & n_distinct(Gnrc_Name) > 1)  # Filter the grouped data to display rows with same Brnd_Name and Mftr_Name but distinct Gnrc_Name
arranged_data <- arrange(filtered_data, Brnd_Name, Mftr_Name, Gnrc_Name) # Arrange data for visual inspection
View(arranged_data)  # No similarly spelt generic name hence, no error in Gnrc_Name column
```
## Spelling Errors in Brnd_Name
### Check for spelling errors in the Brnd_Name column:
```
grouped_data2 <- group_by(drugdata, Gnrc_Name, Mftr_Name) 
filtered_data2 <- filter(grouped_data2, n() > 1 & n_distinct(Brnd_Name) > 1)
arranged_data2 <- arrange(filtered_data2, Gnrc_Name, Mftr_Name, Brnd_Name)
View(arranged_data2)  # Output too large for visual inspection
```
## Spelling Errors in Mftr_Name
### Check for spelling errors in the Mftr_Name column:
```
grouped_data3 <- group_by(drugdata, Brnd_Name, Gnrc_Name)
filtered_data3 <- filter(grouped_data3, n() > 1 & n_distinct(Mftr_Name) > 1)
arranged_data3 <- arrange(filtered_data3, Brnd_Name, Gnrc_Name, Mftr_Name)
View(arranged_data3)  # Output too large for visual inspection
```
## Check for Extra Characters
### Create a function to check for extra characters in the text columns:
```
columns_to_check <- c("Brnd_Name", "Gnrc_Name", "Mftr_Name")  # Defining columns to check 

check_characters <- function(column) {  
  has_punctuation_before <- grepl("^[.,]", column) # To check for '.' or ',' at the start
  has_punctuation_after <- grepl("[.,]$", column) # To check for '.' or ',' at the end
  has_leading_whitespace <- grepl("^\\s", column) # To check for leading whitespace
  has_trailing_whitespace <- grepl("\\s$", column) # To check for trailing whitespace
  
  return(data.frame(has_punctuation_before, has_punctuation_after,
                    has_leading_whitespace, has_trailing_whitespace))
}

check_characters_in_columns <- lapply(drugdata[columns_to_check], check_characters) # Apply function to each column
combine_results <- do.call(cbind, check_characters_in_columns)  # Combine result into one dataframe
View(combine_results)  # No full stops, commas, trailing or leading whitespaces in the values of the text columns
```
## Data Range Validity
### Check for data range validity:
```
summary_stats <- drugdata %>%
  summarise(across(where(is.numeric), list(min = min, max = max), na.rm = TRUE))  # Calculate min and max for all numeric columns
View(summary_stats)  # All values are within normal range. No negative value.
```
### Conclusion from Data Inspection - The raw dataset is clean and ready to be explored.
*
*
*

# Exploratory Data Analysis and Visualizations
### Visualize the overall structure of the dataset:
```
View(head(drugdata))
str(drugdata)
```
## Summary Statistics
### Generate summary statistics for all numerical columns:
```
summary(drugdata)
```
## Spending Trends Visualization
### Visualize spending trends:
```
long_drug_data1 <- pivot_longer(
  data = drugdata,
  cols = starts_with("Tot_spndng"),  # Selecting "Total spending" columns of each year
  names_to = "Year",                   # Creates new column "Year" for names
  values_to = "Total_Spending"         # Creates new column "Total spending" for values
)
View(long_drug_data1)

# Remove NA values in Total Spending column
long_drug_data <- long_drug_data1[!is.na(long_drug_data1$Total_Spending), ]
View(long_drug_data)

# Confirm removal of NA values
nrow(long_drug_data1)
nrow(long_drug_data)

# Convert "Year" to categorical type
long_drug_data$Year <- as.factor(long_drug_data$Year)
```
## Top 10 Drugs by Total Spending
### Aggregate total spending per drug and find the top 10 drugs:
```
top_10_drugs <- drugdata %>%
  group_by(Brnd_Name) %>%
  summarise(Total_Spending_All_Years = sum(across(starts_with("Tot_spndng")), na.rm = TRUE)) %>%
  arrange(desc(Total_Spending_All_Years)) %>%
  slice(1:10)  # Top 10 drugs
```
## Bar Plot
### Create a bar plot showing the top 10 drugs by total spending:
```
ggplot(top_10_drugs, aes(x = reorder(Brnd_Name, -Total_Spending_All_Years), y = Total_Spending_All_Years)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  coord_flip() +
  labs(title = "Top 10 Drugs by Total Spending (2018-2022)",
       x = "Drug Brand",
       y = "Total Spending (USD)") +
  theme_minimal()
```
## Spending vs Total Spending Scatter Plot
### Create a scatter plot of average spending per dosage unit vs total spending:
```
ggplot(drugdata, aes(x = Avg_Spnd_Per_Dsg_Unt_Wghtd_2022, y = Tot_Spndng_2022)) +
  geom_point(aes(color = Brnd_Name), size = 3, alpha = 0.6) +
  labs(title = "Spending per Dosage Unit vs Total Spending (2022)",
       x = "Average Spending per Dosage Unit (2022)",
       y = "Total Spending (2022)") +
  theme_minimal() +
  theme(legend.position = "none")
```
## Spending Distributions by Year
### Create a boxplot showing spending distributions by year:
```
ggplot(long_drug_data, aes(x = Year, y = Total_Spending)) +
  geom_boxplot(aes(fill = Year)) +
  labs(title = "Distribution of Drug Spending by Year",
       x = "Year",
       y = "Total Spending (USD)") +
  theme_minimal() +
  theme(legend.position = "none")
```
## Correlation Heatmap
### Calculate the correlation matrix for numeric columns and plot a heatmap:
```
numeric_data <- drugdata %>%
  select(where(is.numeric))  # Select only numeric columns

cor_matrix <- cor(numeric_data, use = "complete.obs")

# Plot a heatmap of correlations
melted_cor_matrix <- melt(cor_matrix)

ggplot(melted_cor_matrix, aes(Var1, Var2, fill = value)) +
  geom_tile(color = "white") +
  scale_fill_gradient2(low = "blue", high = "red", mid = "white", 
                       midpoint = 0, limit = c(-1, 1), 
                       name = "Correlation") +
  theme_minimal() +
  labs(title = "Correlation Heatmap of Drug Spending Variables") +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```
*
*
*
# Predictive Modeling
## Random Forest Model
### Create a random forest model to predict total spending based on available features:
```
# Splitting dataset into training and testing sets (80% train, 20% test)
set.seed(42)  # For reproducibility
train_index <- createDataPartition(drugdata$Tot_Spndng_2022, p = 0.8, list = FALSE)
train_data <- drugdata[train_index, ]
test_data <- drugdata[-train_index, ]

# Fit a random forest model
rf_model <- randomForest(Tot_Spndng_2022 ~ ., data = train_data, ntree = 100)

# Make predictions
predictions <- predict(rf_model, test_data)

# Evaluate model performance
rf_performance <- postResample(predictions, test_data$Tot_Spndng_2022)
print(rf_performance)  # RMSE and R-squared
```

## Conclusion
### This analysis highlights trends and relationships in drug spending, identifying key insights that can drive decision-making in healthcare.


