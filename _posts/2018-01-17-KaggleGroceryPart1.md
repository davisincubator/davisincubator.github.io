---
layout: post
title: "Kaggle Grocery Part 1: Pandas Sampling and Dask"
date: 2018-01-17
author:
 - abbiepopa
---

# What I learned from Kaggle's Grocery Prediction Competition

I recently predicted grocery sales for a [kaggle competition](https://www.kaggle.com/c/favorita-grocery-sales-forecasting).
In this competition, we were responsible for using data from six tables to predict how many units of different items
would sell on future dates. This competitions presented several challenges, including merging multiple tables, working with
a data frame that was larger than RAM, and working with categorical variables that had many classes. This is part one, where
I discuss how I dealt with the large data frame. I will discuss my handling of categorical variables with h2o in
part 2. I will update this post with a link when part 2 is available.

If you want to see my work, my notebooks can be found [here](https://github.com/abbiepopa/kaggle_grocery/tree/master/scripts).

## Part 1: Dealing with data larger than RAM with Dask

When downloading the data they initially don't actually look that big. The largest dataset, train.csv, is only 5 GB on disk.
This is much smaller than some of the image and MRI (brain data) datasets I've worked on in the past, which are in the 100s of
gigabytes (if not over a terabyte). However, a major difference is that image and MRI data sets I generally read in piece by
piece, then transfer them to some other datatype such HDF5. In the case of these spreadsheets I _ideally_ would like to read
in the whole spreadsheet at once. However, even though the csv is 5 GB on disk, it will be larger as a pandas dataframe! This
is because once the columns are typed, many types take more bytes to represent than does plain text. (Note, if you import the
sys library you can get the size of a python object by using the sys.getsizeof(object) command.) In this case the pandas dataframe
becomes larger than my laptop's puny 8 GB of RAM. (Sorry laptop, I shoulda bought you more RAM.) 

I dealt with this in two ways. The first was to read in a random subset of the training data. In this case, the training 
data are currently sorted by date, so I certainly wouldn't want to read in only the first few rows. One could certainly make an argrument
for only reading in the _last_ few rows, since the most recent data is likely the most relevant to the future, but it's worth
learning to read in a random sample so that is what will be discussed here. Reading in a random sample is extremely simple 
with pandas. Simply use the following code where 'n' is the number of rows in your csv and 's' is the number of rows you want
to read in:

```python
import random
import pandas as pd

n = 125497040
s = 10000
skip = sorted(random.sample(range(1,n), n-s))

train = pd.read_csv('train.csv', skiprows = skip)
```

Now you have a sample of training data! This is extremely useful for setting up your scripts and testing to make sure everything
is running properly. Later you can run your script on the full training data on something like AWS cloud computing.

The second resource I would like to highlight is dask.

![alt text|627x612, 20%](https://dask.readthedocs.io/en/latest/_images/dask_horizontal.svg "Dask's horizontal logo")

I originally heard of dask with regard to its distributing computing scheduling capabilities, but dask also provides
collections for bigger data sets. These work by "under the hood" breaking the data into multiple parallel collections. 
The fun part is, the dask dataframe mimics pandas, so most of your standard pandas commands will work on it. Though for 
anything that brings the whole collection together you will have to use .compute(), taking yourself out of the dask 
framework (though you can put the output file back into a dask collection if you need to). Dask has good documentation, so if 
you will use it someday I recommend going to the [dask docs](https://dask.readthedocs.io/en/latest/) for all your dask needs.

Even with dask it is worth noting there is only so much you can do, which is why having access to a server or using AWS can be 
useful, however these options are outside the scope of this blogpost. You can, however, find a great tutorial [here](http://www.grant-mckinnon.com/?p=6). 