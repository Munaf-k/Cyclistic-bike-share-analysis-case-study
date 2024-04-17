# **Case study: How does a bike-share navigate speedy success?**

![Picture1](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/c13f77ed-a398-4a21-9842-5f90559cfa72)


------------------------------------------------------------------------
# **Introduction**

Welcome to the Cyclistic bike-share analysis case study. Cyclistic is a
fictional bike-share program that features more than 5,800 bicycles and
600 docking stations. Cyclistic sets itself apart by also offering
reclining bikes, hand tricycles, and cargo bikes, making bike-share more
inclusive to people with disabilities and riders who can't use a
standard two-wheeled bike. The majority of riders opt for traditional
bikes; about 8% of riders use the assistive options. Cyclistic users are
more likely to ride for leisure, but about 30% use the bikes to commute
to work each day.

# **About the company**

In 2016, Cyclistic launched a successful bike-share offering. Since
then, the program has grown to a fleet of 5,824 bicycles that are
geotracked and locked into a network of 692 stations across Chicago. The
bikes can be unlocked from one station and returned to any other station
in the system anytime.

Until now, Cyclistic's marketing strategy relied on building general
awareness and appealing to broad consumer segments. One approach that
helped make these things possible was the flexibility of its pricing
plans: single-ride passes, full-day passes, and annual memberships.
Customers who purchase single-ride or full-day passes are referred to as
casual riders. Customers who purchase annual memberships are Cyclistic
members.

Cyclistic's finance analysts have concluded that annual members are much
more profitable than casual riders. Although the pricing flexibility
helps Cyclistic attract more customers, Moreno(The director of marketing
and manager) believes that maximizing the number of annual members will
be key to future growth. Rather than creating a marketing campaign that
targets all-new customers, Moreno believes there is a solid opportunity
to convert casual riders into members. She notes that casual riders are
already aware of the Cyclistic program and have chosen Cyclistic for
their mobility needs.

Moreno has set a clear goal: Design marketing strategies aimed at
converting casual riders into annual members. In order to do that,
however, the team needs to better understand how annual members and
casual riders differ, why casual riders would buy a membership, and how
digital media could affect their marketing tactics. Moreno and her team
are interested in analyzing the Cyclistic historical bike trip data to
identify trends.

# **The steps of the data analysis process:**

1. Ask

2. Prepare

3. Process

4. Analyze

5. Share

6. Act

# **Ask**

Three questions will guide the future marketing program:

1.  How do annual members and casual riders use Cyclistic bikes
    differently?
2.  Why would casual riders buy Cyclistic annual memberships?
3.  How can Cyclistic use digital media to influence casual riders to
    become members?

# **Prepare**

