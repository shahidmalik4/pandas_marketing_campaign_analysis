<div class="cell markdown">

## # Marketing Campaign Analysis

## Overview

Imagine that Freedom ran a recent marketing campaign to promote the value proposition of how the debt relief program helps people achieve financial freedom. Suppose the cost of this campaign was $6 million and five months of data is provided. Let’s say campaign took place over the course of the third month. You now want to show the marketing, sales and operations teams just how successful this campaign was.

## Objectives

1. Provide a quantitative assessment of whether the marketing campaign was successful.

2. Based on the provided data, recommend ways the campaign strategy canalso be applied in the future to improve performance.

</div>

<div class="cell markdown">

## # Data Preprocessing

</div>

<div class="cell code" execution_count="268">

``` python
# import dependencies
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
```

</div>

<div class="cell code" execution_count="269">

``` python
# read in the data
calendar_df = pd.read_csv('./datasets/calendar.csv')
client_df = pd.read_csv('./datasets/clients.csv')
deposit_df = pd.read_csv('./datasets/deposits.csv')
```

</div>

<div class="cell code" execution_count="270">

``` python
# view client data
client_df.head()
```

<div class="output execute_result" execution_count="270">

             client_id client_geographical_region client_residence_status  \
    0  538839486596724                  Northeast                    Rent   
    1  321708286091707                       West                     Own   
    2  848531901757235                    Midwest                     Own   
    3  854405182328779                  Northeast                     Own   
    4  769102176031316                       West                     Own   

       client_age  
    0          91  
    1          83  
    2          84  
    3          83  
    4          85  

</div>

</div>

<div class="cell code" execution_count="271">

``` python
# view deposit data
deposit_df.head()
```

<div class="output execute_result" execution_count="271">

             client_id       deposit_type  deposit_amount deposit_cadence  \
    0  446495122764671     Actual Deposit           303.0         Monthly   
    1  446495122764671     Actual Deposit           303.0         Monthly   
    2  446495122764671  Scheduled Deposit           303.0         Monthly   
    3  446495122764671  Scheduled Deposit           303.0         Monthly   
    4  446495122764671  Scheduled Deposit           303.0         Monthly   

      deposit_date  
    0   2019-10-23  
    1   2019-09-23  
    2   2019-09-23  
    3   2019-10-23  
    4   2019-06-23  

</div>

</div>

<div class="cell code" execution_count="272">

``` python
# View calendar data
calendar_df.head()
```

<div class="output execute_result" execution_count="272">

      gregorian_date month_name
    0     2019-06-01    Month 1
    1     2019-06-02    Month 1
    2     2019-06-03    Month 1
    3     2019-06-04    Month 1
    4     2019-06-05    Month 1

</div>

</div>

<div class="cell code" execution_count="273">

``` python
# Merge the data
df = pd.merge(client_df, deposit_df, on='client_id')
df = pd.merge(df, calendar_df, left_on='deposit_date', right_on="gregorian_date")
df.head()
```

<div class="output execute_result" execution_count="273">

             client_id client_geographical_region client_residence_status  \
    0  538839486596724                  Northeast                    Rent   
    1  773610304672603                       West                    Rent   
    2  773610304672603                       West                    Rent   
    3  552219454660531                       West                     Own   
    4   55107102575545                    Midwest                     Own   

       client_age       deposit_type  deposit_amount deposit_cadence deposit_date  \
    0          91     Actual Deposit         10000.0           Extra   2019-07-11   
    1          24  Scheduled Deposit           100.0        Biweekly   2019-07-11   
    2          24     Actual Deposit           100.0        Biweekly   2019-07-11   
    3          85  Scheduled Deposit           290.0        Biweekly   2019-07-11   
    4          83  Scheduled Deposit           438.0         Monthly   2019-07-11   

      gregorian_date month_name  
    0     2019-07-11    Month 2  
    1     2019-07-11    Month 2  
    2     2019-07-11    Month 2  
    3     2019-07-11    Month 2  
    4     2019-07-11    Month 2  

</div>

</div>

<div class="cell code" execution_count="274">

``` python
# Drop greogorian date column
df = df.drop(columns=['gregorian_date'])

# View the data
df.head()
```

<div class="output execute_result" execution_count="274">

             client_id client_geographical_region client_residence_status  \
    0  538839486596724                  Northeast                    Rent   
    1  773610304672603                       West                    Rent   
    2  773610304672603                       West                    Rent   
    3  552219454660531                       West                     Own   
    4   55107102575545                    Midwest                     Own   

       client_age       deposit_type  deposit_amount deposit_cadence deposit_date  \
    0          91     Actual Deposit         10000.0           Extra   2019-07-11   
    1          24  Scheduled Deposit           100.0        Biweekly   2019-07-11   
    2          24     Actual Deposit           100.0        Biweekly   2019-07-11   
    3          85  Scheduled Deposit           290.0        Biweekly   2019-07-11   
    4          83  Scheduled Deposit           438.0         Monthly   2019-07-11   

      month_name  
    0    Month 2  
    1    Month 2  
    2    Month 2  
    3    Month 2  
    4    Month 2  

</div>

</div>

<div class="cell code" execution_count="275">

``` python
# Show columns with values that are null
df.isnull().sum()
```

<div class="output execute_result" execution_count="275">

    client_id                     0
    client_geographical_region    0
    client_residence_status       0
    client_age                    0
    deposit_type                  0
    deposit_amount                0
    deposit_cadence               0
    deposit_date                  0
    month_name                    0
    dtype: int64

</div>

</div>

<div class="cell markdown">

## # Exploratory Data Analysis

</div>

<div class="cell markdown">

## Client Activity

</div>

<div class="cell code" execution_count="276">

``` python
# Print unique active clients each month
print(df.groupby('month_name').client_id.count())

# Print a space
print('')

# Print total number of unique clients
print(f'Total number of unique clients: {len(df.client_id.unique())}')
```

