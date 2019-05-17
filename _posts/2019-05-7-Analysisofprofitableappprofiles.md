---
title: "Analysis of Profitable App profile: App Store and Google Play Market"
Date: 2019-05-07
tags: [Data Science Projects with Python]
header:
  image: "/images/2019-05-07/mobileapps.png"
excerpt: "Data Science, Data Analysis, Python"
mathjax: "true"
---


The project scope is aimed to identify mobile app profiles that are profitable for the App Store and Google Play markets. We're working as a team of data analysts for a reknowned company that builds Android and iOS mobile apps, and our job is to enable our developer team to make data-driven decisions with respect to the kind of apps they build.

At our company, we only build apps that are free to download and install, and our main source of revenue consists of in-app ads. This means that our revenue for any given app is mostly influenced by the number of users that use our app. Our goal for this project is to analyze data to help our developers understand what kinds of apps are likely to attract more users.

As of September 2018, there were approximately 2 million iOS apps available on the App Store, and 2.1 million Android apps on Google Play.

Collecting data for over four million apps requires a significant amount of time and money, so we'll try to analyze a sample of the data instead. To avoid spending resources on collecting new data ourselves, we should first try to see whether we can find any relevant existing data at no cost. Luckily, these are two data sets that seem suitable for our goals:

* [A data set](https://www.kaggle.com/lava18/google-play-store-apps/home) containing data about approximately ten thousand Android apps from Google Play — the data was collected in August 2018.
* [A data set](https://www.kaggle.com/ramamet4/app-store-apple-data-set-10k-apps/home) containing data about approximately seven thousand iOS apps from the App Store — the data was collected in July 2017.

Let's start by opening the two data sets and then continue with exploring the data.


```python
from csv import reader

### The Google Play data set ###
opened_file = open('googleplaystore.csv', encoding='utf8')
read_file = reader(opened_file)
android = list(read_file)
android_header = android[0]
android = android[1:]

### The App Store data set ###
opened_file = open('AppleStore.csv', encoding='utf8')
read_file = reader(opened_file)
ios = list(read_file)
ios_header = ios[0]
ios = ios[1:] 
```

To make it easier to explore the two data sets, we'll first write a function named `explore_data()` that we can use repeatedly to explore rows in a more readable way. We'll also add an option for our function to show the number of rows and columns for any data set.


```python
def explore_data(dataset, start, end, rows_and_column = False):
    dataset_slice = dataset[start:end]
    for row in dataset_slice:
        print(row)
        print('\n') # adds a new empty line between rows.
    if rows_and_column:
        print('number of rows:', len(dataset))
        print('number of columns:', len(dataset[0]))
print(android_header)
print('\n')
explore_data(android, 0, 3, True) 
```

    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    
    
    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['Coloring book moana', 'ART_AND_DESIGN', '3.9', '967', '14M', '500,000+', 'Free', '0', 'Everyone', 'Art & Design;Pretend Play', 'January 15, 2018', '2.0.0', '4.0.3 and up']
    
    
    ['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']
    
    
    number of rows: 10841
    number of columns: 13


We see that the Google Play data set has 10841 apps and 13 columns. At a quick glance, the columns that might be useful for the purpose of our analysis are '`App`', '`Category`', '`Reviews`', '`Installs`', '`Type`', '`Price`' and '`Genres`'.

Now let's take a look at the App Store data set.


```python
print(ios_header)
print('\n')
explore_data(ios, 0, 3, True) 
```

    ['id', 'track_name', 'size_bytes', 'currency', 'price', 'rating_count_tot', 'rating_count_ver', 'user_rating', 'user_rating_ver', 'ver', 'cont_rating', 'prime_genre', 'sup_devices.num', 'ipadSc_urls.num', 'lang.num', 'vpp_lic']
    
    
    ['281656475', 'PAC-MAN Premium', '100788224', 'USD', '3.99', '21292', '26', '4', '4.5', '6.3.5', '4+', 'Games', '38', '5', '10', '1']
    
    
    ['281796108', 'Evernote - stay organized', '158578688', 'USD', '0', '161065', '26', '4', '3.5', '8.2.2', '4+', 'Productivity', '37', '5', '23', '1']
    
    
    ['281940292', 'WeatherBug - Local Weather, Radar, Maps, Alerts', '100524032', 'USD', '0', '188583', '2822', '3.5', '4.5', '5.0.0', '4+', 'Weather', '37', '5', '3', '1']
    
    
    number of rows: 7197
    number of columns: 16


We have 7197 ios apps and 16 columns in this data set. The columns that seems interesting are: '`track_name`', '`currency`', '`price`', '`rating_count_tot`', '`rating_count_ver`', and '`prime_genre`'. Not all column names are self-explanatory in this case, but details about each column can be found in the data set [documentation](https://www.kaggle.com/ramamet4/app-store-apple-data-set-10k-apps/home). 

# Deleting Wrong Data
The Google Play data set has a dedicated [discussion section](https://www.kaggle.com/lava18/google-play-store-apps/discussion), and we can see that [one of the discussions](https://www.kaggle.com/lava18/google-play-store-apps/discussion/66015) outlines an error for row 10472. Let's print this row and compare it against the header and another row that is correct.


```python
print(android[10472]) # Uncorrect row
print('\n')
print(android_header) # header
print('\n')
print(android[0]) # corrct row
```

    ['Life Made WI-Fi Touchscreen Photo Frame', '1.9', '19', '3.0M', '1,000+', 'Free', '0', 'Everyone', '', 'February 11, 2018', '1.0.19', '4.0 and up']
    
    
    ['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']
    
    
    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']


The row 10472 corresponds to the app *Life Made WI-Fi Touchscreen Photo Frame*, and we can see that the rating is 19. This is clearly off because the maximum rating for a Google Play app is 5. As a consequence, we'll delete this row.


```python
print(len(android))
del(android[10472]) # Don't run this more than once
print(len(android))
```

    10841
    10840


# Removing Duplicate Entries
## Part one
If we explore the Google Play data set long enough, we'll find that some apps have more than one entry. For instance, the application Instagram has four entries:


```python
for app in android:
    name = app[0]
    if name == 'Instagram':
        print(app)    
```

    ['Instagram', 'SOCIAL', '4.5', '66577313', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']
    ['Instagram', 'SOCIAL', '4.5', '66577446', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']
    ['Instagram', 'SOCIAL', '4.5', '66577313', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']
    ['Instagram', 'SOCIAL', '4.5', '66509917', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']


In total, there are 1,181 cases where an app occurs more than once:


```python
duplicate_apps = []
unique_apps = []

for app in android:
    name = app[0]
    if name in unique_apps:
        duplicate_apps.append(name)
    else:
        unique_apps.append(name)
print('Number of Duplicate Apps:', len(duplicate_apps))
print('\n')
print('Examples of Duplicate Apps:', duplicate_apps[:15])
```

    Number of Duplicate Apps: 1181
    
    
    Examples of Duplicate Apps: ['Quick PDF Scanner + OCR FREE', 'Box', 'Google My Business', 'ZOOM Cloud Meetings', 'join.me - Simple Meetings', 'Box', 'Zenefits', 'Google Ads', 'Google My Business', 'Slack', 'FreshBooks Classic', 'Insightly CRM', 'QuickBooks Accounting: Invoicing & Expenses', 'HipChat - Chat Built for Teams', 'Xero Accounting Software']


We don't want to count certain apps more than once when we analyze data, so we need to remove the duplicate entries and keep only one entry per app. One thing we could do is remove the duplicate rows randomly, but we could probably find a better way.

If you examine the rows we printed two cells above for the Instagram app, the main difference happens on the fourth position of each row, which corresponds to the number of reviews. The different numbers show that the data was collected at different times. We can use this to build a criterion for keeping rows. We won't remove rows randomly, but rather we'll keep the rows that have the highest number of reviews because the higher the number of reviews, the more reliable the ratings.

To do that, we will:
* Create a dictionary where each key is a unique app name, and the value is the highest number of reviews of that app
* Use the dictionary to create a new data set, which will have only one entry per app (and we only select the apps with the highest number of reviews)

## Part Two
Let's start building the dictionary. 


```python
reviews_max = {}

for app in android:
    name = app[0]
    n_reviews = float(app[3])
    
    if name in reviews_max and reviews_max[name] < n_reviews:
        reviews_max[name] = n_reviews
    
    elif name not in reviews_max:
        reviews_max[name] = n_reviews
```

In a previous code cell, we found that there are 1,181 cases where an app occurs more than once, so the length of our dictionary (of unique apps) should be equal to the difference between the length of our data set and 1,181.


```python
print('Expected length: ', len(android) - 1181)
print('Actual length: ', len(reviews_max))
```

    Expected length:  9659
    Actual length:  9659



Now, let's use the `reviews_max` dictionary to remove the duplicates. For the duplicate cases, we'll only keep the entries with the highest number of reviews. In the code cell below:
* We start by initializing two empty lists, `android_clean` and `already_added`.
* We loop through the `android` data set, and for every iteration:
    * We isolate the name of the app and the number of reviews.
    * We add the current row `(app)` to the `android_clean` list, and the app name (name) to the `already_added` list if:
        * The number of reviews of the current app matches the number of reviews of that app as described in the `reviews_max` dictionary; and
        * The name of the app is not already in the `already_added` list. We need to add this supplementary condition to account for those cases where the highest number of reviews of a duplicate app is the same for more than one entry (for example, the Box app has three entries, and the number of reviews is the same). If we just check for `reviews_max[name] == n_reviews`, we'll still end up with duplicate entries for some apps.


```python
android_clean = []
already_added = []

for app in android:
    name = app[0]
    n_reviews = float(app[3])
    
    if (reviews_max[name] == n_reviews) and (name not in already_added):
        android_clean.append(app)
        already_added.append(name) # make sure this is inside the if block
```


Now let's quickly explore the new data set, and confirm that the number of rows is 9,659.


```python
explore_data(android_clean, 0, 3, True)
```

    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']
    
    
    ['Sketch - Draw & Paint', 'ART_AND_DESIGN', '4.5', '215644', '25M', '50,000,000+', 'Free', '0', 'Teen', 'Art & Design', 'June 8, 2018', 'Varies with device', '4.2 and up']
    
    
    number of rows: 9659
    number of columns: 13


We have 9659 rows, just as expected.

# Removing Non-English Apps
## Part One
If you explore the data sets enough, you'll notice the names of some of the apps suggest they are not directed toward an English-speaking audience. Below, we see a couple of examples from both data sets:


```python
print(ios[813][1])
print(ios[6731][1])

print(android_clean[4412][0])
print(android_clean[7940][0])
```

    AliExpress Shopping App
    Idle Armies
    中国語 AQリスニング
    لعبة تقدر تربح DZ


We're not keeping these kind of apps, so we'll remove them. One way to go about this is to remove each app whose name contains a symbol that is not commonly used in English text — English text usually includes letters from the English alphabet, numbers composed of digits from 0 to 9, punctuation marks (., !, ?, ;, etc.), and other symbols (+, *, /, etc.).

All these characters that are specific to English texts are encoded using the ASCII standard. Each ASCII character has a corresponding number between 0 and 127 associated with it, and we can take advantage of that to build a function that checks an app name and tells us whether it contains non-ASCII characters.

We built this function below, and we use the built-in `ord()` function to find out the corresponding encoding number of each character.


```python
def is_english(string):
    
    for character in string:
        if ord(character) > 127:
            return False
    
    return True

print(is_english('Instagram'))
print(is_english('爱奇艺PPS -《欢乐颂2》电视剧热播'))
```

    True
    False


The function seems to work fine, but some English app names use emojis or other symbols (™, — (em dash), – (en dash), etc.) that fall outside of the ASCII range. Because of this, we'll remove useful apps if we use the function in its current form.


```python
print(is_english('Docs To Go™ Free Office Suite'))
print(is_english('Instachat 😜'))

print(ord('™'))
print(ord('😜'))
```

    False
    False
    8482
    128540


## Part Two
To minimize the impact of data loss, we'll only remove an app if its name has more than three non-ASCII characters:


```python
def is_english(string):
    non_ascii = 0
    
    for character in string:
        if ord(character) > 127:
            non_ascii += 1
            
        if non_ascii > 3:
            return False
        else:
            return True
print(is_english('Docs To Go™ Free Office Suite'))
print(is_english('Instachat 😜'))                
```

    True
    True


The function is still not perfect, and very few non-English apps might get past our filter, but this seems good enough at this point in our analysis — we shouldn't spend too much time on optimization at this point.

Below, we use the `is_english()` function to filter out the non-English apps for both data sets:


```python
android_english = []
ios_english = []

for app in android_clean:
    name = app[0]
    if is_english(name):
        android_english.append(app)
        
for app in ios:
    name = app[1]
    if is_english(name):
        ios_english.append(app)
        
explore_data(android_english, 0, 3, True)
print('\n')
explore_data(ios_english, 0, 3, True)
```

    ['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']
    
    
    ['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']
    
    
    ['Sketch - Draw & Paint', 'ART_AND_DESIGN', '4.5', '215644', '25M', '50,000,000+', 'Free', '0', 'Teen', 'Art & Design', 'June 8, 2018', 'Varies with device', '4.2 and up']
    
    
    number of rows: 9659
    number of columns: 13
    
    
    ['281656475', 'PAC-MAN Premium', '100788224', 'USD', '3.99', '21292', '26', '4', '4.5', '6.3.5', '4+', 'Games', '38', '5', '10', '1']
    
    
    ['281796108', 'Evernote - stay organized', '158578688', 'USD', '0', '161065', '26', '4', '3.5', '8.2.2', '4+', 'Productivity', '37', '5', '23', '1']
    
    
    ['281940292', 'WeatherBug - Local Weather, Radar, Maps, Alerts', '100524032', 'USD', '0', '188583', '2822', '3.5', '4.5', '5.0.0', '4+', 'Weather', '37', '5', '3', '1']
    
    
    number of rows: 7197
    number of columns: 16


We can see that we are left with 9659 Android apps and 7197 iOS apps.

# Isolating the Free Apps
As we mentioned in the introduction, we only build apps that are free to download and install, and our main source of revenue consists of in-app ads. Our data sets contain both free and non-free apps, and we'll need to isolate only the free apps for our analysis. Below, we isolate the free apps for both our data sets:


```python
android_final = []
ios_final = []

for app in android_english:
    price = app[7]
    if price == '0':
        android_final.append(app)
        
for app in ios_english:
    price = app[4]
    if price == '0':
        ios_final.append(app)
        
print(len(android_final))
print(len(ios_final)) 
```

    8905
    4056


We're left with 8905 Android apps and 4056 iOS apps, which should be enough for our analysis.

# Most Common Apps by Genre
## Part One
As we mentioned in the introduction, our aim is to determine the kinds of apps that are likely to attract more users because our revenue is highly influenced by the number of people using our apps.

To minimize risks and overhead, our validation strategy for an app idea is comprised of three steps:

1. Build a minimal Android version of the app, and add it to Google Play.
2. If the app has a good response from users, we then develop it further.
3. If the app is profitable after six months, we also build an iOS version of the app and add it to the App Store.

Because our end goal is to add the app on both the App Store and Google Play, we need to find app profiles that are successful on both markets. For instance, a profile that might work well for both markets might be a productivity app that makes use of gamification.

Let's begin the analysis by getting a sense of the most common genres for each market. For this, we'll build a frequency table for the `prime_genre` column of the App Store data set, and the `Genres` and `Category` columns of the Google Play data set.

## Part Two
We'll build two functions we can use to analyze the frequency tables:

* One function to generate frequency tables that show percentages
* Another function that we can use to display the percentages in a descending order


```python
def freq_table(dataset, index):
    table = {}
    total = 0
    
    for row in dataset:
        total += 1
        value = row[index]
        if value in table:
            table[value] += 1
        else:
            table[value] = 1
    table_percentages = {}
    for key in table:
        percentage = (table[key] / total) * 100
        table_percentages[key] = percentage
    return table_percentages

def display_table(dataset, index):
    table = freq_table(dataset, index)
    table_display = []
    for key in table:
        key_val_as_tuple = (table[key], key)
        table_display.append(key_val_as_tuple)
    
    table_sorted = sorted(table_display, reverse = True)
    for entry in table_sorted:
        print(entry[1], ':', entry[0])
```

## Part Three
We start by examining the frequency table for the prime_genre column of the App Store data set.


```python
display_table(ios_final,-5)
```

    Games : 55.64595660749507
    Entertainment : 8.234714003944774
    Photo & Video : 4.117357001972387
    Social Networking : 3.5256410256410255
    Education : 3.2544378698224854
    Shopping : 2.983234714003945
    Utilities : 2.687376725838264
    Lifestyle : 2.3175542406311638
    Finance : 2.0710059171597637
    Sports : 1.947731755424063
    Health & Fitness : 1.8737672583826428
    Music : 1.6518737672583828
    Book : 1.6272189349112427
    Productivity : 1.5285996055226825
    News : 1.4299802761341223
    Travel : 1.3806706114398422
    Food & Drink : 1.0601577909270217
    Weather : 0.7642998027613412
    Reference : 0.4930966469428008
    Navigation : 0.4930966469428008
    Business : 0.4930966469428008
    Catalogs : 0.22189349112426035
    Medical : 0.19723865877712032


We can see that among the free English apps, more than a half (55.64%) are games. Entertainment apps are slightly above 8%, followed by photo and video apps, which are 4.11%. Only 3.25% of the apps are designed for education, whereas social networking apps which amount for 3.52% of the apps in our data set.
The general impression is that App Store (at least the part containing free English apps) is dominated by apps that are designed for fun (games, entertainment, photo and video, social networking, sports, music, etc.), while apps with practical purposes (education, shopping, utilities, productivity, lifestyle, etc.) are more rare. However, the fact that fun apps are the most numerous doesn't also imply that they also have the greatest number of users — the demand might not be the same as the offer.
Let's continue by examining the Genres and Category columns of the Google Play data set (two columns which seem to be related).


```python
display_table(android_final, 1) # Category
```

    FAMILY : 18.97810218978102
    GAME : 9.70241437394722
    TOOLS : 8.433464345873105
    BUSINESS : 4.581695676586187
    LIFESTYLE : 3.9303761931499155
    PRODUCTIVITY : 3.885457608085345
    FINANCE : 3.6833239752947784
    MEDICAL : 3.5148792813026386
    SPORTS : 3.3801235261089273
    PERSONALIZATION : 3.312745648512072
    COMMUNICATION : 3.2341381246490735
    HEALTH_AND_FITNESS : 3.065693430656934
    PHOTOGRAPHY : 2.9421673217293653
    NEWS_AND_MAGAZINES : 2.829870859067939
    SOCIAL : 2.6501965188096577
    TRAVEL_AND_LOCAL : 2.3245367770915215
    SHOPPING : 2.2459292532285233
    BOOKS_AND_REFERENCE : 2.1785513756316677
    DATING : 1.8528916339135317
    VIDEO_PLAYERS : 1.7967434025828188
    MAPS_AND_NAVIGATION : 1.4149354295339696
    FOOD_AND_DRINK : 1.235261089275688
    EDUCATION : 1.167883211678832
    ENTERTAINMENT : 0.9545199326221224
    LIBRARIES_AND_DEMO : 0.9320606400898372
    AUTO_AND_VEHICLES : 0.9208309938236946
    HOUSE_AND_HOME : 0.8197641774284109
    WEATHER : 0.7973048848961257
    EVENTS : 0.7074677147669848
    PARENTING : 0.6513194834362718
    ART_AND_DESIGN : 0.6513194834362718
    COMICS : 0.6288601909039866
    BEAUTY : 0.5951712521055587


The landscape seems significantly different on Google Play: there are not that many apps designed for fun, and it seems that a good number of apps are designed for practical purposes (family, tools, business, lifestyle, productivity, etc.). However, if we investigate this further, we can see that the family category (which accounts for almost 19% of the apps) means mostly games for kids.
Even so, practical apps seem to have a better representation on Google Play compared to App Store. This picture is also confirmed by the frequency table we see for the Genres column:


```python
display_table(android_final, -4)
```

    Tools : 8.422234699606962
    Entertainment : 6.086468276249298
    Education : 5.390230207748456
    Business : 4.581695676586187
    Lifestyle : 3.9191465468837734
    Productivity : 3.885457608085345
    Finance : 3.6833239752947784
    Medical : 3.5148792813026386
    Sports : 3.4475014037057834
    Personalization : 3.312745648512072
    Communication : 3.2341381246490735
    Action : 3.0881527231892196
    Health & Fitness : 3.065693430656934
    Photography : 2.9421673217293653
    News & Magazines : 2.829870859067939
    Social : 2.6501965188096577
    Travel & Local : 2.313307130825379
    Shopping : 2.2459292532285233
    Books & Reference : 2.1785513756316677
    Simulation : 2.0662549129702414
    Dating : 1.8528916339135317
    Arcade : 1.8416619876473892
    Video Players & Editors : 1.7742841100505335
    Casual : 1.7518248175182483
    Maps & Navigation : 1.4149354295339696
    Food & Drink : 1.235261089275688
    Puzzle : 1.1229646266142617
    Racing : 0.9882088714205502
    Role Playing : 0.9320606400898372
    Libraries & Demo : 0.9320606400898372
    Strategy : 0.9208309938236946
    Auto & Vehicles : 0.9208309938236946
    House & Home : 0.8197641774284109
    Weather : 0.7973048848961257
    Events : 0.7074677147669848
    Adventure : 0.6850084222346996
    Comics : 0.617630544637844
    Art & Design : 0.6064008983717013
    Beauty : 0.5951712521055587
    Parenting : 0.4941044357102751
    Card : 0.4491858506457047
    Trivia : 0.4267265581134195
    Casino : 0.4267265581134195
    Educational;Education : 0.39303761931499154
    Board : 0.38180797304884895
    Educational : 0.3705783267827063
    Education;Education : 0.3481190342504211
    Word : 0.2582818641212802
    Casual;Pretend Play : 0.23582257158899494
    Music : 0.2021336327905671
    Racing;Action & Adventure : 0.16844469399213924
    Puzzle;Brain Games : 0.16844469399213924
    Entertainment;Music & Video : 0.16844469399213924
    Casual;Brain Games : 0.13475575519371139
    Casual;Action & Adventure : 0.13475575519371139
    Arcade;Action & Adventure : 0.12352610892756878
    Action;Action & Adventure : 0.10106681639528355
    Educational;Pretend Play : 0.08983717012914093
    Simulation;Action & Adventure : 0.07860752386299832
    Parenting;Education : 0.07860752386299832
    Entertainment;Brain Games : 0.07860752386299832
    Board;Brain Games : 0.07860752386299832
    Parenting;Music & Video : 0.06737787759685569
    Educational;Brain Games : 0.06737787759685569
    Casual;Creativity : 0.06737787759685569
    Art & Design;Creativity : 0.06737787759685569
    Education;Pretend Play : 0.05614823133071309
    Role Playing;Pretend Play : 0.044918585064570464
    Education;Creativity : 0.044918585064570464
    Role Playing;Action & Adventure : 0.033688938798427846
    Puzzle;Action & Adventure : 0.033688938798427846
    Entertainment;Creativity : 0.033688938798427846
    Entertainment;Action & Adventure : 0.033688938798427846
    Educational;Creativity : 0.033688938798427846
    Educational;Action & Adventure : 0.033688938798427846
    Education;Music & Video : 0.033688938798427846
    Education;Brain Games : 0.033688938798427846
    Education;Action & Adventure : 0.033688938798427846
    Adventure;Action & Adventure : 0.033688938798427846
    Video Players & Editors;Music & Video : 0.022459292532285232
    Sports;Action & Adventure : 0.022459292532285232
    Simulation;Pretend Play : 0.022459292532285232
    Puzzle;Creativity : 0.022459292532285232
    Music;Music & Video : 0.022459292532285232
    Entertainment;Pretend Play : 0.022459292532285232
    Casual;Education : 0.022459292532285232
    Board;Action & Adventure : 0.022459292532285232
    Video Players & Editors;Creativity : 0.011229646266142616
    Trivia;Education : 0.011229646266142616
    Travel & Local;Action & Adventure : 0.011229646266142616
    Tools;Education : 0.011229646266142616
    Strategy;Education : 0.011229646266142616
    Strategy;Creativity : 0.011229646266142616
    Strategy;Action & Adventure : 0.011229646266142616
    Simulation;Education : 0.011229646266142616
    Role Playing;Brain Games : 0.011229646266142616
    Racing;Pretend Play : 0.011229646266142616
    Puzzle;Education : 0.011229646266142616
    Parenting;Brain Games : 0.011229646266142616
    Music & Audio;Music & Video : 0.011229646266142616
    Lifestyle;Pretend Play : 0.011229646266142616
    Lifestyle;Education : 0.011229646266142616
    Health & Fitness;Education : 0.011229646266142616
    Health & Fitness;Action & Adventure : 0.011229646266142616
    Entertainment;Education : 0.011229646266142616
    Communication;Creativity : 0.011229646266142616
    Comics;Creativity : 0.011229646266142616
    Casual;Music & Video : 0.011229646266142616
    Card;Action & Adventure : 0.011229646266142616
    Books & Reference;Education : 0.011229646266142616
    Art & Design;Pretend Play : 0.011229646266142616
    Art & Design;Action & Adventure : 0.011229646266142616
    Arcade;Pretend Play : 0.011229646266142616
    Adventure;Education : 0.011229646266142616


The difference between the Genres and the Category columns is not crystal clear, but one thing we can notice is that the Genres column is much more granular (it has more categories). We're only looking for the bigger picture at the moment, so we'll only work with the Category column moving forward.
Up to this point, we found that the App Store is dominated by apps designed for fun, while Google Play shows a more balanced landscape of both practical and for-fun apps. Now we'd like to get an idea about the kind of apps that have most users.

# Most Popular Apps by Genre on the App Store
One way to find out what genres are the most popular (have the most users) is to calculate the average number of installs for each app genre. For the Google Play data set, we can find this information in the `Installs` column, but for the App Store data set this information is missing. As a workaround, we'll take the total number of user ratings as a proxy, which we can find in the `rating_count_tot` app.

Below, we calculate the average number of user ratings per app genre on the App Store:


```python
genres_ios = freq_table(ios, -5)

for genre in genres_ios:
    total = 0
    len_genre = 0
    for app in ios_final:
        genre_app = app[-5]
        if genre_app == genre:
            n_ratings = float(app[5])
            total += n_ratings
            len_genre += 1
    avg_n_ratings = total / len_genre
    print(genre, ':', avg_n_ratings)
```

    Games : 18924.68896765618
    Productivity : 19053.887096774193
    Weather : 47220.93548387097
    Shopping : 18746.677685950413
    Reference : 67447.9
    Finance : 13522.261904761905
    Music : 56482.02985074627
    Utilities : 14010.100917431193
    Travel : 20216.01785714286
    Social Networking : 53078.195804195806
    Sports : 20128.974683544304
    Business : 6367.8
    Health & Fitness : 19952.315789473683
    Entertainment : 10822.961077844311
    Photo & Video : 27249.892215568863
    Navigation : 25972.05
    Education : 6266.333333333333
    Lifestyle : 8978.308510638299
    Food & Drink : 20179.093023255813
    News : 15892.724137931034
    Book : 8498.333333333334
    Medical : 459.75
    Catalogs : 1779.5555555555557


On average, Reference apps have the highest number of user reviews, but this figure is heavily influenced by Bible and Dictionary, which have close to half a million user reviews together:


```python
for app in ios_final:
    if app[-5] == 'Reference':
        print(app[1], ':', app[5]) # print name and number of ratings
```

    Bible : 985920
    Dictionary.com Dictionary & Thesaurus : 200047
    Dictionary.com Dictionary & Thesaurus for iPad : 54175
    Muslim Pro: Ramadan 2017 Prayer Times, Azan, Quran : 18418
    Merriam-Webster Dictionary : 16849
    Google Translate : 26786
    Night Sky : 12122
    WWDC : 762
    Jishokun-Japanese English Dictionary & Translator : 0
    教えて!goo : 0
    VPN Express : 14
    彩库宝典-【官方版】 : 0
    New Furniture Mods - Pocket Wiki & Game Tools for Minecraft PC Edition : 17588
    LUCKY BLOCK MOD ™ for Minecraft PC Edition - The Best Pocket Wiki & Mods Installer Tools : 4693
    Guides for Pokémon GO - Pokemon GO News and Cheats : 826
    Horror Maps for Minecraft PE - Download The Scariest Maps for Minecraft Pocket Edition (MCPE) Free : 718
    City Maps for Minecraft PE - The Best Maps for Minecraft Pocket Edition (MCPE) : 8535
    GUNS MODS for Minecraft PC Edition - Mods Tools : 1497
    Real Bike Traffic Rider Virtual Reality Glasses : 8
    無料で音楽や写真・カメラの裏技アプリ for iPhone7 : 0


However, this niche seems to show some potential. One thing we could do is take another popular book and turn it into an app where we could add different features besides the raw version of the book. This might include daily quotes from the book, an audio version of the book, quizzes about the book, etc. On top of that, we could also embed a dictionary within the app, so users don't need to exit our app to look up words in an external app.


The same pattern applies to social networking apps, where the average number is heavily influenced by a few giants like Facebook, Pinterest, Skype, etc. Same applies to photo & video apps, where a few big players like Instagram, Snapchat, Youtube heavily influence the average number.

Our aim is to find popular genres, but Reference, social networking or music apps might seem more popular than they really are. The average number of ratings seem to be skewed by very few apps which have hundreds of thousands of user ratings, while the other apps may struggle to get past the 10,000 threshold. We could get a better picture by removing these extremely popular apps for each genre and then rework the averages, but we'll leave this level of detail for later.

Music apps have 56,482 user ratings on average, but it's actually the Pandora and Spotify which skew up the average rating:


```python
for app in ios_final:
    if app[-5] == 'Music':
        print(app[1], ':', app[5])
```

    Pandora - Music & Radio : 1126879
    Shazam - Discover music, artists, videos & lyrics : 402925
    iHeartRadio – Free Music & Radio Stations : 293228
    Deezer - Listen to your Favorite Music & Playlists : 4677
    Sonos Controller : 48905
    NRJ Radio : 38
    radio.de - Der Radioplayer : 64
    Spotify Music : 878563
    SoundCloud - Music & Audio : 135744
    Sing Karaoke Songs Unlimited with StarMaker : 26227
    SoundHound Song Search & Music Player : 82602
    Ringtones for iPhone & Ringtone Maker : 25403
    Coach Guitar - Lessons & Easy Tabs For Beginners : 2416
    QQ音乐-来这里“发现・音乐” : 745
    TuneIn Radio - MLB NBA Audiobooks Podcasts Music : 110420
    Magic Piano by Smule : 131695
    QQ音乐HD : 224
    The Singing Machine Mobile Karaoke App : 130
    Bandsintown Concerts : 30845
    PetitLyrics : 0
    edjing Mix:DJ turntable to remix and scratch music : 13580
    Smule Sing! : 119316
    Amazon Music : 106235
    AutoRap by Smule : 18202
    My Mixtapez Music : 26286
    Certified Mixtapes - Hip Hop Albums & Mixtapes : 9975
    Karaoke - Sing Karaoke, Unlimited Songs! : 28606
    Napster - Top Music & Radio : 14268
    Musi - Unlimited Music For YouTube : 25193
    UE BOOM : 612
    Spinrilla - Mixtapes For Free : 15053
    Google Play Music : 10118
    Piano - Play Keyboard Music Games with Magic Tiles : 1636
    Bose SoundTouch : 3687
    DatPiff : 2815
    Sounds app - Music And Friends : 5126
    Smart Music: Streaming Videos and Radio : 17
    Free Piano app by Yokee : 13016
    Simple Radio - Live AM & FM Radio Stations : 4787
    Trebel Music - Unlimited Music Downloader : 2570
    TIDAL : 7398
    Acapella from PicPlayPost : 2487
    Medly - Music Maker : 933
    Amazon Alexa : 3018
    Music Freedom - Unlimited Free MP3 Music Streaming : 1246
    PlayGround • Music At Your Fingertips : 150
    Musical Video Maker - Create Music clips lip sync : 320
    Free Music Play - Mp3 Streamer & Player : 2496
    LiveMixtapes : 555
    AmpMe - A Portable Social Party Music Speaker : 1047
    NOISE : 355
    YouTube Music : 7109
    Ringtones for iPhone with Ringtone Maker : 4013
    Music Memos : 909
    Musicloud - MP3 and FLAC Music Player for Cloud Platforms. : 2211
    Bose Connect : 915
    Cloud Music Player - Downloader & Playlist Manager : 319
    Remixlive - Remix loops with pads : 288
    Free Music -  Player & Streamer  for Dropbox, OneDrive & Google Drive : 46
    Boom: Best Equalizer & Magical Surround Sound : 1375
    MP3 Music Player & Streamer for Clouds : 329
    Nicki Minaj: The Empire : 5196
    SongFlip - Free Music Streamer : 5004
    Blocs Wave - Make & Record Music : 158
    Music and Chill : 135
    Free Music - MP3 Streamer & Playlist Manager Pro : 13443
    BOSS Tuner : 13


This idea seems to fit well with the fact that the App Store is dominated by practical apps. This suggests the market might be a bit saturated with pratical apps, which means a for-fun app might have more of a chance to stand out among the huge number of apps on the App Store.

Other genres that seem popular include weather, games, sports,food & drink or Finance. The games genre seem to overlap a bit with the app idea we described above, but the other genres don't seem too interesting to us:

* Weather apps — people generally don't spend too much time in-app, and the chances of making profit from in-app adds are low. Also, getting reliable live weather data may require us to connect our apps to non-free APIs.
* Food and drink — examples here include Starbucks, Dunkin' Donuts, McDonald's, etc. So making a popular food and drink app requires actual cooking and a delivery service, which is outside the scope of our company.
* Finance apps — these apps involve banking, paying bills, money transfer, etc. Building a finance app requires domain knowledge, and we don't want to hire a finance expert just to build an app.

Now let's analyze the Google Play market a bit.

# Most Popular Apps by Genre on Google Play
For the Google Play market, we actually have data about the number of installs, so we should be able to get a clearer picture about genre popularity. However, the install numbers don't seem precise enough — we can see that most values are open-ended (100+, 1,000+, 5,000+, etc.):


```python
display_table(android_final, 5) # the Installs columns
```

    1,000,000+ : 15.687815833801237
    100,000+ : 11.577765300393038
    10,000,000+ : 10.499719258843346
    10,000+ : 10.252667040988209
    1,000+ : 8.422234699606962
    100+ : 6.917462099943853
    5,000,000+ : 6.816395283548568
    500,000+ : 5.53621560920831
    50,000+ : 4.817518248175182
    5,000+ : 4.525547445255475
    10+ : 3.537338573834924
    500+ : 3.2341381246490735
    50,000,000+ : 2.2908478382930935
    100,000,000+ : 2.1224031443009546
    50+ : 1.9090398652442448
    5+ : 0.7860752386299831
    1+ : 0.5165637282425604
    500,000,000+ : 0.26951151038742277
    1,000,000,000+ : 0.22459292532285235
    0+ : 0.044918585064570464
    0 : 0.011229646266142616


One problem with this data is that is not precise. For instance, we don't know whether an app with 100,000+ installs has 100,000 installs, 200,000, or 350,000. However, we don't need very precise data for our purposes — we only want to get an idea which app genres attract the most users, and we don't need perfect precision with respect to the number of users.

We're going to leave the numbers as they are, which means that we'll consider that an app with 100,000+ installs has 100,000 installs, and an app with 1,000,000+ installs has 1,000,000 installs, and so on.

To perform computations, however, we'll need to convert each install number to float — this means that we need to remove the commas and the plus characters, otherwise the conversion will fail and raise an error. We'll do this directly in the loop below, where we also compute the average number of installs for each genre (category).


```python
categories_android = freq_table(android_final, 1)
for category in categories_android:
    total = 0
    len_category = 0
    for app in android_final:
        category_app = app[1]
        if category_app == category:
            n_installs = app[5]
            n_installs = n_installs.replace(',', '')
            n_installs = n_installs.replace('+', '')
            total += float(n_installs)
            len_category += 1
    avg_n_installs = total / len_category
    print(category, ':', avg_n_installs)         
```

    ART_AND_DESIGN : 1952105.1724137932
    AUTO_AND_VEHICLES : 647317.8170731707
    BEAUTY : 513151.88679245283
    BOOKS_AND_REFERENCE : 8587351.855670104
    BUSINESS : 1708215.906862745
    COMICS : 803234.8214285715
    COMMUNICATION : 38322625.697916664
    DATING : 854028.8303030303
    EDUCATION : 1825480.7692307692
    ENTERTAINMENT : 11640705.88235294
    EVENTS : 253542.22222222222
    FINANCE : 1387692.475609756
    FOOD_AND_DRINK : 1924897.7363636363
    HEALTH_AND_FITNESS : 4188821.9853479853
    HOUSE_AND_HOME : 1331540.5616438356
    LIBRARIES_AND_DEMO : 638503.734939759
    LIFESTYLE : 1436126.94
    GAME : 15551995.891203703
    FAMILY : 3668870.823076923
    MEDICAL : 120550.61980830671
    SOCIAL : 23253652.127118643
    SHOPPING : 7001693.425
    PHOTOGRAPHY : 17772018.759541985
    SPORTS : 3638640.1428571427
    TRAVEL_AND_LOCAL : 13984077.710144928
    TOOLS : 10787009.952063914
    PERSONALIZATION : 5183850.806779661
    PRODUCTIVITY : 16738957.554913295
    PARENTING : 542603.6206896552
    WEATHER : 5074486.197183099
    VIDEO_PLAYERS : 24573948.25
    NEWS_AND_MAGAZINES : 9401635.952380951
    MAPS_AND_NAVIGATION : 3993339.603174603


On average, communication apps have the most installs: 38,322,625. This number is heavily skewed up by a few apps that have over one billion installs (WhatsApp, Facebook Messenger, Skype, Google Chrome, Gmail, and Hangouts), and a few others with over 100 and 500 million installs:


```python
for app in android_final:
    if app[1] == 'COMMUNICATION' and (app[5] == '1,000,000,000+'
                                      or app[5] == '500,000,000+'
                                      or app[5] == '100,000,000+'):
        print(app[0], ':', app[5])
```

    WhatsApp Messenger : 1,000,000,000+
    imo beta free calls and text : 100,000,000+
    Android Messages : 100,000,000+
    Google Duo - High Quality Video Calls : 500,000,000+
    Messenger – Text and Video Chat for Free : 1,000,000,000+
    imo free video calls and chat : 500,000,000+
    Skype - free IM & video calls : 1,000,000,000+
    Who : 100,000,000+
    GO SMS Pro - Messenger, Free Themes, Emoji : 100,000,000+
    LINE: Free Calls & Messages : 500,000,000+
    Google Chrome: Fast & Secure : 1,000,000,000+
    Firefox Browser fast & private : 100,000,000+
    UC Browser - Fast Download Private & Secure : 500,000,000+
    Gmail : 1,000,000,000+
    Hangouts : 1,000,000,000+
    Messenger Lite: Free Calls & Messages : 100,000,000+
    Kik : 100,000,000+
    KakaoTalk: Free Calls & Text : 100,000,000+
    Opera Mini - fast web browser : 100,000,000+
    Opera Browser: Fast and Secure : 100,000,000+
    Telegram : 100,000,000+
    Truecaller: Caller ID, SMS spam blocking & Dialer : 100,000,000+
    UC Browser Mini -Tiny Fast Private & Secure : 100,000,000+
    Viber Messenger : 500,000,000+
    WeChat : 100,000,000+
    Yahoo Mail – Stay Organized : 100,000,000+
    BBM - Free Calls & Messages : 100,000,000+


If we removed all the communication apps that have over 100 million installs, the average would be reduced roughly ten times:


```python
under_100m = []

for app in android_final:
    n_installs = app[5]
    n_installs = n_installs.replace(',', '')
    n_installs = n_installs.replace('+', '')
    if (app[1] == 'COMMUNICATION') and (float(n_installs) < 100000000):
        under_100m.append(float(n_installs))

sum(under_100m) / len(under_100m)
```




    3589717.245210728



We see the same pattern for the video players category, which is the winner with 55,088,19 installs. The market is dominated by apps like Youtube, Google Play Movies & TV, or MX Player. The pattern is repeated for social apps (where we have giants like Facebook, Instagram, Google+, etc.), photography apps (Google Photos and other popular photo editors), or productivity apps (Microsoft Word, Dropbox, Google Calendar, Evernote, etc.).

Again, the main concern is that these app genres might seem more popular than they really are. Moreover, these niches seem to be dominated by a few giants who are hard to compete against.

The game genre seems pretty popular, but previously we found out this part of the market seems a bit saturated, so we'd like to come up with a different app recommendation if possible.

The books and reference genre looks fairly popular as well, with an average number of installs of 8,767,811. It's interesting to explore this in more depth, since we found this genre has some potential to work well on the App Store, and our aim is to recommend an app genre that shows potential for being profitable on both the App Store and Google Play.

Let's take a look at some of the apps from this genre and their number of installs:


```python
for app in android_final:
    if app[1] == 'BOOKS_AND_REFERENCE':
        print(app[0], ':', app[5])
```

    E-Book Read - Read Book for free : 50,000+
    Download free book with green book : 100,000+
    Wikipedia : 10,000,000+
    Cool Reader : 10,000,000+
    Free Panda Radio Music : 100,000+
    Book store : 1,000,000+
    FBReader: Favorite Book Reader : 10,000,000+
    English Grammar Complete Handbook : 500,000+
    Free Books - Spirit Fanfiction and Stories : 1,000,000+
    Google Play Books : 1,000,000,000+
    AlReader -any text book reader : 5,000,000+
    Offline English Dictionary : 100,000+
    Offline: English to Tagalog Dictionary : 500,000+
    FamilySearch Tree : 1,000,000+
    Cloud of Books : 1,000,000+
    Recipes of Prophetic Medicine for free : 500,000+
    ReadEra – free ebook reader : 1,000,000+
    Anonymous caller detection : 10,000+
    Ebook Reader : 5,000,000+
    Litnet - E-books : 100,000+
    Read books online : 5,000,000+
    English to Urdu Dictionary : 500,000+
    eBoox: book reader fb2 epub zip : 1,000,000+
    English Persian Dictionary : 500,000+
    Flybook : 500,000+
    All Maths Formulas : 1,000,000+
    Ancestry : 5,000,000+
    HTC Help : 10,000,000+
    English translation from Bengali : 100,000+
    Pdf Book Download - Read Pdf Book : 100,000+
    Free Book Reader : 100,000+
    eBoox new: Reader for fb2 epub zip books : 50,000+
    Only 30 days in English, the guideline is guaranteed : 500,000+
    Moon+ Reader : 10,000,000+
    SH-02J Owner's Manual (Android 8.0) : 50,000+
    English-Myanmar Dictionary : 1,000,000+
    Golden Dictionary (EN-AR) : 1,000,000+
    All Language Translator Free : 1,000,000+
    Azpen eReader : 500,000+
    URBANO V 02 instruction manual : 100,000+
    Bible : 100,000,000+
    C Programs and Reference : 50,000+
    C Offline Tutorial : 1,000+
    C Programs Handbook : 50,000+
    Amazon Kindle : 100,000,000+
    Aab e Hayat Full Novel : 100,000+
    Aldiko Book Reader : 10,000,000+
    Google I/O 2018 : 500,000+
    R Language Reference Guide : 10,000+
    Learn R Programming Full : 5,000+
    R Programing Offline Tutorial : 1,000+
    Guide for R Programming : 5+
    Learn R Programming : 10+
    R Quick Reference Big Data : 1,000+
    V Made : 100,000+
    Wattpad 📖 Free Books : 100,000,000+
    Dictionary - WordWeb : 5,000,000+
    Guide (for X-MEN) : 100,000+
    AC Air condition Troubleshoot,Repair,Maintenance : 5,000+
    AE Bulletins : 1,000+
    Ae Allah na Dai (Rasa) : 10,000+
    50000 Free eBooks & Free AudioBooks : 5,000,000+
    Ag PhD Field Guide : 10,000+
    Ag PhD Deficiencies : 10,000+
    Ag PhD Planting Population Calculator : 1,000+
    Ag PhD Soybean Diseases : 1,000+
    Fertilizer Removal By Crop : 50,000+
    A-J Media Vault : 50+
    Al-Quran (Free) : 10,000,000+
    Al Quran (Tafsir & by Word) : 500,000+
    Al Quran Indonesia : 10,000,000+
    Al'Quran Bahasa Indonesia : 10,000,000+
    Al Quran Al karim : 1,000,000+
    Al-Muhaffiz : 50,000+
    Al Quran : EAlim - Translations & MP3 Offline : 5,000,000+
    Al-Quran 30 Juz free copies : 500,000+
    Koran Read &MP3 30 Juz Offline : 1,000,000+
    Hafizi Quran 15 lines per page : 1,000,000+
    Quran for Android : 10,000,000+
    Al Quran Free - القرآن (Islam) : 50,000+
    Surah Al-Waqiah : 100,000+
    Hisnul Al Muslim - Hisn Invocations & Adhkaar : 100,000+
    Satellite AR : 1,000,000+
    Audiobooks from Audible : 100,000,000+
    日本AV历史 : 10,000+
    Kinot & Eichah for Tisha B'Av : 10,000+
    AW Tozer Devotionals - Daily : 5,000+
    Tozer Devotional -Series 1 : 1,000+
    The Pursuit of God : 1,000+
    AY Sing : 5,000+
    Ay Hasnain k Nana Milad Naat : 10,000+
    Ay Mohabbat Teri Khatir Novel : 10,000+
    Arizona Statutes, ARS (AZ Law) : 1,000+
    Oxford A-Z of English Usage : 1,000,000+
    BD Fishpedia : 1,000+
    BD All Sim Offer : 10,000+
    Youboox - Livres, BD et magazines : 500,000+
    Cъновник BG : 1,000+
    B&H Kids AR : 10,000+
    B y H Niños ES : 5,000+
    Dictionary.com: Find Definitions for English Words : 10,000,000+
    English Dictionary - Offline : 10,000,000+
    Bible KJV : 5,000,000+
    Borneo Bible, BM Bible : 10,000+
    MOD Black for BM : 100+
    BM Box : 1,000+
    Anime Mod for BM : 100+
    NOOK: Read eBooks & Magazines : 10,000,000+
    NOOK Audiobooks : 500,000+
    NOOK App for NOOK Devices : 500,000+
    Browsery by Barnes & Noble : 5,000+
    bp e-store : 1,000+
    Brilliant Quotes: Life, Love, Family & Motivation : 1,000,000+
    BR Ambedkar Biography & Quotes : 10,000+
    BU Alsace : 100+
    Catholic La Bu Zo Kam : 500+
    Khrifa Hla Bu (Solfa) : 10+
    Kristian Hla Bu : 10,000+
    SA HLA BU : 1,000+
    Learn SAP BW : 500+
    Learn SAP BW on HANA : 500+
    CA Laws 2018 (California Laws and Codes) : 5,000+
    Bootable Methods(USB-CD-DVD) : 10,000+
    cloudLibrary : 100,000+
    SDA Collegiate Quarterly : 500+
    Sabbath School : 100,000+
    Cypress College Library : 100+
    Stats Royale for Clash Royale : 1,000,000+
    GATE 21 years CS Papers(2011-2018 Solved) : 50+
    Learn CT Scan Of Head : 5,000+
    Easy Cv maker 2018 : 10,000+
    How to Write CV : 100,000+
    CW Nuclear : 1,000+
    CY Spray nozzle : 10+
    BibleRead En Cy Zh Yue : 5+
    CZ-Help : 5+
    Modlitební knížka CZ : 500+
    Guide for DB Xenoverse : 10,000+
    Guide for DB Xenoverse 2 : 10,000+
    Guide for IMS DB : 10+
    DC HSEMA : 5,000+
    DC Public Library : 1,000+
    Painting Lulu DC Super Friends : 1,000+
    Dictionary : 10,000,000+
    Fix Error Google Playstore : 1,000+
    D. H. Lawrence Poems FREE : 1,000+
    Bilingual Dictionary Audio App : 5,000+
    DM Screen : 10,000+
    wikiHow: how to do anything : 1,000,000+
    Dr. Doug's Tips : 1,000+
    Bible du Semeur-BDS (French) : 50,000+
    La citadelle du musulman : 50,000+
    DV 2019 Entry Guide : 10,000+
    DV 2019 - EDV Photo & Form : 50,000+
    DV 2018 Winners Guide : 1,000+
    EB Annual Meetings : 1,000+
    EC - AP & Telangana : 5,000+
    TN Patta Citta & EC : 10,000+
    AP Stamps and Registration : 10,000+
    CompactiMa EC pH Calibration : 100+
    EGW Writings 2 : 100,000+
    EGW Writings : 1,000,000+
    Bible with EGW Comments : 100,000+
    My Little Pony AR Guide : 1,000,000+
    SDA Sabbath School Quarterly : 500,000+
    Duaa Ek Ibaadat : 5,000+
    Spanish English Translator : 10,000,000+
    Dictionary - Merriam-Webster : 10,000,000+
    JW Library : 10,000,000+
    Oxford Dictionary of English : Free : 10,000,000+
    English Hindi Dictionary : 10,000,000+
    English to Hindi Dictionary : 5,000,000+
    EP Research Service : 1,000+
    FAHREDDİN er-RÂZİ TEFSİRİ : 1,000+
    Hymnes et Louanges : 100,000+
    EU Charter : 1,000+
    EU Data Protection : 1,000+
    EU IP Codes : 100+
    EW PDF : 5+
    BakaReader EX : 100,000+
    EZ Quran : 50,000+
    FA Part 1 & 2 Past Papers Solved Free – Offline : 5,000+
    La Fe de Jesus : 1,000+
    La Fe de Jesús : 500+
    Le Fe de Jesus : 500+
    Florida - Pocket Brainbook : 1,000+
    Florida Statutes (FL Code) : 1,000+
    English To Shona Dictionary : 10,000+
    Greek Bible FP (Audio) : 1,000+
    Golden Dictionary (FR-AR) : 500,000+
    Fanfic-FR : 5,000+
    Bulgarian French Dictionary Fr : 10,000+
    Chemin (fr) : 1,000+
    The SCP Foundation DB fr nn5n : 1,000+


The book and reference genre includes a variety of apps: software for processing and reading ebooks, various collections of libraries, dictionaries, tutorials on programming or languages, etc. It seems there's still a small number of extremely popular apps that skew the average:


```python
for app in android_final:
    if app[1] == 'BOOKS_AND_REFERENCE' and (app[5] == '1,000,000,000+'
                                            or app[5] == '500,000,000+'
                                            or app[5] == '100,000,000+'):
        print(app[0], ':', app[5])
```

    Google Play Books : 1,000,000,000+
    Bible : 100,000,000+
    Amazon Kindle : 100,000,000+
    Wattpad 📖 Free Books : 100,000,000+
    Audiobooks from Audible : 100,000,000+


However, it looks like there are only a few very popular apps, so this market still shows potential. Let's try to get some app ideas based on the kind of apps that are somewhere in the middle in terms of popularity (between 1,000,000 and 100,000,000 downloads):


```python
for app in android_final:
    if app[1] == 'BOOKS_AND_REFERENCE' and (app[5] == '1,000,000+'
                                            or app[5] == '5,000,000+'
                                            or app[5] == '10,000,000+'
                                            or app[5] == '50,000,000+'):
        print(app[0], ':', app[5])
```

    Wikipedia : 10,000,000+
    Cool Reader : 10,000,000+
    Book store : 1,000,000+
    FBReader: Favorite Book Reader : 10,000,000+
    Free Books - Spirit Fanfiction and Stories : 1,000,000+
    AlReader -any text book reader : 5,000,000+
    FamilySearch Tree : 1,000,000+
    Cloud of Books : 1,000,000+
    ReadEra – free ebook reader : 1,000,000+
    Ebook Reader : 5,000,000+
    Read books online : 5,000,000+
    eBoox: book reader fb2 epub zip : 1,000,000+
    All Maths Formulas : 1,000,000+
    Ancestry : 5,000,000+
    HTC Help : 10,000,000+
    Moon+ Reader : 10,000,000+
    English-Myanmar Dictionary : 1,000,000+
    Golden Dictionary (EN-AR) : 1,000,000+
    All Language Translator Free : 1,000,000+
    Aldiko Book Reader : 10,000,000+
    Dictionary - WordWeb : 5,000,000+
    50000 Free eBooks & Free AudioBooks : 5,000,000+
    Al-Quran (Free) : 10,000,000+
    Al Quran Indonesia : 10,000,000+
    Al'Quran Bahasa Indonesia : 10,000,000+
    Al Quran Al karim : 1,000,000+
    Al Quran : EAlim - Translations & MP3 Offline : 5,000,000+
    Koran Read &MP3 30 Juz Offline : 1,000,000+
    Hafizi Quran 15 lines per page : 1,000,000+
    Quran for Android : 10,000,000+
    Satellite AR : 1,000,000+
    Oxford A-Z of English Usage : 1,000,000+
    Dictionary.com: Find Definitions for English Words : 10,000,000+
    English Dictionary - Offline : 10,000,000+
    Bible KJV : 5,000,000+
    NOOK: Read eBooks & Magazines : 10,000,000+
    Brilliant Quotes: Life, Love, Family & Motivation : 1,000,000+
    Stats Royale for Clash Royale : 1,000,000+
    Dictionary : 10,000,000+
    wikiHow: how to do anything : 1,000,000+
    EGW Writings : 1,000,000+
    My Little Pony AR Guide : 1,000,000+
    Spanish English Translator : 10,000,000+
    Dictionary - Merriam-Webster : 10,000,000+
    JW Library : 10,000,000+
    Oxford Dictionary of English : Free : 10,000,000+
    English Hindi Dictionary : 10,000,000+
    English to Hindi Dictionary : 5,000,000+


This niche seems to be dominated by software for processing and reading ebooks, as well as various collections of libraries and dictionaries, so it's probably not a good idea to build similar apps since there'll be some significant competition.

We also notice there are quite a few apps built around the book Quran, which suggests that building an app around a popular book can be profitable. It seems that taking a popular book (perhaps a more recent book) and turning it into an app could be profitable for both the Google Play and the App Store markets.

However, it looks like the market is already full of libraries, so we need to add some special features besides the raw version of the book. This might include daily quotes from the book, an audio version of the book, quizzes on the book, a forum where people can discuss the book, etc.

# Conclusions
In this project, we analyzed data about the App Store and Google Play mobile apps with the goal of recommending an app profile that can be profitable for both markets.

We concluded that taking a popular book (perhaps a more recent book) and turning it into an app could be profitable for both the Google Play and the App Store markets. The markets are already full of libraries, so we need to add some special features besides the raw version of the book. This might include daily quotes from the book, an audio version of the book, quizzes on the book, a forum where people can discuss the book, etc.
