---
published: false
---


This is the second post in our series of blog posts which we shall be presenting regarding social media analysis. We have already talked about <a href="http://blog.priceweave.com/post/103535465394/mining-twitter-in-depth-to-understand-and-analyze">Twitter Mining in depth</a> earlier and also how to <a href="http://blog.dataweave.in/post/102350776388/analyzing-social-trends-data-from-google-trends">analyze social trends in general and gather insights from YouTube</a>. If you are more interested in developing a quick sentiment analysis app, you can check our <a href="http://blog.dataweave.in/post/96618078833/building-a-twitter-sentiment-analysis-app-using-r">short tutorial</a> on that as well.

Today, I will be talking about how we can get data from Twitter in real-time and perform some interesting analytics on top of that to understand social reactions to trending brands and products.
<br><br>
In our last post, we had used Twitter’s <a href="https://dev.twitter.com/rest/public/search">Search API</a> for getting a selective set of tweets and performed some analytics on that. But today, we will be using Twitter’s <a href="https://dev.twitter.com/streaming/overview">Streaming API</a>, to access data feeds in real time. A couple of differences with regards to the two APIs are as follows. The Search API is primarily a REST API which can be used to query for “historical data”. However, the Streaming API gives us access to Twitter’s global stream of tweets data. Moreover, it lets you acquire much larger volumes of data with keyword filters in real-time compared to normal search.

<h3>Installing Dependencies </h3>

I will be using Python for my analysis as usual, so you can install it if you don’t have it already. You can use another language of your choice, but remember to use the relevant libraries of that language. To get started, install the following packages, if you dont have them already. We use <code>simplejson</code> for JSON data processing at DataWeave, but you are most welcome to use the stock <code>json</code> library.

<pre><code>
[root@dip]# pip install twitter           
[root@dip]# pip install simplejson
[root@dip]# pip install prettytable                       
[root@dip]# pip install matplotlib
[root@dip]# pip install nltk
</code></pre>

<h3>Acquiring Data</h3>

We will use the Twitter Streaming API and the equivalent python wrapper to get the required tweets. Since we will be looking to get a large number of tweets in real time, there is the question of where should we store the data and what data model should be used. In general, when building a robust API or application over Twitter data, MongoDB being a schemaless document-oriented database, is a good choice. It also supports expressive queries with indexing, filtering and aggregations. However, since we are going to analyze a relatively small sample of data using <code>pandas</code>, we shall be storing them in flat files.
<br><br>
<i>Note: Should you prefer to sink the data to MongoDB, the mongoexport command line tool can be used to export it to a newline delimited format that is exactly the same as what we will be writing to a file.</i>
<br><br>
The following code snippet shows you how to create a connection to Twitter’s Streaming API and filter for tweets containing a specific keyword. For simplicity, each tweet is saved in a newline delimited file as a JSON document. Since we will be dealing with products and brands, I have queried on two trending products and brands respectively. They are, ‘Sony’ and ‘Microsoft’ with regards to brands and ‘iPhone 6’ and ‘Galaxy S5’ with regards to products. You can write the code snippet as a function for ease of use and call it for specific queries to do a comparative study.

<pre><code>
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
</pre></code>

Let the data stream for a significant period of time so that you can capture a sizeable sample of tweets.

<h3>Analyses and Visualizations</h3> 

Now that you have amassed a collection of tweets from the API in a newline delimited format, let's start with the analyses. One of the easiest ways to load the data into pandas is to build a valid JSON array of the tweets. This can be accomplished using the following code segment.
<br><br>
<pre><code>
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
</code></pre>
<i>Note: With pandas, you will need to have an amount of working memory proportional to the amount of data that you’re analyzing.</i>
<br><br>
Once you run this, you should get a dictionary containing 4 data frames. The output I obtained is shown in the snapshot below.
<br><br>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*LMKVvwtThfLZVvDe_ZSIQQ.png">
<br><br>
<i>Note: Per the Streaming API guidelines, Twitter will only provide up to 1% of the total volume of real time tweets, and anything beyond that is filtered out with each “limit notice”.</i> 
<br><br>
The next snippet shows how to remove the “limit notice” column if you encounter it.
<pre><code>
# Capture the limit notices by indexing into the data frame for non-null field containing "limit"
 
# df is a data frame here
limit_notices = df[pd.notnull(df.limit)]
 
