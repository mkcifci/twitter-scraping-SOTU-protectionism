# Simple Twitter scraping exercise: Looking at tweets about economic protectionism right after Biden's SOTU March 1,2022.

### Load packages 
```R
library(rtweet)
library(tidyverse)
library(tidytext)
library(lubridate)
```

### Prepare rtweet token 

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

### Stream tweets starting at 22:54 PM EST 
```R
stream_tweets(
  # location = lookup_coords("usa"),
  "buy american, made in america",
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
![H](https://user-images.githubusercontent.com/42550111/156684028-a869152d-f742-4008-8dcb-d2fd298d2c35.png)

### Top 10 locations (use 'location' since most tweets did not have geo-tags.

```R
sotu %>% 
  filter(!is.na(location)) %>% 
  count(location, sort = TRUE) %>% 
  top_n(10)
  
# # A tibble: 10 × 2
# location            n
# <chr>           <int>
#   1 United States     224
# 2 USA                92
# 3 California, USA    63
# 4 Texas, USA         50
# 5 Texas              39
# 6 Los Angeles, CA    35
# 7 Washington, DC     32
# 8 Florida, USA       28
# 9 Boston, MA         24
# 10 Arizona, USA       23
```

### Top 10 frequently shared links

``` 
sotu %>% 
  filter(!is.na(urls_expanded_url)) %>% 
  count(urls_expanded_url, sort = TRUE) %>% 
  top_n(10)
  
# https://www.breitbart.com/politics/2022/03/01/fact-check-biden-buy-american-feds-offshore-manufacturing/
# https://www.nationalreview.com/2022/03/the-only-thing-joe-biden-doesnt-want-made-in-america-is-energy/
# https://a.msn.com/01/en-us/AAUr4ff?ocid=winp-st
# https://hannity.com/media-room/biden-blasted-on-buy-american-if-biden-wants-to-buy-american-why-wont-he-buy-american-energy/
# https://www.breitbart.com/politics/2022/03/01/fact-check-biden-claims-he-will-push-buy-american-policies/
# https://www.cars.com/articles/2021-cars-com-american-made-index-which-cars-are-the-most-american-437020/
# https://twitter.com/hensongs/status/1498523209242714112?t=pyV85GIgdKuQwlT-3lND6Q&s=19
# https://gettr.com/post/pxot906e03
# https://twitter.com/thedemocrats/status/1498850590302130179?s=21
# https://www.wsj.com/articles/u-s-covid-19-deaths-in-2021-surpass-2020-11637426356
# https://twitter.com/Soulbeard1/status/1498887867145195521?t=l5STdmWmw2TZSJsOetXR7Q&s=19
```


### Top words

```R
words <- sotu %>%
  mutate(text = str_remove_all(text, "&amp;|&lt;|&gt;"),
         text = str_remove_all(text, "\\s?(f|ht)(tp)(s?)(://)([^\\.]*)[\\.|/](\\S*)"),
         text = str_remove_all(text, "[^\x01-\x7F]")) %>% 
  unnest_tokens(word, text, token = "tweets") %>%
  filter(!word %in% stop_words$word,
         !word %in% str_remove_all(stop_words$word, "'"),
         str_detect(word, "[a-z]"),
         !str_detect(word, "^#"),         
         !str_detect(word, "@\\S+")) %>%
  count(word, sort = TRUE)
  
# # A tibble: 4,109 × 2
# word         n
# <chr>    <int>
# 1 american  4082
# 2 buy       3800
# 3 biden     2837
# 4 america   2779
# 5 oil       1155
# 6 joe       1004
# 7 covid      673
# 8 china      591
# 9 energy     567
# 10 police     565
# # … with 4,099 more rows
```

### Wordcloud

```R
library(wordcloud) 
words %>% 
  with(wordcloud(word, n, random.order = FALSE, max.words = 150, colors = "#0062bd"))
```

![Rplot2](https://user-images.githubusercontent.com/42550111/156685430-a5b2814d-d338-4e3a-9a1e-e63a7017ce31.png)

