# From Scalar Summaries to Functional Comparisons: A Framework for Analyzing Plant Disease Progress Curves

> **r4pde API update:** `compare_curves()` is no longer available in the current package. Its workflow has been replaced by `functional_curves()` for fitting epidemic trajectories and `functional_distances()` for distances, clustering, permutation tests, and bootstrap summaries. The optional r4pde section at the end of `index.qmd` now uses these two functions. See the [curve-fitting documentation](https://emdelponte.github.io/r4pde/reference/functional_curves.html) and [distance-analysis documentation](https://emdelponte.github.io/r4pde/reference/functional_distances.html).

## Overview

This repository contains the data and analysis workflow accompanying the published article **[“From Scalar Summaries to Functional Comparisons: A Framework for Analyzing Plant Disease Progress Curves”](https://apsjournals.apsnet.org/doi/full/10.1094/PHYTO-01-26-0009-LE)** by Emerson M. Del Ponte, published in *Phytopathology* (2026).

Disease progress curves can have similar area under the disease progress curve (AUDPC) or final severity while differing in epidemic timing and shape. Using Bipolaris leaf spot assessments in maize hybrids across environments, this workflow compares scalar summaries with smooth trajectories and functional distances.

## Analysis workflow

1. Prepare severity proportions and identify environment × hybrid trajectories.
2. Fit hierarchical beta generalized additive models (HGAMs), examine diagnostics, and estimate environment-adjusted hybrid mean curves.
3. Calculate functional L2 distances and use Ward hierarchical clustering to describe epidemic phenotypes.
4. Select and plot four illustrative curve pairs contrasting AUDPC, final severity, and trajectory differences.
5. Analyze AUDPC with an environment random-intercept mixed model, obtain Tukey letter groups, and compare them with functional clusters.
6. Optionally fit curves and calculate distances using the updated r4pde API.

The manual HGAM and the package workflow use different model specifications and beta boundary adjustments; their fitted curves and clusters need not match exactly.

## Repository files

| File or directory | Purpose |
| --- | --- |
| [index.qmd](index.qmd) | Quarto source with methods, equations, R code, diagnostics, and figures. |
| [index.html](index.html) | Rendered report for viewing in a browser. |
| [index_files/](index_files/) | Supporting figures, styles, and scripts for the HTML report; keep this directory beside `index.html`. |
| [maize_bipolaris.csv](maize_bipolaris.csv) | Disease severity observations by environment, hybrid, and assessment time. |
| [Fig1.png](Fig1.png) | Four-panel illustration of scalar summaries and trajectory differences; regenerated during rendering. |
| [LICENSE](LICENSE) | MIT license. |

## Data and interpretation

The CSV contains `Ambiente` (environment), `Hibrido` (hybrid), `DAE` (days after emergence), `Fenologia` (phenology), and `Bipolaris` (severity in percent). It has no plot or block identifier. The current analysis therefore treats each environment × hybrid combination as one observed trajectory, rather than making plot-level inference.

By default, the script retains observations with **20 < DAE < 116** and trajectories with at least **five distinct assessment times**. Original severity proportions are used for scalar summaries and raw-curve plots; boundary-adjusted values are used for the manual beta-GAM.

Assessment windows differ among environments. Observed AUDPC integrates over each trajectory's own window, while the illustrative functional distances use a common grid with constant endpoint extension. These comparisons can reflect observation-window differences as well as epidemic shape. The selected pairs illustrate scalar similarity; they do not establish statistical equivalence.

## Reproduce the analysis

### 1. Install prerequisites and obtain the repository

Install a current version of [R](https://cran.r-project.org/) and [Quarto](https://quarto.org/). The R chunks have been checked with R 4.5.2; package versions are recorded in the report's **Session info** section.

```bash
git clone https://github.com/emdelponte/paper-hgam-curves.git
cd paper-hgam-curves
```

### 2. Install R dependencies

Run the following in R:

```r
pkgs <- c(
  "readr", "dplyr", "tidyr", "tibble", "purrr",
  "ggplot2", "mgcv", "pracma", "pheatmap",
  "lme4", "lmerTest", "emmeans", "multcomp", "multcompView",
  "cowplot", "ggrepel", "ggdendro", "patchwork",
  "DHARMa", "stringr", "glue"
)
missing <- setdiff(pkgs, rownames(installed.packages()))
if (length(missing)) install.packages(missing)
```

The setup chunk also installs missing dependencies automatically. Internet access is needed for package installation. The optional package analysis additionally requires `r4pde`:

```r
install.packages("r4pde")
```

### 3. Render the report

From the repository directory, run:

```bash
quarto render index.qmd --to html
```

Open `index.html` in a current browser and keep `index_files/` alongside it when sharing or moving the report. Equations use native MathML, avoiding an external MathJax script dependency.

The render runs the manual analysis and writes `Fig1.png`. The `Figure2_functional_phenotypes.png` export is commented out; uncomment its `ggsave()` call to save that figure separately. Edit the **User settings** chunk to change the data window, minimum assessment count, cluster count, or grid size.

### 4. Enable the optional r4pde workflow

The final `r4pde-functional-analysis` chunk has `#| eval: false`. Change it to `#| eval: true` to include the package analysis in the render, or run the chunk interactively after preparing `dat` and the user settings.

The replacement API separates fitting from subsequent analysis:

```r
fc <- r4pde::functional_curves(
  data = dat,
  time = "DAE",
  response = "bipolaris",
  treatment = "Hibrido",
  environment = "Ambiente",
  unit = "curve_id",
  response_scale = "proportion",
  min_points = min_assess,
  grid_n = grid_n
)

fd <- r4pde::functional_distances(
  object = fc,
  cluster_k = k_clusters,
  hc_method = "ward.D2",
  test_factor = "resistance",
  perm_unit = "Hibrido",
  perm_strata = "Ambiente",
  test_mode = "global",
  n_perm = 999,
  bootstrap = TRUE,
  boot_B = 399,
  boot_seed = 1
)

plot(fc)
r4pde::plot_curves(fd)
r4pde::plot_dendrogram(fd)
fd$test$global
fd$clusters
```

This example uses objects created earlier in `index.qmd`. Fitting settings belong in `functional_curves()`; clustering, testing, and bootstrap settings belong in `functional_distances()`.

The report documents two limits of the checked package implementation: resistance labels are shuffled among curves within environments without synchronizing hybrid-label permutations across environments, and bootstrap summaries resample fitted trajectories without refitting the GAM. Interpret the test and intervals accordingly.

## Citation and questions

Del Ponte, E. M. (2026). From Scalar Summaries to Functional Comparisons: A Framework for Analyzing Plant Disease Progress Curves. *Phytopathology*, **116**(8), 1188–1193. [https://doi.org/10.1094/PHYTO-01-26-0009-LE](https://doi.org/10.1094/PHYTO-01-26-0009-LE).

For questions or reproducibility issues, [open an issue](https://github.com/emdelponte/paper-hgam-curves/issues) and include your R and Quarto versions, the error message, and relevant session information.
