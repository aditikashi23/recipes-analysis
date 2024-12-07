# Recipes and Ratings

by Aditi Kashi and Anish Kudupudi

---

## Introduction

The dataset analyzed comes from Food.com, a platform where users share recipes and rate their cooking experiences. It consists of two components: **recipes** and **user interactions**. The recipes dataset details attributes such as cooking time, nutritional values, and step-by-step instructions. The interactions dataset captures user reviews and ratings for these recipes.

The key question guiding this analysis (Parts 1 and 2) is:

What is the relationship between the cooking time and average rating of recipes?

This question is relevant to both recipe creators and home cooks. For creators, understanding how cooking time influences user satisfaction can help optimize recipes for better engagement. For home cooks, knowing whether longer preparation times lead to better-rated recipes can help them balance time and quality in meal planning.

There are 82909 rows and 13 columns in this dataset, after merging the recipes and review datasets together. 

The column and descriptions for the relevant columns of the recipes dataset are as follows:


| Column     |   Description |
|:------------|--------:|
| minutes |       Minutes to prepare recipe |
| nutrition |       Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| n_steps |       Number of steps in recipe |

The column and descriptions for the reviews dataset are as follows:

| Column     |   Description |
|:------------|--------:|
| rating |       Rating given |


---

## Data Cleaning and Exploratory Data Analysis

 **Step 1: Left merge the recipes and interactions datasets**

Process: The recipes dataset, containing information about recipes (e.g., ingredients, cooking times), was merged with the interactions dataset, which stores user feedback such as ratings and comments. The merge was performed using a left join on id (recipe identifier) in recipes and recipe_id in interactions. This ensured all recipes were retained, even if they had no associated interactions.

Rationale: This step aligned user-generated interactions with their corresponding recipes, enabling the inclusion of ratings data in the recipe dataset.

Impact: Recipes without user interactions had missing values for ratings. This step created a unified dataset that combined recipe attributes and user feedback, making it easier to analyze the relationship between recipe features and ratings.

**Step 2: Fill all ratings of 0 with np.nan**

Process: Ratings of 0 in the dataset were replaced with np.nan.

Rationale: A rating of 0 likely indicated missing or invalid data rather than an actual evaluation of the recipe. Replacing 0 with np.nan ensured these values were excluded from calculations (e.g., averages).

Impact: This step prevented erroneous ratings from skewing the analysis, particularly when calculating average ratings. Treating these values as missing ensured a more accurate representation of user feedback.

**Step 3: Find the average rating per recipe**

Process: The average rating for each recipe was calculated by grouping the merged dataset by id (recipe identifier) and taking the mean of the rating column.

Rationale: Recipes can have multiple ratings from different users. Computing an average rating provided a single, aggregated measure of user sentiment for each recipe.

Impact: The aggregated rating simplified downstream analysis and allowed for easier comparisons between recipes based on user feedback.

**Step 4: Add the average rating back to the recipes dataset**

Process: The calculated average ratings were merged back into the recipes dataset as a new column, average_rating. A left join ensured all recipes were included, even those without ratings.

Rationale: This integration enriched the recipes dataset with user feedback while preserving the original recipe attributes.

Impact: Recipes with no ratings had NaN in the average_rating column. Including average ratings in the recipes dataset made it easier to analyze how recipe features (e.g., cooking time, ingredients) influenced user satisfaction.

**Step 5: Remove recipes with cook times greater than six months**

Process: Recipes with cooking times (minutes) greater than 720 (equivalent to six months) were removed from the dataset.

Rationale: Extremely high cooking times are likely outliers or data entry errors (e.g., minutes recorded in hours or days). Retaining these entries could distort analyses or mislead conclusions. The value of six months was chosen specifically because the only recipes past six months were either not recipes at all (i.e. how to preserve a husband) or involved pickling/brewing, which can be done for a hypothetically indefinite amount of time.

Impact: This step ensured a more realistic dataset, free from implausible outliers. It improved the quality of analyses by focusing on recipes that users are more likely to attempt and rate.

**Step 6: Modify the nutrition column to extract all values into individual columns**

