# Netflix Analysis

# INTRODUCTION

Netflix is one of the most popular streaming services with over 15,000 titles ranging from movies across various genres, original TV series', anime, documentaries, and everything in between. My final project is going to focus on the content of Netflix and what you can learn from this data.

## What

My final project is a statistical analysis report on Netflix data. I found a public data set on kaggle.com that contains key information (genre, language(s), rating, runtime, IMDb score, Rotten Tomatoes score, etc) on over 15,000 titles on Netflix [1]. Within this report, you will find graphs summarizing the various data columns, descriptive statistics, correlations/relationships between variables, and predictions. 

## Why

When deciding on the topic for the final project, the only requirement I had for myself was to focus on gaining more experience in R and to practice the various aspects of statistics that we learned throughout the semester (descriptive statistics, correlations/relationships, linear regression, etc). Because my background is not in computer science, statistics, or any related field, I wanted to make sure I had a solid grasp on the basics instead of attempting a more advanced topic. I chose to analyze a data set, read it into R, and gather statistics. I don’t have a particular reason for choosing this particular topic (Netflix) or data set other than I’m an avid Netflix watcher and I’m interested in learning more about their content. 

## How

To get started, I first downloaded the public data set and opened it with Excel. The data set I chose to use for my final project contains 29 columns and over 15,000 rows. Originally, I intended to use all columns of the data set, but I quickly realized this was too ambitious and more complicated than my skill set would allow. Recognizing this, I deleted 18 columns of data that would help simplify the data imported into R and limit the scope of the statistics that could be carried out in this report. Then, I read the data into R using read.csv() and saved it as MyData. This created an object in the R environment and allowed me to create graphs and perform calculations. 

# Summary of Data with Statistical Analysis

There is a lot of the data in this data set that is categorical. In order to get a better understanding of the data and to see a graphical representation of over 15,000 titles in the Netflix database, I started with creating bar graphs for the five columns with categorical data: Genre, Language, Movie vs. TV, Run time, and Rating. Then, I created histograms and calculated the mean, median, and standard deviation for the three columns with numerical data: IMDb Score, Rotten Tomatoes Score, and Box office. 

## Graphs and Descriptive Statistics

### Genre

```{r}
library(dplyr)
library(tidyr)
TheData <- read.csv(file="631FP_Netflix_Data.csv", header=TRUE, sep=",")
EmptyGenres <- TheData %>% filter(Genre != "")
SeperateGenres <- separate_rows(EmptyGenres, Genre, sep = ", ", convert = TRUE)
par(mar=c(6,4,3,3))
barplot(table(SeperateGenres$Genre), main="Genres", ylim = c(0, 7000), names.arg = c("Action", "Adult", "Adventure", "Animation", "Biography", "Comedy", "Crime", "Documentary", "Drama", "Family", "Fantasty", "Film-Noir", "Game-Show", "History", "Horror", "Music", "Musical", "Mystery", "News", "Reality-TV", "Romance", "Sci-Fi", "Short", "Sport", "Talk-Show", "Thriller", "War", "Western"), las = 2, col = "blue")

```

**Note:** this R chunk required the dplyr library for the %>% function and the tidyr library for the seperate_rows function. 

Based on this graph, it can be seen that Drama is the genre with the most content on Netlfix. This is followed by Comedy, Thriller, and Action. To see the exact number of titles within a specific genre, use a table. For example, how many titles within Netflix are labeled "Comedy?"


```{r}
table(SeperateGenres$Genre)["Comedy"]
```

There are 5,077 titles on Netflix that are within the Comedy genre. 


### Language

```{r}
EmptyLanguages <- TheData %>% filter(Languages != "")
SeperateLanguage <- separate_rows(EmptyLanguages, Languages, sep = ", ", convert = TRUE)
# table(SeperateLanguage$Languages)
```

There are many languages on Netflix. This makes creating a bar graph challenging because there are too many categories to be represented on the x-axis. Instead, the total number of languagees can be found by finding the length of a table. Note: I do not have the table shown in this report because it took up almost 3 pages and didn't provide any crucial information. 

```{r}
LanguageTable <- table(SeperateLanguage$Languages)
length(LanguageTable)
```

There are 192 total languages represented on Netflix. 

### Movie or TV Series

```{r}
EmptyMorTV <- TheData %>% filter(Series.or.Movie != "")
barplot(table(EmptyMorTV$Series.or.Movie), main="Movie vs. TV Series", ylim = c(0, 13000), col = "red")
```

Based on the graph, you can see that there are significantly more movies than TV series on Netflix. To find the exact proportions of movie and TV series, a proportion table can be used.  

```{r}
MovieOrTV <- table(EmptyMorTV$Series.or.Movie)
prop.table(MovieOrTV)
```

