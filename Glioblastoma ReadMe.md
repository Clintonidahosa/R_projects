# Exploratory Data Analysis and Clustering of Glioblastoma Gene Expression Data

## Introduction
Glioblastoma is an aggressive form of brain cancer that poses significant challenges in diagnosis and treatment. This project aims to explore gene expression patterns in glioblastoma, identify significantly up- and down-regulated genes, and perform clustering to uncover potential biological insights.

## Dataset Description
- **Source:** [Glioblastoma Gene Expression Dataset](https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/Cancer2024/glioblastoma.csv)
- **Structure:** The dataset consists of gene expression values with genes as rows and samples as columns.
- **Size:** The dataset contains over 500 genes and 10 samples.

## Analysis Overview
This analysis includes:
- Heatmap generation to visualize gene expression.
- Identification of significantly up- and down-regulated genes.
- Volcano plot creation to show the relationship between fold change and statistical significance.
- K-means clustering of gene expression data.

## Methodology
1. **Libraries and Packages:**
   - `gplots`: For heatmap generation.
   - `RColorBrewer`: For color palettes.
   - `ggplot2`: For creating volcano plots.
   - `factoextra`: For clustering visualization.

2. **Data Preprocessing:**
   - The first column of the dataset is set as row names to ensure the dataframe is purely numeric.
   - Log2 fold change and p-values are calculated to identify significantly regulated genes.

3. **Visualization Techniques:**
   - Heatmaps to visualize gene expression across samples.
   - Volcano plots to represent the relationship between fold change and statistical significance.
   - K-means clustering to identify clusters of gene expression profiles.

## Results
- **Up-regulated Genes:** List of genes that show significant increases in expression.
- **Down-regulated Genes:** List of genes that show significant decreases in expression.
- Insights from heatmaps and volcano plots indicate distinct expression patterns associated with glioblastoma.
- Clustering results provide potential insights into the biological variability among samples.

## Visualizations
*Heatmap of gene expression.*
![Image Description](https://drive.google.com/uc?id=1LnzCDJirnBI6ZbslLBJydGNVfp8WZl_f)  ![Image Description](https://drive.google.com/uc?id=1LnzCDJirnBI6ZbslLBJydGNVfp8WZl_f) 


![Volcano Plot Example](path/to/volcano_plot.png)
*Volcano plot showing fold change vs. p-value.*

![K-means Clustering Example](path/to/clustering.png)
*K-means clustering results.*


## Applications

**Comments:**
- The analysis can aid in identifying potential biomarkers for glioblastoma.
- Insights from this analysis can inform targeted therapies and improve patient outcomes.

## Conclusion

**Summary of Findings:**
- Highlight significant genes, clustering patterns, and potential implications for glioblastoma research.
