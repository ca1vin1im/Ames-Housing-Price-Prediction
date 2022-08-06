# ![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) Project 2: Ames Housing Pricing Prediction

## Background

Properties such as houses are amongst an individual's or a family's major financial committment and investment. It also signifies the attainment of a major milestone in life. Since sellers would aim to maximise sales price while buyers would aim to minimse sales price, most Ames homeowners would desire knowing the fair value of their property and ways to maximise their home sales prices. Additionally, opportunities are ample for homeowners to capitalize on potential asymmetries in information availability.

As your data-driven property consultant in Ames Iowa, I endeavour to build a house price prediction model using historical property transactions from Ames Iowa and subsequently advise you on both the property's fair value and the maximum historically transacted prices by neighbourhoods in Ames Iowa so you could make informed decisions.

---

## Problem Statement

Develop **3 models** (Linear Regression, Lasso Regression, and Ridge Regression) based on the **Ames Housing Dataset**, narrowing down to **1 production model** based on the **lowest [Root Mean Square Error (RMSE)](https://en.wikipedia.org/wiki/Root-mean-square_deviation)** to predict house sales prices for Ames homeowners and advice ways to maximise sales prices as your data-driven property consultant.

### Dataset Used
Data set containing information from the Ames, Iowa Assessor’s Office will used in computing assessed values for individual residential properties sold in Ames, IA from 2006 to 2010.

- **`train.csv`**: 2051 house transactions with 81 house features comprising 22 nominal (categorical features with no particular order), 24 ordinal (categorical features with ordering), 15 discrete (countable numerical features) and 20 continuous variables (uncountably infinite numerical features)
- **`test.csv`**: 878 properties with 80 house features for house price prediction

Refer to the [data dictionary](http://jse.amstat.org/v19n3/decock/DataDocumentation.txt) for details.

---

## Data Cleaning, Feature Engineering, and Exploratory Data Analysis (EDA)

During data cleaning, feature engineering, and Exploratory Data Analysis (EDA), the following steps were taken:

 - **Data Cleaning**
     - Check for duplicated rows
     - Check for appropriate datatypes
     - Initial check for missing/null values
     - Drop rows with 1 or 2 missing/null values
     - Check for missing/null values
     - Check for obvious outliers
     - Impute missing/null values
 - **Feature Engineering**
     - Total property area in square feet [**`'total_pty_sf'`**]
     - Age of garage at year of sale [**`'garage_age'`**]
     - Age of house at year of sale [**`'house_age'`**]
 - **Exploratory Data Analysis (EDA): Numerical Variables**
     - Correlation Analysis (I)
 - **Exploratory Data Analysis (EDA): Categorical Variables**
     - Preprocess ordinal categorical variables — Integer Encoding
     - Correlation Analysis (II)
     - Preprocess nominal categorical variables — Dummy Variable Encoding

### Interesting Finding from EDA of Categorical Variables

With reference to the following count and bar plots, it is reasonably expected for properties having the best overall materials and finishes (i.e. highest Overall Quality of 10) to command the highest sale price, ceteris paribas:

![Overall Quality Count & Box Plots](../media/Overall Quality Count & Box Plots.png)

In contrast, **no** properties was given the highest Overall Condition rating of **10**, properties having Overall Condition rating of **5** commanded highest sale price, and properties having Overall Condition rating of **6** to **9** commanded increasingly lower sale prices:

![Overall Condition Count & Box Plots](../media/Overall Condition Count & Box Plots.png)

A possible explanation is that it is easier for one to assess more objective measures such as materials and finishes of properties. Comparatively, it is much harder for one to assess more subjective measures such as the overall condition of properties.

### Correlation Analysis

![Heatmap Stronger Than ±0.7](../media/Heatmap Stronger Than ±0.7.png)

Referencing the above heatmap, given that several features such as **`'overall_qual'`** (0.8033), **`'exter_qual'`** (0.7143), and **`'total_pty_sf'`** (0.8541) have relative high correlation of **at least 0.71** with **`'saleprice'`**, we could potentially derive at a model that could predict housing prices at Ames, Iowa.

---

## Modeling

I trained the Train dataset on the following 3 models, using cross validation folds = 10 for consistency:
 - Ordinary Least Squares (OLS) Model [**`Baseline Model`**]
 - Lasso Regression Model
 - Ridge Regression Model

### Ordinary Least Squares (OLS) Model [`Baseline Model`]

The OLS Regression Mdel is chosen as the baseline model. Unfortunately, it has extremely high RMSE and thus will not be a recommended model.

### Lasso Regression Model

Upon calculating the Lasso Regression Model's optimal alpha, it was used in fitting the model on the Training dataset.

The plot of Predicted Sale Price vs Actual Sale Price using the Lasso Regression Model is shown below:

![Predicted vs Actual Sale Prices (Lasso)](../media/Predicted vs Actual Sale Prices (Lasso).png)

### Ridge Regression Model

Similarly, upon calculating the Ridge Regression Model's optimal alpha, it was used in fitting the model on the Training dataset.

The plot of Predicted Sale Price vs Actual Sale Price using the Ridge Regression Model is shown below:

![Predicted vs Actual Sale Prices (Ridge)](../media/Predicted vs Actual Sale Prices (Ridge).png)

### Summary of Regression Results

Both the rounded Validation R² and Root Mean Squared Error (RMSE) for the 3 models are summarised below:

| **Model**                    | **Validation R²** | **Validation RMSE** | **Remarks**                      |
|------------------------------|:-----------------:|:-------------------:|----------------------------------|
| Ordinary Least Squares (OLS) |      Negative     |      Too Large      | Baseline, Worst Performing Model |
| Lasso Regression             |      0.898834     |     24344.481732    | Best Performing Model            |
| Ridge Regression             |      0.898019     |     24442.337721    | Second Best Performing Model     |

Given the extremely large error values for both validation R² and validation RMSE, the OLS model will not be a candidate model.

Since the Lasso Regression model has both a higher validation R² and a lower validation RMSE than the Ridge Regression model, the **`Lasso Regression model`** is chosen as the production model.

### Residual Analysis for Lasso Regression Model

The plot of Lasso Residuals vs Predicted Sale Price using the Lasso Regression Model is as follows:

![Lasso Residuals vs Predicted Sale Prices](../media/Lasso Residuals vs Predicted Sale Prices.png)

- A well-behaved residuals vs. predictor will bounce randomly and form a roughly horizontal band around the residual = 0 line, and no data points will stand out from the basic random pattern of the other residuals, i.e. **homoscedastic** or **homogeneity of variance**.
- The points are generally well-behaved within a ± USD 50,000 horizontal band for Predicted Sale Price up to approximately USD 300,000 before starting to form a funnel-like shape with the funnel's broader opening on the right as Predicted Sale Price increases. The occurrence of this funnel shape indicates [**heteroskedasticity** (or **heteroscedasticity**)](https://en.wikipedia.org/wiki/Homoscedasticity_and_heteroscedasticity), i.e. variance of Lasso Residuals increase as Predicted Sale Price increases.  
- The histogram for Predicted Sale Price has a positively- or right-skewed distribution (i.e. long right tail) whereas that for Lasso Residuals has a distribution of approximately zero skewness, with most of the points concentrating around the origin (0,0).
- All these imply the Lasso Regression model's greater accuracy in predicting lower priced property sales. The issue could potentially be alleviated as more data points of larger property sales are captured and included to further refine the model.

Upon retraining the Lasso Regression Model on the Combined Train & Validation datasets, the updated R² and RMSE are 0.917846 and 24803.736226 respectively.

---

## Top Factors with greatest impact on Sale Price

The top 40 Lasso variables having the greatest impact on `'saleprice'` [Sale Price] are as follows:

![Top 40 Lasso Regression Coefficients](../media/Top 40 Lasso Regression Coefficients.png)

- More specifically, the top 5 Lasso variables having the greatest impact on `'saleprice'` [Sale Price] and their corresponding coefficients are as follows:
  - `'total_pty_sf'` [Total Property Area in Square Feet]: **31107.75**
  - `'overall_qual'` [Overall Material and Finish Quality of House]: **14336.84**
  - `'ms_subclass_20'` [1-Story 1946 & Newer All Styles House]: **7535.86**
  - `'bsmtfin_sf_1'` [Type 1 Finished Square Feet]: **7349.48**
  - `'neighborhood_StoneBr'` [Stone Brook Neighborhood in Ames, Iowa]: **7038.89**
- Variables such as `'house_age'` [Age of House at Year of Sale] are reasonably expected to be negatively correlated with `'saleprice'` [Sale Price] since most buyers prefer and are more willing to pay a premium on newer properties.

---

## Kaggle Submission

After repeating similar data cleaning steps on the Test dataset, the predicted sale prices are submitted to Kaggle and the score is as follows: 

![Kaggle Submission 02Aug2022 1746HRS](../media/Kaggle Submission 02Aug2022 1746HRS.png)

---

## Recommendations, Next Steps and Conclusion

With the aim of helping Ames homeowners determine the fair value of your property and identify ways to maximise your home sales prices, I cleaned, explored and analysed Ames Housing Dataset to develop 3 models (Linear Regression, Lasso Regression, and Ridge Regression), narrowing down to 1 production model (Lasso Regression) based on the lowest Root Mean Square Error (RMSE) to predict house sales prices for Ames homeowners and advice ways to maximise sales prices.

This Lasso Regression house sales prices model predicts the fair value of your Ames home. Additionally, with reference to the above table, I provided the maximum sales price for each Ames neighbourhood so you know the reasonable upper limit of the value of your priced home. 

I also identified the **top 40 factors** with the greatest impact on Ames home sales prices, with the top 5 listed below:
  - **`'total_pty_sf'`**: Total Property Area in Square Feet
  - **`'overall_qual'`**: Overall Material and Finish Quality of House
  - **`'ms_subclass_20'`**: 1-Story 1946 & Newer All Styles House
  - **`'bsmtfin_sf_1'`**: Type 1 Finished Square Feet
  - **`'neighborhood_StoneBr'`**: Stone Brook Neighborhood in Ames, Iowa

Top 3 tips for enhancing your Ames home sales value:
  1. Maximise your total property area (i.e. Basement, above grade (ground) living area, garage, porch, pool, etc.)
  2. Maintain good / excellent quality of house thru regular maintenance and upkeep of interior / exterior features
  3. Apply excellent finishing for all areas of your property including the basement

### Next Steps

With your continual strong support I aim to further enhance the robustness of this Lasso pricing model by:
- Exploring other pricing models;
- Continually updating both the data and model to ensure continually accuracy of home sales price and relevancy of strategic and tactic advice I provide to Ames homeowners;   
- Taking into consideration both macro- and micro-economic factors in my pricing model e.g. mortgage rates, crime rates, COVID-19, etc;
- etc.

## Conclusion

I explored 3 and narrowed down to 1 production model (Lasso) based on the lowest Root Mean Square Error (RMSE) to predict house sales prices for Ames homeowners and advice ways to maximise sales prices as your data-driven property consultant. 

I also shared the maximum sales price by neighbourhood, top 5 factors with the greatest impact on Ames home sales prices, and top 3 tips for enhancing your Ames home sales value.

With your continual strong support I aim to further enhance the robustness of this pricing model and provide you with continual data-backed advice that maximises the sales value of your priced home.