<div class="output stream stdout">

    month_name
    Month 1     77827
    Month 2     80205
    Month 3    108505
    Month 4    107494
    Month 5    106363
    Name: client_id, dtype: int64

    Total number of unique clients: 46347

</div>

</div>

<div class="cell code" execution_count="277">

``` python
# Graph unique active clients each month
df.groupby('month_name').client_id.count().plot(kind='bar', title='Unique Active Clients Each Month')
```

<div class="output execute_result" execution_count="277">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8f38f32910>

</div>

<div class="output display_data">

![](cf69881b2a219f496b70e6f30fbafcf48223b98e.png)

</div>

</div>

<div class="cell markdown">

## Regional Analysis

</div>

<div class="cell code" execution_count="278">

``` python
# Break down data by region
df.groupby('client_geographical_region').client_id.count()
```

<div class="output execute_result" execution_count="278">

    client_geographical_region
    Midwest       88185
    Northeast     78576
    South        108004
    West         205629
    Name: client_id, dtype: int64

</div>

</div>

<div class="cell code" execution_count="279">

``` python
# Calculate the month over month increase in active clients per region
df.groupby(['client_geographical_region', 'month_name']).client_id.count().unstack().pct_change(axis='columns') * 100
```

<div class="output execute_result" execution_count="279">

    month_name                  Month 1   Month 2    Month 3   Month 4   Month 5
    client_geographical_region                                                  
    Midwest                         NaN  3.398435  34.776679  0.480648 -0.745217
    Northeast                       NaN  3.151110  38.154748 -1.200514 -1.418560
    South                           NaN  2.167886  31.361589 -1.850781 -0.761849
    West                            NaN  3.356436  36.530015 -0.950134 -1.193106

</div>

</div>

<div class="cell code" execution_count="280">

``` python
# Store the month over month increase in active clients per region in a variable
month_over_month_increase = df.groupby(['client_geographical_region', 'month_name']).client_id.count().unstack().pct_change(axis='columns') * 100

# Graph the month over month increase in active clients per region
month_over_month_increase.plot(kind='bar', title='Month Over Month Increase in Active Clients Per Region')

```

<div class="output execute_result" execution_count="280">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8f1ff94f50>

</div>

<div class="output display_data">

![](646cc33feaaa1985114d81ad91a6db217cfbb9a6.png)

</div>

</div>

<div class="cell code" execution_count="281">

``` python
# View client residence data
df['client_residence_status'].value_counts()
```

<div class="output execute_result" execution_count="281">

    Own     345432
    Rent    134962
    Name: client_residence_status, dtype: int64

</div>

</div>

<div class="cell code" execution_count="282">

``` python
# Calculate the month over month change in client residence status per region
df.groupby(['client_geographical_region', 'client_residence_status', 'month_name']).client_id.count().unstack().pct_change(axis='columns')*100
```

<div class="output execute_result" execution_count="282">

    month_name                                          Month 1   Month 2  \
    client_geographical_region client_residence_status                      
    Midwest                    Own                          NaN  2.707711   
                               Rent                         NaN  4.899329   
    Northeast                  Own                          NaN  3.362723   
                               Rent                         NaN  2.417348   
    South                      Own                          NaN  1.301083   
                               Rent                         NaN  3.675577   
    West                       Own                          NaN  2.814803   
                               Rent                         NaN  4.995127   

    month_name                                            Month 3   Month 4  \
    client_geographical_region client_residence_status                        
    Midwest                    Own                      37.890938 -0.109043   
                               Rent                     28.150992  1.830587   
    Northeast                  Own                      38.147193 -1.493395   
                               Rent                     38.181187 -0.175835   
    South                      Own                      35.048703 -3.568009   
                               Rent                     25.095224  1.299918   
    West                       Own                      38.543788 -1.749922   
                               Rent                     30.563936  1.564166   

    month_name                                           Month 5  
    client_geographical_region client_residence_status            
    Midwest                    Own                     -0.953351  
                               Rent                    -0.277823  
    Northeast                  Own                     -1.355685  
                               Rent                    -1.635632  
    South                      Own                     -0.529521  
                               Rent                    -1.167630  
    West                       Own                     -1.392645  
                               Rent                    -0.586279  

</div>

</div>

<div class="cell code" execution_count="283">

``` python
# Store the month over month change in client residence status per region in a variable
month_over_month_residence = df.groupby(['client_geographical_region', 'client_residence_status', 'month_name']).client_id.count().unstack().pct_change(axis='columns')*100

# Graph the month over month change in client residence status per region
month_over_month_residence.plot(kind='bar', title='Month Over Month Change in Client Residence Status Per Region')
```

<div class="output execute_result" execution_count="283">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8ed84fe150>

</div>

<div class="output display_data">

![](be0b17daaa655e39c43ad88cd4f24aa0f8d21f52.png)

</div>

</div>

<div class="cell markdown">

## Client Age Analysis

</div>

<div class="cell code" execution_count="284">

``` python
# View client age data
df['client_age'].describe()
```

<div class="output execute_result" execution_count="284">

    count    480394.000000
    mean         52.192469
    std          13.773928
    min          21.000000
    25%          41.000000
    50%          52.000000
    75%          62.000000
    max         105.000000
    Name: client_age, dtype: float64

</div>

</div>

<div class="cell code" execution_count="285">

``` python
# View client age as histogram
df['client_age'].hist()
```

<div class="output execute_result" execution_count="285">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8ed84effd0>

</div>

<div class="output display_data">

![](dc4e79dc908fa778466a3646bab72dabd5661891.png)

</div>

</div>

<div class="cell markdown">

# Client Deposit Analysis

</div>

<div class="cell code" execution_count="286">

``` python
# View month over month deposit type by region
df.groupby(['client_geographical_region', 'deposit_type', 'month_name']).deposit_type.count().unstack().pct_change(axis='columns')*100
```

