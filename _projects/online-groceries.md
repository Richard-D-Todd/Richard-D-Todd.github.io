---
title: "Online Groceries: Extracting Data from Receipt Emails and Analysing Spending"
excerpt: "Looking into my groceries spending."
header:
  image: /assets/images/online-groceries_wide.jpg
  teaser: /assets/images/online-groceries.jpg
classes: wide
gallery:
  - url: /assets/images/Groceries-fig1.png
    image_path: assets/images/Groceries-fig1.png
    alt: "Total for Each Delivery"
  - url: /assets/images/Groceries-fig2.png
    image_path: assets/images/Groceries-fig2.png
    alt: "Total and Subtotal for Each Delivery"
  - url: /assets/images/Groceries-fig3.png
    image_path: assets/images/Groceries-fig3.png
    alt: "Spending by Month with Average"
  - url: /assets/images/Groceries-fig6.png
    image_path: assets/images/Groceries-fig6.png
    alt: "Proportion of delivered, substituted and unavailable items"
  - url: /assets/images/dashboard_preview.gif
    image_path: /assets/images/dashboard_preview.gif
    alt: "groceries dashboard page 1"
  - url: /assets/images/dashboard_preview_page_2.gif
    image_path: /assets/images/dashboard_preview_page_2.gif
    alt: "groceries dashboard page 2"

---

## Grocery Shopping Email Analysis
[Project repo on Github][1]

### Context:
I do my regular grocery shops online through the British supermarket chain ASDA. After the delivery is packed and on the way an email is sent to me with a breakdown of which items are unavailable, substituted and sent as ordered, along with the prices and quantities. I want to be able to collect this data and analyse my spending on groceries.

### Aims:
The aim of this project is to get data from the receipt email sent for my online food shops. This data will then be loaded into a database ready for analysis. I would like to investigate the following:
* My spending habits and how these change over time.
* Places were I could potentially save money, or are overspending.
* The common items I buy.

### Gallery
{% include gallery caption="Visuals from the Analysis Notebook and dashboard" %}

### Software and tools used:
* python is used to extract the data from the receipt emails. this is done in the followinf steps:
   1. I used the library ExchangeLib to connect to my outlook email account and save the email body and details to variables.
   2. I parsed the emails for the relevant data and then saved them to Pandas dataframes. From there i cleaned the data and did some basic transformations.
   3. I used the SQLalchemy library to connect to my PostgreSQL database and save the dataframes to database tables.
   4. I developed a dashboard using the Dash library, creating the visuals with the Plotly graphing library.
* PosgreSQL was used to create my database for storing my groceries data.
* I used git for version control and also to allow me to deploy my dashboard to the cloud using the Heroku service.
* I use a RaspberryPi computer to host a my PostgreSQL database and to deploy the dashboard on my home network.

### Status:
Work in progress

## Update: 24/04/21
I have made several changes to this project since my last update. These are:
1. Wrote a script that can extact the email data directly from my microsoft email account, without having to export the email as a .eml file.
2. The local PosgreSQL database is now hosted on a RaspberryPi single board computer.
3. The local version of my dashboard is now hosted on my RaspberryPi.
4. Added a cumulative chart on the home page to show how much I have spent since my first ASDA order.

### Extracting data directly from outlook
My receipt emails are moved to a specific folder using a rule set-up on my inbox which looks at the subject line.

i use the library exchangelib. More information on this library can be found [here][3]. In my script I have a function which sets up the connect. My credentials are read from a .ini file using the ConfigParse library.

```python
def connect_to_exchange():
    """
    Function to connect to microsoft exchange mail server based on credentials in exchange_credentials.ini file
    """
    # Importing account email address and password
    config = configparser.ConfigParser()
    config.read('exchange_credentials.ini')
    email_address = config['credentials']['email_address']
    password = config['credentials']['password']

    # Defining credentials for exchange account and setting account
    credentials = Credentials(email_address, password)
    account = Account(email_address, credentials = credentials, autodiscover = True)
    return account
```

I then get the date the email weas received, subject and body of every email in the 'ASDA Order Receipts' folder.

```python
receipt_folder = account.inbox / 'ASDA Order Receipts'
items = receipt_folder.all().order_by('datetime_received')
# Extract datetime_received, subject and body from each item
item_details = items.values('datetime_received', 'subject', 'body')
```

the rest of the script loops through each email item and does the same prcessing as the extract from file scripts previously created.

