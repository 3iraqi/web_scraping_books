# Web scraping assignment

> The targeted site: [books.toscrape](https://books.toscrape.com/index.html)

- First we import the required libraries

```python
import csv 
import requests as rqst
from bs4 import BeautifulSoup as b_s
```

When inspect the page found that,
The following show the tags that contain information that we need to scrap.

```html

rating:
<p class="star-rating Three">
  
title:
<h3><a href="a-light-in-the-attic_1000/index.html" title="A Light in the Attic">A Light in the ...</a></h3>

price:
<div class="product_price">
<p class="price_color">�51.77</p>

Availability:
<p class="instock availability"> <i class="icon-ok"></i> In stock </p>

</div>
"""
```

---

- First I initialized a list to hold the data called books_details

```python
books_details=[]
NUMBERS_={"One":1,"Two":2,"Three":3,"Four":4,"Five":5} # to convert rating to integer value
```

- Then I use beautiful Soup to collect data from page.

```python
def get_books_info(books,pn):
    for book in books:
        b_rating=book.find("p",class_="star-rating").get("class")[1]
        b_title=book.find("h3").a['title']
        b_link= book.find("h3").a.get("href")
        b_price=book.find("div",class_="product_price").find("p",class_="price_color").string[1:]
        b_availability=book.find("div",class_="product_price").find("p",class_="availability").get_text().strip()

        books_details.append({
            "title":b_title,
            "rating":NUMBERS_[b_rating],
            "price":b_price,
            "availability":b_availability,
            "link":f"https://books.toscrape.com/catalogue/{b_link}",   
            "page_num_in_site":pn+1
            
        })
```

- Then I <font style="color:yellow;font-size:40px;">ask the user</font> to enter the number of pages he want to collect from this site, and start to request the page content using requests library.

```python
page_number = int(input("How many pages do you need?  : "))

for i in range(page_number):
    page_content = rqst.get(url=f'https://books.toscrape.com/catalogue/page-{i+1}.html',timeout=20).content
    soup =b_s(markup=page_content,features= "lxml")
    # site_pages_number=int(soup.find("li",class_="current").get_text().split()[3])
    
    books=soup.find_all("article",class_="product_pod")
    """
    after collecting the information i called the get_books_info function and passing the page that it will deal with.
    """
    get_books_info(books,i)
```

---

- Finally, I put all the data in a CSV file using with open() and CSV library.

```python
keys=books_details[0].keys()
file_name=f"books_details_1_to_{page_number}.csv"

with open(file_name,mode="w",encoding="utf-8-sig",newline="") as output_file:
    dict_writer = csv.DictWriter(f=output_file, fieldnames= keys)
    dict_writer.writeheader()
    dict_writer.writerows(rowdicts=books_details)
    print(" file Created: ")
```

[author: Mohamed.8.eleraqi](Mohamed.8.eleraqi@gmaill.com)
