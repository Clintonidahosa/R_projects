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
   - HEATMAPS - I created heatmaps to visualize gene expression across samples using the heatmap.2() function from the gplots package in R. I applied sequential and 
                diverging color palettes: sequential for gradient changes and diverging to highlight deviations from a central value.
   - SCATTER PLOT - This type of plot was used to visualize the results of differential gene expression analysis, where the log2fold_change represents the magnitude of 
                    change 
                   and the pvalues represent statistical significance.  
   - VOLCANO PLOTS - This is to represent the relationship between fold change and statistical significance. They were created using ggplot2 package also.
   - CLUSTERING - K-means clustering to identify clusters of gene expression profiles.

## Results
- **Up-regulated Genes:** List of genes that show significant increases in expression.
- **Down-regulated Genes:** List of genes that show significant decreases in expression.
- Insights from heatmaps and volcano plots indicate distinct expression patterns associated with glioblastoma.
- Clustering results provide potential insights into the biological variability among samples.

## Visualizations
*Heatmaps of gene expression.*
<p align="center">
  <img src="https://drive.google.com/uc?export=view&id=1LnzCDJirnBI6ZbslLBJydGNVfp8WZl_f" alt="Image 1" width="400"/>
  <img src="https://drive.google.com/uc?export=view&id=1UwM-dpZ2psPjjvBUSd4BxA9qa84HIKkc" alt="Image 2" width="400"/>
  <img src="https://drive.google.com/uc?export=view&id=1_coAIaQ90HEyalEc-TrmJAWs7SVWf15l" alt="Image 3" width="400"/>
  <img src="https://drive.google.com/uc?export=view&id=1J7pe0IClJ_poTTRHmfZjtLhw_KGMksuJ" alt="Image 4" width="400"/>
  <img src="https://drive.google.com/uc?export=view&id=1v4bssRQBHb54vkr5VKlXeVQskH1wW06z" alt="Image 5" width="400"/>
</p>

*Scatter plot showing magnitute of change(log2fold_change) and statistical significance (p-value)
<img src="https://drive.google.com/uc?export=view&id=1hocMAjF47BMIFRNguGRCBXoxTCpw_Saw" alt="Image Description" width="400"/>

*Volcano plot showing fold change vs. p-value.*
<img src="https://drive.google.com/uc?export=view&id=1MYB4cq4ZPvNwac6imjhfxN2iNhteUar9" alt="Image Description" width="400"/>

*K-means clustering results.*
<img src="https://drive.google.com/uc?export=view&id=1-NHaElqQnUXahcz9IGc9d-y42UuIvHHo" alt="Image Description" width="400"/>



### Application of Results

#### 1. Identification of Up- and Down-Regulated Genes

**Interpretation:**  
The analysis identified significant up-regulated and down-regulated genes in glioblastoma. Up-regulated genes may promote tumor growth, while down-regulated genes may inhibit it.

**Real-Life Implications:**
- **Biomarker Discovery:** These genes can serve as potential biomarkers for glioblastoma diagnosis and prognosis.
- **Targeted Therapies:** Targeting up-regulated genes could lead to the development of new therapeutic agents, enhancing patient survival.
- **Personalized Medicine:** Understanding gene expression profiles can help tailor treatment plans for patients.

#### 2. Clustering Analysis

**Interpretation:**  
K-means clustering revealed distinct groups of gene expression profiles, suggesting different molecular subtypes of glioblastoma.

**Real-Life Implications:**
- **Patient Stratification:** Clusters can stratify patients based on tumor profiles for more effective treatments.
- **Guiding Research:** Clustering results may guide future research into the biological mechanisms underlying these subtypes.

#### 3. Volcano Plot Insights

**Interpretation:**  
The volcano plot highlights genes with significant expression changes, providing a visual summary of findings.

**Real-Life Implications:**
- **Focus for Further Research:** Significant genes can be prioritized for investigation in preclinical or clinical studies.
- **Biomarker Validation:** Validating these genes in larger cohorts could confirm their utility as biomarkers.

### Conclusion

The analysis of the TCGA glioblastoma dataset yields valuable insights into the molecular basis of glioblastoma, contributing to improved treatment strategies and patient outcomes.


