# Reproduction Guide

This guide provides step-by-step instructions to reproduce all analyses from "Global Market Integration Undermines Agricultural Insurance Under Climate Change."

## Prerequisites

### Software Requirements

1. **R version 4.0 or higher**
   - Download: https://cran.r-project.org/
   - Recommended: Use RStudio (https://posit.co/download/rstudio-desktop/)

2. **Git** (for cloning repository)
   - Download: https://git-scm.com/downloads

### Time Requirements

- **Quick verification** (using provided processed data): 30 minutes
- **Full reproduction** (from raw data): 4-6 hours
- **Complete replication** (including data collection): 1-2 weeks

## Quick Start (30 minutes)

### Step 1: Clone Repository

```bash
git clone https://github.com/ahdelvalle/agricultural-insurance-climate-markets.git
cd agricultural-insurance-climate-markets
```

### Step 2: Install R Packages

Open R or RStudio and run:

```r
# Install required packages
install.packages(c(
  "MASS",           # Distribution fitting
  "fitdistrplus",   # Additional distribution functions  
  "lmtest",         # Granger causality
  "tseries",        # Time series tests
  "keras",          # Neural networks
  "tensorflow",     # Keras backend
  "ggplot2",        # Visualization
  "dplyr",          # Data manipulation
  "tidyr",          # Data reshaping
  "readr",          # Reading CSV
  "lubridate",      # Date handling
  "readxl"          # Reading Excel files
))

# Install keras/tensorflow (requires additional setup)
library(keras)
install_keras()  # This may take 10-15 minutes
```

### Step 3: Verify Data Files

```r
# Check that all data files are present
setwd("data/processed/")
files <- list.files()
print(files)

# Should see:
# [1] "ontario_corn_prices.csv"
# [2] "ontario_farmers_prices.csv"  
# [3] "mexico_jalisco_data.csv"
# [4] "mexico_sinaloa_data.csv"
# [5] "brazil_goias_data.csv"
# [6] "brazil_matogrosso_data.csv"
```

### Step 4: Run Main Analysis

Open the HTML analysis files in your browser or RStudio:

```bash
# Option A: Open in browser
open code/main_analysis.html

# Option B: In RStudio
# File → Open File → code/main_analysis.html
```

The HTML files are self-contained with embedded code and results. You can:
1. View the complete analysis
2. Extract and run individual code chunks
3. Modify parameters and re-run

### Step 5: Verify Results

Check that your results match the published tables:

```r
# Load result tables
library(readxl)
table1 <- read_excel("results/Table1.xlsx")
table2 <- read_excel("results/Table2.xlsx")

# View tables
View(table1)
View(table2)
```

**Key results to verify**:
- Table 1: 6 of 7 recent-period series (86%) show uniform distributions
- Table 2: 3 of 22 periods (13.6%) show significant Granger causality
- Corn vs. oats: ΔBIC = 138.8

## Full Reproduction (4-6 hours)

This reproduces all analyses from the processed data files.

### Part 1: Distribution Fitting Analysis (2 hours)

```r
# Load libraries
library(MASS)
library(fitdistrplus)
library(dplyr)
library(readr)

# Load Ontario data
ontario <- read_csv("data/processed/ontario_corn_prices.csv")

# Define structural break periods
periods <- list(
  pre_crisis = ontario %>% filter(year >= 2000, year <= 2007),
  crisis = ontario %>% filter(year >= 2008, year <= 2011),
  recovery = ontario %>% filter(year >= 2012, year <= 2019),
  covid = ontario %>% filter(year >= 2020)
)

# Function to fit eight distributions
fit_distributions <- function(prices) {
  distributions <- c("exp", "gamma", "gumbel", "lnorm", 
                     "norm", "t", "unif", "weibull")
  
  results <- data.frame()
  
  for (dist in distributions) {
    tryCatch({
      if (dist == "t") {
        # Student's t requires special handling
        fit <- fitdist(prices, "t", start = list(df = 5))
      } else if (dist == "unif") {
        # Uniform distribution
        fit <- fitdist(prices, "unif")
      } else if (dist == "gumbel") {
        # Gumbel requires specific package
        fit <- fitdist(prices, "gumbel", start = list(location = mean(prices), 
                                                      scale = sd(prices)))
      } else {
        fit <- fitdist(prices, dist)
      }
      
      # Calculate BIC
      bic <- -2 * fit$loglik + length(fit$estimate) * log(length(prices))
      
      # Kolmogorov-Smirnov test
      ks_test <- ks.test(prices, paste0("p", dist), 
                         fit$estimate[1], fit$estimate[2])
      
      results <- rbind(results, data.frame(
        distribution = dist,
        bic = bic,
        ks_pvalue = ks_test$p.value
      ))
    }, error = function(e) {
      message(paste("Error fitting", dist, ":", e$message))
    })
  }
  
  # Sort by BIC (lower is better)
  results <- results %>% arrange(bic)
  
  # Calculate ΔBIC
  results$delta_bic <- results$bic - min(results$bic)
  
  return(results)
}

# Fit distributions for each period
results_list <- lapply(periods, function(df) {
  fit_distributions(df$price_international)
})

# Print results
for (period_name in names(results_list)) {
  cat("\n", toupper(period_name), "PERIOD\n")
  print(results_list[[period_name]])
}
```

**Expected output**:
- Pre-Crisis: Lognormal best fit
- COVID: Uniform best fit (ΔBIC > 10)

### Part 2: Causality Analysis (1.5 hours)

```r
# Load additional libraries
library(lmtest)
library(tseries)

# Function to test Granger causality
test_granger_causality <- function(prices, climate, max_lag = 4) {
  
  # Ensure stationarity
  adf_price <- adf.test(prices)
  adf_climate <- adf.test(climate)
  
  if (adf_price$p.value > 0.05 | adf_climate$p.value > 0.05) {
    warning("Series may be non-stationary")
  }
  
  results <- data.frame()
  
  for (lag in 1:max_lag) {
    # Test if climate Granger-causes prices
    gt <- grangertest(prices ~ climate, order = lag)
    
    results <- rbind(results, data.frame(
      lag = lag,
      f_stat = gt$F[2],
      p_value = gt$`Pr(>F)`[2]
    ))
  }
  
  return(results)
}

# Test causality for each period
causality_results <- lapply(periods, function(df) {
  test_granger_causality(df$price_international, df$spi_24)
})

# Print results
for (period_name in names(causality_results)) {
  cat("\n", toupper(period_name), "PERIOD\n")
  print(causality_results[[period_name]])
  
  # Determine if any lag is significant
  sig <- any(causality_results[[period_name]]$p_value < 0.05)
  cat("Significant causality:", sig, "\n")
}
```

**Expected output**:
- Pre-Crisis: Some significant lags (p < 0.05)
- COVID: No significant lags (p > 0.05)

### Part 3: Neural Network Validation (1.5 hours)

```r
# Load keras
library(keras)
library(tensorflow)

# Function to test neural network improvement
test_neural_network <- function(prices, climate, n_lags = 6) {
  
  # Prepare data
  n <- length(prices)
  
  # Create lagged features
  X_baseline <- matrix(nrow = n - n_lags, ncol = n_lags)
  X_augmented <- matrix(nrow = n - n_lags, ncol = n_lags + 1)
  y <- prices[(n_lags + 1):n]
  
  for (i in 1:(n - n_lags)) {
    X_baseline[i, ] <- prices[i:(i + n_lags - 1)]
    X_augmented[i, ] <- c(prices[i:(i + n_lags - 1)], climate[i + n_lags - 1])
  }
  
  # Split into train/test (80/20)
  train_size <- floor(0.8 * nrow(X_baseline))
  train_idx <- 1:train_size
  test_idx <- (train_size + 1):nrow(X_baseline)
  
  # Normalize
  X_baseline_norm <- scale(X_baseline)
  X_augmented_norm <- scale(X_augmented)
  y_norm <- scale(y)
  
  # Baseline model (prices only)
  model_baseline <- keras_model_sequential() %>%
    layer_simple_rnn(units = 16, activation = "tanh", 
                     input_shape = c(n_lags, 1)) %>%
    layer_dropout(rate = 0.2) %>%
    layer_dense(units = 1)
  
  model_baseline %>% compile(
    optimizer = optimizer_adam(learning_rate = 0.001),
    loss = "mse"
  )
  
  # Reshape for RNN (add time dimension)
  X_baseline_rnn <- array_reshape(X_baseline_norm, 
                                  c(nrow(X_baseline_norm), n_lags, 1))
  
  # Train baseline
  history_baseline <- model_baseline %>% fit(
    X_baseline_rnn[train_idx, , ],
    y_norm[train_idx],
    epochs = 50,
    batch_size = 32,
    validation_split = 0.2,
    verbose = 0,
    callbacks = list(callback_early_stopping(patience = 10))
  )
  
  # Predict and evaluate
  pred_baseline <- model_baseline %>% predict(X_baseline_rnn[test_idx, , ])
  mse_baseline <- mean((pred_baseline - y_norm[test_idx])^2)
  
  # Augmented model (prices + climate)
  model_augmented <- keras_model_sequential() %>%
    layer_simple_rnn(units = 16, activation = "tanh", 
                     input_shape = c(n_lags + 1, 1)) %>%
    layer_dropout(rate = 0.2) %>%
    layer_dense(units = 1)
  
  model_augmented %>% compile(
    optimizer = optimizer_adam(learning_rate = 0.001),
    loss = "mse"
  )
  
  X_augmented_rnn <- array_reshape(X_augmented_norm, 
                                   c(nrow(X_augmented_norm), n_lags + 1, 1))
  
  # Train augmented
  history_augmented <- model_augmented %>% fit(
    X_augmented_rnn[train_idx, , ],
    y_norm[train_idx],
    epochs = 50,
    batch_size = 32,
    validation_split = 0.2,
    verbose = 0,
    callbacks = list(callback_early_stopping(patience = 10))
  )
  
  # Predict and evaluate
  pred_augmented <- model_augmented %>% predict(X_augmented_rnn[test_idx, , ])
  mse_augmented <- mean((pred_augmented - y_norm[test_idx])^2)
  
  # Calculate improvement
  improvement_pct <- 100 * (mse_baseline - mse_augmented) / mse_baseline
  
  # Statistical test (paired t-test on residuals)
  residuals_baseline <- y_norm[test_idx] - pred_baseline
  residuals_augmented <- y_norm[test_idx] - pred_augmented
  test_result <- t.test(residuals_baseline^2, residuals_augmented^2, paired = TRUE)
  
  return(list(
    mse_baseline = mse_baseline,
    mse_augmented = mse_augmented,
    improvement_pct = improvement_pct,
    p_value = test_result$p.value,
    significant = test_result$p.value < 0.10
  ))
}

# Test neural networks for each period
nn_results <- lapply(periods, function(df) {
  test_neural_network(df$price_international, df$spi_24)
})

# Print results
for (period_name in names(nn_results)) {
  cat("\n", toupper(period_name), "PERIOD\n")
  cat("MSE Baseline:", nn_results[[period_name]]$mse_baseline, "\n")
  cat("MSE Augmented:", nn_results[[period_name]]$mse_augmented, "\n")
  cat("Improvement:", nn_results[[period_name]]$improvement_pct, "%\n")
  cat("P-value:", nn_results[[period_name]]$p_value, "\n")
  cat("Significant:", nn_results[[period_name]]$significant, "\n")
}
```

**Expected output**:
- ~14% of periods show significant neural network improvement
- Concordant with Granger causality results (~14%)

### Part 4: Natural Experiment (Corn vs. Oats) (1 hour)

```r
# This requires Ontario oat price data
# For this example, we'll outline the approach

# Load oat data (you would need to obtain this from Statistics Canada)
# oats <- read_csv("data/processed/ontario_oat_prices.csv")

# Compare distributions for COVID period (2020-2025)
# corn_covid <- ontario %>% filter(year >= 2020)
# oats_covid <- oats %>% filter(year >= 2020)

# Fit distributions to both
# corn_fits <- fit_distributions(corn_covid$price_international)
# oats_fits <- fit_distributions(oats_covid$price)

# Compare best fits
# Corn: Expect uniform (lowest BIC)
# Oats: Expect parametric (lognormal, gamma, etc.)

# Calculate ΔBIC between uniform and best parametric
# For corn: ΔBIC = BIC(lognormal) - BIC(uniform) = 138.8
# For oats: ΔBIC = BIC(uniform) - BIC(lognormal) = 40.9
```

## Complete Replication (1-2 weeks)

This reproduces the entire study from raw data collection.

### Phase 1: Data Collection (3-5 days)

**Canadian Data**:
```r
# 1. Download FRED corn prices
library(fredr)
fredr_set_key("YOUR_API_KEY")  # Get free key from FRED

corn_usd <- fredr(
  series_id = "PMAIZMTUSDM",
  observation_start = as.Date("2000-01-01")
)

# 2. Download Bank of Canada exchange rates
library(httr)
url <- "https://www.bankofcanada.ca/valet/observations/FXUSDCAD?start_date=2000-01-01"
exchange <- GET(url)
# Process JSON response...

# 3. Calculate Ontario prices
ontario_prices <- corn_usd %>%
  mutate(
    price_cad = value * 1.15 * exchange_rate,  # 1.15 = regional premium
    date = as.Date(date)
  )
```

**Climate Data**:
```r
# Download from Environment Canada
# This requires web scraping or manual download from:
# https://climate.weather.gc.ca/climate_data/daily_data_e.html?StationID=4333

# Calculate SPI-24
library(SPEI)

monthly_precip <- # ... load monthly precipitation data
spi_24 <- spi(monthly_precip, scale = 24, distribution = "Gamma")
```

**Mexican Data**:
See web scraping scripts (contact author for details)

**Brazilian Data**:
Contact Prof. Herbert Kimura (hkimura@unb.br)

### Phase 2: Data Processing (1-2 days)

1. Clean and validate all price series
2. Calculate SPI-24 for all regions
3. Merge price and climate data by date
4. Identify structural break periods
5. Create final processed datasets

### Phase 3: Analysis (2-3 days)

Run all analyses described in "Full Reproduction" section above.

### Phase 4: Validation (1 day)

1. Compare results to published tables
2. Verify all p-values, BIC values, correlations
3. Check that figures reproduce correctly

## Troubleshooting

### Common Issues

**Issue**: keras/tensorflow installation fails
**Solution**: 
```r
# Try installing specific versions
install.packages("keras")
library(keras)
install_keras(method = "conda", tensorflow = "2.9")
```

**Issue**: Distribution fitting fails
**Solution**: Check for NAs, Inf values, or insufficient sample size
```r
# Clean data
prices <- prices[is.finite(prices) & !is.na(prices)]
# Ensure n > 30 for reliable distribution fitting
```

**Issue**: Granger tests give errors about non-stationarity
**Solution**: Apply differencing or verify ADF test results
```r
# Check stationarity
library(tseries)
adf.test(prices)
# If p > 0.05, series is non-stationary
# Consider first differencing
prices_diff <- diff(prices)
```

**Issue**: Neural networks produce different results each run
**Solution**: Set random seed for reproducibility
```r
library(tensorflow)
set_random_seed(42)
```

## Validation Checklist

After running analyses, verify:

- [ ] Table 1: 86% of recent-period series are uniform
- [ ] Table 1: 0% of pre-2008 series are uniform
- [ ] Table 2: 13.6% of periods show significant Granger causality
- [ ] Table 2: 14.3% of periods show significant neural network improvement
- [ ] Natural experiment: Corn ΔBIC = 138.8 favoring uniform
- [ ] Natural experiment: Oats ΔBIC = 40.9 favoring parametric
- [ ] Brazilian correlation: r > 0.4 contemporaneous, zero Granger causality
- [ ] Power analysis: 45.5% of tests adequately powered

## Getting Help

If you encounter issues:

1. **Check documentation**: Review this guide and data README
2. **Search issues**: https://github.com/ahdelvalle/agricultural-insurance-climate-markets/issues
3. **Open new issue**: Provide details about error, system info, code used
4. **Email author**: ahdelvalle@gmail.com

## Contributing

Found an error or have improvements? 

1. Open an issue describing the problem/suggestion
2. Submit a pull request with fixes/enhancements
3. Email author directly for major contributions

## Citation

If you reproduce or extend this analysis:

```bibtex
@article{hernandez2025agricultural,
  title={Global Market Integration Undermines Agricultural Insurance Under Climate Change},
  author={Hernandez-del-Valle, Adrian},
  journal={Nature Climate Change},
  year={2025},
  note={Manuscript submitted for publication}
}
```

## License

All code and processed data: MIT License

See [LICENSE](../LICENSE) file for details.
