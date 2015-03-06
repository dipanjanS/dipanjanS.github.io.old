---
published: false
---


This is the second post regarding Twitter Mining. We have already talked about [Twitter Mining in depth](http://dipanjans.github.io/twitter-mining-1/) earlier and also how to [analyze social trends in general and gather insights from YouTube](http://dipanjans.github.io/social-trend-analytics/). If you are more interested in developing a quick sentiment analysis app, you can check my [short tutorial](http://dipanjans.github.io/twitter-sentiment-app/) on that as well.

Today, I will be talking about how we can get data from Twitter in real-time and perform some interesting analytics on top of that to understand social reactions to trending brands and products.

In our last post, we had used Twitter’s [Search API](https://dev.twitter.com/rest/public/search) for getting a selective set of tweets and performed some analytics on that. But today, we will be using Twitter’s [Streaming API](https://dev.twitter.com/streaming/overview), to access data feeds in real time. A couple of differences with regards to the two APIs are as follows. The Search API is primarily a REST API which can be used to query for “historical data”. However, the Streaming API gives us access to Twitter’s global stream of tweets data. Moreover, it lets you acquire much larger volumes of data with keyword filters in real-time compared to normal search.

<br>
## Installing Dependencies

I will be using Python for my analysis as usual, so you can install it if you don’t have it already. You can use another language of your choice, but remember to use the relevant libraries of that language. To get started, install the following packages, if you dont have them already. You are most welcome to use the stock `json` library instead of the `simplejson` library.

```sh
[root@dip]# pip install twitter           
[root@dip]# pip install simplejson
[root@dip]# pip install prettytable                       
[root@dip]# pip install matplotlib
[root@dip]# pip install nltk
```

<br>
## Acquiring Data

We will use the Twitter Streaming API and the equivalent python wrapper to get the required tweets. Since we will be looking to get a large number of tweets in real time, there is the question of where should we store the data and what data model should be used. In general, when building a robust API or application over Twitter data, MongoDB being a schemaless document-oriented database, is a good choice. It also supports expressive queries with indexing, filtering and aggregations. However, since we are going to analyze a relatively small sample of data using `pandas`, we shall be storing them in flat files.

> Note: Should you prefer to sink the data to MongoDB, the mongoexport command line tool can be used to export it to a newline delimited format that is exactly the same as what we will be writing to a file.

The following code snippet shows you how to create a connection to Twitter’s Streaming API and filter for tweets containing a specific keyword. For simplicity, each tweet is saved in a newline delimited file as a JSON document. Since we will be dealing with products and brands, I have queried on two trending products and brands respectively. They are, **'Sony'** and **'Microsoft'** with regards to brands and **'iPhone 6'** and **'Galaxy S5'** with regards to products. You can write the code snippet as a function for ease of use and call it for specific queries to do a comparative study.

```python
import io
import simplejson as json
import twitter
 
# Go to https://apps.twitter.com/ to create an app and get values for these credentials    
CONSUMER_KEY = ''
CONSUMER_SECRET = ''
OAUTH_TOKEN = ''
OAUTH_TOKEN_SECRET = ''
 
# Authenticate with OAuth    
auth = twitter.oauth.OAuth(OAUTH_TOKEN, OAUTH_TOKEN_SECRET,
                               CONSUMER_KEY, CONSUMER_SECRET)
    
# Create a connection to the Twitter Streaming API
twitter_stream = twitter.TwitterStream(auth=auth)
 
QUERY = 'microsoft'
OUT_FILE = 'tweets_'+QUERY+'.json'
 
print 'Filtering the public timeline for "{0}"'.format(QUERY)
 
stream = twitter_stream.statuses.filter(track=QUERY)
 
# Write one tweet per line as a JSON document. 
with io.open(OUT_FILE, 'a', encoding='utf-8',buffering=1) as f:
    for tweet in stream:
        f.write(unicode(u'{0}\n'.format(json.dumps(tweet, ensure_ascii=False))))
        print tweet['text']
```

Let the data stream for a significant period of time so that you can capture a sizeable sample of tweets.

<br>
## Analyses and Visualizations

Now that you have amassed a collection of tweets from the API in a newline delimited format, let's start with the analyses. One of the easiest ways to load the data into pandas is to build a valid JSON array of the tweets. This can be accomplished using the following code segment.

```python
import pandas as pd
 
DATA_FILES = ['tweets_microsoft.json', 'tweets_sony.json', 'tweets_galaxys5.json', 'tweets_iphone6.json']
data_frames = dict()
 
for data_file in DATA_FILES:
    data = "[{0}]".format(",".join([line for line in open(data_file).readlines()]))
    data_frames[data_file.split('_')[1].split('.')[0]] = pd.read_json(data, orient='records')
    
# All the values should be of data frame type
print {k:type(v) for k,v in data_frames.items()}
 
# to see an individual sample data frame
print data_frames['sony']
```

> Note: With pandas, you will need to have an amount of working memory proportional to the amount of data that you’re analyzing.

Once you run this, you should get a dictionary containing 4 data frames. The output I obtained is shown in the snapshot below.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*LMKVvwtThfLZVvDe_ZSIQQ.png)

> Note: Per the Streaming API guidelines, Twitter will only provide up to 1% of the total volume of real time tweets, and anything beyond that is filtered out with each "limit notice".

The next snippet shows how to remove the “limit notice” column if you encounter it.

```python
# Capture the limit notices by indexing into the data frame for non-null field containing "limit"
 
# df is a data frame here
limit_notices = df[pd.notnull(df.limit)]
 
# Remove the limit notice column from the DataFrame entirely
df = df[pd.notnull(df['id'])]
 
print "Number of total tweets that were rate-limited", sum([ln['track'] for ln in limit_notices.limit])
print "Total number of limit notices", len(limit_notices)
```

<br>
### Time-based Analysis

Each tweet we captured had a specific time when it was created. To analyze the time period when we captured these tweets, let’s create a time-based index on the `created_at` field of each tweet so that we can perform a time-based analysis to see at what times do people post most frequently about our query terms.

```python
from prettytable import PrettyTable
 
pt = PrettyTable()
pt = PrettyTable(['Brand \ Product', 'First tweet timestamp (UTC)', 'Last tweet timestamp (UTC)'])
 
for key in sorted(data_frames.keys()):
    pt.add_row([key, data_frames[key]['created_at'][0], data_frames[key]['created_at'][-1]])
    
print pt
```

The output I obtained is shown in the snapshot below.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*2z7CnPjnBn-wjDoxcEJ-NQ.png)

