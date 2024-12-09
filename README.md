# Zubin Sannakkayala and Atherv Vidhate

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


## Interesting Aggregates


---

# Assessment of Missingness

## NMAR Analysis
There are many columns in the dataset that have missing values in their columns. However, we focused in on one of them. That column is `CUSTOMERS.AFFECTED`. This column with missing values can be thought of as Not Missing at Random (`NMAR`) if we think about the size of the values. Outages with a larger amount of customers affected would have more attention drawn to them. Outages with less customers affected may be more likely to go under the radar and have reporting companies not record the amount of data collected.

In this way, the missing values in `CUSTOMERS.AFFECTED` may be dependent on the values itself. This is clearly an example of `NMAR` data, where the missingness of the data cannot be predicted by another column, but rather the magnitude of the data itself.

If we had data on the amount of media coverage the outage got, we may be able to predict the missingness of the amount of customers affected. This would make the missingness Missing At Random (`MAR`), where the missingness can be predicted or is based on another column. If there wasn’t much media coverage for the outage we are looking for, we could more accurately predict that the value would be missing in the `CUSTOMERS.AFFECTED` column.


## Missingness Dependency


---


# Hypothesis Testing

**Null Hypothesis**: The number of people affected is the same for outages during the daytime and nighttime.

**Alternative Hypothesis**: The number of people affected is greater for outages during the nighttime than outages during the daytime.

**Test Statistic**: Difference in group means

We performed a permutation Test with 10,000 iterations at the 0.05 significance level to generate an empirical distribution of the test statistic, as shown below:


**P-value**: 0.0068. With our 0.05 significance level, we reject the null hypothesis. We have enough evidence to justify that the difference in people affected during the nighttime and daytime was statistically significant, and not solely due to random chance.


