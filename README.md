# Exploring the use of customer data in predicting and preventing customer churn in a TELCO
## Data Analytics project - Telecommunications
## Table of Contents
- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Cleaning/Preparation](#data-cleaningpreparation)   <!-- the slash is removed -->
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Data Analysis](#data-analysis)
- [PRACTICAL RECOMMENDATIONS](#practical-recommendations)
- [Analysis Limitations](#analysis-limitations)
- [References](#references)

### Project Overview
This project explores the use of a Telecom provider's customer data to predict customer churn and recommend strategies.
Leveraging on the Telco customer data to provide insights into ways of adopting customer retention strategies.
Using techniques such as K-Nearest Neighbours to make classifications and predictions of telco customer behaviours and the likelihood of customer’s exit from telco service.

### Data Sources
Using SQL, data was extracted from the Telco’s enterprise database across three entities—Customer (Customer Info), Billing/Payment, and Service Usage. Records were joined using the shared unique customer_id (Account Key), conformed to the data model, materialized as a curated table, and exported to Customer_Data.csv.
### Tools 
- Python (Gooogle Colab) - Data Cleaning and Analysis
- Power BI - Report Visualisation

### Data Cleaning / Preparation
In this stage, the following steps were completed:

1. **Extracted from enterprice data base usine SQL** Data was extracted from the enterprise data base across the *customer(customer info)*, *Account(billing & payments)*, and *Service Usage(events)* Entities.
2. **Joined on shared keys** The three entities were joined on the shared customer_id key to build a clean **star-schema** data model for analytical use
4. **Profiled the data** (row counts, nulls, types, distincts) to spot quality issues early.
5. **Standardised columns** — consistent names, data types (dates, numerics, categoricals), and formats.
6. **Removed/Masked PII** (personally identifiable information) to meet privacy and ethical guidelines.
7. **Handled missing values** — dropped, imputed, or flagged depending on business relevance.
8. **Deduplicated and cleaned errors/outliers** (obvious typos, impossible values, negative amounts where not allowed).
9. **Created useful features** — e.g., billing month, usage buckets, tenure, ARPU/average spend, late-payment flags.
10. **Validated joins and totals** — cross-checked counts and key integrity after transformations.
11. **Materialised the curated dataset** to `Customer_Data.csv` for downstream analysis and Power BI reporting.


### Exploratory Data Analysis
EDA involved exploring the Online Customer Reviews' Data (OCRs) to answer key questions such as:

1. Which customer data points most predictive of customer satisfaction and operational efficiency in British Airways short-haul flights?
2. What are the primary factors influencing customer recommendations of British Airways’ short-haul services?
3. Can operational efficiency and customer satisfaction be improved if customers data is reviewed and analysed?
4. What actionable recommendations can be derived from this study for British Airways, and what are the implications for future research?

### Data Analysis
```python
1. Inspection of the data
#Reading the data using pandas
df = pd.read_csv('/British Airways-Final-Data-Skytrax.csv')
#observing the data shape
df.shape
(3850, 16)

2. Identifying Short Haul Rotes from the data
#Creating Short Haul Cities List
short_haul_cities = [
    "Dublin", "Edinburgh", "Manchester", "Belfast", "Glasgow", "Cardiff", "Bristol", "Newcastle", "Cork", "Aberdeen",
    "Paris", "Amsterdam", "Brussels", "Luxembourg City", "Frankfurt", "Berlin", "Munich", "Zurich", "Geneva",
    "Vienna", "Düsseldorf", "Hamburg", "Madrid", "Barcelona", "Lisbon", "Porto", "Milan", "Rome", "Venice", "Florence",
    "Nice", "Marseille", "Copenhagen", "Oslo", "Stockholm", "Gothenburg", "Helsinki", "Prague", "Malta", "Tenerife","Dublin",
"Edinburgh", "Manchester", "Belfast","Glasgow","Cardiff","Bristol","Newcastle","Cork","Aberdeen","Paris","Amsterdam","Brussels","Luxembourg City","Frankfurt","Berlin",
"Munich","Zurich","Geneva","Vienna","Düsseldorf","Hamburg","Madrid","Barcelona","Lisbon","Porto",
"Milan","Rome","Venice","Florence","Nice","Marseille","Copenhagen","Oslo","Stockholm","Gothenburg","Helsinki","Prague","Budapest","Warsaw","Krakow","Bratislava",
"Ljubljana","Reykjavik","Malta","Faro","Tenerife"]

#Creation of Ishort_haul DataFrame containing only short-haul Flights data
def is_short_haul(Route):

    for city in short_haul_cities:
        if city in Route:
            return True
    return False

df_Ishort_haul = df[df['Route'].apply(is_short_haul)]

df_Ishort_haul.to_csv("/British Airways-Final-Data-Skytrax.csv", index=False)

#Observing shape of the short_haul data extracted from the initial data list
df_Ishort_haul.shape
(770, 15) #Our data has been reduced from 3,850 rows to 770 rows (only short-haul flights).
msno.matrix(df_Ishort_haul)
```
![msno matrix](missingno_matrix.png)

```python
#The bank rows are replaced with mean of each column and 
df_Ishort_haul['Wifi & Connectivity'].fillna(df_Ishort_haul['Wifi & Connectivity'].mean(), inplace=True)
df_Ishort_haul['Inflight Entertainment'].fillna(df_Ishort_haul['Inflight Entertainment'].mean(), inplace=True)
df_Ishort_haul['Food & Beverages'].fillna(df_Ishort_haul['Food & Beverages'].mean(), inplace=True)
df_Ishort_haul['Seat Comfort'].fillna(df_Ishort_haul['Seat Comfort'].mean(), inplace=True)
df_Ishort_haul['Cabin Staff Service'].fillna(df_Ishort_haul['Cabin Staff Service'].mean(), inplace=True)
df_Ishort_haul['Ground Service'].fillna(df_Ishort_haul['Ground Service'].mean(), inplace=True)

```

### Pearson Correlation Coefficient
Columns that are not relevant to the T-tests and Pearson correlation coefficient Analysis would be deleted from the df_Ishort_haul DataFrame

```python
df_Ishort_haul = df_Ishort_haul.drop(columns=['S/N','reviewer_detail', 'reviews','Type Of Traveller', 'Route', 'Date Flown', 'Aircraft','Seat Type']
```
![Cleaned Data Overview](missingno_matrix2.png)

```Python
correlation_result = df_Ishort_haul.corr()['Recommended'].sort_values(ascending=False)
print(correlation_result)

Recommended               1.000000
Ground Service            0.675204
Seat Comfort              0.604414
Cabin Staff Service       0.598581
Food & Beverages          0.552210
Inflight Entertainment    0.288967
Wifi & Connectivity       0.238077
Name: Recommended, dtype: float64
```
![Graph](corr_graph..png)

Result indicates that improvement in all the service factors would result to improvement of customer recommendations (Which is a proxy to satisfaction). All the service factors show positive correlation.

However, prioritising the most correlated factors would optimise cost and increase efficiency. This is because some service factors are less correlated than others.
The highest correlation comes from Ground service while the lowest is from Wifi & Connectivity.

## PRACTICAL RECOMMENDATIONS
Based on the findings, the following are recommended to British Airways' for Short Haul Flights.

- Ground Service: Allocate resources to improve ground services, such as upgrading mobile apps for seamless check-in, enhancing lounge facilities, and providing better accessibility for passengers with disabilities. These improvements would make Ground Services very satisfactory.

- Cabin staff service: Invest in continuous training for cabin staff to improve customer interactions and service quality.

- Seat Comfort: Enhance seat designs and comfort to meet customer expectations.

- Inflight Entertainment and Wi-Fi Connectivity: While these factors are less significant, they should still be maintained at a satisfactory level, but with lower priority compared to other services.

### Analysis Limitations
This study has provided valuable insights into customer satisfaction and operational efficiency for British Airways. However, some of the research limitations includes:

- Incomplete Data: Some columns had missing data, which were filled with mean values, potentially impacting the analysis.
- Text Data Sample Size: Limitation of the sample size of data might limit the generalisability of the findings.

### References
Skytrax (2024), British Airways Customer reviews. Available at: https://www.airlinequality.com/airline-reviews/british-airways (Accessed: 10 June 2024)

