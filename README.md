# Introduction
---
**Project for DSC80 at UCSD** <br>

In this project, I analyzed a dataset documenting major power outages in the United States between January 2000 and July 2016. The Department of Energy classifies these events as outages that affected at least 50,000 customers or resulted in an unexpected loss of 300 megawatts or more. The data was obtained from Purdue University’s Laboratory for Advancing Sustainable Critical Infrastructure.

[Dataset Source](https://engineering.purdue.edu/LASCI/research-data/outages)


The dataset provides details about each outage along with geographic, climatic, and land-use information, as well as state-level electricity usage and economic indicators.

I wanted to know - **what is the relation between hurricanes and power outages?** Although I explored many features in my DataFrame, hurricanes has been a coherent theme in my project. 

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



