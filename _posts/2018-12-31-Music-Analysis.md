---
layout: post
title: The Life of Popular Music
---

![image_1](/images/blog_post_2/image_1.jpg)

The purpose of music is multifaceted. The head-bobbing rhythms; the sing-along anthems ([this is my jam](https://youtu.be/HgzGwKwLmgM)); movie soundtrack theme songs. It is no surprise how impactful music is to the world. Some might even say that there is no life without music - I am one of such folks.

Given how expansive music is, in areas such as genre and number of songs available, when it comes to distinguishing ‘good’ music from ‘great’ music, the Billboard Hot 100 charts are considered the music industry standard for defining the most popular songs for any given year. Billboard has been releasing charts since the 1950s, and I, being the curious Data Scientist with a strong passion in music, yearns to know what made songs ‘click’ for society over the last sixty-plus years.

Using Billboard Hot 100 charts from 1950-2018 and Spotify’s API, I set out to discover what music trends and shifts exist for popular music over the past six decades.

## Approach  
For this initiative, I define ‘great’ or ‘popular’ music to be any song that was listed in the Billboard Hot 100 charts. 

In terms of data collection, we can rely on two sources: 
 
1. For songs made between **1950 - 2015**, I leverage [data](https://github.com/kevinschaich/billboard/tree/master/data/years) from GitHub user Kevin Schiach. The data contains interesting features such as song sentiment, Flesch-Kincaid metric (determines how complex and readable text or song lyrics are), number of difficult words, and more  
2. Songs made **2016 and onwards** we can first manually pull artist and track names from Wikipedia with the Python library Pandas, specifically by using the 'read_html' function. Afterwards, we can capture the other features found in Kevin Schiach’s data (e.g. Flesch-Kincaid) with the help of other Python libraries such as [vaderSentiment](https://github.com/cjhutto/vaderSentiment) and [textstat](https://pypi.org/project/textstat/) 

In addition, we can utilize Spotify’s API (an endpoint called [get audio features](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/)) to capture other song features like loudness, speechiness (how many spoken words are present in a track), tempo, valence (how much musical positiveness is present in a track), and more. Combining Spotify audio features with my initial set, that brings the song features’ total count to 37.

You can find more information on all features via Kevin Schiach’s repository [here](https://github.com/kevinschaich/billboard) and Spotify for Developers page [here](https://developer.spotify.com/documentation/web-api/reference/tracks/get-audio-features/). 

## Exploratory Data Analysis

With the song features, we can proceed to do some exploratory data analysis to see how exactly did popular music change in the past sixty years. 

![f_k_metric](/images/blog_post_2/image_3.png)

Above, we can see at a high-level how music has shifted over the last six decades through two specific features: the Flesch-Kincaid Grade Level and Sentiment Analysis Score.

The Flesch-Kincaid metric uses a variety of linguistics data like average sentence length, word length, and complexity (e.g. number of syllables) to calculate the readability of text. After applying the Flesch-Kincaid metric on song lyrics, we can see how between Billboard Hot 100's inception to the 1990s, songs have gradually become less complex and easier to interpret. However, from the 90s onwards we see that popular music have become more complicated by the sharp positive slope at the end of the graph.

Sentiment Analysis is a natural language processing technique that quantifies how positive, negative, or neutral a piece of text is. The 'compound score' ranges on a scale of -1 to 1, where scores less than -0.05 represent negative sentiment texts, and scores greater than +0.05 represent positive sentiment texts. On the right, we see the sentiment analysis results, where it appears that songs have gotten less positive (angrier?) over the years.

## Genre Representation

Next, we see how much each genre represented a decade's Billboard Hot 100 charts. 

To get here, I came up with 15 aggregate genres, and based on each track's initial genre tags, I assigned each song to an aggregate genre with the help of cosine similarity calculations (see below for Python code walkthrough):

```python
# Creating 15 aggregated genres
aggregate_genres = [{"rock": ["symphonic rock", "jazz-rock", "heartland rock", "rap rock", "garage rock", "folk-rock", "roots rock", "adult alternative pop rock", "rock roll", "punk rock", "arena rock", "pop-rock", "glam rock", "southern rock", "indie rock", "funk rock", "country rock", "piano rock", "art rock", "rockabilly", "acoustic rock", "progressive rock", "folk rock", "psychedelic rock", "rock & roll", "blues rock", "alternative rock", "rock and roll", "soft rock", "rock and indie", "hard rock", "pop/rock", "pop rock", "rock", "classic pop and rock", "psychedelic", "british psychedelia", "punk", "metal", "heavy metal"]},
{"alternative/indie": ["adult alternative pop rock", "alternative rock", "alternative metal", "alternative", "lo-fi indie", "indie", "indie folk", "indietronica", "indie pop", "indie rock", "rock and indie"]},
{"electronic/dance": ["dance and electronica", "electro house", "electronic", "electropop", "progressive house", "hip house", "house", "eurodance", "dancehall", "dance", "trap"]},
{"soul": ["psychedelic soul", "deep soul", "neo-soul", "neo soul", "southern soul", "smooth soul", "blue-eyed soul", "soul and reggae", "soul"]},
{"classical/soundtrack": ["classical", "orchestral", "film soundtrack", "composer"]},
{"pop": ["country-pop", "latin pop", "classical pop", "pop-metal", "orchestral pop", "instrumental pop", "indie pop", "sophisti-pop", "pop punk", "pop reggae", "britpop", "traditional pop", "power pop", "sunshine pop", "baroque pop", "synthpop", "art pop", "teen pop", "psychedelic pop", "folk pop", "country pop", "pop rap", "pop soul", "pop and chart", "dance-pop", "pop", "top 40"]},
{"hip-hop/rnb": ["conscious hip hop", "east coast hip hop", "hardcore hip hop", "west coast hip hop", "hiphop", "southern hip hop", "hip-hop", "hip hop", "hip hop rnb and dance hall", "contemporary r b", "gangsta rap", "rapper", "rap", "rhythm and blues", "contemporary rnb", "contemporary r&b", "rnb", "rhythm & blues","r&b", "blues"]},
{"disco": ["disco"]},
{"swing":  ["swing"]},
{"folk": ["contemporary folk", "folk"]},
{"country": ["country rock", "country-pop", "country pop", "contemporary country", "country"]},
{"jazz": ["vocal jazz", "jazz", "jazz-rock"]},
{"religious": ["christian", "christmas music", "gospel"]},
{"blues": ["delta blues", "rock blues", "urban blues", "electric blues", "acoustic blues", "soul blues", "country blues", "jump blues", "classic rock. blues rock", "jazz and blues", "piano blues", "british blues", "british rhythm & blues", "rhythm and blues", "blues", "blues rock", "rhythm & blues"]},
{"reggae": ["reggae fusion", "roots reggae", "reggaeton", "pop reggae", "reggae", "soul and reggae"]}]
```
```python3
# helper function to calculate cosine similarities between each track's genre tags and tags associated with each of the 15 aggregate genre

import math
from collections import Counter

def counter_cosine_similarity(c1, c2):
    terms = set(c1).union(c2)
    dotprod = sum(c1.get(k, 0) * c2.get(k, 0) for k in terms)
    magA = math.sqrt(sum(c1.get(k, 0)**2 for k in terms))
    magB = math.sqrt(sum(c2.get(k, 0)**2 for k in terms))
    return dotprod / (magA * magB)
```
```python3
# create a series that assigns an 'aggregated genre' to each song track

list_of_genres = []

# iterate through each track
for track_tags in df_final_set['tags']:
    
    sim_counter = 0
    final_genre = 'N/A'
    
    if type(track_tags) is not float:
        if (len(track_tags) != 0):

            # iterate through each aggregated genre
            for genre in aggregate_genres:

                # pull genres associated with each aggregated genre
                for agg_genre, genres in genre.items():

                    # calculate cosine similarity between track tags vs. genres associated with aggregated genre
                    sim_temp = counter_cosine_similarity(Counter(track_tags), Counter(genres))

                    # if cosine similarity value is greater than counter, then update
                    if sim_counter < sim_temp:
                        sim_counter = sim_temp
                        final_genre = agg_genre

    # add aggregated genre to list
    list_of_genres.append(final_genre)

# create column 'agg_genre' in DataFrame
df_final_set['agg_genre'] = pd.Series(list_of_genres,index=df_final_set.index)
```

The main takeaway from this graph is how the genre 'Rock' was the Billboard Hot 100 charts' majority-genre from inception the 80's. It wasn't until the 90's that we see the uprising of other genres such as Pop, Hip-Hop / R&B, and Electronic / Dance.

![genre breakdown](/images/blog_post_2/image_4.png)

## The Rise of Hip-Hop and R&B

















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