# Capstone Project - Car Pricing Algorithm

### Problem Statement
Which basic statistical model is best at predicting used car prices based on car features?  What is the best way of implementing these models specific to used cars?


### Data Used/Data Dictionary

All data used was taken directly from the Carvana website, using both schema.org data and regular html parsing, during the first weekend of August, 2022.

https://www.carvana.com/cars

The features for each car are:

**name:** a summary of the car year, manufacturer, and model\
**year:**  the model year of the car\
**manufacturer:** the name of the company that manufactured the car\
**model:**  the model name of the car\
**trim:**  the trim level of the car\
**mileage:**  the odometer reading of the car at the time it was put up for resale (miles)\
**color:**  the color of the car\
**price:**  the price for which the car is being offered for resale (US dollars)\


### The Modeling Process

After data scraping, a dataframe was created with 10,659 unique cars (VINs were compared and duplicates dropped to ensure unique cars).  If Carvana had ten or fewer cars for sale of a particular model, then those cars and that model were not included in this dataframe.  This left a dataframe with 404 different car models.

Initially the entire dataframe was one hot encoded for categorical variables, split into train and test sets, and tested under a standard linear regression.  Unfortunately, the appearance of some unseen car model variables in the test set led to astronomical coefficients and predictions.  The dataframe was then duplicated eight times, train-test split again, and then tested.  This model worked as expected, although data leakage limited any useful result.

Decision tree and random forest models worked better on this original train-test set (without any duplication), but still had relatively high RMSE values and low r-squared values, indicating unacceptable bias.

The next step involved sampling approximately 50 unseen cars on 'per-car model' models.  For example, if a car was a Camara a model would be fit only on Camaro's in the original dataset and a prediction made on the price of that unseen car.  The function disqualified any car whose model was not in our initial dataset, which turned out to be two cars here.  RMSE was reduced drastically on all three models with the random forest model doing the best with an RMSE of approximately 2000 (dollars).

Lastly, these 50 unseen cars were tested on the original dataframe-wide algorithms.  This effectively increased the size of my training set and provided a much smaller testing set.  RMSE and r-squared values were, understandably, similar to the original train-test split, although the decision tree performed worse here.


### Conclusion

With a dataset involving a very specific manufactured set of goods, one algorithm was not the best choice for predicting price.  Rather, splitting the goods into the smallest group that was still large enough to warrant a statistical model worked better.  This is likely due to the fact that one particular categorical value for one car - like trim level - has no meaningful correlation to another car which does not even use that label.

Additionally, on these small datasets split by car model, random forest worked the best judging by RMSE on our test set of unseen cars. This makes sense for two reasons:  first, the decision tree based models tend to work well on data like this, where some features are almost a subset of another feature.  Secondly, the decision tree based models were able to handle unseen categorical values better.  While the linear regression model would fail with certain astronomical coefficients, the decision tree and random forest models, while not ideal, still produced realistic predictions - even with poor data.

For a company who had access to far more features and a much more robust database, any three of these models would likely work well.  However, it seems that a random forest (or even decision tree) would still be the best choice as it does avoid any risk of unusual coefficients sneaking into the model.

And lastly, this exercise was simply an investigation into a snapshot of available car prices.  Any true pricing algorithm would have to account for the profit maximizing level of those base prices, and would have to account for demand, inventory costs, seasonal adjustments, and other factors.  This goes well beyond the scope of this project.