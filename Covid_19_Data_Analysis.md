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
## Part One 
##### 1. Identify and list the primary and foreign keys for each data frame.

Table: covid.usa.ts.confirmed & covid.usa.ts.deaths

UID and Combined_Key, one of this two can be primary key.

Foreign Key can be FIPS, Admin2, Province_State, Lat, Long_

Table: covid.usa.daily

Primary Key is Province_State

Foregin Key can be Province_State, Lat, Long_, FIPS

Table: vacc.people

Primary Key can be Combined_Key

Foreign Key can be FIPS, Province_State, Date, Lat, Long_

##### 2. Using the time series data sets covid.usa.ts.confirmed and covid.use.ts.deaths, which are both at the county level and are in wide format, reshape them into long format (using the function pivot_longer) to generate a single new data frame with the daily time series BY STATE including both number of confirmed cases and deaths, so that you have one row for each combination of state and date. Call this new data frame covid.usa.states.ts.

#####Note: after reshaping your file into long format, your new date column (or however you decide to call it) needs to be converted into a Date-Time type variable. For example, if your variable is called my.date.variable, you can make this conversion using lubridate::mdy(my.date.variable).

```
#reshape covid.us.ts.confirmed to long format
covid.usa.ts.confirmed.long <- covid.usa.ts.confirmed %>%
  pivot_longer(cols = "1/1/21":"5/23/22",
               names_to = "Dates",
               values_to = "Confirmed") %>% 
  select(Province_State, Dates, Confirmed) %>% 
  mutate(Dates = mdy(Dates)) %>% 
  group_by(Province_State, Dates) %>% 
  summarise(Confirmed = sum(Confirmed))
#head(covid.usa.ts.confirmed.long)

#reshape covid.us.ts.deaths to long format
covid.usa.ts.deaths.long <- covid.usa.ts.deaths %>% 
  pivot_longer(cols = "1/1/21":"5/23/22",
               names_to = "Dates",
               values_to = "Deaths") %>% 
  select(Province_State, Dates, Deaths) %>% 
  mutate(Dates = mdy(Dates)) %>% 
  group_by(Province_State, Dates) %>% 
  summarise(Deaths = sum(Deaths))
#head(covid.usa.ts.deaths.long)

#merge two table to a new data frame which call "covid.usa.states.ts"
covid.usa.states.ts <- covid.usa.ts.confirmed.long %>% 
  full_join(covid.usa.ts.deaths.long,
            by = c("Province_State","Dates")) %>% 
  select(Dates, Confirmed, Deaths, Province_State)
# %>% 
#   group_by(Province_State) %>% 
#   summarise(
#     Confirmed.Cases = sum(Confirmed),
#     Deaths.Cases = sum(Deaths)
#   )
head(covid.usa.states.ts)
```

##### 3. Append to covid.usa.states.ts (created in previous problem) all of the information from matching rows in vacc.people (without repeating columns with the same info in the two data sets).

```
#Select variables from "vacc.people"
vacc.people.select <- vacc.people %>% 
  select(Date, Province_State, People_Fully_Vaccinated, People_Partially_Vaccinated)
#head(vacc.people.select)
#full join "covid.usa.states.ts" and "vacc.people"
covid.usa.states.ts <- covid.usa.states.ts %>% 
  select(Dates, Province_State, Deaths, Confirmed) %>% 
  full_join(vacc.people.select,
            by = c("Dates"="Date","Province_State"))
head(covid.usa.states.ts)
```

## Part Two
##### 1. Using covid.usa.daily select 3 highly impacted states, 3 mildly impacted states by COVID-19, where by ‘highly impacted’ I mean the states with high numbers of confirmed cases.

Observation:
Three highly impacted states
-California(confirmed:9496470)
-Texas(confirmed:6905608)
-Florida(confirmed:6101783)

Three mildly impacted states
-Diamond Princess(confirmed:49)
-Grand Princess(confirmed:103)
-American Samoa(confirmed:6087)

