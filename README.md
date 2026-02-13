# From Scalar Summaries to Functional Comparisons: A Framework for Plant Disease Progress Curves

## Overview

This repository provides the data and computational workflow for the paper:  
**"From Scalar Summaries to Functional Comparisons: A Framework for Plant Disease Progress Curves"** by Emerson M. Del Ponte.

### Summary
Traditional analysis of plant disease progress curves (DPCs) often relies on scalar measures like the Area Under the Disease Progress Curve (AUDPC), which can obscure important differences in epidemic timing and shape. This study introduces a curve-based framework that treats DPCs as **epidemic phenotypes**. 

Using **Hierarchical Generalized Additive Models (HGAM)**, the framework estimates environment-adjusted mean epidemic curves while accounting for environmental heterogeneity. By quantifying similarity through functional distances and hierarchical clustering, the approach identifies distinct epidemic phenotypes that traditional scalar comparisons might miss. The framework was validated using a multi-environment dataset of southern corn leaf blight in maize hybrids, demonstrating its utility for host resistance phenotyping and comparative epidemiology.

## Repository Structure

- **`index.qmd`**: The primary Quarto source file containing the complete analysis, mathematical equations, and code.
- **`index.html`**: The interactive, rendered version of the analysis (recommended for reading).
- **`maize_bipolaris.csv`**: The raw dataset used in the study, including trial locations, hybrids, and disease severity assessments.
- **`LICENSE`**: MIT License.

## How to Reproduce the Analysis

Reproducibility is a core goal of this research. Follow these steps to replicate the results:

### 1. Prerequisites
- **R** (version 4.0 or higher)
- **Quarto** (available at [quarto.org](https://quarto.org/))

### 2. Setup the Environment
Clone the repository and install the required R packages:

```r
pkgs <- c(
  "readr", "dplyr", "tidyr", "tibble", "purrr", 
  "ggplot2", "mgcv", "pracma", "pheatmap", 
  "lme4", "lmerTest", "emmeans", "multcompView", 
  "cowplot", "ggrepel", "ggdendro", "patchwork", 
  "DHARMa", "stringr", "glue"
)
install.packages(pkgs)
```

### 3. Render the Project
Generate the final report by rendering the Quarto document:

```bash
git clone https://github.com/emdelponte/paper-hgam-curves.git
cd paper-hgam-curves
quarto render index.qmd
```

## Computational Environment Details

A full specification of the computational environment (R version and package versions) is available in the **Session Info** section at the end of the `index.html` file.

## Citation

**Del Ponte, E. M. (2025).** From Scalar Summaries to Functional Comparisons: A Framework for Plant Disease Progress Curves.  
*For questions or issues, please open an issue in this repository.*
