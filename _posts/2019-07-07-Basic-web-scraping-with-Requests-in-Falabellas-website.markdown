---
title:  "Basic web scraping with Requests in Falabella's website"
date:   2019-07-07 21:45:16
categories: articles
abstract: sth sth [...]
---

The other day I was talking to my friend about his job. While he was explaining me the actvities he do, he told me that one of them is usually going into the company's website and check if the products are already there.

However, you can only search the information of ten products at the same time so the process its really slow when you need to check the information many products.

Well, with the help of Python and the library Requests you can actually get all the information needed without even going to the website and having to worry about its limits with a technique called **web scraping**.

### Introduction

Web scraping is a technique of capturing the data from the web into our local machine in an automated way in order to perform certain data analysis to get useful insights from that website. It involves fetching (also known as retriving or downloading) a web page and then extractimg information from its HTML code to take something out of it and use it for another purpose. 

Since most of the websites use HTML and follow the same structure of HEAD + BODY, and change from URLs using GET and POST requests, the process of web scraping can be replicated among then. 

Requests is a Python library that allows you to send HTTP/1.1 requests using Python. With it, you can add content like headers, form data, multipart files, and parameters to your requests and also access the response data. It abstracts the complexities of making requests behind a simple API so that you can focus on interacting with services and consuming data in your application.

This library let you use the main types of requests: get, post, put, delete, head and options, and use them to make calls to the websites. Also, it let you check the status code of the requests in order to see if you had any errors or if it was successful.

### Getting started

First, be sure that you have Requests installed in your computer. If not, you can easilly install them using pip:

```python
pip install requests
``` 

### Exploring the website

The first step to do web scraping is to know how the website you want to scrape works. 

In this case, we are going to be working on [falabella's website](https://www.falabella.com.pe/falabella-pe/)

<img src="{{ site.baseurl }}/images/posts/requests/2019_07_07_1.JPG" title="Falabella's website">

And to be more specific, we will be working with the search bar:

<img src="{{ site.baseurl }}/images/posts/requests/2019_07_07_2.JPG" title="Search bar">

#### What we know

Thanks to my friend, I already know that we can search a product with its SKU number in the search bar and look up to ten at the same time. However, since we want to automate this process for a bigger amount of products, we will analyze what is the behavior when we search for only one.

When we find a product we get an URL with a pattern like https<span></span>://www<span></span>.falabella.com.pe/falabella-pe/product/sku/name/sku and the product with its image and its description in the HTML code. When the product it is not published yet, we get an URL like https<span></span>://www<span></span>.falabella.com.pe/falabella-pe/noSearchResult?Ntt=sku and an HTML with a message that tell us that we got no results for our search.

With this information, we already see a pattern of what happen when we search for a product.

#### What we need to know

Now that we know what happen when we search for a product, we need to know what its the process behind this search. In order to do that, we will use the developer tools of Chrome and see what are the requests made to the website in the Network tag [(More details here)](https://developers.google.com/web/tools/chrome-devtools/network/).

<img src="{{ site.baseurl }}/images/posts/requests/2019_07_07_3.JPG" title="Chrome's developer tools"> 

With this, we see that the search is made by calling the following URL: https<span></span>://www<span></span>.falabella.com.pe/falabella-pe/search/?Ntt=sku


### Checking if a product is published

In order to use the search bar and check if a product its published we need to use the URL found previously and see what are the contents of the resulting website.

Yet, since the actual information that we need is also in the url of the result, we will be only using that to see if our product its published or not:   

``` python
import requests

# Set the SKUs to find and the URL where we will be looking up
products = ['880969943','881212428','15799133','123554']
search_url= "https://www.falabella.com.pe/falabella-pe/search/?Ntt="

for product in products :
	# Make a head call to the URL
	response = requests.head(search_url+product)	
	# Get the final URL shown by the browser   
	url = response.headers['location']
	
	# Check if we have a result for our SKU or not
	if url.find("noSearchResult") != -1:
	  	print(product,'Not Published')
	else:
	   	print(product,'Published')
```
In this case, since we are only looking for information in the url of the website, we use a HEAD call instead of a GET to reduce the response time of the website.

In many cases, you would need to retrieve the whole HTML of the web page with a GET call and then analyze the text to find information:

```python
import requests

# Set the SKUs to find and the URL where we will be looking up
products = ['880969943','881212428','15799133','123554']
search_url= "https://www.falabella.com.pe/falabella-pe/search/?Ntt="

for product in products :
	# Make a get call to the URL
	response = requests.get(search_url+product)	
	# Get the html of the website   
	html_text = response.text
	
	# Check if we have a result for our SKU or not
	if html_text.find("Código del producto:"+product) != -1:
	  	print(product,'Published')
	else:
	   	print(product,'Not Published')
```

### Checking if a product is part of a group

Also, some products that only change in colour or size are grouped inside a code. This code is also something needed for my friend, so we will be getting it:

```python
import requests
import re

products = ['880969943','880602606','15799133','123554']
search_url= "https://www.falabella.com.pe/falabella-pe/search/?Ntt="

#check for skus
for product in products :
	# Make a head call to the URL
	response = requests.head(search_url+product)

	# We can check the status code to see if we ran into a problem
	if response.status_code != 302:
		print('Error')
		continue

	# Get the final URL shown by the browser and remove a part of it   
	url = response.headers['location'].replace("product/","")
	
	# Check if we have a result for our SKU or not
	if url.find("prod") != -1:
		# Find the code with the help of a regular expression
		group = re.search('prod(.+?)/', url).group(0).replace("/","")
		print("Group", group, product)
	else:
		print("Not in group", product, product)
```


The full code used in these examples can be found in the [blog's github](https://github.com/jpereiran/jpereiran-blog/tree/master/code/requests/excel).

### Conclusion
Whenever someone wants to work with Excel files in python, most tutorials and websites suggest the use of pandas, opepyxl or xlsxwriter packages. However, it is important to know that there are other options availabe that can help you with more functionalities. Microsoft’s COM technology is an option that can be used effectively through python to do tasks that might be too difficult to do otherwise and not limitting its use to only Excel. Hopefully this example can give you some inspiration on how to incorporate this library into your work.

### Next steps

In this post I showed you the basics of web scraping and how to use Python and Requests to automate the process of getting some data from the web. Also, the different approaches you can take when needed to make a decision about what you need to extract from a website to solve a problem.

In the next tutorial about web scraping, I'll show you how to use other libraries to get the information in the HTML of the page in a simpler way and how to speed up the process using multiple threads to look at multiple URLs a the same time.
