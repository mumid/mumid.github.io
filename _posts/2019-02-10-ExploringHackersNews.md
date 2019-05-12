---
title: "Data Science Project: Exploring Hackers News Posts"
Date: 2019-02-10
tags: [Data Science, Data Analysis, Python]
header:
  image: "/images/2019-02-10/office-4.jpg"
excerpt: "Data Science, Data Analysis, Python"
mathjax: "true"
---

In this project, we'll compare two different types of posts from [Hacker News](https://news.ycombinator.com/), a popular site where technology related stories (or 'posts') are voted and commented upon. The two types of posts we'll explore begin with either `Ask HN` or `Show HN`.

Users submit `Ask HN` posts to ask the Hacker News community a specific question, such as "What is the best online course you've ever taken?" Likewise, users submit `Show HN` posts to show the Hacker News community a project, product, or just generally something interesting.

We'll specifically compare these two types of posts to determine the following:

* Do `Ask HN` or `Show HN` receive more comments on average?
* Do posts created at a certain time receive more comments on average?

It should be noted that the data set we're working with was reduced from almost 300,000 rows to approximately 20,000 rows by removing all submissions that did not receive any comments, and then randomly sampling from the remaining submissions.

# Introduction
First, we'll read in the data and remove the headers.


```python
# Read in the data.
import csv

f = open('hacker_news.csv')
hn = list(csv.reader(f))
hn[:5]
```




    [['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at'],
     ['12224879',
      'Interactive Dynamic Video',
      'http://www.interactivedynamicvideo.com/',
      '386',
      '52',
      'ne0phyte',
      '8/4/2016 11:52'],
     ['10975351',
      'How to Use Open Source and Shut the Fuck Up at the Same Time',
      'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/',
      '39',
      '10',
      'josep2',
      '1/26/2016 19:30'],
     ['11964716',
      "Florida DJs May Face Felony for April Fools' Water Joke",
      'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/',
      '2',
      '1',
      'vezycash',
      '6/23/2016 22:20'],
     ['11919867',
      'Technology ventures: From Idea to Enterprise',
      'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429',
      '3',
      '1',
      'hswarna',
      '6/17/2016 0:01']]



# Removing Headers from a List of Lists


```python
# Remove the headers.
headers = hn[0]
hn = hn[1:]
print(headers)
print(hn[:5])
```

    ['id', 'title', 'url', 'num_points', 'num_comments', 'author', 'created_at']
    [['12224879', 'Interactive Dynamic Video', 'http://www.interactivedynamicvideo.com/', '386', '52', 'ne0phyte', '8/4/2016 11:52'], ['10975351', 'How to Use Open Source and Shut the Fuck Up at the Same Time', 'http://hueniverse.com/2016/01/26/how-to-use-open-source-and-shut-the-fuck-up-at-the-same-time/', '39', '10', 'josep2', '1/26/2016 19:30'], ['11964716', "Florida DJs May Face Felony for April Fools' Water Joke", 'http://www.thewire.com/entertainment/2013/04/florida-djs-april-fools-water-joke/63798/', '2', '1', 'vezycash', '6/23/2016 22:20'], ['11919867', 'Technology ventures: From Idea to Enterprise', 'https://www.amazon.com/Technology-Ventures-Enterprise-Thomas-Byers/dp/0073523429', '3', '1', 'hswarna', '6/17/2016 0:01'], ['10301696', 'Note by Note: The Making of Steinway L1037 (2007)', 'http://www.nytimes.com/2007/11/07/movies/07stein.html?_r=0', '8', '2', 'walterbell', '9/30/2015 4:12']]


We can see above that the data set contains the title of the posts, the number of comments for each post, and the date the post was created. Let's start by exploring the number of comments for each type of post.

# Extracting Ask HN and Show HN Posts

First, we'll identify posts that begin with either `Ask HN` or `Show HN` and separate the data for those two types of posts into different lists. Separating the data makes it easier to analyze in the following steps.


