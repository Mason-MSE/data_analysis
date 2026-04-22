# Beijing PM2.5 Multi-Site Air Quality Data Analysis

Beijing air quality data from 12 monitoring stations collected from March 2013 to February 2017.

## Dataset Overview

- **Total Records**: 420,768 hourly observations
- **Monitoring Stations**: 12 sites across Beijing
- **Time Period**: March 2013 - February 2017
- **Features**: 19 columns

---

## Part 1: Data Loading and Structure

### Load the Dataset

```python
import pandas as pd
import glob

files = glob.glob("PRSA_Data_*.csv")
df = pd.concat([pd.read_csv(f) for f in files], ignore_index=True)
```

### Display First 5 Rows
| No | year | month | day | hour | PM2.5 | PM10 | SO2 | NO2 | CO | O3 | TEMP | PRES | DEWP | RAIN | wd | WSPM | station | datetime |
|----|------|-------|-----|------|-------|------|-----|-----|-----|-----|------|------|------|------|----|------|---------|---------------------|
| 1  | 2013 | 3     | 1   | 0    | 4.0   | 4.0  | 3.0 | 2.0 | 200 | 82.0| -2.3 | 1020.8 | -19.7 | 0.0 | E  | 0.5  | Dingling | 2013-03-01 00:00:00 |
| 2  | 2013 | 3     | 1   | 1    | 7.0   | 7.0  | 3.0 | 9.0 | 200 | 80.0| -2.5 | 1021.3 | -19.0 | 0.0 | ENE| 0.7  | Dingling | 2013-03-01 01:00:00 |
| 3  | 2013 | 3     | 1   | 2    | 5.0   | 5.0  | 3.0 | 2.0 | 200 | 79.0| -3.0 | 1021.3 | -19.9 | 0.0 | ENE| 0.2  | Dingling | 2013-03-01 02:00:00 |
| 4  | 2013 | 3     | 1   | 3    | 6.0   | 6.0  |12.0 | 8.0 | 300 | 81.0| -3.6 | 1021.8 | -19.1 | 0.0 | NNE| 1.0  | Dingling | 2013-03-01 03:00:00 |
| 5  | 2013 | 3     | 1   | 4    | 3.0   | 3.0  |14.0 | 8.0 | 300 | 81.0| -3.5 | 1022.3 | -19.4 | 0.0 | N  | 2.1  | Dingling | 2013-03-01 04:00:00 |

### Column Names and Data Types

| Column | Type | Description |
|--------|------|------------|
| No | int64 | Record ID |
| year | int64 | Year (2013-2017) |
| month | int64 | Month (1-12) |
| day | int64 | Day (1-31) |
| hour | int64 | Hour (0-23) |
| PM2.5 | float64 | PM2.5 concentration (μg/m³) |
| PM10 | float64 | PM10 concentration (μg/m³) |
| SO2 | float64 | Sulfur dioxide (μg/m³) |
| NO2 | float64 | Nitrogen dioxide (μg/m³) |
| CO | float64 | Carbon monoxide (μg/m³) |
| O3 | float64 | Ozone (μg/m³) |
| TEMP | float64 | Temperature (°C) |
| PRES | float64 | Pressure (hPa) |
| DEWP | float64 | Dew Point (°C) |
| RAIN | float64 | Precipitation (mm) |
| wd | object | Wind direction |
| WSPM | float64 | Wind speed (m/s) |
| station | object | Monitoring station |
| datetime | datetime64 | Timestamp |

### Total Rows and Columns

- **Rows**: 420,768
- **Columns**: 19

---

## Part 2: Missing Values Identification and Handling

### Identify Missing Values

| Column | Missing Count |
|--------|-------------|
| PM2.5 | 8,739 |
| PM10 | 6,449 |
| SO2 | 9,021 |
| NO2 | 12,116 |
| CO | 20,701 |
| O3 | 13,277 |
| TEMP | 398 |
| PRES | 393 |
| DEWP | 403 |
| RAIN | 390 |
| wd | 1,822 |
| WSPM | 318 |
| station | 0 |

### Missing Values Percentage

| Column | Missing % |
|--------|----------|
| PM2.5 | 2.08% |
| CO | 4.92% |
| NO2 | 2.88% |
| O3 | 3.16% |

### Replace and Remove Missing Values

```python
# Remove rows with missing PM2.5
df.dropna(subset=['PM2.5'], inplace=True)

# Remove rows with >30% missing values
df = df[df.isna().mean(axis=1) < 0.3]

# Sort by time
df.sort_values(by=['year','month','day','hour'], inplace=True)

# Forward and backward fill
df.ffill(inplace=True)
df.bfill(inplace=True)
df.dropna(inplace=True)
```

---

## Part 3: Descriptive Statistics

### Statistical Summary for Pollutants

