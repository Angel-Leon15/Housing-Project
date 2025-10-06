# Contributing to Housing Market Analysis

Thank you for your interest in contributing to this project! This document provides guidelines and instructions for contributing.

##  Ways to Contribute

There are many ways you can contribute to this project:

- **Bug Reports**: Report issues or unexpected behavior
- **Feature Requests**: Suggest new analyses or visualizations
- **Code Contributions**: Submit bug fixes or new features
- **Documentation**: Improve documentation, add examples, or fix typos
- **Data**: Suggest additional datasets or data sources
- **Testing**: Test the analysis with different datasets

##  Reporting Bugs

Before creating a bug report, please check existing issues to avoid duplicates.

### How to Submit a Bug Report

1. **Use a clear and descriptive title**
2. **Describe the exact steps to reproduce the problem**
3. **Provide specific examples** (code snippets, data samples)
4. **Describe the behavior you observed and expected**
5. **Include your environment details**:
   - R version
   - Operating system
   - Package versions
   - Dataset characteristics (size, structure)

### Example Bug Report

```markdown
**Title**: Error when processing neighborhoods with special characters

**Description**: 
The analysis crashes when the Neighborhood column contains special characters like "&" or "/".

**Steps to Reproduce**:
1. Load dataset with neighborhood names containing "&"
2. Run the neighborhood price trend analysis chunk
3. Error occurs at line X

**Expected**: Should handle special characters gracefully
**Actual**: Error: "invalid character in factor level"

**Environment**:
- R version: 4.2.1
- OS: Windows 11
- dplyr version: 1.1.0
```

## Suggesting Enhancements

Enhancement suggestions are tracked as GitHub issues. When creating an enhancement suggestion:

1. **Use a clear and descriptive title**
2. **Provide a detailed description** of the suggested enhancement
3. **Explain why this enhancement would be useful**
4. **Include examples** of how it would work
5. **List any alternatives** you've considered

### Enhancement Ideas

- Additional statistical tests (e.g., ANOVA for neighborhoods)
- Time series forecasting models (ARIMA, Prophet)
- Interactive visualizations using plotly
- Feature engineering (price per square foot, age of property)
- Model comparison (Random Forest, XGBoost)
- Cross-validation and model evaluation metrics
- Spatial analysis if geographic coordinates available

##  Code Contributions

### Getting Started

1. **Fork the repository**
2. **Clone your fork**
   ```bash
   git clone https://github.com/your-username/housing-market-analysis.git
   ```
3. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```
4. **Make your changes**
5. **Test your changes** thoroughly
6. **Commit your changes**
   ```bash
   git commit -m "Add: brief description of changes"
   ```
7. **Push to your fork**
   ```bash
   git push origin feature/your-feature-name
   ```
8. **Open a Pull Request**

### Coding Standards

#### R Code Style

Follow the [tidyverse style guide](https://style.tidyverse.org/):

- **Naming**: Use snake_case for variables and functions
  ```r
  # Good
  housing_market <- read_csv("data.csv")
  calculate_avg_price <- function(data) { ... }
  
  # Avoid
  HousingMarket <- read_csv("data.csv")
  CalculateAvgPrice <- function(data) { ... }
  ```

- **Spacing**: Use spaces around operators
  ```r
  # Good
  x <- 5 + 3
  
  # Avoid
  x<-5+3
  ```

- **Line Length**: Keep lines under 80 characters
- **Comments**: Use comments to explain "why", not "what"
  ```r
  # Calculate year-over-year change to identify market volatility
  percent_change <- (price_new - price_old) / price_old * 100
  ```

#### Code Organization

- **Modular**: Break complex operations into functions
- **DRY Principle**: Don't repeat yourself - create reusable functions
- **Clear Variable Names**: Use descriptive names
- **Error Handling**: Add checks for edge cases

Example:
```r
# Good: Modular and clear
calculate_appreciation_rate <- function(price_data) {
  if (nrow(price_data) < 2) {
    stop("Insufficient data for appreciation calculation")
  }
  
  price_data %>%
    arrange(YearBuilt) %>%
    mutate(percent_change = (avg_price - lag(avg_price)) / lag(avg_price) * 100) %>%
    filter(percent_change > 0) %>%
    summarise(avg_appreciation = mean(percent_change, na.rm = TRUE))
}
```

### Documentation Standards

- **Function Documentation**: Use roxygen2 style comments
  ```r
  #' Calculate Average Price by Year
  #'
  #' @param data A dataframe containing housing data
  #' @param year_col Name of the year column (default: "YearBuilt")
  #' @return A dataframe with years and average prices
  #' @examples
  #' yearly_avg <- calculate_yearly_average(housing_data)
  calculate_yearly_average <- function(data, year_col = "YearBuilt") {
    # Function implementation
  }
  ```

- **Code Comments**: Explain complex logic
- **README Updates**: Update README.md if adding new features
- **Methodology Updates**: Document new analytical approaches in METHODOLOGY.md

### Testing

Before submitting a pull request:

1. **Test with multiple datasets** if possible
2. **Check for edge cases**:
   - Empty datasets
   - Missing values
   - Extreme outliers
   - Single neighborhood
   - Limited year range
3. **Verify visualizations** render correctly
4. **Run the full R Markdown** document to ensure it knits without errors

##  Pull Request Process

1. **Update documentation** (README, METHODOLOGY) if needed
2. **Ensure your code follows** the style guidelines
3. **Test thoroughly** before submitting
4. **Write a clear PR description**:
   - What changes were made
   - Why they were made
   - How to test them
   - Any breaking changes
5. **Link related issues** using keywords (e.g., "Fixes #123")
6. **Be responsive** to feedback and review comments

### Pull Request Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Code refactoring
- [ ] Performance improvement

## Testing
Describe how you tested your changes

## Checklist
- [ ] Code follows style guidelines
- [ ] Documentation updated
- [ ] Tested with sample data
- [ ] R Markdown knits successfully
- [ ] No breaking changes (or documented if present)
```

## 🎯 Priority Areas

We're particularly interested in contributions in these areas:

1. **Model Improvements**: Alternative prediction models, ensemble methods
2. **Visualization Enhancements**: Interactive plots, better color schemes
3. **Performance Optimization**: Handling large datasets efficiently
4. **Feature Engineering**: Additional derived features
5. **Statistical Validation**: Cross-validation, residual analysis
6. **Error Handling**: Robust error checking and informative messages

## Resources

- [R for Data Science](https://r4ds.had.co.nz/)
- [Tidyverse Style Guide](https://style.tidyverse.org/)
- [Git Tutorial](https://git-scm.com/docs/gittutorial)
- [Writing Good Commit Messages](https://chris.beams.io/posts/git-commit/)

##  Questions?

If you have questions about contributing:
- Open an issue with the "question" label
- Check existing issues and discussions
- Reach out via email [leon.angelf15@gmail.com]

## Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inclusive environment for all contributors, regardless of experience level, background, or identity.

### Expected Behavior

- Be respectful and constructive in communication
- Accept feedback gracefully
- Focus on what's best for the project
- Show empathy toward others

### Unacceptable Behavior

- Harassment or discriminatory language
- Personal attacks or trolling
- Publishing others' private information
- Other conduct that could reasonably be considered inappropriate

## Thank You!

Your contributions help make this project better for everyone. We appreciate your time and effort!

---

*This contributing guide is adapted from open source best practices and can be modified as the project evolves.*
