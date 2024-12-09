# Zubin Sannakkayala and Atherv Vidhate

Work will be added shortly

# Data Cleaning and Exploratory Data Analysis
## Data Cleaning


To clean our data, we had to start with out initial Excel file of data on the outages. We removed rows at the top of the Excel file talking about the source of the data.

<img width="671" alt="Screenshot 2024-12-08 at 10 04 45 PM" src="https://github.com/user-attachments/assets/f2502a1b-86a8-4566-b009-05c0a8f4ad75">

We then removed a row in the dataset showing the units of each of the columns so that we could have purely the data to work with. After that, we converted the Excel file into a CSV to be used easier in Python. 

We converted important date and time columns into `pd.Timestamp` objects. These columns were 'OUTAGE.START.DATE', 'OUTAGE.START.TIME', 'OUTAGE.RESTORATION.DATE', and 'OUTAGE.RESTORATION.TIME'. To make it easier to work all in one with these data columns, we also combined these 4 columns into 2 columns, 'OUTAGE.START' and 'OUTAGE.RESTORATION', respectively.