```
#Find 3 highly impacted states
covid.usa.daily %>% 
  arrange(desc(Confirmed))

most_death_three <- covid.usa.daily$Confirmed[order(covid.usa.daily$Confirmed, decreasing = T)[1:3]]
most_death_three

covid.usa.daily.h1 <- covid.usa.daily %>% 
  filter(Confirmed == most_death_three[1])
covid.usa.daily.h2 <- covid.usa.daily %>% 
  filter(Confirmed == most_death_three[2])
covid.usa.daily.h3 <- covid.usa.daily %>% 
  filter(Confirmed == most_death_three[3])
covid.usa.daily.h1
covid.usa.daily.h2
covid.usa.daily.h3
#3 highly impacted states
#California     confirmed:9496470 
#Texas          confirmed:6905608 
#Florida        confirmed:6101783

#Find 3 mildly impacted states
covid.usa.daily %>% 
  arrange(desc(-Confirmed))

most_mildly_three <- covid.usa.daily$Confirmed[order(covid.usa.daily$Confirmed, decreasing = F)[1:3]]
most_mildly_three

covid.usa.daily.m1 <- covid.usa.daily %>% 
  filter(Confirmed == most_mildly_three[1])
covid.usa.daily.m2 <- covid.usa.daily %>% 
  filter(Confirmed == most_mildly_three[2])
covid.usa.daily.m3 <- covid.usa.daily %>% 
  filter(Confirmed == most_mildly_three[3])
covid.usa.daily.m1
covid.usa.daily.m2
covid.usa.daily.m3
#3 mildly impacted states
#Diamond Princess     confirmed:49   
#Grand Princess       confirmed:103  
#American Samoa       confirmed:6087
```

![highly impacted states](C:\Users\tulou\Pictures\ScreenShot\Screenshot 2022-06-04 193924.png)

##### 2. Create a visualization of the evolution of confirmed cases, deaths, and people vaccinated for each of the 6 states identified as highly and mildly impacted (use the covid.usa.states.ts data.frame to create the figure).

```
highly.impact <- c("California", "Texas", "Florida")
mildly.impact <- c("Diamond Princess", "Grand Princess", "American Samoa")

vis_data <- covid.usa.states.ts %>%
  filter(Province_State == "California" | Province_State == "Texas" | 
           Province_State == "Florida" | Province_State == "Diamond Princess" | 
           Province_State == "Grand Princess" | Province_State == "American Samoa") 
#head(vis_data, n = 100)
#view(vis_data)
vis_data$Province_State <- factor(vis_data$Province_State, 
                                  levels = c("California", "Texas", "Florida", # re-level
                                             "Diamond Princess", "Grand Princess", "American Samoa"))
#head(vis_data)

vis_data <- vis_data[!is.na(vis_data$Confirmed) & 
                       !is.na(vis_data$Deaths) & 
                       !is.na(vis_data$People_Fully_Vaccinated),]
#head(vis_data)

ggplot(data = vis_data) + 
  geom_line(aes(x = Dates, y = Confirmed, color = "Confirmed")) + 
  geom_line(aes(x = Dates, y = People_Fully_Vaccinated, color = "Fully Vaccinated")) + 
  geom_line(aes(x = Dates, y = Deaths, color = "Death")) + 
  xlab("Date") + ylab("Confirmed Cases") +
  theme_bw() +
  facet_wrap(Province_State~., scales = "free") +
  theme(legend.position = "right",
        plot.title = element_text(size = 15), #adjust the title size
        plot.subtitle=element_text(size = 13, 
                                   face = "italic", 
                                   color = "black")) + # adjust the subtitle size
  labs(subtitle = "(compares the number of confirmed cases, death cases, and the number of people get fully vaccinated)") + # added subtitle
  scale_color_manual(name = "Cases", 
                     values = c("Confirmed" = "darkgreen", 
                                "Fully Vaccinated" = "blue", 
                                "Death" = "red"))

ggplot(data = vis_data) +
  geom_line(aes(x = Dates, y = Deaths, color = "red")) +
  xlab("Date") + ylab("Confirmed Cases") +
  theme_bw() +
  facet_wrap(Province_State~., scales = "free") +
  theme(legend.position = "right",
        plot.title = element_text(size = 15)) + 
  scale_color_discrete(name = "Cases", labels = "Death") 
```