```python
# Identify posts that begin with either `Ask HN` or `Show HN` and 
#separate the data into different lists.

ask_posts = []
show_posts =[]
other_posts = []
for post in hn:
    title = post[1]
    if title.lower().startswith("ask hn"):
        ask_posts.append(post)
    elif title.lower().startswith("show hn"):
        show_posts.append(post)
    else:
        other_posts.append(post)
print(len(ask_posts))
print(len(show_posts))
print(len(other_posts))
```

    1744
    1162
    17194


# Calculating the Average Number of Comments for Ask HN and Show HN Posts

Now that we separated ask posts and show posts into different lists, we'll calculate the average number of comments each type of post receives.


```python
#Calculate the average number of comments `Ask HN` posts receive.

total_ask_comments = 0

for post in ask_posts:
    total_ask_comments += int(post[4])
    
avg_ask_comments = total_ask_comments / len(ask_posts)
print(avg_ask_comments)
```

    14.038417431192661



```python
#Calculate the average number of comments `Show HN` posts receive.
total_show_comments = 0

for post in show_posts:
    total_show_comments += int(post[4])
    
avg_show_comments = total_show_comments / len(show_posts)
print(avg_show_comments)
```

    10.31669535283993


On average, ask posts in our sample receive approximately 14 comments, whereas show posts receive approximately 10. Since ask posts are more likely to receive comments, we'll focus our remaining analysis just on these posts.

# Finding the Amount of Ask Posts and Comments by Hour Created
Next, we'll determine if we can maximize the amount of comments an ask post receives by creating it at a certain time. First, we'll find the amount of ask posts created during each hour of day, along with the number of comments those posts received. Then, we'll calculate the average amount of comments ask posts created at each hour of the day receive.


```python
# Calculate the amount of ask posts created during each hour of day and 
#the number of comments received.
import datetime as dt
result_list = []
for post in ask_posts:
    result_list.append([post[6], int(post[4])])

counts_by_hour = {}
comments_by_hour = {}
date_format = "%m/%d/%Y %H:%M"

for each_row in result_list:
    date = each_row[0]
    comment = each_row[1]
    time = dt.datetime.strptime(date, date_format).strftime("%H")
    if time not in counts_by_hour:
        counts_by_hour[time] = 1
        comments_by_hour[time] = comment
    else:
        counts_by_hour[time] += 1
        comments_by_hour[time] += comment
comments_by_hour
```




    {'00': 447,
     '01': 683,
     '02': 1381,
     '03': 421,
     '04': 337,
     '05': 464,
     '06': 397,
     '07': 267,
     '08': 492,
     '09': 251,
     '10': 793,
     '11': 641,
     '12': 687,
     '13': 1253,
     '14': 1416,
     '15': 4477,
     '16': 1814,
     '17': 1146,
     '18': 1439,
     '19': 1188,
     '20': 1722,
     '21': 1745,
     '22': 479,
     '23': 543}



# Calculating the Average Number of Comments for Ask HN Posts by Hour
we'll use the two dictionaries `counts_by_hour` and `comments_by_hour` to calculate the average number of comments for posts created during each hour of the day.


```python
# Calculate the average amount of comments `Ask HN` posts 
# created at each hour of the day receive.
avg_by_hour = []

for hr in comments_by_hour:
    avg_by_hour.append([hr, comments_by_hour[hr] / counts_by_hour[hr]])
avg_by_hour
```




    [['16', 16.796296296296298],
     ['17', 11.46],
     ['14', 13.233644859813085],
     ['08', 10.25],
     ['12', 9.41095890410959],
     ['22', 6.746478873239437],
     ['01', 11.383333333333333],
     ['10', 13.440677966101696],
     ['03', 7.796296296296297],
     ['15', 38.5948275862069],
     ['07', 7.852941176470588],
     ['02', 23.810344827586206],
     ['00', 8.127272727272727],
     ['21', 16.009174311926607],
     ['13', 14.741176470588234],
     ['06', 9.022727272727273],
     ['05', 10.08695652173913],
     ['23', 7.985294117647059],
     ['20', 21.525],
     ['19', 10.8],
     ['11', 11.051724137931034],
     ['18', 13.20183486238532],
     ['04', 7.170212765957447],
     ['09', 5.5777777777777775]]