<div class="output execute_result" execution_count="286">

    month_name                                    Month 1   Month 2    Month 3  \
    client_geographical_region deposit_type                                      
    Midwest                    Actual Deposit         NaN  4.030738  34.076655   
                               Scheduled Deposit      NaN  2.799890  35.447263   
    Northeast                  Actual Deposit         NaN  3.733553  38.163945   
                               Scheduled Deposit      NaN  2.605210  38.146034   
    South                      Actual Deposit         NaN  2.651077  30.449983   
                               Scheduled Deposit      NaN  1.723771  32.207113   
    West                       Actual Deposit         NaN  3.705574  35.877770   
                               Scheduled Deposit      NaN  3.034884  37.134635   

    month_name                                     Month 4   Month 5  
    client_geographical_region deposit_type                           
    Midwest                    Actual Deposit     1.340956 -1.046261  
                               Scheduled Deposit -0.335140 -0.454950  
    Northeast                  Actual Deposit    -0.550838 -1.592430  
                               Scheduled Deposit -1.816204 -1.251661  
    South                      Actual Deposit    -0.380392 -0.650872  
                               Scheduled Deposit -3.196455 -0.866367  
    West                       Actual Deposit     0.058239 -1.249161  
                               Scheduled Deposit -1.876312 -1.140605  

</div>

</div>

<div class="cell code" execution_count="287">

``` python
# Store month over month deposit type by region in a variable
month_over_month_deposit = df.groupby(['client_geographical_region', 'deposit_type', 'month_name']).deposit_type.count().unstack().pct_change(axis='columns')*100

# Graph month over month deposit type by region
month_over_month_deposit.plot(kind='bar', title='Month Over Month Deposit Type by Region')
```

<div class="output execute_result" execution_count="287">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8f1a6c7b90>

</div>

<div class="output display_data">

![](9195b53ec0f4a450e60158d245b9115613ae9152.png)

</div>

</div>

<div class="cell code" execution_count="288">

``` python
# View the deposit type as a bar chart
df['deposit_type'].value_counts().plot(kind='bar')
```

<div class="output execute_result" execution_count="288">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8eeb4a0290>

</div>

<div class="output display_data">

![](6ad39aba28793a37fcf10dca7a057c1377ffb5b7.png)

</div>

</div>

<div class="cell code" execution_count="289">

``` python
# View month over month deposit cadence by region
df.groupby(['client_geographical_region', 'deposit_cadence', 'month_name']).deposit_cadence.count().unstack().pct_change(axis='columns')*100
```

<div class="output execute_result" execution_count="289">

    month_name                                  Month 1    Month 2    Month 3  \
    client_geographical_region deposit_cadence                                  
    Midwest                    Biweekly             NaN   3.225327  33.966883   
                               Extra                NaN  32.142857  51.351351   
                               Monthly              NaN   2.782752  34.955100   
    Northeast                  Biweekly             NaN   3.127102  37.691555   
                               Extra                NaN  24.342105  92.592593   
                               Monthly              NaN   2.675120  37.033133   
    South                      Biweekly             NaN   3.023699  28.291909   
                               Extra                NaN  17.803030  68.488746   
                               Monthly              NaN   1.169703  32.467779   
    West                       Biweekly             NaN   3.948517  34.641483   
                               Extra                NaN   6.846473  85.436893   
                               Monthly              NaN   2.834051  36.623578   

    month_name                                    Month 4   Month 5  
    client_geographical_region deposit_cadence                       
    Midwest                    Biweekly          2.214101 -0.315457  
                               Extra           -16.326531 -5.182927  
                               Monthly          -0.466779 -1.007783  
    Northeast                  Biweekly         -0.153919 -1.399265  
                               Extra           -17.582418 -1.666667  
                               Monthly          -1.516650 -1.428412  
    South                      Biweekly         -0.144270 -0.526316  
                               Extra           -12.213740 -6.086957  
                               Monthly          -2.647017 -0.749559  
    West                       Biweekly          1.007949 -0.869297  
                               Extra           -15.183246 -0.246914  
                               Monthly          -1.862014 -1.465427  

</div>

</div>

<div class="cell code" execution_count="290">

``` python
# Store month over month deposit cadence by region in a variable
month_over_month_cadence = df.groupby(['client_geographical_region', 'deposit_cadence', 'month_name']).deposit_cadence.count().unstack().pct_change(axis='columns')*100

# Graph month over month deposit cadence by region
month_over_month_cadence.plot(kind='bar', title='Month Over Month Deposit Cadence by Region')
```

<div class="output execute_result" execution_count="290">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8f4eac7ad0>

</div>

<div class="output display_data">

![](b8270f7ae992ca30a31a22c157542a41c9a9b197.png)

</div>

</div>

<div class="cell code" execution_count="291">

``` python
# Graph the deposit cadence each month
df.groupby('month_name').deposit_cadence.count().plot(kind='line', title='Deposit Cadence Each Month')
```

<div class="output execute_result" execution_count="291">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8f2e2abbd0>

</div>

<div class="output display_data">

![](5dea70d355bf718451814efa5214e58df8a2d765.png)

</div>

</div>

<div class="cell code" execution_count="292">

``` python
# Plot the number of unique clients by month
df.groupby('month_name').client_id.nunique().plot(kind='line', title='Number of Unique Clients by Month')
```

<div class="output execute_result" execution_count="292">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8eeb57a550>

</div>

<div class="output display_data">

![](da4e116db582f1dd528ec8d92e4dbc26171e458d.png)

</div>

</div>

<div class="cell code" execution_count="293">

``` python
# Plot the number of clients by month
df.groupby('month_name').client_id.count().plot(kind='line', title='Number of Clients by Month')
```

<div class="output execute_result" execution_count="293">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8eeb57a950>

</div>

<div class="output display_data">

![](cef9b82cf49c3e10105b0e32f0cc216a76fd2ff4.png)

</div>

</div>

