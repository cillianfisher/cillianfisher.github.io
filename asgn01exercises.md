# ASGN 01

This assignment covers the basics of writing code in Jupyternote books, loading and saving data, and then working with the basic python structures: strings and lists. You'll also write some functions. The idea is to get familiarity with writing code and thinking and planning out solutions programmatically.

Make sure you read the instructions in this folder. The problems in this file are only part of the assignment.

My goal is for everyone to get 100% on this assignment. If you submit the assignment before the second week of class starts, email us and we will give you feedback so that you can revise your solutions and improve your grade. 

I would suggesting opening a tab in your browser to chapter 1.7 of our [textbook](ledatascifi.github.io) and this [explainer](https://nbviewer.org/github/jakevdp/WhirlwindTourOfPython/blob/master/06-Built-in-Data-Structures.ipynb). 

Remember,
1. There are usually MANY ways to achieve things programatically. 
2. You can use external resources to try to figure out things, like saving CSV files. Google is your friend!
3. **You must NOT work with other students on this assignment.** The goal of this assignment is for you to learn how to find and use resources (Google, Stackoverflow, ledatascifi.github.io) so that you can solve problems. If you can't figure something out from internet sleuthing, email me and your TA (CC us both). 
    
If you work with another student on this assignment, that is considered cheating and I will follow the standard university procedures. 

## Q1. Using Jupyterlab

The next cell is Markdown, but it should be python. Convert it, then execute it.


```python
for i in range(4,10): 
    print(i)
```

    4
    5
    6
    7
    8
    9


## Q2 Loading data.

There is a simple csv file in the data subfolder of the repo. Load it and print the last 8 rows. The formatting of how you print it does not matter.


```python
# work on exercise here
# HINT: Use pandas
import pandas as pd
file_path = 'data/wordle.csv'

data = pd.read_csv(file_path)
print(data.tail(8))

```

         whole_word pos1 pos2 pos3 pos4 pos5
    2307      refit    r    e    f    i    t
    2308      aorta    a    o    r    t    a
    2309      adult    a    d    u    l    t
    2310      judge    j    u    d    g    e
    2311      rower    r    o    w    e    r
    2312      artsy    a    r    t    s    y
    2313      rural    r    u    r    a    l
    2314      shave    s    h    a    v    e


Now save _just_ the last 8 rows to a csv file in this folder, and call it "temp.csv". The file should be nine lines long. (The variable names and the eight rows.)


```python
# work on the exercise prompt here


```

## Let's work on some questions with this dataset:

These are daily sales for the 28 days of February 2015 (which began on a Sunday):


```python
daily_sales = [86,100,67,98,56,89,91,
               75,93,51,51,69,97,71,
               82,79,99,64,71,96,88,
               63,79,66,94,64,62,90]

```

## Q3 Slicing and working with lists. What is the...

...total sales in first two weeks?


```python
# work on exercise here
first2weeks= daily_sales[:14]
sum(first2weeks)
```




    1094



## Q4. Slicing and working with lists. What is the...
...average daily sales for the last 10 days?


```python
# work here
last10days = daily_sales[-10:]
average_last10days = sum(last10days) / len(last10days)
print(average_last10days)
```

    77.3


## Q5. Slicing and working with lists. What is the...

...average sales on Tuesdays?

DO NOT MANUALLY COPY THE NUMBERS FROM ABOVE. The point is that your database could change and you need it to have a way to automatically pull Tuesdays still. You can assume the first date in the dataset is always Sunday. 


```python
# work here

tuesdaysales = daily_sales[2::7]

avg_tuesdaysales = sum(tuesdaysales) / len(tuesdaysales)

print(avg_tuesdaysales)
```

    70.75


Bonus (ungraded) challenge:
- For the month, which day of the week did you sell the most on average (and how much was it)? 
- Answer this without copy-pasting your Q5 answer 7 times. (One option: Use a for loop.)


```python
# work here on bonus

```

## Q6 Monster sales

- What is the most you sold in a day?
- And what is the date of that? (Feb 5? Feb 22? Feb 25?)

Be careful with the second part of this!


```python
# work here
max(daily_sales)
print(max(daily_sales))
max_sales_index = daily_sales.index(max(daily_sales))
print("Feb",max_sales_index + 1)


```

    100
    Feb 2


## Q7. Functions, for loops, and if-statements

Write a function that takes a list, `list1` and returns the fraction of elements in the list above that list's average.

Hint: You'll probably use a "for-loop" and an "if-statement".


```python
def big_day(list1):
    #####################################################
    # work on the exercise prompt between these lines
    list_avg = sum(list1) / len(list1)
    above_avg_count = 0
    for element in list1:
        if element > list_avg:
            above_avg_count +=1

    fraction_above_avg = above_avg_count / len(list1)
    return fraction_above_avg
    #####################################################

# leave this, its a test unit:
print(big_day(daily_sales)) # the answer should be 0.5357142857142857
```

    0.5357142857142857


## Q8. Functions, for loops, and if-statements

Write a function that counts how many weeks you sold more on Friday than Saturday. Assume the function gets a list of lists: The inside lists always have 7 elements corresponding to Sunday through Saturday.

The inputs will look like this:


```python
weekly_sales = [daily_sales[:7],
                daily_sales[7:14],
                daily_sales[14:21],
                daily_sales[21:28]]
weekly_sales
```




    [[86, 100, 67, 98, 56, 89, 91],
     [75, 93, 51, 51, 69, 97, 71],
     [82, 79, 99, 64, 71, 96, 88],
     [63, 79, 66, 94, 64, 62, 90]]




```python
def big_fridays(list_of_weekly_sales):
    #####################################################
    # work on the exercise prompt between these lines
    bigfriday_week = 0
    for week in list_of_weekly_sales:
        if week[4] > week[5]:
            bigfriday_week +=1
    #####################################################

# leave this, its a test unit:
big_fridays(weekly_sales) # you know what the answer should be...
print(big_fridays(weekly_sales))
```

    None


## Q9. Functions, for loops, and if-statements

Write a function that counts how many months you sold more than your competitor. Assume the function gets a list of lists: The inside lists contain two elements: your sales for that month, and a competitor's sales.

The inputs will look like this. You sold 86 in January but your competitor sold 100. 


```python
monthly_sales_vs_competitor = [
    ["Jan",86,100],["Feb",75,93],["Mar",82,79],["Apr",63,79],
    ["May",67,98],["Jun",51,51],["Jul",99,64],["Aug",66,94],
    ["Sep",56,89],["Oct",69,97],["Nov",71,96],["Dec",64,62]]

monthly_sales_vs_competitor
```




    [['Jan', 86, 100],
     ['Feb', 75, 93],
     ['Mar', 82, 79],
     ['Apr', 63, 79],
     ['May', 67, 98],
     ['Jun', 51, 51],
     ['Jul', 99, 64],
     ['Aug', 66, 94],
     ['Sep', 56, 89],
     ['Oct', 69, 97],
     ['Nov', 71, 96],
     ['Dec', 64, 62]]




```python
def outselling_the_baddies(inList):
    #####################################################
    # work on the exercise prompt between these lines
    months_outsold = 0
    for month_data in inList:
        my_sales, competitor_sales = month_data[1], month_data[2]
        if my_sales > competitor_sales:
            months_outsold +=1
    #####################################################

# when you run this, you should get the right answer:
outselling_the_baddies(monthly_sales_vs_competitor) 
```

## Q10: Being a friendly human on GitHub

Go to https://github.com/settings/profile and make sure your "Name" is your actual name (not your username) and upload a profile picture. 


## Getting full credit 

Cool - you finished the above problems! To get 100% on this assignment, make sure you read the instructions in this folder. The problems in this file are only part of the assignment.
- One thing in the instructions is "**IMPORTANT: When you are done with the problems, clear the output and rerun all cells.**"
- But there are other things to do as well. 