# Sorting and Printing Values from a List of Lists


```python
swap_avg_by_hr = []
for row in avg_by_hour:
    swap_avg_by_hr.append([row[1], row[0]])

print(swap_avg_by_hr)

sorted_swap = sorted(swap_avg_by_hr, reverse = True)

sorted_swap
```

    [[16.796296296296298, '16'], [11.46, '17'], [13.233644859813085, '14'], [10.25, '08'], [9.41095890410959, '12'], [6.746478873239437, '22'], [11.383333333333333, '01'], [13.440677966101696, '10'], [7.796296296296297, '03'], [38.5948275862069, '15'], [7.852941176470588, '07'], [23.810344827586206, '02'], [8.127272727272727, '00'], [16.009174311926607, '21'], [14.741176470588234, '13'], [9.022727272727273, '06'], [10.08695652173913, '05'], [7.985294117647059, '23'], [21.525, '20'], [10.8, '19'], [11.051724137931034, '11'], [13.20183486238532, '18'], [7.170212765957447, '04'], [5.5777777777777775, '09']]





    [[38.5948275862069, '15'],
     [23.810344827586206, '02'],
     [21.525, '20'],
     [16.796296296296298, '16'],
     [16.009174311926607, '21'],
     [14.741176470588234, '13'],
     [13.440677966101696, '10'],
     [13.233644859813085, '14'],
     [13.20183486238532, '18'],
     [11.46, '17'],
     [11.383333333333333, '01'],
     [11.051724137931034, '11'],
     [10.8, '19'],
     [10.25, '08'],
     [10.08695652173913, '05'],
     [9.41095890410959, '12'],
     [9.022727272727273, '06'],
     [8.127272727272727, '00'],
     [7.985294117647059, '23'],
     [7.852941176470588, '07'],
     [7.796296296296297, '03'],
     [7.170212765957447, '04'],
     [6.746478873239437, '22'],
     [5.5777777777777775, '09']]




```python
# Sort the values and print the the 5 hours with the highest average comments.
print("Top 5 Hours for 'Ask HN' Comments")
for avg, hr in sorted_swap[:5]:
    print(
        "{}:{:.2f} average comments per post".format(dt.datetime.strptime(hr, "%H").strftime("%H:%M"), avg)
    )
    
```

    Top 5 Hours for 'Ask HN' Comments
    15:00:38.59 average comments per post
    02:00:23.81 average comments per post
    20:00:21.52 average comments per post
    16:00:16.80 average comments per post
    21:00:16.01 average comments per post


The hour that receives the most comments per post on average is 15:00, with an average of 38.59 comments per post. There's about a 60% increase in the number of comments between the hours with the highest and second highest average number of comments.

According to the data set [documentation](https://www.kaggle.com/hacker-news/hacker-news-posts/home), the timezone used is Eastern Time in the US. So, we could also write 15:00 as 3:00 pm est.

# Conclusion

In this project, we analyzed ask posts and show posts to determine which type of post and time receive the most comments on average. Based on our analysis, to maximize the amount of comments a post receives, we'd recommend the post be categorized as ask post and created between 15:00 and 16:00 (3:00 pm est - 4:00 pm est).

However, it should be noted that the data set we analyzed excluded posts without any comments. Given that, it's more accurate to say that of the posts that received comments, ask posts received more comments on average and ask posts created between 15:00 and 16:00 (3:00 pm est - 4:00 pm est) received the most comments on average.