<div class="cell code" execution_count="294">

``` python
# Plot the sum of deposits that occurred each month
df.groupby('month_name').deposit_amount.sum().plot(kind='line', title='Sum of Deposits by Month')
```

<div class="output execute_result" execution_count="294">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8f2e253c10>

</div>

<div class="output display_data">

![](f2bdd6225a133a3eb0fa3323d3cf74b703823a7c.png)

</div>

</div>

<div class="cell code" execution_count="295">

``` python
# View the change in deposits from month to month
df.groupby('month_name').deposit_amount.sum().diff()
```

<div class="output execute_result" execution_count="295">

    month_name
    Month 1           NaN
    Month 2     601600.41
    Month 3    9788625.83
    Month 4   -1159237.37
    Month 5    -434644.50
    Name: deposit_amount, dtype: float64

</div>

</div>

<div class="cell code" execution_count="296">

``` python
# Plot average deposit amount by month
df.groupby('month_name').deposit_amount.mean().plot(kind='line', title='Average Deposit Amount by Month')
```

<div class="output execute_result" execution_count="296">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8f2e24d090>

</div>

<div class="output display_data">

![](70b9ee8a679e03cec73184602c54845027e2bb40.png)

</div>

</div>

<div class="cell code" execution_count="297">

``` python
# Show summary statistics for deposit amount
df['deposit_amount'].describe()
```

<div class="output execute_result" execution_count="297">

    count    480394.000000
    mean        365.207130
    std         346.644434
    min           0.010000
    25%         200.000000
    50%         290.000000
    75%         452.000000
    max       30000.000000
    Name: deposit_amount, dtype: float64

</div>

</div>

<div class="cell code" execution_count="298">

``` python
# View the distribution of deposit amount
# Adjust the x-axis to see the distribution
df['deposit_amount'].plot.hist(bins=100, xlim=(0, 2500))
```

<div class="output execute_result" execution_count="298">

    <matplotlib.axes._subplots.AxesSubplot at 0x7f8ee8848190>

</div>

<div class="output display_data">

![](ac302fac1c4bfd2e25206accd6d3f479be28782c.png)

</div>

</div>

<div class="cell markdown">

## Question 1

### Determine if the Marketing Campaign was a Success

To determine the success of the marketing campaign the following metric
were evaluated:

-   Number of Unique Clients Per Month
-   Total Deposits Per Month
-   Deposit Cadence Per Month
-   Deposit Type Per Month

</div>

<div class="cell code" execution_count="357">

``` python
# Plot the deposit amount by month as a line graph
df.groupby('month_name').deposit_amount.sum().plot(kind='bar', title='Deposit Amount by Month')

plt.ylabel('Deposit Amount')
plt.xlabel('')

# Export the graph as a png
plt.savefig('./resources/images/deposit_amount_by_month.png', bbox_inches="tight")

# Show the graph
plt.show()

```

<div class="output display_data">

![](a11bff0515b0ba937b0b4caa9fb7958669f074d9.png)

</div>

</div>

<div class="cell code" execution_count="300">

``` python
# Print the sum of deposits each month
print(df.groupby('month_name').deposit_amount.sum())
```

<div class="output stream stdout">

    month_name
    Month 1    29284830.86
    Month 2    29886431.27
    Month 3    39675057.10
    Month 4    38515819.73
    Month 5    38081175.23
    Name: deposit_amount, dtype: float64

</div>

</div>

<div class="cell markdown">

On average, Freedom debt relief charges a fee if 21.5% of the total
amount of debt that they settle for their clients.

</div>

<div class="cell code" execution_count="301">

``` python
# Print the profit each month (deposit_amount * 0.215)
profit_per_month = df.groupby('month_name').deposit_amount.sum() * 0.215

# Print the profit each month
print(profit_per_month)
```

<div class="output stream stdout">

    month_name
    Month 1    6.296239e+06
    Month 2    6.425583e+06
    Month 3    8.530137e+06
    Month 4    8.280901e+06
    Month 5    8.187453e+06
    Name: deposit_amount, dtype: float64

</div>

</div>

<div class="cell code" execution_count="358">

``` python
# Plot the profit each month
profit_per_month.plot(kind='bar', 
                      title='Profit by Month',
                      ylabel='Profit (Millions)',
                      xlabel='')
        
# Export the graph as a png
plt.savefig('./resources/images/profit_by_month.png', bbox_inches="tight")

# Show the graph
plt.show()
```

<div class="output display_data">

![](536768542ea11fa29677345094d8d07733662a81.png)

</div>

</div>

<div class="cell code" execution_count="302">

``` python
# Find the difference in profit between month 3 and month 2
month_3_profit_increase = profit_per_month["Month 3"] - profit_per_month["Month 2"]

# Find the difference in profit between month 4 and month 2
month_4_profit_increase = profit_per_month["Month 4"] - profit_per_month["Month 2"]

# Find the difference in profit between month 5 and month 2
month_5_profit_increase = profit_per_month["Month 5"] - profit_per_month["Month 2"]

# Print the profit increase from before, during, and after marketing campaign
print(f'Profit increase between month 3 and month 2: {month_3_profit_increase:,.2f}')
print(f'Profit increase between month 4 and month 2: {month_4_profit_increase:,.2f}')
print(f'Profit increase between month 5 and month 2: {month_5_profit_increase:,.2f}')

# Determine average profit increase for months 3, 4, and 5
average_profit_increase = (month_3_profit_increase + month_4_profit_increase + month_5_profit_increase) / 3

# Print the average profit increase
print(f'Average profit increase: {average_profit_increase:,.2f} per month')

# Determine total profit for months 3, 4, and 5
total_profit_increase = month_3_profit_increase + month_4_profit_increase + month_5_profit_increase

# Print the total profit increase
print(f'Total profit increase: {total_profit_increase:,.2f} over 3 months')
```