I had started capturing the Twitter stream at around 7 pm on the 6th of December, 2014 and stopped it at around 11:45 am on the 7th of December, 2014. So the results seem consistent based on that. With a time-based index now in place, we can trivially do some useful things like calculate the boundaries, compute histograms and so on. Operations such as grouping by a time unit are also easy to accomplish and seem a logical next step. The following code snippet illustrates how to group by the `hour` of our data frame, which is exposed as a `datetime.datetime` timestamp since we now have a time-based index in place. We print an hourly distribution of tweets also just to see which brand/product was most talked about on Twitter during that time period.

```python
brands = ['sony', 'microsoft']
products = ['iphone6', 'galaxys5']
 
brands_grouped_time = [data_frames[key].groupby(lambda x: x.hour) for key in brands]
products_grouped_time = {key:data_frames[key].groupby(lambda x: x.hour) for key in products}
 
# dividing by 1000 in the distribution because people talked more about brands during this time period    
for brand, brand_grouped_time in brands_grouped_time.items():
    print "\nNumber of relevant tweets by the hour (UTC) for", brand
    pt = PrettyTable(['Hour', 'Total Tweets', 'Tweet Distribution'])
    pt.align["Tweet Distribution"] = "l"
    for hour, group in brand_grouped_time:
        pt.add_row([hour, len(group), '*'*(len(group) / 1000)])
    print pt
 
# dividing by 100 in the distribution because people talked less about products during this time period    
for product, product_grouped_time in products_grouped_time.items():
    print "\nNumber of relevant tweets by the hour (UTC) for", product
    pt = PrettyTable(['Hour', 'Total Tweets', 'Tweet Distribution'])
    pt.align["Tweet Distribution"] = "l"
    for hour, group in product_grouped_time:
        pt.add_row([hour, len(group), '*'*(len(group) / 100)])
    print pt
```