Using Cyclistic's historical trip data to analyze and identify trends.
[Download the previous 12 months of Cyclistic trip data
here](https://divvy-tripdata.s3.amazonaws.com/index.html). (Note: The
datasets have a different name because Cyclistic is a fictional company.
For the purposes of this case study, the datasets are appropriate and
will enable you to answer the business questions. The data has been made
available by Motivate International Inc. under this
[license](https://divvybikes.com/data-license-agreement).) This is
public data that you can use to explore how different customer types are
using Cyclistic bikes. But note that data-privacy issues prohibit you
from using riders' personally identifiable information. This means that
you won't be able to connect pass purchases to credit card numbers to
determine if casual riders live in the Cyclistic service area or if they
have purchased multiple single passes.

# **Process**

**Data Source**

For this analysis, I sourced the dataset from the Prepare Phase.

**Tools and Platform**

The analysis was conducted using Python programming language in a
Jupyter Notebook environment hosted on Google Colab. Google Colab offers
a cloud-based workspace, facilitating the writing and execution of
Python code interactively within a notebook format. This platform
eliminates the need for local software installations, streamlining the
data analysis and visualization process.

**Data Loading and Analysis**

The dataset was loaded into a Pandas DataFrame after importing the
requisite Python libraries. Pandas, a versatile data manipulation and
analysis library, was employed for reading diverse data formats, data
cleaning, preprocessing, and executing comprehensive data analysis. This
enabled the extraction of meaningful insights and facilitated the
visualization of various patterns and trends inherent in the dataset.

``` python
#importing the required libraries
import os
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```
``` python
# Mount Google Drive
from google.colab import drive
drive.mount('/content/drive')
```

For this analysis, I utilized the data spanning 12 months from the year
2023. The data was organized on a monthly basis, To evaluate the entire
year\'s trends, I merged all monthly datasets into a single file.

``` python
# path containing CSV files
csv_folder_path = '/content/drive/My Drive/Cyclistic bike-share analysis case study/'

# List all CSV files in the directory
csv_files = [file for file in os.listdir(csv_folder_path) if file.endswith('.csv')]

# Created an empty list to store DataFrames
dfs = []

# Loop through each CSV file and read it into a DataFrame
for file in csv_files:
    file_path = os.path.join(csv_folder_path, file)
    df = pd.read_csv(file_path)
    dfs.append(df)

# Concatenated all DataFrames in the list
merged_df = pd.concat(dfs, ignore_index=True)
```

``` python
# the merged DataFrame
merged_df.head()
```


# **Analyze**

Now, data is stored appropriately and has been prepared for analysis.

``` python
# no. of columns and rows in dataset
merged_df.shape
```

``` python
# Information about dataset
merged_df.info()
```

As we can see that \'**started_at**\' and \'**ended_at**\' columns are
currently stored as object data types. To facilitate accurate date and
time analysis, these columns should be converted to datetime data types.

``` python
# Convert 'started_at' and 'ended_at' to datetime data type
merged_df['started_at'] = pd.to_datetime(merged_df['started_at'])
merged_df['ended_at'] = pd.to_datetime(merged_df['ended_at'])
```

``` python
#after converting datatypes
merged_df.info()
```


To find out how long each ride lasted, I subtracted the
\'**started_at**\' time from the \'**ended_at**\' time. Then, I
converted the result into minutes and stored it in a new column called
\'**ride_length_minutes**\'.

``` python
# ride length in minutes
merged_df['ride_length_minutes'] = (merged_df['ended_at'] - merged_df['started_at']).dt.total_seconds() / 60
```
``` python
# Updated DataFrame with ride length in minutes
print(merged_df[['started_at', 'ended_at', 'ride_length_minutes']])
```


``` python
# Descriptive Statistics of dataset
merged_df.describe()
```

``` python
# Checking for Missing Values
merged_df.isnull().sum()
```

Here\'s the columns with missing values **start_station_name,
start_station_id, end_station_name, end_station_id, end_lat, end_lng.**
``` python
# Remove rows with missing values
merged_df.dropna(subset=['start_station_name', 'start_station_id', 'end_station_name', 'end_station_id', 'end_lat', 'end_lng'], inplace=True)
```
``` python
# after removing rows with missing values
merged_df.isnull().sum()
```


Calculating the **day of the week** for each bike ride using the
\'**started_at**\' timestamp. Then, Counting the number of rides for
each day of the week to understand the ride frequency.
``` python
# Calculating day of the week people ride bikes
merged_df['day_of_week'] = merged_df['started_at'].dt.day_name()

# Count the number of rides for each day of the week
ride_counts_by_day = merged_df['day_of_week'].value_counts()

print("Number of rides by day of the week:")
print(ride_counts_by_day)
```

``` python
# updated dataset
merged_df.head()
```

``` python
# updated dataset
merged_df.info()
```

Comparing the ride frequency between **annual members** and **casual
riders**. Then counting the number of rides for each member type to
analyze the difference in usage patterns.
``` python
# frequency of rides between annual members and casual riders
ride_frequency = merged_df.groupby('member_casual')['ride_id'].count()
print(ride_frequency)
```


# **Share**

Supporting visualizations and key findings.
``` python
# Calculating ride frequency by riders type
ride_frequency = merged_df['member_casual'].value_counts()

# Converting ride frequency to thousands
ride_frequency_thousands = ride_frequency / 1000

# Creating a bar plot with different colors for each riders type
plt.figure(figsize=(8, 6))
sns.barplot(x=ride_frequency_thousands.index, y=ride_frequency_thousands.values, hue=ride_frequency_thousands.index, palette={'member': 'cyan', 'casual': 'gray'}, dodge=False)
plt.title('Ride Frequency by Riders Type (in Thousands)')
plt.xlabel('Riders Type')
plt.ylabel('Number of Rides (in Thousands)')

# Format y-axis labels to represent values in thousands
plt.yticks(ticks=plt.yticks()[0], labels=[f'{int(y)}K' for y in plt.yticks()[0]])

# Adding data labels on top of bars
for i, value in enumerate(ride_frequency_thousands):
    plt.text(i, value + 0.1, f"{value:.1f}K", ha='center', va='bottom', fontsize=9)

plt.show()
```
![717cb799edb2d7091f9c366b2ca12ee31751ce37](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/4e5c7c15-bdbc-4d40-ab19-4a6a701b5aee)


``` python
# Calculating average ride duration by riders type
ride_duration = merged_df.groupby('member_casual')['ride_length_minutes'].mean()

# custom colors for 'member' and 'casual'
colors = {'member': 'cyan', 'casual': 'gray'}

# Creating a bar plot with custom colors
plt.figure(figsize=(8, 6))
sns.barplot(x=ride_duration.index, y=ride_duration.values, hue=ride_duration.index, palette=colors, dodge=False)
plt.title('Average Ride Duration by Riders Type')
plt.xlabel('Riders Type')
plt.ylabel('Average Ride Duration (minutes)')

# Adding data labels
for i, value in enumerate(ride_duration):
    plt.text(i, value + 0.1, f"{value:.1f}", ha='center', va='bottom', fontsize=9)

plt.show()
```
![b31e9a7889ff0db73a6b82ac04346a10a0e436da](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/c84539b4-957f-4a5a-87e3-9e8f12708a3e)

``` python
ride_duration = merged_df.groupby('member_casual')['ride_length_minutes'].mean()
print(ride_duration)
```


These Graphs gives us a clear understanding of the **usage patterns
between casual riders and annual members.**
``` python
# Defining the order of days of the week in ascending order
days_order = ['Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday', 'Sunday']

# Graph for rides by user type and day of week
plt.figure(figsize=(12, 8))
sns.countplot(data=merged_df, x='day_of_week', hue='member_casual', order=days_order)
plt.title('Rides by Riders Type and Day of Week')
plt.xlabel('Day of Week')
plt.ylabel('Number of Rides')
plt.legend(title='Riders Type')
plt.show()
```
![bef9f9b9838ae25fb3242b2314cbb912202683d1](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/372fa2bc-092e-4ce6-b8fe-818a773efedc)

Here we can see that Casual riders tend to use the bikes for fun on
weekends, while members use them mainly for daily work commutes.

Another new column named \'**hour_of_day**\' is created to store the
hour (in 24-hour format) extracted from the \'**started_at**\' timestamp
column using the dt.hour method.

These transformations enable more granular analysis by hour of the day
for the ride data.
``` python
merged_df['hour_of_day'] = merged_df['started_at'].dt.hour
```
``` python
# Ride Frequency by Hour of Day
plt.figure(figsize=(10, 6))
sns.countplot(data=merged_df, x='hour_of_day', hue='member_casual')
plt.title('Ride Frequency by Hour of Day')
plt.xlabel('Hour of Day')
plt.ylabel('Number of Rides')
plt.legend(title='Riders Type')
plt.show()
```
![811a7d830d6823e9a32860d129844cf19738202e](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/51996deb-35d0-441a-8abc-6c0e99b4f38e)


Both casual riders and members are using bikes more frequently between 4
pm and 7 pm.
``` python
# Creating Pie chart to visualize Ride frequency by riders type
plt.figure(figsize=(8, 6))
plt.pie(ride_frequency_thousands, labels=ride_frequency_thousands.index, autopct='%1.1f%%', startangle=140)
plt.title('Ride Frequency by riders Type (in Percentage)')
plt.show()
```
![4e6fb967118a5e562735d3bdda38e06c003ae8a5](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/5e9f709d-c9d3-4774-8c34-085baca1de1c)

``` python
# updated dataset
merged_df.info()
```

``` python
# Calculating the distance between two points using the Haversine formula
def haversine_distance(lat1, lon1, lat2, lon2):

    # Converting latitude and longitude from degrees to radians
    lat1, lon1, lat2, lon2 = map(np.radians, [lat1, lon1, lat2, lon2])

    # Haversine formula
    dlat = lat2 - lat1
    dlon = lon2 - lon1
    a = np.sin(dlat / 2) ** 2 + np.cos(lat1) * np.cos(lat2) * np.sin(dlon / 2) ** 2
    c = 2 * np.arctan2(np.sqrt(a), np.sqrt(1 - a))

    # Radius of the Earth in Km
    r = 6371.0

    # Calculating the distance
    distance = r * c
    return distance
```

``` python
# Calculating distance traveled in kilometers for all rides
merged_df['distance_km'] = haversine_distance(
    merged_df['start_lat'], merged_df['start_lng'],
    merged_df['end_lat'], merged_df['end_lng'])

# the new 'distance_km' column
print(merged_df[['start_lat', 'start_lng', 'end_lat', 'end_lng', 'distance_km']])
```

``` python
# Calculating average distance traveled by rider type
avg_distance_by_rider_type = merged_df.groupby('member_casual')['distance_km'].mean().reset_index()

# custom colors for 'member' and 'casual'
colors = {'member': 'cyan', 'casual': 'gray'}

# a bar plot with custom colors
plt.figure(figsize=(8, 6))
ax = sns.barplot(x='member_casual', y='distance_km', data=avg_distance_by_rider_type, hue='member_casual', palette=colors, dodge=False)

# Adding data labels
for index, value in enumerate(avg_distance_by_rider_type['distance_km']):
    ax.text(index, value, f'{value:.2f}', color='black', ha="center")

plt.title('Average Distance Traveled by Rider Type')
plt.xlabel('Rider Type')
plt.ylabel('Average Distance Traveled (km)')
plt.legend().remove()
plt.show()
```
![1e8b13862dd0dfaf2be315a0385647a32005f154](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/92029e90-9405-4eac-a9f4-dc8619c0b949)

``` python
# Calculating average distance traveled by bike types
avg_distance_by_bike_type = merged_df.groupby('rideable_type')['distance_km'].mean().reset_index()

# Creating a bar plot
plt.figure(figsize=(10, 6))
sns.barplot(x='rideable_type', y='distance_km', hue='rideable_type', data=avg_distance_by_bike_type,
            dodge=False)

# Adding data labels
for index, value in enumerate(avg_distance_by_bike_type['distance_km']):
    plt.text(index, value, f'{value:.2f}', color='black', ha="center")

plt.title('Average Distance Traveled by Bike Type')
plt.xlabel('Bike Type')
plt.ylabel('Average Distance Traveled (km)')
plt.show()
```
![8f5966c938827b961420703e796b90eeeda98d26](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/ef1e31a3-1ba1-43ac-aa2b-433bb29cde1b)

``` python
# the plotting style
sns.set(style="whitegrid")

# Graph for ride frequency by riders type and bike type
plt.figure(figsize=(12, 8))
sns.countplot(data=merged_df, x='member_casual', hue='rideable_type')
plt.title('Ride Frequency by User Type and Bike Type')
plt.xlabel('Riders Type')
plt.ylabel('Number of Rides')
plt.legend(title='Bike Type')
plt.show()
```
![9625a1ca77386cf0c03b283cbd54ee939c9d113e](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/118ec17b-308d-491e-9fd6-24bd9f342ac8)

``` python
# month from started_at column
merged_df['month'] = merged_df['started_at'].dt.month

# Group the data by month and user type, and counting the number of rides
rides_by_month_and_user = merged_df.groupby(['month', 'member_casual'])['ride_id'].count().reset_index()

# a line plot for rides across months for different user types
plt.figure(figsize=(12, 8))
sns.lineplot(data=rides_by_month_and_user, x='month', y='ride_id', hue='member_casual', marker='o')
plt.title('Rides Across Months by Riders Type')
plt.xlabel('Month')
plt.ylabel('Number of Rides')
plt.xticks(range(1, 13), ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'])
plt.legend(title='Riders Type')
plt.show()
```
![fcdd2e04261a6ee8e85750273ff5f5cf69fa8272](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/aeb44492-ea40-4bcb-8641-a3c3a0483ecf)


Both Casual and member riders uses bike more frequently in month like
May,Jun,July and August.
``` python
# ride frequency by bike type
ride_frequency_by_bike_type = merged_df['rideable_type'].value_counts()

# pie chart for bike type
plt.figure(figsize=(8, 6))
plt.pie(ride_frequency_by_bike_type, labels=ride_frequency_by_bike_type.index, autopct='%1.1f%%', startangle=140)
plt.title('Ride Frequency by Bike Type in %')
plt.show()
```
![2971e8086162596e4908247bde119130a7bce3b5](https://github.com/munaf101/Cyclistic-bike-share-analysis-case-study/assets/105988451/9c8718da-84d7-4ef2-8e1b-e3dc247dd998)

About 62% customers prefers Classic Bike.
``` python
# updated dataset after analysis
merged_df.info()
```


``` python
# Save the DF to a CSV
merged_df.to_csv('merged_data.csv', index=False)
```

``` python
# Save the dataset to a CSV file
file_path = '/content/drive/MyDrive/merged_df.csv'
merged_df.to_csv(file_path, index=False)
```

# **Act**

Recommendations based on analysis

1.  **Weekend Promotions**: Since casual riders tend to use bikes for
    fun on weekends, offer special weekend promotions or discounts to
    encourage them to become members. Highlight the benefits of
    membership, such as lower rates or priority access, especially
    during weekends.

2.  **Evening Offers**: As both casual riders and members use bikes more
    frequently between 4 pm and 7 pm, introduce incentives targeted at
    these peak usage times. This could include discounted rates or
    exclusive member-only perks for rides during these hours.

3.  **Seasonal Membership Offers**: Given that both casual and member
    riders use bikes more frequently in months like May, June, July, and
    August, create seasonal membership offers or packages. These could
    provide better value for money during peak riding seasons, making
    membership more appealing to casual riders.

4.  **Bike Preference Promotions**: Since about 62% of customers prefer
    classic bikes, promote the benefits of classic bikes to casual
    riders. Showcase their comfort, reliability, and ease of use, and
    offer exclusive deals or discounts on classic bike rentals for
    members to incentivize them to switch.