<div class="output stream stdout">

    Profit increase between month 3 and month 2: 2,104,554.55
    Profit increase between month 4 and month 2: 1,855,318.52
    Profit increase between month 5 and month 2: 1,761,869.95
    Average profit increase: 1,907,247.67 per month
    Total profit increase: 5,721,743.02 over 3 months

</div>

</div>

<div class="cell markdown">

-   We can see that, on average, the marketing campaign boosted profits
    by rougly $1.9 million per month.

-   Over month's 3, 4, and 5 the total profit was rougly $5.7 million.
    Given that the cost of the marketing campaign was $5 million we can
    see a $700,000 increase.

-   To further understand the cost/benefits of the marketing campaign,
    lets take a look at total profits over a 12 month period with or
    without the campaign.

-   We will calculate the average profit pre and post campaign and use
    these values to determine how much we expect total profit to
    increase each month.

</div>

<div class="cell code" execution_count="303">

``` python
# Average profit for months 1 and 2
avg_profit_before = (profit_per_month["Month 1"] + profit_per_month["Month 2"]) / 2

# Average profit for months 3, 4, and 5
avg_profit_after = (profit_per_month["Month 3"] + profit_per_month["Month 4"] + profit_per_month["Month 5"]) / 3


# Print the average profit before and after the marketing campaign
print(f'Average profit before the marketing campaign: {avg_profit_before:,.2f}')
print(f'Average profit after the marketing campaign: {avg_profit_after:,.2f}')
```

<div class="output stream stdout">

    Average profit before the marketing campaign: 6,360,910.68
    Average profit after the marketing campaign: 8,332,830.40

</div>

</div>

<div class="cell code" execution_count="304">

``` python
# Create a dataframe with 12 rows and 2 columns
hypothetical_df = pd.DataFrame(np.zeros((12, 2)), columns=['no_campaign', 'campaign'])

# Set the starting value for no_campaign to 0
hypothetical_df['no_campaign'][0] = 0

# Set the starting value for campaign to -5,000,000
hypothetical_df['campaign'][0] = -5000000

# Set the value for month 1 for no campaign to 6,300,000 and campaign to the value of month 0 + 8,200,000
hypothetical_df['no_campaign'][1] = 6300000
hypothetical_df['campaign'][1] = hypothetical_df['campaign'][0] + 8200000

# Create a for loop to calculate the value for each month based on the previous month

for i in range(2, 12):
    hypothetical_df['no_campaign'][i] = hypothetical_df['no_campaign'][i-1] + 6300000
    hypothetical_df['campaign'][i] = hypothetical_df['campaign'][i-1] + 8200000

# Create a column for the difference in profit between the two scenarios
hypothetical_df['difference'] = hypothetical_df['campaign'] - hypothetical_df['no_campaign']

# Add 1 to each value in the index
hypothetical_df.index = hypothetical_df.index + 1

# Change the index name to month
hypothetical_df.index.name = 'month'

# Change values of month to Month 1, Month 2, etc.
hypothetical_df.index = hypothetical_df.index.map(lambda x: f'Month {x}')

# View hypothetical_df
hypothetical_df
```

<div class="output execute_result" execution_count="304">

              no_campaign    campaign  difference
    month                                        
    Month 1           0.0  -5000000.0  -5000000.0
    Month 2     6300000.0   3200000.0  -3100000.0
    Month 3    12600000.0  11400000.0  -1200000.0
    Month 4    18900000.0  19600000.0    700000.0
    Month 5    25200000.0  27800000.0   2600000.0
    Month 6    31500000.0  36000000.0   4500000.0
    Month 7    37800000.0  44200000.0   6400000.0
    Month 8    44100000.0  52400000.0   8300000.0
    Month 9    50400000.0  60600000.0  10200000.0
    Month 10   56700000.0  68800000.0  12100000.0
    Month 11   63000000.0  77000000.0  14000000.0
    Month 12   69300000.0  85200000.0  15900000.0

</div>

</div>

<div class="cell markdown">

We can see that by the 4th month the campaign is netting greater profit.
In addition, the yearly difference would be $15,900,000 if our
assumptions on profit per month are accurate.

</div>

<div class="cell code" execution_count="359">

``` python
# Plot the hypothetical_df
hypothetical_df.plot(
    kind='line',
    figsize=(10, 5), 
    title='Hypothetical Profit',
    ylabel='Total Profit (Millions)',
    xlabel='',
    ylim=(-5000000, 100000000),
    yticks=np.arange(-5000000, 100000000, 10000000),
    xlim=(0, 12),
    xticks=np.arange(0, 12, 1),
    rot=45,
    )

# Export the graph as a png
plt.savefig('./resources/images/hypothetical_profit.png', bbox_inches="tight")

plt.show()
```

<div class="output display_data">

![](7aa352ad3df11236dea0f0f9ed16b1458c94e9e5.png)

</div>

</div>

<div class="cell code" execution_count="360">

``` python
# Create a line graph of the number of clients each month
df.groupby('month_name').client_id.count().plot(kind='bar', title='Number of Clients by Month')

plt.ylabel('Number of Clients')
plt.xlabel('')

# Export the graph as a png
plt.savefig('./resources/images/number_of_clients_by_month.png', bbox_inches="tight")

# Show the graph
plt.show()
```

<div class="output display_data">

![](8902273bf247e378937481fc1295bc041f29b6c2.png)

</div>

</div>

<div class="cell code" execution_count="307">

``` python
# Create a variable that stores unique clients from months 1 and 2
month_1_2 = df[df['month_name'].isin(['Month 1', 'Month 2'])].client_id.unique()

# Create a variable that stores unique clients from month 3
month_3 = df[df['month_name'].isin(['Month 3'])].client_id.unique()


# Print values
print(f'Unique clients prior to the start of the marketing campaign {len(month_1_2)}')
print(f'Unique clients immediately after the marketing campaign: {len(month_3)}')

# Print difference in unique clients over the course of the marketing campaign
print(f'There was an increase of {len(month_3) - len(month_1_2)} clients over the course of the marketing campaign.')
```

