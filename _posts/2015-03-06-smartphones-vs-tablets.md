---
layout: post
title: "Smartphones vs Tablets: Does size matter?"
published: true
---


We have seen a steady increase in the number of smartphones and tablets since the last five years. Looking at the number of smartphones, tablets and now wearables ( smart watches and fitbits ) that are being launched in the mobiles market, we can truly call this **'The Mobile Age'**.

A particularly interesting problem in this domain is, detecting whether a particular product is a mobile phone (smartphone) or a tablet based on just the product name or brief description. If it is mentioned explicitly somewhere in the product information or metadata obtained from any e-commerce website, we have our solution right in front of us. Unfortunately, with the data obtained from the Web, it is very difficult to track this information unless we aggregate data from multiple websites to get a lot of metadata pertaining to the device.

To address the above problem, I decided to take two approaches.

 - Try to extract this information from the product metadata
 - Try to get a list of smartphones and tablets from well known sites and then build a classifier on top of that data

Here we will talk mainly about the second approach since it is more challenging and engaging than the former. To start with, I needed some data specific to phone models, brands, sizes, dimensions, resolutions and everything else related to the device specifications. Full credits for this data goes to [GSMArena](http://www.gsmarena.com/) since they own the entire database for important information pertaining to devices. I devised a basic crawler using **Python** and crawled, extracted and aggregated this information and stored it as a JSON dump. Each device is represented as a JSON document like the sample shown below.

```json
{
  "Body": {
    "Dimensions": "200 x 114 x 8.7 mm",
    "Weight": "290 g (Wi-Fi), 299 g (LTE)"
  },
  "Sound": {
    "3.5mm jack ": "Yes",
    "Alert types": "N/A",
    "Loudspeaker ": "Yes, with stereo speakers"
  },
  "Tests": {
    "Audio quality": "Noise -92.2dB / Crosstalk -92.3dB"
  },
  "Features": {
    "Java": "No",
    "OS": "Android OS, v4.3 (Jelly Bean), upgradable to v4.4.2 (KitKat)",
    "Chipset": "Qualcomm Snapdragon S4Pro",
    "Colors": "Black",
    "Radio": "No",
    "GPU": "Adreno 320",
    "Messaging": "Email, Push Email, IM, RSS",
    "Sensors": "Accelerometer, gyro, proximity, compass",
    "Browser": "HTML5",
    "Features_extra detail": "- Wireless charging- Google Wallet- SNS integration- MP4/H.264 player- MP3/WAV/eAAC+/WMA player- Organizer- Image/video editor- Document viewer- Google Search, Maps, Gmail,YouTube, Calendar, Google Talk, Picasa- Voice memo- Predictive text input (Swype)",
    "CPU": "Quad-core 1.5 GHz Krait",
    "GPS": "Yes, with A-GPS support"
  },
  "title": "Google Nexus 7 (2013)",
  "brand": "Asus",
  "General": {
    "Status": "Available. Released 2013, July",
    "2G Network": "GSM 850 / 900 / 1800 / 1900 - all versions",
    "3G Network": "HSDPA 850 / 900 / 1700 / 1900 / 2100 ",
    "4G Network": "LTE 800 / 850 / 1700 / 1800 / 1900 / 2100 / 2600 ",
    "Announced": "2013, July",
    "General_extra detail": "LTE 700 / 750 / 850 / 1700 / 1800 / 1900 / 2100",
    "SIM": "Micro-SIM"
  },
  "Battery": {
    "Talk time": "Up to 9 h (multimedia)",
    "Battery_extra detail": "Non-removable Li-Ion 3950 mAh battery"
  },
  "Camera": {
    "Video": "Yes, 1080p@30fps",
    "Primary": "5 MP, 2592 x 1944 pixels, autofocus",
    "Features": "Geo-tagging, touch focus, face detection",
    "Secondary": "Yes, 1.2 MP"
  },
  "Memory": {
    "Internal": "16/32 GB, 2 GB RAM",
    "Card slot": "No"
  },
  "Data": {
    "GPRS": "Yes",
    "NFC": "Yes",
    "USB": "Yes, microUSB (SlimPort) v2.0",
    "Bluetooth": "Yes, v4.0 with A2DP, LE",
    "EDGE": "Yes",
    "WLAN": "Wi-Fi 802.11 a/b/g/n, dual-band",
    "Speed": "HSPA+, LTE"
  },
  "Display": {
    "Multitouch": "Yes, up to 10 fingers",
    "Protection": "Corning Gorilla Glass",
    "Type": "LED-backlit IPS LCD capacitive touchscreen, 16M colors",
    "Size": "1200 x 1920 pixels, 7.0 inches (~323 ppi pixel density)"
  }
}
```

From the above document, it is clear that there are a lot of attributes that can be assigned to a mobile device. However, we would not need all of them for building our simple algorithm for labeling smartphones and tablets. I had decided to use some key numeric variables like the device screen size and dimensions for separating out smartphones and tablets. 

Finally, the list of attributes that I decided to use were,

 - Screen Resolution
 - Device dimensions
 - Screen size

I wrote some regular expressions for extracting out the features related to the device screen size and resolution. Getting the resolution was easy, which was achieved with the following Python code snippet. There were a couple of `NA` values but I didn't go out of my way to get the data by searching on the web because resolution varies a lot and is not a key attribute for determining if a device is a phone or a tablet.

```python
size_str = repr(doc["Display"]["Size"])
resolution_pattern = re.compile(r'(?:\S+\s)x\s(?:\S+\s)\s?pixels')
if resolution_pattern.findall(size_str):
    resolution = ''.join([token.replace("'","") 
                for token in resolution_pattern.findall(size_str)[0].split()[0:3]])
else:
    resolution = 'NA'
```

But the real problems started when I wrote regular expressions for extracting the screen size. I started off with analyzing the dataset and it seemed that screen size was mentioned in `inches` so I wrote the following regular expression for getting screen size.

```python
size_str = repr(doc["Display"]["Size"])
screen_size_pattern = re.compile(r'(?:\S+\s)\s?inches')
if screen_size_pattern.findall(size_str):
    screen_size = screen_size_pattern.findall(size_str)[0].split()[0]
else:
    screen_size = 'NA'
```

However, I noticed that I was getting a lot of `NA` values for many devices. On looking up the same devices online, I noticed there were three distinct patterns with regards to screen size. They are,

 - Screen size in `inches`
 - Screen size in `lines`
 - Screen size in `chars` or `characters`

Now, some of you might be wondering what on earth do `lines` and `chars` mean and how do they measure screen size. On digging it up, I found that basically both of them mean the same thing but in different formats. If we have `n lines` as the screen size, it means, the screen can display at most `n` lines of text at any instance of time. Likewise, if we have `n x m chars` as the screen size, it means the device can display `n` lines of text at any instance of time with each line having a maximum of `m` characters. The picture below will make things more clear. It represents a screen of 4 lines or 4 x 20 chars.

![](http://i.imgur.com/dGYZ6VO.jpg)

Thus, the earlier logic for extracting screen size had to be modified and we used the following code snippet. We had to take care of multiple cases in our regexes, because the data did not have a consistent format.

```python
size_str = repr(doc["Display"]["Size"])
screen_size_pattern = re.compile(r'(?:\S+\s)\s?inc[h|hes]')
if screen_size_pattern.findall(size_str):
    screen_size = screen_size_pattern.findall(size_str)[0]
                 .replace("'","").split()[0]+' inches'
else:
    screen_size_pattern = re.compile(r'(?:\S+\s)\s?lines')
    if screen_size_pattern.findall(size_str):
        screen_size = screen_size_pattern.findall(size_str)[0]
                     .replace("'","").split()[0]+' lines'
    else:
        screen_size_pattern = re.compile(r'(?:\S+\s)x\s(?:\S+\s)\s?char[s|acters]')
        if screen_size_pattern.findall(size_str):
            screen_size = screen_size_pattern.findall(size_str)[0]
                         .replace("'","").split()[0]+' lines'
        else:
            screen_size = 'NA'
```

Next, I extracted the 'dimensions' attribute from the dataset and performed some transformations on it to get the total volume of the phone. It was achieved using the following code snippet.

```python
dimensions = doc['Body']['Dimensions']
dimensions = re.sub (r'[^\s*\w*.-]', '', dimensions.split ('(') [0].split (',') [0].split ('mm') [0]).strip ('-').strip ('x')
if not dimensions:
    dimensions = 'NA'
    total_area = 'NA'
else:
    if 'cc' in dimensions:
        total_area = dimensions.split ('cc') [0]
    else:
        total_area = reduce (operator.mul, [float (float (elem.split ('-') [0])/10)   
                            for elem in dimensions.split ('x')], 1)
        total_area = round(float(total_area),3)
```

I used [PrettyTable](https://pypi.python.org/pypi/PrettyTable/) to output the results in a clear and concise format as shown below.

```text
+----------------+-------+-----------+----------+--------------------+----------+
|   Model Name   | Brand |Screen Size|Resolution|     Dimensions     |Total Area|
+----------------+-------+-----------+----------+--------------------+----------+
|   Liquid E3    |  Acer | 4.7 inches| 720x1280 |    136 x 68 x 9    |  83.232  |
|   Liquid Z4    |  Acer | 4.0 inches| 480x800  |   124 x 64 x 9.7   |  76.979  |
| Iconia B1-721  |  Acer | 7.0 inches| 600x1024 | 199 x 122.3 x 11.4 |  277.45  |
| Iconia B1-720  |  Acer | 7.0 inches| 600x1024 |198.1 x 121.9 x 10.2| 246.314  |
| Iconia A1-830  |  Acer | 7.9 inches| 768x1024 | 203 x 138.4 x 8.2  | 230.381  |
|   Liquid Z5    |  Acer | 5.0 inches| 480x854  | 145.5 x 73.5 x 8.8 |  94.109  |
|   Liquid S2    |  Acer | 6.0 inches|1080x1920 |    166 x 86 x 9    | 128.484  |
|      ...       |  ...  |     ...   |    ...   |         ...        |    ...   |
|      ...       |  ...  |     ...   |    ...   |         ...        |    ...   |
|Galaxy Note 8.0 |Samsung| 8.0 inches| 800x1280 | 210.8 x 135.9 x 8  | 229.182  |
|  Rex 90 S5292  |Samsung| 3.5 inches| 320x480  | 113 x 61.9 x 11.9  |  83.237  |
|      ...       |  ...  |     ...   |    ...   |         ...        |    ...   |
|      ...       |  ...  |     ...   |    ...   |         ...        |    ...   |
|      F101      |  ZTE  | 2.0 inches| 176x220  |  105 x 46 x 12.6   |  60.858  |
|      F100      |  ZTE  | 2.0 inches| 176x220  |  105 x 46 x 12.6   |  60.858  |
|Coral200 Sollar |  ZTE  | 1.5 inches| 128x128  | 106 x 45.6 x 18.1  |  87.488  |
+----------------+-----+-------------+----------+--------------------+----------+
```

Next, we stored the above data in a csv file and used [Pandas](http://pandas.pydata.org/), [Matplotlib](http://matplotlib.org/), [Seaborn](http://stanford.edu/~mwaskom/software/seaborn/tutorial/plotting_distributions.html) and [IPython](http://ipython.org/) to do some quick exploratory data analysis and visualizations.

The following depicts the top ten brands with the most number of mobile devices as per the dataset.

![](http://i.imgur.com/0uVVKoN.png)

Then, I looked at the device area frequency for each brand using boxplots as depicted below. Based on the plot, it is quite evident that almost all the plots are right skewed, with a majority of the distribution of device dimensions (total area) falling in the range [0,150]. There are some notable exceptions like **'Apple'** where the skew is considerably less than the general trend. On slicing the data for the brand **'Apple'**, we noticed that this was because devices from **'Apple'** have an almost equal distribution based on the number of smartphones and tablets, leading to the distribution being almost normal.

![](http://i.imgur.com/jiT8gw6.png)

Based on similar experiments, I noticed that tablets had larger dimensions as compared to mobile phones, and screen sizes followed that same trend. I made some quick plots with respect to the device areas as shown below.

![](http://i.imgur.com/eDt2CXo.png)

On taking a closer look at the above plots we can see that, the second plot shows the distribution of device areas in a kernel density plot. This distribution resembles a sharp peaked Gaussian distribution but with a right skew or to be more accurate, a logistic distribution function. The histogram plot depicts the same, except here we see the frequency of devices vs the device areas. Looking at it closely, I came to the decision that, the bell shaped curve had the maximum number of devices and those must be all the smartphones, while the long thin tail on the right side must indicate tablets. So I set a cutoff of 160 cubic centimeters for distinguishing between phones and tablets. 

I also decided to calculate the correlation between `Total Area` and `Screen Size` because as one might guess, devices with larger area have large screen sizes. So we transformed the screen sizes from textual to numeric format based on some processing, and calculated the correlation between them which came to be around **0.73** or **73%**

We did get a high correlation between Screen Size and Device Area. However, I still wanted to investigate why we didn't get a score close to **90%**. On doing some data digging, I noticed an interesting pattern.

![](http://i.imgur.com/SCFCl7y.png)

After looking at the above results, what came to my mind immediately was: why do phones with such small screen sizes have such big dimensions? I soon realized that these devices were either old cordless phones or smartphones with a physical keypad!

![](http://i.imgur.com/1F7iCFi.gif)

Thus, I used screen sizes in conjunction with dimensions for labeling the devices.After this, I decided to use the following logic for labeling smartphones and tablets.

```python
device_class = None
if total_area >= 160.0:
    device_class = 'Tablet'
elif total_area &lt; 160.0:
    device_class = 'Phone'
if 'lines' in screen_size:
    device_class = 'Phone'
elif 'inches' in screen_size:
    if float(screen_size.split()[0]) &lt; 6.0:
        device_class = 'Phone'
```

After carrying out extensive analytics of this data related to mobile devices, I was able to label handheld devices correctly, just like I desired!

```text
+---------------+-------+-------------+------------+------------+--------------+
|   Model Name  | Brand | Screen Size | Resolution | Total Area | Device Class |
+---------------+-------+-------------+------------+------------+--------------+
|   Liquid E3   |  Acer |  4.7 inches |  720x1280  |   83.232   |    Phone     |
|   Liquid Z4   |  Acer |  4.0 inches |  480x800   |   76.979   |    Phone     |
| Iconia B1-721 |  Acer |  7.0 inches |  600x1024  |   277.45   |    Tablet    |
| Iconia B1-720 |  Acer |  7.0 inches |  600x1024  |  246.314   |    Tablet    |
| Iconia A1-830 |  Acer |  7.9 inches |  768x1024  |  230.381   |    Tablet    |
|   Liquid Z5   |  Acer |  5.0 inches |  480x854   |   94.109   |    Phone     |
|   Liquid S2   |  Acer |  6.0 inches | 1080x1920  |  128.484   |    Phone     |
|   Liquid Z3   |  Acer |  3.5 inches |  320x480   |   68.016   |    Phone     |
|   Liquid S1   |  Acer |  5.7 inches |  720x1280  |  129.878   |    Phone     |
| Iconia Tab A3 |  Acer | 10.1 inches |  800x1280  |   464.1    |    Tablet    |
|      ...      |  ...  |     ...     |     ...    |    ...     |     ...      |
|      ...      |  ...  |     ...     |     ...    |    ...     |     ...      |
+---------------+-------+-------------+------------+------------+--------------+
```