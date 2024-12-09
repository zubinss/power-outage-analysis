# Zubin Sannakkayala and Atherv Vidhate

Work will be added shortly

# Data Cleaning and Exploratory Data Analysis
## Data Cleaning


To clean our data, we had to start with out initial Excel file of data on the outages. We removed rows at the top of the Excel file talking about the source of the data.

<img width="671" alt="Screenshot 2024-12-08 at 10 04 45 PM" src="https://github.com/user-attachments/assets/f2502a1b-86a8-4566-b009-05c0a8f4ad75">

We then removed a row in the dataset showing the units of each of the columns so that we could have purely the data to work with. After that, we converted the Excel file into a CSV to be used easier in Python. 

We converted important date and time columns into `pd.Timestamp` objects. These columns were `OUTAGE.START.DATE`, `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.DATE`, and `OUTAGE.RESTORATION.TIME`. To make it easier to work all in one with these data columns, we also combined these 4 columns into 2 columns, `OUTAGE.START` and `OUTAGE.RESTORATION`, respectively.

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
