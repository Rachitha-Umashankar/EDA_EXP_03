## EXP 3 - Delhi Air Quality Analysis

## Aim


To compare air quality parameters in Delhi across different stations and analyze the relationship between pollutants (e.g., PM2.5 and NO₂) using scatter plots and correlation analysis.


## Procedure / Algorithm

1)Load the dataset using pandas.

2)Preprocess the data:

3)Convert the date column (period.datetimeFrom.utc) to datetime format.

4)Drop missing or invalid values.

5)Pivot the dataset so each pollutant (parameter) becomes a separate column.

6)Plot scatter plot between PM2.5 and NO₂ to study their relationship.

7)Plot correlation heatmap between all pollutants to identify relationships.

8)Interpret the results — identify which pollutants are correlated and which stations are most polluted.


## Program

Name : U.RACHITHA 

Reg No:212225220078

```
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import StandardScaler, MinMaxScaler

# Load the dataset
df = pd.read_csv("C:/Users/acer/Downloads/del-sirifort-cpcb-2024-25.csv")

# Display basic information
print("Dataset Shape:", df.shape)

print("\nFirst 5 Rows:")
display(df.head())

print("\nDataset Information:")
df.info()

# --------------------------------------------------
# 1. Select required air-quality parameters
# --------------------------------------------------

pollutants = [
    "PM2.5 (µg/m³)",
    "PM10 (µg/m³)",
    "NO2 (µg/m³)",
    "Ozone (µg/m³)"
]

# Convert pollutant columns to numeric
for col in pollutants:
    df[col] = pd.to_numeric(df[col], errors="coerce")

# Remove missing values
df_clean = df[pollutants].dropna().copy()

print("\nRows after preprocessing:", len(df_clean))

# --------------------------------------------------
# 2. Univariate Distribution Analysis
# --------------------------------------------------

for col in pollutants:
    plt.figure(figsize=(8, 5))
    sns.histplot(df_clean[col], kde=True)
    plt.title(f"Distribution of {col}")
    plt.xlabel(col)
    plt.ylabel("Frequency")
    plt.tight_layout()
    plt.show()

# --------------------------------------------------
# 3. Measures of Central Tendency
# --------------------------------------------------

central_tendency = pd.DataFrame({
    "Mean": df_clean[pollutants].mean(),
    "Median": df_clean[pollutants].median(),
    "Mode": df_clean[pollutants].mode().iloc[0]
})

print("\nMeasures of Central Tendency:")
display(central_tendency.round(2))

# --------------------------------------------------
# 4. Measures of Spread
# --------------------------------------------------

spread = pd.DataFrame({
    "Minimum": df_clean[pollutants].min(),
    "Maximum": df_clean[pollutants].max(),
    "Range": df_clean[pollutants].max() - df_clean[pollutants].min(),
    "Variance": df_clean[pollutants].var(),
    "Standard Deviation": df_clean[pollutants].std(),
    "IQR": (
        df_clean[pollutants].quantile(0.75)
        - df_clean[pollutants].quantile(0.25)
    )
})

print("\nMeasures of Spread:")
display(spread.round(2))

# --------------------------------------------------
# 5. Boxplot for Outlier Detection
# --------------------------------------------------

for col in pollutants:
    plt.figure(figsize=(8, 4))
    sns.boxplot(x=df_clean[col])
    plt.title(f"Boxplot of {col}")
    plt.xlabel(col)
    plt.tight_layout()
    plt.show()

# --------------------------------------------------
# 6. Outlier Detection using IQR Method
# --------------------------------------------------

outlier_results = []

for col in pollutants:

    Q1 = df_clean[col].quantile(0.25)
    Q3 = df_clean[col].quantile(0.75)
    IQR = Q3 - Q1

    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR

    outliers = df_clean[
        (df_clean[col] < lower_bound) |
        (df_clean[col] > upper_bound)
    ]

    outlier_results.append({
        "Pollutant": col,
        "Q1": Q1,
        "Q3": Q3,
        "IQR": IQR,
        "Lower Bound": lower_bound,
        "Upper Bound": upper_bound,
        "Number of Outliers": len(outliers)
    })

outlier_df = pd.DataFrame(outlier_results)

print("\nOutlier Detection using IQR:")
display(outlier_df.round(2))

# --------------------------------------------------
# 7. Min-Max Scaling
# --------------------------------------------------

minmax_scaler = MinMaxScaler()

minmax_scaled = pd.DataFrame(
    minmax_scaler.fit_transform(df_clean[pollutants]),
    columns=pollutants
)

print("\nMin-Max Scaled Data:")
display(minmax_scaled.head().round(3))

# --------------------------------------------------
# 8. Standardization using Z-Score
# --------------------------------------------------

standard_scaler = StandardScaler()

standardized = pd.DataFrame(
    standard_scaler.fit_transform(df_clean[pollutants]),
    columns=pollutants
)

print("\nStandardized Data:")
display(standardized.head().round(3))

# --------------------------------------------------
# 9. Inequality Analysis using Gini Coefficient
# --------------------------------------------------

def gini_coefficient(values):

    values = values.sort_values().values

    n = len(values)

    if values.sum() == 0:
        return 0

    index = range(1, n + 1)

    gini = (
        (2 * sum(i * x for i, x in zip(index, values)))
        / (n * values.sum())
    ) - ((n + 1) / n)

    return gini


gini_values = {}

for col in pollutants:
    gini_values[col] = gini_coefficient(df_clean[col])

gini_df = pd.DataFrame(
    gini_values,
    index=["Gini Coefficient"]
).T

print("\nInequality Analysis:")
display(gini_df.round(3))

# --------------------------------------------------
# 10. Complete Statistical Summary
# --------------------------------------------------

print("\nComplete Statistical Summary:")
display(df_clean[pollutants].describe().round(2))
```

