---
title: "Update on Groceries Dashboard"
categories:
- Blog
tags:
    - Blog
    - Learning
    - Update
    - Python
    - Project
    - Visualisation
classes: wide
---

First update in what feels like forever. I have made much progress on my groceries dashboard since my last update. I will fully update the project page but I will outline the changes in this post!

## Hosting the database and dashboard on my Raspberry Pi
Last year I bought a Raspberry Pi 4 single-board computer. This is my first time using a linux based system and so I have been picking up smoe knowledge of using bash terminals. I decided that I would be able to use the pi to host my PostgreSQL database and also host the Dash dashboard. The main reason for this is that the RaspberryPi has a very low power draw compared to my desktop pc and so I can leave it running the dashboard script.

To run the dashboard I wrote a bash shell script to launch the correct virtual environment and then run the dashboard script, index.py, which runs the server.

The next thing I want to set-up is a CRON job or another technique to schedule the script which extrats the email data and loads into the database. which brings me to the next big change, a wrote a script to connect to my email account directly rather than reading saved .eml email files.

## Extracting data from outlook directly
I wanted to automate the process of collecting the data from my receipt emails. To do this I started working with the library exchangelib. The process for extracting the data is now:
1. I set-up as rule in my inbox so that when it sees an email with the subject line associated with the receipt emails it moves it to a specific folder.
2. The script uses the exchangelib library to connect to my microsoft email account.
3. The contents of each email in my 'ASDA receipt emails' folder within my email account are stored in a list. I then iterate through each email, extracting the received date, subject line and body text.
4. As with the script to extract the data from .eml files, the body of the email is parsed and the data stored in Pandas dataframes.
5. Once an email has been processed and the data stored in my database the email is moved to a 'processed' subfolder.

The advantage of doing things this way is that I can run this script from my Raspberry Pi without having to manually save email files. I just need to schedule this task and create an error logging and notification system then i can leave this running without my input.

## Changes to the dashboard
The only addition I have made is to update the home tab with a cumulative line plot for all my orders from the beginning of me ordering from ASDA.

## What comes next?
As mentioned I plan to schedule the import task on the Raspberry Pi so that this system can be 100% automated. I also plan to make improvements to the dashboard itself. I have been learning about timeseries analysis on my apprenticeship and so want to implement some changes to my time series plots. I am thinking of using a rolling mean of some kind aswell as some forecasting, as my data is fairly stationary.

Another thing I want to improve is a tab that looks at how much I am spending on certain categories of products. to do this I want to classify the products I have ordered so far into several categories. To do this I plan on writing a Python script which pulls a list of all unique products and then ask me what their tier 1, 2 and 3 categorisations are. one all the products have been categorised I would import this into a new table which has all the category data. I would then repeat this process semi-regularly to make sure all products have been categorised.


