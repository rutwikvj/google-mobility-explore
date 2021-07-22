```sql
SELECT * FROM `bigquery-public-data.covid19_google_mobility.mobility_report` LIMIT 1000
```

# To check if the date is time series
```sql
SELECT * FROM `bigquery-public-data.covid19_google_mobility.mobility_report` 
where country_region_code="IN"
and sub_region_2 = 'Bangalore Urban'
order by date desc
```

# Business Question
* Average change in Baseline Banglore Urban for all indicators
* Comparing two locations (US and India)
* Peaks and Dips of India (change from baseline)

# Average change in Baseline Banglore Urban for all indicators
```sql
SELECT 
avg(retail_and_recreation_percent_change_from_baseline) as ret_avg_chg
FROM `bigquery-public-data.covid19_google_mobility.mobility_report` 
where country_region_code="IN"
and sub_region_2 = 'Bangalore Urban'
```

# Average change in Baseline Banglore Urban for indicators over 2020 and 2021
```sql
SELECT substr(string(date),1,4) as year,
avg(retail_and_recreation_percent_change_from_baseline) as ret_avg_chg,
avg(grocery_and_pharmacy_percent_change_from_baseline),
avg(parks_percent_change_from_baseline),
avg(transit_stations_percent_change_from_baseline)
FROM `bigquery-public-data.covid19_google_mobility.mobility_report` 
where country_region_code="IN"
and sub_region_2 = 'Bangalore Urban'
group by year
```

# Comparing two locations (US and India) for indicators over 2020 and 2021
```sql
SELECT country_region_code, substr(string(date),1,4) as year,
avg(retail_and_recreation_percent_change_from_baseline) as ret_avg_chg,
avg(grocery_and_pharmacy_percent_change_from_baseline) as gro_avg_hg,
avg(parks_percent_change_from_baseline) as par_avg_chg,
avg(residential_percent_change_from_baseline) as res_avg_chg,
avg(workplaces_percent_change_from_baseline) as wor_Avg_chg,
avg(transit_stations_percent_change_from_baseline) as tra_Avg_chg
FROM `bigquery-public-data.covid19_google_mobility.mobility_report` 
where country_region_code in ("IN","US")
and sub_region_2 is NULL
and sub_region_1 is NULL
group by year,country_region_code
order by year
```
# Peaks and Dips of India and US(change from baseline)
```sql
SELECT country_region_code, date,
retail_and_recreation_percent_change_from_baseline as ret_avg_chg,
grocery_and_pharmacy_percent_change_from_baseline as gro_avg_hg,
parks_percent_change_from_baseline as par_avg_chg,
residential_percent_change_from_baseline as res_avg_chg,
workplaces_percent_change_from_baseline as wor_Avg_chg,
transit_stations_percent_change_from_baseline as tra_Avg_chg
FROM `bigquery-public-data.covid19_google_mobility.mobility_report` 
where country_region_code in ("IN","US")
and sub_region_2 is NULL
and sub_region_1 is NULL
```


# Peaks and Dips of India and US(change from baseline) with dates and values
```sql
with country_retail_min_max as 
(SELECT
country_region_code
 ,max(retail_and_recreation_percent_change_from_baseline) as max_retail
 ,min(retail_and_recreation_percent_change_from_baseline) as min_retail
FROM
 `bigquery-public-data.covid19_google_mobility.mobility_report`
where 1=1
    and country_region_code in ('IN', 'US')
    and sub_region_1 is NULL 
    and date > date('2020-04-01')
group by 1
)

select *  
FROM country_retail_min_max retail 
 join `bigquery-public-data.covid19_google_mobility.mobility_report` mob
on mob.country_region_code = retail.country_region_code
    and ( mob.retail_and_recreation_percent_change_from_baseline = retail.max_retail
    or mob.retail_and_recreation_percent_change_from_baseline = retail.min_retail)
where 1=1
    and mob.country_region_code in ('IN', 'US')
    and sub_region_1 is NULL 
    and date > date('2020-04-01')
order by date 
```
