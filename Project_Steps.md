# XGBoost_Time_Series
Delhi's mean temperature, humidity and wind speed predicions using XGBoost.

## ___________________________________________
This work was developed using the Daily Climate Data Set of Delhis's mean temperature, humidity and wind speed, with the intent of building a model using XGBoost to predict the future state of these variables in that location.
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

When we increase the learning_rate,we descrease the n_estimators.
This hyperparameters tuning was made using sklearn's Grid Search.










