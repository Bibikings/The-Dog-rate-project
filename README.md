# The-Dog-rate-project

This project focuses on the data wrangling process of a popular Twitter account (@weratedogs) that does just that, show us dogs in all formas and breed.

## A brief summary into the Twitter account

>WeRateDogs is a Twitter account started in 2015 that rates people’s dogs professionally (if professional means humorous comment about the dog). The account has over 9million followers currently.

The data for this exercise was gathered over three sources listed below:

#### Twitter_archive_enhanced.csv which was downloaded manually from the Udacity classroom and loaded into the jupyter notebook using pandas.read_csv function. the dataframe was labelled dogs_df.
Code:

import pandas as pd dogs_df = pd.read_csv('twitter-archive-enhanced.csv')

##### Image_prediction_tsv hosted in the URL below https://d17h27t6h515a5.cloudfront.net/topher/2017/August/599fd2ad_image-predictions/image-predictions.tsv. The requests library was used to extract the tsv file and named image_df.
Code: import requests import os

folder_name= 'rate_dogs' if not os.path.exists(folder_name): os.makedirs(folder_name)

url = 'https://d17h27t6h515a5.cloudfront.net/topher/2017/August/599fd2ad_image-predictions/image-predictions.tsv' response = requests.get(url) response

with open(os.path.join(folder_name, url.split('/')[-1]), mode = 'wb') as file: file.write(response.content) os.listdir(folder_name)

The Tweepy library was used to extract the favourites and retweet counts for each tweet using the Twitter’s API into a json file and the json library was used to load the file directly into jupyter notebook and named tweet_df.
Code:

import tweepy from tweepy import OAuthHandler import json from timeit import default_timer as timer

consumer_key = 'HIDDEN' consumer_secret = 'HIDDEN' access_token = 'HIDDEN' access_secret = 'HIDDEN'

auth = OAuthHandler(consumer_key, consumer_secret) auth.set_access_token(access_token, access_secret)

api = tweepy.API(auth, wait_on_rate_limit=True)

tweet_ids = dogs_df.tweet_id.values len(tweet_ids)

count = 0 fails_dict = {} start = timer()

with open('tweet_json.txt', 'w') as outfile:

for tweet_id in tweet_ids:
    count += 1
    print(str(count) + ": " + str(tweet_id))
    try:
        tweet = api.get_status(tweet_id, tweet_mode='extended')
        print("Success")
        json.dump(tweet._json, outfile)
        outfile.write('\n')
    except tweepy.errors.TweepError as e:
        print("Fail")
        fails_dict[tweet_id] = e
        pass
end = timer() print(end - start) print(fails_dict)

import json df_list = []

with open('tweet-json.txt') as file: for line in file: df_list.append(json.loads(line)) tweet_df = pd.DataFrame(df_list, columns = ['id', 'retweet_count', 'favorite_count']) 

## tweet_df.head

The id column was renamed tweet_id to make merging easier.

The next step after gathering was to assess the data;

After gathering data from the three sources, the data were assessed (visually and programmatically) to identify quality and tidiness issues. Several functions were used to assess the data progamatically leveraging the pandas and numpy librabry: .info(), .sample(), .describe(), .value_counts() e.t.c.

These functions were used to determine issues around missing values, incorrect data types, multiple columns that can be collapsed into one e.t.c Underlisted are the data issues that were detected at the point of assessing:

## Dogs_df

Invalid data type for timestamp(datetime not object).
Invalid names like None and a.
Some denominator figure are either less or greater than 10 with 1 tweet_id having denominator as 0.
440 numerators are less than 10, WeRateDogs is know for mostly rating dogs above 10
There are 181 retweets, we only need original retweets Image_df

Dog breed(p1,p2 and p3) is not uniform(some start with small letters while some start with block letters).

Underscore used in dog breed as opposed to spaces.
Missing data, only 2075 rows instead of 2356 rows. Tweet_df

Missing rows (2354 and not 2356).

### Tidiness issues

Dog stages are broken down into 4 columns.
Dog breed is broken down into several columns.
All three dataset are related and should be in one dataset
The quality issues were grouped under each data frame.

Next step after assessing was cleaning:

Copies of each dataset were made before cleaning.

#### Code:

dogs_clean = dogs_df.copy() image_clean = image_df.copy() tweet_clean = tweet_df.copy()

The Define, Code and Test Method was adopted for cleaning section.

The cleaning step started with dealing with the tidiness issues.The dataframes were merged using the merge function in pandas library.

After cleaning, data was stored for analysis and visualisation. See attached document for the visualisation report.

See attached notebook for list of full codes for all stages.

 