# Remove the limit notice column from the DataFrame entirely
df = df[pd.notnull(df['id'])]
 
print "Number of total tweets that were rate-limited", sum([ln['track'] for ln in limit_notices.limit])
print "Total number of limit notices", len(limit_notices)
</pre></code>

<h5>Time-based Analysis</h5> 
Each tweet we captured had a specific time when it was created. To analyze the time period when we captured these tweets, let’s create a time-based index on the <i>created_at</i> field of each tweet so that we can perform a time-based analysis to see at what times do people post most frequently about our query terms.
<pre><code>
from prettytable import PrettyTable
 
pt = PrettyTable()
pt = PrettyTable(['Brand \ Product', 'First tweet timestamp (UTC)', 'Last tweet timestamp (UTC)'])
 
for key in sorted(data_frames.keys()):
    pt.add_row([key, data_frames[key]['created_at'][0], data_frames[key]['created_at'][-1]])
    
print pt
</code></pre>
<br>
The output I obtained is shown in the snapshot below.
<br>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*2z7CnPjnBn-wjDoxcEJ-NQ.png"/>
<br>
I had started capturing the Twitter stream at around 7 pm on the 6th of December and stopped it at around 11:45 am on the 7th of December. So the results seem consistent based on that. With a time-based index now in place, we can trivially do some useful things like calculate the boundaries, compute histograms and so on. Operations such as grouping by a time unit are also easy to accomplish and seem a logical next step. The following code snippet illustrates how to group by the “hour” of our data frame, which is exposed as a <code>datetime.datetime</code> timestamp since we now have a time-based index in place. We print an hourly distribution of tweets also just to see which brand/product was most talked about on Twitter during that time period.
<br>
<pre><code>
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
</code></pre>
<br>
The outputs I obtained are depicted in the snapshot below.
<br><br>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*rpeisIN_BoF7N-b0t1NimQ.png"/>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*nDCnIRq6g3K9T3TYe9ZG3A.png"/>
<br><br>
The “Hour” field here follows a 24 hour format. What is interesting here is that, people have been talking more about <b>Sony</b> than <b>Microsoft</b> in <b>Brands</b>. In <b>Products</b>, <b>iPhone 6</b> seems to be trending more than <b>Samsung’s Galaxy S5</b>. Also the trend shows some interesting insights that people tend to talk more on Twitter in the morning and late evenings.
<br>
<h5>Time-based Visualizations</h5> 
It could be helpful to further subdivide the time ranges into smaller intervals so as to increase the resolution of the extremes. Therefore, let’s group into a custom interval by dividing the hour into 15-minute segments. The code is pretty much the same as before except that you call a custom function to perform the grouping. This time, we will be visualizing the distributions using <code>matplotlib</code>.
<br>
<pre><code>
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
</code></pre>
<br>
The two visualizations are depicted below. Ofcourse don’t forget to ignore the section of the plots from after 11:30 am to around 7 pm because during this time no tweets were collected by me. This is indicated by a steep rise in the curve and is insignificant. The real regions of significance are from hour 7 to 11:30 and hour 19 to 22.
<br><br>
Considering brands, the visualization for Microsoft vs. Sony is depicted below. Sony is the clear winner here.
<br>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*TJxM0RVjYrAm04L8SpUnlA.png"/>
<br>
Considering products, the visualization for iPhone 6 vs. Galaxy S5 is depicted below. The clear winner here is definitely iPhone 6.
<br>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*LljvAgD-PZV_F39Ztq42cQ.png"/>