| Pollutant | Mean | Median | Min | Max | Std |
|----------|------|--------|-----|-----|-----|
| PM2.5 | 79.82 | 55.0 | 2.0 | 999.0 | 81.02 |
| PM10 | 104.78 | 82.0 | 2.0 | 999.0 | 92.37 |
| SO2 | 15.88 | 7.0 | 0.29 | 500.0 | 21.74 |
| NO2 | 50.65 | 43.0 | 1.03 | 290.0 | 35.25 |
| CO | 1232.94 | 900.0 | 100.0 | 10000.0 | 1166.55 |
| O3 | 57.02 | 44.0 | 0.21 | 1071.0 | 56.59 |

### Key Observations

- **PM2.5**: Mean 79.82 μg/m³, median 55.0 μg/m³ (right-skewed distribution)
- **PM10**: Highest mean concentration at 104.78 μg/m³
- **CO**: Highest variability with std of 1166.55
- **O3**: Negative correlation with PM2.5

---

## Part 4: Station-based Analysis

### Filter Data by Station

```python
# Filter by specific station
df[df['station']=='Changping']

# Filter by multiple stations
df[df['station'].isin(['Aotizhongxin','Dingling'])]
```

### Average PM2.5 by Station (Ranked)

| Station | Mean PM2.5 |
|---------|------------|
| Dongsi | 86.08 |
| Wanshouxigong | 84.99 |
| Nongzhanguan | 84.77 |
| Gucheng | 84.07 |
| Wanliu | 83.42 |
| Guanyuan | 82.92 |
| Aotizhongxin | 82.56 |
| Tiantan | 82.06 |
| Shunyi | 79.33 |
| Changping | 71.62 |
| Huairou | 69.66 |
| Dingling | 66.39 |

### Key Observations

- **Highest pollution**: Dongsi (86.08 μg/m³) - urban center
- **Lowest pollution**: Dingling (66.39 μg/m³) - suburban/north
- Clear gradient from urban to suburban areas

---

## Part 5: Data Visualization

### Histogram of PM2.5

```python
import seaborn as sns

sns.histplot(df['PM2.5'], bins=50, kde=True)
plt.title('Distribution of PM2.5')
plt.xlabel('PM2.5')
plt.ylabel('Frequency')
plt.show()
```

**Observation**: Right-skewed distribution with most values concentrated below 100 μg/m³

### Line Plot of PM2.5 Over Time

```python
df_sample = df.sample(5000)
df_sample['datetime']=pd.to_datetime(df_sample[['year','month','day','hour']])
df_sample = df_sample.sort_values('datetime')

sns.lineplot(x='datetime', y='PM2.5', data=df_sample)
plt.title('PM2.5 Over Time')
plt.xlabel('Time')
plt.ylabel('PM2.5')
plt.show()
```

**Observation**: Shows temporal patterns with seasonal variation

### Boxplot of Pollutants

```python
sns.boxenplot(data=df[['PM2.5','PM10','SO2','NO2','CO','O3']])
plt.title('Boxplot of Pollutants')
plt.xlabel('Pollutants')
plt.ylabel('Values')
plt.show()
```

**Observation**: 
- CO and PM10 have highest median values
- Multiple outliers in all pollutants (especially PM2.5 and PM10 with max values reaching 999)

---

## Part 6: Correlation Analysis

### Which Variable is Most Correlated with PM2.5?

| Variable | Correlation with PM2.5 |
|----------|------------------------|
| PM10 | +0.881 |
| CO | +0.788 |
| NO2 | +0.667 |
| SO2 | +0.484 |
| O3 | -0.151 |
| TEMP | -0.131 |
| DEWP | +0.114 |
| PRES | +0.020 |

### Does Temperature Affect Pollution Levels?

**Yes, temperature shows a negative correlation with PM2.5 (-0.131)**

- Higher temperatures are associated with lower PM2.5 levels
- This may be due to:
  - Better atmospheric dispersion in warmer conditions
  - Seasonal effects (heating in winter increases emissions)
  - Inverse relationship with atmospheric stability

### Correlation Heatmap

```python
corr = df[['PM2.5','PM10','SO2','NO2','CO','O3','TEMP','PRES','DEWP']].corr()
sns.heatmap(corr, annot=True, cmap='coolwarm')
plt.title('Correlation Matrix')
plt.show()
```

---

## Monitoring Stations

- **Urban**: Dongsi, Wanshouxigong, Nongzhanguan, Guanyuan, Gucheng, Wanliu, Aotizhongxin, Tiantan
- **Suburban**: Shunyi, Changping, Huairou, Dingling

---

## Requirements

```
pandas
matplotlib
seaborn
numpy
```

---

## Usage Example

```python
import pandas as pd
import glob
import matplotlib.pyplot as plt
import seaborn as sns

# Load data
files = glob.glob("PRSA_Data_*.csv")
df = pd.concat([pd.read_csv(f) for f in files], ignore_index=True)

# Check structure
print(df.shape)
print(df.dtypes)

# Check missing values
print(df.isna().sum())

# Descriptive statistics
print(df.describe())

# Correlation analysis
corr = df[['PM2.5','PM10','SO2','NO2','CO','O3','TEMP']].corr()
print(corr['PM2.5'].sort_values(ascending=False))
```
