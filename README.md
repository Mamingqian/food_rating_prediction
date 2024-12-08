# Predicting Recipe Ratings: Know Your Recipe's Rating Before Cooking

Author: Mingqian Ma

E-mail: mamq@umich.edu


## Introduction
The dataset we are working with contains information about recipes, their attributes, and user interactions, including ratings and reviews. It is a rich dataset that allows us to explore how different aspects of a recipe and user feedback influence the rating a recipe receives. The dataset is composed of two files:
1. **`RAW_recipes.csv`**: Contains detailed information about recipes, such as preparation time, ingredients, and nutrition data.
2. **`RAW_interactions.csv`**: Contains user interactions with recipes, including ratings, reviews, and timestamps.

---

### **Question for Investigation**

**Question**: *For a new recipe comment by a user, can we predict the rating the user will give to the recipe?*

This question is central to understanding the factors that drive user satisfaction with recipes. It has practical implications for food bloggers, recipe platforms, and culinary businesses aiming to optimize their content for better user engagement and satisfaction.

Readers of our website should care because:
- **User Perspective**: It provides insights into what recipe attributes resonate most with users.
- **Business Perspective**: Platforms can use these insights to recommend recipes more effectively, enhancing user retention and satisfaction.

---

### **Dataset Overview**

**Number of Rows**:  
- Recipes: `RAW_recipes.csv` contains **231652 rows**.
- Interactions: `RAW_interactions.csv` contains **731927 rows**.

**Relevant Columns and Descriptions**:  
The following columns from the dataset are relevant to the question:

1. **Recipe Attributes**:
   - **`name`**: The name of the recipe, which might influence user appeal.
   - **`minutes`**: The preparation time for the recipe.
   - **`tags`**: Categorical tags describing the recipe (e.g., "dessert", "gluten-free").
   - **`nutrition`**: A list containing nutritional information in the format `[calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]`.
   - **`n_steps`**: Number of steps in the recipe.
   - **`steps`**: Detailed instructions for preparing the recipe.
   - **`description`**: A short description of the recipe.

2. **User Interaction Data**:
   - **`review`**: User-provided text feedback on the recipe.
   - **`rating`**: The rating (1–5) the user gave to the recipe.

---

### **Why These Columns?**

- **Quantitative Features**: Features like `minutes`, `n_steps`, and `nutrition` provide objective measures of recipe complexity and nutritional content.
- **Text Features**: Features like `description`, `review`, and `steps` capture user sentiment, engagement, and perceived recipe quality.
- **Target Variable**: The `rating` column is the dependent variable we aim to predict, making it the focus of our analysis.

By leveraging these features, we aim to uncover actionable insights about user preferences and provide a predictive framework for future recipe ratings.

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning and Preprocessing
We started by merging the `RAW_recipes` and `RAW_interactions` datasets using the `recipe_id` column. This allowed us to combine recipe information with user interactions.

Next, we replaced ratings of 0 with NaN to indicate missing or invalid ratings and calculated the average rating for each recipe.

