# XGBoost_Time_Series
Delhi's mean temperature, humidity and wind speed predicions using XGBoost.

## ___________________________________________
This work was developed using the Daily Climate Data Set of Delhi's mean temperature, humidity and wind speed, with the intent of building a model using XGBoost to predict the future state of these variables in that location.
## ___________________________________________

# The Dataset:
The dataset used was obtained on Kaggle, and it contained the following columns:

date | mean temperature (°C) | humidity (g of H2O vapor / m3 of air) | wind speed (km/h) | mean pressure

of the period between 2013 and 2016.


# First Steps: Cleaning the data
Data cleaning is important in order to remove from the dataset data instances that are not necessary, not representative or are simply explicit outliers that could harm the model built, making its generalization capability worse. Therefore, the following steps were taken: removing the column that contained a variable that was not in our interest, the mean pressure, and the data point in 2017-01-01, since it was the only date in that year. Afterwards, the date column was set as the index to make our ploting easier later on. 
Also, three separate datasets were bulit considering each variable (meantemp, humidity and wind speed).
For each of these datasets, their time series were plotted along with the lines that show their upper and lower interqurtile limits in order to check for outliers using the interquartile range as the tool for such, where:
Upper Limit = Q3 + (1.5*(Q3-Q1))
Lower Limit = Q1 - (1.5*(Q3-Q1))
With this, the following plots were obtained:

![image](https://user-images.githubusercontent.com/100734219/224862544-f88d328a-4b36-46da-8461-a59c8fe47db5.png)
![image](https://user-images.githubusercontent.com/100734219/224862586-d46ad04f-96a0-4566-8d58-d2d350f5e77e.png)
![image](https://user-images.githubusercontent.com/100734219/224862608-3dc90d79-e272-4678-9660-eb16d194f20b.png)

We can see that the mean temperature had no outliers, but wind speed had datapoints over the upper limit and humidity had datapoints under the lower limit. These outliers were removed from the respective datasets.
Then, for the sake of simplicity, we selected the data before 2016 as the training set (for each variable) and the data of 2016 as the testing dataset. The datasets visual representation stayed as follows:

![image](https://user-images.githubusercontent.com/100734219/224863609-0ebb39ec-f37a-4f00-a700-d46fa49c7220.png)
![image](https://user-images.githubusercontent.com/100734219/224863626-a5d40641-c408-48da-a1be-fa000461ccb0.png)
![image](https://user-images.githubusercontent.com/100734219/224863640-d6bea8cf-d22a-4872-8d53-8ca76de2c7b9.png)

# Feature Engineering
Using the date, 6 time series features were created in order to apply the model:


Day of the week

Quarter

Month

Year

Day of the month

Week of the year


As these features represent well temporal aspects of time along a year, contemplating the main segmentations it can have.
After this, each training and testing datasets, for each variable, was submited to this feature creation. Along with this, the datasets were separated in the input and output datasets. The input dataset included the time series features previously created, and the output dataset included the variable (meantemp, humidity or wind speed).

# Grid Search: Finding the best hyperparameters along selection
The following hyperparameters were tuned in order to obtain their best combination:

max_depth (which is the depth of the trees used) - values tested: 3, 6, 10;

learning_rate (which controlls the intensity of the modification that each tree performs, affecting how fast the model learns) - values tested: 0.0001, 0.001, 0.01, 0.05, 0.1;

n_estimators (which is the number of trees used) - valued tested: 100, 500, 1000, 1500, 2000.

When we increase the learning_rate, we need to descrease the n_estimators.
This hyperparameters tuning was made using sklearn's Grid Search.

The results obtained with Grid Search were as follows:

Mean Temperature: Best Parameters according to Grid Search: {'learning_rate': 0.01, 'max_depth': 3, 'n_estimators': 500}

Humidity: Best Parameters according to Grid Search: {'learning_rate': 0.05, 'max_depth': 3, 'n_estimators': 100}

Wind Speed: Best Parameters according to Grid Search: {'learning_rate': 0.01, 'max_depth': 3, 'n_estimators': 500}

# Model Training with the best hyperparameters according to Grid Search

## Results for the variable Mean Temperature

Mean Squared Error =  10.9

Mean Absolute Error =  2.7

![image](https://user-images.githubusercontent.com/100734219/225484188-277c6e19-1700-4029-9fb6-bb38f7e5506a.png)

And no overfitting was observer during training. Although, visually, the predictions could be better, that was the result with the best hyperparameters along the previously selected ones.

## Results for the variable Humidity

Mean Squared Error =  131.4

Mean Absolute Error =  9.0

![image](https://user-images.githubusercontent.com/100734219/225484522-c0e2e790-7149-458f-a4f1-1422c54a5047.png)

But now we could see some overfitting:

![image](https://user-images.githubusercontent.com/100734219/225484622-565a844a-1cf7-405f-8190-6518b338461b.png)

At step 70 it starts happening. We can later on try to limit the n_estimators to try preventing that.

## Results for the variable Wind Speed

Mean Squared Error =  14.3
Mean Absolute Error =  2.9

![image](https://user-images.githubusercontent.com/100734219/225484869-3852887d-00d1-47f8-99ea-86c607be236d.png)

Now we can also see some overfitting happening:

![image](https://user-images.githubusercontent.com/100734219/225485051-5801d3c5-a98c-485e-8666-b58ac6a51a0f.png)

From step 450 and on. We can later on try to limit the n_estimators to try preventing that.

# Humidity and Wind Speed manual hyperparameter tuning: trying to overcome overfitting

## Humidity

Manual hyperparameter tuning was made in order to try preventing the overfitting that happened using the hyperparameters obtained using Grid Search previously. For that, the step chosen was to increase the number of trees and descrease the learning rate (maintaining the max_depth at 3) until MAE and MSE stopped improving and/or new overfitting started happening again. The following results were reached:

![image](https://user-images.githubusercontent.com/100734219/226490000-2f6b397a-3c10-409c-ae71-4e8cdc617cf7.png)

With this, the hyperparameters chosen were n_estimators = 3200 and learning_rate = 0.001, with the following result:

![image](https://user-images.githubusercontent.com/100734219/226490189-cee5017b-f134-4b01-856b-0eaf8316fa46.png)

## Wind Speed

Using the same logic as previsouly (used for Humidity), the following results were reached:

![image](https://user-images.githubusercontent.com/100734219/226495228-50cee12b-3631-4026-bdd2-656a27cdf915.png)

With this, the hyperparameters chosen were n_estimators = 6000 and learning_rate = 0.0005, with the following result:

![image](https://user-images.githubusercontent.com/100734219/226496653-b38aa3f5-9a2f-4b95-9e7f-7cd7710a054d.png)

# Final hyperparameters and predicting the future with them

The final hyperparameters for each variable prediction using XGBoost were:

Mean Temperature:

        - n_estimators = 500
        - max_depth = 3
        - learning_rate = 0.01

Humidity:

        - n_estimators = 3200
        - max_depth = 3
        - learning_rate = 0.001

Wind Speed:

        - n_estimators = 6000
        - max_depth = 3
        - learning_rate = 0.0005


Now, using these values, the following results were obtained for the whole year (daily) of 2017:

## Mean Temperature

![image](https://user-images.githubusercontent.com/100734219/226497385-75031533-6388-4648-a475-287d8d37948c.png)

## Humidity

![image](https://user-images.githubusercontent.com/100734219/226497421-181f5f06-10ca-43c7-81cb-5cf0fec76fae.png)

## Wind Speed

![image](https://user-images.githubusercontent.com/100734219/226497451-1bf631d3-e16e-495d-bbe1-08c74d59f6ed.png)

