# Housing Market Analysis & Price Prediction

A comprehensive data science project analyzing historical housing market trends and predicting future housing prices using statistical modeling and machine learning techniques in R.

## Project Overview

This project performs an in-depth analysis of housing market data to understand price trends, identify influential features, and forecast future housing prices. The analysis includes trend visualization, outlier detection, feature importance analysis, and predictive modeling with adjusted volatility based on historical appreciation/depreciation rates.

## Key Features

- **Trend Analysis**: Visualizes housing price trends over time by year built and neighborhood
- **Outlier Detection**: Identifies price outliers using z-score analysis (|z| > 3)
- **Feature Importance**: Linear regression model to determine which factors most influence housing prices
- **Price Prediction**: Forecasts housing prices from 2020-2050 with realistic volatility adjustments
- **Appreciation/Depreciation Analysis**: Calculates historical year-over-year percentage changes

## Project Structure

```
.
├── Data Science Project.Rmd    # Main R Markdown analysis file
├── housing_price_dataset.csv   # Input dataset (not included in repo)
├── README.md                    # Project documentation
├── CONTRIBUTING.md              # Contribution guidelines
└── METHODOLOGY.md               # Detailed methodology and documentation
```

##  Prerequisites

### Required R Packages

```r
install.packages(c(
  "broom",
  "readr",
  "dplyr",
  "dbplyr",
  "tidyverse",
  "ggplot2"
))
```

### System Requirements

- R version 4.0.0 or higher
- RStudio (recommended)
- Minimum 4GB RAM

## 📈 Dataset Structure

The analysis expects a CSV file with the following columns:
- `Price`: Housing price (numeric)
- `SquareFeet`: Property size in square feet (numeric)
- `Bedrooms`: Number of bedrooms (numeric)
- `Bathrooms`: Number of bathrooms (numeric)
- `YearBuilt`: Year the property was built (numeric)
- `Neighborhood`: Neighborhood category (categorical)

## 🚀 Getting Started

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/housing-market-analysis.git
   cd housing-market-analysis
   ```

2. **Prepare your data**
   - Place your `housing_price_dataset.csv` file in the project root directory
   - Ensure the CSV follows the dataset structure above

3. **Open in RStudio**
   ```r
   # Open the .Rmd file
   file.edit("Data Science Project.Rmd")
   ```

4. **Run the analysis**
   - Click "Knit" in RStudio to generate the HTML report, or
   - Run chunks individually for interactive analysis

## 📊 Key Findings

The analysis reveals:

1. **Influential Features** (in order of importance):
   - Square Feet
   - Neighborhood
   - Year Built
   - Bathrooms
   - Bedrooms

2. **Market Volatility**:
   - Average appreciation rate: ~1.54% per year
   - Average depreciation rate: ~-1.24% per year

3. **Price Predictions**: The model generates both static and volatility-adjusted forecasts for 2020-2050

## 📝 Analysis Workflow

1. **Data Loading & Exploration**: Import dataset and generate summary statistics
2. **Trend Analysis**: Visualize price trends by year and neighborhood
3. **Outlier Detection**: Identify anomalous prices using statistical methods
4. **Regression Modeling**: Build linear model to understand feature importance
5. **Rate Calculation**: Compute historical appreciation/depreciation rates
6. **Future Prediction**: Generate price forecasts with realistic volatility

## 🤝 Contributing

Contributions are welcome! Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

## 📄 License

This project is open source and available under the [MIT License](LICENSE).



## 🙏 Acknowledgments

- Dataset source: [housing_price_dataset.csv](housing_price_dataset.csv)
- Inspired by real-world housing market analysis needs
- Built with R and the tidyverse ecosystem

---

**Note**: This project was created for educational/analytical purposes. Predictions should not be used as sole basis for financial decisions.
