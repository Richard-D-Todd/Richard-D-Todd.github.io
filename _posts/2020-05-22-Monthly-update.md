---
title: "First Month (and a Half) Update"
categories:
  - Blog
tags:
  - Blog
  - Learning
  - Update
  - Python
  - Project
classes: wide
---
it has now been a month since my last update. I have spent the last month mostly working on a project to grab the data from my online grocery orders and load them into a PostgreSQL database, before visualising my spending. I have also made a small project to simulate rolling multiple sets of dice.

In terms of reading I have read the first two chapters of the 'Python Datascience Handbook' online textbook. I have also finished the IBM course 'Databases and SQL for Data Science'. For now I have ended my Coursera subscription, as I found myself spending most of my time reading and working on my projects. I do however intend to eventually complete the remaining four courses of the IBM Data Science Professional Certificate.

### Groceries Analysis Project
I do the majority of my grocery shopping online via the superkmarket chain ASDA. I wanted a way to track my spending on groceries and be able to also visualise my spending habits. This project sparted with me wondering if I would be able to extract the information form the e-receipts that ASDA email the day of the delivery. This email details the below:
1. The subtotal and total for the delivery.
2. The items that were ordered.
3. Items that are substituted and the substitutions made.
4. Products that are uavailable and can't be delivered.
5. Any multibuy savings made.
6. Quantity and cost for each item.

More information on the project can be found on the project page [here][1] and the Github repository can be found [here.][2]   
In this project I have learnt the following so far:
1. How to read .eml files in Python.
2. How to use loops and filters to break up large strings.
3. Export Pandas Dataframes to CSV files and to PostgreSQL databases.
4. Use SQL statements directly from a Jupyter Notebook to query my PostgeSQL database.
5. Plot simple graphs in Matplotlib, within a Jupyter Notebook.

### Dice Rolling Project
I have a regular session of the roleplaying game Dungeons and Dragons. In this game a key mechanic is rolling of a 20 sided die to determine the outcome of player's actions and other polyhedral dice to make attacks and interact with the world. This got me thinking about the probabilities involved when rolling multiple dice and polyhedral dice with relatively large numbers of faces. I therefore sorted a Jupyter Notebook, where I have:
1. Calculated probabilities of dice roll outcomes.
2. Simulated rolling a large number of dice.
3. Plotting the above results to see what the probability distributions look like.

More information can be found on the project page [here][3] and the Github repository can be found [here.][4] In this project so far I have learnt the following:
1. How to write functions using loops to simulate rolling large numbers of dice.
2. Plotting histograms in Matplotlib.
3. Using SciPy Stats package to calculate the probability Density Function (PDF) for a specified mean and standard deviation.
4. How to plot a PDF in Matplotlib, against a normalised histogram.
5. Use itertools to produce a list of combinations of two different sets of numbers. This was used to represent rolling multiple dice and calculating the outcomes.
6. Using vectorised operations on Numpy arrays to calculate the probabilities of each outcome for a specified dice roll.
8. Writing Classes, in particular creating a dice class that can store the various attributes of dice in an object, and then calculate probabilities based on these attributes.

[1]: /projects/online-groceries
[2]: https://github.com/Richard-D-Todd/Extract-Email
[3]: /projects/rolling-dice
[4]: https://github.com/Richard-D-Todd/Dice-Rolling