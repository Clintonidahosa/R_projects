# Medicaid Drug Spending Analysis (2018-2022)

## Overview
This project involves analyzing Medicaid drug spending data from 2018 to 2022. The dataset includes variables such as total spending, average spending per dosage unit, manufacturer information, and clinical indications. The analysis focuses on trends in drug spending, identifying outliers, and performing predictive modeling to forecast future spending patterns.

## Project Objectives
- **Data Cleaning**: Handle missing values and outlier flags across multiple years.
- **Exploratory Data Analysis (EDA)**: Visualize trends in Medicaid drug spending and dosage units over time.
- **Predictive Modeling**: Use machine learning techniques (Random Forest) to predict total spending for 2022 based on prior years’ data.
- **Insights**: Identify drugs with significant spending trends, potential outliers, and provide strategic insights on drug spending patterns.

## Dataset Description
- **Source**: Medicaid Spending by Drug, 2018-2022.
- **Key Variables**:
  - **Brand Name & Generic Name**: Identifying each drug.
  - **Manufacturer**: Details about the manufacturers.
  - **Total Spending (2018-2022)**: Annual total spending on each drug.
  - **Total Claims & Dosage Units**: Number of prescription fills and dosage units.
  - **Outlier Flags**: Identifies drugs with spending deviations.
  - **Changes in Average Spending**: Annual spending trends per dosage unit.

## Analysis Steps

### 1. Data Cleaning:
- Handled missing values for spending and dosage units.
- Filtered out drugs with fewer than 11 claims or flagged as outliers.

### 2. Exploratory Data Analysis (EDA):
- Visualized spending trends for drugs over the 5-year period.
- Analyzed spending changes per dosage unit for different manufacturers.

### 3. Predictive Modeling:
- Split the dataset into training (80%) and testing (20%) sets.
- Trained a Random Forest model to predict 2022 drug spending using previous years’ data.
- Evaluated model performance using RMSE and R-squared.

### 4. Visualization:
- Generated plots comparing predicted vs actual spending for 2022.

## Technologies Used
- **Programming Languages**: R
- **Libraries**: `randomForest`, `ggplot2`, `dplyr`, `caret`
- **Tools**: RStudio

## Key Results
- Identified drugs with substantial increases or decreases in spending.
- Successfully predicted drug spending for 2022 with reasonable accuracy.
- Provided insights into spending patterns that can aid policymakers in cost control.

## Conclusion
This project provides insights into Medicaid drug spending trends and uses machine learning to predict future spending. The analysis can help policymakers and healthcare providers understand how drug costs evolve over time and identify potential cost-saving opportunities.
