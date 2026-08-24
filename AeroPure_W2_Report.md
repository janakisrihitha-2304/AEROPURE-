# AeroPure --- W2 Data Ingestion, Cleaning, Validation & EDA Report

## 1. W2 Objective

The objective of W2 was to prepare the air-quality dataset for
machine-learning work by:

-   inspecting and validating the raw data;
-   identifying missing values and long missing gaps;
-   detecting invalid and negative pollutant values;
-   analysing outliers;
-   performing justified missing-value treatment;
-   creating missingness indicators;
-   analysing AQI across cities and time;
-   examining pollutant--AQI relationships;
-   checking for data leakage;
-   establishing a chronological, leakage-safe train/test split;
-   producing a validated ML-ready dataset.

> **Scope note:** This W2 notebook currently covers the air-quality
> sensor dataset. Weather, satellite and traffic feeds are not
> represented in the results shown here and should not be claimed as
> completed unless they are added separately.

------------------------------------------------------------------------

## 2. Dataset Overview

The final ML dataset contains:

-   **Rows:** 24,850
-   **Columns:** 29
-   **Time period:** 2015-01-01 to 2020-07-01
-   **Target:** AQI
-   **Cities:** multiple Indian cities
-   **Pollutant variables:** PM2.5, PM10, NO, NO2, NOx, NH3, CO, SO2,
    O3, Benzene and Toluene

The final ML dataset contains only observations for which the AQI target
is available.

Before the final ML filtering, the cleaned dataset contained 29,531
rows. Of these, 24,850 had a valid AQI target and 4,681 did not. The
4,681 rows without AQI were excluded from the supervised ML dataset
because AQI is the prediction target.

------------------------------------------------------------------------

## 3. Initial Data Quality Assessment

### 3.1 Negative values

The initial negative-value check showed:

  Pollutant     Negative values
  ----------- -----------------
  PM2.5                       0
  PM10                        0
  NO                          0
  NO2                         0
  NOx                         0
  NH3                         0
  CO                          0
  SO2                         0
  O3                          0
  Benzene                     0
  Toluene                     0
  Xylene                      0

No negative pollutant measurements were detected.

### 3.2 Duplicate and infinite values

The final validation showed:

-   **Duplicate rows:** 0
-   **Infinite values:** 0
-   **Negative pollutant values after processing:** 0

City and Date were complete, with no missing values.

------------------------------------------------------------------------

## 4. Missing-Value Analysis

The initial missingness analysis identified substantial variation
between pollutants.

Important initial missingness levels included:

  Feature     Missing count   Missing %
  --------- --------------- -----------
  Xylene             17,349      58.75%
  PM10               10,033      33.97%
  NH3                 9,252      31.33%
  Toluene             6,995      23.69%
  AQI                 4,681      15.85%
  Benzene             4,376      14.82%
  PM2.5               3,567      12.08%
  NOx                 3,394      11.49%
  O3                  2,558       8.66%
  SO2                 2,471       8.37%
  NO2                 2,410       8.16%
  NO                  2,399       8.12%
  CO                    968       3.28%

### Observation

Xylene had the highest missingness and was therefore not suitable for
reliable imputation in the current feature set.

------------------------------------------------------------------------

## 5. Missing-Gap Analysis

Long consecutive missing periods were examined by pollutant.

The largest missing gaps included:

  Pollutant     Longest missing gap (days)
  ----------- ----------------------------
  PM10                               2,009
  NH3                                2,009
  PM2.5                              1,211
  Benzene                            1,169
  NOx                                1,169
  Toluene                            1,169
  NO2                                1,153
  NO                                 1,111
  O3                                 1,093
  SO2                                1,072
  CO                                   347

These long gaps showed that simple interpolation across the entire time
series would not be appropriate.

------------------------------------------------------------------------

## 6. Xylene Removal

Xylene was removed because of its very high missingness.

The average missingness analysis showed approximately **64.51%
missingness across cities** for Xylene, with some cities having
essentially complete absence of observations.

Therefore:

``` text
Xylene → removed from the ML feature set
```

This reduced the cleaned dataset from 30 columns to 29 columns.

------------------------------------------------------------------------

## 7. Short-Gap Interpolation

Short missing gaps were treated separately from long gaps.

Interpolation was used only for short gaps where temporal continuity
made interpolation reasonable.

After short-gap interpolation, missingness was reduced, but substantial
missingness remained for variables such as:

-   PM10
-   NH3
-   Toluene
-   Benzene
-   PM2.5

This confirmed that short-gap interpolation alone was insufficient.

------------------------------------------------------------------------

## 8. Missingness Indicators

Missingness indicators were created for the pollutant variables.