<h5>Tweeting Frequency Analysis</h5>
In addition to time-based analysis, we can do other types of analysis as well. The most popular analysis in this case would be frequency based analysis of the users authoring the tweets. The following code snippet will compute the Twitter accounts that authored the most tweets and compare it to the total number of unique accounts that appeared for each of our query terms.
<br>
<pre><code>
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
</code></pre>
<br>
The results which I obtained are depicted below.
<br>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*AGqe9gD6Drk2KEFGoXltGQ.png"/>
<br>
What we do notice is that a lot of these tweets are also made by bots, advertisers and SEO technicians. Some examples are Galaxy_Sleeves and iphone6_sleeves which are obviously selling covers and cases for the devices.
<br>
<h5>Tweeting Frequency Visualizations</h5>
After frequency analysis, we can plot these frequency values to get better intuition about the underlying distribution, so let’s take a quick look at it using histograms. The following code snippet created these visualizations for both brands and products using subplots.
<br>
<pre><code>
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
</code></pre>
<br>
The visualizations I obtained are depicted below.
<br>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*iWLq1C-xO1SALa2nqAK1Gw.png"/>
<br>
The distributions follow the “Pareto Principle” as expected where we see that a selective number of users make a large number of tweets and the majority of users create a small number of tweets. Besides that, we see that based on the tweet distributions, Sony and iPhone 6 are more trending than their counterparts.
<br>
<h5>Locale Analysis</h5>
Another important insight would be to see where your target audience is located and their frequency. The following code snippet achieves the same.
<br>
<pre><code>
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
</code></pre>
<br>
The outputs which I obtained are depicted in the following snapshot. Remember that Twitter follows the <a href="http://www.wikiwand.com/en/List_of_ISO_639-1_codes">ISO 639–1</a> language code convention.
<br>
<center><img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*GtoCl9FBqGFg_lgsQ6WynQ.png"/></center>
<br>
The trend we see is that most of the tweets are from English speaking countries as expected. Surprisingly, most of the Tweets regarding iPhone 6 are from Japan!
<br>
<h5>Analysis of Trending Topics</h5> 
In this section, we will see some of the topics which are associated with the terms we used for querying Twitter. For this, we will be running our analysis on the English language tweets. We will be using the <a href="http://www.nltk.org/"><code>nltk</code></a> library here to take care of a couple of things like removing stopwords which have little significance. Now I will be doing the analysis here for brands only, but you are most welcome to try it out with products too because, the following code snippet can be used to accomplish both the computations.
<br>
<pre><code>
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
</code></pre>
<br>
What the above code does is that, it takes each tweet, tokenizes it and then computes a term frequency and outputs the 20 most common terms for each brand. Ofcourse an n-gram analysis can give a deeper insight into trending topics but the same can also be accomplished with ntlk’s collocations function which takes in the tokens and outputs the context in which they were mentioned. The outputs I obtained are depicted in the snapshot below.
<br>
<img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*VcBmWCh8MzD_bwwT538KDg.png"/>
<br>
Some interesting insights we see from the above outputs are as follows.
<ul>
    <li>Sony was hacked recently and it was rumored that North Korea was responsible for that, however they have denied that. We can see that is trending on Twitter in context of Sony. You can read about it <a href="http://www.theguardian.com/world/2014/dec/07/north-korea-sony-hack-a-righteous-deed-but-we-didnt-do-it">here</a>.</li>
    <li>Sony has recently introduced <a href="http://www.engadget.com/2014/12/06/project-skylight-ps4-beta/">Project Sony Skylight</a> which lets you customize your PS4.</li>
    <li> There are rumors of Lumia 1030, Microsoft’s first flagship phone.
People are also talking a lot about Windows 10, the next OS which is going to be released by Microsoft pretty soon.</li>
    <li>Interestingly, “ebay price” comes up for both the brands, this might be an indication that eBay is offering discounts for products from both these brands.</li>
</ul>

To get a detailed view on the tweets matching some of these trending terms, we can use nltk’s concordance function as follows.
<br>
<pre><code>
print 'Tweets for Sony talking about hack'
nltk.Text([token.encode('utf-8').strip() for token in sony_tokens]).concordance('hack')
 
print 'Tweets for Microsoft talking about Lumia 1030'
nltk.Text([token.encode('utf-8').strip() for token in microsoft_tokens]).concordance('1030')
</code></pre>
<br>
The outputs I obtained are as follows. We can clearly see the tweets which contain the token we searched for.  In case you are unable to view the text clearly, click on the image to zoom.
<br>
<a href="http://i.imgur.com/JWgcC7m.png", target="_blank"><img src="https://d262ilb51hltx0.cloudfront.net/max/800/1*PRgLav_biOtx4ltaryIFMg.png"/></a>
<br>
Thus, you can see that the Twitter Streaming API is a really good source to track social reaction to any particular entity whether it is a brand or a product. On top of that, if you are armed with an arsenal of Python’s powerful analysis tools and libraries, you can get the best insights from the unending stream of tweets.
<br><br>
That’s all for now folks! Before I sign off, I would like to thank <b>Matthew A. Russell</b> and his excellent book <a href="http://shop.oreilly.com/product/0636920030195.do">Mining the Social Web</a> once again, without which this post would not have been possible..
