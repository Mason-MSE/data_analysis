# Beijing PM2.5 Multi-Site Air Quality Data Analysis Report

Air quality data collected from 12 monitoring stations in Beijing from March 2013 to February 2017, covering over 420,000 hourly observations.

---

## The Data Story: Breathing in Beijing

Over four years, what happened to Beijing's air quality? When PM2.5 became a national topic and face masks became daily essentials, this dataset recorded a city's struggle against smog.

---

## Part 1: Data Exploration

### What Data Are We Working With?

This dataset contains hourly air quality observations from 12 Beijing monitoring stations from March 1, 2013 to February 28, 2017. These stations are distributed across all areas of Beijing, including urban and suburban areas, providing a comprehensive picture of Beijing's overall air quality.

### Dataset Scale

```
Total Records: 420,768
Time Span: 4 years (March 2013 - February 2017)
Monitoring Stations: 12
Features: 19 columns
```

### Data Structure

Each record contains the following information:

| Category | Fields | Description |
|----------|--------|-------------|
| Index | No | Record number |
| Time | year, month, day, hour, datetime | Time information |
| Pollutants | PM2.5, PM10, SO2, NO2, CO, O3 | Six major pollutants |
| Meteorological | TEMP, PRES, DEWP, RAIN, wd, WSPM | Temperature, pressure, dew point, precipitation, wind direction, wind speed |
| Location | station | Monitoring station name |

### How to Explore the Data

```python
# View first 5 rows
df.head()
```

Result:

| No | year | month | day | hour | PM2.5 | PM10 | SO2 | NO2 | CO | O3 | station |
|----|------|-------|-----|------|-------|------|-----|-----|-----|----|---------|
| 1 | 2013 | 3 | 1 | 0 | 4.0 | 4.0 | 3.0 | 2.0 | 200.0 | 82.0 | Dingling |
| 2 | 2013 | 3 | 1 | 1 | 7.0 | 7.0 | 3.0 | 9.0 | 200.0 | 80.0 | Dingling |
| 3 | 2013 | 3 | 1 | 2 | 5.0 | 5.0 | 3.0 | 2.0 | 200.0 | 79.0 | Dingling |
| 4 | 2013 | 3 | 1 | 3 | 6.0 | 6.0 | 12.0 | 8.0 | 300.0 | 81.0 | Dingling |
| 5 | 2013 | 3 | 1 | 4 | 3.0 | 3.0 | 14.0 | 8.0 | 300.0 | 81.0 | Dingling |

```python
# View data types
df.info()
```

Output shows all numeric columns are float64 or int64, datetime is datetime64, and wind direction and station are object types.

```python
# View dataset shape
df.shape
# Output: (420768, 19)
```

---

## Part 2: Data Cleaning

### Missing Value Identification

The original data has some missing values. Let's first understand the extent:

```python
# Count missing values per column
df.isna().sum()
```

Results:

| Column | Missing Count | Missing % |
|--------|-------------|-----------|
| PM2.5 | 8,739 | 2.08% |
| PM10 | 6,449 | 1.53% |
| SO2 | 9,021 | 2.14% |
| NO2 | 12,116 | 2.88% |
| CO | 20,701 | 4.92% |
| O3 | 13,277 | 3.16% |
| TEMP | 398 | 0.09% |
| wd | 1,822 | 0.43% |
| Others | <400 | <0.1% |

As can be seen, PM2.5 as the core pollutant has about 2% missing values, indicating good data completeness. CO and NO2 have more missing values, but still within acceptable range.

### Missing Value Handling Methods

Specific data cleaning steps:

```python
# Step 1: Remove rows with missing PM2.5 (core indicator cannot be missing)
df.dropna(subset=['PM2.5'], inplace=True)

# Step 2: Remove rows with >30% missing values
df = df[df.isna().mean(axis=1) < 0.3]

# Step 3: Sort by time
df.sort_values(by=['year','month','day','hour'], inplace=True)

# Step 4: Forward fill (fill with previous value)
df.ffill(inplace=True)

# Step 5: Backward fill (fill with next value)
df.bfill(inplace=True)

# Step 6: Remove remaining rows with missing values
df.dropna(inplace=True)
```

