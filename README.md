# twitter-scraping-SOTU-protectionism

```R
## load packages
library(rtweet)
library(tidyverse)
library(tidytext)
library(lubridate)
```

```R
## prepare rtweet token

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

```R
stream_tweets(
  # location = lookup_coords("usa"),
  '"buy american", "made in america"',
  timeout = 60 * 60, # last 1 hours from 10:37 PM
  file_name = "tweets_from_sotu.json",
  parse = FALSE, token = token,

```

