# Growth and Retention Strategies for Mobile Apps

Mobile apps are omnipresent in our daily lives. With new low-code platforms allowing everyone with skills or not to create apps, it has become a easy to create apps and make money with them. Hence, these two factors, simplicit yand lucrativeness, affects how we should develop our apps. In this project, I did a comprehensive analysis of the Android app market which contains tens of thousands of apps in a myriad of categories. I will derive actionable insights from the data to come up with strategies to boost **growth** and **retention**.

## How can we effectively come up with strategies to size and price our app?

Some of the questions which we would answer to devise our stategies are found below:

* Does the size of an app affect its rating?
* Do users really care about system-heavy apps or do they prefer light-weighted apps?
* Does the price of an app affect its rating?
* Do users always prefer free apps over paid apps?

## Methods

The analytical methods below were used to answer the research questions above:

* Analyze the data using a Bar Chart that shows the distribution of apps across different categories.
* Examine the relationship between size, price, and rating of apps using Jointplot.
* Use a Strip Plot to visualize the distribution of paid apps across different categories.
* Prepare the data for a Box Plot that compares the number of installs of paid apps vs. number of installs of free apps.


## Abstract
In this Google Play Store Apps Dataset, I wanted to find out what makes some apps better than others? Why some apps more popular than others? What factors should we take into consideration when designing an app for it to be a first-class app? In this analysis, I found out that size and price affects the number of downloads and they are inversely proportional. While Game apps are popular, Family apps have the biggest market prevalence. People are willing to pay up to $80 for an app as long as it brings them value. Jointplots, strip plots, barcharts and histograms were used to come to these conclusions.

## Dataset(s)
The dataset is from Kaggle: https://www.kaggle.com/lava18/google-play-store-apps

● apps.csv: contains all the details of the applications on Google Play. There are 13 features that describe a given app.

● user_reviews.csv: contains 100 reviews for each app, most helpful first. The text in each review has been pre-processed and attributed with three new features: Sentiment (Positive, Negative or Neutral), Sentiment Polarity and Sentiment Subjectivity.

## Plan of Action
1) Load Dataset
2) Data Wrangling
3) Exploring App Categories
4) Distribution of App Ratings
5) Analysis of Size and Price of AppsRelatyion between Apps Category and Price
6) Filtering of Junk Apps
7) Analysis of popularity of Paid vs Free Apps
8) Sentiment Analysis of User Reviews
9) Limitations of Analysis
10) Conclusion

## 1. Load Dataset
The first step is to load in the dataset. Remove any duplicate values which might be present. Analyse the number of data points present and print teh first five rows of the dataset.

```
# Read in dataset
import pandas as pd
apps_with_duplicates = pd.read_csv("datasets/apps.csv")

# Drop duplicates from apps_with_duplicates
apps = apps_with_duplicates.drop_duplicates()

# Print the total number of apps
print('Total number of apps in the dataset = ', len(apps_with_duplicates))

# Have a look at a random sample of 5 rows
print(apps.head(5))
print(apps.shape)
```
We would also want to use ```apps.info``` to check the data type.

## 2. Data Wrangling
When it comes to Data Wrangling, I believe it follows the **Pareto Principle** which states that "for many outcomes, roughly 80% of consequences come from 20% of causes." In this situation, Data Wrangling/Data Cleaning consists of 80% of the whole work and only 20% is the real analysis task. If we fail to clean our data at an early stage, we can be sure our output will be flawed.

By looking at a random sample of the dataset rows (from the above task), we observe that some entries in the columns like “Installs" and "Price" have a few special characters ("+" "," "$") due to the way the numbers have been represented. This prevents the columns from being purely numeric, making it difficult to use them in subsequent future mathematical calculations. Ideally, we would want these columns to contain only digits from [0-9].
Specifically, the special characters "," and "+" present in "Installs" column and "$" present in "Price" column need to be removed.

```
# List of characters to remove
chars_to_remove = ["+", ",", "$"]
# List of column names to clean
cols_to_clean = ["Installs","Price"]

# Loop for each column in cols_to_clean
for col in cols_to_clean:
    # Loop for each char in chars_to_remove
    for char in chars_to_remove:
        # Replace the character with an empty string
        apps[col] = apps[col].apply(lambda x: x.replace(char, ""))
        
# Print a summary of the apps dataframe
print(apps.info())
```
## 3. Exploring App Categories

