# Python normalized distribution automation interface
use python to fetch stock prices of targeted IPO companies every one hour and create normalized distribution graph.
## Top Interview
- [2.create function of geting price](#2create-function-of-geting-price)  
- [5.refine those data collected](#5refine-those-data-collected)  
- [146.create normalized distribution graph](#146create-normalized-distribution-graph)  
- [380.plot and set time](#380plot-and-set-time)  
### 2.create function of geting price
```python
import urllib ##Basic use 
from bs4 import BeautifulSoup ##polishing html codes
from urllib import request
from urllib import parse
from urllib.request import urlopen
import re
import time
import datetime
import csv
import sys
import codecs
import string
import random
import socket
import http.cookiejar
def get_price(url1):
    with urlopen(url1) as text:
        soup = BeautifulSoup(text, 'html.parser')
        for p in soup.find_all('p'):
            p_txt=p.get_text()
            if('The current last sale of' in p_txt):
                spantxt=p.find_all('span')
                for i in range(len(spantxt)):
                    data=spantxt[i].get_text()
                    if('The current last sale of' in data):
                        try:
                            price=spantxt[i+1].get_text()
                            if("$" in price):
                                pruned_txt=price.strip(string.punctuation)
                                return pruned_txt # get rid of () $ punctuations
                        except IndexError:
                             pass
def get_price(url2):
    with urlopen(url2) as text:
        soup = BeautifulSoup(text, 'html.parser')
        for p in soup.find_all('p'):
            p_txt=p.get_text()
            if('The current last sale of' in p_txt):
                spantxt=p.find_all('span')
                for i in range(len(spantxt)):
                    data=spantxt[i].get_text()
                    if('The current last sale of' in data):
                        try:
                            price=spantxt[i+1].get_text()
                            if("$" in price):
                                pruned_txt=price.strip(string.punctuation)
                                return pruned_txt # get rid of () $ punctuations
                        except IndexError:
                             pass
```
### 5.refine those data collected
use those targeted IPO companies list created previuosly to match->prune and float those prices.
```python
dic={}
with open('longterm.csv', newline='') as csvfile:
    reader = csv.DictReader(csvfile)
    for row in reader:
        dic[row['ï»¿Stockgroup']]=row['Normalgroup']
def resultstock():
    stocklist=list()
    for k,v in dic.items():
        url1="https://www.nasdaq.com/symbol/"+k
        p=get_price(url1)
        if(p is not None):
            rep=p.split('$')
            rep2=rep[len(rep)-1]
            rep3=float(rep2)
            stocklist.append(rep3)
    return [a for a in stocklist]
def resultnormal():
    normallist=list()
    for k,v in dic.items():
        url2="https://www.nasdaq.com/symbol/"+v
        p2=get_price(url2)
        if(p2 is not None):
            Opp=p2.split('$')
            Opp2=Opp[len(Opp)-1]
            Opp3=float(Opp2)
            normallist.append(Opp3)
    return [b for b in normallist]
```
### 146.create normalized distribution graph
```python
import numpy as np 
import matplotlib.mlab as mlab 
import matplotlib.pyplot as plt 
import pandas
from scipy.stats import norm 
def stockgraph():
    mean1, std1=norm.fit(resultstock())
    plt.hist(resultstock(), color='gold',density=True)
    xmin1, xmax1 = plt.xlim() 
    x1 = np.linspace(xmin1, xmax1, 100) 
    y1 = norm.pdf(x1, mean1, std1) 
    plt.plot(x1, y1,color='red') 
    plt.xlabel('sotckprices')
    plt.ylabel('Probability Density')
    plt.title('Stock Group Current Price')
def normalgraph():
    mean2, std2=norm.fit(resultnormal())
    plt.hist(resultnormal(),color='green', density=True)
    xmin2, xmax2 = plt.xlim() 
    x2 = np.linspace(xmin2, xmax2, 100) 
    y2 = norm.pdf(x2, mean2, std2) 
    plt.plot(x2, y2,color='blue')
    plt.xlabel('sotckprices')
    plt.ylabel('Probability Density')
    plt.title('Normal Group Current Price')
```
### 380.plot and set time
```python
def timer(n):
    while True:
        T=time.strftime("%Y-%m-%d %H:%M:%S", time.localtime()) ##get local time
        print(T)
        plt.show(stockgraph())
        plt.show(normalgraph()) 
        time.sleep(n)
timer(3600)
```
    
