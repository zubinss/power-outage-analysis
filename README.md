# Introduction

This dataset was accessed from Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure, at [https://engineering.purdue.edu/LASCI/research-data/outages](https://engineering.purdue.edu/LASCI/research-data/outages). The dataset examines statistics about major power outages in the United States from January 2000 to July 2016. 

Power outages are a significant issue affecting communities worldwide. While brief outages may cause mild inconveniences, outages that last over 24 hours can lead to negative impacts on families and their daily lives. Outages of this length lead to various disruptions such as loss of refrigeration, resulting in additional financial burdens, as well as complicate job logistics, especially for those working from home or relying on electronic devices and internet access. Small businesses can easily lose revenue, and potential damage to reputation if services are interrupted. Beyond the immediate effects, prolonged outages can strain local economies, increase insurance claims, and amplify recovery costs for utility providers.

Understanding whether a power outage is likely to become severe is crucial for effective preparation, and having access to predictive insights can enable families to make timely decisions, such as securing backup power sources or temporary relocation. This drives us to center our data analysis around the following question:


**What patterns can be identified in the timing and duration of severe power outages?**

The original DataFrame contains 1534 rows, corresponding to 1534 outages, and 57 columns with statistics about each outage. For the sake of our analysis, we will only use the following 17 columns in our analysis:


| Column Name           | Description         |
|-----------------------|---------------------|
| U.S._STATE            | The name of the US State the outage took place in.                    |
| NERC.REGION           | North American Electric Reliability Corporation (NERC) regions involved in the outage event.                    |
| CLIMATE.REGION        | US climate regions that are defined by the National Centers for Environmental Information                    |
| ANOMALY.LEVEL         | The oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season                    |
| OUTAGE.START          | The exact start date and time of the outage.                    |
| OUTAGE.RESTORATION    | The exact restoration date and time of the outage.                    |
| OUTAGE.DURATION       | The exact length of time of the outage.                    |
| CUSTOMERS.AFFECTED    | The amount of customers reported to be affected by the outage.                    |
| PC.REALGSP.STATE      | The per capita real gross state product (GSP) in the U.S. state (in 2009 US Dollars)                    |
| PCT_WATER_TOT         | The percentage of water area in the US state as compared to the overall water area in the continental U.S.             |
| CLIMATE.CATEGORY      | The climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for ONI                    |
| DEMAND.LOSS.MW        | Amount of peak demand lost during an outage event (in Megawatt)                    |
| POPULATION            | Population in the U.S. state in a year                    |
| Hour                  | The hour of the day that the outage took place in                    |
| Daypart               | The part of the day that the outage took place in                    |
| is_day                | Whether or not the outage took place during the day                    |
| is_severe             | Whether or not the outage is severe (defined by if the outage lasted for more than 24 hours)                    |

<br>

---

<br>

# Data Cleaning and Exploratory Data Analysis

<br>

## Data Cleaning


To clean our data, we had to start with out initial Excel file of data on the outages. We removed rows at the top of the Excel file talking about the source of the data.

<img width="671" alt="Screenshot 2024-12-08 at 10 04 45 PM" src="https://github.com/user-attachments/assets/f2502a1b-86a8-4566-b009-05c0a8f4ad75">

We then removed a row in the dataset showing the units of each of the columns so that we could have purely the data to work with. After that, we converted the Excel file into a CSV to be used easier in Python. 

We converted important date and time columns into `pd.Timestamp` objects. These columns were `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, and `OUTAGE.RESTORATION.TIME`. To make it easier to work all in one with these data columns, we also combined these 4 columns into 2 columns, `OUTAGE.START` and `OUTAGE.RESTORATION`, respectively.

In addition, we created the `is_day` and `Daypart` columns. `is_day` is a boolean variable that is True if the outage took place between 8 am and 8 pm, and False if it is between 8 pm and 8 am. `Daypart` categorizes the hour of day that the outage took place based on the table below:

| Daypart       | Hours                 |
|---------------|-----------------------|
| Morning       | 5:00 am - 11:59 am    | 
| Afternoon     | 12:00 pm - 4:59 pm    | 
| Evening       | 5:00 pm - 8:59 pm     | 
| Night         | 9:00 pm - 4:59 am     | 

There are a lot of columns in the dataset. To get only the ones necessary for our data analysis, we dropped all the columns except for `U.S._STATE`, `NERC.REGION`, `CLIMATE.REGION`, `ANOMALY.LEVEL`, `OUTAGE.START`, `OUTAGE.RESTORATION`, `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`, `PC.REALGSP.STATE`, `PCT_WATER_TOT`, `CLIMATE.CATEGORY`, `DEMAND.LOSS.MW`, `POPULATION`, `Hour`, `Daypart`, `is_day`, and `is_severe`.

Below is the head of our filtered table of data that we used for the rest of this project:


  
| U.S._STATE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | OUTAGE.START        | OUTAGE.RESTORATION   |   OUTAGE.DURATION |   CUSTOMERS.AFFECTED |   PC.REALGSP.STATE |   PCT_WATER_TOT | CLIMATE.CATEGORY   |   DEMAND.LOSS.MW |   Hour | Daypart   | is_day   |   is_severe |   POPULATION |
|:-------------|:--------------|:-------------------|----------------:|:--------------------|:---------------------|------------------:|---------------------:|-------------------:|----------------:|:-------------------|-----------------:|-------:|:----------|:---------|------------:|-------------:|
| Minnesota    | MRO           | East North Central |            -0.3 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |              3060 |                70000 |              51268 |         8.40733 | normal             |              nan |     17 | Evening   | False    |           1 |      5348119 |
| Minnesota    | MRO           | East North Central |            -0.1 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |                 1 |                  nan |              53499 |         8.40733 | normal             |              nan |     18 | Evening   | False    |           0 |      5457125 |
| Minnesota    | MRO           | East North Central |            -1.5 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |              3000 |                70000 |              50447 |         8.40733 | cold               |              nan |     20 | Evening   | False    |           1 |      5310903 |
| Minnesota    | MRO           | East North Central |            -0.1 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |              2550 |                68200 |              51598 |         8.40733 | normal             |              nan |      4 | Night     | False    |           1 |      5380443 |
| Minnesota    | MRO           | East North Central |             1.2 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |              1740 |               250000 |              54431 |         8.40733 | warm               |              250 |      2 | Night     | False    |           1 |      5489594 |



<br>


## Univariate Analysis

<iframe
  src="assets/output.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In this plot we are looking at the distribution of the probability of outage durations over time. We can see how most outages don't last that long, as they are in the \[0-999\] minutes bin. Over 50% of the durations are in that bin, but interestingly there are outages that last for a very long time. The longest outage had an duration of over 108,000 minutes. This means the outage lasted for 75+ days!


<iframe
  src="assets/pie_nerc.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In this plot we see the breakdown of the distribution of outages by NERC Region. The NERC Region is the North American Electric Reliability Corporation region involved in the outage event. The NERC Region with the highest amount of outages is WECC and RFC, which both have over 400 incidents.


<br>


### All NERC Regions and Their Outage Counts

| Acronym       | Full Name                                    | Count |
|---------------|---------------------------------------------|-------|
| WECC          | Western Electricity Coordinating Council    | 451   |
| RFC           | ReliabilityFirst Corporation                | 419   |
| SERC          | SERC Reliability Corporation                | 205   |
| NPCC          | Northeast Power Coordinating Council        | 150   |
| TRE           | Texas Reliability Entity                    | 111   |
| SPP           | Southwest Power Pool                        | 67    |
| MRO           | Midwest Reliability Organization            | 46    |
| FRCC          | Florida Reliability Coordinating Council     | 44    |
| ECAR          | East Central Area Reliability Coordination   | 34    |
| HECO          | Hawaiian Electric Company                   | 3     |
| FRCC, SERC    | Florida & SERC Combined Regions             | 1     |
| HI            | Hawaii Region                               | 1     |
| PR            | Puerto Rico                                 | 1     |
| ASCC          | Alaska Systems Coordinating Council         | 1     |



<br>

## Bivariate Analysis

<iframe
  src="assets/scatter_duration_customers.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

Here we can see the outage duration vs the number of customers affected by the outage. Most of the points are bunched in the lower left hand corner of the plot. Most outages don’t end up affecting more than 0.5M people and don’t last for more than around 15,000 minutes (~10 days). However, there are outliers, which although don’t last for a very large amount of time, affect millions of people. These are the larger hurricanes and storms that come through and rip up local power grids.


<br>


## Interesting Aggregates


| Daypart   |   OUTAGE.DURATION |
|:----------|------------------:|
| Afternoon |           1980.55 |
| Evening   |           2779.81 |
| Morning   |           3000.73 |
| Night     |           2992.26 |


Here we are seeing a pivot table of the average length of outages split across whether the part of the day was morning, afternoon, evening, or night. We can see that the longest durations are on average during the morning and very closely after at night. If we were to look at these by whether or not it is daytime or nighttime, we get that daytime is ~4981.26 minutes long on average and nighttime is ~5772.05 minutes long on average. Although on their own morning and night are the two longest, if we look at daytime and nighttime, nighttime has a much longer average duration. This may be due to power going out at night when many people are using it and overloading the grid. 

<br>

---

<br>

# Assessment of Missingness

<br>

## NMAR Analysis
There are many columns in the dataset that have missing values in their columns. However, we focused in on one of them. That column is `CUSTOMERS.AFFECTED`. This column with missing values can be thought of as Not Missing at Random (`NMAR`) if we think about the size of the values. Outages with a larger amount of customers affected would have more attention drawn to them. Outages with less customers affected may be more likely to go under the radar and have reporting companies not record the amount of data collected.

In this way, the missing values in `CUSTOMERS.AFFECTED` may be dependent on the values itself. This is clearly an example of `NMAR` data, where the missingness of the data cannot be predicted by another column, but rather the magnitude of the data itself.

If we had data on the amount of media coverage the outage got, we may be able to predict the missingness of the amount of customers affected. This would make the missingness Missing At Random (`MAR`), where the missingness can be predicted or is based on another column. If there wasn’t much media coverage for the outage we are looking for, we could more accurately predict that the value would be missing in the `CUSTOMERS.AFFECTED` column.

<br>

## Missingness Dependency

We will looking into and analyzing the `OUTAGE.DURATION` column


<br>

### 1. Missingness Dependency on Climate Regions.
Null Hypothesis: The missingness of the amount of customers affected is the same across all climate regions.

Alternative Hypothesis: The missingness of the amount of customers affected differs across the different climate regions.


| Climate Region       | Is NA    | Not NA   |
|-----------------------|----------|----------|
| Central              | 0.097065 | 0.144700 |
| East North Central   | 0.045147 | 0.108756 |
| Northeast            | 0.189616 | 0.245161 |
| Northwest            | 0.158014 | 0.057143 |
| South                | 0.167043 | 0.142857 |
| Southeast            | 0.022573 | 0.131797 |
| Southwest            | 0.106095 | 0.041475 |
| West                 | 0.191874 | 0.121659 |
| West North Central   | 0.022573 | 0.006452 |


<iframe
  src="assets/bar_climate_customer_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

There seems to be a noticeable difference between categories and missingness. We perform a permutation test to check for missingness dependency. We have a significance level of `p=0.05`. We will use total variation distance (TVD) as our test statistic, as we are dealing with categorical data points.

<iframe
  src="assets/hist_climate_perm.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

During testing, we found an observed test statistic of `0.28`, which has a p-value of `0.0`. It is far beyond the distribution of climate regions, showing that we reject the null hypothesis. We conclude that there is evidence to suggest that the missingness of missingness of customer amounts differs across climate regions. 


<br>

### 2. Missingness Dependency on the Part of Day

We now look to see if the missingness of the customers affected is dependent on the part of the day. The parts of the day we are splitting the data up into is Morning, Afternoon, Evening, and Night.

Null Hypothesis: The missingness of the amount of customers affected is the same across all parts of the day.

Alternative Hypothesis: The missingness of the amount of customers affected differs across the different parts of the day.

<iframe
  src="assets/bar_daypart_customer_missing.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

We can see here that the distributions of missingness seem much more even based on the parts of day that the climate regions. We can run another permutation test to statistically quantify whether or not the differences are significant or not. We will use the TVD again as our test statistic.

<iframe
  src="assets/hist_daypart_perm.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

In our testing, we find now that there is an observed test statistic of `0.18`, which has a p-value of `0.272`. This p-value is not lower than our significance level of `0.05`, so we fail to reject the null hypothesis. We do not have enough evidence to conclude that there is a relationship between the dependency of the missingness of the amount of customers affected and the part of the day.

<br>

---

<br>

# Hypothesis Testing

**Null Hypothesis**: The number of people affected is the same for outages during the daytime and nighttime.

**Alternative Hypothesis**: The number of people affected is greater for outages during the nighttime than outages during the daytime.

**Test Statistic**: Difference in group means

We performed a permutation Test with 10,000 iterations at the 0.05 significance level to generate an empirical distribution of the test statistic, as shown below:

<iframe
  src="assets/hist_hypo.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**P-value**: 0.0068. With our 0.05 significance level, we reject the null hypothesis. We have enough evidence to justify that the difference in people affected during the nighttime and daytime was statistically significant, and not solely due to random chance.

This test is relevant to our initial question because it provides some insight as to whether or not the time of day as an impact on the number of people affected. Rejecting the null in this instance tells us that one of the features we encoded will likely be useful for the model we build to help further investigate our question.

<br>

---

<br>

# Framing a Prediction Problem

To answer our question, we want to create a model that predicts whether or not a power outage will exceed a certain severity threshold – in this case, a day, or 1,440 minutes. We will construct this model as a binary classification problem, where outages will be predicted as “severe” (1) or “not severe” (0). 

For our model, our response variable is the `is_severe` column, which is a boolean variable that determines whether the difference between time of restoration and time of start is greater than a day. We chose this column because like we mentioned in our introduction, being able to anticipate severe outages can be very helpful. The metric we are using to measure the success of our model is accuracy. 

When building our model, it was important to take into consideration what variables we would know prior to the time of a hypothetical power outage, or the model wouldn't necessarily be helpful from a logistical standpoint.

<br>

---

<br>

# Baseline Model

Our baseline model is a binary classifier using a **RandomForest** that consists of the features `NERC.REGION` (categorical), `PC.REALGSP.STATE` (quantitative), and `PCT_WATER_TOT` (quantitative). We used a random forest and used GridSearchCV to help identify the best parameters, which were a `max_depth` of 8, `min_samples_split` of 15, and `n_estimators` of 100. The predicted column consisted of 1s if the predicted outage would be severe, and 0 if the predicted outage would not be severe. Initially, we used a DecisionTreeClassifier but realized that it would not be as successful as a RandomForestClassifier. 

`NERC.REGION` indicates the North American Electric Reliability Corporation regions involved in the outage event, which we thought would be useful due to different regions having different implementations of energy infrastructures. We one-hot encoded this column, which had 8 unique values in it. `NERC.REGION` was One-Hot Encoded for this model.

`PC.REALGSP.STATE` identifies per capita real gross state product (GSP) in the U.S. state the outage took place in. Higher values may correspond to larger investments into energy infrastructure and protection against intentional power outage attacks. `PC.REALGSP.STATE` was transformed using a StandardScaler.

`PCT_WATER_TOT` states the percentage of water area in the U.S. state as compared to the overall water area in the continental U.S. Higher values may equate to increased hydropower availability. `PCT_WATER_TOT` was transformed using a StandardScaler.

Our initial model had an R<sup>2</sup> of 0.76 on the training set and **0.73** on the test set. For a baseline model, this was a better score than we had expected, but we think the fact that we only used a few features makes us believe we can definitely improve the model.

<br>

---

<br>

# Final Model

Our final model still used a **RandomForest** with a few more added features, whilst removing the `PCT_WATER_TOT` column:

`Daypart` states the time of day based on the table in the Data Cleaning section. Outages that happen at night or evening may take longer to fix due to smaller number of people taking action against power outages.

`ANOMALY.LEVEL` represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season, which may impact the duration of a power outage due to extreme weather tendencies. 

`CLIMATE.CATEGORY` represents the climate episodes corresponding to the years as either “warm,” “cold,” or “normal,” which like anomaly level may add the impacts of the climate as a predictor. 

`DEMAND.LOSS.MW` states the amount of peak demand lost during an outage event (in Megawatt). Higher values may correspond to longer and more severe outages 

Our hyperparamters for the final model using GridSearchCV were a `max_depth` of 10, a `min_samples_split` of 7, and an `n_estimators` value of 65.

Our final model had an R<sup>2</sup> of 0.89 on the training set and **0.78** on the test set, which was a 5% increase from our baseline model. This boost shows a higher generalization to unseen data, with hyperparameter tuning that further optimizes the model’s ability to capture complex patterns without overfitting.

<br>

---

<br>

# Fairness Analysis

Our groups for the fairness analysis are populations that are less than 9,000,000 people and greater than or equal to 9,000,000 people. We did this because our model relies on the `PC.REAL.GSP` column, which has a positive relationship with the population of the state itself. We want to make sure that the model is effective in recognizing severe outages in both states with high populations and low populations.
When determining an evaluation metric, we decided on recall, because when considering the real-life effects of using this model, false negatives are the least ideal scenario– when a business or family is not prepared for an extended outage and as a result reap the consequences. 
We will conduct a permutation test with a significance level of 0.05, using absolute difference of means as our test statistic on the test data we used for our final model with the following hypotheses:



**Null Hypothesis**: 
Our final model’s recall is the same for populations with less than 9,000,000 people and populations with greater than 9,000,000 people.


**Alternate Hypothesis**: 
Our final model’s recall is not the same for populations with less than 9,000,000 people and populations with greater than 9,000,000 people.


The figure below shows the result of our permutation test with 10,000 simulations:


<iframe
  src="assets/fig_final.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>


**P-value**: 0.4381


We fail to reject the null hypothesis. We do not have conclusive evidence that our final model’s recall is not the same for populations with less than 9,000,000 people and populations with greater than 9,000,000 people.

<br>
