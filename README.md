# Beijing PM2.5 Multi-Site Air Quality Data

Beijing air quality data from 12 monitoring stations collected from March 2013 to February 2017.

## Dataset Overview

- **Records**: 420,768 hourly observations
- **Stations**: 12 monitoring stations across Beijing
- **Time Period**: March 2013 - February 2017
- **Features**: 19 columns (pollutants, meteorology, time, station)

### Columns

| Column | Description |
|--------|------------|
| PM2.5 | Particulate matter < 2.5 microns |
| PM10 | Particulate matter < 10 microns |
| SO2 | Sulfur dioxide |
| NO2 | Nitrogen dioxide |
| CO | Carbon monoxide |
| O3 | Ozone |
| TEMP | Temperature (°C) |
| PRES | Pressure (hPa) |
| DEWP | Dew Point (°C) |
| RAIN | Precipitation (mm) |
| wd | Wind direction |
| WSPM | Wind speed (m/s) |
| station | Monitoring station name |
| datetime | Timestamp |

### Monitoring Stations

- Aotizhongxin, Changping, Dingling, Dongsi, Guanyuan, Gucheng
- Huairou, Nongzhanguan, Shunyi, Tiantan, Wanliu, Wanshouxigong

---

## Research Questions

### 1. Data Exploration & Cleaning
- What is the data quality? How many missing values exist and how are they handled?

### 2. Descriptive Statistics
- What are the central tendencies and distributions of key pollutants (PM2.5, PM10, SO2, NO2, CO, O3)?

### 3. Temporal Analysis
- How does PM2.5 concentration change over time? Are there seasonal patterns?

### 4. Spatial Analysis
- Which monitoring stations have the highest PM2.5 levels? How do stations compare?

### 5. Correlation Analysis
- Which variable is most correlated with PM2.5? Is temperature negatively correlated with pollution?

### 6. Comparative Distribution
- How do pollutants compare in terms of distribution? Which has the highest median values?

---

## Key Findings

### Missing Values
- PM2.5: 8,739 missing (2.08%)
- CO: 20,701 missing (4.92%)
- Handled using forward/backward fill after removing rows with missing PM2.5

### PM2.5 Statistics
| Metric | Value |
|--------|-------|
| Mean | 79.82 |
| Median | 55.0 |
| Min | 2.0 |
| Max | 999.0 |
| Std | 81.02 |

### Correlation with PM2.5
| Variable | Correlation |
|----------|------------|
| PM10 | 0.881 |
| CO | 0.788 |
| NO2 | 0.667 |
| SO2 | 0.484 |
| TEMP | -0.131 |
| O3 | -0.151 |

### Station Ranking (by PM2.5 mean)
1. Dongsi (86.08) - highest
2. Wanshouxigong (84.99)
3. Nongzhanguan (84.77)
...
12. Dingling (66.39) - lowest

---

## Requirements

```
pandas
matplotlib
seaborn
```

## Usage

```python
import pandas as pd
import glob

files = glob.glob("PRSA_Data_*.csv")
df = pd.concat([pd.read_csv(f) for f in files], ignore_index=True)
```