# Data Documentation

This directory contains processed datasets used in "Global Market Integration Undermines Agricultural Insurance Under Climate Change."

## Overview

All datasets are monthly time series combining:
1. Agricultural commodity prices (corn/maize)
2. Climate indices (Standardized Precipitation Index, SPI-24)
3. Metadata (dates, regions, data sources)

## Data Files

### 1. Ontario (Canada) - International Prices
**File**: `ontario_corn_prices.csv`  
**Period**: January 2000 - October 2025 (304 months)  
**Source**: FRED Global Price of Corn + Bank of Canada exchange rates

**Variables**:
- `date`: Date (YYYY-MM-DD format)
- `year`: Year
- `month`: Month (1-12)
- `price_international`: Corn price (CAD/metric ton), internationally-linked
- `spi_24`: 24-month Standardized Precipitation Index
- `spi_1`: 1-month Standardized Precipitation Index

**Price Calculation**:
```
price_international = (FRED_USD_price × 1.15) × exchange_rate_USDCAD
```
Where:
- `FRED_USD_price`: Global corn price from FRED (USD/MT)
- `1.15`: Regional premium factor (Ontario basis differential)
- `exchange_rate_USDCAD`: Monthly average from Bank of Canada

**Climate Data**:
- Station: Ottawa Macdonald-Cartier Airport (ID: 4333)
- Variable: Monthly total precipitation
- SPI calculation: 24-month rolling window, gamma distribution fit

### 2. Ontario (Canada) - Farm-Gate Prices
**File**: `ontario_farmers_prices.csv`  
**Period**: January 2012 - October 2025 (160 months)  
**Source**: Grain Farmers of Ontario + OMAFRA

**Variables**:
- `date`: Date (YYYY-MM-DD format)
- `year`: Year
- `month`: Month (1-12)
- `price_farmers`: Farm-gate corn price (CAD/metric ton), real terms
- `spi_24`: 24-month Standardized Precipitation Index

**Notes**:
- Prices are from actual farmer sales, not futures
- Deflated to 2012 CAD using Canadian CPI
- Same climate data as international prices (Ottawa station)

### 3. Jalisco (Mexico)
**File**: `mexico_jalisco_data.csv`  
**Period**: January 2000 - December 2024 (300 months)  
**Source**: SNIIM (Sistema Nacional de Información e Integración de Mercados)

**Variables**:
- `date`: Date (YYYY-MM-DD format)
- `year`: Year
- `month`: Month (1-12)
- `price`: Corn price (MXN/metric ton)
- `spi_24`: 24-month Standardized Precipitation Index
- `state`: "Jalisco"
- `market`: Specific market location within Jalisco

**Climate Data**:
- Source: CONAGUA (Comisión Nacional del Agua)
- Stations: 415 stations across Mexico, subset for Jalisco region
- Spatial averaging: Thiessen polygons

### 4. Sinaloa (Mexico)
**File**: `mexico_sinaloa_data.csv`  
**Period**: January 2000 - December 2024 (300 months)  
**Source**: SNIIM

**Variables**: Same structure as Jalisco file
- `state`: "Sinaloa"

**Notes**:
- Sinaloa is Mexico's largest corn-producing state
- Different growing season than Jalisco (Oct-Apr vs May-Oct)

### 5. Goiás (Brazil)
**File**: `brazil_goias_data.csv`  
**Period**: January 2002 - December 2024 (276 months)  
**Source**: LAMFO (University of Brasilia)

**Variables**:
- `date`: Date (YYYY-MM-DD format)
- `year`: Year
- `month`: Month (1-12)
- `price`: Corn price (BRL/metric ton)
- `spi_24`: 24-month Standardized Precipitation Index
- `state`: "Goiás"
- `municipality`: Specific municipality

**Climate Data**:
- Source: INMET (Instituto Nacional de Meteorologia)
- Stations: 39 stations in Goiás state
- Spatial averaging: Thiessen polygons

**Access**: Brazilian raw data available from Prof. Herbert Kimura (hkimura@unb.br) subject to institutional data sharing agreements

### 6. Mato Grosso (Brazil)
**File**: `brazil_matogrosso_data.csv`  
**Period**: January 2002 - December 2024 (276 months)  
**Source**: LAMFO (University of Brasilia)

**Variables**: Same structure as Goiás file
- `state`: "Mato Grosso"

**Climate Data**:
- Source: INMET
- Stations: 33 stations in Mato Grosso state

**Notes**:
- Mato Grosso is Brazil's largest corn-producing state
- Different policy environment than Goiás (less CONAB intervention)

## Data Quality Notes

### Missing Data
All datasets are complete with no missing values in the processed versions. Original raw data had gaps that were handled as follows:

1. **Ontario climate**: Only Ottawa Macdonald-Cartier Airport maintained complete post-2018 records. Other stations showed 70-93% missing data.

