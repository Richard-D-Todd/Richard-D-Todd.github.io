---
title: "Scheduling My Dashboard"
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

Today I want to go over the process for setting up my groceries dashboard to automatically get data, and refresh, without manual input.

I have been looking into how to use the crontab tool on the raspberry pi to automate the launching and refreshing of scripts. 

## Automating the email extract
The first step in automating the collection of my data from my receipt emails is creating a shell script to activate the virtual Python environment on my raspberry pi and then launch the *extract_from_exchange.py* Python script.

I connected to the raspberry pi via SSH and then entered the below command.

```
sudo nano run_exchange_extract_script.sh
```
This created a BASH shell file. I then typed the below to launch the correct virtual environment and run the Python script.

```Shell
#! /bin/bash

echo 'activating virtual environment'
activate () {
        . groceries_venv/bin/activate
}
activate
echo 'launching script'
cd 'Extract From Exchange'
python extract_from_exchange_script.py
```

the script is then turned into an executable file using the below command.
```bash
sudo chmod +x run_exchange_extract_script.sh
```

Now to run this file I just enter the below.

```
sh run_exchange_extract_script.sh
```
I then set a Cronjob to run the above script once an hour between 10:00 to 18:00 every day. This means that I do not need to manually run the script after every groceries delivery anymore. To set up a Cron job I typed.

```
crontab -e
```

Then I set up the automation with the below.

```
0 10-18 * * * cd /home/pi/Documents/Programming/Groceries-Analysis && ./run_exchange_extract_script.sh
```

The above starts with a the time the tast should run. So 0 is the says at 0 minutes past the hour, 10-18 says between the hours of 10am and 6pm and then *** leaves the other fields blank. The command is then sh to run a shell script followed by the path of the script.

## Automating the dashboard refresh
To refresh I dashboard I decided that I would have the dashboard script run constantly on my raspbery pi, then once an hour my dashboard updates. This is done through the Interval element in the Dash Python library. This means that any call back with this element as an input will refresh after the specified interval (in milliseconds).

```python
dcc.Interval(
    id='interval_component',
    interval=3600000, #1 hour in milliseconds
    n_intervals=0
```

An example of how this is used is to query the database for the first figure on the order overview tab.

```python
@app.callback(
    Output(component_id='total_per_delivery', component_property='figure'),
    [Input(component_id='total_by_month', component_property='selectedData'),
    Input(component_id='select_month_type', component_property='value'),
    Input(component_id='interval_component', component_property='n_intervals')]
)


def create_graph_1(selected, month_type, n):
    df = pd.read_sql_table('order_details', con=engine)
```

Because the interval component is an input of this call back, and the input is called in the function *create_graph_1*, the function will refresh once an hour. This in turn will query the database with the Pandas *read_sql_table* function. It will also update the graph created in this function.

My using this interval as an input to all my call backs everything will refresh once an hour. Most of these refreshes will lead to no change, until there is a change to the database (a new order has been added.)

So by using these two technique for scheduling refreshs my dashboard is now automated. The only change I would like to make is to add a error handling system to the extract_from_exchange Python script, so that it can accurately log errors when it is unable to run correctly or data is not as expected.