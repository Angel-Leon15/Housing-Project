# Methodology & Technical Documentation

This document provides detailed information about the analytical methods, statistical techniques, and implementation details used in this housing market analysis project.

##  Table of Contents

1. [Data Structure](#data-structure)
2. [Analytical Pipeline](#analytical-pipeline)
3. [Statistical Methods](#statistical-methods)
4. [Modeling Approach](#modeling-approach)
5. [Assumptions & Limitations](#assumptions--limitations)
6. [Future Improvements](#future-improvements)

---

##  Data Structure

### Input Dataset Requirements

The analysis expects a structured CSV file with the following schema:

| Column Name | Data Type | Description | Constraints |
|------------|-----------|-------------|-------------|
| `Price` | Numeric | Housing sale price | Non-negative, USD |
| `SquareFeet` | Numeric | Property size | Positive integer |
| `Bedrooms` | Numeric | Number of bedrooms | Non-negative integer |
| `Bathrooms` | Numeric | Number of bathrooms | Non-negative, can be decimal (e.g., 2.5) |
| `YearBuilt` | Numeric | Construction year | Valid year (e.g., 1900-2023) |
| `Neighborhood` | Categorical | Neighborhood designation | String/Factor |

### Data Quality Considerations

- **Missing Values**: The current implementation assumes complete data. Missing values should be handled through imputation or removal before analysis.
- **Data Types**: Ensure proper type conversion (categorical variables as factors, numeric variables as numeric).
- **Outliers**: Addressed through z-score analysis but not automatically removed.

---

## 🔄 Analytical Pipeline

### Phase 1: Data Import & Exploration

```r
Housing_Market <- read_csv("./housing_price_dataset.csv", show_col_types = F)
```

**Purpose**: Load and inspect the dataset structure

**Outputs**:
- Data structure summary (`glimpse()`)
- Descriptive statistics (`summary()`)

**Key Insights**:
- Data dimensions
- Variable distributions
- Initial range and central tendency measures

### Phase 2: Historical Trend Analysis

#### 2.1 Overall Price Trends

```r
yearly_price_trend <- Housing_Market %>%
  group_by(YearBuilt) %>%
  summarise(avg_price = mean(Price))
```

**Method**: Aggregation by year with mean calculation

**Visualization**: Line plot showing temporal evolution of average housing prices

**Interpretation**: 
- Identifies long-term price trajectories
- Reveals market cycles (boom and bust periods)
- Shows overall appreciation trends

#### 2.2 Neighborhood-Specific Trends

```r
neighborhood_price_trend <- Housing_Market %>%
  group_by(YearBuilt, Neighborhood) %>%
  summarise(avg_price = mean(Price))
```

**Method**: Two-dimensional aggregation (year × neighborhood)

**Visualization**: Faceted line plots by neighborhood

**Interpretation**:
- Compares neighborhood price evolution
- Identifies high-growth vs. stable neighborhoods
- Reveals neighborhood-specific market dynamics

### Phase 3: Outlier Detection

#### Z-Score Method

```r
Housing_Market$Price_z <- scale(Housing_Market$Price)
outliers <- Housing_Market[abs(Housing_Market$Price_z) > 3, ]
```

**Statistical Basis**: 
- Assumes approximately normal distribution of prices
- Z-score represents standard deviations from mean
- Threshold |z| > 3 captures ~99.7% of normal distribution

**Formula**:
```
z = (x - μ) / σ
where:
  x = observed price
  μ = mean price
  σ = standard deviation of price
```

**Interpretation**:
- Properties with |z| > 3 are potential outliers
- Could represent: luxury properties, data errors, or unique market conditions
- Visualized via scatter plot (SquareFeet vs. Price)

**Limitations**:
- Sensitive to assumption of normality
- May flag legitimate high-value properties
- Doesn't account for regional price differences

---

## 📊 Statistical Methods

### Multiple Linear Regression

#### Model Specification

```r
model <- lm(Price ~ SquareFeet + Bedrooms + Bathrooms + YearBuilt + Neighborhood, 
            data = Housing_Market)
```

**Mathematical Form**:
```
Price = β₀ + β₁(SquareFeet) + β₂(Bedrooms) + β₃(Bathrooms) + 
        β₄(YearBuilt) + β₅(Neighborhood) + ε

where:
  β₀ = intercept
  β₁...β₅ = coefficients
  ε = error term
```

#### Assumptions

1. **Linearity**: Relationship between predictors and response is linear
2. **Independence**: Observations are independent
3. **Homoscedasticity**: Constant variance of residuals
4. **Normality**: Residuals are approximately normally distributed
5. **No multicollinearity**: Predictors are not highly correlated

#### Model Diagnostics (Recommended)

While not implemented in the current version, these checks are recommended:

```r
# Residual plots
plot(model)

# Variance Inflation Factor
library(car)
vif(model)

# Adjusted R-squared
summary(model)$adj.r.squared
```

#### Feature Importance Analysis

```r
coefficients <- tidy(model)
top_features <- coefficients %>%
  arrange(desc(abs(estimate))) %>%
  select(term, estimate)
```

**Interpretation**:
- Larger absolute coefficient = greater influence on price
- Sign indicates direction of relationship (+ or -)
- Statistical significance shown in p-values

**Typical Rankings**:
1. SquareFeet (positive, strong effect)
2. Neighborhood (categorical, varies by level)
3. YearBuilt (positive for newer homes)
4. Bathrooms (positive, moderate effect)
5. Bedrooms (positive, but weaker than expected)

---

## Modeling Approach

### Historical Rate Calculation

#### Year-over-Year Percentage Change

```r
yearly_price_trend <- yearly_price_trend %>%
  mutate(percent_change = (avg_price - lag(avg_price)) / lag(avg_price) * 100)
```

**Formula**:
```
% Change = ((Price_t - Price_t-1) / Price_t-1) × 100
```

#### Appreciation & Depreciation Rates

```r
appreciation_rates <- yearly_price_trend %>%
  filter(percent_change > 0) %>%
  summarise(average_appreciation = mean(percent_change, na.rm = TRUE))

depreciation_rates <- yearly_price_trend %>%
  filter(percent_change < 0) %>%
  summarise(average_depreciation = mean(percent_change, na.rm = TRUE))
```

**Purpose**: Quantifies market volatility for realistic forecasting

**Typical Values**:
- Appreciation: ~1.54% (growth years)
- Depreciation: ~-1.24% (decline years)

### Price Prediction Methodology

#### Step 1: Base Prediction (Static)

```r
future_data <- expand.grid(
  SquareFeet = mean(Housing_Market$SquareFeet),
  Bedrooms = mean(Housing_Market$Bedrooms),
  Bathrooms = mean(Housing_Market$Bathrooms),
  YearBuilt = mean(Housing_Market$YearBuilt),
  Neighborhood = unique(Housing_Market$Neighborhood),
  Year = future_years
)

predicted_prices <- predict(model, newdata = future_data)
```

**Approach**: 
- Uses average values for property characteristics
- Generates predictions for each neighborhood
- Produces straight-line forecast (unrealistic)

**Limitation**: Doesn't account for market volatility

#### Step 2: Volatility-Adjusted Prediction

```r
random_percent_changes <- runif(length(predicted_prices), 
                                min = min_percent_change, 
                                max = max_percent_change)

for (i in 2:length(adjusted_predicted_prices)) {
  adjusted_predicted_prices[i] <- adjusted_predicted_prices[i - 1] * 
                                   (1 + random_percent_changes[i])
}
```

**Enhancement**: Incorporates historical volatility

**Method**:
- Randomly samples percentage changes from historical range
- Applies compound growth/decline year-over-year
- Creates realistic fluctuation in forecasts

**Formula**:
```
Price_t = Price_t-1 × (1 + r_t)

where r_t ~ Uniform(min_depreciation, max_appreciation)
```

**Advantages**:
- More realistic than linear extrapolation
- Reflects market uncertainty
- Bounded by historical volatility

**Limitations**:
- Assumes past volatility predicts future volatility
- Doesn't model economic cycles
- Random component reduces reproducibility

---

##  Assumptions & Limitations

### Data Assumptions

1. **Representativeness**: Dataset represents the broader housing market
2. **Temporal Consistency**: Data collection methods remained consistent over time
3. **Completeness**: No systematic missing data patterns
4. **Accuracy**: Prices reflect actual transaction values (not asking prices)

### Model Assumptions

1. **Linear Relationships**: Price changes linearly with predictors
   - *Reality*: May have non-linear relationships (e.g., diminishing returns on square footage)
   
2. **Static Coefficients**: Feature importance remains constant over time
   - *Reality*: Preferences shift (e.g., work-from-home increases bedroom value)
   
3. **Independence**: Properties are independent observations
   - *Reality*: Spatial autocorrelation likely exists
   
4. **Stationarity**: Market dynamics remain stable
   - *Reality*: External shocks (recessions, policy changes) alter dynamics

### Prediction Limitations

1. **No External Variables**: Doesn't incorporate:
   - Interest rates
   - Economic indicators (unemployment, GDP)
   - Policy changes (zoning, tax incentives)
   - Supply and demand shifts
   
2. **Extrapolation Risk**: Predictions extend far beyond training data range
   
3. **Neighborhood Homogeneity**: Assumes neighborhoods remain comparable over time
   
4. **Black Swan Events**: Cannot predict unprecedented market shocks

### Statistical Limitations

1. **Outlier Treatment**: Identifies but doesn't remove outliers
2. **No Cross-Validation**: Model not validated on hold-out data
3. **No Model Comparison**: Alternative models not evaluated
4. **Overfitting Risk**: Model may not generalize to new data

---

## 🚀 Future Improvements

### Data Enhancements

- [ ] **Additional Features**:
  - Geographic coordinates (latitude/longitude)
  - School district quality ratings
  - Crime statistics
  - Proximity to amenities (transit, shopping)
  - Lot size
  - Property age (vs. year built)
  - Recent renovation flags

- [ ] **Time Series Data**: Transaction dates for temporal modeling

- [ ] **External Data**: Economic indicators, interest rates

### Analytical Enhancements

- [ ] **Advanced Outlier Detection**: 
  - Isolation Forest
  - Local Outlier Factor (LOF)
  - DBSCAN clustering

- [ ] **Feature Engineering**:
  ```r
  # Price per square foot
  Housing_Market$price_per_sqft <- Housing_Market$Price / Housing_Market$SquareFeet
  
  # Property age
  Housing_Market$age <- current_year - Housing_Market$YearBuilt
  
  # Bedroom density
  Housing_Market$bed_density <- Housing_Market$Bedrooms / Housing_Market$SquareFeet
  ```

- [ ] **Interaction Terms**: 
  ```r
  model <- lm(Price ~ SquareFeet * Neighborhood + Bedrooms * Bathrooms + ..., 
              data = Housing_Market)
  ```

### Model Improvements

- [ ] **Alternative Models**:
  - Random Forest (handles non-linearity)
  - Gradient Boosting (XGBoost, LightGBM)
  - Neural Networks
  - Spatial models (account for geographic proximity)

- [ ] **Time Series Forecasting**:
  - ARIMA models
  - Prophet (Facebook's forecasting tool)
  - LSTM networks

- [ ] **Ensemble Methods**: Combine multiple models for robust predictions

- [ ] **Model Validation**:
  ```r
  # k-fold cross-validation
  library(caret)
  train_control <- trainControl(method = "cv", number = 10)
  model_cv <- train(Price ~ ., data = Housing_Market, 
                    method = "lm", trControl = train_control)
  ```

- [ ] **Regularization**: Ridge or Lasso regression to prevent overfitting
  ```r
  library(glmnet)
  model_ridge <- cv.glmnet(x = model.matrix(~.-1, data = predictors), 
                           y = Housing_Market$Price, alpha = 0)
  ```

### Visualization Enhancements

- [ ] **Interactive Plots**: 
  ```r
  library(plotly)
  ggplotly(price_trend_plot)
  ```

- [ ] **Geospatial Visualization**: 
  ```r
  library(leaflet)
  # Map with price overlays
  ```

- [ ] **Dashboard**: Shiny app for interactive exploration

- [ ] **Residual Analysis**: 
  ```r
  # Diagnostic plots
  autoplot(model)
  ```

### Documentation Improvements

- [ ] Add code comments explaining complex operations
- [ ] Include example outputs in documentation
- [ ] Create tutorial vignette for beginners
- [ ] Add FAQ section for common issues

---

## 📚 References

### Statistical Methods
- James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). *An Introduction to Statistical Learning*
- Fox, J. (2015). *Applied Regression Analysis and Generalized Linear Models*

### R Programming
- Wickham, H., & Grolemund, G. (2017). *R for Data Science*
- Wickham, H. (2016). *ggplot2: Elegant Graphics for Data Analysis*

### Real Estate Analytics
- Shiller, R. J. (2015). *Irrational Exuberance* (3rd ed.)
- Case, K. E., & Shiller, R. J. (1989). "The Efficiency of the Market for Single-Family Homes"

---

## 🔧 Technical Notes

### Software Versions

This analysis was developed using:
- R version: 4.0+
- tidyverse: 1.3.0+
- ggplot2: 3.3.0+
- broom: 0.7.0+

### Computational Complexity

- **Trend Analysis**: O(n log n) due to grouping and sorting
- **Outlier Detection**: O(n) for z-score calculation
- **Linear Regression**: O(n × p²) where p = number of predictors
- **Prediction**: O(n × p) for n predictions

### Memory Considerations

For large datasets (>1M rows):
- Consider using `data.table` for faster grouping
- Use sampling for exploratory analysis
- Implement chunked processing for predictions

---

##  Support

For technical questions or methodology clarifications:
- Open a GitHub issue with the "methodology" label
- Email: [leon.angelf15@gmail.com]
- Reference specific sections using anchor links

---

*Last Updated: 2023-11-25*
*Version: 1.0*