Process: The nutrition column was processed using string functions to extract the values for calories, percent daily fat, sugar, and more.

Rationale: These values are going to be used in further stages of analysis, so it is required that they are in their own columns and processed into numerical values.

Impact: This helped turn the nutrition column into appropriate columns for regression tasks.

Here are the first five rows of the dataset post-cleaning. Some values have been truncated to adhere to proper formatting:

| name                                                |     id |   minutes |   contributor_id | submitted   | tags                                                       | nutrition                                     |   n_steps | steps                                                 | description                                                       | ingredients                                                    |   n_ingredients |   average_rating |   calories |   total_fat_pdv |   sugar_pdv |   sodium_pdv |   protein_pdv |   saturated_fat_pdv |   carbohydrates_pdv | cooking_time_range   |   avg_ingredients_per_step |   log_n_steps |   interaction |   log_minutes |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:------------------------------------------------------|:----------------------------------------------|----------:|:------------------------------------------------------|:------------------------------------------------------|:------------------------------------------------------|----------------:|-----------------:|-----------:|----------------:|------------:|-------------:|--------------:|--------------------:|--------------------:|:---------------------|---------------------------:|--------------:|--------------:|--------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', '... | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 | ['heat the oven to 350f and arrange the rack in th... | these are the most; chocolatey, moist, rich, dense... | ['bittersweet chocolate', 'unsalted butter', 'eggs... |               9 |                4 |      138.4 |              10 |          50 |            3 |             3 |                  19 |                   6 | 30-60 min            |                   0.9      |       2.3979  |            90 |       3.71357 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', ... | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing b... | this is the recipe that we use at my school cafete... | ['white sugar', 'brown sugar', 'salt', 'margarine'... |              11 |                5 |      595.1 |              46 |         211 |           22 |            13 |                  51 |                  26 | 30-60 min            |                   0.916667 |       2.56495 |           132 |       3.82864 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', '... | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart b... | since there are already 411 recipes for broccoli c... | ['frozen broccoli cuts', 'cream of chicken soup', ... |               9 |                5 |      194.8 |              20 |           6 |           32 |            22 |                  36 |                   3 | 30-60 min            |                   1.5      |       1.94591 |            54 |       3.71357 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation... | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 | ['freheat the oven to 300 degrees', 'grease a 10-i... | why a millionaire pound cake?  because it's super ... | ['butter', 'sugar', 'eggs', 'all-purpose flour', '... |               7 |                5 |      878.3 |              63 |         326 |           13 |            20 |                 123 |                  39 | 1-2 hrs              |                   1        |       2.07944 |            49 |       4.79579 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'pre... | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 | ['pan fry bacon , and set aside on a paper towel t... | ready, set, cook! special edition contest entry: a... | ['meatloaf mixture', 'unsmoked bacon', 'goat chees... |              13 |                5 |      267   |              30 |          12 |           12 |            29 |                  48 |                   2 | 1-2 hrs              |                   0.764706 |       2.89037 |           221 |       4.51086 |

Below is a histogram of cooking times. The histogram visualizes the distribution of cooking times for recipes in the dataset. Most recipes have short cooking times, with the majority falling under 100 minutes, suggesting that users prefer recipes with faster preparation times. This trend indicates that future analysis should focus on how average ratings vary within this dominant range to explore the relationship between cooking time and user satisfaction.

<iframe
  src="assets/cook_time_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Below is a scatterplot of cooking time and average recipe ratings. The scatterplot shows the relationship between cooking time and average recipe ratings. While there is no strong correlation, recipes with shorter cooking times appear to have a dense concentration of high ratings, suggesting that quick recipes are more common  among users. Longer cooking times do not seem to significantly impact ratings but are less common. There appear to be lines around 1, 2, 3, 4, and 5 since many recipes have only one rating, which results in a whole number average rating.

<iframe
  src="assets/rating_time_scatter.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Below is a grouped table of cooking time and average rating. This is helpful to our analysis because it helps visualize how the average rating changes as the cooking time increases. There doesn't appear to be much of a change until the "Over 5 hours" category, where there is a drop from 4.62 to 4.52. This could be simply because there are less recipes in that cooking time range.

| cooking_time_range     |   average_rating |
|:------------|--------:|
| 0-10 min   |        4.69 |
| 10-30 min  |      4.63|
| 30-60 min  |       4.61 |
| 1-2 hrs    |        4.63|
| 2-5 hrs    |      4.62|
| Over 5 hrs    |     4.52|


---

## Framing a Prediction Problem

The prediction problem is a regression task, where the goal is to predict the cooking time (minutes) for a recipe based on other numerical features, such as the number of steps and the number of ingredients. Regression is appropriate because the response variable is continuous, representing time in minutes.

**Response Variable: minutes**
Reason for Choosing It: Cooking time is a critical aspect of a recipe, influencing its practicality and appeal. Predicting it based on recipe complexity and ingredients can offer valuable insights to home cooks or recipe platforms.

**Chosen Metric: R² (Coefficient of Determination)**
Reason: R² evaluates how well the independent variables (features) explain the variance in the dependent variable (response). This makes it ideal for regression tasks where the aim is to maximize the proportion of variance explained by the model.
A high R² value indicates a good fit, meaning the model accurately predicts cooking time for a range of recipes.

**Why Not Other Metrics?**
Mean Squared Error (MSE):
While MSE is useful to measure error magnitude, it is not as intuitive as R² for interpreting model performance.
Mean Absolute Error (MAE):
MAE measures average prediction error, but it does not provide insight into how well the model captures variance in the target variable.

**Selected Features**
Number of Steps (n_steps):
Why It's Known: The number of steps is part of the recipe design, which is available before cooking starts. It does not depend on the outcome or performance of the recipe and is static information available during recipe submission or retrieval.
Number of Ingredients (n_ingredients):
Why It's Known: The ingredient count is derived from the recipe's ingredient list, which is also finalized and available before cooking begins. This information is inherently part of the recipe structure.
Various Nutrition Facts (calories, sugar_pdv, total_fat_pdv, carbohydrates_pdv, protein_pdv):
Why It's Known: These facts can be calculated through the ingredients of any recipe, which is known.

---

## Baseline Model

The baseline model is a **Linear Regression** model implemented using the `LinearRegression` class from `sklearn`. It was chosen because it provides a straightforward method to evaluate the linear relationship between the selected features and the target variable, `minutes`.

### **Features in the Model**
1. **Quantitative Features (8)**:
   - **`n_steps`**: Represents the number of steps in a recipe, inherently numerical and continuous.
   - **`n_ingredients`**: Denotes the number of ingredients used in the recipe, also a numerical and continuous feature.
   - **`average_rating`**: Denotes the average rating given to a recipe.
   - **`calories`**: Denotes the calories in a given food item.
   - **`sugar_pdv`**: Denotes the percentage of sugar for daily value consumption.
   - **`total_fat_pdv`**: Denotes the percentage of total fat for daily value consumption.
   - **`carbohydrates_pdv`**: Denotes the percentage of carbohydrates for daily value consumption.
   - **`protein_pdv`**: Denotes the percentage of protein for daily value consumption.
     
2. **Ordinal Features**: None.

3. **Nominal Features**: None.

4. **Encodings and Transformations**:
   - No encoding was required since all features were quantitative.
   - No scaling or normalization was applied because scaling has no effect on predictions for a simple linear regression model.

### **Model Performance**
1. **Training R² Score**: **0.0609**
   - Indicates that the model explains only 6.09% of the variance in the training data.
   
2. **Testing R² Score**: **0.0516**
   - Shows that the model performs similarly on unseen data, explaining 5.16% of the variance.

3. **Predictions**: The model’s predictions are close to the mean of the `minutes` column, indicating a limited ability to capture relationships between features and the target.

### **Evaluation of Model Quality**
- **Is the Model "Good"?**
  - No, the current model is not good. A low R² score suggests that the model fails to explain the variability in the cooking time (`minutes`). This implies that `n_steps` and `n_ingredients` alone are insufficient to predict the target variable effectively.


---

## Final Model

**Features Added and Justification**

In the final model, we engineered two new features:

Interaction Term (n_steps * n_ingredients):
This feature captures the interplay between the number of steps and the number of ingredients in a recipe. It is logical to assume that recipes with a high number of steps and ingredients are more complex and, consequently, might take longer to prepare. Adding this interaction term allows the model to account for such combined effects that the baseline model missed.
Log Transformation of the Target (minutes):
The target variable minutes was log-transformed to handle the positive skew in the data distribution. Many recipes likely have short cooking times, with fewer instances of recipes taking significantly longer, creating a right-skewed distribution. By applying the log transformation, we make the data more normally distributed, which is favorable for linear regression models that assume residuals are normally distributed. This transformation also helps reduce the influence of outliers on model performance.
These features were chosen based on their relevance to the cooking process and their ability to better represent the underlying data-generating process, rather than through trial-and-error improvements to performance.

**Tranformers and Scalers**

QuantileTransformer for calories and sugar_pdv: The best quantile range was selected based on its ability to make the distributions closer to uniform.
StandardScaler for the interaction term: Ensures the interaction term is scaled appropriately alongside other features.

**Modeling Algorithm and Hyperparameters**

**Why Random Forest Was Tried Alongside Linear Regression**

While linear regression is a straightforward and interpretable algorithm, it makes several assumptions about the data, including linear relationships between features and the target variable, and normally distributed residuals. However, real-world data often violates these assumptions. Given this, I also experimented with Random Forest Regressor as part of the modeling process for the following reasons:

Handling Nonlinear Relationships:
Random forests are non-parametric models that can capture complex, nonlinear relationships between features and the target variable. In the context of predicting minutes, there may be intricate interactions between variables like calories, n_steps, and n_ingredients that are hard for linear regression to capture.
Feature Importance Analysis:
Random forests naturally rank the importance of features. This helped assess whether the engineered features, like the interaction term (n_steps * n_ingredients), were meaningful contributors to the model's predictions. This insight was useful for both the random forest model and to inform future iterations of feature selection for the linear regression model.
Robustness to Outliers:
Random forests are less sensitive to outliers compared to linear regression. This made it a good candidate for exploring whether the outliers in minutes (e.g., recipes with extremely long cooking times) were disproportionately affecting model performance.

**GridSearchCV**

For the final model, GridSearchCV systematically explored combinations of hyperparameters that are crucial for tuning the Random Forest Regressor.

Explanation of Each Parameter
n_estimators:
Definition: This controls the number of decision trees in the Random Forest.
Values Tried: [50, 100, 200].
Reason for Selection: Increasing the number of trees generally improves model performance by reducing variance, but at the cost of increased computation time. Testing a range of values helps find the trade-off between performance and efficiency.
max_depth:
Definition: This sets the maximum depth of each decision tree.
Values Tried: [5, 10, 20, None].
Reason for Selection: Limiting tree depth helps prevent overfitting by restricting how complex the trees can become. A smaller depth ensures the trees capture the general trends in the data, while deeper trees allow the model to capture finer details. The None option allows trees to grow until they are fully expanded.
Grid Search Process

The best parameters were n_estimators = 200 and max_depth = 10.

**Final Model Performance**

**Training R² Score: 0.4056**
**Test R² Score: 0.3256**

The final model showed a significant improvement in both training and testing, indicating that the engineered features and transformations allowed the model to better explain the variance in the data.

**Why the Final Model Improved**

The improved performance can be attributed to:

Feature Engineering: The interaction term allowed the model to capture nuanced relationships between the number of steps and ingredients, which are jointly correlated with cooking time. The log transformation stabilized the variance in the target variable, improving prediction accuracy.
Data Transformation: Using QuantileTransformer and StandardScaler ensured the numerical features were on comparable scales, improving the stability of the linear regression model.
Hyperparameter Tuning: GridSearchCV helped identify optimal parameters for transformations, ensuring each feature contributed effectively to the model.
Overall, the final model's performance improvement demonstrates the importance of feature engineering and data transformations in addressing the limitations of a basic linear regression model when applied to complex datasets like recipe preparation times.


---
