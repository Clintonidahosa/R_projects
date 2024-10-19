# NHANES Data Analysis Project

## Overview
The NHANES (National Health and Nutrition Examination Survey) is a program run by the CDC to assess the health and nutritional status of adults and children in the US. It combines survey questions and physical examinations, including medical and physiological measurements and laboratory tests. NHANES examines a representative sample of about 5,000 people each year, providing crucial data to determine the prevalence of diseases and risk factors, establish national standards, and support epidemiology studies and health sciences research. This information helps develop public health policy, design health programs and services, and expand the nation's health knowledge.

### Dataset
- **Dataset Link**: [NHANES Dataset](https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/nhanes.csv)
- **Data Dictionary**: [Link to Data Dictionary](https://github.com/HackBio-Internship/public_datasets/blob/main/R/nhanes_dd.csv)

## Tasks Completed
1. Processed all NA values (either by deleting or converting to zero).
2. Visualized the distribution of BMI, Weight, Weight in pounds (weight * 2.2), and Age using histograms.
3. Calculated the mean 60-second pulse rate for all participants.
4. Determined the range of values for diastolic blood pressure in all participants.
5. Calculated the variance and standard deviation for income among all participants.
6. Visualized the relationship between weight and height, colored by:
   - Gender
   - Diabetes
   - Smoking Status
7. Conducted t-tests between the following variables and made conclusions based on P-Value:
   - Age and Gender
   - BMI and Diabetes
   - Alcohol Year and Relationship Status

## Solution Code

```
# Import dataset from GitHub and assign name
nhanes = read.csv("https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/nhanes.csv")

# View dataset to ensure it is loaded
View(nhanes)
```
<img src="https://drive.google.com/uc?export=view&id=1BZCc2RQBLedMaYE8-OJasBFqC0Ld2VRF" alt="Image description" width="500" height="150">


```
# Check all columns for distinct values to check for NA
lapply(nhanes, unique)

# TASK 1 - DELETE ROWS WITH NA IN RELEVANT COLUMNS
cleaned_data1 <- nhanes[complete.cases(nhanes[,c("BMI",
                                                  "Height",
                                                  "Age",
                                                  "RelationshipStatus",
                                                  "Income",
                                                  "Weight",
                                                  "Pulse",
                                                  "BPDia",
                                                  "Diabetes",
                                                  "AlcoholYear",
                                                  "SmokingStatus")]), ]

# TASK 2 - Visualize the distribution of BMI, Weight, Weight in pounds (weight * 2.2) and Age with a histogram.
# Create column (weight * 2.2)
cleaned_data1$Mod_Weight <- cleaned_data1$Weight * 2.2

# Setting the plotting area with 1 row and 2 columns
par(mfrow = c(2, 2)) 

# Creating and customizing the histograms
hist(cleaned_data1$BMI, main="BMI histogram", xlab="BMI", col="red", border="black")
hist(cleaned_data1$Weight, main="Weight histogram", xlab="Weight(kg)", col="blue", border="black")
hist(cleaned_data1$Mod_Weight, main="Weight(pounds) histogram", xlab="Weight(pounds)", col="green", border="black")
hist(cleaned_data1$Age, main="Age histogram", xlab="Age", col="yellow", border="black")
```

```

# TASK 3 - What’s the mean 60-second pulse rate for all participants in the data?
Pulse_mean = mean(cleaned_data1$Pulse)
print(Pulse_mean)
```

```

# TASK 4 - What’s the range of values for diastolic blood pressure in all participants?
BPDia_range = range(cleaned_data1$BPDia)
print(BPDia_range)
```

```

# TASK 5 - What’s the variance and standard deviation for income among all participants?
Var_Income = var(cleaned_data1$Income)
print(Var_Income)
SD_Income = sd(cleaned_data1$Income)
print(SD_Income)
```

```

# TASK 6 - Visualize the relationship between weight and height
plot(cleaned_data1$Weight, cleaned_data1$Height, 
     main = "Weight vs Height",
     xlab = "Weight(kg)",
     ylab = "Height(cm)",
     col = "light blue",
     lwd = 2)
```

```

# TASK 6bi - Color the points by gender
unique(cleaned_data1$Gender)   # shows distinct values in Gender column
Gender_color <- c("male"="red", "female"="blue")  # assigns colors to values in Gender column
plot(cleaned_data1$Weight, cleaned_data1$Height, 
     main = "Scattered plot colored by gender",
     xlab = "Weight(kg)",
     ylab = "Height(cm)",
     col = Gender_color[cleaned_data1$Gender],
     lwd = 2)
legend("topright", legend=names(Gender_color), col=Gender_color, pch=19)
```

```

# TASK 6bii - Color the points by Diabetes
unique(cleaned_data1$Diabetes)   # shows distinct values in Diabetes column
Diabetes_color <- c("No"="black", "Yes"="purple")  # assigns colors to values in Diabetes column
plot(cleaned_data1$Weight, cleaned_data1$Height, 
     main = "Scattered plot colored by Diabetes",
     xlab = "Weight(kg)",
     ylab = "Height(cm)",
     col = Diabetes_color[cleaned_data1$Diabetes],
     lwd = 2)
legend("topright", legend=names(Diabetes_color), col=Diabetes_color, pch=19)
```

```

# TASK 6biii - Color the points by SmokingStatus
unique(cleaned_data1$SmokingStatus)   # shows distinct values in SmokingStatus column
SmokingStatus_color <- c("Current"="green", "Never"="orange", "Former"="black")  # assigns colors to values in SmokingStatus column
plot(cleaned_data1$Weight, cleaned_data1$Height, 
     main = "Scattered plot colored by SmokingStatus",
     xlab = "Weight(kg)",
     ylab = "Height(cm)",
     col = SmokingStatus_color[cleaned_data1$SmokingStatus],
     lwd = 2)
legend("topright", legend=names(SmokingStatus_color), col=SmokingStatus_color, pch=19)
```

```

# TASK 7a - Conduct t-test between Age and Gender and interpret P-value
t_test_1 <- t.test(Age ~ Gender, data = cleaned_data1)
print(t_test_1)
# p-value > 0.05 hence, there is no statistically significant difference between the average ages of the female and male groups in the sample (accept null hypothesis).
```

```

# TASK 7b - Conduct t-test between BMI and Diabetes and interpret P-value
t_test_2 <- t.test(BMI ~ Diabetes, data = cleaned_data1)
print(t_test_2)
# p-value < 0.05 hence there is a significant difference in BMI between the two groups (reject null hypothesis).
```

```

# TASK 7c - Conduct t-test between Alcohol Year and Relationship Status and interpret P-value
t_test_3 <- t.test(AlcoholYear ~ RelationshipStatus, data = cleaned_data1)
print(t_test_3)
# p-value < 0.05 hence there is a significant difference in alcohol consumption between the two groups (reject null hypothesis).
```


## Conclusion
This project provides insights into the health and nutritional status of participants in the NHANES dataset. The analyses performed highlight important relationships between various health metrics and demographic factors, contributing to a better understanding of public health in the US.
