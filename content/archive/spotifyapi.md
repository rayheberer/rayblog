---
author: Ray Heberer
date: 2017-06-20
linktitle: Interacting with the Spotify API in R
title: Interacting with the Spotify API in R
tags: ["Spotify", "API", "R", "RStudio", "iXperience", "Code Sharing"]
highlight: false
---

### Introduction

This week at [iXperience](http://ixlink.co/honest-owl) I have been given the freedom to choose a dataset that interests me and perform some analysis. Given my background in music, Spotify seemed like a good choice.

In this post, I will show how to authorize a [Spotify App](https://developer.spotify.com/my-applications/#!/applications) from within R, in the hope that my many trials and errors will benefit others who could potentially be interested in this rich source of data.

### Spotify Web API Authentication

Having worked with the Twitter API last week ([@RobertRealGuy](https://twitter.com/robertrealguy)), I initially hoped that the Spotify API would be equally tractable in R. Unfortunately, while the Twitter API's OAuth process is basically accomplished in one step using the [twitteR package](https://cran.r-project.org/web/packages/twitteR/twitteR.pdf), Spotify has no packages on CRAN just yet, and I was not successful with the two in-development packages I found on GitHub.

Basically, authenticating a Spotify App and then making requests has two steps, instead of one. First, a token must be extracted from the response generated by the POST request in which the client ID and secret are provided. Then, and this is the important bit, that token must be specified as a header in every subsequent request made to the APIs endpoints. At least, that is my understanding.

Anyways, on to the code. I will be loading up a few libraries that will be used further down this post as well.

```
library(httr)
library(magrittr)
library(rvest)
library(ggplot2)

clientID = "aSIfiwOUlD"
secret = "sH0wTH3sEpUBlicLy"

response = POST(
  'https://accounts.spotify.com/api/token',
  accept_json(),
  authenticate(clientID, secret),
  body = list(grant_type = 'client_credentials'),
  encode = 'form',
  verbose()
)

token = content(response)$access_token

```

Because the authorization header in the requests I will be making using this token have to be of a certain form (having the word "bearer"), I assign some formatted text to a new variable.

```
authorization.header = paste0("Bearer ", token)
```

With this, you should be fully equipped to start interacting with the Spotify API. I hope this has solved some difficulties and answered some questions, and if you'd like to stick around, I'll briefly demonstrate what sort of things are possible with the API.

### Data Wrangling

It's instructive to look through the [list of endpoints](https://developer.spotify.com/web-api/endpoint-reference/) Spotify's Web API gives developers access to. Very quickly, one might notice that having a track's ID is quite essential for many of the requests.

Since I wanted the tracks in my dataset to have some sort of significance, I went to [Spotify Charts](https://spotifycharts.com/regional/global/weekly/latest) to pull the weekly top 200 IDs. Now, extracting these IDs is just an exercise in web scraping, and I will leave my code below.

```
weekly.top.songs = read_html("https://spotifycharts.com/regional/global/weekly/latest") %>%
  html_nodes("#content > div > div > div > span > table > tbody > tr > td.chart-table-image > a")

id.start = regexpr("/track/", weekly.top.songs) # seems to always be 7
id.end = regexpr('" target="', weekly.top.songs) # seems to always be 63

top.song.ids = substr(weekly.top.songs, id.start+7, id.end-1)
```

top.song.ids is a character vector with a length of 200. This is what I will looping through to gather data about these tracks using the Spotify API.

### Making API Requests

The endpoints that interested me the most were the ones pertaining audio features and audio analysis. Of course, getting basic information through the tracks-by-ID endpoint also seemed to be a good idea.

Notice how I use that authorization.headers variable I made earlier as the value of the config argument in the GET function (made available throught he [httr package](https://cran.r-project.org/web/packages/httr/index.html)).

Since I want to get results for all 200 ids in my list, I'm also wrapping the call in the lapply() function.

```
tracks = lapply(1:200, function(n) {
  GET(url = paste("https://api.spotify.com/v1/tracks/", top.song.ids[n], sep = ""),
                        config = add_headers(authorization = authorization.header))
})

features = lapply(1:200, function(n) {
  GET(url = paste0("https://api.spotify.com/v1/audio-features/", top.song.ids[n]),
               config = add_headers(authorization = authorization.header))
})
```

These return lists of objects of the type "response." Below I will show how I converted the "features" list into a tidy data frame. Though not shown, I went through a similar process with the "tracks" list.

```
features.content = sapply(1:200, function(n) {
  content(features[[n]])
})

features.content = t(features.content)

features.df = cbind(rank = 1:200, rank.desc = 200:1, danceability = features.content[,1], 
                    energy = features.content[,2], key = features.content[,3], 
                    loudness = features.content[,4], mode = features.content[,5],
                    speechiness = features.content[,6], acousticness = features.content[,7],
                    instrumentalness = features.content[,8], liveness = features.content[,9],
                    valence = features.content[,10], tempo = features.content[,11], 
                    duration_ms = features.content[,17], time_signature = features.content[,18])

features.df = features.df %>% as.data.frame

for (i in 1:ncol(features.df)) {
  features.df[,i] = unlist(features.df[,i])
}

head(features.df)

##   rank rank.desc danceability energy key loudness mode speechiness acousticness
## 1    1       200        0.694  0.815   2   -4.328    1      0.1200     0.229000
## 2    2       199        0.599  0.667   7   -4.267    1      0.0367     0.053300
## 3    3       198        0.548  0.650   8   -5.827    0      0.0591     0.219000
## 4    4       197        0.825  0.652   1   -3.183    0      0.0802     0.581000
## 5    5       196        0.904  0.611   1   -6.842    0      0.0888     0.000259
## 6    6       195        0.774  0.626   3   -4.432    0      0.0432     0.096900
##   instrumentalness liveness valence   tempo duration_ms time_signature
## 1         0.00e+00   0.0924   0.826  88.931      228827              4
## 2         0.00e+00   0.1340   0.811  80.984      288877              4
## 3         0.00e+00   0.2250   0.554 144.937      194897              4
## 4         0.00e+00   0.0931   0.933  95.977      233713              4
## 5         2.03e-05   0.0976   0.427 150.020      177000              4
## 6         3.12e-05   0.0848   0.758 100.041      211475              4
```

The appropriate indexes for features.content were found by inspection. The content() function also proved to be invaluable.

### Exploratory Data Analysis Part 1 - Summary Statistics

I didn't hold high hopes for being able to find incredibly insightful correlations between the entries in my 200-track dataset. However, I did believe that finding some summary statistics and plotting distributions for values contained in the features could be useful. But first, it was important that I understood what all of these numbers meant. For this, I needed to look at the reference for the [Audio Features Object](https://developer.spotify.com/web-api/get-audio-features/).

Interestingly, "liveness", "instrumentalness", and "acousticness" are all probabilities, or confidence measures. That means that they are themselves outputs of some classification model that produces probabilities, such as logistic regression, or a naive Bayes classifier. Because of this, I wouldn't exactly know how to interpret the summary statistics. Also, it's worth noting that measures such as "key", "mode" and "time signature" can only take on certain integer values. 

Everything else seems like something that having statistics for would be meaningful. I'll spare the experienced computer scientists in my audience the cringe factor of having to look at my separate sapply() functions for every statistic.


```
feature.summaries = cbind(feature = names(features.df)[-c(1,2)],
                          mean = feature.means,
                          median = feature.medians,
                          standard.deviation = feature.sds,
                          min = feature.mins,
                          max = feature.maxes,
                          range = feature.maxes-feature.mins,
                          range.over.sd = (feature.maxes-feature.mins)/feature.sds,
                          skewness = 3*(feature.means-feature.medians)/feature.sds)
      
##    feature            mean           median    standard.deviation  min        max
## [1,] "danceability"     "0.694325"     "0.7215"  "0.122961118073109" "0.314" "0.953"  
## [2,] "energy"           "0.666735"     "0.673"   "0.152997634477723" "0.262"    "0.962"  
## [3,] "key"              "5.235"        "5.5"     "3.56134678945675"  "0"        "11"     
## [4,] "loudness"         "-5.72471"     "-5.5255" "1.93518286721119"  "-12.007"  "-1.715" 
## [5,] "mode"             "0.615"        "1"       "0.487816383612321" "0"        "1"

## [6,] "speechiness"      "0.1101095"    "0.06675" "0.100544386856711" "0.0232"   "0.476"  
## [7,] "acousticness"     "0.17247903"   "0.1095"  "0.183495450299763" "0.000259" "0.968"  
## [8,] "instrumentalness" "0.0054312726" "0"       "0.028609255324031" "0"        "0.254"  
## [9,] "liveness"         "0.1630265"    "0.125"   "0.11237274409673"  "0.0456"   "0.727"  
##[10,] "valence"          "0.5194325"    "0.516"   "0.214750372306497" "0.0854"   "0.965"  
##[11,] "tempo"            "120.610345"   "112.993" "29.5928094030934"  "74.989"   "199.864"
##[12,] "duration_ms"      "218517.67"    "213167"  "33383.5367807405"  "126346"   "343150" 
##[13,] "time_signature"   "3.97"         "4"       "0.171015295093097" "3"        "4"

##      range      range.over.sd      skewness            
## [1,] "0.639"    "5.196764717283"   "-0.66301446569092" 
## [2,] "0.7"      "4.57523413606714" "-0.122845036553404"
## [3,] "11"       "3.0887191420294"  "-0.223230156173943"
## [4,] "10.292"   "5.318360437343"   "-0.308823527805023"
## [5,] "1"        "2.04995164900965" "-2.36769415460614" 
## [6,] "0.4528"   "4.50348362704026" "1.2937420383833"   
## [7,] "0.967741" "5.27392367723055" "1.02965544754024"  
## [8,] "0.254"    "8.87824576778296" "0.569529602062506" 
## [9,] "0.6814"   "6.06374797978996" "1.01518834408636"  
##[10,] "0.8796"   "4.09591839377403" "0.0479510228056928"
##[11,] "124.875"  "4.21977509127425" "0.772215800423843" 
##[12,] "216804"   "6.49433885402692" "0.480836111087567" 
##[13,] "1"        "5.84743019304574" "-0.526268717374113"
```

For skewness, I'm using [Pearson's Coefficient of Skewness](http://www.statisticshowto.com/pearsons-coefficient-of-skewness/). So far, nothing here seems too surprising, although if the distributions are normal enough, then making statements about the likelihood that a song is in a top chart given a certain quality by using a z-score would be something that could plausibly be done.

### Visualizations and Final Thoughts

We're straying a bit far from the main purpose of this post, which was to help others get started with the Spotify API in R. I think that this demonstration so far has shown that the data is rather easy to work with, so I'll just end with some initial visualizations of the distributions of these variables, and briefly discuss one interesting result.

```
ggplot(features.df, aes(x = danceability)) + geom_histogram(bins = 25) + theme_minimal()
ggplot(features.df, aes(x = energy)) + geom_histogram(bins = 25) + theme_minimal()
ggplot(features.df, aes(x = valence)) + geom_histogram(bins = 25) + theme_minimal()
ggplot(features.df, aes(x = mode)) + geom_bar() + theme_minimal()
ggplot(features.df, aes(x = key)) + geom_bar() + theme_minimal()
```

![](/img/Danceability.png)


![](/img/Energy.png)


![](/img/Valence.png)


![](/img/Mode.png)


![](/img/Key.png)


Already, I have a number of options to explore:


-Valence clearly seems to have multiple peaks. Sad songs, happy songs, and in-between?

-Can a song's likelihood of making the top charts be inferred from its danceability?

-Why are so few songs on the top charts in the key of D#/Eb? Human vocal ranges?

I will be undertaking a more involved interrogation of the data in the next few days, and pushing my results to a [GitHub Repository](https://github.com/rayheberer/spotify). Until then, I hope that this post has at the very least brought some peoples' attention to the wealth of data available through the Spotify Web API.