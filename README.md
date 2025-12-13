## Power Outages and Hurricanes - A Data-Driven Study
# Introduction
---
**Project for DSC80 at UCSD** <br>

In this project, I analyzed a dataset documenting major power outages in the United States between January 2000 and July 2016. The Department of Energy classifies these events as outages that affected at least 50,000 customers or resulted in an unexpected loss of 300 megawatts or more. The data was obtained from Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure.

[Dataset Source](https://engineering.purdue.edu/LASCI/research-data/outages)


The dataset provides details about each outage along with geographic, climatic, and land-use information, as well as state-level electricity usage and economic indicators.

I wanted to know - **what is the relation between hurricanes and power outages?** Although I explored many features in my DataFrame, hurricanes has been a coherent theme in my project. 
Since Hurricanes is the theme, everytime I analyze something related to "<span style="background-color:#f8d7da;">Hurricane</span>", I shall highlight it in red.  

First, I will clean my Dataframe and visualize few columns to get a feel of the data. I will check whether _____(fill missingness) and also will perform a permutation test to check if the Outages caused by Hurricanes and Outages by other events come from the same distribution. Then finally, I will predict whether a Power Outage is caused by a hurricane or not. 


The original raw DataFrame contains 1534 rows, corresponding to 1534 outages, and 57 columns. But I shall keep columns relavant to our analysis:

|Column                |Description|
|---                |---        |
|`'YEAR'`                |Year an outage occurred|
|`'MONTH'`                |Month an outage occurred|
|`'U.S._STATE'`                |State the outage occurred in|
|`'CLIMATE.REGION'`                |U.S. Climate regions as specified by National Centers for Environmental Information (9 Regions)|
|`'ANOMALY.LEVEL'`                |Oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season|
|`'CLIMATE.CATEGORY'`	|This represents the climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI)|
|`'OUTAGE.START.DATE'`                |Day of the year when the outage event started|
|`'OUTAGE.START.TIME'`                |Time of the day when the outage event started|
|`'OUTAGE.RESTORATION.DATE'`                |Day of the year when power was restored to all the customers|
|`'OUTAGE.RESTORATION.TIME'`                |Time of the day when power was restored to all the customers|
|`'CAUSE.CATEGORY'`                |Categories of all the events causing the major power outages|
|`'CAUSE.CATEGORY.DETAIL'`	|Detailed description of the event categories causing the major power outages|
|`'HURRICANE.NAMES'`	|If the outage is due to a hurricane, then the hurricane name is given by this variable|
|`'OUTAGE.DURATION'`                |Duration of outage events (in minutes)|
|`'CUSTOMERS.AFFECTED'`                |Number of customers affected by the power outage event|
|`'POPULATION'`	|Population in the U.S. state in a year|

# Data Cleaning and Exploratory Data Analysis
---
**Data Cleaning** <br>

1. I first kept columns necessary for our analysis. the column names we kept are listed in the table above. 

2. I merged the `OUTAGE.START.DATE` and `OUTAGE.START.TIME` columns to create a single timestamp column called `OUTAGE.START`. I repeated this process for the restoration fields by combining `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` into `OUTAGE.RESTORATION`. Since the new timestamp columns contain all the necessary information, I removed the original date and time columns afterward.

3. I then inspected my key outcome variables, `OUTAGE.DURATION` and `CUSTOMERS.AFFECTED`, to identify entries with a value of 0. Because a major outage cannot realistically have a duration of zero minutes or affect zero customers, these values are likely placeholders for missing data. I replaced all zeros in these columns with np.nan.

After cleaning, here's the head of my power dataframe, with few columns selected.

|   YEAR |   MONTH | U.S._STATE   | OUTAGE.START        | OUTAGE.RESTORATION   |   OUTAGE.DURATION |
|-------:|--------:|:-------------|:--------------------|:---------------------|------------------:|
|   2014 |       5 | Minnesota    | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |                 1 |
|   2010 |      10 | Minnesota    | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |              3000 |
|   2012 |       6 | Minnesota    | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |              2550 |
|   2015 |       7 | Minnesota    | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |              1740 |
|   2010 |      11 | Minnesota    | 2010-11-13 15:00:00 | 2010-11-14 22:00:00  |              1860 |


**Univariate Analysis** <br>

I analysed the Number of total Power Outages per State (From 2000 to 2016)

<iframe
  src="assets/outages-per-state.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

And then I analysed the Number of total Power Outages per Cause (From 2000 to 2016)

<iframe
  src="assets/frequency-outages.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Bivariate Analysis** <br>

I made an <mark>Interactive</mark> plot showing Power Outages per Year. The Slider can move from 2000 to 2016

<iframe
  src="assets/power-outages-2000.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

