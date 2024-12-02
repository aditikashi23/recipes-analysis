# Recipes and Ratings

by Aditi Kashi and Anish Kudupudi

---

## Introduction

The dataset analyzed comes from Food.com, a platform where users share recipes and rate their cooking experiences. It consists of two components: **recipes** and **user interactions**. The recipes dataset details attributes such as cooking time, nutritional values, and step-by-step instructions. The interactions dataset captures user reviews and ratings for these recipes.

The key question guiding this analysis (Parts 1 and 2) is:

**
What is the relationship between the cooking time and average rating of recipes?
**

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

<iframe src="assets/10-80-enrollment.html" width=800 height=600 frameBorder=0></iframe>

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
