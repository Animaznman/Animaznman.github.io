---
layout: post

# title: First Project: [MetametacriticCritic](../blob/master/Metametacriticcritic.ipynb)
---
7/23/2019
## Inspiration:

It's not secret that I love video games. I enjoy them a lot. It's also a very volatile industry, where millions of dollars pumped into game development may not directly produce a hit game. Much like the movie industry, the gaming industry is rife with critics. These critics also are an avenue that game developers and publishers can tap into in order to help navigate their creative process. But which critics should you pay more attention to? The professional ones, or the audience where the revenue ultimately comes from?

## Methodology:
In Jupyter Notebook:
* Use Beautiful Soup to webscrape data
* Exploratory Data Analysis with pandas
* Machine Learning through sklearn
* Plotting with matplotlib and seaborn
1. <a name = "1">Get data</a> by webscraping the webpages of [Metacritic](www.metacritic.com) and [VGChartz](www.vgchartz.com).
2. Determine <a name="2">important features</a> through correlation and validation testing.
3. <a name = "3">Evaluate model</a> on test set and look at metrics to see how it performs.
4. Make a conclusion based on findings.

## Execution:
### [Getting the data](#1):

I knew from the start of my project, I wanted to use Metacritic as a source. This was my first time doing a webscrape project, so I wanted to a scrape a website that had a fairly simple layout. Writing the script to scrape Metacritic through beautiful soup was not too difficult, but I did end up using regular expressions to do some lifting. I rant into a bit more of a roadbloack with scraping VGChartz. While trying to access all the PC game pages, I kept being interrupted by a [429 error](https://httpstatuses.com/429). I interpreted this to mean that my web crawler was making too many requests, and had to resort to scraping VGChartz's tabular pages instead. This is disappointing because because the data that is in the webpage itself is more detailed in the number (521,535 units sold vs. .500mil units sold).

After obtaining these two sets of data, I formatted them. With VGCHartz, I only had the title and the units sold/shipped. For Metacritic, I was able to pull the title, average professional ratings, number of professional reviews, average user rating, number of user reviews, game genre, and game rating by ESRB. After joining these two sets by title, I wound up with around 3000 rows of data. I felt like this was a little sparse, compared to datasets I've looked at previously, but felt good knowing this was what I scraped on my own.

### [Important features](#2):

One of the first steps into feature engineering is actually doing exploratory data analysis. Find out which variables correlate to others. Determine which variables are collinear with each other. Search to find if one variable acts accordingly with another. In this dataset, one variable had such a case. 

The professional ratings seemed to be acting quadratically with the number of professional reviews. This made sense to me, since the more likely somebody was to rate a game high, the more likely somebody would be willing to give it a review. But higher professional ratings also coincided with higher user reviews and user ratings as well. In order to account for this, I added the critic ratings squared feature.

One of the first things I used incorrectly was the genres. I erroneously took all the genres and clumped them together into more general genres. Genres like FPS, Shooter, Fighter, and Sports, were all clumped together in an Action genre. This reduction happened to a lot of other potential features as well. The reason I think this is a misstep is because it reduced a lot of potential features that might have been better indicators for unit sale/ship.

### [Model Evaluation](#3):

For good model evaluation, I chose the metric of [R^2/adj]('https://en.wikipedia.org/wiki/Coefficient_of_determination').. With that in mind, I wanted to engineer my features to create a better model. I added the square of critic rating as another feature because of what was previously discussed. With the incorrect fear of having too many features, I used [LASSO regression]('https://www.analyticsvidhya.com/blog/2017/06/a-comprehensive-guide-for-linear-ridge-and-lasso-regression/') to regularize. I also noticed that my variables were not well scaled with each other. Professional ratings were on a scale of 1-100, where user ratings were averaged from 1-10. I scaled up my user ratings to match the pros.

After running the LASSO regression and then [Ordinary Least Squares]('http://setosa.io/ev/ordinary-least-squares-regression/'), my R^2/adj. values barely changed. What was worse is that my coefficients in both models often times were opposite. In the plain model, professional ratings were positive by the thousands, in the engineered model, they would be negative by the thousands. This was extremely discouraging. Other metrics that I wasn't measuring by, but were very noticeable were the [Kurtosis]('https://www.investopedia.com/terms/k/kurtosis.asp') and [Condition number]('https://blogs.mathworks.com/cleve/2017/07/17/what-is-the-condition-number-of-a-matrix/'). The kurtosis of my model was above 600. For reference, the ideal kurtosis is 2. And the condition number was exponentially higher than ideal as well.

At this point, I figured that linear regression would not meet the needs of creating a good model. I attempted using [polynomial regression]('https://towardsdatascience.com/polynomial-regression-bbe8b9d97491') instead, without more than a reason of being frustrated. This possibly could have been a point where I should have employed Poisson regression, but I had not thought of it during the time of execution. By this point since I had around 5 main features, I thought to double the possible degree of the polynomial. After looping through polynomial regression 10 times, I came to the best performing model of 8 degrees. The metric I used to measure these models was mean absolute value. This may be another point of contention because with linear regression, I chose R^2/adj to be my metric, so my metric across models is inconsistent. When I got my 8 degree model, I saw that my model had a **mean absolute error of 100,000*. The standard deviation of the error was *200,000*. This may seem inaccurate, and honestly it is. However, it is immensely more accurate than what I received from my linear regression model.

A little bit more detail about my model and its inaccuracies is that it performs fairly well when the predictions are above 200,000. But, when a the projected values were below that, the model started to go awry. Also, whenever the predictions are off, they usually shoot higher than the actual value.

### [Conclusions]("4"):

With a good (enough) model, I was able to create a model that was accurate (enough) for my purposes. The final step in my project would be to compare the feature coefficients for professional rating and user rating.:

|| Mean Absolute Error | Standard Deviation |
|---|---|---|
| Professional | 2412.3722           | 954.006            |
|  User        | 2465.3722           | 2114.967           |

I noticed that the coefficients for both the pros and users were near the same. To interpret this to real world outcome, if a company wanted to appeal more towards critics, they would increase the number of units sold by 2400 per each point gained from either professional critics or user critics. There is half as much variance in the professional usage than for the users. A reason for this may be that since there are many more user critics than professional critics, their ratings overall will vary.

But again overall, **the critics know what will sell.** If game companies just listen to them, the games will be more likely to sell more.