Once an email file has been processed the email is moved to a 'processed' subfolder so that it is not duplicated when the script is next ran.
```python
# Move email to 'processed' folder
processed_folder = receipt_folder / 'processed'
items[0].move(processed_folder)
print(f"Email moved to processed folder for file {item_num} out of {num_emails}")
item_num += 1
```

### Host database and dashboard on RaspberryPi
I now host the groceries PostgreSQL database on my RaspberryPi instead of my PC. The only change that was required was to point my *create_sqlalchemy_engine()* function to by database. Again I did this with a .ini file.

```python
def create_sqlalchemy_engine():
    """
    This function creates a sqlalchemy engine with the credentials stored in the credentials.py file
    """
    config = configparser.ConfigParser()
    config.read('database.ini')
    username = config['postgresql']['user']
    password = config['postgresql']['password']
    database = config['postgresql']['database']
    host = config['postgresql']['host']
    con_string = 'postgresql+psycopg2://{}:{}@{}/{}?gssencmode=disable'.format(username, password, host, database)
    engine = create_engine(con_string)
    print("Local DB: {}".format(con_string))
    return engine
```

To launch the dashboard on my RaspberryPi I wrote a Bash shell script to activate the required virtual environment and then launch the dashboard script (index.py is the script which launches the Dash server).

```bash
#! /bin/bash

echo 'activating virtual environment'
activate () {
	. groceries_venv/bin/activate
}
activate
echo 'launching dashboard'
cd Dashboard
python index.py
```

### Further changes I would like to make
Now that I have a script which can extract the data directly from my email account I can automate the process of data extraction. Currently I have to manually run this script after every delivery. 

Since the RaspberryPi has such low power consumption I plan to schedule the extract from exchange script to run everyday that an order is delivered. I could do this using a CRON job. To do this I would want to know when an error is encountered, therefore I will also research the best way to handle errors in my script, log these errors and also notify me when an issue is encountered.

In terms of the dashboard itself I would like to start categorising the products which have been delivered. The reason for this is it would allow me to beter investigate my spending patterns. My plan for this is to write Python script which gets all the products from my database and then ask me to input multiple categories and subcategories for each product. This would then be saved in another table in my database, just to store these categorisations.

Recently on my apprenticeship I have been learning about time series analysis. i would like to apply some of these techniques, such as a rolling mean and potentially some forecasting. I envisage the forecasting not being too difficult as my data is fairly static.

## Update: 19/10/20
Since my last update I have created a dashboard for my groceries project. The dashboard was created using the Dash library in python and is deployed using the [Heroku service](2).

Dash is used to create the figures and the interactive elements such as the dropdowns and radio buttons. It also allows the plots to interact with each other. The plots themselves are created using the Plotly plotting library.

### Orders overview page
The first page I created has the time series chart with the totals for each order. The second visual the monthly totals for orders. the incomplete month to date is coloured in orange to differentiate it. There are two different 'modes' for the monthly total:

1. Calendar month: This groups the data by the calendar month.
2. Pay period: I currently get paid on the 27th of each month. I found that I was planning shops based around pay day and so I created a pay period view to show spending per pay period. For example June is from 27/05/2020 to 26/06/2020.

The bars in the monthly view can be used to highlight the corresponding bars in the time series chart at the top of the page. Multiple months can be selected.

the last visual shows the proportion of the items in the order which were either:
1. Delivered as ordered. (called available)
2. Substituted for another product.
3. Unavailable and thefore not delivered and without substitution. (called unavailable)

This visual can be toggled between a time series view that has time as continuous variable on the x-axis. The second is a compact view which makes the x-axis discreet variables with each delivery date as a distinct label.

![dashboard page 1](/assets/images/dashboard_preview.gif)

###  Order details page
The second page shows the order details. A dropdown is used to select a specific order. this then populates the table with all the delivered items for that order. This includes the item name and both the cost, quantity and unit cost.

Below the table is a bar chart which shows the number of items that are:
1. Delivered as ordered. (called available)
2. Substituted for another product.
3. Unavailable and thefore not delivered and without substitution. (called unavailable)

Finally the bottom visual shows the above counts as proportions on a single bar.

![dashboard page 2](/assets/images/dashboard_preview_page_2.gif)

### Going forward
the next aim is to develop a third page which shows trends in the items I am buying. For this I first want to categorise all the delivered items so tha I can look into categories of similar items rather than specific brands and items. I also want to create it so that I don't have to manually save the eml files for processing and can instead have a script connect directly to my outlook account and grab the email from there.


