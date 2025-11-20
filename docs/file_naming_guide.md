# File Naming Guide

This document explains the mapping between original file names and the standardized names used in this GitHub repository.

## Code Files

### Main Analysis Files

| Original Name | GitHub Name | Description |
|--------------|-------------|-------------|
| `Nature_submission_CODE.html` | `code/main_analysis.html` | Primary statistical analysis submitted with manuscript |
| `Nature_submission_CODE_2.html` | `code/alternative_analysis.html` | Alternative specifications and robustness checks |
| `BUENO_SPIvsCompPrices_StructuralBreak_6_REGIONES_CDFs_Causality.html` | `code/comprehensive_analysis.html` | Complete analysis across all 6 regions with structural breaks, CDFs, and causality tests |

## Data Files

### Processed Data

All data files are renamed to follow a consistent pattern: `[country]_[region]_[variable].csv`

| Original Name | GitHub Name | Description |
|--------------|-------------|-------------|
| `Dataframe_Canada_CornPrices_SPI_1_and_24_month.csv` | `data/processed/ontario_corn_prices.csv` | Ontario corn prices with SPI indices (1-month and 24-month) |
| `Dataframe_ONTARIO_REAL_GFARMERS_comprices.csv` | `data/processed/ontario_farmers_prices.csv` | Ontario farm-gate prices (real terms) |
| `Dataframe_Jalisco_SPI_24month.csv` | `data/processed/mexico_jalisco_data.csv` | Jalisco (Mexico) corn prices and SPI-24 |
| `Dataframe_Sinaloa_SPI_24month.csv` | `data/processed/mexico_sinaloa_data.csv` | Sinaloa (Mexico) corn prices and SPI-24 |
| `Dataframe_conjunta_Maiz_24monthSPI_Goias_VFINAL.csv` | `data/processed/brazil_goias_data.csv` | Goiás (Brazil) corn prices and SPI-24 |
| `Dataframe_conjunta_Maiz_24monthSPI_MatoGrosso_VFINAL.csv` | `data/processed/brazil_matogrosso_data.csv` | Mato Grosso (Brazil) corn prices and SPI-24 |

## Results Files

| Original Name | GitHub Name | Description |
|--------------|-------------|-------------|
| `Table1.xlsx` | `results/Table1.xlsx` | Distributional transformation results across all regions and periods |
| `Table2.xlsx` | `results/Table2.xlsx` | Temporal causality analysis (Granger tests and neural networks) |

## Figures

| Original Name | GitHub Name | Description |
|--------------|-------------|-------------|
| `Figure1.png` | `figures/Figure1.png` | Distributional collapse of Ontario corn prices |
| `Figure2.png` | `figures/Figure2.png` | Index inclusion natural experiment (corn vs. oats) |
| `Figure3.png` | `figures/Figure3.png` | Brazilian policy shock and climate-price relationships |

## Naming Conventions

### Code Files
- Format: `[purpose]_[method].html`
- Use descriptive names: `main_analysis`, `comprehensive_analysis`, `alternative_analysis`
- Keep original HTML format for reproducibility

### Data Files
- Format: `[country]_[region]_[type].csv`
- Countries: `ontario` (Canada), `mexico`, `brazil`
- Regions: `jalisco`, `sinaloa`, `goias`, `matogrosso`
- Types: `data` (includes prices and climate), `prices` (prices only), `climate` (climate only)

### Results Files
- Keep table numbers matching manuscript: `Table1.xlsx`, `Table2.xlsx`
- Use descriptive names for supplementary tables: `Table_S1_robustness.xlsx`

### Figures
- Keep figure numbers matching manuscript: `Figure1.png`, `Figure2.png`, etc.
- Use descriptive names for supplementary figures: `Figure_S1_timeseries.png`

## Variable Names in Data Files

### Common Variables Across All Files:
- `date` or `Date`: Date in YYYY-MM-DD format
- `year`: Year (numeric)
- `month`: Month (numeric, 1-12)
- `price` or `corn_price`: Corn price in local currency per metric ton
- `spi_24` or `SPI_24`: 24-month Standardized Precipitation Index
- `spi_1` or `SPI_1`: 1-month Standardized Precipitation Index (Ontario only)

### Region-Specific Variables:
- **Ontario files**:
  - `price_international`: Internationally-linked price (from FRED + regional premium)
  - `price_farmers`: Farm-gate price (from Grain Farmers of Ontario)
  - `exchange_rate`: USD/CAD exchange rate

- **Mexican files**:
  - `state`: State name (Jalisco or Sinaloa)
  - `market`: Specific market location

- **Brazilian files**:
  - `state`: State name (Goiás or Mato Grosso)
  - `municipality`: Municipality name

## File Size Reference

| File Type | Typical Size | Note |
|-----------|--------------|------|
| HTML code files | 500 KB - 2 MB | Self-contained with embedded data |
| CSV data files | 10-150 KB | Monthly data, 200-300 observations |
| Excel tables | 10-20 KB | Summary statistics only |
| PNG figures | 50-200 KB | High resolution (300 dpi) |

## Version Control

All files in this repository represent the **final version submitted to Nature Climate Change** in November 2025.

- No version numbers in filenames (use Git tags for versions)
- Original working files preserved locally but not uploaded to GitHub
- Only cleaned, documented, final versions included

## Questions?

If you need clarification on any file naming or structure:
1. Check the [data metadata README](../data/metadata/README_data.md)
2. Open an issue on GitHub
3. Contact: ahdelvalle@gmail.com