<div class="output stream stdout">

    Unique clients prior to the start of the marketing campaign 34051
    Unique clients immediately after the marketing campaign: 43806
    There was an increase of 9755 clients over the course of the marketing campaign.

</div>

</div>

<div class="cell code" execution_count="308">

``` python
# Create a line graph of the number of deposits each month
df.groupby('month_name').deposit_amount.count().plot(kind='line', title='Number of Deposits by Month')

plt.ylabel('Number of Deposits')
plt.xlabel('')

plt.show()
```

<div class="output display_data">

![](977c51c6a6a68d57fabcc6c96a8a46f26bbfe5e0.png)

</div>

</div>

<div class="cell code" execution_count="309">

``` python
# Create a variable that stores unique deposits from months 1 and 2
month_1_2 = df[df['month_name'].isin(['Month 1', 'Month 2'])].deposit_amount.unique()

# Create a variable that stores unique deposits from month 3
month_3 = df[df['month_name'].isin(['Month 3'])].deposit_amount.unique()

# Print values
print(f'Unique deposits prior to the start of the marketing campaign {len(month_1_2)}')
print(f'Unique deposits immediately after the marketing campaign: {len(month_3)}')

# Print difference in unique deposits over the course of the marketing campaign
print(f'There was an increase of {len(month_3) - len(month_1_2)} deposits over the course of the marketing campaign.')
```

<div class="output stream stdout">

    Unique deposits prior to the start of the marketing campaign 1757
    Unique deposits immediately after the marketing campaign: 2090
    There was an increase of 333 deposits over the course of the marketing campaign.

</div>

</div>

<div class="cell code" execution_count="310">

``` python
# Create a line graph that visualizes the number of deposits by deposit type each month
df.groupby(['month_name', 'deposit_type']).deposit_type.count().unstack().plot(kind='line', title='Number of Deposits by Deposit Type Each Month')

plt.ylabel('Number of Deposits')
plt.xlabel('')

plt.show()
```

<div class="output display_data">

![](e479f9575245d7621bfd7f9ee17bac2eabe24d24.png)

</div>

</div>

<div class="cell markdown">

## Question 2

### How can the marketing campaign be improved?

Overall, the marketing campaign was very successful and showed
significant increases across the areas measured.

One way to improve the marketing campaign would be to identify
individuals who typically pay more and/or were likely to join the
service as a result of the campaign

</div>

<div class="cell code" execution_count="311">

``` python
# Bin clients by age
df['client_age'] = pd.cut(df['client_age'], 
                          bins=[0, 20, 30, 40, 50, 60, 70, 80, 90, 100], 
                          labels=['0-20', '21-30', '31-40', '41-50', '51-60', '61-70', '71-80', '81-90', '91-100'])

# Create a graph that visualizes how unique clients are stratified by age
df.groupby('client_age').client_id.nunique().plot(kind='bar', title='Number of Unique Clients by Age')

plt.ylabel('Number of Unique Clients')
plt.xlabel('')
plt.show()
```

<div class="output display_data">

![](3b82e48bd802f3fb3d6df30dc558cc852c48eec0.png)

</div>

</div>

<div class="cell code" execution_count="312">

``` python
# Create a graph that visualizes how total deposit amount are stratified by age over each month
df.groupby(['month_name', 'client_age']).deposit_amount.sum().unstack().plot(kind='line', title='Total Deposit Amount by Age Each Month')

# Move legend to the right
plt.legend(bbox_to_anchor=(1.05, 1), loc=2, borderaxespad=0.)

# Add labels
plt.ylabel('Total Deposit Amount')
plt.xlabel('')

# Show plot
plt.show()
```

<div class="output display_data">

![](229c8fdc5d46bad3ba88eb67fd0f2b8a78644636.png)

</div>

</div>

<div class="cell code" execution_count="328">

``` python
# Create a dataframe that shows the change in total deposit amount by age each month
age_df = df.groupby(['month_name', 'client_age']).deposit_amount.sum().unstack().diff()

age_df
```

<div class="output execute_result" execution_count="328">

    client_age  0-20      21-30       31-40       41-50       51-60       61-70  \
    month_name                                                                    
    Month 1      NaN        NaN         NaN         NaN         NaN         NaN   
    Month 2      0.0   36475.26   175969.91   205030.04    70677.07    68975.76   
    Month 3      0.0  552091.97  1820536.90  2540971.29  2396941.88  1506835.65   
    Month 4      0.0   27044.61  -149727.45  -292603.04  -337438.61  -252323.69   
    Month 5      0.0  -60984.83  -127508.48   -81693.69   -62890.53   -96875.20   

    client_age      71-80      81-90    91-100  
    month_name                                  
    Month 1           NaN        NaN       NaN  
    Month 2      21457.43    7141.00  16073.94  
    Month 3     826569.69  144394.39   -753.94  
    Month 4    -113422.51  -39031.68   -259.00  
    Month 5     -18353.12   14330.35  -1107.00  

</div>

</div>

<div class="cell code" execution_count="330">

``` python
# Create a dataframe that shows the percent change in total deposit amount by age each month
age_df_pct = df.groupby(['month_name', 'client_age']).deposit_amount.sum().unstack().pct_change() * 100

age_df_pct
```

<div class="output execute_result" execution_count="330">

    client_age  0-20      21-30      31-40      41-50      51-60      61-70  \
    month_name                                                                
    Month 1      NaN        NaN        NaN        NaN        NaN        NaN   
    Month 2      NaN   4.936282   4.101104   3.051522   0.924891   1.186887   
    Month 3      NaN  71.201200  40.757401  36.698167  31.079314  25.624444   
    Month 4      NaN   2.037276  -2.381426  -3.091440  -3.337909  -3.415643   
    Month 5      NaN  -4.502275  -2.077506  -0.890653  -0.643589  -1.357751   

    client_age      71-80      81-90     91-100  
    month_name                                   
    Month 1           NaN        NaN        NaN  
    Month 2      0.658963   0.926657  30.214173  
    Month 3     25.217979  18.565405  -1.088346  
    Month 4     -2.763525  -4.232660  -0.377992  
    Month 5     -0.459880   1.622690  -1.621717  

