# Regression Modeling

In this assignment you'll put together the pieces of tidymodels to answer an applied question about the Subaru data. The data set for this assignment is the `subaru-data.txt` file that is posted to Canvas in Week 13.

The goal is to use this subaru data to understand a few fundamental questions:

-   How do prices vary by location? (This is open-ended but need not be incredibly detailed. Plan on writing a paragraph or two.)
-   What is the relationship between price and key covariates like age, mileage, and model?
-   What are five cars that seem like an unusually good deal?

This last question is the heart of the analysis, since we'd like to use this model to "carbitrage" car inventory. As we discussed in class, once you've built you're model we're interested in cars that have a large negative residual. These cars are cheaper than what our model thinks they're worth. Be on the look out for *absurd* deals, as these are probably salvage titles, cars being sold for parts, etc. Basically, you're looking for a car that's a couple of grand lower than expected, but not 20% of the expected price.

## Subaru Data

The Subaru data is messy, since it's based on scraping Craigslist (CL) web pages that are designed to make scraping a hassle. As such, expect to do some significant data cleaning. You don't need to do this with `recipes` and you may find it much easier to use `dplyr` for the cleaning portion. Here is a description of the data:

-   This data set has about 43K rows across 12 columns.
-   `post_id`: This is the unique identifier of the CL post.
-   `time_posted`: This is the date and time the post went live. The time is in UTC.
-   `make`: This is the car "make", either "subaru" or "Subaru".
-   `model`: This is the car model. These values are user entered, so you'll see all sorts of weird values. One step in your cleaning should be to cut the data down to a single model or a handful of models to use.
-   `year`: This is the year information from the post. If the car year field on the CL post has been filled in, that's what you're getting. But many CL users don't use this field. If that field is empty *and* the user has just a single year in the post, then you'll have that year in the `year` field. Many times users have multiple years in the CL post when they write about how long ago they did various pieces of work. In cases where the year field is empty and there are multiple years, I've just concatenated them. So a post that says "the car is from 1999 but I redid the brakes in 2005, the engine in 2008, and the steering wheel in 2011" would have "1999200520082011" in the year field. Don't use this field without cleaning these somehow!
-   `odometer`: this is the user-entered mileage. It's missing in many cases. Look for weird values like 0 or 10,000,000.
-   `vin`: The vehicle identification number. One hypothesis I've had is that sellers who list their VIN are likely to have filled in their data more accurately. I have no idea if this is true.
-   `title`: The statuses of `clean` and `lien` are both pretty normal. Other statuses might indicate a car that we'd be unlikely to purchase for driving.
-   `cylinders`: the number of cylinders the car's engine has. It's most common for Subarus to have 4, 6, or 8 cylinders. Other values are probably suspect?
-   `loction`: the location where the car is sold. This is the first part of the URL, so `http://missoula.craigslist.com` has a location of "missoula".
-   `price`: our response variable. This field is not the most reliable, sometimes from parsing issues, sometimes because users enter something like 0 or 1.
-   `url`: the URL of the post. Recent posts may have valid URLs, but many of these are likely to be broken because the post has been removed.

## Assignment

I'd like you to build a model to predict price, using what we've learned this semester. The ultimate goal is to recommend a few cars that seem like a good deal, so you might like to limit your data to just one or two car models. Remember, you don't have to use *all* the data since some of it is quite messy. You're just trying to find good deals on cars. The implication is that you should work with data you can trust.

Your model building should include the following steps:

-   Data cleaning: this can be done in `dplyr`, `recipes`, or both.
-   Splitting: split the data into a training and test set. Build your model on the training set and evaluate it on the test set. The car you find that's a good deal can be from either set, though. More on that below.
-   Model building: build your model using tidyverse functions. As an example, you might build a model using `linear_reg()` with `set_engine("lm")`. Or you might build a tree-model using `decision_tree()` with `set_engine("rpart")` or `set_engine("C5.0")`. These are just examples, you're free to do this however you'd like.
-   Predict: use your model to predict price. Find the cars with the largest negative residuals (that you trust). Indicate which cars you'd recommend buying.
-   Model interpretation: answer the other questions asked at the top about how price varies by `location`. Interpret the parameters in your model, similar to what we did in the write up for the diamond regression model from a few weeks ago.

Write up your results in an R-Markdown file. Make sure it is properly formatted and reasonably easy to read. Make sure to include the knitted HTML.

### Building your Final Model

Once you've decided on your final model form and evaluated it on the test set, I recommend refitting the model on *all* of the cleaned data. This will give you the best estimates for prediction. Use this "big model" to calculate the residuals and make your final inferences.
