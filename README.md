import csv
import numpy as np
import re
import nltk
import time
import sys
import csv
import tweepy
from tweepy.streaming import StreamListener
from tweepy import OAuthHandler
from tweepy import Stream
from sklearn.model_selection import train_test_split

def reading_documents():
  my_documents = []
  with open("F:\\output.csv", "rb") as csvfile:
    rows = csv.reader(csvfile)
    for row in rows:
      my_documents.append(row[1])
  return my_documents
  
def preprocessing_document(my_documents):
  documents_processed = []
  s =1
  c =1
  
  for my_document in my_documents:
    print " preprocessing document:", c
    c+=1
    text = my_document
    s+=1
    text = re.sub(r"(?:\@|'|http?\://)\s+", "", text)
    text = re.sub(r'[^\w\s]', '', text)
    text = re.sub("\d+", "", text)
    tokens_text = nltk.word_tokenize(text)
    stopwords = nltk.corpus.stopwords.words('english')
    tokens_text = [w for w in tokens_text if w.lower() not in stopwords]
    print tokens_text
    print "###############################"
    documents_processed.append(tokens_text)
  return documents_processed
  
access_token = "745412918615891968-vvQEqAiIhDxjALHwGzcpm5C08ZHs0TF"
access_token_secret = "MY1PVWhe4y7weNVBNGWaE2lYyeKXuCp2U2TR9KaK8WUET"
consumer_key = "SunoDexVqnqLiIHHQ5wo7jMX7"
consumer_secret = "19Fppep8mXUn1QHHtaAAUYrBAjxdSR32yt6oUCfHGGRZDSJlyM"
auth = OAuthHandler(consumer_key,consumer_secret)
auth.set_access_token(access_token,access_token_secret)
api = tweepy.API(auth)
#This is a basic listener that just prints received tweets to stdout.

class listener(StreamListener):
  def on_data(self, data):
    try:
      #print data
      tweet = data.split(',"text":"')[1].split('","source')[0]
      print tweet
      saveThis = str(time.time())+'::'+tweet
      saveFile = open("F:\\output.csv", "a")
      saveFile.write(data)
      saveFile.write('\n')
      saveFile.close()
      return True
    except BaseException, e:
      print 'failed ondata, ',str(e)
      time.sleep(5)
      
  def on_error(self, status):
    print status
    
auth = OAuthHandler(consumer_key, consumer_secret)
auth.set_access_token(access_token, access_token_secret)
twitterStream = Stream(auth, listener())
twitterStream.filter(track=['event'])

