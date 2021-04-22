# mullaney-james-big-data-project
#### By James Mullaney
## Text Data
The book I will be using is [The Count of Monte Cristo](https://www.gutenberg.org/files/1184/1184-0.txt)
## Tools & Languages
I will be using Databricks as the tool and Python as the language
##  The Process
#### Gathering
Use URLLib to collect the .txt data from a URL and as a useable variable in python.
'''python
# Use URLLib to retrieve txt document from url

import urllib.request
urllib.request.urlretrieve("https://www.gutenberg.org/files/1184/1184-0.txt","/tmp/theCount.txt")
dbutils.fs.mv("file:/tmp/theCount.txt","dbfs:/tmp/theCount.txt")
theCountRDD = sc.textFile("dbfs:/tmp/theCount.txt") 
'''
#### Cleaning
#### Processing
#### Graphing
