# mullaney-james-big-data-project
#### By James Mullaney
## Text Data
The book I will be using is [The Count of Monte Cristo](https://www.gutenberg.org/files/1184/1184-0.txt)
## Tools & Languages
I will be using Databricks as the tool and Python as the language
##  The Process
#### Gathering
Use URLLib to collect the .txt data from a URL and as a useable variable in python.
```python
# Use URLLib to retrieve txt document from url

import urllib.request
urllib.request.urlretrieve("https://www.gutenberg.org/files/1184/1184-0.txt","/tmp/theCount.txt")
dbutils.fs.mv("file:/tmp/theCount.txt","dbfs:/tmp/theCount.txt")
theCountRDD = sc.textFile("dbfs:/tmp/theCount.txt") 
```
#### Cleaning
Seperate the entire book into a collection of data thats split into just the words the book contains.
```python
# tokenize the book to seperate the words . . . messily currently
messyTheCountRDD = theCountRDD.flatMap(lambda line: line.lower().strip().split(" "))

# Remove punction
import re
cleanTheCountRDD = messyTheCountRDD.map(lambda t: re.sub(r'[^a-z]' , '', t))

# Filter out stopwords
from pyspark.ml.feature import StopWordsRemover
remover = StopWordsRemover()
stopwords = remover.getStopWords()

wordsTheCountRDD = cleanTheCountRDD.filter(lambda w: w not in stopwords)
```
#### Processing
First, we map each word on a flatmap paired with the amount of times that word is said. Then, gather the top 10 counts and make a list with the word and count.
```python
# map() words to (word,1) intermediate key-value pairs
IKVPairsTheCountRDD = wordsTheCountRDD.map(lambda x: (x,1))

# reduceByKey to get (word, count) result, then gather the 10 most common words
finalTheCountRDD = IKVPairsTheCountRDD.reduceByKey( lambda y, value : y + value)
wordListTheCountRDD = finalTheCountRDD.map(lambda x: (x[1], x[0])).sortByKey(False).take(10)
```
#### Graphing
Using matplotlib and seaborn to create a bar graph of the 10 most common words in The Count Of Monte Cristo
```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Prepare chart info
source = 'The Count of Monte Cristo'
title = 'Top Words in ' + source
xlabel = 'word'
ylabel = 'count'

# create Pandas dataframe from list of tuples
dataFrameTheCount = pd.DataFrame.from_records(wordListTheCountRDD, columns =[xlabel, ylabel]) 

# create plot (using matplotlib)
plt.figure(figsize=(10,3))
sns.barplot(xlabel, ylabel, data=dataFrameTheCount).set_title(title)
```
#### Result
![image](https://user-images.githubusercontent.com/54418767/115654464-cecb0c00-a2f6-11eb-9caa-a631ab7253fd.png)

**Note:** There are 1,316 pages in *The Count of Monte Cristo*, so "Said" appears roughly 2.64 times a page.
