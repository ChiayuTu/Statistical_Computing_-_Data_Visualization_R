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
