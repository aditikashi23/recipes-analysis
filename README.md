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

<table border="1" class="dataframe">\n  <thead>\n    <tr style="text-align: right;">\n      <th></th>\n      <th>name</th>\n      <th>id</th>\n      <th>minutes</th>\n      <th>contributor_id</th>\n      <th>submitted</th>\n      <th>tags</th>\n      <th>nutrition</th>\n      <th>n_steps</th>\n      <th>steps</th>\n      <th>description</th>\n      <th>ingredients</th>\n      <th>n_ingredients</th>\n      <th>average_rating</th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>0</th>\n      <td>1 brownies in the world    best ever</td>\n      <td>333281</td>\n      <td>40</td>\n      <td>985201</td>\n      <td>2008-10-27</td>\n      <td>[\'60-minutes-or-less\', \'time-to-make\', \'course\', \'main-ingredient\', \'preparation\', \'for-large-groups\', \'desserts\', \'lunch\', \'snacks\', \'cookies-and-brownies\', \'chocolate\', \'bar-cookies\', \'brownies\', \'number-of-servings\']</td>\n      <td>[138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]</td>\n      <td>10</td>\n      <td>[\'heat the oven to 350f and arrange the rack in the middle\', \'line an 8-by-8-inch glass baking dish with aluminum foil\', \'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted\', \'remove from heat and let cool to room temperature\', \'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated\', \'add cooled chocolate and mix until uniform in color\', \'add flour and stir until just incorporated\', \'transfer batter to the prepared baking dish\', \'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes\', \'remove from the oven and cool completely before cutting\']</td>\n      <td>these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you\'ll ever make.....sereiously! there\'s no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they\'re pure heaven!</td>\n      <td>[\'bittersweet chocolate\', \'unsalted butter\', \'eggs\', \'granulated sugar\', \'unsweetened cocoa powder\', \'vanilla extract\', \'brewed espresso\', \'kosher salt\', \'all-purpose flour\']</td>\n      <td>9</td>\n      <td>4.0</td>\n    </tr>\n    <tr>\n      <th>1</th>\n      <td>1 in canada chocolate chip cookies</td>\n      <td>453467</td>\n      <td>45</td>\n      <td>1848091</td>\n      <td>2011-04-11</td>\n      <td>[\'60-minutes-or-less\', \'time-to-make\', \'cuisine\', \'preparation\', \'north-american\', \'for-large-groups\', \'canadian\', \'british-columbian\', \'number-of-servings\']</td>\n      <td>[595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0]</td>\n      <td>12</td>\n      <td>[\'pre-heat oven the 350 degrees f\', \'in a mixing bowl , sift together the flours and baking powder\', \'set aside\', \'in another mixing bowl , blend together the sugars , margarine , and salt until light and fluffy\', \'add the eggs , water , and vanilla to the margarine / sugar mixture and mix together until well combined\', \'add in the flour mixture to the wet ingredients and blend until combined\', \'scrape down the sides of the bowl and add the chocolate chips\', \'mix until combined\', \'scrape down the sides to the bowl again\', \'using an ice cream scoop , scoop evenly rounded balls of dough and place of cookie sheet about 1 - 2 inches apart to allow for spreading during baking\', \'bake for 10 - 15 minutes or until golden brown on the outside and soft &amp; chewy in the center\', \'serve hot and enjoy !\']</td>\n      <td>this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don\'t have margarine or don\'t like it, then just use butter (softened) instead.</td>\n      <td>[\'white sugar\', \'brown sugar\', \'salt\', \'margarine\', \'eggs\', \'vanilla\', \'water\', \'all-purpose flour\', \'whole wheat flour\', \'baking soda\', \'chocolate chips\']</td>\n      <td>11</td>\n      <td>5.0</td>\n    </tr>\n    <tr>\n      <th>2</th>\n      <td>412 broccoli casserole</td>\n      <td>306168</td>\n      <td>40</td>\n      <td>50969</td>\n      <td>2008-05-30</td>\n      <td>[\'60-minutes-or-less\', \'time-to-make\', \'course\', \'main-ingredient\', \'preparation\', \'side-dishes\', \'vegetables\', \'easy\', \'beginner-cook\', \'broccoli\']</td>\n      <td>[194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]</td>\n      <td>6</td>\n      <td>[\'preheat oven to 350 degrees\', \'spray a 2 quart baking dish with cooking spray , set aside\', \'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce\', \'pour into baking dish , sprinkle remaining cheese over top\', \'bake for 25 minutes or until cheese is lightly browned\', \'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes\']</td>\n      <td>since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don\'t think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell\'s soup. but i think mine is better since i don\'t like cream of mushroom soup.submitted to "zaar" on may 28th,2008</td>\n      <td>[\'frozen broccoli cuts\', \'cream of chicken soup\', \'sharp cheddar cheese\', \'garlic powder\', \'ground black pepper\', \'salt\', \'milk\', \'soy sauce\', \'french-fried onions\']</td>\n      <td>9</td>\n      <td>5.0</td>\n    </tr>\n    <tr>\n      <th>3</th>\n      <td>millionaire pound cake</td>\n      <td>286009</td>\n      <td>120</td>\n      <td>461724</td>\n      <td>2008-02-12</td>\n      <td>[\'time-to-make\', \'course\', \'cuisine\', \'preparation\', \'occasion\', \'north-american\', \'desserts\', \'american\', \'southern-united-states\', \'dinner-party\', \'holiday-event\', \'cakes\', \'dietary\', \'christmas\', \'thanksgiving\', \'low-sodium\', \'low-in-something\', \'taste-mood\', \'sweet\', \'4-hours-or-less\']</td>\n      <td>[878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0]</td>\n      <td>7</td>\n      <td>[\'freheat the oven to 300 degrees\', \'grease a 10-inch tube pan with butter , dust the bottom and sides with flour , and set aside\', \'in a large mixing bowl , cream the butter and sugar with an electric mixer and add the eggs one at a time , beating after each addition\', \'alternately add the flour and milk , stirring till the batter is smooth\', \'add the two extracts and stir till well blended\', \'scrape the batter into the prepared pan and bake till a cake tester or knife blade inserted in the center comes out clean , about 1 1 / 2 hours\', \'cool the cake in the pan on a rack for 5 minutes , then turn it out on the rack to cool completely\']</td>\n      <td>why a millionaire pound cake?  because it\'s super rich!  this scrumptious cake is the pride of an elderly belle from jackson, mississippi.  the recipe comes from "the glory of southern cooking" by james villas.</td>\n      <td>[\'butter\', \'sugar\', \'eggs\', \'all-purpose flour\', \'whole milk\', \'pure vanilla extract\', \'almond extract\']</td>\n      <td>7</td>\n      <td>5.0</td>\n    </tr>\n    <tr>\n      <th>4</th>\n      <td>2000 meatloaf</td>\n      <td>475785</td>\n      <td>90</td>\n      <td>2202916</td>\n      <td>2012-03-06</td>\n      <td>[\'time-to-make\', \'course\', \'main-ingredient\', \'preparation\', \'main-dish\', \'potatoes\', \'vegetables\', \'4-hours-or-less\', \'meatloaf\', \'simply-potatoes2\']</td>\n      <td>[267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]</td>\n      <td>17</td>\n      <td>[\'pan fry bacon , and set aside on a paper towel to absorb excess grease\', \'mince yellow onion , red bell pepper , and add to your mixing bowl\', \'chop garlic and set aside\', \'put 1tbsp olive oil into a saut pan , along with chopped garlic , teaspoons white pepper and a pinch of kosher salt\', \'bring to a medium heat to sweat your garlic\', \'preheat oven to 350f\', \'coarsely chop your baby spinach add to your heated pan , stir frequently for approximately 5 min to wilt\', \'add your spinach to the mixing bowl\', \'chop your now cooled bacon , and add it to the mixing bowl\', \'add your meatloaf mix to the bowl , with one egg and mix till thoroughly combined\', \'add your goat cheese , one egg , 1 / 8 tsp white pepper and 1 / 8 tsp of kosher salt and mix till thoroughly combined\', \'transfer to a 9x5 meatloaf pan , and cook for 60 min or until the internal temperature is at least 160f\', \'let stand for 5min\', \'melt 1tbsp unsalted butter into a frying pan , and cook up to three eggs at a time\', \'crack each egg into a separate dish , in order to prevent egg shells from reaching the pan , then add salt and pepper to taste\', \'wait until the egg whites are firm looking , but slightly runny on top before flipping your eggs\', \'after flipping , wait 10~20 seconds before removing each egg and placing it over your slices of meatloaf\']</td>\n      <td>ready, set, cook! special edition contest entry: a mediterranean flavor inspired meatloaf dish. featuring: simply potatoes - shredded hash browns, egg, bacon, spinach, red bell pepper, and goat cheese.</td>\n      <td>[\'meatloaf mixture\', \'unsmoked bacon\', \'goat cheese\', \'unsalted butter\', \'eggs\', \'baby spinach\', \'yellow onion\', \'red bell pepper\', \'simply potatoes shredded hash browns\', \'fresh garlic\', \'kosher salt\', \'white pepper\', \'olive oil\']</td>\n      <td>13</td>\n      <td>5.0</td>\n    </tr>\n  </tbody>\n</table>

Below is a histogram of cooking times.The histogram visualizes the distribution of cooking times for recipes in the dataset. Most recipes have short cooking times, with the majority falling under 100 minutes, suggesting that users prefer recipes with faster preparation times. This trend indicates that future analysis should focus on how average ratings vary within this dominant range to explore the relationship between cooking time and user satisfaction.

<iframe
  src="assets/cook_time_hist.html"
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