</div>

</div>

<div class="cell code" execution_count="313">

``` python
# Create a dataframe that shows the number of unique clients by age each month
client_age_df = df.groupby(['month_name', 'client_age']).client_id.nunique().unstack()

# View client_age_df
client_age_df
```

<div class="output execute_result" execution_count="313">

    client_age  0-20  21-30  31-40  41-50  51-60  61-70  71-80  81-90  91-100
    month_name                                                               
    Month 1        0   1145   5258   7269   7923   6125   3507    847      63
    Month 2        0   1214   5427   7394   8058   6219   3525    850      67
    Month 3        0   2061   7576   9987  10629   7973   4481   1014      82
    Month 4        0   1934   7060   9322   9899   7434   4170    945      73
    Month 5        0   1897   7039   9297   9881   7406   4177    945      73

</div>

</div>

<div class="cell code" execution_count="314">

``` python
# Calculate percent change in unique clients by age each month as a dataframe
percent_change = df.groupby(['month_name', 'client_age']).client_id.nunique().unstack().pct_change() *100

percent_change
```

<div class="output execute_result" execution_count="314">

    client_age  0-20      21-30      31-40      41-50      51-60      61-70  \
    month_name                                                                
    Month 1      NaN        NaN        NaN        NaN        NaN        NaN   
    Month 2      NaN   6.026201   3.214150   1.719631   1.703900   1.534694   
    Month 3      NaN  69.769357  39.598305  35.068975  31.906180  28.203891   
    Month 4      NaN  -6.162057  -6.810982  -6.658656  -6.868003  -6.760316   
    Month 5      NaN  -1.913133  -0.297450  -0.268183  -0.181837  -0.376648   

    client_age      71-80      81-90     91-100  
    month_name                                   
    Month 1           NaN        NaN        NaN  
    Month 2      0.513259   0.354191   6.349206  
    Month 3     27.120567  19.294118  22.388060  
    Month 4     -6.940415  -6.804734 -10.975610  
    Month 5      0.167866   0.000000   0.000000  

</div>

</div>

<div class="cell code" execution_count="334">

``` python
# Plot the client residence status each month
df.groupby(['month_name', 'client_residence_status']).client_id.count().unstack().plot(
    kind='line', 
    title='Number of Owners vs Renters Each Month',
    ylabel='Number of Clients',
    xlabel='',
    )

plt.show()
```

<div class="output display_data">

![](a08a4ef05a198439e10ef5108695147e1e3505a7.png)

</div>

</div>

<div class="cell code" execution_count="316">

``` python
# Calculate the percent change in client residence status each month
client_residence_status_df = df.groupby(['month_name', 'client_residence_status']).client_id.count().unstack().pct_change() * 100

# View client_residence_status_df
client_residence_status_df
```

<div class="output execute_result" execution_count="316">

    client_residence_status        Own       Rent
    month_name                                   
    Month 1                        NaN        NaN
    Month 2                   2.581073   4.253681
    Month 3                  37.651029  29.403841
    Month 4                  -1.779635   1.309560
    Month 5                  -1.139376  -0.828610

</div>

</div>

<div class="cell code" execution_count="317">

``` python
# Plot client residence status based on age
df.groupby(['client_age', 'client_residence_status']).client_id.count().unstack().plot(
    kind='bar',
    title='Client Residence Status by Age',
    ylabel='Number of Clients',
    xlabel='',
    )

plt.show()
```

<div class="output display_data">

![](01cee67044c481d9097525eab06ec8173807a5d1.png)

</div>

</div>

<div class="cell code" execution_count="337">

``` python
# View the number of owners between the ages of 21-30 at month 2
young_owners_before = df[(df['client_age'] == '21-30') & (df['client_residence_status'] == 'Own') & (df['month_name'] == 'Month 2')].client_id.count()

# Print the number of owners between the ages of 21-30 at month 2
print(f'There were {young_owners_before} owners between the ages of 21-30 at month 2.')

# View the number of owners between the ages of 21-30 at month 3
young_owners_after = df[(df['client_age'] == '21-30') & (df['client_residence_status'] == 'Own') & (df['month_name'] == 'Month 3')].client_id.count()

# Print the number of owners between the ages of 21-30 at month 3
print(f'There were {young_owners_after} owners between the ages of 21-30 at month 3.')
```

<div class="output stream stdout">

    There were 1359 owners between the ages of 21-30 at month 2.
    There were 2714 owners between the ages of 21-30 at month 3.

</div>

</div>

<div class="cell code" execution_count="318">

``` python
# Create graph of deposit types by age
df.groupby(['client_age', 'deposit_type']).deposit_type.count().unstack().plot(
    kind='bar', 
    title='Number of Deposits by Deposit Type by Age')

# Move legend to the right
plt.legend(bbox_to_anchor=(1.05, 1), loc=2, borderaxespad=0.)

# SHow plot
plt.show()
```

<div class="output display_data">

![](88e96fcf656cb65d8e6e09825bb6c38082934fd9.png)

</div>

</div>

<div class="cell code" execution_count="321">