The outputs I obtained are depicted in the snapshot below.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*rpeisIN_BoF7N-b0t1NimQ.png)
![](https://d262ilb51hltx0.cloudfront.net/max/800/1*nDCnIRq6g3K9T3TYe9ZG3A.png)

The `Hour` field here follows a 24 hour format. What is interesting here is that, people have been talking more about **Sony** than **Microsoft** in **Brands**. In **Products**, **iPhone 6** seems to be trending more than **Samsung's Galaxy S5**. Also the trend shows some interesting insights that people tend to talk more on Twitter in the morning and late evenings.

<br>
### Time-based Visualizations

It could be helpful to further subdivide the time ranges into smaller intervals so as to increase the resolution of the extremes. Therefore, let’s group into a custom interval by dividing the hour into 15-minute segments. The code is pretty much the same as before except that you call a custom function to perform the grouping. This time, we will be visualizing the distributions using `matplotlib`.

```python
import matplotlib.pyplot as plt
 
def group_by_15_min_intervals(x):
    if   0 &lt;= x.minute &lt;= 15: return (x.hour, "0-15")
    elif 15 &lt; x.minute &lt;= 30: return (x.hour, "16-30")
    elif 30 &lt; x.minute &lt;= 45: return (x.hour, "31-45")
    else: return (x.hour, "46-00")
    
brands_grouped_time = {key:data_frames[key].groupby(lambda x: group_by_15_min_intervals(x)) for key in brands}
products_grouped_time = {key:data_frames[key].groupby(lambda x: group_by_15_min_intervals(x)) for key in products}
 
# Plot for brands
plt.ylabel("Tweet Volume")
plt.xlabel("Time")
plt.title("Brands Social Trend")
plt.plot([float(str(hour[0])+'.'+hour[1].split('-')[0]) for hour, group in brands_grouped_time['sony']][1:-1], [len(group)for hour, group in brands_grouped_time['sony']][1:-1],'r', label='Sony')
plt.plot([float(str(hour[0])+'.'+hour[1].split('-')[0]) for hour, group in brands_grouped_time['microsoft']][1:-1], [len(group)for hour, group in brands_grouped_time['microsoft']][1:-1],'b', label='Microsoft')
plt.legend()
 
# Plot for products
plt.ylabel("Tweet Volume")
plt.xlabel("Time")
plt.title("Products Social Trend")
plt.plot([float(str(hour[0])+'.'+hour[1].split('-')[0]) for hour, group in products_grouped_time['iphone6']][1:-1], [len(group)for hour, group in products_grouped_time['iphone6']][1:-1],'r', label='iPhone 6')
plt.plot([float(str(hour[0])+'.'+hour[1].split('-')[0]) for hour, group in products_grouped_time['galaxys5']][1:-1], [len(group)for hour, group in products_grouped_time['galaxys5']][1:-1],'b', label='Galaxy S5')
plt.legend()
```

The two visualizations are depicted below. Ofcourse don’t forget to ignore the section of the plots from 11:30 am to around 7 pm because during this time no tweets were collected by me. This is indicated by a steep rise in the curve and is insignificant. The real regions of significance are from hour 7 to 11:30 and hour 19 to 22.

Considering brands, the visualization for **Microsoft vs. Sony** is depicted below. **Sony** is the clear winner here.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*TJxM0RVjYrAm04L8SpUnlA.png)

Considering products, the visualization for **iPhone 6 vs. Galaxy S5** is depicted below. The clear winner here is definitely **iPhone 6**.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*LljvAgD-PZV_F39Ztq42cQ.png)

<br>
### Tweeting Frequency Analysis

In addition to time-based analysis, we can do other types of analysis as well. The most popular analysis in this case would be frequency based analysis of the users authoring the tweets. The following code snippet will compute the Twitter accounts that authored the most tweets and compare it to the total number of unique accounts that appeared for each of our query terms.

```python
# Just to jog your memory, this was already initialized earlier
brands = ['sony', 'microsoft']
products = ['iphone6', 'galaxys5']
 
# For brands
brands_user_coll = {key:df.pop('user').apply(pd.Series) for key, df in [(key, data_frames[key]) for key in brands]}
brands_authors = {key:brands_user_coll[key].screen_name for key in brands_user_coll.keys()}
brands_authors_counter = {key:Counter(brands_authors[key].values) for key in brands_authors.keys()}
 
# Display the results in a neat tabulated form
for brand in brands_authors_counter.keys():
    print "\nMost frequent (top 10) authors of tweets for", brand
    pt = PrettyTable(['Author', 'Tweet Count'])
    [pt.add_row([a, f]) for a, f in brands_authors_counter[brand].most_common(10)]
    print pt
    num_unique_authors = len(set(brands_authors[brand].values))
    print "There are {0} unique authors out of {1} tweets".format(num_unique_authors, len(data_frames[brand]))
    
# For products    
products_user_coll = {key:df.pop('user').apply(pd.Series) for key, df in [(key, data_frames[key]) for key in products]}
products_authors = {key:products_user_coll[key].screen_name for key in products_user_coll.keys()}
products_authors_counter = {key:Counter(products_authors[key].values) for key in products_authors.keys()}
 
# Display the results in a neat tabulated form
for product in products_authors_counter.keys():
    print "\nMost frequent (top 10) authors of tweets for", product
    pt = PrettyTable(['Author', 'Tweet Count'])
    [pt.add_row([a, f]) for a, f in products_authors_counter[product].most_common(10)]
    print pt
    num_unique_authors = len(set(products_authors[product].values))
    print "There are {0} unique authors out of {1} tweets".format(num_unique_authors, len(data_frames[product]))
```

