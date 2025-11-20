# Global Market Integration Undermines Agricultural Insurance Under Climate Change

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Status](https://img.shields.io/badge/Status-Submitted-blue)](https://github.com/ahdelvalle/agricultural-insurance-climate-markets)

## Overview

This repository contains the code and processed data for the manuscript:

**"Global Market Integration Undermines Agricultural Insurance Under Climate Change"**  
Adrian Hernandez-del-Valle, PhD  
*Submitted to Nature Climate Change, November 2025*

## Abstract

Agricultural insurance protects millions of farmers globally but depends on predictable price distributions to calculate premiums and estimate risks. We demonstrate that global commodity market integration under climate change eliminates this statistical foundation. Analyzing 25 years of corn prices across five North and South American regions, we find that 86% shifted from traditional parametric distributions to uniform distributions (where all prices are equally probable) by 2020-2025, versus zero before 2008. Three causal identification strategies—including a natural experiment comparing indexed corn versus non-indexed oats under identical climate exposure (ΔBIC=138.8, decisive evidence)—establish financial market integration, not climate change per se, as the driver. Neural network analysis confirms genuine structural breakdown rather than methodological artifacts. Canadian insurance payments surged from $890M (2018) to $4.9B (2022) as traditional actuarial pricing became mathematically intractable. Effective risk management requires hybrid systems combining market-based mechanisms with government stabilization programs.

## Repository Structure

```
.
├── code/
│   ├── main_analysis.html              # Primary statistical analysis
│   ├── comprehensive_analysis.html     # Extended analysis with all regions
│   └── alternative_analysis.html       # Robustness checks
│
├── data/
│   ├── processed/                      # Processed datasets ready for analysis
│   │   ├── ontario_corn_prices.csv
│   │   ├── canada_climate_spi.csv
│   │   ├── mexico_jalisco_data.csv
│   │   ├── mexico_sinaloa_data.csv
│   │   ├── brazil_goias_data.csv
│   │   └── brazil_matogrosso_data.csv
│   │
│   └── metadata/
│       ├── README_data.md              # Detailed data documentation
│       └── data_sources.txt            # List of all data sources with URLs
│
├── results/
│   ├── Table1.xlsx                     # Distributional transformation results
│   └── Table2.xlsx                     # Temporal causality results
│
├── docs/
│   ├── file_naming_guide.md           # Guide to file naming conventions
│   └── reproduction_guide.md          # Step-by-step reproduction guide
│
├── figures/                            # Figures from manuscript (for reference)
│   ├── Figure1.png
│   ├── Figure2.png
│   └── Figure3.png
│
├── LICENSE                             # MIT License
├── README.md                           # This file
└── .gitignore                         # Git ignore file
```

## Key Findings

- **86% of recent-period series (2020-2025)** shifted from parametric to uniform distributions
- **Zero pre-2008 series** exhibited uniform distributions  
- **Natural experiment (corn vs. oats)**: ΔBIC=138.8 provides decisive evidence that market integration, not climate change per se, drives distributional transformation
- **Temporal causality disappeared**: Only 13.6% of periods show significant climate-price Granger causality, with no systematic pattern
- **Canadian insurance crisis**: Payments increased 5.5× from $890M (2018) to $4.9B (2022)
- **Neural network validation**: Concordance with linear methods (14.3% vs 13.6% significance) confirms genuine structural breakdown

## Requirements

### Software
- R version 4.0 or higher
- Python 3.8 or higher
- RStudio (optional but recommended for .html files)

### R Packages
```r
install.packages(c(
  "MASS",           # Distribution fitting (fitdistr function)
  "fitdistrplus",   # Additional distribution functions
  "lmtest",         # Granger causality tests (grangertest function)
  "tseries",        # Time series analysis (adf.test)
  "keras",          # Neural network models
  "tensorflow",     # Backend for keras
  "ggplot2",        # Visualization
  "dplyr",          # Data manipulation
  "tidyr",          # Data reshaping
  "readr",          # Reading CSV files
  "lubridate"       # Date handling
))
```

### Python Packages (if using Python versions of code)
```bash
pip install pandas numpy scipy statsmodels tensorflow keras matplotlib seaborn
```

## Data Sources

All data sources are documented in detail in [`data/metadata/README_data.md`](data/metadata/README_data.md).

### Primary Sources:

1. **Ontario Corn Prices**: 
   - International prices: [FRED Global Price of Corn](https://fred.stlouisfed.org/series/PMAIZMTUSDM)
   - Farm-gate prices: [Grain Farmers of Ontario](https://gfo.ca/marketing/)
   - Exchange rates: [Bank of Canada](https://www.bankofcanada.ca/valet/observations/FXUSDCAD)

2. **Climate Data**:
   - [Environment and Climate Change Canada](https://climate.weather.gc.ca/)
   - Station: Ottawa Macdonald-Cartier Airport (Station ID: 4333)
   - Variable: Monthly precipitation for SPI-24 calculation

3. **Mexican Data**: 
   - [SNIIM](http://www.economia-sniim.gob.mx/) (Sistema Nacional de Información e Integración de Mercados)
   - Regions: Jalisco, Sinaloa
   - Period: 2000-2024

4. **Brazilian Data**: 
   - Available from [LAMFO](http://www.lamfo.unb.br/) (University of Brasilia) 
   - Contact: Prof. Herbert Kimura (hkimura@unb.br)
   - Regions: Goiás, Mato Grosso
   - Period: 2002-2024

5. **Insurance Data**:
   - [Agriculture and Agri-Food Canada](https://agriculture.canada.ca/)
   - AgriInsurance program data (2005-2023)

## Reproducibility

### Quick Start

1. **Clone this repository:**
```bash
git clone https://github.com/ahdelvalle/agricultural-insurance-climate-markets.git
cd agricultural-insurance-climate-markets
```

2. **Install required R packages** (see Requirements section above)

3. **Run the main analysis:**

   **Option A: Using R**
   ```r
   # Open R or RStudio
   # Navigate to the code directory
   setwd("code")
   
   # The HTML files can be opened directly in a browser
   # Or run the underlying R code if you have the .Rmd source
   ```

   **Option B: View HTML files directly**
   ```bash
   # Open in your default browser
   open code/main_analysis.html
   ```

4. **Expected outputs:**
   - Distribution fitting results for all 22 structural break periods
   - Granger causality test results (linear and nonlinear)
   - Neural network validation results
   - Figures and tables matching manuscript

### Detailed Reproduction Guide

See [`docs/reproduction_guide.md`](docs/reproduction_guide.md) for step-by-step instructions to reproduce all analyses from scratch.

## File Naming Conventions

The files in this repository follow specific naming conventions. See [`docs/file_naming_guide.md`](docs/file_naming_guide.md) for details.

**Original files → GitHub names:**
- `BUENO_SPIvsCompPrices_StructuralBreak_6_REGIONES_CDFs_Causality.html` → `code/comprehensive_analysis.html`
- `Nature_submission_CODE.html` → `code/main_analysis.html`
- `Nature_submission_CODE_2.html` → `code/alternative_analysis.html`
- `Dataframe_Canada_CornPrices_SPI_1_and_24_month.csv` → `data/processed/ontario_corn_prices.csv`

## Methodology Overview

### Distributional Analysis
- **Eight candidate distributions tested**: Exponential, Gamma, Gumbel, Log-Normal, Normal, Student's t, Uniform, Weibull
- **Model selection**: Bayesian Information Criterion (BIC)
- **Decision rule**: ΔBIC > 10 indicates decisive evidence
- **Validation**: Kolmogorov-Smirnov goodness-of-fit tests

### Causal Identification Strategies

1. **Index Inclusion Natural Experiment**
   - Comparison: Indexed corn vs. non-indexed oats
   - Control: Identical Ontario climate exposure
   - Result: ΔBIC = 138.8 (factor of 13 beyond "decisive evidence" threshold)

2. **Brazilian Policy Shock**
   - Natural experiment: CONAB intervention period vs. market integration period
   - Result: High contemporaneous correlation (r>0.4) but zero Granger causality
   - Interpretation: Active government stabilization prevents predictable relationships

3. **Neural Network Validation**
   - Architecture: SimpleRNN (16 units) with dropout (0.2)
   - Comparison: Baseline (prices only) vs. augmented (prices + climate)
   - Result: 14.3% significant improvements (concordant with 13.6% linear Granger)
   - Interpretation: Temporal instability reflects genuine structural breakdown

### Structural Break Detection
- **Theory-driven candidates**: 2008 financial crisis, 2012 drought, 2015 El Niño, 2020 COVID-19, 2022 Ukraine war
- **Empirical validation**: Chow tests, CUSUM tests, rolling correlation analysis
- **Result**: 22 distinct structural break periods identified

## Results Summary

### Table 1: Distributional Transformation
- Pre-2008: 100% parametric distributions (0/5 uniform)
- 2020-2025: 86% uniform distributions (6/7 uniform)
- Decisive evidence (ΔBIC > 10) in all recent-period comparisons

### Table 2: Temporal Causality Instability
- Granger causality: 3/22 periods significant (13.6%)
- Neural network causality: 3/21 periods significant (14.3%)
- Pattern: No systematic relationship by region or time
- Power analysis: 45.5% of tests adequately powered (>0.70)

## Citation

If you use this code or data in your research, please cite:

```bibtex
@article{hernandez2025agricultural,
  title={Global Market Integration Undermines Agricultural Insurance Under Climate Change},
  author={Hernandez-del-Valle, Adrian},
  journal={Nature Climate Change},
  year={2025},
  note={Manuscript submitted for publication},
  url={https://github.com/ahdelvalle/agricultural-insurance-climate-markets}
}
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contact

**Adrian Hernandez-del-Valle, PhD**  
Technology Solutions Consultant  
Services Communautaires pour Réfugiés et Immigrants (SCRI)  
Montreal, Quebec, Canada

📧 Email: ahdelvalle@gmail.com  
🔗 GitHub: [@ahdelvalle](https://github.com/ahdelvalle)  
🔗 ORCID: [Get your ORCID](https://orcid.org/)

## Acknowledgments

- **Prof. Herbert Kimura** (LAMFO, University of Brasilia) for facilitating access to Brazilian agricultural data
- **Environment and Climate Change Canada** for climate and meteorological data
- **CONAGUA** (Comisión Nacional del Agua, Mexico) for Mexican climate data
- **INMET** (Instituto Nacional de Meteorologia, Brazil) for Brazilian climate data
- **Grain Farmers of Ontario** for farm-gate price data
- **Agriculture and Agri-Food Canada** for insurance data

## Competing Interests

The author declares no financial or non-financial competing interests. This research received no specific grant funding. The author is employed by Services Communautaires pour Réfugiés et Immigrants (SCRI), a non-profit organization that does not have financial interests in agricultural insurance or commodity markets.

## AI Assistance Disclosure

The author used Claude (Anthropic, Sonnet 4.5) to assist with manuscript preparation, including structuring methodology descriptions, improving organization, and generating this repository documentation. All scientific analyses, data collection, statistical computations, interpretation of results, and final content decisions were performed solely by the author. All AI-generated text was reviewed, edited, and verified for accuracy.

## Version History

- **v1.0** (November 2025): Initial submission to Nature Climate Change
- Updates will be posted here following peer review process

## Issues and Contributions

This repository is maintained for reproducibility of published research. 

### Reporting Issues
If you encounter problems reproducing the analyses:
1. Check the [Issues](https://github.com/ahdelvalle/agricultural-insurance-climate-markets/issues) page to see if it's already reported
2. Open a new issue with:
   - Clear description of the problem
   - Your system information (OS, R version, package versions)
   - Steps to reproduce
   - Expected vs. actual behavior

### Questions
For scientific questions about the methodology or results:
- Open an issue on GitHub, or
- Contact the author directly at ahdelvalle@gmail.com

## Additional Resources

- **Manuscript preprint**: [Link will be added if posted to preprint server]
- **Supplementary Information**: [Will be added upon publication]
- **Related work**: See author's publications on agricultural economics and climate change

---

**Repository Status**: Active maintenance during review process  
**Last Updated**: November 2025  
**DOI**: [Will be added upon Zenodo archival]

---

## Quick Links

- [📊 View Tables](results/)
- [💻 View Code](code/)
- [📁 View Data](data/processed/)
- [📖 Documentation](docs/)
- [📝 License](LICENSE)
- [❓ Report Issue](https://github.com/ahdelvalle/agricultural-insurance-climate-markets/issues)