75.6% of content on Netflix is movies. 24.4% of content on Netflix is a TV series. 

### Runtime

```{r}
EmptyRun <- TheData %>% filter(Runtime != "")
par(mar=c(8,4,3,3))
barplot(table(EmptyRun$Runtime), main = "Runtime", names.arg = c("< 30 minutes", "30-60 minutes", "1-2 hours", "> 2 hrs"), ylim = c(0, 10000), las = 2, col = "green")
```

According to the table, a majority of the content on Netflix has a runtime of 1-2 hours. 

### Rating

```{r}
EmptyRating <- TheData %>% filter(View.Rating != "", View.Rating != "UNRATED", View.Rating != "E", View.Rating != "NOT RATED", View.Rating != "M/PG", View.Rating != "AL", View.Rating != "U", View.Rating != "E10+", View.Rating != "TV-13", View.Rating != "X", View.Rating != "MA-17", View.Rating != "GP", View.Rating != "M", View.Rating != "NC-17", View.Rating != "Approved", View.Rating != "Passed", View.Rating != "TV-Y7-FV")
barplot(table(EmptyRating$View.Rating), main = "Ratings", names.arg = c("G", "Not Rated", "PG", "PG-13", "R", "TV-14", "TV-G", "TV-MA", "TV-PG", "TV-Y", "TV-Y7", "Unrated"), ylim = c(0, 2500), las = 2, col = "purple")
```

The most common rating is "R" followed by "PG-13," "Not Rated," and "TV-MA." Again, the exact numbers for each rating can be found using a table.

```{r}
RatingsTable <- table(EmptyRating$View.Rating)
RatingsTable["R"]
RatingsTable["PG-13"]
RatingsTable["TV-MA"]
RatingsTable["Not Rated"]
```

There are 2,096 titles with an "R" rating, 1,373 titles with a "PG-13" rating, 1,320 titles with a "Not Rated rating, and 1,136 titles with a "TV-MA" rating. 

### IMDb Score

```{r}
hist(TheData$IMDb.Score, main = "IMDb Score", xlab = "IMDb Score", col = "pink")
```

The above graph displays a distribution of the IMDb Scores of Netflix content. Looking at the graph, it can be seen that the distribution is unimodal with a slight left-skew. Below is the mean, median, and standard deviation for IMDb Score. 

#### Mean

```{r}
mean(TheData$IMDb.Score, na.rm = TRUE)
```
The average IMDb score is 6.496.

#### Median
```{r}
median(TheData$IMDb.Score, na.rm = TRUE)
```

The middle value or median in the data set is 6.6. The mean is less than the median, which means that the data is left-skewed. This can be confirmed by the above graph.

#### Standard Deviation

```{r}
sd(TheData$IMDb.Score, na.rm = TRUE)
```

The standard deviation is 1.147

### Rotten Tomatoes Score

```{r}
hist(TheData$Rotten.Tomatoes.Score, main = "Rotten Tomatoes Score", xlab = "Rotten Tomatoes Score", col = "yellow", breaks = 20)
```

The above graph displays a distribution of the Rotten Tomatoes Scores of Netflix content. Looking at the graph, it can be seen that the distribution is unimodal with a left-skew.Below is the mean, median, and standard deviation. 

### Mean

```{r}
mean(TheData$Rotten.Tomatoes.Score, na.rm = TRUE)
```

The average Rotten Tomatoes score is 59.523

### Median

```{r}
median(TheData$Rotten.Tomatoes.Score, na.rm = TRUE)
```

The middle value or median in the data set is 64. The mean is less than the median, which means that the data is left-skewed. This can be confirmed by the above graph.

### Standard Deviation

```{r}
sd(TheData$Rotten.Tomatoes.Score, na.rm = TRUE)
```

The standard deviation is 26.999.

### Box Office

```{r}
EmptyBoxoffice <- TheData %>% filter(Boxoffice != "")
EmptyBoxoffice$Boxoffice = gsub("\\$", "", EmptyBoxoffice$Boxoffice)
EmptyBoxoffice$Boxoffice = gsub("\\,", "", EmptyBoxoffice$Boxoffice)
hist(as.numeric(EmptyBoxoffice$Boxoffice), main = "Box Office", xlab = "Box Office Total", col = "orange", breaks = 20)
```

The above graph displays a distribution of the Box Office Totals for the titles on Netflix that were shown in theaters. Looking at the graph, it can be seen that the distribution is unimodal with heavy right-skew. Below is the mean, median, and standard deviation. 

### Mean

```{r}
mean(as.numeric(EmptyBoxoffice$Boxoffice), na.rm = TRUE)
```

The average box office total is 45,682,292. 

### Median

```{r}
median(as.numeric(EmptyBoxoffice$Boxoffice), na.rm = TRUE)
```

