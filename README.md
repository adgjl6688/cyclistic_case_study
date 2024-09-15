# Google Data Analytics Capstone: Cyclistic Case Study

Course: [Google Data Analytics Capstone: Complete a Case Study](https://www.coursera.org/learn/google-data-analytics-capstone)

## Background context
You are a junior data analyst on the marketing analyst team at Cyclistic, a bike-share company in Chicago. The marketing director believes the company’s future success depends on maximizing the number of annual memberships. Therefore, your team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, your team will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve your recommendations, so they must be backed up with compelling data insights and professional data visualizations.

## Ask
### Business Task

Devise marketing strategies to convert casual riders to members.

### Analysis Questions

Three questions will guide the future marketing program:

1. How do annual members and casual riders use Cyclistic bikes differently?  
2. Why would casual riders buy Cyclistic annual memberships?  
3. How can Cyclistic use digital media to influence casual riders to become members?

Moreno has assigned me the first question to answer: How do annual members and casual riders use Cyclistic bikes differently?

## Prepare

Cyclistic’s historical trip data trends from Jan 2023 to Dec 2023 from [divvy_tripdata](https://divvy-tripdata.s3.amazonaws.com/index.html). **Motivate International Inc.** has made the data available under this [license](https://www.divvybikes.com/data-license-agreement).

This is the public data that can be used to explore how different customer types are using Cyclistic bikes. However, note that data privacy issues prohibit riders from using personally identifiable information. This means we won’t be able to connect pass purchases to credit card numbers to determine if casual riders live in the Cyclistic service area or if they have purchased multiple single passes.

## Process

I added two columns "ride_length" and "day_of_week" to each .csv file using the Excel spreadsheet. 

Combine 12 months of data in RStudio. Clean the data.

### Codes

```{r}
# Load required libraries
library(tidyverse)
library(lubridate)
library(skimr)
library(janitor)

# Load and combine data
setwd("/Users/zhuoli/Downloads/2023_divvy_tripdata/2023_divvy_tripdata_csv")
file_list <- list.files(pattern = "*.csv")
combined_data <- file_list %>% map_dfr(read_csv)
combined_data <- clean_names(combined_data)

# Cleaning and mutating the data
combined_data <- clean_names(combined_data)
combined_data <- combined_data %>%
  mutate(started_at = mdy_hms(started_at),
         ended_at = mdy_hms(ended_at))
combined_data <- combined_data %>%
  filter(ride_length > 0)
```

## Analyze and Share
Analyze Differences Between Annual Members and Casual Riders: Explore patterns, trends, and differences between annual members and casual riders.

### Codes

```{r}
# Trip Duration Comparison
combined_data %>%
  group_by(member_casual) %>%
  summarise(avg_ride_length = mean(ride_length),
            total_rides = n()) %>%
  ggplot(aes(x = member_casual, y = avg_ride_length, fill = member_casual)) +
  geom_col() +
  labs(title = "Average Ride Duration by User Type",
       x = "User Type", y = "Average Ride Duration (minutes)")
```
![image](https://github.com/user-attachments/assets/29009bb7-ce71-4de0-9673-b87077c462c2)


```{r}
# Usage by Day of Week
combined_data %>%
  mutate(day_of_week = factor(day_of_week, levels = c(2, 3, 4, 5, 6, 7, 1),  # Reorder levels: Monday=2, ..., Sunday=1
                              labels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"))) %>%
  group_by(member_casual, day_of_week) %>%
  summarise(number_of_rides = n()) %>%
  ggplot(aes(x = day_of_week, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title = "Number of Rides by Day of the Week", x = "Day of the Week", y = "Number of Rides")
```
![image](https://github.com/user-attachments/assets/0fc97362-7375-404b-8e5f-df0c1a0c5b5a)


### Summary
1. Casual riders use Cyclistic bikes longer than annual members on average.
2. Annual members tend to have more rides during weekdays.


## Act
After identifying the differences between casual and member riders, marketing strategies to target casual riders can be developed to persuade them to become members.

### Recommendations
1. The marketing campaign can focus on promoting a lower rate of cost of longer rides to casual riders.
2. To maintain current annual members, we can have some discounts when using the bikes on weekends.
3. We can offer the cashback/gift program to loyal members. The more they use Cyclistic bikes, the less they pay, or they can get some cashbacks/small gifts, such as an encouraging medal to people who accomplish a 100km ride.