## Output

<img width="1338" height="420" alt="Screenshot 2026-09-01 144437" src="https://github.com/user-attachments/assets/bc91c51c-124b-4dee-b4c5-7d39f87d1afd" />

<img width="1352" height="422" alt="Screenshot 2026-09-01 144458" src="https://github.com/user-attachments/assets/85ecb9f9-eb3c-4452-a547-8035756241b7" />

<img width="1345" height="417" alt="Screenshot 2026-09-01 144512" src="https://github.com/user-attachments/assets/017fb293-b273-4ad6-9537-c4d8670ab715" />

<img width="1345" height="371" alt="Screenshot 2026-09-01 144526" src="https://github.com/user-attachments/assets/9650adb9-4a17-4178-b7e0-2a2b16591833" />
<img width="1343" height="360" alt="Screenshot 2026-09-01 144536" src="https://github.com/user-attachments/assets/66a28ccd-ff5e-46b9-b777-137c78c03461" />
<img width="1347" height="410" alt="Screenshot 2026-09-01 183740" src="https://github.com/user-attachments/assets/df10a706-9f59-45ce-855e-fc934e68307d" />
<img width="932" height="302" alt="Screenshot 2026-09-01 183846" src="https://github.com/user-attachments/assets/c7315eae-cde0-4691-9c77-782a5caac75c" />
<img width="576" height="242" alt="Screenshot 2026-09-01 183856" src="https://github.com/user-attachments/assets/1cf39fdc-e0fb-4b0b-afa1-cde42fdc1d7e" />

<img width="692" height="355" alt="Screenshot 2026-09-01 183905" src="https://github.com/user-attachments/assets/a9517072-2c0e-4d20-9702-1583d574d2d1" />

## Interpretation

1)PM2.5 and NO₂ show a strong positive correlation, suggesting that both pollutants increase together, likely due to vehicle and industrial emissions.

## Result

The dataset was successfully loaded and processed to extract pollutant-wise and station-wise air quality data for Delhi.


