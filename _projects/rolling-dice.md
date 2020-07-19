---
title: "Rolling Dice: Probabilities and Rolling Script"
excerpt: "Probabilities of outcomes from rolling multiple dice."
header:
  image: /assets/images/Dice.jpg
  teaser: /assets/images/Dice.jpg
classes: wide
gallery:
  - url: /assets/images/dice_rollling_fig1.png
    image_path: assets/images/dice_rollling_fig1.png
    alt: "figure 1"
  - url: /assets/images/dice_rollling_fig2.png
    image_path: assets/images/dice_rollling_fig2.png
    alt: "figure 2"
  - url: /assets/images/dice_rollling_fig3.png
    image_path: assets/images/dice_rollling_fig3.png
    alt: "figure 3"
  - url: /assets/images/x_value_increase.gif
    image_path: assets/images/x_value_increase.gif
    alt: "figure 4"
---

## Dice Rolling
[Project repo on Github][1]

### Context:
I regularly play the popular roleplaying game Dungeons and Dragons with a group of friends. This game is focused around the rolling of various dice to determine player outcomes, in particular 20 sided dice. This got me curious as to how I could calculate the probabilities of outcomes for rolling multiple dice, and rolling dice of various dimensions.

### Aims:
* Plot the probabilities of various dice rolls in Matplotlib.
* Write a script that can take an input of any combination of dice to roll and simulate mutiple dice rolls to output a probability for each outcome possible.

### Gallery
{% include gallery caption="Visuals from the dice rolling notebook" %}

## Software and tools used:
* Jupyter Notebook to similate dice rolls and plot probabilities of various outcomes.

### Status
Completed

## Update: 19/07/2020

### Simulating dice rolls
The first step of the project was simulating dice rolls _n_ times for a specified dice roll. I use the below convention for dice rolls:

_**xDd + m (e.g 2D20 + 5)**_

* _**x: The number of dice rolled in a single roll.**_
* _**d: The number of faces on a die (in reality these are restricted to platonic solids).**_
* _**m: A modifier that is used to add a fixed value to the outcome of rolling xDd.**_

So for example _**2D6**_ means rolling two sixed-sided dice and adding the resulting outcomes. Rolling _**3D4 + 2**_ means rolling three four-sided dice, summing their results and then adding a value of 2.

I created two functions to simulate dice rolls. These functions returned a list of all the outcomes of the rolls. this was then plotted as a histogram. Below is the histogram for rolling three six sided dice, 100,000 times.

![Histogram for outcomes of simulated rolls](https://rdtodd.co.uk/assets/images/Dice-rolling-fig-1.png)

I then calculated the mean and standard deviation for these outcomes. Using this I plotted a normal probability density function using these statistics over a normalised histogram of my data. This shows that these outcomes can be approximated by a normal distribution. This is especially true for a large number of dice being rolled. i.e. for large values of _x_.

![Normalised histogram with PDF plotted over it](https://rdtodd.co.uk/assets/images/Dice-rolling-fig-2.png)

### Calculating probabilities for each outcome of a dice roll
I then wanted to calculate the probabilites for each outcome of a specified dice roll. I did this by using the python module _intertools_ which generated all the combinations possible for the dice rolled and their frequency. I could then calculate the probability for each outcome. This was plotted as a bar chart with a line overlaid to show the shape of the distribution.

![bar chart for the probabilities of each outcome](https://rdtodd.co.uk/assets/images/Dice-rolling-fig-3.png)

The final part of this project was to plot the probability of a specified result ontop of the distribution for that dice roll, as below:

![Probability for specified outcome, on top of its distribtion](https://rdtodd.co.uk/assets/images/Dice-rolling-fig-4.png)

### Functions and classes

The functions below are used to simulate _n_ dice rolls.

```python
def roll_dice(x, d):
    """
    Function to simulate rolling x dice with d number of faces
    """
    roll = 0
    for i in range(x):
        roll += np.random.randint(1, d + 1)
    return roll

def simulate_dice_rolls(n, x, d):
    """This function simulates n dice rolls, for x number of dice, with d number of faces."""
    results = []
    for i in range(n):
        results.append(roll_dice(x, d))
    results = np.asarray(results)
    return results
```

The Class below allows for the attributes of a dice roll to be stored in an object and then use methods to generate the probabilities for each outcome.

```python
class dice:
    """
    The dice object is used to store the x, d and m attributes of a dice roll. Corresponds to a die xDd + m.
    """
    
    def __init__(self, x, d, m=0):
        self.x = x
        self.d = d
        self.m = m
    
    def roll_dice(self):
        """The roll_dice method returns a dice roll outcome as an int based on the x, d and m values of a dice object"""
        roll = 0
        for i in range(self.x):
            roll += np.random.randint(1, self.d + 1)
        return roll + self.m
        
    def roll_dice_multi(self, n):
        """The roll_dice_muti method outputs n dice roll outcomes as a list based on the x, d and m values of a dice object"""
        self.n = n 
        rolls = []
        for j in range(self.n):
            roll = 0
            for i in range(self.x):
                roll += np.random.randint(1, self.d + 1)
                roll = roll + self.m
            rolls.append(roll)
        return rolls
    
    def calc_outcome_probabilities(self):
        combinations = itertools.product(range(1, self.d + 1), repeat = self.x)
        combinations_array = np.asarray(list(combinations))
        row_sums = combinations_array.sum(axis=1)
        outcome, count = np.unique(row_sums, return_counts=True)
        outcome = outcome + self.m
        p = count/len(combinations_array)
        probability_array = np.asarray([outcome, count, p])
        return probability_array.T
```

The probabilities are returned in a NumPy array:

![probability array](https://rdtodd.co.uk/assets/images/probability-array.PNG)

[1]: https://github.com/Richard-D-Todd/Dice-Rolling