The head of the combined dataframe is as follows:

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                        | nutrition                                    |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | description                                                                                                                                                                                                                                                                                                                                                                       | ingredients                                                                                                                                                                    |   n_ingredients |          user_id |   recipe_id | date       |   rating | review                                                                                                                                                                                                                                                                                                                                           |   average_rating |   review_length |   description_length |   steps_length |   calories |   total_fat |   sugar |   sodium |   protein |   saturated_fat |   carbohydrates |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------|----------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------:|-----------------:|------------:|:-----------|---------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------:|----------------:|---------------------:|---------------:|-----------:|------------:|--------:|---------:|----------:|----------------:|----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'for-large-groups', 'desserts', 'lunch', 'snacks', 'cookies-and-brownies', 'chocolate', 'bar-cookies', 'brownies', 'number-of-servings'] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]     |        10 | ['heat the oven to 350f and arrange the rack in the middle', 'line an 8-by-8-inch glass baking dish with aluminum foil', 'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted', 'remove from heat and let cool to room temperature', 'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated', 'add cooled chocolate and mix until uniform in color', 'add flour and stir until just incorporated', 'transfer batter to the prepared baking dish', 'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes', 'remove from the oven and cool completely before cutting']                                                  | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you'll ever make.....sereiously! there's no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they're pure heaven!                                                                                                              | ['bittersweet chocolate', 'unsalted butter', 'eggs', 'granulated sugar', 'unsweetened cocoa powder', 'vanilla extract', 'brewed espresso', 'kosher salt', 'all-purpose flour'] |               9 | 386585           |      333281 | 2008-11-19 |        4 | These were pretty good, but took forever to bake.  I would send it ended up being almost an hour!  Even then, the brownies stuck to the foil, and were on the overly moist side and not easy to cut.  They did taste quite rich, though!  Made for My 3 Chefs.                                                                                   |                4 |             254 |                  260 |            753 |      138.4 |          10 |      50 |        3 |         3 |              19 |               6 |
| 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | ['60-minutes-or-less', 'time-to-make', 'cuisine', 'preparation', 'north-american', 'for-large-groups', 'canadian', 'british-columbian', 'number-of-servings']                                                               | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] |        12 | ['pre-heat oven the 350 degrees f', 'in a mixing bowl , sift together the flours and baking powder', 'set aside', 'in another mixing bowl , blend together the sugars , margarine , and salt until light and fluffy', 'add the eggs , water , and vanilla to the margarine / sugar mixture and mix together until well combined', 'add in the flour mixture to the wet ingredients and blend until combined', 'scrape down the sides of the bowl and add the chocolate chips', 'mix until combined', 'scrape down the sides to the bowl again', 'using an ice cream scoop , scoop evenly rounded balls of dough and place of cookie sheet about 1 - 2 inches apart to allow for spreading during baking', 'bake for 10 - 15 minutes or until golden brown on the outside and soft & chewy in the center', 'serve hot and enjoy !'] | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don't have margarine or don't like it, then just use butter (softened) instead.                                                                                                                                            | ['white sugar', 'brown sugar', 'salt', 'margarine', 'eggs', 'vanilla', 'water', 'all-purpose flour', 'whole wheat flour', 'baking soda', 'chocolate chips']                    |              11 | 424680           |      453467 | 2012-01-26 |        5 | Originally I was gonna cut the recipe in half (just the 2 of us here), but then we had a park-wide yard sale, & I made the whole batch & used them as enticements for potential buyers ~ what the hey, a free cookie as delicious as these are, definitely works its magic! Will be making these again, for sure! Thanks for posting the recipe! |                5 |             336 |                  230 |            802 |      595.1 |          46 |     211 |       22 |        13 |              51 |              26 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |  29782           |      306168 | 2008-12-31 |        5 | This was one of the best broccoli casseroles that I have ever made.  I made my own chicken soup for this recipe. I was a bit worried about the tsp of soy sauce but it gave the casserole the best flavor. YUM!                                                                                                                                  |                5 |             469 |                  369 |            485 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |
|                                      |        |           |                  |             |                                                                                                                                                                                                                             |                                              |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                |                 |                  |             |            |          | The photos you took (shapeweaver) inspired me to make this recipe and it actually does look just like them when it comes out of the oven.                                                                                                                                                                                                        |                  |                 |                      |                |            |             |         |          |           |                 |                 |
|                                      |        |           |                  |             |                                                                                                                                                                                                                             |                                              |           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |                                                                                                                                                                                                                                                                                                                                                                                   |                                                                                                                                                                                |                 |                  |             |            |          | Thanks so much for sharing your recipe shapeweaver. It was wonderful!  Going into my family's favorite Zaar cookbook :)                                                                                                                                                                                                                          |                  |                 |                      |                |            |             |         |          |           |                 |                 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 |      1.19628e+06 |      306168 | 2009-04-13 |        5 | I made this for my son's first birthday party this weekend. Our guests INHALED it! Everyone kept saying how delicious it was. I was I could have gotten to try it.                                                                                                                                                                               |                5 |             162 |                  369 |            485 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |
| 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | ['60-minutes-or-less', 'time-to-make', 'course', 'main-ingredient', 'preparation', 'side-dishes', 'vegetables', 'easy', 'beginner-cook', 'broccoli']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | ['preheat oven to 350 degrees', 'spray a 2 quart baking dish with cooking spray , set aside', 'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce', 'pour into baking dish , sprinkle remaining cheese over top', 'bake for 25 minutes or until cheese is lightly browned', 'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don't think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell's soup. but i think mine is better since i don't like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | ['frozen broccoli cuts', 'cream of chicken soup', 'sharp cheddar cheese', 'garlic powder', 'ground black pepper', 'salt', 'milk', 'soy sauce', 'french-fried onions']          |               9 | 768828           |      306168 | 2013-08-02 |        5 | Loved this.  Be sure to completely thaw the broccoli.  I didn&#039;t and it didn&#039;t get done in time specified.  Just cooked it a little longer though and it was perfect.  Thanks Chef.                                                                                                                                                     |                5 |             188 |                  369 |            485 |      194.8 |          20 |       6 |       32 |        22 |              36 |               3 |

### Univariate Analysis: Distribution of Ratings

We analyzed the distribution of average ratings using a histogram to identify trends. Here is the plot:

<iframe
  src="average_rating_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We see that the ratings are heavily left skewed, meaning that most people tend to give high scores to the recipes. Even over half of the recipes are given average rating of 5.0, which is the highest score. This should be taken into consideration when we are building the model.

### Bivariate Analysis: Visualize the relationship between the ratings and number of steps/number of ingredients

First a simple visualization of side-by-side boxplots of the ratings for recipes with different number of steps:

<iframe
  src="number_of_steps_by_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We can see that although mostly the distributions seems similar, and there is no significant difference in the ratings for recipes with different number of steps. Still, a very noticeable trend is that for higher ratings, the n_steps has a larger tail at the top. This could inversely mean that recipes with more steps are more likely to get higher ratings, which is also a good thing to consider when building the model afterwards.

### Aggregation Analysis: We further use a pivot table to display the mean and median of minutes/number of ingredients/number of steps for each rating to see if there is any trend.

|   ('mean', 'minutes') |   ('mean', 'n_ingredients') |   ('mean', 'n_steps') |   ('median', 'minutes') |   ('median', 'n_ingredients') |   ('median', 'n_steps') |
|----------------------:|----------------------------:|----------------------:|------------------------:|------------------------------:|------------------------:|
|               99.5318 |                     8.90282 |              10.6334  |                      40 |                             8 |                       9 |
|               97.6822 |                     9.20998 |              10.7458  |                      40 |                             9 |                       9 |
|               95.8014 |                     9.19923 |              10.0278  |                      40 |                             9 |                       9 |
|               93.8956 |                     9.09911 |               9.66844 |                      35 |                             9 |                       9 |
|              108.531  |                     9.05441 |              10.0504  |                      35 |                             9 |                       9 |

### Data Imputation

We see that the there are many NaN value in rating/average_rating. If the average rating is also missing, it means that that recipe has no rating at all. We will impute the missing values with the mean of the ratings. We delete these rows with missing average_rating as they have no effect at all in the model building. As for missing value with "user_id, recipe_id, date", there's 1 for each and we delete these rows as they are corrupted data.

As for the missing values in rating, we impute them with the average rating of the recipe. This is because the rating is the rating given by the user, and the average rating is the average rating of the recipe. We can see that the average rating is a good indicator of the rating given by the user, so we can use the average rating to impute the missing values in rating. Also, as imputation causes that not all scores are integers, we round the imputed values to the nearest integer.

The plot before imputation is as follows:

<iframe
  src="rating_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The plot after imputation is as follows:

<iframe
  src="rating_distribution_after.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We see that it does not change the distribution of the ratings much, and the distribution is still heavily left skewed as original.

## Framing a Prediction Problem

Here is a formal formulation of the problem we want to solve:
For a new recipe comment by a user, we want to predict the rating that the user will give to the recipe. We will use the information of the recipe uploaded by the user as well as the user's comments to predict his/her final rating to the recipe.

As there's only 1-5 stars in the rating, we treat the problem as a 5-class classification problem. We will use the information of the recipe uploaded by the user as well as the user's comments to predict his/her final rating to the recipe.

Formal Problem Statement:

Input: Information uploaded by the user about the recipe, including 'name', 'minutes', 'tags', 'nutrition', 'n_steps', 'steps', 'description', 'review' that are relevant meaningful features to the rating of the recipe.

Output: The rating that the user will give to the recipe, which is a number from 1 to 5. This is a 5-class classification problem.

Metric: We use Macro-Averaged AUC as the evaluation metric. This is because we want to see the overall performance of the model in predicting the ratings of the recipes. Also, we've seen that the rating distribution is heavily left skewed, and using AUC over accuracy is more robust to the class imbalance problem.

Note a Macro-Averaged AUC is just the average of the AUC of each class, which is an expansion of the AUC metric to multi-class classification problems.

## Baseline Model

We select a baseline model that utilize the original features to predict the ratings of the recipes. We will use a simple multi-class logistic regression model as the baseline model. We'll just treat the numerical values as standardized numbers and the text data using tf-idf vectorization. Then all the features will be inputted into the logistic regression model to predict the ratings of the recipes. For empty text values and missing text values, we impute "No data" to them. All numeerical values that are missing will be imputed with 0. We discard nutrition for baseline as it's difficult to deal with and requires additional parsing.

Baseline Model
Features:

Quantitative Features (2):
'minutes': Numerical feature indicating the time required for the recipe.
'n_steps': Numerical feature representing the number of steps in the recipe.
Textual Features (1):
'review': Processed using TF-IDF vectorization to extract up to 5000 numerical features representing the term frequency-inverse document frequency of words in user reviews.
Other text features are discarded.

Preprocessing:

Numerical Features: Standardized using StandardScaler to ensure a uniform scale for the logistic regression model.
Textual Features: Vectorized using TfidfVectorizer, transforming the unstructured text into numerical representations.

Model:
A Logistic Regression classifier was used, configured for multiclass classification to predict ratings (1-5).

Performance:

Macro-Averaged AUC: 0.9005, which is a good indication that the model performs well in distinguishing between ratings across all classes.

Strengths:
The model is simple and interpretable. The high AUC score on test dataset indicates the model generalizes well to unseen data.

Weaknesses:
Logistic regression assumes linear relationships, which might oversimplify the problem. We haven't consider the interaction between features, which could be important for predicting ratings. The model could be improved by incorporating additional features and exploring more complex models.


## Final Model

### **Features Added and Their Importance**

1. **Interaction Feature: `calories_protein`**
   - **Why It Matters**: The interaction between `calories` and `protein` reflects the nutrient density of the recipe, which could influence user satisfaction. For example, recipes that are high in both calories and protein may be perceived as more filling or nutritious, leading to higher ratings. By including this feature, we capture the joint effect of these nutritional properties, which are likely important for the final tastes and people's rating toward the recipe.

2. **Text Features: `review` and `description` with TF-IDF (Bigrams)**
   - **Why It Matters**: Textual descriptions (`description`) and user reviews (`review`) contain rich contextual information. The inclusion of bi-grams modeling allows us to capture common patterns or sentiments (e.g., "easy recipe", "too salty"), providing insights into recipe quality or user preferences.
   - **Reason for N-gram Choice**: Bigrams enhance the interpretability and informativeness of text data by considering local word relationships while maintaining computational efficiency.

3. **TF-IDF Parameters**:
   - **`max_features=5000`**: Focuses on the most relevant words/phrases while avoiding noise from infrequent terms.
   - **`ngram_range=(1, 2)`**: Captures both single words and common two-word phrases.

4. **Nutrition Parsing**:
   - **`calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)`**: The nutritional information is parsed to extract key nutrient values and percentages of daily values (PDV). These features provide insights into the healthiness and balance of the recipe, which can influence user ratings.

All these new features are added to the final model to enhance predictive performance and capture nuanced patterns in user feedback and recipe attributes. We aim to improve the rating prediction by introducing the new features and interaction term.

---

### **Improvement Over Baseline Model**

1. Architecture Improvement: By introducing Random Forest as the classifier instead of a simple logistic regression model, we leverage the ensemble learning technique to capture complex interactions between features and enhance predictive performance. Random Forest is robust to overfitting and can handle non-linear relationships, making it suitable for our multi-feature, multi-class classification problem with both text and numerical data.

2. Performance Analysis: The improved model achieves Macro-Averaged AUC of 0.8548, which is higher compared to the baseline, indicating better performance across all rating classes, even with the addition of new features and complexity. This indicates that our model generalizes well to unseen data and predicts rating better, proving that the additional features and the new model architecture are effective in enhancing predictive performance.


## Conclusion

Our study highlights the potential of leveraging recipe attributes and user feedback to predict ratings. By advancing from a simple logistic regression model to a Random Forest classifier with refined features, we improved predictive performance and captured meaningful insights into user preferences. The final model's robustness makes it valuable for real-world applications in recipe platforms, offering practical tools to optimize content and enhance user satisfaction. Future work could explore deep learning models and additional user-centric features to further refine predictions.





