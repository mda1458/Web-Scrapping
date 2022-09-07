# Web scrapping Using Python3

### GitHub Best repositories of certain topics

### Outline:
- We are going to scrape https://github.com/topics
- We will get topics and then we scrape topic title, URL and description
- For every topic we will get top 25 repo and we grab repo name, username, rating and URL
- Then we will make CSV

### Importing libraries


```python
import requests
import pandas as pd
from bs4 import BeautifulSoup
```

### Defining functions

Downloading page and parsing


```python
def get_topics_page():
    topics_url = 'https://github.com/topics'
    # Downloading page
    response = requests.get(topics_url)
    # Status Checker
    if response.status_code != 200:
        raise Exception(f'Failed to load page {topic_url}')
    # Parser
    doc = BeautifulSoup(response.text, 'html.parser')
    return doc
```

Getting Topics Information


```python
def get_topic_titles(doc):
    # Find by inspect element
    selection_class = 'f3 lh-condensed mb-0 mt-1 Link--primary'
    # Tags searching
    topic_title_tags = doc.find_all('p', {'class': selection_class})
    # Titles extraction and appending to list
    topic_titles = [tag.text for tag in topic_title_tags]
    return topic_titles
```


```python
def get_topic_descs(doc):
    # Find by inspect element
    desc_selector = 'f5 color-fg-muted mb-0 mt-1'
    # Tags searching
    topic_desc_tags = doc.find_all('p', {'class': desc_selector})
    # Description extraction and appending to list
    topic_descs = [tag.text.strip() for tag in topic_desc_tags]
    return topic_descs
```


```python
def get_topic_urls(doc):
    # Tags searching
    topic_link_tags = doc.find_all('a', {'class': 'no-underline flex-1 d-flex flex-column'})
    # URL extraction and appending to list
    parent_url='https://github.com'
    topic_urls=[(parent_url+tag['href']) for tag in topic_link_tags]
    return topic_urls
```

Getting info of each topics


```python
def getRepoInfo(tags,star_tag):
    a_tags=tags.find_all('a')
    username=a_tags[0].text.strip()
    repo=a_tags[1].text.strip()
    parent_url='https://github.com'
    repo_url=parent_url+a_tags[1]['href']
    stars=star_tag['title']
    return username,repo,stars,repo_url
```

Compiling all details to DF


```python
def makeDfOFTopicURL(URL):
    # Downloading page
    url_response = requests.get(URL)
    content=url_response.text
    # Parsing And Data Extraction
    parsing=BeautifulSoup(content, 'html.parser')
    tags=parsing.find_all('h3',class_='f3 color-fg-muted text-normal lh-condensed')
    stars_tags=parsing.find_all('span', {'id':'repo-stars-counter-star'})
    # Making its dictionary
    topic_info_dict={
    'Username':[],
    'Repository':[],
    'Stars':[],
    'Repository URL':[]
    }
    for i in range (len(tags)):
        repoInfo=getRepoInfo(tags[i],stars_tags[i])
        topic_info_dict['Username'].append(repoInfo[0])
        topic_info_dict['Repository'].append(repoInfo[1])
        topic_info_dict['Stars'].append(repoInfo[2])
        topic_info_dict['Repository URL'].append(repoInfo[3])
    return pd.DataFrame(topic_info_dict)
```

Main Scrapper


```python
def scrape_topics(URL,name):
    topic_df=makeDfOFTopicURL(URL)
    topic_df.to_csv(name+'.csv',index=None)
```


```python
def scrapeData():
    print('Scrapping...')
    for index,row in topicsDF.iterrows():
        a=row['Topics']
        print(f'Scrapping top repositories for {a}')
        scrape_topics(row['URL'],row['Topics'])
```

### Main Program

Converting extracted info to csv using pandas DataFrame


```python
doc=get_topics_page()
dataDict={'Topics': get_topic_titles(doc),
        'Description':get_topic_descs(doc),
        'URL':get_topic_urls(doc)}
topicsDF = pd.DataFrame(dataDict)
```


```python
scrapeData()
```

    Scrapping...
    Scrapping top repositories for 3D
    Scrapping top repositories for Ajax
    Scrapping top repositories for Algorithm
    Scrapping top repositories for Amp
    Scrapping top repositories for Android
    Scrapping top repositories for Angular
    Scrapping top repositories for Ansible
    Scrapping top repositories for API
    Scrapping top repositories for Arduino
    Scrapping top repositories for ASP.NET
    Scrapping top repositories for Atom
    Scrapping top repositories for Awesome Lists
    Scrapping top repositories for Amazon Web Services
    Scrapping top repositories for Azure
    Scrapping top repositories for Babel
    Scrapping top repositories for Bash
    Scrapping top repositories for Bitcoin
    Scrapping top repositories for Bootstrap
    Scrapping top repositories for Bot
    Scrapping top repositories for C
    Scrapping top repositories for Chrome
    Scrapping top repositories for Chrome extension
    


```python

```
