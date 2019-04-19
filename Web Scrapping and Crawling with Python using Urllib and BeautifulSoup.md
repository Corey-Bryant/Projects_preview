
# Web Scrapping and Crawling with Python using Urllib and BeautifulSoup

So I realized I should update my web scrape example since the other one was 3 years old and used Python 2.7. This example uses Python 3.6. In addition, I decided to add an example showcasing a web crawling application.

When doing any web scrapping, check the terms and conditions of the page or the company.


```python
# Required libraries
from urllib.request import urlopen
from bs4 import BeautifulSoup
import pandas as pd
import datetime
import random
import re
```

Now to load the page as a Python object that contains the information of the web page. Then I will convert it into a BeautifulSoup object that allows one to see the web page's struture when printed out. The printing of the page isn't required, I'm doing it so you can see the page's content. It would be easy to go to the page and 'inspect' the elements to see the structure while the code is being written.


```python
html = urlopen("http://www.pythonscraping.com/pages/page3.html")
bsObj = BeautifulSoup(html)

print(bsObj.prettify())
```

    <html>
     <head>
      <style>
       img{
    	width:75px;
    }
    table{
    	width:50%;
    }
    td{
    	margin:10px;
    	padding:10px;
    }
    .wrapper{
    	width:800px;
    }
    .excitingNote{
    	font-style:italic;
    	font-weight:bold;
    }
      </style>
     </head>
     <body>
      <div id="wrapper">
       <img src="../img/gifts/logo.jpg" style="float:left;"/>
       <h1>
        Totally Normal Gifts
       </h1>
       <div id="content">
        Here is a collection of totally normal, totally reasonable gifts that your friends are sure to love! Our collection is
    hand-curated by well-paid, free-range Tibetan monks.
        <p>
         We haven't figured out how to make online shopping carts yet, but you can send us a check to:
         <br/>
         123 Main St.
         <br/>
         Abuja, Nigeria
    We will then send your totally amazing gift, pronto! Please include an extra $5.00 for gift wrapping.
        </p>
       </div>
       <table id="giftList">
        <tr>
         <th>
          Item Title
         </th>
         <th>
          Description
         </th>
         <th>
          Cost
         </th>
         <th>
          Image
         </th>
        </tr>
        <tr class="gift" id="gift1">
         <td>
          Vegetable Basket
         </td>
         <td>
          This vegetable basket is the perfect gift for your health conscious (or overweight) friends!
          <span class="excitingNote">
           Now with super-colorful bell peppers!
          </span>
         </td>
         <td>
          $15.00
         </td>
         <td>
          <img src="../img/gifts/img1.jpg"/>
         </td>
        </tr>
        <tr class="gift" id="gift2">
         <td>
          Russian Nesting Dolls
         </td>
         <td>
          Hand-painted by trained monkeys, these exquisite dolls are priceless! And by "priceless," we mean "extremely expensive"!
          <span class="excitingNote">
           8 entire dolls per set! Octuple the presents!
          </span>
         </td>
         <td>
          $10,000.52
         </td>
         <td>
          <img src="../img/gifts/img2.jpg"/>
         </td>
        </tr>
        <tr class="gift" id="gift3">
         <td>
          Fish Painting
         </td>
         <td>
          If something seems fishy about this painting, it's because it's a fish!
          <span class="excitingNote">
           Also hand-painted by trained monkeys!
          </span>
         </td>
         <td>
          $10,005.00
         </td>
         <td>
          <img src="../img/gifts/img3.jpg"/>
         </td>
        </tr>
        <tr class="gift" id="gift4">
         <td>
          Dead Parrot
         </td>
         <td>
          This is an ex-parrot!
          <span class="excitingNote">
           Or maybe he's only resting?
          </span>
         </td>
         <td>
          $0.50
         </td>
         <td>
          <img src="../img/gifts/img4.jpg"/>
         </td>
        </tr>
        <tr class="gift" id="gift5">
         <td>
          Mystery Box
         </td>
         <td>
          If you love suprises, this mystery box is for you! Do not place on light-colored surfaces. May cause oil staining.
          <span class="excitingNote">
           Keep your friends guessing!
          </span>
         </td>
         <td>
          $1.50
         </td>
         <td>
          <img src="../img/gifts/img6.jpg"/>
         </td>
        </tr>
       </table>
       <div id="footer">
        © Totally Normal Gifts, Inc.
        <br/>
        +234 (617) 863-0736
       </div>
      </div>
     </body>
    </html>
    
    

For this example, the only content I care about is the data table which is in the "table id="giflist" element. This is very easy with BeautifulSoup, all that is required is to call the table object that is noted by it's HTML tag. Now to take a look at the table.