The middle value or median is 19,551,067. The mean is significantly more than the median, which means that the data is heavily right-skewed. This can be confirmed by the above graph. 

### Standard Deviation

```{r}
sd(as.numeric(EmptyBoxoffice$Boxoffice), na.rm = TRUE)
```

The standard deviation is 70,976,344. 

## Correlations/Relationships

To summarize the relationships between the numerical data, scatter plots were created and then analyzed. 


### IMDb Score and Box Office

```{r}
FilterIMDb <- TheData %>% filter(IMDb.Score != "")
FilterIMDbandBox <- FilterIMDb %>% filter(Boxoffice != "")
FilterIMDbandBox$Boxoffice = gsub("\\$", "", FilterIMDbandBox$Boxoffice)
FilterIMDbandBox$Boxoffice = gsub("\\,", "", FilterIMDbandBox$Boxoffice)
plot(x= FilterIMDbandBox$IMDb.Score, y= as.numeric(FilterIMDbandBox$Boxoffice), xlab = "IMDb Score", ylab = "Box Office")
```

Based on the above graph, the relationship between IMDb Score and Box Office is nonlinear. The graph looks similar to a bell curve. There appears to be a peak of Box Office earnings around an IMDb score of 7. The majority of data falls within this curve; however, there appear to be some outliers. A few of the highest earning titles have IMDb scores closer to 8 or 9. These outliers could be due to a number of factors including popular or A-list actors, higher filming budget, unique marketing, etc. 

### Rotten Tomatoes Score and Box Office

```{r}
FilterRotten <- TheData %>% filter(Rotten.Tomatoes.Score != "")
FilterRottenandBox <- FilterRotten %>% filter(Boxoffice != "")
FilterRottenandBox$Boxoffice = gsub("\\$", "", FilterRottenandBox$Boxoffice)
FilterRottenandBox$Boxoffice = gsub("\\,", "", FilterRottenandBox$Boxoffice)
plot(x= FilterRottenandBox$Rotten.Tomatoes.Score, y= as.numeric(FilterRottenandBox$Boxoffice), xlab = "Rotten Tomatoes Score", ylab = "Box Office")
```

Based on the above graph, there does not appear to be a correlation between Box Office and Rotten Tomatoes Score. The data falls on a horizontal line along the x-axis. The Rotten Tomatoes score does not appear to have an effect on the Box Office earnings. 

### IMDb Score and Rotten Tomatoes Score

```{r}

IMDbvsRotten <- TheData %>% filter(IMDb.Score != "" & Rotten.Tomatoes.Score != "")
plot(x= IMDbvsRotten$Rotten.Tomatoes.Score, y= IMDbvsRotten$IMDb.Score, xlab = "Rotten Tomatoes Score", ylab = "IMDb Score")
```

Based on the scatter plot, it can be seen that IMDb Score and Rotten Tomatoes Score have a positive linear relationship. Although there are some outliers, the variation remains constant and the majority of data lies along the line. 

Because the relationship is linear, the assumptions (conditions) of linear regression were checked to see if they were met in order to apply the linear regression model. 

Assumptions of Linear Regression:
1. The Y-values, errors, are independent. 
2. The y values can be expressed as a linear function of the x variable. 
3. The variations around the regression line is constant.
4. The y values (or residuals) are normally distributed.[2] 

Notice in the code below the Rotten Tomatoes Score has been divided by 10. This accounts for the IMDb being out of 10 and Rotten Tomatoes being out of 100. Now the two scores are  comparable which allows for more accurate calculations and plots. 

```{r}
AdjustScore <- IMDbvsRotten$Rotten.Tomatoes.Score / 10
LinReg <- lm(IMDbvsRotten$IMDb.Score ~ AdjustScore)
par(mfrow = c(2, 2))
plot(LinReg)
```

The first assumption, independence of the residuals, is true. The IMDb scores are not dependent on the Rotten Tomatoes score. 

In the first graph (the residual plot), it can be seen that the linearity assumption is met as the red-line is flat meaning that there is no pattern. It can also be seen that the variation of residuals is constant. 

In the second graph (Q-Q plot), it can be seen that the y values are normally distributed because the points fall roughly on a diagonal line. 

All four conditions of linear regression are met, so the model can be applied. 

```{r}
summary(LinReg)
```

The slope and intercept can be found using the summary table above. 

**The line of regression is y = 0.271x + 4.83** Below is the scatter plot with the line of regression in red. 

```{r}
plot(x= IMDbvsRotten$Rotten.Tomatoes.Score, y= IMDbvsRotten$IMDb.Score, xlab = "Rotten Tomatoes Score", ylab = "IMDb Score")
abline(LinReg, col = "red")
```