``` python
# Create a variable that stores the total number of clients between the ages of 41 and 60 before teh marketing campaign
clients_before = df[(df['client_age'] >= '41-50') & (df['client_age'] <= '51-60') & (df['month_name'] == 'Month 2')].client_id.nunique()

# Create a variable that stores the total number of clients between the ages of 41 and 60 after the marketing campaign
clients_after = df[(df['client_age'] >= '41-50') & (df['client_age'] <= '51-60') & (df['month_name'] == 'Month 3')].client_id.nunique()

# Calculate the increase in clients between the ages of 41 and 60
clients_increase = clients_after - clients_before

# Calculate the total increase in clients after the marketing campaign
clients_total_increase = df[(df['month_name'] == 'Month 3')].client_id.nunique() - df[(df['month_name'] == 'Month 2')].client_id.nunique()

# Print the increase in clients between the ages of 41 and 60
print(f'The increase in clients between the ages of 41 and 60: {clients_increase}')

# Print the total increase in clients
print(f'The total increase in clients: {clients_total_increase}')

# Print the percentage of clients between the ages of 41 and 60
print(f'The percentage of clients between the ages of 41 and 60: {round(clients_increase / clients_total_increase * 100, 2)}%')
```

<div class="output stream stdout">

    The increase in clients between the ages of 41 and 60: 5164
    The total increase in clients: 11049
    The percentage of clients between the ages of 41 and 60: 46.74%

</div>

</div>

<div class="cell code" execution_count="325">

``` python
# Create a variable that stores the total number of clients between the ages of 21 and 30 before the marketing campaign
clients_before = df[(df['client_age'] >= '21-30') & (df['client_age'] < '31-40') & (df['month_name'] == 'Month 2')].client_id.nunique()

# Create a variable that stores the total number of clients between the ages of 21 and 30 after the marketing campaign
clients_after = df[(df['client_age'] >= '21-30') & (df['client_age'] < '31-40') & (df['month_name'] == 'Month 3')].client_id.nunique()

# Calculate the increase in clients between the ages of 21 and 30
clients_increase = clients_after - clients_before

# Calculate the total increase in clients after the marketing campaign
clients_total_increase = df[(df['month_name'] == 'Month 3')].client_id.nunique() - df[(df['month_name'] == 'Month 2')].client_id.nunique()

# Print the increase in clients between the ages of 21 and 30
print(f'The increase in clients between the ages of 21 and 30: {clients_increase}')

# Print the total increase in clients
print(f'The total increase in clients: {clients_total_increase}')

# Print the percentage of clients between the ages of 21 and 30
print(f'The percentage of clients between the ages of 21 and 30: {round(clients_increase / clients_total_increase * 100, 2)}%')
```

<div class="output stream stdout">

    The increase in clients between the ages of 21 and 30: 847
    The total increase in clients: 11049
    The percentage of clients between the ages of 21 and 30: 7.67%

</div>

</div>

<div class="cell code" execution_count="331">

``` python
# Calculate the average deposit amount by age and by deposit type
df.groupby(['client_age', 'deposit_type']).deposit_amount.mean().unstack()
```

<div class="output execute_result" execution_count="331">

    deposit_type  Actual Deposit  Scheduled Deposit
    client_age                                     
    0-20                     NaN                NaN
    21-30             250.046130         242.563669
    31-40             315.965341         310.204116
    41-50             365.061566         352.915622
    51-60             377.105370         368.278941
    61-70             400.540748         386.319253
    71-80             458.414268         450.887682
    81-90             466.379423         469.820519
    91-100            504.666478         474.071429

</div>

</div>

<div class="cell code" execution_count="346">

``` python
# Calculate the median deposit amount by age and by deposit type
df.groupby(['client_age', 'deposit_type']).deposit_amount.median().unstack()
```

<div class="output execute_result" execution_count="346">

    deposit_type  Actual Deposit  Scheduled Deposit
    client_age                                     
    0-20                     NaN                NaN
    21-30                  200.0              200.0
    31-40                  248.0              250.0
    41-50                  282.0              284.0
    51-60                  294.0              296.0
    61-70                  312.0              314.0
    71-80                  386.0              390.0
    81-90                  394.0              397.0
    91-100                 407.0              407.0

</div>

</div>

<div class="cell code" execution_count="332">

``` python
# Calculate the average deposit amount by age and by deposit cadence
df.groupby(['client_age', 'deposit_cadence']).deposit_amount.mean().unstack()
```

<div class="output execute_result" execution_count="332">

    deposit_cadence    Biweekly        Extra     Monthly
    client_age                                          
    0-20                    NaN          NaN         NaN
    21-30            163.460743   382.058385  338.690448
    31-40            213.145112   526.570995  420.034310
    41-50            241.946994   780.741702  471.509504
    51-60            248.293374   724.917044  480.037514
    61-70            248.961719   885.670087  461.223083
    71-80            246.312206   775.123328  471.051333
    81-90            303.862745   544.921188  476.126951
    91-100                  NaN  1912.882222  469.185129

</div>

</div>

<div class="cell code" execution_count="344">

``` python
# Plot the average deposit amount by age and by deposit cadence with error bars
df.groupby(['client_age', 'deposit_cadence']).deposit_amount.mean().unstack().plot(
    kind='bar',
    title='Average Deposit Amount by Age and Deposit Cadence',
    ylabel='Average Deposit Amount',
    xlabel='',
    yerr=df.groupby(['client_age', 'deposit_cadence']).deposit_amount.std().unstack(),
    )

plt.show()
```

<div class="output display_data">

![](65e196b7258f1e41cf00cdc850febc09be0a5b5f.png)

</div>

</div>

<div class="cell code" execution_count="345">

``` python
# Calculate the median deposit amount by age and by deposit cadence
df.groupby(['client_age', 'deposit_cadence']).deposit_amount.median().unstack()
```

<div class="output execute_result" execution_count="345">

    deposit_cadence  Biweekly  Extra  Monthly
    client_age                               
    0-20                  NaN    NaN      NaN
    21-30               141.0  191.0    288.0
    31-40               179.0  240.0    354.0
    41-50               202.0  260.0    400.0
    51-60               210.5  284.0    408.0
    61-70               210.0  286.0    388.0
    71-80               205.0  300.0    404.0
    81-90               255.0  335.0    400.0
    91-100                NaN  300.0    407.0

</div>

</div>