```python
bsObj.table
```




    <table id="giftList">
    <tr><th>
    Item Title
    </th><th>
    Description
    </th><th>
    Cost
    </th><th>
    Image
    </th></tr>
    <tr class="gift" id="gift1"><td>
    Vegetable Basket
    </td><td>
    This vegetable basket is the perfect gift for your health conscious (or overweight) friends!
    <span class="excitingNote">Now with super-colorful bell peppers!</span>
    </td><td>
    $15.00
    </td><td>
    <img src="../img/gifts/img1.jpg"/>
    </td></tr>
    <tr class="gift" id="gift2"><td>
    Russian Nesting Dolls
    </td><td>
    Hand-painted by trained monkeys, these exquisite dolls are priceless! And by "priceless," we mean "extremely expensive"! <span class="excitingNote">8 entire dolls per set! Octuple the presents!</span>
    </td><td>
    $10,000.52
    </td><td>
    <img src="../img/gifts/img2.jpg"/>
    </td></tr>
    <tr class="gift" id="gift3"><td>
    Fish Painting
    </td><td>
    If something seems fishy about this painting, it's because it's a fish! <span class="excitingNote">Also hand-painted by trained monkeys!</span>
    </td><td>
    $10,005.00
    </td><td>
    <img src="../img/gifts/img3.jpg"/>
    </td></tr>
    <tr class="gift" id="gift4"><td>
    Dead Parrot
    </td><td>
    This is an ex-parrot! <span class="excitingNote">Or maybe he's only resting?</span>
    </td><td>
    $0.50
    </td><td>
    <img src="../img/gifts/img4.jpg"/>
    </td></tr>
    <tr class="gift" id="gift5"><td>
    Mystery Box
    </td><td>
    If you love suprises, this mystery box is for you! Do not place on light-colored surfaces. May cause oil staining. <span class="excitingNote">Keep your friends guessing!</span>
    </td><td>
    $1.50
    </td><td>
    <img src="../img/gifts/img6.jpg"/>
    </td></tr>
    </table>



Now to get this HTML table into a Pandas data frame.


```python
table = bsObj.find('table') # This finds the table object
table_rows = table.find_all('tr') # This finds all the rows
```


```python
headers = []
contents = []

for tr in table_rows:
    # This is the table header labels
    th = tr.findAll('th')
    col = [tr.text.strip() for tr in th if tr.text.strip()]
    
    # This is the table data
    td = tr.findAll('td')
    row = [tr.text.strip() for tr in td if tr.text.strip()]
    
    if col:
        headers.append(col)
    
    if row:
        contents.append(row)
```


```python
df = pd.DataFrame(contents)
df.columns = headers[0][:-1]

df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item Title</th>
      <th>Description</th>
      <th>Cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Vegetable Basket</td>
      <td>This vegetable basket is the perfect gift for ...</td>
      <td>$15.00</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Russian Nesting Dolls</td>
      <td>Hand-painted by trained monkeys, these exquisi...</td>
      <td>$10,000.52</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fish Painting</td>
      <td>If something seems fishy about this painting, ...</td>
      <td>$10,005.00</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Dead Parrot</td>
      <td>This is an ex-parrot! Or maybe he's only resting?</td>
      <td>$0.50</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Mystery Box</td>
      <td>If you love suprises, this mystery box is for ...</td>
      <td>$1.50</td>
    </tr>
  </tbody>
</table>
</div>



## Web Crawing Wikipedia Starting from Kevin Bacon
Here will be an example of a web crawler that crawls Wikipedia and grabs some links to other pages on Wikipedia starting from Kevin Bacon's web page. This only grabs 10 links because I arbitrarily decided on that number.


```python
random.seed(datetime.datetime.now())

def getLinks(articleUrl):
    html = urlopen("http://en.wikipedia.org" + articleUrl)
    bsObj = BeautifulSoup(html)
    return bsObj.find("div", {"id":"bodyContent"}).findAll("a", href=re.compile("^(/wiki/)((?!:).)*$"))


links = getLinks("/wiki/Kevin_Bacon")
pages_traveled = 0

while pages_traveled < 10:
    newArticle = links[random.randint(0, len(links)-1)].attrs["href"] # Starts from a random link found on Kevin Bacon's page
    print(newArticle)
    pages_traveled = pages_traveled + 1
    links = getLinks(newArticle)
```

    /wiki/Diner_(film)
    /wiki/Barry_Levinson
    /wiki/Diablo_Cody
    /wiki/Syst%C3%A8me_universitaire_de_documentation
    /wiki/United_States_Government_Printing_Office
    /wiki/Office_of_Technology_Assessment
    /wiki/United_States_Capitol_rotunda
    /wiki/United_States_Supreme_Court_Building
    /wiki/Dwight_D._Eisenhower_Memorial
    /wiki/Washington,_D.C.
    

This is the foundation of a web crawler, one could traverse each page and grab some data or do whatever is desired on each page before going onto the next. Combining a variation of this code with the above scrapping code will allow one to do just that.