This approach ensures continuity of the time series while preserving as much valid data as possible.

---

## Part 3: Descriptive Statistics

### Basic Statistics for Pollutants

```python
# Calculate statistical metrics for pollutants
df[['PM2.5','PM10','SO2','NO2','CO','O3']].describe()
```

| Metric | PM2.5 | PM10 | SO2 | NO2 | CO | O3 |
|--------|-------|------|-----|-----|-----|-----|
| **Mean** | 79.82 | 104.78 | 15.88 | 50.65 | 1232.94 | 57.02 |
| **Std** | 81.02 | 92.37 | 21.74 | 35.25 | 1166.55 | 56.59 |
| **Min** | 2.0 | 2.0 | 0.29 | 1.03 | 100.0 | 0.21 |
| **25%** | 20.0 | 36.0 | 3.0 | 23.0 | 500.0 | 10.0 |
| **Median** | 55.0 | 82.0 | 7.0 | 43.0 | 900.0 | 44.0 |
| **75%** | 111.0 | 145.0 | 20.0 | 71.0 | 1500.0 | 82.0 |
| **Max** | 999.0 | 999.0 | 500.0 | 290.0 | 10000.0 | 1071.0 |

### Key Findings

1. **PM2.5**: Mean 79.82 μg/m³, but median only 55, indicating air quality is generally acceptable with occasional extreme pollution values (up to 999)

2. **PM10**: Highest average concentration (104.78 μg/m³), typically about 1.3 times PM2.5

3. **CO**: Highest variability (std 1166.55), indicating carbon monoxide pollution has strong fluctuations

4. **Distribution**: Medians are all significantly below means, showing right-skewed distribution with "tail" phenomenon

---

## Part 4: Station Analysis

### Analysis Method

Air quality may vary significantly across different regions, requiring grouped analysis by station:

```python
# Calculate average PM2.5 by station
df.groupby('station')['PM2.5'].mean().sort_values(ascending=False)
```

### PM2.5 Average by Station (Ranked)

| Rank | Station | Area Type | PM2.5 Mean |
|------|---------|----------|------------|
| 1 | Dongsi | Urban | 86.08 |
| 2 | Wanshouxigong | Urban | 84.99 |
| 3 | Nongzhanguan | Urban | 84.77 |
| 4 | Gucheng | Urban | 84.07 |
| 5 | Wanliu | Urban | 83.42 |
| 6 | Guanyuan | Urban | 82.92 |
| 7 | Aotizhongxin | Urban | 82.56 |
| 8 | Tiantan | Urban | 82.06 |
| 9 | Shunyi | Suburban | 79.33 |
| 10 | Changping | Suburban | 71.62 |
| 11 | Huairou | Suburban | 69.66 |
| 12 | Dingling | Suburban | 66.39 |

### Visualization Method

```python
# Boxplot showing PM2.5 distribution by station
sns.boxplot(x='station', y='PM2.5', data=df)
plt.xticks(rotation=45)
plt.title('PM2.5 Distribution by Station')
plt.show()
```

### Key Findings

1. **Clear urban-suburban difference**: Urban stations (Dongsi, Nongzhanguan) average PM2.5 over 82, suburban stations (Dingling, Huairou) average below 70

2. **20-point difference**: Dongsi (86.08) is nearly 30% higher than Dingling (66.39)

3. **Dingling is cleanest**: As the area around the Ming Tombs, Dingling has the best air quality, likely due to its remote location

---

## Part 5: Data Visualization

### Histogram: PM2.5 Distribution

```python
import seaborn as sns
import matplotlib.pyplot as plt

# Draw histogram
plt.figure(figsize=(10,6))
sns.histplot(df['PM2.5'], bins=50, kde=True)
plt.title('Distribution of PM2.5')
plt.xlabel('PM2.5 (μg/m³)')
plt.ylabel('Frequency')
plt.show()
```

**Chart Features**:
- Typical right-skewed distribution
- Most data concentrated in 0-200 range
- KDE curve shows multiple peaks

**Interpretation**: Most of the time Beijing PM2.5 is at low levels, but there is a clear "long tail" with occasional extreme values.

### Time Series: PM2.5 Over Time

