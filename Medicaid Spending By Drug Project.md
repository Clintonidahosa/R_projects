# MEDICAID SPENDING BY DRUG R PROJECT
***
### Installing Relevant Packages - To ensure that all necessary packages are installed, run the following code:

```
required_packages <- c("dplyr", "ggplot2", "tidyr", "reshape2", "hunspell", "caret", "randomForest")  # Added caret and randomForest

# Check which packages are not installed
packages_to_install <- required_packages[!(required_packages %in% installed.packages()[,"Package"])]

# Install the missing packages
if(length(packages_to_install)) {
  install.packages(packages_to_install)
}
```

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

### Import the dataset from GitHub with an extended timeout due to large data:
```
# Set the timeout to 300 seconds
options(timeout = 300)  # Dataset takes a long time to load 
url <- "https://raw.githubusercontent.com/Clintonidahosa/R_projects/refs/heads/main/Medicaid%20Spending%20by%20Drug%20dataset.csv"
drugdata <- read.csv(url)
```

***
# Data Inspection and Cleaning
### Perform a visual inspection of the dataset:
```
View(drugdata)
```
<img src="https://drive.google.com/uc?export=view&id=1P86WFR4Ycb_KEEf30w94qVxCXX85mDnp" alt="Description of Image" width="500"/>


### Check for missing data in each column:
```
missing_data <- colSums(is.na(drugdata))
View(missing_data)  # NA values present but not cleaned because they are relevant for Exp. Analysis.
```
<img src="https://drive.google.com/uc?export=view&id=1GTm7dtO8JoaN8WElAzjYUul4nVJEGgqQ" alt="Description of Image" width="500"/>

### Check the data types of each column:
```
col_type <- sapply(drugdata, class)
View(col_type)  # Column types are appropriate
```
<img src="https://drive.google.com/uc?export=view&id=1pD__XhOKeJoepiVx6rY8w27aKTBkuhmH" alt="Description of Image" width="500"/>

### Check for duplicate rows:
```
duplicate_rows <- drugdata[duplicated(drugdata), ]
View(duplicate_rows)  # There are no duplicate rows
```

### Check for spelling errors in the Gnrc_Name column:
```
grouped_data <- group_by(drugdata, Brnd_Name, Mftr_Name)  # Group Brnd_Name and Mftr_Name columns
filtered_data <- filter(grouped_data, n() > 1 & n_distinct(Gnrc_Name) > 1)  # Filter the grouped data to display rows with same Brnd_Name and Mftr_Name but distinct Gnrc_Name
arranged_data <- arrange(filtered_data, Brnd_Name, Mftr_Name, Gnrc_Name) # Arrange data for visual inspection
View(arranged_data)  # No similarly spelt generic name hence, no error in Gnrc_Name column
```

### Check for spelling errors in the Brnd_Name column:
```
grouped_data2 <- group_by(drugdata, Gnrc_Name, Mftr_Name) 
filtered_data2 <- filter(grouped_data2, n() > 1 & n_distinct(Brnd_Name) > 1)
arranged_data2 <- arrange(filtered_data2, Gnrc_Name, Mftr_Name, Brnd_Name)
View(arranged_data2)  # Output too large for visual inspection
```

### Check for spelling errors in the Mftr_Name column:
```
grouped_data3 <- group_by(drugdata, Brnd_Name, Gnrc_Name)
filtered_data3 <- filter(grouped_data3, n() > 1 & n_distinct(Mftr_Name) > 1)
arranged_data3 <- arrange(filtered_data3, Brnd_Name, Gnrc_Name, Mftr_Name)
View(arranged_data3)  # Output too large for visual inspection
```

### Check for Extra Characters - Create a function to check for extra characters in the text columns:
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

### Check for data range validity:
```
summary_stats <- drugdata %>%
  summarise(across(where(is.numeric), list(min = min, max = max), na.rm = TRUE))  # Calculate min and max for all numeric columns
View(summary_stats)  # All values are within normal range. No negative value.
```
<img src="https://drive.google.com/uc?export=view&id=1p82xPyYab9rTq6F2BbDZ6GvQEoiRj2YJ" alt="Description of Image" width="500"/>

#### Conclusion from Data Inspection - The raw dataset is clean and ready to be explored.
*
*
*

# Exploratory Data Analysis and Visualizations
### Visualize the overall structure of the dataset:
```
View(head(drugdata))
str(drugdata)
```
<img src="https://drive.google.com/uc?export=view&id=1q_qvGJlmPH5ymMoUSRmIbBQaGjlK04ia" alt="Description of Image" width="500"/>

### Generate summary statistics for all numerical columns:
```
summary(drugdata)
```
<img src="https://drive.google.com/uc?export=view&id=1ei9viNeHUvBlcUusT02mtTeGa1DQ3NL1" alt="Description of Image" width="500"/>

### Spending Trends Visualization - Visualize spending trends:
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