With more than 1 billion active users in 190 countries around the world, Google Play continues to be an important distribution platform to build a global audience. For businesses to get their apps in front of users, it's important to make them more quickly and easily discoverable on Google Play. To improve the overall search experience, Google has introduced the concept of grouping apps into categories.

This brings us to the following questions:

* Which category has the highest share of (active) apps in the market
* Is any specific category dominating the market?
* Which categories have the fewest number of apps?



```
import plotly
plotly.offline.init_notebook_mode(connected=True)
import plotly.graph_objs as go

# Print the total number of unique categories
num_categories = len(apps['Category'].unique())
print('Number of categories = ', num_categories)

# Count the number of apps in each 'Category'. 
num_apps_in_category = apps['Category'].value_counts()

# Sort num_apps_in_category in descending order based on the count of apps in each category
sorted_num_apps_in_category = num_apps_in_category.sort_values(ascending = False)

data = [go.Bar(
        x = num_apps_in_category.index, # index = category name
        y = num_apps_in_category.values, # value = count
)]

plotly.offline.iplot(data)
```


![alt text](https://github.com/yudhisteer/Google-Playstore-App-Analytics/blob/main/Plots/newplot.png)

We will see that there are 33 unique app categories present in our dataset. Family and Game apps have the highest market prevalence. Interestingly, Tools, Business and Medical apps are also at the top.

## 4. Distribution of App Ratings

After having witnessed the market share for each category of apps, I analysed how all these apps perform on an average. App ratings (on a scale of 1 to 5) impact the discoverability, conversion of apps as well as the company's overall brand image. Ratings are a key performance indicator of an app.

```
# Average rating of apps
avg_app_rating = apps['Rating'].mean()
print('Average app rating = ', avg_app_rating)

# Distribution of apps according to their ratings
data = [go.Histogram(
        x = apps['Rating']
)]

# Vertical dashed line to indicate the average app rating
layout = {'shapes': [{
              'type' :'line',
              'x0': avg_app_rating,
              'y0': 0,
              'x1': avg_app_rating,
              'y1': 1000,
              'line': { 'dash': 'dashdot'}
          }]
          }

plotly.offline.iplot({'data': data, 'layout': layout})
```


![alt text](https://github.com/yudhisteer/Google-Playstore-App-Analytics/blob/main/Plots/newplot%20(1).png)

From the research, I found that the average volume of ratings across all app categories is 4.17. The histogram plot is skewed to the left indicating that the majority of the apps are highly rated with only a few exceptions in the low-rated apps.


## 5. Analysis of Size and Price of AppsRelatyion between Apps Category and Price

For size, if the mobile app is too large, it may be difficult and/or expensive for users to download. Lengthy download times could turn users off before they even experience your mobile app. Plus, each user's device has a finite amount of disk space. For price, some users expect their apps to be free or inexpensive. These problems compound if the developing world is part of your target market; especially due to internet speeds, earning power and exchange rates.

The analysis that follows will answer our research qurstions we postulated before starting the project.

* Does the size of an app affect its rating?
* Do users really care about system-heavy apps or do they prefer light-weighted apps?
* Does the price of an app affect its rating?
* Do users always prefer free apps over paid apps?

![alt text](https://github.com/yudhisteer/Google-Playstore-App-Analytics/blob/main/Plots/Screenshot%202021-08-31%20131238.png)

I found that the majority of top rated apps (rating over 4) range from 2 MB to 20 MB. I also found that the vast majority of apps price themselves under $10.

## 7. Analysis of popularity of Paid vs Free Apps
The costs of apps are largely based on features, complexity, and platform.

There are many factors to consider when selecting the right pricing strategy for a mobile app. It is important to consider the willingness of the customer to pay for an app. A wrong price could break the deal before the download even happens. Potential customers could be turned off by what they perceive to be a shocking cost, or they might delete an app they’ve downloaded after receiving too many ads or simply not getting their money's worth.

Different categories demand different price ranges. Some apps that are simple and used daily, like the calculator app, should probably be kept free. However, it would make sense to charge for a highly-specialized medical app that diagnoses diabetic patients. Below, we see that Medical and Family apps are the most expensive. Some medical apps extend even up to $80! All game apps are reasonably priced below $20.


![alt text](https://github.com/yudhisteer/Google-Playstore-App-Analytics/blob/main/Plots/Screenshot%202021-08-31%20131632.png)

It looks like a bunch of the really expensive apps are "junk" apps. That is, apps that don't really have a purpose. Some app developer may create an app called I Am Rich Premium or most expensive app (H) just for a joke or to test their app development skills. Some developers even do this with malicious intent and try to make money by hoping people accidentally click purchase on their app in the store. We will only take apps whose price is below $100.

![alt text](https://github.com/yudhisteer/Google-Playstore-App-Analytics/blob/main/Plots/Screenshot%202021-08-31%20132023.png)


## 7. Analysis of popularity of Paid vs Free Apps
For apps in the Play Store today, there are five types of pricing strategies: 
1. Free 
2. Freemium 
3. Paid 
4. Paymium
5. Subscription. 


We'll focus on free and paid apps only. Some characteristics of free apps are:

* Free to download.
* Main source of income often comes from advertisements.
* Often created by companies that have other products and the app serves as an extension of those products.
* Can serve as a tool for customer retention, communication, and customer service.


Some characteristics of paid apps are:

* Users are asked to pay once for the app to download and use it.
* The user can't really get a feel for the app before buying it.


Are paid apps installed as much as free apps?

![alt text](https://github.com/yudhisteer/Google-Playstore-App-Analytics/blob/main/Plots/newplot%20(2).png)

It turns out that paid apps have a relatively lower number of installs than free apps, though the difference is not as stark as I would have expected!

## 8. Sentiment Analysis of User Reviews
Mining user review data to determine how people feel about your product, brand, or service can be done using a technique called **sentiment analysis**. User reviews for apps can be analyzed to identify if the mood is positive, negative or neutral about that app. For example, positive words in an app review might include words such as 'amazing', 'friendly', 'good', 'great', and 'love'. Negative words might be words like 'malware', 'hate', 'problem', 'refund', and 'incompetent'.

![alt text](https://github.com/yudhisteer/Google-Playstore-App-Analytics/blob/main/Plots/Screenshot%202021-08-31%20132826.png)

By plotting sentiment polarity scores of user reviews for paid and free apps, we observe that free apps receive a lot of harsh comments, as indicated by the outliers on the negative y-axis. Reviews for paid apps appear never to be extremely negative. This may indicate something about app quality, i.e., paid apps being of higher quality than free apps on average. The median polarity score for paid apps is a little higher than free apps, thereby syncing with our previous observation.

## 9. Limitations of Analysis
This dataset is based on data found on Google Play Store only, that is only from Android users. A more conclusive argument could have been made if data from iOS App Store was available where we could get a bigger dataset and also analyze the ratings of the same app on the two platforms. Also, there are a lot of redundant apps(junk apps) on playstore. Although, I have cleaned them in my notebook, there are still some which are difficult to be spotted.
These apps affects the overall analysis.

## 10. Conclusion
1. Family and Game apps have the highest market prevalence. If we make such apps, it better be good as there are a lot of fierce competitors.
2. Average App rating of app = 4.17. Unless we have a crappy app, we can be sure to receive good ratings.
3. If we want our app to be on top of Play Store list, then make it less than 20MB and make it free or less than $10.
4. Don’t be shy to price your Family or Medical app up to $80. People are willing to pay for such apps as long as they solve their problems with your solution.
5. People download more free apps than paid apps but not by a lot. So if you feel your app should be a paid one, don’t hesitate to put a price on it.
6. If you want good quality apps, opt for the paid ones.


### References
- Data Source: https://www.kaggle.com/lava18/google-play-store-apps
- Lavanya Gupta: DataCamp Project Instructor and Data Source
- Micromaster's UC San Diego: Python for Data Science Course
- Hands on machine learning with scikit-learn and tensorflow - Aurelien Geron
