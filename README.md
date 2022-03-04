# twitter-scraping-SOTU-protectionism

### load packages 
```R
library(rtweet)
library(tidyverse)
library(tidytext)
library(lubridate)
```

### prepare rtweet token 

```R
app_name <- " ____ " # enter your app name

## copy and paste keys 
consumer_key <- " ____ " # enter your consumer key
consumer_secret <- " ____ "

access_token <- " ____ "
access_secret <- " ____ "

## create token
token <- create_token(
  app = app_name,
  consumer_key = consumer_key,
  consumer_secret = consumer_secret,
  access_token = access_token,
  access_secret = access_secret
)

## print token
get_token() 
```

### stream tweets starting at 22:54 PM EST 
```R
stream_tweets(
  # location = lookup_coords("usa"),
  '"buy american", "made in america"',
  timeout = 60 * 60 * 2 , 
  file_name = "tweets_from_sotu.json",
  parse = FALSE, token = token,
```

```R
sotu <- parse_stream("tweets_from_sotu.json") # parse stream

sotu$created_at <-  ymd_hms(format(sotu$created_at, tz = "America/New_York")) # turn data into EST
```

### Quickly visualize frequency of tweets over time 
```R
ts_plot(sotu, "minutes", tz="America/New_York") +
  labs(x = NULL, y = NULL,
       title = "Frequency of tweets that contain 'Buy American' or 'Made in America'",
       subtitle = paste0(format(min(sotu$created_at), "%d %B %Y"), " to ", format(max(sotu$created_at),"%d %B %Y")),
       caption = "Data collected from Twitter's REST API via rtweet") +
  theme_minimal()
```