### Top 10 Drugs by Total Spending - Aggregate total spending per drug and find the top 10 drugs:
```
top_10_drugs <- drugdata %>%
  group_by(Brnd_Name) %>%
  summarise(Total_Spending_All_Years = sum(across(starts_with("Tot_spndng")), na.rm = TRUE)) %>%
  arrange(desc(Total_Spending_All_Years)) %>%
  slice(1:10)  # Top 10 drugs
```

### Bar Plot - Create a bar plot showing the top 10 drugs by total spending:
```
ggplot(top_10_drugs, aes(x = reorder(Brnd_Name, -Total_Spending_All_Years), y = Total_Spending_All_Years)) +
  geom_bar(stat = "identity", fill = "steelblue") +
  coord_flip() +
  labs(title = "Top 10 Drugs by Total Spending (2018-2022)",
       x = "Drug Brand",
       y = "Total Spending (USD)") +
  theme_minimal()
```
<img src="https://drive.google.com/uc?export=view&id=1y6KVVZEBFpRHavroB2Ad7ko0TIoOPyC0" alt="Description of Image" width="500"/>

### Spending vs Total Spending Scatter Plot - average spending per dosage unit vs total spending:
```
ggplot(drugdata, aes(x = Avg_Spnd_Per_Dsg_Unt_Wghtd_2022, y = Tot_Spndng_2022)) +
  geom_point(aes(color = Brnd_Name), size = 3, alpha = 0.6) +
  labs(title = "Spending per Dosage Unit vs Total Spending (2022)",
       x = "Average Spending per Dosage Unit (2022)",
       y = "Total Spending (2022)") +
  theme_minimal() +
  theme(legend.position = "none")
```
<img src="https://drive.google.com/uc?export=view&id=1zRu3_HlGUCys-ib5Phgk2pWNDmRiVKVx" alt="Description of Image" width="500"/>

### Spending Distributions by Year - Create a boxplot showing spending distributions by year:
```
ggplot(long_drug_data, aes(x = Year, y = Total_Spending)) +
  geom_boxplot(aes(fill = Year)) +
  labs(title = "Distribution of Drug Spending by Year",
       x = "Year",
       y = "Total Spending (USD)") +
  theme_minimal() +
  theme(legend.position = "none")
```
<img src="https://drive.google.com/uc?export=view&id=108lbg4Umze_CUb-gTI1rjxxUwH7itHKR" alt="Description of Image" width="500"/>

### Correlation Heatmap - Calculate the correlation matrix for numeric columns and plot a heatmap:
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
<img src="https://drive.google.com/uc?export=view&id=1YENAvCT9M7JWev8CYmbsotQzqPwc43Oa" alt="Description of Image" width="500"/>

*
*
*
# Predictive Modeling ( Random Forest Model)
### Create a random forest model to predict total spending based on available features:
```
# Select only 2022 data columns to avoid missing values
drugdata_2022 <- drugdata %>%
  select(Brnd_Name, Gnrc_Name, Mftr_Name, Tot_Spndng_2022, Tot_Dsg_Unts_2022, Tot_Clms_2022, Avg_Spnd_Per_Dsg_Unt_Wghtd_2022, Avg_Spnd_Per_Clm_2022, Outlier_Flag_2022)

# Splitting dataset into training and testing sets (80% train, 20% test)
set.seed(42)  # For reproducibility
train_index <- createDataPartition(drugdata_2022$Tot_Spndng_2022, p = 0.8, list = FALSE)
train_data <- drugdata_2022[train_index, ]
test_data <- drugdata_2022[-train_index, ]


# Fit a random forest model
rf_model <- randomForest(Tot_Spndng_2022 ~ ., data = train_data, ntree = 100)

# Make predictions on the test set
predictions <- predict(rf_model, test_data)

# Evaluate model performance
rf_performance <- postResample(predictions, test_data$Tot_Spndng_2022)
print(rf_performance)  # RMSE and R-squared

```
<img src="https://drive.google.com/uc?export=view&id=1DfZrPXfIaAdu3deqDU9YlACH4fbs3844" alt="Description of Image" width="500"/>

### Plotting predicted vs actual Total spending 2022
```
# Create a dataframe to store actual vs predicted values
comparison_df <- data.frame(
  Actual = test_data$Tot_Spndng_2022,
  Predicted = predictions
)

# Plot predicted vs actual spending
ggplot(comparison_df, aes(x = Actual, y = Predicted)) +
  geom_point(color = "blue", alpha = 0.6) +
  geom_abline(slope = 1, intercept = 0, linetype = "dashed", color = "red") +
  labs(title = "Predicted vs Actual Total Spending (2022)",
       x = "Actual Spending (2022)",
       y = "Predicted Spending (2022)") +
  theme_minimal() +
  theme(plot.title = element_text(hjust = 0.5))  # Center the title
```
<img src="https://drive.google.com/uc?export=view&id=1xD26uapc37IEls_Rb4wQ0sLeaFqjuVHq" alt="Description of Image" width="500"/>

### Conclusion - This analysis highlights trends and relationships in drug spending, identifying key insights that can drive decision-making in healthcare.


