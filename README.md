# Netflix Analysis

## Introduction

Netflix is one of the most popular streaming services with over 15,000 titles ranging from movies across various genres, original TV series', anime, documentaries, and everything in between. My final project is going to focus on the content of Netflix and what you can learn from this data.

## What

My final project is a statistical analysis report on Netflix data. I found a public data set on kaggle.com that contains key information (genre, language(s), rating, runtime, IMDb score, Rotten Tomatoes score, etc) on over 15,000 titles on Netflix. Within this report, you will find graphs summarizing the various data columns, descriptive statistics, correlations/relationships between variables, and predictions. 

## How

To get started, I first downloaded the public data set and opened it with Excel. The data set I chose to use for my final project contains 29 columns and over 15,000 rows. Originally, I intended to use all columns of the data set, but I quickly realized this was too ambitious and more complicated than my skill set would allow. Recognizing this, I deleted 18 columns of data that would help simplify the data imported into R and limit the scope of the statistics that could be carried out in this report. Then, I read the data into R using read.csv() and saved it as MyData. This created an object in the R environment and allowed me to create graphs and perform calculations. 

## Summary of Data with Statistical Analysis

There is a lot of the data in this data set that is categorical. In order to get a better understanding of the data and to see a graphical representation of over 15,000 titles in the Netflix database, I started with creating bar graphs for the five columns with categorical data: Genre, Language, Movie vs. TV, Run time, and Rating. Then, I created histograms and calculated the mean, median, and standard deviation for the three columns with numerical data: IMDb Score, Rotten Tomatoes Score, and Box office. 
 
## Correlations & Relationships

To summarize the relationships between the numerical data, scatter plots were created and then analyzed. 

### IMDb Score and Box Office

```{r}
FilterIMDb <- TheData %>% filter(IMDb.Score != "")
FilterIMDbandBox <- FilterIMDb %>% filter(Boxoffice != "")
FilterIMDbandBox$Boxoffice = gsub("\\$", "", FilterIMDbandBox$Boxoffice)
FilterIMDbandBox$Boxoffice = gsub("\\,", "", FilterIMDbandBox$Boxoffice)
plot(x= FilterIMDbandBox$IMDb.Score, y= as.numeric(FilterIMDbandBox$Boxoffice), xlab = "IMDb Score", ylab = "Box Office")
```

<img width="834" alt="Screen Shot 2024-04-03 at 2 00 58 PM" src="https://github.com/sarahkpree/Netflix_Analysis/assets/61251211/c6a02933-4e99-46c1-8483-f16fa57c77fc">


Based on the above graph, the relationship between IMDb Score and Box Office is nonlinear. The graph looks similar to a bell curve. There appears to be a peak of Box Office earnings around an IMDb score of 7. The majority of data falls within this curve; however, there appear to be some outliers. A few of the highest earning titles have IMDb scores closer to 8 or 9. These outliers could be due to a number of factors including popular or A-list actors, higher filming budget, unique marketing, etc.

### Rotten Tomatoes Score and Box Office

```{r}
FilterRotten <- TheData %>% filter(Rotten.Tomatoes.Score != "")
FilterRottenandBox <- FilterRotten %>% filter(Boxoffice != "")
FilterRottenandBox$Boxoffice = gsub("\\$", "", FilterRottenandBox$Boxoffice)
FilterRottenandBox$Boxoffice = gsub("\\,", "", FilterRottenandBox$Boxoffice)
plot(x= FilterRottenandBox$Rotten.Tomatoes.Score, y= as.numeric(FilterRottenandBox$Boxoffice), xlab = "Rotten Tomatoes Score", ylab = "Box Office")
```

<img width="811" alt="Screen Shot 2024-04-03 at 2 01 24 PM" src="https://github.com/sarahkpree/Netflix_Analysis/assets/61251211/40eb8389-7180-47fa-876e-77aecc0dafa7">

Based on the above graph, there does not appear to be a correlation between Box Office and Rotten Tomatoes Score. The data falls on a horizontal line along the x-axis. The Rotten Tomatoes score does not appear to have an effect on the Box Office earnings.

### IMDb Score and Rotten Tomatoes Score

```{r}

IMDbvsRotten <- TheData %>% filter(IMDb.Score != "" & Rotten.Tomatoes.Score != "")
plot(x= IMDbvsRotten$Rotten.Tomatoes.Score, y= IMDbvsRotten$IMDb.Score, xlab = "Rotten Tomatoes Score", ylab = "IMDb Score")
```
<img width="816" alt="Screen Shot 2024-04-03 at 2 02 13 PM" src="https://github.com/sarahkpree/Netflix_Analysis/assets/61251211/ac762834-be55-4c2b-bf78-323618e86ae0">

Based on the scatter plot, it can be seen that IMDb Score and Rotten Tomatoes Score have a positive linear relationship. Although there are some outliers, the variation remains constant and the majority of data lies along the line. 

Because the relationship is linear, the assumptions (conditions) of linear regression were checked to see if they were met in order to apply the linear regression model. 

**Assumptions of Linear Regression:**
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
<img width="813" alt="Screen Shot 2024-04-03 at 2 02 40 PM" src="https://github.com/sarahkpree/Netflix_Analysis/assets/61251211/058a32a2-1960-4305-88fe-f44f9fa23da9">

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
