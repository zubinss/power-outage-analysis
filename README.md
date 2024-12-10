# Introduction

# Data Cleaning and Exploratory Data Analysis

## Data Cleaning


To clean our data, we had to start with out initial Excel file of data on the outages. We removed rows at the top of the Excel file talking about the source of the data.

<img width="671" alt="Screenshot 2024-12-08 at 10 04 45 PM" src="https://github.com/user-attachments/assets/f2502a1b-86a8-4566-b009-05c0a8f4ad75">

We then removed a row in the dataset showing the units of each of the columns so that we could have purely the data to work with. After that, we converted the Excel file into a CSV to be used easier in Python. 

We converted important date and time columns into `pd.Timestamp` objects. These columns were `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, and `OUTAGE.RESTORATION.TIME`. To make it easier to work all in one with these data columns, we also combined these 4 columns into 2 columns, `OUTAGE.START` and `OUTAGE.RESTORATION`, respectively.

---

## Univariate Analysis

(insert histogram plot here)

In this plot we are looking at the distribution of the probability of outage durations over time. We can see how most outages don't last that long, as they are in the \[0-999\] minutes bin. Over 50% of the durations are in that bin, but interestingly there are outages that last for a very long time. The longest outage had an duration of over 108,000 minutes. This means the outage lasted for 75+ days!


(insert pie chart here)

In this plot we see the breakdown of the distribution of outages by NERC Region. The NERC Region is the North American Electric Reliability Corporation region involved in the outage event. The NERC Region with the highest amount of outages is WECC and RFC, which both have over 400 incidents.

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


## Bivariate Analysis

(insert scatter plot here)

Here we can see the outage duration vs the number of customers affected by the outage. Most of the points are bunched in the lower left hand corner of the plot. Most outages don’t end up affecting more than 0.5M people and don’t last for more than around 15,000 minutes (~10 days). However, there are outliers, which although don’t last for a very large amount of time, affect millions of people. These are the larger hurricanes and storms that come through and rip up local power grids.



## Interesting Aggregates

(insert pivot table here)

Here we are seeing a pivot table of the average length of outages split across whether the part of the day was morning, afternoon, evening, or night. We can see that the longest durations are on average during the morning and very closely after at night. If we were to look at these by whether or not it is daytime or nighttime, we get that daytime is ~4981.26 minutes long on average and nighttime is ~5772.05 minutes long on average. Although on their own morning and night are the two longest, if we look at daytime and nighttime, nighttime has a much longer average duration. This may be due to power going out at night when many people are using it and overloading the grid. 

---

# Assessment of Missingness

## NMAR Analysis
There are many columns in the dataset that have missing values in their columns. However, we focused in on one of them. That column is `CUSTOMERS.AFFECTED`. This column with missing values can be thought of as Not Missing at Random (`NMAR`) if we think about the size of the values. Outages with a larger amount of customers affected would have more attention drawn to them. Outages with less customers affected may be more likely to go under the radar and have reporting companies not record the amount of data collected.

In this way, the missing values in `CUSTOMERS.AFFECTED` may be dependent on the values itself. This is clearly an example of `NMAR` data, where the missingness of the data cannot be predicted by another column, but rather the magnitude of the data itself.

If we had data on the amount of media coverage the outage got, we may be able to predict the missingness of the amount of customers affected. This would make the missingness Missing At Random (`MAR`), where the missingness can be predicted or is based on another column. If there wasn’t much media coverage for the outage we are looking for, we could more accurately predict that the value would be missing in the `CUSTOMERS.AFFECTED` column.


## Missingness Dependency

We will looking into and analyzing the `OUTAGE.DURATION` column

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


(insert 1st bar chart here)

There seems to be a noticeable difference between categories and missingness. We perform a permutation test to check for missingness dependency. We have a significance level of `p=0.05`. We will use total variation distance (TVD) as our test statistic, as we are dealing with categorical data points.

(insert 1st permutation graph)

During testing, we found an observed test statistic of `0.28`, which has a p-value of `0.0`. It is far beyond the distribution of climate regions, showing that we reject the null hypothesis. We conclude that there is evidence to suggest that the missingness of missingness of customer amounts differs across climate regions. 

### 2. Missingness Dependency on the Part of Day

We now look to see if the missingness of the customers affected is dependent on the part of the day. The parts of the day we are splitting the data up into is Morning, Afternoon, Evening, and Night.

Null Hypothesis: The missingness of the amount of customers affected is the same across all parts of the day.

Alternative Hypothesis: The missingness of the amount of customers affected differs across the different parts of the day.

(insert second bar chart here)

We can see here that the distributions of missingness seem much more even based on the parts of day that the climate regions. We can run another permutation test to statistically quantify whether or not the differences are significant or not. We will use the TVD again as our test statistic.

(insert 2nd permutation graph)

In our testing, we find now that there is an observed test statistic of `0.18`, which has a p-value of `0.272`. This p-value is not lower than our significance level of `0.05`, so we fail to reject the null hypothesis. We do not have enough evidence to conclude that there is a relationship between the dependency of the missiningness of the amount of customers affected and the part of the day.


---


# Hypothesis Testing

**Null Hypothesis**: The number of people affected is the same for outages during the daytime and nighttime.

**Alternative Hypothesis**: The number of people affected is greater for outages during the nighttime than outages during the daytime.

**Test Statistic**: Difference in group means

We performed a permutation Test with 10,000 iterations at the 0.05 significance level to generate an empirical distribution of the test statistic, as shown below:


**P-value**: 0.0068. With our 0.05 significance level, we reject the null hypothesis. We have enough evidence to justify that the difference in people affected during the nighttime and daytime was statistically significant, and not solely due to random chance.


---


# Framing a Prediction Problem

To answer our question, we want to create a model that predicts whether or not a power outage will exceed a certain severity threshold – in this case, a day, or 1,440 minutes. We will construct this model as a binary classification problem, where outages will be predicted as “severe” (1) or “not severe” (0). 

For our model, our response variable is the `OUTAGE.DURATION` column, which is the difference between time of restoration and time of start, in minutes. The metric we are using to measure the success of our model is accuracy. 


---


# Baseline Model

Our baseline model is a binary classifier that consists of the features `NERC.REGION`, `PC.REALGSP.STATE`, and `PCT_WATER_TOT`. We used a random forest and used GridSearchCV to help identify the best parameters, which were a `max_depth` of 8, `min_samples_split` of 15, and `n_estimators` of 100. The predicted column consisted of 1s if the predicted outage would be severe, and 0 if the predicted outage would not be severe. Initially, we used a DecisionTreeClassifier but realized that it would not be as successful as a RandomForestClassifier. 

Our initial model had an R^2 of 0.76 on the training set and **0.73** on the test set. 



---


# Final Model


---


# Fairness Analysis





