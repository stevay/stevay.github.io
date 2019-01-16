---
layout: post
title: The Life of Popular Music
---

![image_1](/images/blog_post_2/image_1.jpg)

The purpose of music is multifaceted. The head-bobbing rhythms; the sing-along anthems ([this is my jam](https://youtu.be/HgzGwKwLmgM)); the movie soundtrack theme songs. It is no surprise how impactful music is to the world. Some might even say that there is no life without music - I am one of such folks.

Given how expansive music is, in areas such as genre and number of songs available, when it comes to distinguishing ‘good’ music from ‘great’ music, the Billboard Hot 100 charts are considered the music industry standard for defining the most popular songs for any given year. Billboard has been releasing charts since the 1950s, and I, being the curious Data Scientist with a strong passion in music, yearn to know what made songs ‘click’ for society over the last 60-plus years.

Using Billboard Hot 100 charts from 1950-2018 and Spotify’s API, I set out to discover what music trends and shifts exist for popular music over the past six decades.

## Approach  
For this initiative, I define ‘great’ or ‘popular’ music to be any song that was listed in the Billboard Hot 100 charts. 

In terms of data collection, I relied on **two** sources: 
 
1. For songs made between **1950 - 2015**, I leveraged [data](https://github.com/kevinschaich/billboard/tree/master/data/years) from GitHub user Kevin Schiach. The data contains interesting features such as song sentiment, Flesch-Kincaid metric (determines how complex and readable text or song lyrics are), number of difficult words, and more.  
2. Songs made **2016 and onwards** I first manually pulled artist and track names from Wikipedia with the Python library Pandas. Afterwards, I captured the other features found in Kevin Schiach’s data (e.g. Flesch-Kincaid) with the help of other Python libraries such as [vaderSentiment](https://github.com/cjhutto/vaderSentiment) and [textstat](https://pypi.org/project/textstat/). 

In addition, I utilized Spotify’s API, specifically an endpoint called [get audio features](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/), to capture other song features like loudness, speechiness (how many spoken words are present in a track), tempo, valence (how much musical positiveness is present in a track), etc. Combining Spotify audio features with my initial set, that brings the song features’ total count to 37.

You can find more information on all features via Kevin Schiach’s repository [here](https://github.com/kevinschaich/billboard) and Spotify for Developers page [here](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/). 

## Exploratory Data Analysis


## Project Benson - aka MTA Data Analysis

![alt text](/images/blog_post_1/image_8.png)
  
I am (*was?*) an East Coaster. I was born in New York and moved to New Jersey soon after. Eventually I went back to New York for my undergraduate degree and for work, aka ‘adulting.’ It wasn’t until recently did I decide to move to the west coast (**BEST COAST!**... am I doing it right?) 

So when my professors dropped the news earlier this week that my class’ first project would revolve around New York City MTA data, there was definitely a sense of nostalgia happening. The many fond memories I have of the public transportation system offered in the city that never sleeps, like the lack of air conditioning in practically every subway station; the rats that roamed and ruled the train tracks; the delays, oh the delays…

Although my recollection of the NYC subway stations isn’t captured in the freely available MTA subway data, what is captured played a huge role in helping me and my group complete our first project. 

Our task was to utilize MTA data, and other data sources of our choosing, in order to assist the client define which train stations to deploy street teams for both promoting an upcoming gala event and inviting participants who are passionate about increasing diversity in the technology industry. At a high level, my group came up with a general four-step approach for solving this problem:

![process flow](/images/blog_post_1/image_4.png)

1. **Pull**: Gather all the data sources that we believed were both relevant and beneficial for our research purposes. In this case, we knew we wanted MTA data to get a sense of what activity at each train station was like. Also, given the second requirement of wanting to find gala participants who would contribute to the client’s cause, we wanted to pull NYC income and household data offered by the U.S. Census Bureau. In order to tie MTA data with census data, zip code data was required - we turned to Google Maps Platform for that. 

2. **Clean**: Data is messy. There are many characteristics that can skew the insights gathered from exploratory data analysis, like: 
  - Duplicate data
  - Unstandardized data values
  - Incorrect / flawed data types  

   MTA data is no exception to the messy data syndrome - got to clean before accurate insights can be seen.  

3. **Analyze**: This is where the insights and recommendations stem from. For my group specifically, we knew we wanted to find train stations with the highest total number of entries over the last 52-weeks. We also wanted to consider city areas with large number of households that had incomes ranging between $100k to $200k. Our assumption was that such households would be more inclined to contribute to the annual gala. 
	
	![tools](/images/blog_post_1/image_5.png)  
   *Data sources and tools used for this project*  
   
	Most of our analysis was performed within Python using a software library called Pandas. Pandas is great for both data manipulation and analysis, with capabilities such as:
	- Creating DataFrames, which essentially is a table with both columns and rows - very similar to Excel
	- Ability to load data from flat files (e.g. CSV), Excel files, and databases
	- ‘Group By’ functionality for both aggregating and transforming data
	- Merging and joining data sets
	- Easy handling of missing or null data (represented as NaN in Pandas)

   An example Pandas use case is when my group wanted to merge MTA data with NYC census data. By default, the NYC census data came with zip codes, but MTA data did not. However, with the help of a technique known as geocoding, we were able to gather the zip codes of every train station. From there, we used Pandas to create a DataFrame that merged MTA data with NYC census data via zip codes. Voila!  
   
4. **Results**: Once we built all the different DataFrames and calculated all values we wanted, it was time for us to visually display our findings. The tools we used for this project were Python libraries Matplotlib and Seaborn. Matplotlib provides the basic tools required to develop plots such as bar graphs and scatter charts. Seaborn is a Python library based on Matplotlib that allows for more aesthetically pleasing statistical graphs. 

   You can see an example of a graph we built for our group project below:
     
   ![data visual](/images/blog_post_1/image_7.png)
   

5. **Future**: Given the timeframe of this project, there were additional ideas my group had in terms of other factors to consider or visuals to develop, but unfortunately we were not able to include them in our final presentation. All those ideas went into this bucket of our general approach. 

   For our project specifically, knowing what we know now after our initial analysis, future considerations include:
	- Looking into MTA turnstile exits activity
	- Analyzing longitudinal MTA data (e.g. Sept 2017 vs. Sept 2018 MTA activity)
	- Seeing if demographics correlates with MTA activity (e.g. gender)
	- Identifying tech-centric locations and their proximity to MTA train stations

## Final Thoughts  
When it comes exploratory data analysis, the sky is the limit in terms of scope. There is so much data one can analyze and get insights from. It is important to remember that when starting off, it is OK to start with a narrower scope and expanding it afterwards. Perfectionism - that feeling of making sure you capture every data source you believe is relevant for your analysis - is not your friend here. Baby steps!

If interested, here's the [link](https://github.com/RedGeryon/MTA-subway) to my group’s GitHub if you want to see what our final presentation looks like, as well as some of the code we developed to capture the results we ended up with. 

If you made it this far, thanks for reading! I’ll leave an inspirational quote below to close this blog out:

![alt text](https://media.giphy.com/media/QChZZqzSo9d2o/giphy.gif)

**STEVAY OUT!**