# EEG Polynomial Regression Analysis

This is an R-based statistical analysis of EEG (electroencephalography) signal data. The project covers exploratory data analysis, polynomial regression modelling, and Approximate Bayesian Computation (ABC).

## Project Structure

```
eeg_polynomial_regression/
├── datasets/
│   ├── time_...csv          # Time column
│   ├── X_...csv             # Input EEG signals (X1–X4)
│   └── y_...csv             # Output EEG signal (y)
├── images/                  # Generated plots (17 PNG files)
├── eeg_analysis.R           # Main analysis script
├── final_df.csv             # Consolidated dataset (exported by script)
└── README.md
```

## Prerequisites

- **R** (tested on base R; no external packages required)
- All plots are generated using base R graphics (`png()`, `plot()`, `hist()`, etc.)

## Data

The dataset contains:

| Column | Description |
|--------|-------------|
| `time` | Time index |
| `X1–X4` | Four input EEG signal channels |
| `y` | Output EEG signal |

No missing values or duplicates are present in the dataset.

## Analysis Overview

### Task 1: Preliminary Data Analysis

- **Time series plots** of all four input signals and the output signal over time
- **Distribution analysis** via histograms, density curves, and rug plots for each signal
- **Combined density plot** overlaying all input signal distributions
- **Correlation analysis** including a heatmap of pairwise correlations and scatter plots with regression lines

### Task 2: Polynomial Regression Modelling

Five candidate polynomial regression models are defined, each using different combinations of polynomial features (e.g., `X1²`, `X1³`, `X1⁴`, `X2⁴`, `X3³`, `X3⁴`):

| Model | Features |
|-------|----------|
| Model 1 | X4, X1², X1³, X2⁴, X1⁴ |
| Model 2 | X4, X1³, X3⁴ |
| Model 3 | X3³, X3⁴ |
| Model 4 | X2, X1³, X3⁴ |
| Model 5 | X4, X1², X1³, X3⁴ |

All models include an intercept term.

**Evaluation metrics:**

- **Residual Sum of Squares (RSS)**
- **Log-likelihood**
- **AIC** (Akaike Information Criterion)
- **BIC** (Bayesian Information Criterion)
- **Residual analysis** — histograms with KDE and Q-Q plots for each model

**Model Selection:** Model 2 is identified as the preferred model based on the lowest AIC and BIC values, with residuals closest to a Gaussian distribution. This conclusion is dataset-specific (No Free Lunch theorem).

**Train/Test Evaluation (Model 2):**

- 70/30 train/test split (seeded at 42)
- Parameters re-estimated on training data
- **95% confidence intervals** computed for test predictions
- Predictions with error bars and shaded CI bands plotted against actual test data (full and zoomed views)

### Task 3: Approximate Bayesian Computation (ABC)

- The two parameters with the largest absolute least-squares estimates are selected for ABC estimation; the remaining two are held fixed.
- **Uniform priors** are centred around the LS estimates, with a range of ±15× the standard error.
- **Rejection ABC** is performed with 50,000 iterations and an RSS tolerance of 1.1× the Model 2 RSS.
- Results include a **joint posterior scatter plot**, **marginal posterior histograms**, and a summary table with posterior means, standard deviations, and 95% credible intervals.

## Generated Plots

All plots are saved to the `images/` directory:

| File | Description |
|------|-------------|
| `overall_eeg_signals_vs_time.png` | All input signals overlaid on one time axis |
| `individual_eeg_signals_vs_time.png` | Individual 4-panel time series |
| `output_eeg_signal_vs_time.png` | Output signal y over time |
| `distribution_input_eeg_signals.png` | Histograms + density for X1–X4 |
| `unique_eeg_distribution.png` | Combined density overlay of X1–X4 |
| `distribution_output_eeg_signal.png` | Histogram + density of y |
| `correlation_heatmap.png` | Pairwise correlation heatmap |
| `scatter_plot_correlation_x_vs_y.png` | All X signals vs y on one plot |
| `Scatterplot_correlation_X_and_y.png` | Individual scatter plots with regression lines |
| `model_*_residual_analysis.png` | Histogram + Q-Q plot for each model (×5) |
| `model2_predictions_ci_errorbars.png` | Test predictions with 95% CI |
| `model2_predictions_ci_errorbars_zoomed.png` | Zoomed view of CI plot |
| `abc_joint_marginal_posterior.png` | Joint + marginal posterior distributions |

## Running the Script

```bash
Rscript eeg_analysis.R
```

Or run interactively in R/RStudio:

```r
source("eeg_analysis.R")
```

The script will read the CSVs from `datasets/`, generate all plots into `images/`, and export the consolidated dataframe to `final_df.csv`.

## References

- Model selection follows the No Free Lunch theorem — the chosen model is optimal for this specific dataset only.
