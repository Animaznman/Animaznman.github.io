---
layout: post

title: First Project: [MetametacriticCritic](../blob/master/Metametacriticcritic.ipynb)
---

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
3. Evaluate model on test set and look at metrics to see how it performs.
4. Make a conclusion based on findings.

## Execution:
### [Getting the data](#1):

I knew from the start of my project, I wanted to use Metacritic as a source. This was my first time doing a webscrape project, so I wanted to a scrape a website that had a fairly simple layout. Writing the script to scrape Metacritic through beautiful soup was not too difficult, but I did end up using regular expressions to do some lifting. I rant into a bit more of a roadbloack with scraping VGChartz. While trying to access all the PC game pages, I kept being interrupted by a [429 error](https://httpstatuses.com/429). I interpreted this to mean that my web crawler was making too many requests, and had to resort to scraping VGChartz's tabular pages instead. This is disappointing because because the data that is in the webpage itself is more detailed in the number (521,535 units sold vs. .500mil units sold).

After obtaining these two sets of data, I formatted them. With VGCHartz, I only had the title and the units sold/shipped. For Metacritic, I was able to pull the title, average professional ratings, number of professional reviews, average user rating, number of user reviews, game genre, and game rating by ESRB. After joining these two sets by title, I wound up with around 3000 rows of data. I felt like this was a little sparse, compared to datasets I've looked at previously, but felt good knowing this was what I scraped on my own.

### [Important features](#2):

One of the first steps into feature engineering is actually doing exploratory data analysis. Find out which variables correlate to others. Determine which variables are collinear with each other. Search to find if one variable acts accordingly with another. In this dataset, one variable had such a case. 

The professional ratings seemed to be acting quadratically with the number of professional reviews. This made sense to me, since the more likely somebody was to rate a game high, the more likely somebody would be willing to give it a review. But higher professional ratings also coincided with higher user reviews and user ratings as well. In order to account for this, I added the critic ratings squared feature.

One of the first things I used incorrectly was the genres. I erroneously took all the genres and clumped them together into more general genres. Genres like FPS, Shooter, Fighter, and Sports, were all clumped together in an Action genre. This reduction happened to a lot of other potential features as well. The reason I think this is a misstep is because it reduced a lot of potential features that might have been better indicators for unit sale/ship.
