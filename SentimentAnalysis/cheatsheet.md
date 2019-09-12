# ﻿R Workshop | Sentiment Analysis on Amazon Reviews

The below markdown file consists of commands and code snippets that will help you complete the lab - Sentiment Analysis on Amazon Reviews.

## Code Snippets

### Setting up your Directory

```
#Set your data file location inside setwd function
setwd("D:/R_Workshop-master/R_Workshop-master/SentimentAnalysis")
```

### Install/Import Packages

```
#Install/import the libraries which are necessary for our R application
	
library(sentimentr)
library(tm )
library(tidyr)
library(tidytext)
library(SnowballC)
library(stringr)
library(psych)
library(syuzhet)
library(reshape2)
library(dplyr)
```

### Read the Data set 

```
data <- read.csv("SentimentAnalysisDataset.csv")

#Returns all the first 5000 values
data<-head(data,5000)

#Store the complete cases subset in a new data frame
#All the data in reviews.text is sored to s
s<- data$reviews.text
s <- gsub("[^a-zA-Z]+", " ", s)
```

###  Build a corpus, and specify the source to be character vectors

```
docs <- Corpus(VectorSource(s))
trans <- content_transformer(function (x , pattern ) gsub(pattern, " ", x))
```

### Text Pre-processing

```
#Remove special characters
docs <- tm_map(docs, trans, "/")
docs <- tm_map(docs, trans, "@")
docs <- tm_map(docs, trans, "\\|")
```

### Transform all the content to lower case

```
docs <- tm_map(docs, content_transformer(tolower))
```

### Remove all numbers punctuation and some key words

```
docs <- tm_map(docs, removeNumbers)
docs <- tm_map(docs, removePunctuation)
docs <- tm_map(docs, removeWords, c("fire","product","amazon","amazon","kindle","purchase"))
exceptions <- grep(pattern = "not|nor|no|n't", x = stopwords(), value = TRUE)
```

### Remove English common stop words

```
my_stopwords <- setdiff(stopwords("en"), exceptions)
docs<-tm_map(docs,removeWords, c(my_stopwords))
```

### Text stemming

```
docs <- tm_map(docs, stemDocument)
```

### Eliminate extra white spaces
```
docs <- tm_map(docs, stripWhitespace)

#Converts the preprocessed data to dataframe and retrieve the text column for calulating sentimnet of this column
df <- data.frame(text=sapply(docs, as.character), stringsAsFactors=F)
r <- df[, "text"]
```

### Perform Sentiment Analysis on the pre-processed text

```
score<-sentiment(r)
s1<-as.data.frame(score)
data$sentiment<- ifelse(s1$sentiment>0,"positive", ifelse(s1$sentiment < 0, "negative", "neutral"))
data$text<-r
res <- data[, c(1:17,22,23,18:21)]
```

### Write the results to results.csv file

```
write.csv(res, "results.csv")
a<-table(data$sentiment)
```

### Combine the data frames by rows

```
a<-data.frame(rbind(a))
print(a)
```

### Get the Total count

```
total_count = a$positive + a$negative + a$neutral
```

### Calculate the positive, negative and neutral scores

```
pos_score = round( 100 * a$positive / total_count )
neg_score = round( 100 * a$negative / total_count )
neu_score = round( 100 * a$neutral / total_count )
labels<-colnames(a)
print(labels)
```

### Get the values of negative, neutral and positive scores

```
values=c(neg_score,neu_score,pos_score)
print(values)
lbls <-paste(labels,values)
lbls <- paste(lbls,"%",sep="")
```

### Exploratory text analysis

```
#Plot the Sentiment analysis on a Pie & a Bar chart
pie(values, labels = lbls, col = rainbow(length(lbls)), main = "Sentiment Analysis On Amazon Reviews")
```