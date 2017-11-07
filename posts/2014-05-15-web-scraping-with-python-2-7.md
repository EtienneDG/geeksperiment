---
id: 527
title: Web scraping with Python 2.7 (1/2)
date: 2014-05-15T12:36:21+00:00
author: Etienne
excerpt: No API? Roll up your sleeves and start scraping!
layout: post
guid: http://geeksperiment.com/?p=527
permalink: /web-scraping-with-python-2-7/
categories:
  - Projects
tags:
  - beautifulsoup
  - DOM
  - python
  - web scraping
---
A lot of companies expose their data to (almost) everyone, mainly through APIs. A &laquo;&nbsp;few&nbsp;&raquo; years back it wasn&rsquo;t that common and was mainly used by IT companies that wanted to make their data available so that new apps can emerge from it. Nowadays, it has become a must have for a lot of [companies](http://business-solutions.travel.michelin.fr/vos-besoins/integrer-des-calculs-de-trajets-dans-vos-applications/viamichelin-rest-api.html), [(former) startups](https://dev.twitter.com/docs/api) and even [governments](https://www.data.gov/developers/apis) to propose this kind of access to data.
  
However, when there isn&rsquo;t a key-in-hand API what choice is left? **Web scraping!**

&nbsp;

## General principle of web scraping

Web scraping describes the method of extracting content from a web page in order to reuse it for further use or calculation. Why &laquo;&nbsp;Scraping&nbsp;&raquo;? Because we have to scrap everything that is not the actual content  (and that we want to extract). The process implies to know the structure of the page you&rsquo;re scraping: how is the content organized, what is the element/id/class of the item you want to extract, etc.

One would think that extracting text surrounded by tags is easy, **well it&rsquo;s not**. Unfortunately HTML isn&rsquo;t that to deal with&#8230;at least with a few lines of code. To comprehend why scraping is not that easy, I will first show an example on how to extract all the links from a page only with basic operations. Secondly, I&rsquo;ll will do the same thing with the (excellent) [BeautifulSoup library](http://www.crummy.com/software/BeautifulSoup/).

&nbsp;

## Scraping the old-school way

So I want to write code that is able to differentiate the useful parts from the page structure. To begin with, I have to get our page **source code**, for this purpose the [urllib python library](https://docs.python.org/2/library/urllib.html) will do the trick:

```python  
import urllib

def get_page_content(url):
    try:
        return urllib.urlopen(url).read()
    except:
        return "Error"


res = get_page_content("http://en.wikipedia.org/wiki/Beautiful_Soup")
print res
```

First step is done: we have our web page in a string. **Awesome**! I can now easily extract all links from it&#8230;or can I&#8230;

I **assume** that all links starts with &laquo;&nbsp;<a&nbsp;&raquo; and ends with &laquo;&nbsp;</a>&nbsp;&raquo; and that the adress is contained within those tags just after &laquo;&nbsp;href=&nbsp;&raquo;:

```python
def extract_links(page):
    all_links = []
    start_str = "&lt;a"
    end_str = "&lt;/a&gt;"
    while start_str in page:
        start_position = page.find(start_str)
        end_position = page.find(end_str,start_position)
        link = page[start_position:end_position]
        href_pos = link.find('href="')+ len('href="')
        all_links.append(link[href_pos:link.find('"',href_pos)])
        page = page[end_position:]
    return all_links


res = get_page_content("http://en.wikipedia.org/wiki/Beautiful_Soup")
print res
links = extract_links(res)
print links
```
And here are of few links extracted :

```python
['=',
 '#mw-navigation',
 '#p-search',
 '/wiki/Software_design',
 '/w/index.php?title=Leonard_Richardson&amp;action=edit&amp;redlink=1',
 'http://en.wikipedia.org/w/index.php?title=Beautiful_Soup&amp;oldid=589228809',
 '#', '/wiki/Main_Page', '/wiki/Main_Page', '/wiki/Portal:Contents',...]
 ```

Clearly this is **not what was expected**&#8230;

Several problems encountered with the links of this page:

  * Empty link
  * Anchors
  * Relative addresses

I added a check on the adress format:

```python
def extract_links(page):
    all_links = []
    start_str = "&lt;a"
    end_str = "&lt;/a&gt;"
    while start_str in page:
        start_position = page.find(start_str)
        end_position = page.find(end_str,start_position)
        link = page[start_position:end_position]
        href_pos = link.find('href="')+ len('href="')
        href_content = link[href_pos:link.find('"',href_pos)]
        #link format check
        if href_content[0] != "#" and href_content != "=":
            if href_content[0] == "/":
                all_links.append("http://en.wikipedia.org"+href_content)
            else:
                all_links.append(href_content)
        page = page[end_position:]
    return all_links
```

OK let&rsquo;s face it the function is not that pretty and not very adaptive. Soup time!

&nbsp;

## Scraping with tools (i.e. BeautifulSoup)

BeautifulSoup is a powerfull library to manipulate DOM content. It&rsquo;s rather straight forward to use, for you can call page elements by their type, class, id. See [BeautifulSoup doc](http://www.crummy.com/software/BeautifulSoup/bs4/doc/). I recommend to install BS from pip which is (normally) the easy way.

Back to business. I still want to extract all the links from my page, which is now much easier. The process is more or less the same: get all the links, check if their &laquo;&nbsp;href&nbsp;&raquo; attribute is empty, get the value of the &laquo;&nbsp;href&nbsp;&raquo; attribute and append it to the result array:

```python
import urllib
from BeautifulSoup import BeautifulSoup

def get_page_content(url):
    try:
        return urllib.urlopen(url).read()
    except:
        return "Error"

def extract_links(page):
    all_links = []
    root_url = "http://en.wikipedia.org/"
    soup = BeautifulSoup(page, convertEntities=BeautifulSoup.HTML_ENTITIES)
    links = soup.findAll('a')
    for url in links:
        if url.has_key('href'):
            all_links.append(root_url + url['href'])
    return all_links


res = get_page_content("http://en.wikipedia.org/wiki/Beautiful_Soup")
print res
links = extract_links(res)
print links
```

And that&rsquo;s it. Where the first example the main loop was 17 lines long, here it&rsquo;s only 3 lines and much clearer.

##  Few BeautifulSoup examples

Finding an element by id:

```python
soup.find('span', {'id': re.compile(r"^beginningOfMyID")})
```

Finding an element&rsquo;s parent:

```python
tag = soup.find('img', {'src' : re.compile('images/myImg.png')}).findParent()
```
  
Extracting an element from the page (prevents you from keeping track of start/end position and edits the page in mean time):

```python
tag = soup.find('h1').extract()
```