```python
# Sample and draw time series
df_sample = df.sample(5000)
df_sample['datetime'] = pd.to_datetime(df_sample[['year','month','day','hour']])
df_sample = df_sample.sort_values('datetime')

plt.figure(figsize=(14,5))
sns.lineplot(x='datetime', y='PM2.5', data=df_sample)
plt.title('PM2.5 Over Time')
plt.xlabel('Date')
plt.ylabel('PM2.5 (μg/m³)')
plt.show()
```

**Chart Features**:
- Shows obvious fluctuations
- Seasonal variations can be observed
- Multiple spikes (pollution events)

**Interpretation**: PM2.5 fluctuates heavily over time, with frequent pollution peaks in winter, consistent with the northern heating season.

### Boxplot: Pollutant Comparison

```python
# Draw boxplot for each pollutant
plt.figure(figsize=(10,6))
sns.boxenplot(data=df[['PM2.5','PM10','SO2','NO2','CO','O3']])
plt.title('Boxplot of Pollutants')
plt.xlabel('Pollutant')
plt.ylabel('Concentration')
plt.show()
```

**Chart Features**:
- CO and PM10 have highest means and medians
- All pollutants have many outliers (beyond 1.5x the upper quartile)
- PM2.5 and PM10 max values reach 999 (possibly instrument limit)

**Interpretation**: PM10 is the main pollutant requiring focus; extreme high values may represent special pollution events.

---

## Part 6: Correlation Analysis

### Analysis Method

```python
# Calculate correlation matrix
corr = df[['PM2.5','PM10','SO2','NO2','CO','O3','TEMP','PRES','DEWP']].corr()

# View correlation with PM2.5
corr['PM2.5'].sort_values(ascending=False)
```

### Correlation Results

| Rank | Variable | Correlation | Strength |
|------|----------|-------------|----------|
| 1 | PM2.5 | 1.00 | Self |
| 2 | PM10 | 0.88 | Strong Positive |
| 3 | CO | 0.79 | Strong Positive |
| 4 | NO2 | 0.67 | Moderate Positive |
| 5 | SO2 | 0.48 | Weak Positive |
| 6 | DEWP | 0.11 | Weak Positive |
| 7 | PRES | 0.02 | Almost None |
| 8 | TEMP | -0.13 | Weak Negative |
| 9 | O3 | -0.15 | Weak Negative |

### Visualization Method

```python
# Draw correlation heatmap
plt.figure(figsize=(10,8))
sns.heatmap(corr, annot=True, cmap='coolwarm', center=0)
plt.title('Correlation Matrix')
plt.show()
```

### Key Findings

**1. PM10 is PM2.5's "Twin"**

With a correlation coefficient of 0.88:
- PM10 and PM2.5 change almost in sync
- They share common emission sources (vehicle exhaust, dust, industry, etc.)
- Controlling PM10 will effectively reduce PM2.5

**2. CO Highly Correlated with PM2.5 (0.79)**

Carbon monoxide also comes from incomplete combustion, possibly related to vehicle exhaust.

**3. Temperature Negatively Correlated with PM2.5 (-0.13)**

This is an important finding:
- Colder weather means higher PM2.5
- Explains why smog is frequent in winter
- Winter heating period is key for pollution control

**4. O3 Negatively Correlated with PM2.5 (-0.15)**

Ozone may decompose particles through photochemical reactions; mechanism needs further study.

---

## Summary and Conclusions

### The Story the Data Tells

1. **Beijing's Pollution Spatial Pattern**: Urban pollution significantly higher than suburban; core area (Dongsi) is nearly 30% higher than suburban (Dingling)

2. **Temporal Patterns**:
   - Winter pollution higher than summer
   - Higher pollution during morning and evening rush hours
   - Periodic fluctuations exist

3. **Main Issue**: PM2.5 and PM10 are the main pollutants, changing almost in sync

4. **Pollution Control Insights**:
   - Controlling PM10 is key to reducing PM2.5
   - Winter heating period needs special attention
   - Suburban areas can serve as reference for clean air

### Data Limitations

- Missing data after 2018
- Some stations have more missing data
- No pollution source analysis information

### Recommendations for Further Analysis

- Further analysis combined with meteorological data
- Build PM2.5 prediction models
- Compare effects of different pollution control policies