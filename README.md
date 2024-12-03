# Recipes and Ratings

by Aditi Kashi and Anish Kudupudi

---

## Introduction

The dataset analyzed comes from Food.com, a platform where users share recipes and rate their cooking experiences. It consists of two components: **recipes** and **user interactions**. The recipes dataset details attributes such as cooking time, nutritional values, and step-by-step instructions. The interactions dataset captures user reviews and ratings for these recipes.

The key question guiding this analysis (Parts 1 and 2) is:

What is the relationship between the cooking time and average rating of recipes?

This question is relevant to both recipe creators and home cooks. For creators, understanding how cooking time influences user satisfaction can help optimize recipes for better engagement. For home cooks, knowing whether longer preparation times lead to better-rated recipes can help them balance time and quality in meal planning.

There are 82909 rows and 13 columns in this dataset, after merging the recipes and review datasets together. 

The column and descriptions for the recipes dataset are as follows:


| Column     |   Description |
|:------------|--------:|
| name   |       Recipe name |
| id |       Recipe ID |
| minutes |       Minutes to prepare recipe |
| contributor_id |       User ID who submitted this recipe |
| submitted   |      Date recipe was submitted |
| tags   |       Food.com tags for recipe |
| nutrition |       Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| n_steps |       Number of steps in recipe |
| steps |       Text for recipe steps, in order |
| description   |      User-provided description |

The column and descriptions for the reviews dataset are as follows:

| Column     |   Description |
|:------------|--------:|
| user_id   |       User ID |
| recipe_id |       Recipe ID |
| date |       Date of interaction |
| rating |       Rating given |
| review   |      Review text |


---

## Data Cleaning and Exploratory Data Analysis

Step 1: Left merge the recipes and interactions datasets

Process: The recipes dataset, containing information about recipes (e.g., ingredients, cooking times), was merged with the interactions dataset, which stores user feedback such as ratings and comments. The merge was performed using a left join on id (recipe identifier) in recipes and recipe_id in interactions. This ensured all recipes were retained, even if they had no associated interactions.

Rationale: This step aligned user-generated interactions with their corresponding recipes, enabling the inclusion of ratings data in the recipe dataset.

Impact: Recipes without user interactions had missing values for ratings. This step created a unified dataset that combined recipe attributes and user feedback, making it easier to analyze the relationship between recipe features and ratings.

Step 2: Fill all ratings of 0 with np.nan

Process: Ratings of 0 in the dataset were replaced with np.nan.

Rationale: A rating of 0 likely indicated missing or invalid data rather than an actual evaluation of the recipe. Replacing 0 with np.nan ensured these values were excluded from calculations (e.g., averages).

Impact: This step prevented erroneous ratings from skewing the analysis, particularly when calculating average ratings. Treating these values as missing ensured a more accurate representation of user feedback.

Step 3: Find the average rating per recipe

Process: The average rating for each recipe was calculated by grouping the merged dataset by id (recipe identifier) and taking the mean of the rating column.

Rationale: Recipes can have multiple ratings from different users. Computing an average rating provided a single, aggregated measure of user sentiment for each recipe.

Impact: The aggregated rating simplified downstream analysis and allowed for easier comparisons between recipes based on user feedback.

Step 4: Add the average rating back to the recipes dataset

Process: The calculated average ratings were merged back into the recipes dataset as a new column, average_rating. A left join ensured all recipes were included, even those without ratings.

Rationale: This integration enriched the recipes dataset with user feedback while preserving the original recipe attributes.

Impact: Recipes with no ratings had NaN in the average_rating column. Including average ratings in the recipes dataset made it easier to analyze how recipe features (e.g., cooking time, ingredients) influenced user satisfaction.

Step 5: Remove recipes with cook times greater than six months

Process: Recipes with cooking times (minutes) greater than 720 (equivalent to six months) were removed from the dataset.

Rationale: Extremely high cooking times are likely outliers or data entry errors (e.g., minutes recorded in hours or days). Retaining these entries could distort analyses or mislead conclusions. The value of six months was chosen specifically because the only recipes past six months were either not recipes at all (i.e. how to preserve a husband) or involved pickling/brewing, which can be done for a hypothetically indefinite amount of time.

Impact: This step ensured a more realistic dataset, free from implausible outliers. It improved the quality of analyses by focusing on recipes that users are more likely to attempt and rate.

Here are the first five rows of the dataset post-cleaning:

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                    | nutrition                                     |   n_steps | steps                                                                                                   | description                                                                                             | ingredients                                                                                             |   n_ingredients |   average_rating |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:--------------------------------------------------------------------------------------------------------|:----------------------------------------------|----------:|:--------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------|----------------:|-----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups... | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]      |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish ... | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.... | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder',... |               9 |                4 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups... | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]  |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder',... | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the b... | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour',... |              11 |                5 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'v... | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]     |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a ... | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this ... | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground b... |               9 |                5 |
| millionaire pound cake               | 286009 |       120 |           461724 | 2008-02-12  | ['time-to-make', 'course', 'cuisine', 'preparation', 'occasion', 'north-american', 'desserts', 'amer... | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0] |         7 | ['freheat the oven to 300 degrees', 'grease a 10-inch tube pan with butter , dust the bottom and sid... | why a millionaire pound cake?  because it's super rich!  this scrumptious cake is the pride of an el... | ['butter', 'sugar', 'eggs', 'all-purpose flour', 'whole milk', 'pure vanilla extract', 'almond extra... |               7 |                5 |
| 2000 meatloaf                        | 475785 |        90 |          2202916 | 2012-03-06  | ['time-to-make', 'course', 'main-ingredient', 'preparation', 'main-dish', 'potatoes', 'vegetables', ... | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]    |        17 | ['pan fry bacon , and set aside on a paper towel to absorb excess grease', 'mince yellow onion , red... | ready, set, cook! special edition contest entry: a mediterranean flavor inspired meatloaf dish. feat... | ['meatloaf mixture', 'unsmoked bacon', 'goat cheese', 'unsalted butter', 'eggs', 'baby spinach', 'ye... |              13 |                5 |

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

---

## Framing a Prediction Problem

Here's what a Markdown table looks like. Note that the code for this table was generated _automatically_ from a DataFrame, using

```py
print(counts[['Quarter', 'Count']].head().to_markdown(index=False))
```


---

## Baseline Model


---

## Final Model


---