The results which I obtained are depicted below.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*AGqe9gD6Drk2KEFGoXltGQ.png)

What we do notice is that a lot of these tweets are also made by bots, advertisers and SEO technicians. Some examples are `Galaxy_Sleeves` and `iphone6_sleeves` which are obviously selling covers and cases for the devices.

<br>
### Tweeting Frequency Visualizations

After frequency analysis, we can plot these frequency values to get better intuition about the underlying distribution, so let's take a quick look at it using histograms. The following code snippet created these visualizations for both brands and products using subplots.

```python
# Brands Tweets Visualizations
fig, axes = plt.subplots(2, sharex=True)
axes[0].hist(sorted(brands_authors_counter['sony'].values()), bins=20, alpha=0.7, label='Sony', log=True, color='r')
axes[0].set_title('Sony')
axes[1].hist(sorted(brands_authors_counter['microsoft'].values()), bins=20, alpha=0.7, label='Microsoft', log=True, color='b')
axes[1].set_title('Microsoft')
for ax in axes:
    ax.set_xlabel('Number of Tweets')
    ax.set_ylabel('Number of Authors')
 
# Products Tweets Visualizations
fig, axes = plt.subplots(2, sharex=True)
axes[0].hist(sorted(products_authors_counter['iphone6'].values()), bins=20, alpha=0.7, label='iPhone 6', log=True, color='r')
axes[0].set_title('iPhone 6')
axes[1].hist(sorted(products_authors_counter['galaxys5'].values()), bins=20, alpha=0.7, label='Galaxy S5', log=True, color='b')
axes[1].set_title('Galaxy S5')
for ax in axes:
    ax.set_xlabel('Number of Tweets')
    ax.set_ylabel('Number of Authors')
```

The visualizations I obtained are depicted below.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*iWLq1C-xO1SALa2nqAK1Gw.png)

The distributions follow the "Pareto Principle" as expected where we see that a selective number of users make a large number of tweets and the majority of users create a small number of tweets. Besides that, we see that based on the tweet distributions, Sony and iPhone 6 are more trending than their counterparts.

<br>
### Locale Analysis

Another important insight would be to see where your target audience is located and their frequency. The following code snippet achieves the same.

```python
# Top ten locales for Brands
for brand in brands:
    print 'Top 10 locales for', brand
    pt = PrettyTable(['Language', 'Tweets'])
    top_ten = dict(data_frames[brand].lang.value_counts()[:10])
    top_ten = [[key, top_ten[key]] for key in sorted(top_ten.keys())]
    top_ten.sort(key = lambda row: row[1], reverse=True)
    [pt.add_row(row) for row in top_ten]
    print pt
 
# Top ten locales for Products
for product in products:
    print 'Top 10 locales for', product
    pt = PrettyTable(['Language', 'Tweets'])
    top_ten = dict(data_frames[product].lang.value_counts()[:10])
    top_ten = [[key, top_ten[key]] for key in sorted(top_ten.keys())]
    top_ten.sort(key = lambda row: row[1], reverse=True)
    [pt.add_row(row) for row in top_ten]
    print pt
```

