# Bike Sharing Demand
This is a project page for the kaggle competition on bike sharing demand. (https://www.kaggle.com/c/bike-sharing-demand)

#  Background
In the competition, bike rentals per hour need to be predicted. The training set consists of bike rentals per hour for the first 20 days of each calendar month for 2012 and 2013. The test set consists of the remaining days of each month. 

The following independent variables are given (taken from the project page):
'datetime' - hourly date + timestamp  
'season' -  1 = spring, 2 = summer, 3 = fall, 4 = winter 
'holiday' - whether the day is considered a holiday
'workingday' - whether the day is neither a weekend nor holiday
'weather - 1: Clear, Few clouds, Partly cloudy, Partly cloudy
2: Mist + Cloudy, Mist + Broken clouds, Mist + Few clouds, Mist
3: Light Snow, Light Rain + Thunderstorm + Scattered clouds, Light Rain + Scattered clouds
4: Heavy Rain + Ice Pallets + Thunderstorm + Mist, Snow + Fog 
'temp' - temperature in Celsius
'atemp' - "feels like" temperature in Celsius
'humidity' - relative humidity
'windspeed' - wind speed

Three dependent variables are given. 
'count' is the total number of bike rentals per hour. 
'registered' is the number of bike rentals per hour for registered users.
'casual' is the number of bike rentals per hour for casual users.

#  Exploratory data analysis

In order to get a better understanding of the dataset, I created a number of visualization. Some visualizations
might not look very interesting but it is a good idea to double-check the data.
 
 Is the depent variable normally distributed? (No)
```
path_training_data = "data/train(1).csv"
data = pd.read_csv(path_training_data)
data['count'].plot(kind='hist', bins=500)
plt.xlabel('Rentals per hour')
plt.ylabel('Frequency')
plt.xlim([0, 1000])
plt.grid('on')
```

![alt tag](https://github.com/drawer87/kaggle/blob/master/outcome_var.jpg)

Do casual and registered users account for the same number of rentals?
Registered users seem to make up for a much larger proportion of the total rentals per hour. Moreover,
the spread in rentals per hour is larger. Moreover, there seem to be a number of outliers.

```
boxplot_data = pd.DataFrame() 
boxplot_data['registered'] = data['registered'].values
boxplot_data['casual'] = data['casual'].values
boxplot_data.plot(kind='box')
plt.ylabel('Rentals per hour')
plt.ylim([-50, 600])
```
![alt tag](https://github.com/drawer87/kaggle/blob/master/rentals_registered_casual.jpg)

Does the daily usage pattern differ between registered and casual users?

```
hours = pd.DatetimeIndex(data['datetime']).hour
unique_hours = list(range(24))
boxplot_group_1 = []
boxplot_group_2 = []

plt.subplot(1, 2, 1)
for current_hour in unique_hours:
    hour_index = np.where(hours == current_hour)[0]
    boxplot_group_1.append(list(data['casual'].values[hour_index]))
    boxplot_group_2.append(list(data['registered'].values[hour_index]))

plt.boxplot(boxplot_group_1) 
plt.xlabel('Hour of the day')
plt.ylabel('Rentals')
plt.ylim([0, 1000])
plt.title('Casual users')

plt.subplot(1, 2, 2)
plt.boxplot(boxplot_group_2) #list(data['casual'].values[hour_index]), 1)
plt.xlabel('Hour of the day')
plt.ylabel('Rentals')
plt.ylim([0, 1000])
plt.title('Registered users')
```

![alt tag](https://github.com/drawer87/kaggle/blob/master/rentals_per_hour_casual.jpg)

Is there a difference per calendar day?

![alt tag](https://github.com/drawer87/kaggle/blob/master/rentals_per_calendar_day.jpg)


Is there a difference in rentals per week day?
![alt tag](https://github.com/drawer87/kaggle/blob/master/rentals_per_weekday.jpg)


Conclusion:

Both users have very different characteristics. Registered users follow a very stable usage pattern. They take their bike to work in the morning and also driven back in the evening. Casual users mostly rent bikes on the weekend (preferrably Sundays). Weather, season, holidays and year have an effect on the number of rents. 

#  Feature Engineering


#  Model Development


For registered and casual users, a separate xgb boost is estimated. XGB hyperparameters are found using random initialisations and selecting the best based on their performance on a separate validation set.

#  Model performance

The test set seems to be quite different from the training set as the local rmsle was always much lower (0.33) than the public score (0.40). The final score on the public leaderboard is: 0.40693 (within the top10% of all submissions).


![alt tag](https://github.com/drawer87/kaggle/blob/master/kaggle_score.jpg)

