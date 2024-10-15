# Project Title: Data Analysis of TCGA Glioblastoma Dataset

## 1. Project Overview

**Objective:** Analyze gene expression data from glioblastoma samples to identify significant up- and down-regulated genes and explore clustering patterns.

**Data Source:** TCGA Glioblastoma dataset.


# Installing Necessary Packages

To ensure the necessary libraries are loaded, install the following packages:

```
install.packages("gplots")       # for heatmap generation
install.packages("RColorBrewer") # for color palettes
install.packages("ggplot2")      # for volcano plots
install.packages("factoextra")   # for clustering visualization
```

# Loading Necessary Libraries
Load the required libraries to use their functions:
```
library(gplots)       # for heatmap generation
library(RColorBrewer) # for color palettes
library(ggplot2)      # for volcano plots
library(factoextra)   # for clustering visualization 
```
# Load the Glioblastoma Dataset
Load the glioblastoma dataset (rows as genes, columns as samples) and set the first column to be row names so that the rest of the dataframe can be purely numeric:
```
GBLM <- read.csv("https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/Cancer2024/glioblastoma.csv", row.names = 1) 
View(GBLM) # View dataset to see the structure
```
# Color palettes for the heatmaps
Sequential color palette
Create a sequential color palette using the maximum number of shades of green:
```
seq <- brewer.pal(9, "Greens") # Green gradient where light green represents low counts
display.brewer.pal(n=9, name = "Greens") 
```
Diverging Color Palette
Create a diverging color palette (5 red, 5 blue, 1 neutral) to represent expression figures across two colors:
```
div <- rev(brewer.pal(11, "RdBu")) # Red for up-regulation and blue for down-regulation
display.brewer.pal(n=11, name = "RdBu")
```
# Heatmaps of Glioblastoma Dataset
Heatmap with Sequential Palette Coloring of Green
```
heatmap.2(as.matrix(GBLM),
          col = seq,
          Rowv = F, Colv = F, dendrogram = "none",
          trace = "none",
          key = T, scale = "row",
          key.title = "Gene expression",
          density.info = "none",
          main = "Heatmap of 500+ genes expression \nin glioblastoma dataset",
          cexRow = 0.9, cexCol = 0.9, margins = c(12,8))
```
Heatmap with Diverging Palette Coloring of RdBu
```
heatmap.2(as.matrix(GBLM),
          col = div,
          Rowv = F, Colv = F, dendrogram = "none",
          trace = "none",
          key = T, scale = "row",
          key.title = "Gene expression",
          density.info = "none",
          main = "Heatmap of 500+ genes expression \nin glioblastoma dataset",
          cexRow = 0.9, cexCol = 0.9, margins = c(11,10))
```
Heatmap Clustering by Genes (Rows)
```
heatmap.2(as.matrix(GBLM),
          col = div,
          Rowv = T, Colv = F, dendrogram = "row",
          trace = "none",
          key = T, scale = "row",
          key.title = "Gene expression",
          density.info = "none",
          main = "Heatmap of 500+ genes expression \nin glioblastoma dataset \nclustering by genes ",
          cexRow = 0.9, cexCol = 0.7, margins = c(11,10))
```
Heatmap Clustering by Samples (Columns)
```
heatmap.2(as.matrix(GBLM),
          col = div,
          Rowv = F, Colv = T, dendrogram = "column",
          trace = "none",
          key = T, scale = "row",
          key.title = "Gene expression",
          density.info = "none",
          main = "Heatmap of 500+ genes expression \nin Glioblastoma dataset\n clustering by samples",
          cexRow = 0.9, cexCol = 0.7, margins = c(11,10))
```
Heatmap Clustering by Genes and Samples
```
heatmap.2(as.matrix(GBLM),
          col = div,
          Rowv = T, Colv = T, dendrogram = "both",
          trace = "none",
          key = T, scale = "row",
          key.title = "Gene expression",
          density.info = "none",
          main = "Heatmap of 500+ genes expression \nin Glioblastoma dataset\n clustering by genes and samples",
          cexRow = 0.9, cexCol = 0.7, margins = c(11,10))
```
# Subset Genes that are Significantly Up- and Down-Regulated
Get the column names and divide the dataframe into two groups using index positions of columns:
```
colnames(GBLM) 
group1 <- c(1, 2, 3, 4, 5)
group2 <- c(6, 7, 8, 9, 10)

# Creating new dataframes for each group
group1_GBLM <- GBLM[, group1]
group2_GBLM <- GBLM[, group2]

# Mean of each row (genes) for both groups
group1_mean <- rowMeans(group1_GBLM)  
group2_mean <- rowMeans(group2_GBLM) 

# Create a new column for log2 fold change
GBLM$log2fold_change <- log2(group2_mean + 1) - log2(group1_mean + 1) # Adding 1 to prevent log of 0

# Two-sample t-test for each row and assign p-values
GBLM$pvalues <- apply(GBLM, 1, function(row) {
  t.test(row[1:5], row[6:10])$p.value
})
```
# Visualize the Fold Change and -Log10 P-value
```
plot(GBLM$log2fold_change, GBLM$pvalues)
abline(h = 0.25, col = "red", lty = 2)
abline(v = 1.5, col = "blue", lty = 2)
abline(v = -1.5, col = "blue", lty = 2)
```

# Displaying up- and down-regulated genes 
```
UpRegGenes <- GBLM[GBLM$log2fold_change > 1.5 & GBLM$pvalues < 0.25, ]
rownames(UpRegGenes) # List of up-regulated genes
DownRegGenes <- GBLM[GBLM$log2fold_change < -1.5 & GBLM$pvalues < 0.25, ]
rownames(DownRegGenes) # List of down-regulated genes 
```
# Creating Volcano Plots
Visualize the relationship between fold change and statistical significance using a volcano plot:
```
ggplot(GBLM, aes(log2fold_change, -log10(pvalues))) +
  geom_point(aes(color = ifelse(log2fold_change > 1.5 & pvalues < 0.25, "Upregulated", 
                                 ifelse(log2fold_change < -1.5 & pvalues < 0.25, "Downregulated", "Not Significant"))),
                 alpha = 0.7) +
  scale_color_manual(values = c("red", "blue", "grey")) +
  theme_minimal() +
  labs(title = "Volcano Plot of Gene Expression",
       x = "Log2 Fold Change",
       y = "-Log10 P-value") +
  theme(legend.title = element_blank())
```
# K-means Clustering on the Expression Data
Normalize the data and perform K-means clustering:
```
# Normalize the data (optional)
norm_data <- scale(as.matrix(GBLM))

# Perform K-means clustering
set.seed(123)  # For reproducibility
kmeans_res <- kmeans(norm_data, centers = 3)  # Change centers based on your data

# Add cluster results to the original dataset
GBLM$cluster <- as.factor(kmeans_res$cluster)

# Visualize clusters
fviz_cluster(kmeans_res, data = norm_data, geom = "point",
             main = "K-means Clustering of Gene Expression Data",
             ellipse.type = "convex")
```

## 9. Real-World Applications

**Comments:**
- The analysis can aid in identifying potential biomarkers for glioblastoma.
- Insights from this analysis can inform targeted therapies and improve patient outcomes.

## 10. Conclusion

**Summary of Findings:**
- Highlight significant genes, clustering patterns, and potential implications for glioblastoma research.