The outputs which I obtained are depicted in the following snapshot. Remember that Twitter follows the [ISO 639–1]((http://www.wikiwand.com/en/List_of_ISO_639-1_codes) language code convention.

<center><img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*GtoCl9FBqGFg_lgsQ6WynQ.png"/></center>

The trend we see is that most of the tweets are from English speaking countries as expected. Surprisingly, most of the Tweets regarding iPhone 6 are from Japan!

<br>
### Analysis of Trending Topics

In this section, we will see some of the topics which are associated with the terms we used for querying Twitter. For this, we will be running our analysis on the English language tweets. We will be using the [`nltk`](http://www.nltk.org/) library here to take care of a couple of things like removing stopwords which have little significance. Now I will be doing the analysis here for brands only, but you are most welcome to try it out with products too because, the following code snippet can be used to accomplish both the computations.

```python
from collections import Counter
import nltk
import copy
 
# This is just a rough compilation, it can be done in a better way
ignore_terms = []
ignore_terms.extend(nltk.corpus.stopwords.words('english'))
ignore_terms.extend(['-', '#', '', 'rt']) # ignoring tokens like retweets and symbols
 
# Analysis for brands
print '\n\nAnalysis for Sony'
sony_df = copy.copy(data_frames['sony'])
sony_en_text = sony_df[sony_df['lang'] == 'en'].pop('text')
sony_tokens = []
[sony_tokens.extend([t.lower().strip(":,.") for t in txt.split()]) for txt in sony_en_text.values]  
sony_tokens_counter = Counter(sony_tokens)
[sony_tokens_counter.pop(t, None) for t in ignore_terms]
print '\nMost common terms:'
print sony_tokens_counter.most_common(20)
print '\nMost common phrases:'
nltk.Text([token.encode('utf-8').strip() for token in sony_tokens]).collocations()
 
 
print '\n\nAnalysis for Microsoft'
microsoft_df = copy.copy(data_frames['microsoft'])  
microsoft_en_text = microsoft_df[microsoft_df['lang'] == 'en'].pop('text')
microsoft_tokens = []
[microsoft_tokens.extend([t.lower().strip(":,.") for t in txt.split()]) for txt in microsoft_en_text.values]
microsoft_tokens_counter = Counter(microsoft_tokens)
[microsoft_tokens_counter.pop(t, None) for t in ignore_terms]
print '\nMost common terms:'
print microsoft_tokens_counter.most_common(20)
print '\nMost common phrases:'
nltk.Text([token.encode('utf-8').strip() for token in microsoft_tokens]).collocations()
```

What the above code does is that, it takes each tweet, tokenizes it and then computes a term frequency and outputs the 20 most common terms for each brand. Ofcourse an n-gram analysis can give a deeper insight into trending topics but the same can also be accomplished with ntlk's `collocations` function which takes in the tokens and outputs the context in which they were mentioned. The outputs I obtained are depicted in the snapshot below.

![](https://d262ilb51hltx0.cloudfront.net/max/800/1*VcBmWCh8MzD_bwwT538KDg.png)

Some interesting insights we see from the above outputs are as follows.

 - Sony was hacked recently and it was rumored that North Korea was responsible for that, however they have denied that. We can see that is trending on Twitter in context of Sony. You can read about it [here](http://www.theguardian.com/world/2014/dec/07/north-korea-sony-hack-a-righteous-deed-but-we-didnt-do-it)
 - Recently a group known as LizardSquad claimed that it was responsible for hacking some popular game networks
 - Sony has recently introduced [Project Sony Skylight](http://www.engadget.com/2014/12/06/project-skylight-ps4-beta/) which lets you customize your PS4.
 - There are rumors of Lumia 1030, Microsoft's first flagship phone.
People are also talking a lot about Windows 10, the next OS which is going to be released by Microsoft pretty soon.
 - Interestingly, **ebay price** comes up for both the brands, this might be an indication that eBay is offering discounts for products from both these brands.

To get a detailed view on the tweets matching some of these trending terms, we can use nltk's `concordance` function as follows.

```python
print 'Tweets for Sony talking about hack'
nltk.Text([token.encode('utf-8').strip() for token in sony_tokens]).concordance('hack')
 
print 'Tweets for Microsoft talking about Lumia 1030'
nltk.Text([token.encode('utf-8').strip() for token in microsoft_tokens]).concordance('1030')
```

The outputs I obtained are as follows. We can clearly see the tweets which contain the token we searched for.  In case you are unable to view the text clearly, click on the image to zoom.

<a href="http://i.imgur.com/JWgcC7m.png", target="_blank">![](https://d262ilb51hltx0.cloudfront.net/max/800/1*PRgLav_biOtx4ltaryIFMg.png)</a>

Thus, you can see that the Twitter Streaming API is a really good source to track social reaction to any particular entity whether it is a brand or a product. On top of that, if you are armed with an arsenal of Python's powerful analysis tools and libraries, you can get the best insights from the unending stream of tweets.

That's all for now folks! Before I sign off, I would like to thank **Matthew A. Russell** and his excellent book <a href="http://shop.oreilly.com/product/0636920030195.do", target="_blank">Mining the Social Web</a> once again, without which this post would not have been possible.