2. **Mexican climate**: Stations with >20% missing data in any year were excluded. Final dataset uses 415 stations with complete records.

3. **Brazilian climate**: Stations with >15% missing data excluded. Thiessen polygon averaging provides spatial redundancy.

### Outliers
No outliers were removed. Extreme values during crisis periods (2008, 2020-2022) are genuine and central to the analysis.

### Data Transformations

1. **Prices**:
   - All nominal prices (no deflation except Ontario farm-gate prices)
   - Currency conversions applied consistently using monthly averages
   - No log transformations (preserves distributional properties)

2. **SPI Calculation**:
   - 24-month rolling window
   - Gamma distribution fit to historical precipitation
   - Transformed to standard normal (mean=0, sd=1)
   - Standardization period: Full available record for each station

3. **Spatial Averaging** (Mexico and Brazil):
   - Thiessen polygon method
   - Weights based on Voronoi tessellation
   - Robust to individual station failures

## Structural Break Periods

The analysis divides data into structural break periods based on:
- 2008 financial crisis and biofuel mandates
- 2012 global drought
- 2015 El Niño
- 2020 COVID-19 pandemic
- 2022 Ukraine war

Exact break dates vary by region based on empirical validation (Chow tests, CUSUM tests).

**Typical periods**:
- Pre-Crisis: 2000-2007
- Crisis: 2008-2011  
- Recovery: 2012-2019
- COVID: 2020-2025

See manuscript Methods section for detailed break point identification methodology.

## Data Citations

### Primary Sources to Cite:

1. **FRED Economic Data**
   - Federal Reserve Bank of St. Louis. (2025). Global Price of Corn. Retrieved from https://fred.stlouisfed.org/series/PMAIZMTUSDM

2. **Environment and Climate Change Canada**
   - Environment and Climate Change Canada. (2025). Historical Climate Data. Retrieved from https://climate.weather.gc.ca/

3. **SNIIM (Mexico)**
   - Secretaría de Economía. (2025). Sistema Nacional de Información e Integración de Mercados. Retrieved from http://www.economia-sniim.gob.mx/

4. **LAMFO (Brazil)**
   - Kimura, H., et al. (2025). Brazilian Agricultural Market Data [Data set]. Laboratório de Aprendizado de Máquina em Finanças e Organizações, University of Brasilia.

5. **CONAGUA (Mexico)**
   - Comisión Nacional del Agua. (2025). Sistema de Información Climatológica Integral. México: SEMARNAT.

6. **INMET (Brazil)**
   - Instituto Nacional de Meteorologia. (2025). Banco de Dados Meteorológicos. Brasília, Brazil: INMET.

## Usage Rights

### Public Data (freely available):
- Ontario international prices (FRED) - Public domain
- Ontario climate data (Environment Canada) - Open Government License
- Mexican climate data (CONAGUA) - Open access

### Restricted Data (available upon request):
- Brazilian price data (LAMFO) - Contact Prof. Herbert Kimura
- Brazilian climate data (INMET) - Public access with registration

### Processed Data (this repository):
- All processed datasets in this repository - MIT License
- Free to use with attribution

## Data Processing Pipeline

1. **Raw data collection**: Web scraping (Mexico), API access (Canada), collaboration (Brazil)
2. **Quality control**: Missing data identification, outlier detection, temporal consistency checks
3. **Climate index calculation**: SPI-24 using standardized methodology
4. **Spatial averaging**: Thiessen polygons for regional indices
5. **Currency conversion**: Monthly exchange rates from central banks
6. **Merging**: Align price and climate data by date and region
7. **Validation**: Cross-check against published statistics

## Reproducibility Notes

To fully reproduce the data processing:

1. **Ontario prices**: 
   - Download from FRED: `PMAIZMTUSDM` series
   - Download Bank of Canada exchange rates: `FXUSDCAD` series
   - Apply 1.15 regional premium factor
   - Merge with Ottawa precipitation data

2. **Mexican data**:
   - Access SNIIM web portal (requires registration)
   - Extract monthly corn prices for Jalisco and Sinaloa markets
   - Download CONAGUA climate data
   - Calculate regional SPI-24 indices

3. **Brazilian data**:
   - Contact Prof. Herbert Kimura for raw data access
   - Original processing scripts available upon request

## Questions or Issues?

If you have questions about:
- **Data quality**: Check this README or open a GitHub issue
- **Variable definitions**: See this README or manuscript Methods
- **Access to raw data**: Contact original data providers listed above
- **Processing details**: Contact ahdelvalle@gmail.com

## Version History

- **v1.0** (November 2025): Initial release with manuscript submission
- All data current as of October 2025

## Related Files

- See [`data_sources.txt`](data_sources.txt) for complete URL list
- See [`../docs/file_naming_guide.md`](../../docs/file_naming_guide.md) for file naming conventions
- See [`../../code/`](../../code/) for analysis code using these data
