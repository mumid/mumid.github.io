title: "Data Science Project: Analysis of profitable app profiles for App and Google Play Store"
Date: 2019-01-14
tags: [Data Science, Data Analysis, Python]
header:
  image:"/images/2019-01-14/analysis-banner.jpg"
excerpt: Data Science, Data Analysis, Python
mathjax:"true"
-----

{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Profitable App profile: App Store and Google Play Market\n",
    "\n",
    "The project scope is aimed to identify mobile app profiles that are profitable for the App Store and Google Play markets. We're working as a team of data analysts for a reknowned company that builds Android and iOS mobile apps, and our job is to enable our developer team to make data-driven decisions with respect to the kind of apps they build.\n",
    "\n",
    "At our company, we only build apps that are free to download and install, and our main source of revenue consists of in-app ads. This means that our revenue for any given app is mostly influenced by the number of users that use our app. Our goal for this project is to analyze data to help our developers understand what kinds of apps are likely to attract more users."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "As of September 2018, there were approximately 2 million iOS apps available on the App Store, and 2.1 million Android apps on Google Play.\n",
    "\n",
    "Collecting data for over four million apps requires a significant amount of time and money, so we'll try to analyze a sample of the data instead. To avoid spending resources on collecting new data ourselves, we should first try to see whether we can find any relevant existing data at no cost. Luckily, these are two data sets that seem suitable for our goals:\n",
    "\n",
    "* [A data set](https://www.kaggle.com/lava18/google-play-store-apps/home) containing data about approximately ten thousand Android apps from Google Play — the data was collected in August 2018.\n",
    "* [A data set](https://www.kaggle.com/ramamet4/app-store-apple-data-set-10k-apps/home) containing data about approximately seven thousand iOS apps from the App Store — the data was collected in July 2017.\n",
    "\n",
    "Let's start by opening the two data sets and then continue with exploring the data."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 29,
   "metadata": {},
   "outputs": [],
   "source": [
    "from csv import reader\n",
    "\n",
    "### The Google Play data set ###\n",
    "opened_file = open('googleplaystore.csv', encoding='utf8')\n",
    "read_file = reader(opened_file)\n",
    "android = list(read_file)\n",
    "android_header = android[0]\n",
    "android = android[1:]\n",
    "\n",
    "### The App Store data set ###\n",
    "opened_file = open('AppleStore.csv', encoding='utf8')\n",
    "read_file = reader(opened_file)\n",
    "ios = list(read_file)\n",
    "ios_header = ios[0]\n",
    "ios = ios[1:] "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "To make it easier to explore the two data sets, we'll first write a function named `explore_data()` that we can use repeatedly to explore rows in a more readable way. We'll also add an option for our function to show the number of rows and columns for any data set."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 30,
   "metadata": {
    "scrolled": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']\n",
      "\n",
      "\n",
      "['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']\n",
      "\n",
      "\n",
      "['Coloring book moana', 'ART_AND_DESIGN', '3.9', '967', '14M', '500,000+', 'Free', '0', 'Everyone', 'Art & Design;Pretend Play', 'January 15, 2018', '2.0.0', '4.0.3 and up']\n",
      "\n",
      "\n",
      "['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']\n",
      "\n",
      "\n",
      "number of rows: 10841\n",
      "number of columns: 13\n"
     ]
    }
   ],
   "source": [
    "def explore_data(dataset, start, end, rows_and_column = False):\n",
    "    dataset_slice = dataset[start:end]\n",
    "    for row in dataset_slice:\n",
    "        print(row)\n",
    "        print('\\n') # adds a new empty line between rows.\n",
    "    if rows_and_column:\n",
    "        print('number of rows:', len(dataset))\n",
    "        print('number of columns:', len(dataset[0]))\n",
    "print(android_header)\n",
    "print('\\n')\n",
    "explore_data(android, 0, 3, True) "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We see that the Google Play data set has 10841 apps and 13 columns. At a quick glance, the columns that might be useful for the purpose of our analysis are '`App`', '`Category`', '`Reviews`', '`Installs`', '`Type`', '`Price`' and '`Genres`'.\n",
    "\n",
    "Now let's take a look at the App Store data set."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 31,
   "metadata": {
    "scrolled": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "['id', 'track_name', 'size_bytes', 'currency', 'price', 'rating_count_tot', 'rating_count_ver', 'user_rating', 'user_rating_ver', 'ver', 'cont_rating', 'prime_genre', 'sup_devices.num', 'ipadSc_urls.num', 'lang.num', 'vpp_lic']\n",
      "\n",
      "\n",
      "['281656475', 'PAC-MAN Premium', '100788224', 'USD', '3.99', '21292', '26', '4', '4.5', '6.3.5', '4+', 'Games', '38', '5', '10', '1']\n",
      "\n",
      "\n",
      "['281796108', 'Evernote - stay organized', '158578688', 'USD', '0', '161065', '26', '4', '3.5', '8.2.2', '4+', 'Productivity', '37', '5', '23', '1']\n",
      "\n",
      "\n",
      "['281940292', 'WeatherBug - Local Weather, Radar, Maps, Alerts', '100524032', 'USD', '0', '188583', '2822', '3.5', '4.5', '5.0.0', '4+', 'Weather', '37', '5', '3', '1']\n",
      "\n",
      "\n",
      "number of rows: 7197\n",
      "number of columns: 16\n"
     ]
    }
   ],
   "source": [
    "print(ios_header)\n",
    "print('\\n')\n",
    "explore_data(ios, 0, 3, True) "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We have 7197 ios apps and 16 columns in this data set. The columns that seems interesting are: '`track_name`', '`currency`', '`price`', '`rating_count_tot`', '`rating_count_ver`', and '`prime_genre`'. Not all column names are self-explanatory in this case, but details about each column can be found in the data set [documentation](https://www.kaggle.com/ramamet4/app-store-apple-data-set-10k-apps/home). "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Deleting Wrong Data\n",
    "The Google Play data set has a dedicated [discussion section](https://www.kaggle.com/lava18/google-play-store-apps/discussion), and we can see that [one of the discussions](https://www.kaggle.com/lava18/google-play-store-apps/discussion/66015) outlines an error for row 10472. Let's print this row and compare it against the header and another row that is correct."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 32,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "['Life Made WI-Fi Touchscreen Photo Frame', '1.9', '19', '3.0M', '1,000+', 'Free', '0', 'Everyone', '', 'February 11, 2018', '1.0.19', '4.0 and up']\n",
      "\n",
      "\n",
      "['App', 'Category', 'Rating', 'Reviews', 'Size', 'Installs', 'Type', 'Price', 'Content Rating', 'Genres', 'Last Updated', 'Current Ver', 'Android Ver']\n",
      "\n",
      "\n",
      "['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']\n"
     ]
    }
   ],
   "source": [
    "print(android[10472]) # Uncorrect row\n",
    "print('\\n')\n",
    "print(android_header) # header\n",
    "print('\\n')\n",
    "print(android[0]) # corrct row"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The row 10472 corresponds to the app *Life Made WI-Fi Touchscreen Photo Frame*, and we can see that the rating is 19. This is clearly off because the maximum rating for a Google Play app is 5. As a consequence, we'll delete this row."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 33,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "10841\n",
      "10840\n"
     ]
    }
   ],
   "source": [
    "print(len(android))\n",
    "del(android[10472]) # Don't run this more than once\n",
    "print(len(android))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Removing Duplicate Entries\n",
    "## Part one\n",
    "If we explore the Google Play data set long enough, we'll find that some apps have more than one entry. For instance, the application Instagram has four entries:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 34,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "['Instagram', 'SOCIAL', '4.5', '66577313', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']\n",
      "['Instagram', 'SOCIAL', '4.5', '66577446', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']\n",
      "['Instagram', 'SOCIAL', '4.5', '66577313', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']\n",
      "['Instagram', 'SOCIAL', '4.5', '66509917', 'Varies with device', '1,000,000,000+', 'Free', '0', 'Teen', 'Social', 'July 31, 2018', 'Varies with device', 'Varies with device']\n"
     ]
    }
   ],
   "source": [
    "for app in android:\n",
    "    name = app[0]\n",
    "    if name == 'Instagram':\n",
    "        print(app)    "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In total, there are 1,181 cases where an app occurs more than once:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 35,
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Number of Duplicate Apps: 1181\n",
      "\n",
      "\n",
      "Examples of Duplicate Apps: ['Quick PDF Scanner + OCR FREE', 'Box', 'Google My Business', 'ZOOM Cloud Meetings', 'join.me - Simple Meetings', 'Box', 'Zenefits', 'Google Ads', 'Google My Business', 'Slack', 'FreshBooks Classic', 'Insightly CRM', 'QuickBooks Accounting: Invoicing & Expenses', 'HipChat - Chat Built for Teams', 'Xero Accounting Software']\n"
     ]
    }
   ],
   "source": [
    "duplicate_apps = []\n",
    "unique_apps = []\n",
    "\n",
    "for app in android:\n",
    "    name = app[0]\n",
    "    if name in unique_apps:\n",
    "        duplicate_apps.append(name)\n",
    "    else:\n",
    "        unique_apps.append(name)\n",
    "print('Number of Duplicate Apps:', len(duplicate_apps))\n",
    "print('\\n')\n",
    "print('Examples of Duplicate Apps:', duplicate_apps[:15])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We don't want to count certain apps more than once when we analyze data, so we need to remove the duplicate entries and keep only one entry per app. One thing we could do is remove the duplicate rows randomly, but we could probably find a better way.\n",
    "\n",
    "If you examine the rows we printed two cells above for the Instagram app, the main difference happens on the fourth position of each row, which corresponds to the number of reviews. The different numbers show that the data was collected at different times. We can use this to build a criterion for keeping rows. We won't remove rows randomly, but rather we'll keep the rows that have the highest number of reviews because the higher the number of reviews, the more reliable the ratings."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "To do that, we will:\n",
    "* Create a dictionary where each key is a unique app name, and the value is the highest number of reviews of that app\n",
    "* Use the dictionary to create a new data set, which will have only one entry per app (and we only select the apps with the highest number of reviews)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Part Two\n",
    "Let's start building the dictionary. "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 36,
   "metadata": {},
   "outputs": [],
   "source": [
    "reviews_max = {}\n",
    "\n",
    "for app in android:\n",
    "    name = app[0]\n",
    "    n_reviews = float(app[3])\n",
    "    \n",
    "    if name in reviews_max and reviews_max[name] < n_reviews:\n",
    "        reviews_max[name] = n_reviews\n",
    "    \n",
    "    elif name not in reviews_max:\n",
    "        reviews_max[name] = n_reviews"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "In a previous code cell, we found that there are 1,181 cases where an app occurs more than once, so the length of our dictionary (of unique apps) should be equal to the difference between the length of our data set and 1,181."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 37,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Expected length:  9659\n",
      "Actual length:  9659\n"
     ]
    }
   ],
   "source": [
    "print('Expected length: ', len(android) - 1181)\n",
    "print('Actual length: ', len(reviews_max))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "\n",
    "Now, let's use the `reviews_max` dictionary to remove the duplicates. For the duplicate cases, we'll only keep the entries with the highest number of reviews. In the code cell below:\n",
    "* We start by initializing two empty lists, `android_clean` and `already_added`.\n",
    "* We loop through the `android` data set, and for every iteration:\n",
    "    * We isolate the name of the app and the number of reviews.\n",
    "    * We add the current row `(app)` to the `android_clean` list, and the app name (name) to the `already_added` list if:\n",
    "        * The number of reviews of the current app matches the number of reviews of that app as described in the `reviews_max` dictionary; and\n",
    "        * The name of the app is not already in the `already_added` list. We need to add this supplementary condition to account for those cases where the highest number of reviews of a duplicate app is the same for more than one entry (for example, the Box app has three entries, and the number of reviews is the same). If we just check for `reviews_max[name] == n_reviews`, we'll still end up with duplicate entries for some apps."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 38,
   "metadata": {},
   "outputs": [],
   "source": [
    "android_clean = []\n",
    "already_added = []\n",
    "\n",
    "for app in android:\n",
    "    name = app[0]\n",
    "    n_reviews = float(app[3])\n",
    "    \n",
    "    if (reviews_max[name] == n_reviews) and (name not in already_added):\n",
    "        android_clean.append(app)\n",
    "        already_added.append(name) # make sure this is inside the if block"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "\n",
    "Now let's quickly explore the new data set, and confirm that the number of rows is 9,659."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 39,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']\n",
      "\n",
      "\n",
      "['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']\n",
      "\n",
      "\n",
      "['Sketch - Draw & Paint', 'ART_AND_DESIGN', '4.5', '215644', '25M', '50,000,000+', 'Free', '0', 'Teen', 'Art & Design', 'June 8, 2018', 'Varies with device', '4.2 and up']\n",
      "\n",
      "\n",
      "number of rows: 9659\n",
      "number of columns: 13\n"
     ]
    }
   ],
   "source": [
    "explore_data(android_clean, 0, 3, True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We have 9659 rows, just as expected."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Removing Non-English Apps\n",
    "## Part One\n",
    "If you explore the data sets enough, you'll notice the names of some of the apps suggest they are not directed toward an English-speaking audience. Below, we see a couple of examples from both data sets:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 40,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "AliExpress Shopping App\n",
      "Idle Armies\n",
      "中国語 AQリスニング\n",
      "لعبة تقدر تربح DZ\n"
     ]
    }
   ],
   "source": [
    "print(ios[813][1])\n",
    "print(ios[6731][1])\n",
    "\n",
    "print(android_clean[4412][0])\n",
    "print(android_clean[7940][0])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We're not keeping these kind of apps, so we'll remove them. One way to go about this is to remove each app whose name contains a symbol that is not commonly used in English text — English text usually includes letters from the English alphabet, numbers composed of digits from 0 to 9, punctuation marks (., !, ?, ;, etc.), and other symbols (+, *, /, etc.).\n",
    "\n",
    "All these characters that are specific to English texts are encoded using the ASCII standard. Each ASCII character has a corresponding number between 0 and 127 associated with it, and we can take advantage of that to build a function that checks an app name and tells us whether it contains non-ASCII characters.\n",
    "\n",
    "We built this function below, and we use the built-in `ord()` function to find out the corresponding encoding number of each character."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 41,
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "True\n",
      "False\n"
     ]
    }
   ],
   "source": [
    "def is_english(string):\n",
    "    \n",
    "    for character in string:\n",
    "        if ord(character) > 127:\n",
    "            return False\n",
    "    \n",
    "    return True\n",
    "\n",
    "print(is_english('Instagram'))\n",
    "print(is_english('爱奇艺PPS -《欢乐颂2》电视剧热播'))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The function seems to work fine, but some English app names use emojis or other symbols (™, — (em dash), – (en dash), etc.) that fall outside of the ASCII range. Because of this, we'll remove useful apps if we use the function in its current form."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 42,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "False\n",
      "False\n",
      "8482\n",
      "128540\n"
     ]
    }
   ],
   "source": [
    "print(is_english('Docs To Go™ Free Office Suite'))\n",
    "print(is_english('Instachat 😜'))\n",
    "\n",
    "print(ord('™'))\n",
    "print(ord('😜'))"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Part Two\n",
    "To minimize the impact of data loss, we'll only remove an app if its name has more than three non-ASCII characters:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 43,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "True\n",
      "True\n"
     ]
    }
   ],
   "source": [
    "def is_english(string):\n",
    "    non_ascii = 0\n",
    "    \n",
    "    for character in string:\n",
    "        if ord(character) > 127:\n",
    "            non_ascii += 1\n",
    "            \n",
    "        if non_ascii > 3:\n",
    "            return False\n",
    "        else:\n",
    "            return True\n",
    "print(is_english('Docs To Go™ Free Office Suite'))\n",
    "print(is_english('Instachat 😜'))                "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The function is still not perfect, and very few non-English apps might get past our filter, but this seems good enough at this point in our analysis — we shouldn't spend too much time on optimization at this point.\n",
    "\n",
    "Below, we use the `is_english()` function to filter out the non-English apps for both data sets:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 44,
   "metadata": {
    "scrolled": false
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "['Photo Editor & Candy Camera & Grid & ScrapBook', 'ART_AND_DESIGN', '4.1', '159', '19M', '10,000+', 'Free', '0', 'Everyone', 'Art & Design', 'January 7, 2018', '1.0.0', '4.0.3 and up']\n",
      "\n",
      "\n",
      "['U Launcher Lite – FREE Live Cool Themes, Hide Apps', 'ART_AND_DESIGN', '4.7', '87510', '8.7M', '5,000,000+', 'Free', '0', 'Everyone', 'Art & Design', 'August 1, 2018', '1.2.4', '4.0.3 and up']\n",
      "\n",
      "\n",
      "['Sketch - Draw & Paint', 'ART_AND_DESIGN', '4.5', '215644', '25M', '50,000,000+', 'Free', '0', 'Teen', 'Art & Design', 'June 8, 2018', 'Varies with device', '4.2 and up']\n",
      "\n",
      "\n",
      "number of rows: 9659\n",
      "number of columns: 13\n",
      "\n",
      "\n",
      "['281656475', 'PAC-MAN Premium', '100788224', 'USD', '3.99', '21292', '26', '4', '4.5', '6.3.5', '4+', 'Games', '38', '5', '10', '1']\n",
      "\n",
      "\n",
      "['281796108', 'Evernote - stay organized', '158578688', 'USD', '0', '161065', '26', '4', '3.5', '8.2.2', '4+', 'Productivity', '37', '5', '23', '1']\n",
      "\n",
      "\n",
      "['281940292', 'WeatherBug - Local Weather, Radar, Maps, Alerts', '100524032', 'USD', '0', '188583', '2822', '3.5', '4.5', '5.0.0', '4+', 'Weather', '37', '5', '3', '1']\n",
      "\n",
      "\n",
      "number of rows: 7197\n",
      "number of columns: 16\n"
     ]
    }
   ],
   "source": [
    "android_english = []\n",
    "ios_english = []\n",
    "\n",
    "for app in android_clean:\n",
    "    name = app[0]\n",
    "    if is_english(name):\n",
    "        android_english.append(app)\n",
    "        \n",
    "for app in ios:\n",
    "    name = app[1]\n",
    "    if is_english(name):\n",
    "        ios_english.append(app)\n",
    "        \n",
    "explore_data(android_english, 0, 3, True)\n",
    "print('\\n')\n",
    "explore_data(ios_english, 0, 3, True)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We can see that we are left with 9659 Android apps and 7197 iOS apps."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Isolating the Free Apps\n",
    "As we mentioned in the introduction, we only build apps that are free to download and install, and our main source of revenue consists of in-app ads. Our data sets contain both free and non-free apps, and we'll need to isolate only the free apps for our analysis. Below, we isolate the free apps for both our data sets:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 49,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "8905\n",
      "4056\n"
     ]
    }
   ],
   "source": [
    "android_final = []\n",
    "ios_final = []\n",
    "\n",
    "for app in android_english:\n",
    "    price = app[7]\n",
    "    if price == '0':\n",
    "        android_final.append(app)\n",
    "        \n",
    "for app in ios_english:\n",
    "    price = app[4]\n",
    "    if price == '0':\n",
    "        ios_final.append(app)\n",
    "        \n",
    "print(len(android_final))\n",
    "print(len(ios_final)) "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We're left with 8905 Android apps and 4056 iOS apps, which should be enough for our analysis."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Most Common Apps by Genre\n",
    "## Part One\n",
    "As we mentioned in the introduction, our aim is to determine the kinds of apps that are likely to attract more users because our revenue is highly influenced by the number of people using our apps.\n",
    "\n",
    "To minimize risks and overhead, our validation strategy for an app idea is comprised of three steps:\n",
    "\n",
    "1. Build a minimal Android version of the app, and add it to Google Play.\n",
    "2. If the app has a good response from users, we then develop it further.\n",
    "3. If the app is profitable after six months, we also build an iOS version of the app and add it to the App Store.\n",
    "\n",
    "Because our end goal is to add the app on both the App Store and Google Play, we need to find app profiles that are successful on both markets. For instance, a profile that might work well for both markets might be a productivity app that makes use of gamification.\n",
    "\n",
    "Let's begin the analysis by getting a sense of the most common genres for each market. For this, we'll build a frequency table for the `prime_genre` column of the App Store data set, and the `Genres` and `Category` columns of the Google Play data set."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Part Two\n",
    "We'll build two functions we can use to analyze the frequency tables:\n",
    "\n",
    "* One function to generate frequency tables that show percentages\n",
    "* Another function that we can use to display the percentages in a descending order"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 53,
   "metadata": {},
   "outputs": [],
   "source": [
    "def freq_table(dataset, index):\n",
    "    table = {}\n",
    "    total = 0\n",
    "    \n",
    "    for row in dataset:\n",
    "        total += 1\n",
    "        value = row[index]\n",
    "        if value in table:\n",
    "            table[value] += 1\n",
    "        else:\n",
    "            table[value] = 1\n",
    "    table_percentages = {}\n",
    "    for key in table:\n",
    "        percentage = (table[key] / total) * 100\n",
    "        table_percentages[key] = percentage\n",
    "    return table_percentages\n",
    "\n",
    "def display_table(dataset, index):\n",
    "    table = freq_table(dataset, index)\n",
    "    table_display = []\n",
    "    for key in table:\n",
    "        key_val_as_tuple = (table[key], key)\n",
    "        table_display.append(key_val_as_tuple)\n",
    "    \n",
    "    table_sorted = sorted(table_display, reverse = True)\n",
    "    for entry in table_sorted:\n",
    "        print(entry[1], ':', entry[0])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Part Three\n",
    "We start by examining the frequency table for the prime_genre column of the App Store data set."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 54,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Games : 55.64595660749507\n",
      "Entertainment : 8.234714003944774\n",
      "Photo & Video : 4.117357001972387\n",
      "Social Networking : 3.5256410256410255\n",
      "Education : 3.2544378698224854\n",
      "Shopping : 2.983234714003945\n",
      "Utilities : 2.687376725838264\n",
      "Lifestyle : 2.3175542406311638\n",
      "Finance : 2.0710059171597637\n",
      "Sports : 1.947731755424063\n",
      "Health & Fitness : 1.8737672583826428\n",
      "Music : 1.6518737672583828\n",
      "Book : 1.6272189349112427\n",
      "Productivity : 1.5285996055226825\n",
      "News : 1.4299802761341223\n",
      "Travel : 1.3806706114398422\n",
      "Food & Drink : 1.0601577909270217\n",
      "Weather : 0.7642998027613412\n",
      "Reference : 0.4930966469428008\n",
      "Navigation : 0.4930966469428008\n",
      "Business : 0.4930966469428008\n",
      "Catalogs : 0.22189349112426035\n",
      "Medical : 0.19723865877712032\n"
     ]
    }
   ],
   "source": [
    "display_table(ios_final,-5)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We can see that among the free English apps, more than a half (55.64%) are games. Entertainment apps are slightly above 8%, followed by photo and video apps, which are 4.11%. Only 3.25% of the apps are designed for education, whereas social networking apps which amount for 3.52% of the apps in our data set.\n",
    "The general impression is that App Store (at least the part containing free English apps) is dominated by apps that are designed for fun (games, entertainment, photo and video, social networking, sports, music, etc.), while apps with practical purposes (education, shopping, utilities, productivity, lifestyle, etc.) are more rare. However, the fact that fun apps are the most numerous doesn't also imply that they also have the greatest number of users — the demand might not be the same as the offer.\n",
    "Let's continue by examining the Genres and Category columns of the Google Play data set (two columns which seem to be related)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 55,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "FAMILY : 18.97810218978102\n",
      "GAME : 9.70241437394722\n",
      "TOOLS : 8.433464345873105\n",
      "BUSINESS : 4.581695676586187\n",
      "LIFESTYLE : 3.9303761931499155\n",
      "PRODUCTIVITY : 3.885457608085345\n",
      "FINANCE : 3.6833239752947784\n",
      "MEDICAL : 3.5148792813026386\n",
      "SPORTS : 3.3801235261089273\n",
      "PERSONALIZATION : 3.312745648512072\n",
      "COMMUNICATION : 3.2341381246490735\n",
      "HEALTH_AND_FITNESS : 3.065693430656934\n",
      "PHOTOGRAPHY : 2.9421673217293653\n",
      "NEWS_AND_MAGAZINES : 2.829870859067939\n",
      "SOCIAL : 2.6501965188096577\n",
      "TRAVEL_AND_LOCAL : 2.3245367770915215\n",
      "SHOPPING : 2.2459292532285233\n",
      "BOOKS_AND_REFERENCE : 2.1785513756316677\n",
      "DATING : 1.8528916339135317\n",
      "VIDEO_PLAYERS : 1.7967434025828188\n",
      "MAPS_AND_NAVIGATION : 1.4149354295339696\n",
      "FOOD_AND_DRINK : 1.235261089275688\n",
      "EDUCATION : 1.167883211678832\n",
      "ENTERTAINMENT : 0.9545199326221224\n",
      "LIBRARIES_AND_DEMO : 0.9320606400898372\n",
      "AUTO_AND_VEHICLES : 0.9208309938236946\n",
      "HOUSE_AND_HOME : 0.8197641774284109\n",
      "WEATHER : 0.7973048848961257\n",
      "EVENTS : 0.7074677147669848\n",
      "PARENTING : 0.6513194834362718\n",
      "ART_AND_DESIGN : 0.6513194834362718\n",
      "COMICS : 0.6288601909039866\n",
      "BEAUTY : 0.5951712521055587\n"
     ]
    }
   ],
   "source": [
    "display_table(android_final, 1) # Category"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The landscape seems significantly different on Google Play: there are not that many apps designed for fun, and it seems that a good number of apps are designed for practical purposes (family, tools, business, lifestyle, productivity, etc.). However, if we investigate this further, we can see that the family category (which accounts for almost 19% of the apps) means mostly games for kids.\n",
    "Even so, practical apps seem to have a better representation on Google Play compared to App Store. This picture is also confirmed by the frequency table we see for the Genres column:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 56,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Tools : 8.422234699606962\n",
      "Entertainment : 6.086468276249298\n",
      "Education : 5.390230207748456\n",
      "Business : 4.581695676586187\n",
      "Lifestyle : 3.9191465468837734\n",
      "Productivity : 3.885457608085345\n",
      "Finance : 3.6833239752947784\n",
      "Medical : 3.5148792813026386\n",
      "Sports : 3.4475014037057834\n",
      "Personalization : 3.312745648512072\n",
      "Communication : 3.2341381246490735\n",
      "Action : 3.0881527231892196\n",
      "Health & Fitness : 3.065693430656934\n",
      "Photography : 2.9421673217293653\n",
      "News & Magazines : 2.829870859067939\n",
      "Social : 2.6501965188096577\n",
      "Travel & Local : 2.313307130825379\n",
      "Shopping : 2.2459292532285233\n",
      "Books & Reference : 2.1785513756316677\n",
      "Simulation : 2.0662549129702414\n",
      "Dating : 1.8528916339135317\n",
      "Arcade : 1.8416619876473892\n",
      "Video Players & Editors : 1.7742841100505335\n",
      "Casual : 1.7518248175182483\n",
      "Maps & Navigation : 1.4149354295339696\n",
      "Food & Drink : 1.235261089275688\n",
      "Puzzle : 1.1229646266142617\n",
      "Racing : 0.9882088714205502\n",
      "Role Playing : 0.9320606400898372\n",
      "Libraries & Demo : 0.9320606400898372\n",
      "Strategy : 0.9208309938236946\n",
      "Auto & Vehicles : 0.9208309938236946\n",
      "House & Home : 0.8197641774284109\n",
      "Weather : 0.7973048848961257\n",
      "Events : 0.7074677147669848\n",
      "Adventure : 0.6850084222346996\n",
      "Comics : 0.617630544637844\n",
      "Art & Design : 0.6064008983717013\n",
      "Beauty : 0.5951712521055587\n",
      "Parenting : 0.4941044357102751\n",
      "Card : 0.4491858506457047\n",
      "Trivia : 0.4267265581134195\n",
      "Casino : 0.4267265581134195\n",
      "Educational;Education : 0.39303761931499154\n",
      "Board : 0.38180797304884895\n",
      "Educational : 0.3705783267827063\n",
      "Education;Education : 0.3481190342504211\n",
      "Word : 0.2582818641212802\n",
      "Casual;Pretend Play : 0.23582257158899494\n",
      "Music : 0.2021336327905671\n",
      "Racing;Action & Adventure : 0.16844469399213924\n",
      "Puzzle;Brain Games : 0.16844469399213924\n",
      "Entertainment;Music & Video : 0.16844469399213924\n",
      "Casual;Brain Games : 0.13475575519371139\n",
      "Casual;Action & Adventure : 0.13475575519371139\n",
      "Arcade;Action & Adventure : 0.12352610892756878\n",
      "Action;Action & Adventure : 0.10106681639528355\n",
      "Educational;Pretend Play : 0.08983717012914093\n",
      "Simulation;Action & Adventure : 0.07860752386299832\n",
      "Parenting;Education : 0.07860752386299832\n",
      "Entertainment;Brain Games : 0.07860752386299832\n",
      "Board;Brain Games : 0.07860752386299832\n",
      "Parenting;Music & Video : 0.06737787759685569\n",
      "Educational;Brain Games : 0.06737787759685569\n",
      "Casual;Creativity : 0.06737787759685569\n",
      "Art & Design;Creativity : 0.06737787759685569\n",
      "Education;Pretend Play : 0.05614823133071309\n",
      "Role Playing;Pretend Play : 0.044918585064570464\n",
      "Education;Creativity : 0.044918585064570464\n",
      "Role Playing;Action & Adventure : 0.033688938798427846\n",
      "Puzzle;Action & Adventure : 0.033688938798427846\n",
      "Entertainment;Creativity : 0.033688938798427846\n",
      "Entertainment;Action & Adventure : 0.033688938798427846\n",
      "Educational;Creativity : 0.033688938798427846\n",
      "Educational;Action & Adventure : 0.033688938798427846\n",
      "Education;Music & Video : 0.033688938798427846\n",
      "Education;Brain Games : 0.033688938798427846\n",
      "Education;Action & Adventure : 0.033688938798427846\n",
      "Adventure;Action & Adventure : 0.033688938798427846\n",
      "Video Players & Editors;Music & Video : 0.022459292532285232\n",
      "Sports;Action & Adventure : 0.022459292532285232\n",
      "Simulation;Pretend Play : 0.022459292532285232\n",
      "Puzzle;Creativity : 0.022459292532285232\n",
      "Music;Music & Video : 0.022459292532285232\n",
      "Entertainment;Pretend Play : 0.022459292532285232\n",
      "Casual;Education : 0.022459292532285232\n",
      "Board;Action & Adventure : 0.022459292532285232\n",
      "Video Players & Editors;Creativity : 0.011229646266142616\n",
      "Trivia;Education : 0.011229646266142616\n",
      "Travel & Local;Action & Adventure : 0.011229646266142616\n",
      "Tools;Education : 0.011229646266142616\n",
      "Strategy;Education : 0.011229646266142616\n",
      "Strategy;Creativity : 0.011229646266142616\n",
      "Strategy;Action & Adventure : 0.011229646266142616\n",
      "Simulation;Education : 0.011229646266142616\n",
      "Role Playing;Brain Games : 0.011229646266142616\n",
      "Racing;Pretend Play : 0.011229646266142616\n",
      "Puzzle;Education : 0.011229646266142616\n",
      "Parenting;Brain Games : 0.011229646266142616\n",
      "Music & Audio;Music & Video : 0.011229646266142616\n",
      "Lifestyle;Pretend Play : 0.011229646266142616\n",
      "Lifestyle;Education : 0.011229646266142616\n",
      "Health & Fitness;Education : 0.011229646266142616\n",
      "Health & Fitness;Action & Adventure : 0.011229646266142616\n",
      "Entertainment;Education : 0.011229646266142616\n",
      "Communication;Creativity : 0.011229646266142616\n",
      "Comics;Creativity : 0.011229646266142616\n",
      "Casual;Music & Video : 0.011229646266142616\n",
      "Card;Action & Adventure : 0.011229646266142616\n",
      "Books & Reference;Education : 0.011229646266142616\n",
      "Art & Design;Pretend Play : 0.011229646266142616\n",
      "Art & Design;Action & Adventure : 0.011229646266142616\n",
      "Arcade;Pretend Play : 0.011229646266142616\n",
      "Adventure;Education : 0.011229646266142616\n"
     ]
    }
   ],
   "source": [
    "display_table(android_final, -4)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The difference between the Genres and the Category columns is not crystal clear, but one thing we can notice is that the Genres column is much more granular (it has more categories). We're only looking for the bigger picture at the moment, so we'll only work with the Category column moving forward.\n",
    "Up to this point, we found that the App Store is dominated by apps designed for fun, while Google Play shows a more balanced landscape of both practical and for-fun apps. Now we'd like to get an idea about the kind of apps that have most users."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Most Popular Apps by Genre on the App Store\n",
    "One way to find out what genres are the most popular (have the most users) is to calculate the average number of installs for each app genre. For the Google Play data set, we can find this information in the `Installs` column, but for the App Store data set this information is missing. As a workaround, we'll take the total number of user ratings as a proxy, which we can find in the `rating_count_tot` app.\n",
    "\n",
    "Below, we calculate the average number of user ratings per app genre on the App Store:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 57,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Games : 18924.68896765618\n",
      "Productivity : 19053.887096774193\n",
      "Weather : 47220.93548387097\n",
      "Shopping : 18746.677685950413\n",
      "Reference : 67447.9\n",
      "Finance : 13522.261904761905\n",
      "Music : 56482.02985074627\n",
      "Utilities : 14010.100917431193\n",
      "Travel : 20216.01785714286\n",
      "Social Networking : 53078.195804195806\n",
      "Sports : 20128.974683544304\n",
      "Business : 6367.8\n",
      "Health & Fitness : 19952.315789473683\n",
      "Entertainment : 10822.961077844311\n",
      "Photo & Video : 27249.892215568863\n",
      "Navigation : 25972.05\n",
      "Education : 6266.333333333333\n",
      "Lifestyle : 8978.308510638299\n",
      "Food & Drink : 20179.093023255813\n",
      "News : 15892.724137931034\n",
      "Book : 8498.333333333334\n",
      "Medical : 459.75\n",
      "Catalogs : 1779.5555555555557\n"
     ]
    }
   ],
   "source": [
    "genres_ios = freq_table(ios, -5)\n",
    "\n",
    "for genre in genres_ios:\n",
    "    total = 0\n",
    "    len_genre = 0\n",
    "    for app in ios_final:\n",
    "        genre_app = app[-5]\n",
    "        if genre_app == genre:\n",
    "            n_ratings = float(app[5])\n",
    "            total += n_ratings\n",
    "            len_genre += 1\n",
    "    avg_n_ratings = total / len_genre\n",
    "    print(genre, ':', avg_n_ratings)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "On average, Reference apps have the highest number of user reviews, but this figure is heavily influenced by Bible and Dictionary, which have close to half a million user reviews together:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 59,
   "metadata": {
    "scrolled": true
   },
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Bible : 985920\n",
      "Dictionary.com Dictionary & Thesaurus : 200047\n",
      "Dictionary.com Dictionary & Thesaurus for iPad : 54175\n",
      "Muslim Pro: Ramadan 2017 Prayer Times, Azan, Quran : 18418\n",
      "Merriam-Webster Dictionary : 16849\n",
      "Google Translate : 26786\n",
      "Night Sky : 12122\n",
      "WWDC : 762\n",
      "Jishokun-Japanese English Dictionary & Translator : 0\n",
      "教えて!goo : 0\n",
      "VPN Express : 14\n",
      "彩库宝典-【官方版】 : 0\n",
      "New Furniture Mods - Pocket Wiki & Game Tools for Minecraft PC Edition : 17588\n",
      "LUCKY BLOCK MOD ™ for Minecraft PC Edition - The Best Pocket Wiki & Mods Installer Tools : 4693\n",
      "Guides for Pokémon GO - Pokemon GO News and Cheats : 826\n",
      "Horror Maps for Minecraft PE - Download The Scariest Maps for Minecraft Pocket Edition (MCPE) Free : 718\n",
      "City Maps for Minecraft PE - The Best Maps for Minecraft Pocket Edition (MCPE) : 8535\n",
      "GUNS MODS for Minecraft PC Edition - Mods Tools : 1497\n",
      "Real Bike Traffic Rider Virtual Reality Glasses : 8\n",
      "無料で音楽や写真・カメラの裏技アプリ for iPhone7 : 0\n"
     ]
    }
   ],
   "source": [
    "for app in ios_final:\n",
    "    if app[-5] == 'Reference':\n",
    "        print(app[1], ':', app[5]) # print name and number of ratings"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "However, this niche seems to show some potential. One thing we could do is take another popular book and turn it into an app where we could add different features besides the raw version of the book. This might include daily quotes from the book, an audio version of the book, quizzes about the book, etc. On top of that, we could also embed a dictionary within the app, so users don't need to exit our app to look up words in an external app."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "\n",
    "The same pattern applies to social networking apps, where the average number is heavily influenced by a few giants like Facebook, Pinterest, Skype, etc. Same applies to photo & video apps, where a few big players like Instagram, Snapchat, Youtube heavily influence the average number.\n",
    "\n",
    "Our aim is to find popular genres, but Reference, social networking or music apps might seem more popular than they really are. The average number of ratings seem to be skewed by very few apps which have hundreds of thousands of user ratings, while the other apps may struggle to get past the 10,000 threshold. We could get a better picture by removing these extremely popular apps for each genre and then rework the averages, but we'll leave this level of detail for later.\n",
    "\n",
    "Music apps have 56,482 user ratings on average, but it's actually the Pandora and Spotify which skew up the average rating:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 60,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Pandora - Music & Radio : 1126879\n",
      "Shazam - Discover music, artists, videos & lyrics : 402925\n",
      "iHeartRadio – Free Music & Radio Stations : 293228\n",
      "Deezer - Listen to your Favorite Music & Playlists : 4677\n",
      "Sonos Controller : 48905\n",
      "NRJ Radio : 38\n",
      "radio.de - Der Radioplayer : 64\n",
      "Spotify Music : 878563\n",
      "SoundCloud - Music & Audio : 135744\n",
      "Sing Karaoke Songs Unlimited with StarMaker : 26227\n",
      "SoundHound Song Search & Music Player : 82602\n",
      "Ringtones for iPhone & Ringtone Maker : 25403\n",
      "Coach Guitar - Lessons & Easy Tabs For Beginners : 2416\n",
      "QQ音乐-来这里“发现・音乐” : 745\n",
      "TuneIn Radio - MLB NBA Audiobooks Podcasts Music : 110420\n",
      "Magic Piano by Smule : 131695\n",
      "QQ音乐HD : 224\n",
      "The Singing Machine Mobile Karaoke App : 130\n",
      "Bandsintown Concerts : 30845\n",
      "PetitLyrics : 0\n",
      "edjing Mix:DJ turntable to remix and scratch music : 13580\n",
      "Smule Sing! : 119316\n",
      "Amazon Music : 106235\n",
      "AutoRap by Smule : 18202\n",
      "My Mixtapez Music : 26286\n",
      "Certified Mixtapes - Hip Hop Albums & Mixtapes : 9975\n",
      "Karaoke - Sing Karaoke, Unlimited Songs! : 28606\n",
      "Napster - Top Music & Radio : 14268\n",
      "Musi - Unlimited Music For YouTube : 25193\n",
      "UE BOOM : 612\n",
      "Spinrilla - Mixtapes For Free : 15053\n",
      "Google Play Music : 10118\n",
      "Piano - Play Keyboard Music Games with Magic Tiles : 1636\n",
      "Bose SoundTouch : 3687\n",
      "DatPiff : 2815\n",
      "Sounds app - Music And Friends : 5126\n",
      "Smart Music: Streaming Videos and Radio : 17\n",
      "Free Piano app by Yokee : 13016\n",
      "Simple Radio - Live AM & FM Radio Stations : 4787\n",
      "Trebel Music - Unlimited Music Downloader : 2570\n",
      "TIDAL : 7398\n",
      "Acapella from PicPlayPost : 2487\n",
      "Medly - Music Maker : 933\n",
      "Amazon Alexa : 3018\n",
      "Music Freedom - Unlimited Free MP3 Music Streaming : 1246\n",
      "PlayGround • Music At Your Fingertips : 150\n",
      "Musical Video Maker - Create Music clips lip sync : 320\n",
      "Free Music Play - Mp3 Streamer & Player : 2496\n",
      "LiveMixtapes : 555\n",
      "AmpMe - A Portable Social Party Music Speaker : 1047\n",
      "NOISE : 355\n",
      "YouTube Music : 7109\n",
      "Ringtones for iPhone with Ringtone Maker : 4013\n",
      "Music Memos : 909\n",
      "Musicloud - MP3 and FLAC Music Player for Cloud Platforms. : 2211\n",
      "Bose Connect : 915\n",
      "Cloud Music Player - Downloader & Playlist Manager : 319\n",
      "Remixlive - Remix loops with pads : 288\n",
      "Free Music -  Player & Streamer  for Dropbox, OneDrive & Google Drive : 46\n",
      "Boom: Best Equalizer & Magical Surround Sound : 1375\n",
      "MP3 Music Player & Streamer for Clouds : 329\n",
      "Nicki Minaj: The Empire : 5196\n",
      "SongFlip - Free Music Streamer : 5004\n",
      "Blocs Wave - Make & Record Music : 158\n",
      "Music and Chill : 135\n",
      "Free Music - MP3 Streamer & Playlist Manager Pro : 13443\n",
      "BOSS Tuner : 13\n"
     ]
    }
   ],
   "source": [
    "for app in ios_final:\n",
    "    if app[-5] == 'Music':\n",
    "        print(app[1], ':', app[5])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This idea seems to fit well with the fact that the App Store is dominated by practical apps. This suggests the market might be a bit saturated with pratical apps, which means a for-fun app might have more of a chance to stand out among the huge number of apps on the App Store.\n",
    "\n",
    "Other genres that seem popular include weather, games, sports,food & drink or Finance. The games genre seem to overlap a bit with the app idea we described above, but the other genres don't seem too interesting to us:\n",
    "\n",
    "* Weather apps — people generally don't spend too much time in-app, and the chances of making profit from in-app adds are low. Also, getting reliable live weather data may require us to connect our apps to non-free APIs.\n",
    "* Food and drink — examples here include Starbucks, Dunkin' Donuts, McDonald's, etc. So making a popular food and drink app requires actual cooking and a delivery service, which is outside the scope of our company.\n",
    "* Finance apps — these apps involve banking, paying bills, money transfer, etc. Building a finance app requires domain knowledge, and we don't want to hire a finance expert just to build an app.\n",
    "\n",
    "Now let's analyze the Google Play market a bit."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Most Popular Apps by Genre on Google Play\n",
    "For the Google Play market, we actually have data about the number of installs, so we should be able to get a clearer picture about genre popularity. However, the install numbers don't seem precise enough — we can see that most values are open-ended (100+, 1,000+, 5,000+, etc.):"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 61,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "1,000,000+ : 15.687815833801237\n",
      "100,000+ : 11.577765300393038\n",
      "10,000,000+ : 10.499719258843346\n",
      "10,000+ : 10.252667040988209\n",
      "1,000+ : 8.422234699606962\n",
      "100+ : 6.917462099943853\n",
      "5,000,000+ : 6.816395283548568\n",
      "500,000+ : 5.53621560920831\n",
      "50,000+ : 4.817518248175182\n",
      "5,000+ : 4.525547445255475\n",
      "10+ : 3.537338573834924\n",
      "500+ : 3.2341381246490735\n",
      "50,000,000+ : 2.2908478382930935\n",
      "100,000,000+ : 2.1224031443009546\n",
      "50+ : 1.9090398652442448\n",
      "5+ : 0.7860752386299831\n",
      "1+ : 0.5165637282425604\n",
      "500,000,000+ : 0.26951151038742277\n",
      "1,000,000,000+ : 0.22459292532285235\n",
      "0+ : 0.044918585064570464\n",
      "0 : 0.011229646266142616\n"
     ]
    }
   ],
   "source": [
    "display_table(android_final, 5) # the Installs columns"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "One problem with this data is that is not precise. For instance, we don't know whether an app with 100,000+ installs has 100,000 installs, 200,000, or 350,000. However, we don't need very precise data for our purposes — we only want to get an idea which app genres attract the most users, and we don't need perfect precision with respect to the number of users.\n",
    "\n",
    "We're going to leave the numbers as they are, which means that we'll consider that an app with 100,000+ installs has 100,000 installs, and an app with 1,000,000+ installs has 1,000,000 installs, and so on.\n",
    "\n",
    "To perform computations, however, we'll need to convert each install number to float — this means that we need to remove the commas and the plus characters, otherwise the conversion will fail and raise an error. We'll do this directly in the loop below, where we also compute the average number of installs for each genre (category)."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 62,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "ART_AND_DESIGN : 1952105.1724137932\n",
      "AUTO_AND_VEHICLES : 647317.8170731707\n",
      "BEAUTY : 513151.88679245283\n",
      "BOOKS_AND_REFERENCE : 8587351.855670104\n",
      "BUSINESS : 1708215.906862745\n",
      "COMICS : 803234.8214285715\n",
      "COMMUNICATION : 38322625.697916664\n",
      "DATING : 854028.8303030303\n",
      "EDUCATION : 1825480.7692307692\n",
      "ENTERTAINMENT : 11640705.88235294\n",
      "EVENTS : 253542.22222222222\n",
      "FINANCE : 1387692.475609756\n",
      "FOOD_AND_DRINK : 1924897.7363636363\n",
      "HEALTH_AND_FITNESS : 4188821.9853479853\n",
      "HOUSE_AND_HOME : 1331540.5616438356\n",
      "LIBRARIES_AND_DEMO : 638503.734939759\n",
      "LIFESTYLE : 1436126.94\n",
      "GAME : 15551995.891203703\n",
      "FAMILY : 3668870.823076923\n",
      "MEDICAL : 120550.61980830671\n",
      "SOCIAL : 23253652.127118643\n",
      "SHOPPING : 7001693.425\n",
      "PHOTOGRAPHY : 17772018.759541985\n",
      "SPORTS : 3638640.1428571427\n",
      "TRAVEL_AND_LOCAL : 13984077.710144928\n",
      "TOOLS : 10787009.952063914\n",
      "PERSONALIZATION : 5183850.806779661\n",
      "PRODUCTIVITY : 16738957.554913295\n",
      "PARENTING : 542603.6206896552\n",
      "WEATHER : 5074486.197183099\n",
      "VIDEO_PLAYERS : 24573948.25\n",
      "NEWS_AND_MAGAZINES : 9401635.952380951\n",
      "MAPS_AND_NAVIGATION : 3993339.603174603\n"
     ]
    }
   ],
   "source": [
    "categories_android = freq_table(android_final, 1)\n",
    "for category in categories_android:\n",
    "    total = 0\n",
    "    len_category = 0\n",
    "    for app in android_final:\n",
    "        category_app = app[1]\n",
    "        if category_app == category:\n",
    "            n_installs = app[5]\n",
    "            n_installs = n_installs.replace(',', '')\n",
    "            n_installs = n_installs.replace('+', '')\n",
    "            total += float(n_installs)\n",
    "            len_category += 1\n",
    "    avg_n_installs = total / len_category\n",
    "    print(category, ':', avg_n_installs)         "
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "On average, communication apps have the most installs: 38,322,625. This number is heavily skewed up by a few apps that have over one billion installs (WhatsApp, Facebook Messenger, Skype, Google Chrome, Gmail, and Hangouts), and a few others with over 100 and 500 million installs:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 63,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "WhatsApp Messenger : 1,000,000,000+\n",
      "imo beta free calls and text : 100,000,000+\n",
      "Android Messages : 100,000,000+\n",
      "Google Duo - High Quality Video Calls : 500,000,000+\n",
      "Messenger – Text and Video Chat for Free : 1,000,000,000+\n",
      "imo free video calls and chat : 500,000,000+\n",
      "Skype - free IM & video calls : 1,000,000,000+\n",
      "Who : 100,000,000+\n",
      "GO SMS Pro - Messenger, Free Themes, Emoji : 100,000,000+\n",
      "LINE: Free Calls & Messages : 500,000,000+\n",
      "Google Chrome: Fast & Secure : 1,000,000,000+\n",
      "Firefox Browser fast & private : 100,000,000+\n",
      "UC Browser - Fast Download Private & Secure : 500,000,000+\n",
      "Gmail : 1,000,000,000+\n",
      "Hangouts : 1,000,000,000+\n",
      "Messenger Lite: Free Calls & Messages : 100,000,000+\n",
      "Kik : 100,000,000+\n",
      "KakaoTalk: Free Calls & Text : 100,000,000+\n",
      "Opera Mini - fast web browser : 100,000,000+\n",
      "Opera Browser: Fast and Secure : 100,000,000+\n",
      "Telegram : 100,000,000+\n",
      "Truecaller: Caller ID, SMS spam blocking & Dialer : 100,000,000+\n",
      "UC Browser Mini -Tiny Fast Private & Secure : 100,000,000+\n",
      "Viber Messenger : 500,000,000+\n",
      "WeChat : 100,000,000+\n",
      "Yahoo Mail – Stay Organized : 100,000,000+\n",
      "BBM - Free Calls & Messages : 100,000,000+\n"
     ]
    }
   ],
   "source": [
    "for app in android_final:\n",
    "    if app[1] == 'COMMUNICATION' and (app[5] == '1,000,000,000+'\n",
    "                                      or app[5] == '500,000,000+'\n",
    "                                      or app[5] == '100,000,000+'):\n",
    "        print(app[0], ':', app[5])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "If we removed all the communication apps that have over 100 million installs, the average would be reduced roughly ten times:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 66,
   "metadata": {},
   "outputs": [
    {
     "data": {
      "text/plain": [
       "3589717.245210728"
      ]
     },
     "execution_count": 66,
     "metadata": {},
     "output_type": "execute_result"
    }
   ],
   "source": [
    "under_100m = []\n",
    "\n",
    "for app in android_final:\n",
    "    n_installs = app[5]\n",
    "    n_installs = n_installs.replace(',', '')\n",
    "    n_installs = n_installs.replace('+', '')\n",
    "    if (app[1] == 'COMMUNICATION') and (float(n_installs) < 100000000):\n",
    "        under_100m.append(float(n_installs))\n",
    "\n",
    "sum(under_100m) / len(under_100m)"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "We see the same pattern for the video players category, which is the winner with 55,088,19 installs. The market is dominated by apps like Youtube, Google Play Movies & TV, or MX Player. The pattern is repeated for social apps (where we have giants like Facebook, Instagram, Google+, etc.), photography apps (Google Photos and other popular photo editors), or productivity apps (Microsoft Word, Dropbox, Google Calendar, Evernote, etc.).\n",
    "\n",
    "Again, the main concern is that these app genres might seem more popular than they really are. Moreover, these niches seem to be dominated by a few giants who are hard to compete against.\n",
    "\n",
    "The game genre seems pretty popular, but previously we found out this part of the market seems a bit saturated, so we'd like to come up with a different app recommendation if possible.\n",
    "\n",
    "The books and reference genre looks fairly popular as well, with an average number of installs of 8,767,811. It's interesting to explore this in more depth, since we found this genre has some potential to work well on the App Store, and our aim is to recommend an app genre that shows potential for being profitable on both the App Store and Google Play.\n",
    "\n",
    "Let's take a look at some of the apps from this genre and their number of installs:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 67,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "E-Book Read - Read Book for free : 50,000+\n",
      "Download free book with green book : 100,000+\n",
      "Wikipedia : 10,000,000+\n",
      "Cool Reader : 10,000,000+\n",
      "Free Panda Radio Music : 100,000+\n",
      "Book store : 1,000,000+\n",
      "FBReader: Favorite Book Reader : 10,000,000+\n",
      "English Grammar Complete Handbook : 500,000+\n",
      "Free Books - Spirit Fanfiction and Stories : 1,000,000+\n",
      "Google Play Books : 1,000,000,000+\n",
      "AlReader -any text book reader : 5,000,000+\n",
      "Offline English Dictionary : 100,000+\n",
      "Offline: English to Tagalog Dictionary : 500,000+\n",
      "FamilySearch Tree : 1,000,000+\n",
      "Cloud of Books : 1,000,000+\n",
      "Recipes of Prophetic Medicine for free : 500,000+\n",
      "ReadEra – free ebook reader : 1,000,000+\n",
      "Anonymous caller detection : 10,000+\n",
      "Ebook Reader : 5,000,000+\n",
      "Litnet - E-books : 100,000+\n",
      "Read books online : 5,000,000+\n",
      "English to Urdu Dictionary : 500,000+\n",
      "eBoox: book reader fb2 epub zip : 1,000,000+\n",
      "English Persian Dictionary : 500,000+\n",
      "Flybook : 500,000+\n",
      "All Maths Formulas : 1,000,000+\n",
      "Ancestry : 5,000,000+\n",
      "HTC Help : 10,000,000+\n",
      "English translation from Bengali : 100,000+\n",
      "Pdf Book Download - Read Pdf Book : 100,000+\n",
      "Free Book Reader : 100,000+\n",
      "eBoox new: Reader for fb2 epub zip books : 50,000+\n",
      "Only 30 days in English, the guideline is guaranteed : 500,000+\n",
      "Moon+ Reader : 10,000,000+\n",
      "SH-02J Owner's Manual (Android 8.0) : 50,000+\n",
      "English-Myanmar Dictionary : 1,000,000+\n",
      "Golden Dictionary (EN-AR) : 1,000,000+\n",
      "All Language Translator Free : 1,000,000+\n",
      "Azpen eReader : 500,000+\n",
      "URBANO V 02 instruction manual : 100,000+\n",
      "Bible : 100,000,000+\n",
      "C Programs and Reference : 50,000+\n",
      "C Offline Tutorial : 1,000+\n",
      "C Programs Handbook : 50,000+\n",
      "Amazon Kindle : 100,000,000+\n",
      "Aab e Hayat Full Novel : 100,000+\n",
      "Aldiko Book Reader : 10,000,000+\n",
      "Google I/O 2018 : 500,000+\n",
      "R Language Reference Guide : 10,000+\n",
      "Learn R Programming Full : 5,000+\n",
      "R Programing Offline Tutorial : 1,000+\n",
      "Guide for R Programming : 5+\n",
      "Learn R Programming : 10+\n",
      "R Quick Reference Big Data : 1,000+\n",
      "V Made : 100,000+\n",
      "Wattpad 📖 Free Books : 100,000,000+\n",
      "Dictionary - WordWeb : 5,000,000+\n",
      "Guide (for X-MEN) : 100,000+\n",
      "AC Air condition Troubleshoot,Repair,Maintenance : 5,000+\n",
      "AE Bulletins : 1,000+\n",
      "Ae Allah na Dai (Rasa) : 10,000+\n",
      "50000 Free eBooks & Free AudioBooks : 5,000,000+\n",
      "Ag PhD Field Guide : 10,000+\n",
      "Ag PhD Deficiencies : 10,000+\n",
      "Ag PhD Planting Population Calculator : 1,000+\n",
      "Ag PhD Soybean Diseases : 1,000+\n",
      "Fertilizer Removal By Crop : 50,000+\n",
      "A-J Media Vault : 50+\n",
      "Al-Quran (Free) : 10,000,000+\n",
      "Al Quran (Tafsir & by Word) : 500,000+\n",
      "Al Quran Indonesia : 10,000,000+\n",
      "Al'Quran Bahasa Indonesia : 10,000,000+\n",
      "Al Quran Al karim : 1,000,000+\n",
      "Al-Muhaffiz : 50,000+\n",
      "Al Quran : EAlim - Translations & MP3 Offline : 5,000,000+\n",
      "Al-Quran 30 Juz free copies : 500,000+\n",
      "Koran Read &MP3 30 Juz Offline : 1,000,000+\n",
      "Hafizi Quran 15 lines per page : 1,000,000+\n",
      "Quran for Android : 10,000,000+\n",
      "Al Quran Free - القرآن (Islam) : 50,000+\n",
      "Surah Al-Waqiah : 100,000+\n",
      "Hisnul Al Muslim - Hisn Invocations & Adhkaar : 100,000+\n",
      "Satellite AR : 1,000,000+\n",
      "Audiobooks from Audible : 100,000,000+\n",
      "日本AV历史 : 10,000+\n",
      "Kinot & Eichah for Tisha B'Av : 10,000+\n",
      "AW Tozer Devotionals - Daily : 5,000+\n",
      "Tozer Devotional -Series 1 : 1,000+\n",
      "The Pursuit of God : 1,000+\n",
      "AY Sing : 5,000+\n",
      "Ay Hasnain k Nana Milad Naat : 10,000+\n",
      "Ay Mohabbat Teri Khatir Novel : 10,000+\n",
      "Arizona Statutes, ARS (AZ Law) : 1,000+\n",
      "Oxford A-Z of English Usage : 1,000,000+\n",
      "BD Fishpedia : 1,000+\n",
      "BD All Sim Offer : 10,000+\n",
      "Youboox - Livres, BD et magazines : 500,000+\n",
      "Cъновник BG : 1,000+\n",
      "B&H Kids AR : 10,000+\n",
      "B y H Niños ES : 5,000+\n",
      "Dictionary.com: Find Definitions for English Words : 10,000,000+\n",
      "English Dictionary - Offline : 10,000,000+\n",
      "Bible KJV : 5,000,000+\n",
      "Borneo Bible, BM Bible : 10,000+\n",
      "MOD Black for BM : 100+\n",
      "BM Box : 1,000+\n",
      "Anime Mod for BM : 100+\n",
      "NOOK: Read eBooks & Magazines : 10,000,000+\n",
      "NOOK Audiobooks : 500,000+\n",
      "NOOK App for NOOK Devices : 500,000+\n",
      "Browsery by Barnes & Noble : 5,000+\n",
      "bp e-store : 1,000+\n",
      "Brilliant Quotes: Life, Love, Family & Motivation : 1,000,000+\n",
      "BR Ambedkar Biography & Quotes : 10,000+\n",
      "BU Alsace : 100+\n",
      "Catholic La Bu Zo Kam : 500+\n",
      "Khrifa Hla Bu (Solfa) : 10+\n",
      "Kristian Hla Bu : 10,000+\n",
      "SA HLA BU : 1,000+\n",
      "Learn SAP BW : 500+\n",
      "Learn SAP BW on HANA : 500+\n",
      "CA Laws 2018 (California Laws and Codes) : 5,000+\n",
      "Bootable Methods(USB-CD-DVD) : 10,000+\n",
      "cloudLibrary : 100,000+\n",
      "SDA Collegiate Quarterly : 500+\n",
      "Sabbath School : 100,000+\n",
      "Cypress College Library : 100+\n",
      "Stats Royale for Clash Royale : 1,000,000+\n",
      "GATE 21 years CS Papers(2011-2018 Solved) : 50+\n",
      "Learn CT Scan Of Head : 5,000+\n",
      "Easy Cv maker 2018 : 10,000+\n",
      "How to Write CV : 100,000+\n",
      "CW Nuclear : 1,000+\n",
      "CY Spray nozzle : 10+\n",
      "BibleRead En Cy Zh Yue : 5+\n",
      "CZ-Help : 5+\n",
      "Modlitební knížka CZ : 500+\n",
      "Guide for DB Xenoverse : 10,000+\n",
      "Guide for DB Xenoverse 2 : 10,000+\n",
      "Guide for IMS DB : 10+\n",
      "DC HSEMA : 5,000+\n",
      "DC Public Library : 1,000+\n",
      "Painting Lulu DC Super Friends : 1,000+\n",
      "Dictionary : 10,000,000+\n",
      "Fix Error Google Playstore : 1,000+\n",
      "D. H. Lawrence Poems FREE : 1,000+\n",
      "Bilingual Dictionary Audio App : 5,000+\n",
      "DM Screen : 10,000+\n",
      "wikiHow: how to do anything : 1,000,000+\n",
      "Dr. Doug's Tips : 1,000+\n",
      "Bible du Semeur-BDS (French) : 50,000+\n",
      "La citadelle du musulman : 50,000+\n",
      "DV 2019 Entry Guide : 10,000+\n",
      "DV 2019 - EDV Photo & Form : 50,000+\n",
      "DV 2018 Winners Guide : 1,000+\n",
      "EB Annual Meetings : 1,000+\n",
      "EC - AP & Telangana : 5,000+\n",
      "TN Patta Citta & EC : 10,000+\n",
      "AP Stamps and Registration : 10,000+\n",
      "CompactiMa EC pH Calibration : 100+\n",
      "EGW Writings 2 : 100,000+\n",
      "EGW Writings : 1,000,000+\n",
      "Bible with EGW Comments : 100,000+\n",
      "My Little Pony AR Guide : 1,000,000+\n",
      "SDA Sabbath School Quarterly : 500,000+\n",
      "Duaa Ek Ibaadat : 5,000+\n",
      "Spanish English Translator : 10,000,000+\n",
      "Dictionary - Merriam-Webster : 10,000,000+\n",
      "JW Library : 10,000,000+\n",
      "Oxford Dictionary of English : Free : 10,000,000+\n",
      "English Hindi Dictionary : 10,000,000+\n",
      "English to Hindi Dictionary : 5,000,000+\n",
      "EP Research Service : 1,000+\n",
      "FAHREDDİN er-RÂZİ TEFSİRİ : 1,000+\n",
      "Hymnes et Louanges : 100,000+\n",
      "EU Charter : 1,000+\n",
      "EU Data Protection : 1,000+\n",
      "EU IP Codes : 100+\n",
      "EW PDF : 5+\n",
      "BakaReader EX : 100,000+\n",
      "EZ Quran : 50,000+\n",
      "FA Part 1 & 2 Past Papers Solved Free – Offline : 5,000+\n",
      "La Fe de Jesus : 1,000+\n",
      "La Fe de Jesús : 500+\n",
      "Le Fe de Jesus : 500+\n",
      "Florida - Pocket Brainbook : 1,000+\n",
      "Florida Statutes (FL Code) : 1,000+\n",
      "English To Shona Dictionary : 10,000+\n",
      "Greek Bible FP (Audio) : 1,000+\n",
      "Golden Dictionary (FR-AR) : 500,000+\n",
      "Fanfic-FR : 5,000+\n",
      "Bulgarian French Dictionary Fr : 10,000+\n",
      "Chemin (fr) : 1,000+\n",
      "The SCP Foundation DB fr nn5n : 1,000+\n"
     ]
    }
   ],
   "source": [
    "for app in android_final:\n",
    "    if app[1] == 'BOOKS_AND_REFERENCE':\n",
    "        print(app[0], ':', app[5])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "The book and reference genre includes a variety of apps: software for processing and reading ebooks, various collections of libraries, dictionaries, tutorials on programming or languages, etc. It seems there's still a small number of extremely popular apps that skew the average:"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 68,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Google Play Books : 1,000,000,000+\n",
      "Bible : 100,000,000+\n",
      "Amazon Kindle : 100,000,000+\n",
      "Wattpad 📖 Free Books : 100,000,000+\n",
      "Audiobooks from Audible : 100,000,000+\n"
     ]
    }
   ],
   "source": [
    "for app in android_final:\n",
    "    if app[1] == 'BOOKS_AND_REFERENCE' and (app[5] == '1,000,000,000+'\n",
    "                                            or app[5] == '500,000,000+'\n",
    "                                            or app[5] == '100,000,000+'):\n",
    "        print(app[0], ':', app[5])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "However, it looks like there are only a few very popular apps, so this market still shows potential. Let's try to get some app ideas based on the kind of apps that are somewhere in the middle in terms of popularity (between 1,000,000 and 100,000,000 downloads):"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": 69,
   "metadata": {},
   "outputs": [
    {
     "name": "stdout",
     "output_type": "stream",
     "text": [
      "Wikipedia : 10,000,000+\n",
      "Cool Reader : 10,000,000+\n",
      "Book store : 1,000,000+\n",
      "FBReader: Favorite Book Reader : 10,000,000+\n",
      "Free Books - Spirit Fanfiction and Stories : 1,000,000+\n",
      "AlReader -any text book reader : 5,000,000+\n",
      "FamilySearch Tree : 1,000,000+\n",
      "Cloud of Books : 1,000,000+\n",
      "ReadEra – free ebook reader : 1,000,000+\n",
      "Ebook Reader : 5,000,000+\n",
      "Read books online : 5,000,000+\n",
      "eBoox: book reader fb2 epub zip : 1,000,000+\n",
      "All Maths Formulas : 1,000,000+\n",
      "Ancestry : 5,000,000+\n",
      "HTC Help : 10,000,000+\n",
      "Moon+ Reader : 10,000,000+\n",
      "English-Myanmar Dictionary : 1,000,000+\n",
      "Golden Dictionary (EN-AR) : 1,000,000+\n",
      "All Language Translator Free : 1,000,000+\n",
      "Aldiko Book Reader : 10,000,000+\n",
      "Dictionary - WordWeb : 5,000,000+\n",
      "50000 Free eBooks & Free AudioBooks : 5,000,000+\n",
      "Al-Quran (Free) : 10,000,000+\n",
      "Al Quran Indonesia : 10,000,000+\n",
      "Al'Quran Bahasa Indonesia : 10,000,000+\n",
      "Al Quran Al karim : 1,000,000+\n",
      "Al Quran : EAlim - Translations & MP3 Offline : 5,000,000+\n",
      "Koran Read &MP3 30 Juz Offline : 1,000,000+\n",
      "Hafizi Quran 15 lines per page : 1,000,000+\n",
      "Quran for Android : 10,000,000+\n",
      "Satellite AR : 1,000,000+\n",
      "Oxford A-Z of English Usage : 1,000,000+\n",
      "Dictionary.com: Find Definitions for English Words : 10,000,000+\n",
      "English Dictionary - Offline : 10,000,000+\n",
      "Bible KJV : 5,000,000+\n",
      "NOOK: Read eBooks & Magazines : 10,000,000+\n",
      "Brilliant Quotes: Life, Love, Family & Motivation : 1,000,000+\n",
      "Stats Royale for Clash Royale : 1,000,000+\n",
      "Dictionary : 10,000,000+\n",
      "wikiHow: how to do anything : 1,000,000+\n",
      "EGW Writings : 1,000,000+\n",
      "My Little Pony AR Guide : 1,000,000+\n",
      "Spanish English Translator : 10,000,000+\n",
      "Dictionary - Merriam-Webster : 10,000,000+\n",
      "JW Library : 10,000,000+\n",
      "Oxford Dictionary of English : Free : 10,000,000+\n",
      "English Hindi Dictionary : 10,000,000+\n",
      "English to Hindi Dictionary : 5,000,000+\n"
     ]
    }
   ],
   "source": [
    "for app in android_final:\n",
    "    if app[1] == 'BOOKS_AND_REFERENCE' and (app[5] == '1,000,000+'\n",
    "                                            or app[5] == '5,000,000+'\n",
    "                                            or app[5] == '10,000,000+'\n",
    "                                            or app[5] == '50,000,000+'):\n",
    "        print(app[0], ':', app[5])"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "This niche seems to be dominated by software for processing and reading ebooks, as well as various collections of libraries and dictionaries, so it's probably not a good idea to build similar apps since there'll be some significant competition.\n",
    "\n",
    "We also notice there are quite a few apps built around the book Quran, which suggests that building an app around a popular book can be profitable. It seems that taking a popular book (perhaps a more recent book) and turning it into an app could be profitable for both the Google Play and the App Store markets.\n",
    "\n",
    "However, it looks like the market is already full of libraries, so we need to add some special features besides the raw version of the book. This might include daily quotes from the book, an audio version of the book, quizzes on the book, a forum where people can discuss the book, etc."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Conclusions\n",
    "In this project, we analyzed data about the App Store and Google Play mobile apps with the goal of recommending an app profile that can be profitable for both markets.\n",
    "\n",
    "We concluded that taking a popular book (perhaps a more recent book) and turning it into an app could be profitable for both the Google Play and the App Store markets. The markets are already full of libraries, so we need to add some special features besides the raw version of the book. This might include daily quotes from the book, an audio version of the book, quizzes on the book, a forum where people can discuss the book, etc."
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.7.3"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 2
}