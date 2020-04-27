---
permalink: /projects/
title: "Projects"
classes: wide
---

# Links to my projects below:

## Grocery Shopping Email Analysis
Github link goes here

### Context:
I do my regular grocery shops online though the British supermarket chain ASDA. When the delivery is packed and on the way an email is sent to me with a breakdown of which items are unavailable, substituted and sent as ordered, along with the prices and quantities. I want to be able to collect this data and Analyse my spending on groceries.

### Aims:
The aim of this project is to get data from the receipt email sent for my online food shops. This data will then be collated in a database ready for analysis. I would like to investigate the following:
* My spending habits and how these change over time.
* Places were I could potentially save money, or are overspending.
* The common items I buy, and items I have bought once and not bought again.

### Software and tools used:
* Python is used to extract the contents of the email files, convert them to Pandas DataFrames and then export to .CSV files and a PostgreSQL database. It will then be used for analysis and visualisation, using Pandas, Matplotlib and Seaborn to help me investigate my spending habits.
* PostgreSQL is used as a database to hold the data extracted from the emails. Since I will have multiple tables, SQL can be used to query my data before analysis in Python.

### Status:
Work in progress

## Dice Rolling
Github link goes heere

### Context:
I am planning on playing the popular roleplaying game Dungeons and Dragons (DnD) with my friends. This game is focused around the rolling of various dice, in particular 20 sided dice, and I would like to investigate the probabilities of a variety of dice rolls.

### Aims:
* Plot the probabilities of various dice rolls in Matplotlib.
* Write a script that can take an input of any combination of dice to roll and simulate mutiple dice rolls to output a probability for each outcome possible.

## Software and tools used:
* Python to similate dice rolls and plot probabilities of various outcomes.