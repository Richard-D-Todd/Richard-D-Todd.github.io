---
title: "Online Groceries: Scrapping Receipt Email and Analysing Spending"
excerpt: "Looking into my groceries spending."
header:
  image: /assets/images/online-groceries.jpg
  teaser: /assets/images/online-groceries.jpg
classes: wide
gallery:
  - url: /assets/images/groceriesfig1.png
    image_path: assets/images/groceriesfig1.png
    alt: "Total spend per order"
  - url: /assets/images/groceriesfig2.png
    image_path: assets/images/groceriesfig2.png
    alt: "Total spend per order"
  - url: /assets/images/groceriesfig3.png
    image_path: assets/images/groceriesfig3.png
    alt: "Spending by Month with Average"
  - url: /assets/images/groceriesfig4.png
    image_path: assets/images/groceriesfig4.png
    alt: "Proportion of delivered, substituted and unavailable items"
---

## Grocery Shopping Email Analysis
[Project repo on Github][1]

### Context:
I do my regular grocery shops online though the British supermarket chain ASDA. When the delivery is packed and on the way an email is sent to me with a breakdown of which items are unavailable, substituted and sent as ordered, along with the prices and quantities. I want to be able to collect this data and analyse my spending on groceries.

### Aims:
The aim of this project is to get data from the receipt email sent for my online food shops. This data will then be collated in a database ready for analysis. I would like to investigate the following:
* My spending habits and how these change over time.
* Places were I could potentially save money, or are overspending.
* The common items I buy, and items I have bought once and not bought again.

### Gallery
{% include gallery caption="Visuals from the Analysis Notebook" %}

### Software and tools used:
* Python is used to extract the contents of the email files, convert them to Pandas DataFrames and then export to .CSV files and a PostgreSQL database. It will then be used for analysis and visualisation, using Pandas, Matplotlib and Seaborn to help me investigate my spending habits.
* PostgreSQL is used for a database to hold the data extracted from the emails. Since I will have multiple tables, I will use SQL statements inline in a Jupyter notebook to query the tables.

### Status:
Work in progress

[1]: https://github.com/Richard-D-Todd/Extract-Email