## Update: 18/07/20
Currently with this project I have two python scripts. One which can take a receipt email, extract and clean the data and then save to CSV and enter the data into a PostgreSQL database.

The second script works the sdame way as the first, but can be used on a whole directory of .eml files.

I have an analysis Jupyter notebook. With this so far I have several graphs. These are shown the gallery above. I have also written SQL queries to return the most expensive and most common items from my shopping. I have created the following:
1. Bar chart to show total for each delivery against delivery date.
  ![Total for Each Delivery](/assets/images/Groceries-fig1.png)
2. Bar chart to show the total and subtotal for each delivery. The x axis is still delivery date as above, however the variable is not coninuous and is instead discreete. The reason I did this was because I am not neccessarily interested in the gap between deliveries with this visual, but instead the manitude of the subtotal and the total.
  ![Total and subtotal for Each Delivery](/assets/images/Groceries-fig2.png)
3. Total spend per calendar month ploted as a bar chart, with the average as a horizonal line.
![Total Spend per Calendar Month](/assets/images/Groceries-fig3.png) 
4. Total spend per pay month plotted the same as above. The reason I wanted to show the total spend per pay month is because I get paid on the 27th of each month. This skews the calendar month totals as I may for example delay a payment until after payday when I have more money, or to bring forward a delivery until just after  payday before the start of the next month.
![Total Spend per Pay Month](/assets/images/Groceries-fig4.png)
5. I noticed that I had a increase in the number of substitutions, beginning at the end of February. I therefore decided to calculate the proportions of each delivery that were either; delivered as ordered, substituted or unavailable. This was done based on the count of each item in that category and not the quantity or price. This was then plotted as a discreete bar chart with proportion against the delivery date. It can be seent aht the proportion of substituted items increased in March but then fell again in april. 
![Proportion of Items Delivered as Ordered, Substituted and Unavailable per Delivery](/assets/images/Groceries-fig5.png)
6. This is the same visual as above but with the delivery date as a continuous variable and the offical start of the UK lockdown (23rd March) shown as a vertical line. This graph neatly shows the effect of panic buying on the overall availability of items. also the reason for the large gaps in time between delivery after lockdown was due to the increased demand on online delivery services wich was not present pre-lockdown.
![Proportion of Items Delivered as Ordered, Substituted and Unavailable per Delivery](/assets/images/Groceries-fig6.png)
7. SQL  statement to show the ten most frequently delivered items as a count of items:
```sql
SELECT item, count(item)
FROM delivered_items
GROUP BY item
ORDER BY count(item) desc
LIMIT 10;
```
8. Similar to above this SQL statement returns the top ten items based on the sum of quantity instead of the count of occurances in the table.
```sql
SELECT item, SUM(quantity) 
FROM delivered_items
GROUP BY  item
ORDER BY SUM(quantity) desc
LIMIT 10;
```
9. SQL statement to return the top 15 most expensive items by unit price:
```sql
SELECT DISTINCT item, unit_price
FROM delivered_items
ORDER BY unit_price desc
LIMIT 15;
```
10. SQL statment to return the top ten items by the total spent on those items
```sql
SELECT item, unit_price, SUM(quantity), unit_price * SUM(quantity) AS total_spend
FROM delivered_items
GROUP BY item, unit_price
ORDER BY total_spend desc
LIMIT 10;
```
In all of the above SQL statements one thing I notices was that you could have items that were pretty much identical, for example packs of chicken breast, however you can't have them aggregate as they have slightly different names. In future I may want to give some items a generic name if they meet certain conditions. For example, giving a secondary label for common items such as mince meat, rice or chicken breast.

### Going forward
I have my first initial visualisations and SQL queries created. Recently ASDA changed the formatting of the receipt emails and so the last two receipt emails no longer work with my scripts. Therefore I need to update my scripts. For this I plan to add a section of code to take the subject line of the email. If the subject matches the old format then the script will continue as before. If the subject line is of the new template then the data will be cleaned in a slightly different way. I will create a new Jupyter notebook to explore the changes that are required and then update my scripts as necessary. 

I would also like to work in a basic categorisation based on the headings from the receipt email. For example, frozen, fridge and cupboard categories.

My long term goal is to create a dashboard, potentially with Dash and plotly, which will allow me and my partner to easily monitor our spending and trends.

---

[1]: https://github.com/Richard-D-Todd/Extract-Email
[2]: https://www.heroku.com/
[3]: https://ecederstrand.github.io/exchangelib/
