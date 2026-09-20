# High-Dimensional Hepatotoxicity Analysis

Analysis of the effect of paracetamol exposure on liver toxicity using high-dimensional gene expression data from rats.

## Overview

This project investigates how different doses of paracetamol influence liver gene expression. The analysis uses transcriptomic data collected from 64 rats, with measurements for 3,116 genes and several clinical variables.

The main objective is to identify genes whose expression is affected by the administered dose of paracetamol.

The project applies:

- Exploratory data analysis
- Principal Component Analysis (PCA)
- Dimension reduction
- Multivariate linear modeling
- Residual dependence analysis
- Residual whitening
- Stability selection
- Gene-level visualization and clustering

## Repository Contents

- [`drug_toxicity.pdf`](drug_toxicity.pdf): PDF report containing the complete analysis and results.
- [`drug_toxicity.rmd`](drug_toxicity.rmd): R Markdown file containing the analysis code.  
  **The code used to generate the report is available in this file.**
- [`liver_data.Rdata`](liver_data.Rdata): Gene expression, treatment, clinical, and gene annotation data.
- [`Freqs_liver_toxicity_TOEPLITZ_nbreplis_2.Rdata`](Freqs_liver_toxicity_TOEPLITZ_nbreplis_2.Rdata): Stability-selection results.
- [`LICENSE`](LICENSE): Project license.

## Dataset

The main object loaded from `liver_data.Rdata` is called `liver.toxicity`. It contains four data frames:

- `gene`: gene expression measurements for 64 samples and 3,116 genes.
- `clinic`: clinical measurements for the 64 animals.
- `treatment`: treatment information, including animal identifiers, dose groups, and exposure time.
- `gene.ID`: gene identifiers and annotations.

The treatment groups are balanced across four paracetamol dose levels:

- 50
- 150
- 1,500
- 2,000

Each dose group contains 16 samples.

The analysis found no missing values in the principal data tables.

## Analysis Workflow

### 1. Exploratory Analysis

The structure and distribution of the data were examined before modeling. Principal Component Analysis was then used to visualize global variation in gene expression.

The first two principal components explained approximately:

- PC1: 28.1% of the total variance
- PC2: 14.9% of the total variance

Together, they explained approximately 43% of the total variance. The PCA visualization showed partial separation of samples according to paracetamol dose, suggesting that dose has an effect on gene expression.

### 2. Dimension Reduction

Because the dataset is high-dimensional, the most variable genes were selected to reduce computational cost.

The analysis retained the 500 genes with the greatest importance across the first ten principal components. This reduction was necessary because the subsequent stability-selection procedure involves computationally expensive matrix operations.

### 3. Multivariate Linear Modeling

A multivariate linear model was used to study the effect of dose on gene expression.

The model can be written as:

$$\
Y = X\beta + \varepsilon
\$$

where:

- \(Y\) is the matrix of gene expression values.
- \(X\) is the design matrix encoding the dose groups.
- ($\beta\$) contains the estimated regression coefficients.
- ($\varepsilon\$) is the residual matrix.

The dose variable was treated as a categorical factor with four levels. The design matrix therefore contains an intercept and dose-related contrasts.

### 4. Residual Dependence and Whitening

The residuals were examined to assess whether they were independent. The whitening test indicated strong correlation between residuals, meaning that the independence assumption was not appropriate.

Several covariance structures were compared:

- AR(1)
- ARMA
- Non-parametric Toeplitz covariance

The non-parametric Toeplitz model provided the most suitable representation of the residual dependence. It was therefore used to whiten the residuals before variable selection.

### 5. Stability Selection

Stability selection was used to identify genes consistently associated with the dose factor.

This method repeatedly performs variable selection on subsamples of the data and calculates the selection frequency of each gene–model-effect pair.

A selection-frequency threshold of 0.93 was used. With this threshold, the analysis identified:

- 193 selected genes
- The genes most strongly associated with dose were ranked according to their re-estimated coefficients.

## Results Visualization

The report includes several visualizations:

- PCA projection of the samples
- Selection-frequency plots
- Heatmaps of the most strongly affected genes
- Boxplots showing gene expression across dose groups
- A dendrogram showing clusters of genes with similar expression profiles

The selected genes displayed changes in expression across paracetamol dose groups. The dendrogram also revealed groups of genes with similar residual-expression patterns, which may indicate shared regulatory mechanisms or biological pathways.

## Requirements

The analysis was developed in R and uses the following packages:

- `MultiVarSel`
- `ggplot2`
- `dplyr`
- `reshape2`
- `knitr`

You may install the CRAN packages with:

```r
install.packages(c("ggplot2", "dplyr", "reshape2", "knitr"))
```

The `MultiVarSel` package must also be installed and available in the R environment.

## Reproducing the Analysis

1. Clone or download this repository.
2. Open R or RStudio.
3. Set the working directory to the repository directory.
4. Make sure the `.Rdata` files are available.
5. Open [`drug_toxicity.rmd`](drug_toxicity.rmd).
6. Install the required R packages.
7. Knit the R Markdown file to generate the PDF report.

The report can also be generated from the command line with:

```r
rmarkdown::render("drug_toxicity.rmd")
```

The analysis code is contained in `drug_toxicity.rmd`, while `drug_toxicity.pdf` contains the corresponding rendered report.

## Computational Considerations

The stability-selection step is computationally demanding because it involves repeated model fitting and large matrix operations, including Kronecker products.

To make the analysis feasible with limited computational resources:

- The gene expression data were reduced to the 500 most variable genes.
- Stability-selection results were saved in an `.Rdata` file.
- Parallel computation can be enabled using the `doMC` package on compatible systems.

The original analysis used repeated stability selection and saved the resulting selection frequencies for later visualization and interpretation.

## Limitations

- The current analysis focuses primarily on the main effect of paracetamol dose.
- Exposure time was not included in the final multivariate model.
- The dimension-reduction step limits the analysis to the most variable genes.
- The selected-gene threshold of 0.93 is a methodological choice and may affect the number of detected genes.
- The results identify statistical associations and require further biological validation.

## Conclusion

This project demonstrates the use of multivariate statistical methods for analyzing high-dimensional toxicogenomic data.

The results suggest that paracetamol dose influences liver gene expression and that a subset of genes can be identified using residual whitening and stability selection. The analysis also highlights the importance of modeling residual dependence and managing computational complexity when working with high-dimensional biological datasets.

## Authors

- Rabah Achour
- Mohand Arezki Braneci

## License

See [`LICENSE`](LICENSE) for licensing information.
