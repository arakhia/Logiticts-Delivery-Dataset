# Logiticts-Delivery-dataset

## Introduction

This is Data Analysis project merely based on SQL queries and Tableau Visualization. The project explores Logistics Food Delivery data, the aim is to derive business decisions from the dataset, we could answer the following questions:
* Which type of food are purchased the most, and which time of the day?
* How the customers are ordering (apps, calls ... etc)?
* Which stores have higher sales?
* What are the peak times during the day? Is there a relation bweteen delivery time and peak hours?

## Data Source

[Kaggle - Porter Delivery Time Estimation](https://www.kaggle.com/datasets/ranitsarkar01/porter-delivery-time-estimation)


## Exploratory Data Analysis (EDA) - SQL Queries

```
-- Calculate orders per category
Select store_primary_category, count(*) as TotalOrders
From dataset
Group by store_primary_category
Order by TotalOrders desc
```

```
-- Show food categories by sales 
Select outter_logistics.store_primary_category, outter_logistics.MaxSubTotal, outter_logistics.SumSubTotal
	From (
	Select logistics.*,
		Max(Cast(subtotal as float)) over(partition by store_primary_category) as MaxSubTotal,
		SUM(Cast(subtotal as float)) over(partition by store_primary_category) as SumSubTotal
	From dataset logistics 
	Where store_primary_category<>''
	) outter_logistics
Group by outter_logistics.store_primary_category, outter_logistics.MaxSubTotal, outter_logistics.SumSubTotal
Order by outter_logistics.MaxSubTotal desc
```

```
-- Calculate delivery_time_minutes, ordering shift and delivery shift
Select *, DATEDIFF(MINUTE, created_at, actual_delivery_time) as delivery_time_minutes,
	(
	Case
		When CONVERT(Time, created_at) between '00:00:00' and '05:59:59' Then 'early_morning'
		When CONVERT(Time, created_at) between '06:00:00' and '11:59:59' Then 'morning'
		When CONVERT(Time, created_at) between '12:00:00' and '17:59:59' Then 'afternoon'
		When CONVERT(Time, created_at) between '18:00:00' and '23:59:59' Then 'night'
		Else 'undefined'
	End
	) as order_shift,
	(
	Case
		When CONVERT(Time, actual_delivery_time) between '00:00:00' and '05:59:59' Then 'early_morning'
		When CONVERT(Time, actual_delivery_time) between '06:00:00' and '11:59:59' Then 'morning'
		When CONVERT(Time, actual_delivery_time) between '12:00:00' and '17:59:59' Then 'afternoon'
		When CONVERT(Time, actual_delivery_time) between '18:00:00' and '23:59:59' Then 'night'
		Else 'undefined'
	End
	) as delivery_shift
From dataset
```

## Dashboard
[Dashboard Tableau](https://public.tableau.com/views/Logistics_DashBoard/Dashboard2?:language=en-US&:display_count=n&:origin=viz_share_link)

> **Disclaimer**: The dashboard is mainly based on the data and its quality. Also, I made few assumptions when required.