Examples:

-   `PM2.5_Missing`
-   `PM10_Missing`
-   `NO_Missing`
-   `NO2_Missing`
-   `NOx_Missing`
-   `NH3_Missing`
-   `CO_Missing`
-   `SO2_Missing`
-   `O3_Missing`
-   `Benzene_Missing`
-   `Toluene_Missing`

These indicators preserve information about whether an observation was
originally missing even after imputation.

------------------------------------------------------------------------

## 9. AQI Target Validation

Before creating the supervised ML dataset:

-   Total cleaned rows: **29,531**
-   Rows with valid AQI: **24,850**
-   Rows without AQI: **4,681**

Because AQI is the supervised learning target, rows without AQI were
excluded from the ML dataset rather than inventing target values.

Final ML target:

-   **Minimum AQI:** 13
-   **Maximum AQI:** 2,049

------------------------------------------------------------------------

## 10. AQI Distribution

For the complete ML target:

-   Count: **24,850**
-   Mean: **166.46**
-   Median: **118**
-   Minimum: **13**
-   Maximum: **2,049**

The mean being substantially higher than the median indicates a
right-skewed AQI distribution, with a relatively small number of very
high-AQI observations.

------------------------------------------------------------------------

## 11. AQI Category Distribution

The observed AQI bucket counts were:

  AQI category           Count
  -------------------- -------
  Moderate               8,829
  Satisfactory           8,224
  Poor                   2,781
  Very Poor              2,337
  Good                   1,341
  Severe                 1,338
  Missing AQI bucket     4,681

The missing category corresponds to rows where AQI itself was
unavailable.

------------------------------------------------------------------------

## 12. City-wise AQI Analysis

The highest average AQI cities in the analysis included:

  City             Mean AQI   Median AQI
  -------------- ---------- ------------
  Ahmedabad          452.12        384.5
  Delhi              259.49        257.0
  Patna              240.78        215.0
  Gurugram           225.12        208.0
  Lucknow            217.97        198.0
  Talcher            172.89        128.5
  Jorapokhar         159.25        133.0
  Brajrajnagar       150.28        122.0
  Kolkata            140.57         94.0
  Guwahati           140.11         98.0

These values describe the distribution present in this dataset and
should not be interpreted as current real-world AQI rankings.

------------------------------------------------------------------------

## 13. Temporal Analysis

### 13.1 Monthly trend

Monthly AQI statistics were calculated for the available period.

The analysis covered **67 monthly periods** from 2015 through July 2020.

Example values:

  Month       Mean AQI
  --------- ----------
  2015-01       343.00
  2015-02       418.83
  2015-03       298.16
  2015-04       192.22
  2015-05       193.18
  2020-03       110.18
  2020-04        86.72
  2020-05        87.45
  2020-06        76.21
  2020-07        72.50

### 13.2 Year-wise trend

  Year     Count   Mean AQI   Median AQI
  ------ ------- ---------- ------------
  2015     1,827     212.46          175
  2016     2,573     197.15          149
  2017     3,234     181.47          129
  2018     5,724     182.68          124
  2019     7,071     156.52          109
  2020     4,421     113.52           93

The yearly values show a lower average AQI in the later portion of the
dataset. Because the dataset ends in July 2020 and observation counts
vary by year, this should be treated as a dataset-level trend rather
than a complete annual comparison.

------------------------------------------------------------------------

## 14. Outlier Analysis

IQR-based outlier analysis was performed.

Initial outlier counts were substantial for several pollutants. After
imputation, the IQR analysis gave the following approximate outlier
percentages:

  Pollutant     Outlier %
  ----------- -----------
  Toluene          12.58%
  SO2               9.87%
  NO                9.62%
  CO                9.21%
  PM2.5             8.03%
  NOx               7.72%
  Benzene           7.46%
  PM10              6.24%
  NH3               4.85%
  NO2               4.44%
  O3                2.95%

The outliers were **not automatically deleted** because unusually high
pollution values can represent genuine pollution events rather than data
errors.

------------------------------------------------------------------------

## 15. Correlation Analysis

A pollutant--AQI correlation matrix was generated.

The final correlations with AQI were approximately:

  Pollutant     Correlation with AQI
  ----------- ----------------------
  CO                           0.677
  PM2.5                        0.654
  NO2                          0.534
  PM10                         0.494
  SO2                          0.489
  NOx                          0.469
  NO                           0.437
  Toluene                      0.277
  O3                           0.200
  NH3                          0.100
  Benzene                      0.046

The strongest linear relationships in this dataset are observed for CO
and PM2.5.

Correlation does not establish causation and should not by itself
determine final feature selection.

------------------------------------------------------------------------

