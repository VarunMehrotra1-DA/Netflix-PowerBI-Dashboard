# Netflix Movies & TV Shows Power BI Dashboard

## 📌 Project Overview
An interactive Power BI dashboard analyzing over 8,000+ Netflix titles to uncover trends in content types, genres, release years, and geographic distribution.

## Data Columns
a. id: A unique count of this ID would be useful while calculating and understanding trends. Example: The number of movies released in 2020 would simply be a unique count of the ID field.

b. release_year: At the outset, it seems that it includes movies from 1953 to 2022, i.e. 70 years of data. But on closer inspection, we notice that some years are missing e.g. 1955, 1957. 

c. title: The dataset becomes very interesting as many of the titles can be recognised.

d. type: If we use the FILTER option in Excel to see the different TYPE values that are there, we will see two: MOVIE and SHOW.

e. description: Although for our analysis, description may not be very important but we can extract keywords out of it and use machine learning to extract information like the genre of the movie/show.

f. age_certification: Something to note here is that in many cases age certification is missing. We may want to handle missing values separately in some cases, but here since there is no effective way to substitute this missing information, we will leave it as is for our analysis.

g. runtime: Although it is not mentioned, it is clear that runtime is in minutes. One should see the range of values, visualise the distribution and if required, handle outliers in this field.

h. imdb_score: This is the IMDb rating of the movie/show. This tells us how well the movie was rated by the voters.
i. imdb_votes: This is the number of people who voted on IMDb for a movie or show. It is important to recognise that this leads to a lot of subjectivity because if very few people have voted for a movie/show its imdb_score will not be very reliable.

j. The dataset has 5283 records and this data includes both movies and shows.

## Problem Statements
As an analyst, your goal is to help the NETFLIX derive insights from the data. These insights will help them take decisions w.r.t. TV & Movie Shows. Eventually it will help them to increase the number of new movies and TV shows. Your manager asks you to perform the following operations and get answers to key questions. 

1. Which was the best movie and TV show overall in the last 50 years?
2. How many movies do we have in our dataset across the last few years? Do we have
more representation of movies from the last 20 years, or is the dataset free from any
such skewness?
3. On average, how has the IMDb score been trending over the last 50 years? Has it
been deteriorating or improving?
4. Have more people started voting for movies/shows on IMDb over the last 50 years?
5. On average, how has the runtime changed over the last 50 years?
6. How does age certification of a movie affect its rating? 

---
## 🛠️ Solution Approach

### 1. Data Cleaning & Transformation (Power Query)
1. We deleted the unwanted blank rows in the dataset sheet of orders.
2. We replaced all null values to 'Unknown' as it was more than 5% iin age certification column.
3. We replaced all null values to 0 as it was more than 5% in imdb score column.
4. We replaced all 0s to 38.63 (runtime avg for tv shows) as it was for only TV SHOWS and not more than 5% in runtime column.
5. We created a custom column as overall rating by multiplying imdb score to imdb votes as it was required to evaluate on even grounds.
6. Change data type of overall rating to number.
7. We created a custom column for image generation in report view inside the slicer for easier evaluation of release year.

### 2. Data Analysis Expressions (DAX)
Highlight the key metrics you created. Use code blocks to make your DAX readable:
```dax
"overall_rating", each [imdb_score]*[imdb_votes]
```
```dax
if[release_year]<=1992 then "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRUBeQKzo1SXLUfUygjGrCUJsGV69GQ5zkmx4BAl-UGpA&s=10" 
else if[release_year]>=1993 and [release_year]<=2012 then "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS-NipSWXCX7SzNMWuP9uj_DmZNdAsOKIlto9bM7aNpsA&s=10"
else if[release_year]>=2013 then "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTUKAM9cvQo5Bgi_MKBElABBlRI1Tf79PSTfQk1vU0XQg&s=10"
else "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTfA8pVKVbLzl4Emj9u5Xbd5l5Ht9nZ4qzr6USqt-9jOg&s=10")
```
```dax
Avg_Votes_Per_Title = AVERAGE(Netflix[imdb_votes])
```
```dax
TITLES = COUNT(Netflix[title])
```
```dax
Year_Classification = 
VAR ReleaseYear = Netflix[release_year]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(ReleaseYear), "Unknown",
    ReleaseYear<= 1992, "Classic",
    ReleaseYear>= 1993 && ReleaseYear<=2012, "DVD Era",
    ReleaseYear>= 2013, "Streaming", 
    "Other"
)

---

## Live Preview
![Dashboard Screenshot](screenshot1.png) 

## 💡 Key Insights
* **Insight 1:** Best of the last 50 years
Vote-weighted IMDb rating. 
BEST MOVIE
Inception (2010)
2.27M votes
BEST TV SHOW
Breaking Bad (2008)
1.73M votes
* **Insight 2:** Skewed catalogue
95% of titles are from the last 20 years; pre-2000 is only 4%
* **Insight 3:** Voting over last 50 Years
total votes per decade are highest for the 2010s (about 68M), but only because the catalogue is much larger & newer titles have had less time to collect votes.
* **Insight 4:** Age Certification & Rating
the differences are significant (p < 0.001) but modest, with certification explaining about 4% of the variance. 
* **Insight 5:** Ratings softening
Movies fell from 6.55 to 6.19 (2003–12 vs 2013–22) as volume surged.
* **Insight 6:** Format shift
Movies ~126 to 97 min since the 1970s; shows 26 to 40 min.

## 💡 Key Performance Indicators
* **Indicator 1:** Overall total titles is 5262 (COUNT of titles) 
* **Indicator 2:** Average of IMDB VOTES is 23.32K 
* **Indicator 3:** Average of RUNTIME is 79 mins
* **Indicator 4:** Average of Overall Rating

## Tools Used
* Power BI Desktop (Data Transformation, DAX Modeling, Visualizations)
* Netflix Dataset (netflix_titles.xlsx)