And Finally, I checked the Number of Total <span style="background-color:#f8d7da;">Hurricane</span> related Power Outages per State (From 2000 to 2016)  

<iframe
  src="assets/Hurricane-related.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

I see that there are only certain US states whose power is affected by <span style="background-color:#f8d7da;">hurricanes</span>. This resuly is useful for the Hurricane-outage prediction Model I shall build eventually. 

**Grouping and Aggregates** <br>

I first grouped <span style="background-color:#f8d7da;">hurricanes</span> based on the max, mean and total outage <u>minutes</u> they caused. 

| HURRICANE.NAMES   |   max_outage |   mean_outage |   total_outage |
|:------------------|-------------:|--------------:|---------------:|
| Sandy             |        20280 |       8668.94 |         147372 |
| Ike               |        27698 |      10565.8  |          84526 |
| Rita              |        18804 |       9711    |          38844 |
| Gustav            |        17812 |      15513.5  |          31027 |
| Frances           |        12060 |       6012    |          30060 |

And then I grouped <span style="background-color:#f8d7da;">hurricanes</span> based on their frequency over months of the year (From 2000 to 2016)

| MONTH     |   HURRICANE.NAMES |
|:----------|------------------:|
| January   |                 0 |
| February  |                 0 |
| March     |                 0 |
| April     |                 0 |
| May       |                 0 |
| June      |                 0 |
| July      |                 3 |
| August    |                12 |
| September |                36 |
| October   |                21 |
| November  |                 0 |
| December  |                 0 |

July, August, September, October happens to be "<span style="background-color:#f8d7da;">hurricane</span>" season. 

# Assessment of Missingness
---

# Hypothesis Testing
---
I wanted to check if the Outages caused by <span style="background-color:#f8d7da;">hurricanes</span> and Outages caused by other reasons come from the same population.

But the hurricane sample is very small (n≈70) compared to other casuality's data (n≈1500), therefore this large imbalance affects the ability of statistical tests to detect differences. 

Even though the hurricane sample is small, I still want to get a feel of how different the distributions are, compared to other causalities. 

- *Null Hypothesis* : distributions of outage durations are the same for hurricane and non-hurricane Outages
- *Alternate hypothesis* : distribution of outages are different for hurricane and non-hurricane Outages.

Test Statistic is the Difference in means. I performed over 30,000 simulations to generate an empirical distribution of the test statisic under the null hypothesis.

The p-value I got was <mark>3.33×10⁻⁵</mark>, so with a standard significance level of 0.05, we reject the null hypothesis. But still we can not conclude anything as the sample size of hurricanes is very minute compared to the total dataframe size. Although the hurricane sample is small, the test still provides a useful way to evaluate how hurricane outages compare to other outage causes.

<iframe
  src="assets/permtest-related.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Framing a Prediction Model
---

Now I shall predict if an Outage was caused by an <span style="background-color:#f8d7da;">hurricane</span> or not. I shall assign the event caused by a hurricane as (1) and other case as (0).

This is a Binary Classification problem. I restricted features to information which is salient to my prediction and is also available before the event: `'MONTH'`, `'U.S._STATE'`, `'CLIMATE.CATEGORY'`, `'ANOMALY.LEVEL'`, and `'CLIMATE.REGION'`. I used a logistic regression classifier with appropriate preprocessing and class balancing. Because the positive class is very rare, I evaluated performance using the F1-score, which better reflects model quality in imbalanced settings compared to accuracy.

# Baseline Model
---

For the baseline model, I am only using two features:

- `'MONTH'` – ordinal categorical (1–12)
- `'CLIMATE.REGION'` – nominal categorical

Because both features are categorical, I used a OneHotEncoder inside a ColumnTransformer to convert each categorical value into binary indicator columns. I then trained a logistic regression classifier with class_weight='balanced' to address the severe class imbalance (hurricane outages are very rare).

|          |precision    |recall    |f1-score   |support  |
|:---------|------------:|---------:|----------:|--------:|
|0         |0.99         |0.91      |0.95       |293      |
|1         |0.32         |0.86      |0.46       |14       |

And overall:
- Accuracy: 0.91
- Macro F1: 0.71
- Weighted F1: 0.93

I consider the baseline model not very good, despite the high accuracy. 

- The model has extremely poor precision for hurricanes (0.32). This means that when the model predicts “hurricane,” it is wrong 68% of the time. This makes it unreliable for rare-event prediction.

- The high recall for hurricanes (0.86) comes from overpredicting them.The model predicts “hurricane” too often, inflating recall but damaging precision.

- Accuracy is misleading because ~95% of events are non-hurricanes. A model could predict “not hurricane” for everything and still be highly accurate.






