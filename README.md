# Power Consumption Analysis

This project analyzes power consumption across three distinct distribution zones (Zone 1, Zone 2, and Zone 3) alongside environmental factors like Temperature, Humidity, and Wind Speed.

## Dataset

The original dataset `powerconsumption1.csv` contains historical consumption records recorded at 10-minute intervals throughout 2017.

## Reproduction Steps

Follow these sequential steps to fully reproduce the cleaning, feature engineering, visualization, and peak-consumption profiling analysis:

### 1. Environment Setup & Data Loading
Install the required data profiling library and import the visualization packages:
```bash
pip install fg-data-profiling pandas numpy matplotlib seaborn
```
Load the dataset:
```python
import pandas as pd
import numpy as np

df = pd.read_csv('powerconsumption1.csv')
```

### 2. Exploratory Data Profiling
Generate an automated, interactive HTML report summarizing data types, distributions, and null values:
```python
from data_profiling import ProfileReport

profile = ProfileReport(df, title="Power Consumption Report", explorative=True)
profile.to_file('report.html')
```

### 3. Data Cleansing & Feature Engineering
Convert the `Datetime` column to parsed datetime objects and categorize the numerical variables (Temperature, Humidity, Wind Speed, Diffuse Flows, and Power Consumption per Zone) into specific range groups for grouped aggregation:
```python
# Parse dates
df['Datetime'] = pd.to_datetime(df['Datetime'])

# Extract time attributes
df['year'] = df['Datetime'].dt.year
df['month'] = df['Datetime'].dt.month
df['day'] = df['Datetime'].dt.day
df['hour'] = df['Datetime'].dt.hour
df['date'] = df['Datetime'].dt.date
df['dayoftheweek'] = df['Datetime'].dt.day_name()

# Categorize variables (e.g., Temperature groups)
temp_conds = [df['Temperature'] < 10, df['Temperature'] < 20, df['Temperature'] < 30, df['Temperature'] < 40]
temp_choices = ['below 10', 'below 20', 'below 30', 'below 40']
df['temperaturegroup'] = np.select(temp_conds, temp_choices, default='below 50')
```

### 4. Data Visualization & Insights
Run the visualization blocks to study trends and external dependencies:
*   **Total Trends Over Time:** Line plots comparing overall daily power demands across the three zones.
*   **Seasonality Patterns:** Line charts plotting average demand variations grouped by Month of the Year and Hour of the Day.
*   **Weekly Fluctuations:** Standard bar and line patterns analyzing weekend vs. weekday energy drops.
*   **Environmental Correlations:** Heatmap matrix studying how Humidity (negative correlation) and Temperature (positive correlation) impact electrical loads.

### 5. Find Peak & Minimum Demand Windows
Identify the exact dates, hours, and temperatures of absolute highest and lowest grid stress:
```python
# Find indices for extremes
z1_max = df.loc[df['PowerConsumption_Zone1'].idxmax()]
z1_min = df.loc[df['PowerConsumption_Zone1'].idxmin()]
```

### 6. Export Final Dataset
Save the engineered dataset containing the new group features to a local CSV:
```python
df.to_csv('power_consumption_processed.csv', index=False)
```