## 16. Data Leakage Check

A final leakage check was performed.

Results:

-   AQI-derived columns such as `AQI_Bucket`: **not present**
-   No feature was found to be an exact duplicate of AQI
-   Missingness indicators were retained as separate features
-   No suspicious perfect correlation with AQI was found

The strongest feature correlations were below 0.70.

------------------------------------------------------------------------

## 17. Chronological Train/Test Split

A time-based split was used instead of a random split.

### Training set

-   Rows: **19,882**
-   Period: **2015-01-01 → 2019-12-06**

### Test set

-   Rows: **4,968**
-   Period: **2019-12-07 → 2020-07-01**

This preserves temporal ordering and evaluates the model on a later
period.

------------------------------------------------------------------------

## 18. Leakage-Safe Imputation

For the final ML pipeline, imputation was performed after the
chronological split.

The procedure was:

1.  Split the raw ML data chronologically.
2.  Create missingness indicators.
3.  Calculate city-wise pollutant medians using **training data only**.
4.  Use those training-derived city medians to fill training and test
    data.
5.  Use training global medians as fallback values when a city-specific
    training median was unavailable.
6.  Verify that no pollutant values remained missing.

Final verification:

-   Remaining missing pollutants in train: **0**
-   Remaining missing pollutants in test: **0**
-   Training rows changed: **0**
-   Test rows changed: **0**

This prevents test-period observations from being used to calculate
imputation statistics.

------------------------------------------------------------------------

## 19. Final ML Feature Set

The current W3 preparation uses:

### Pollutant features --- 11

-   PM2.5
-   PM10
-   NO
-   NO2
-   NOx
-   NH3
-   CO
-   SO2
-   O3
-   Benzene
-   Toluene

### Missingness indicators --- 11

One indicator for each pollutant.

### Time features --- 4

-   Year
-   Month
-   Day
-   DayOfWeek

### Total

**26 input features**

Target:

``` text
AQI
```

Current ML matrices:

-   `X_train`: **19,882 × 26**
-   `X_test`: **4,968 × 26**
-   `y_train`: **19,882**
-   `y_test`: **4,968**

------------------------------------------------------------------------

## 20. Final W2 Validation

  Validation item                    Result
  ---------------------------------- --------
  Final ML rows                      24,850
  Final columns                      29
  Missing pollutant values           0
  Missing AQI in ML dataset          0
  Duplicate rows                     0
  Negative pollutant values          0
  Infinite values                    0
  Xylene removed                     Yes
  Missingness indicators             Yes
  Time-based split                   Yes
  Train-only imputation statistics   Yes
  AQI_Bucket used as feature         No
  Final cleaned dataset saved        Yes

------------------------------------------------------------------------

## 21. Output Files

The W2 workflow produced/uses the following project outputs:

### Cleaned ML dataset

``` text
data/processed/AeroPure_ML_clean.csv
```

### Graphs

The `graphs/` directory contains generated EDA visualizations including:

-   AQI boxplot
-   AQI category distribution
-   AQI distribution
-   Average AQI by city
-   Missing-value percentage
-   Monthly AQI trend
-   Pollutant--AQI correlation matrix
-   Pollutant correlation with AQI

### Notebook

``` text
notebooks/W2_AeroPure.ipynb
```

------------------------------------------------------------------------

## 22. W2 Conclusion

The air-quality dataset has been systematically inspected, cleaned and
validated for machine-learning preparation.

The major data-quality issues were missing pollutant observations,
particularly Xylene, PM10, NH3 and Toluene. Xylene was removed because
of its very high missingness. Short missing gaps were interpolated,
while remaining missing pollutant observations were handled using a
leakage-safe hierarchical imputation strategy based only on training
data.

The final supervised ML dataset contains 24,850 observations with a
complete AQI target. The final validation found no remaining missing
pollutant values, duplicate rows, infinite values or negative pollutant
measurements.

A chronological train/test split was established, with 19,882 training
observations and 4,968 future-period test observations. The resulting
feature set contains 26 predictors consisting of pollutant
concentrations, missingness indicators and time features.

**W2 is therefore complete for the current air-quality dataset and the
data is ready for the W3 modelling pipeline.**

------------------------------------------------------------------------

## 23. Recommended W3 Next Steps

1.  Feature distribution and transformation checks.
2.  Decide whether scaling is required based on the selected models.
3.  Encode city information if it is included.
4.  Establish a simple baseline model.
5.  Train tree-based and/or linear regression models.
6.  Evaluate using MAE, RMSE and R².
7.  Compare models on the chronological test set.
8.  Perform feature-importance analysis.
9.  Tune the selected model.
10. Save the final model and evaluation results.
