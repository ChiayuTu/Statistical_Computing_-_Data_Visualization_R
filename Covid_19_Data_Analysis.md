# STAT 361 Assignment Three: Covid 19 Data Analysis
##### Date       : 06/04/2021
##### Authoe     : Chiayu Tu
##### Instructor : Daniel Taylor Rodriguez

## Objective for this assignment
1. Upload csv files into R

2. Become proficient at manipulating, filtering, summarizing, cleaning, recoding, combining, etc. data in R using dplyr, tidyr, and purr

3. Use exploratory data analysis tools, such as summary statistics tables and figures to determine if a hypothesis has any merit

4. Create informative summary tables and figures

5. Use tools to extract information from character strings

## Background and Data for this assignment

Although by this point some of you may be avoiding the information overload surrounding Covid-19, understanding what has happened up to now by direct exploration of the latest data directly from the source, and extracting one’s own conclusions can be empowering.

At this point of the term you have an R toolbox broad enough to tackle the massive amount of data found in Johns Hopkins COVID-19 repository (click here). These data has been updated daily since the COVID-19 pandemic started. I also included links to other supplementary data sets to potentially explore the effectiveness of measures taken throughout the pandemic, including-mask use and vaccination. The datasets included are:

1. covid.ts.cases: daily time series for the confirmed number of COVID-19 cases at the county level for the US (file description here).

2. covid.ts.deaths: daily time series for the confirmed number of COVID-19 deaths at the county level for the US (file description here).

3. covid.usa.daily: COVID-19 USA daily state reports with the number of confirmed cases between April 14th and May 7th (file description here).

4. Vaccination data: state level COVID-19 daily vaccination numbers time series data from the Johns Hopkins University repository (file description here, )

5. State policy data: data files (one file by state) about dates and description of policies going into/out of effect. To load data for a particular state go to this link, find the name of the state file you want to work with. For example if you want to load data from California, use
```
policytrackerCA <- read_csv("https://raw.githubusercontent.com/govex/COVID-19/govex_data/data_tables/policy_data/table_data/Current/California_policy.csv")
```
To change the state you just need to replace the name of the state in the file name (e.g., California_policy.csv by Oregon_policy.csv) at the end of the link above for the one you want to work with.

Here is the data:
```
#confirmed COVID-19 time series cases US (trimmed to include from 01/01/2021 to 05/23/2022)
covid.usa.ts.confirmed <- read_csv('https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_confirmed_US.csv') %>%  select(UID:Combined_Key,`1/1/21`:`5/23/22`)

#Confirmed COVID-19 time series deaths US (trimmed to include from 01/01/2021 to 05/23/2022) these data include the population by county
covid.usa.ts.deaths <- read_csv('https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_time_series/time_series_covid19_deaths_US.csv') %>%
  select(UID:Population,`1/1/21`:`5/23/22`)

#Daily data summary by state for 05-23-20222
covid.usa.daily <- read_csv("https://raw.githubusercontent.com/CSSEGISandData/COVID-19/master/csse_covid_19_data/csse_covid_19_daily_reports_us/05-23-2022.csv") 

#US vaccinated people data
vacc.people <- read_csv("https://raw.githubusercontent.com/govex/COVID-19/master/data_tables/vaccine_data/us_data/time_series/people_vaccinated_us_timeline.csv")


#uncomment to load policy tracker data for a state
#data on policy adoption by state (there is one file by state).  If loading data in CA, use:
#policytrackerCA <- read_csv("https://raw.githubusercontent.com/govex/COVID-19/govex_data/data_tables/policy_data/table_data/Current/California_policy.csv")
#If loading data in OR, use:
#policytrackerOR <- read_csv("https://raw.githubusercontent.com/govex/COVID-19/govex_data/data_tables/policy_data/table_data/Current/Oregon_policy.csv")
```
