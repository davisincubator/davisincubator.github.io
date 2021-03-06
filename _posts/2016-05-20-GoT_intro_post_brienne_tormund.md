---
layout: post
title: "Shipping Thrones"
date: 2016-05-20
author:
  - kylefrankovich
---
Like many viewers of this past Sunday's episode of Game of Thrones ("Book of the Stranger"), I caught our favorite redhead from North of The Wall Tormund Giantsbane making eyes at the badass warrior Brienne of Tarth. I was happy to see that the internet in general was shipping them as hard as I was, but I was curious if I'd be able to get an objective measure of our collective desire for this new couple to happen from a recent data project I've been working on.

## **The Project**
For the past month, I've been collaborating with my good friend [Emily](http://davisig.org/members/emilyhalket/) on a web scraping project for one of our favorite shows, Game of Thrones. We chose this project because we're both huge nerds and we also wanted to get some experience in generating large datasets that would allow us to do a bunch of cool analyses and visualizations. A side benefit is that the datasets would be about dragons and Arya instead of the usual brain data we're used to.

My idea for the project was to collect twitter data relating to the show, and to then use natural language processing to extract information about each episode to see if we can learn anything about how people are reacting to the show on social media.

### **Data Collection**
For the project, we've been using [tweepy](https://github.com/tweepy/tweepy), a really great package for python that allows you to access the twitter API. We collect the data through two types of calls to the API: [stream](https://dev.twitter.com/streaming/overview) and [search](https://dev.twitter.com/rest/public/search). There are pros and cons to both methods, and Emily or I may bore you with the details in a later writeup. For this post, I'm providing a preview of some of the data generated by the search API.

To collect the data, I wrote a python script that searches for the Game of Thrones hashtag within a 48 hour period around each episode premiere. On average, we're getting about 457,000 tweets per episode through the search API (stream gives us better resolution: we collect live streaming data during each episode premiere and get about the same amount of data within 1-2 hours).

This is a lot of data to handle for each episode, so I also wrote a few python helper functions that can load the data, reduce it to a more manageable size (by saving just the data I'm interested in for the current analysis and archiving the rest for later use), and output things like character mention counts per episode into .csv files I can later load into R for data visualization.

## 😍 **Brienne and Tormund** 😍

<iframe src='https://gfycat.com/ifr/GlitteringEntireHoiho' frameborder='0' scrolling='no' width='640' height='359.55' allowfullscreen></iframe>

> Brienne not really reciprocating...

As mentioned above, I was curious if we would see evidence of the internet's obsession with this nascent couple in our twitter data. Below is a plot of Brienne and Tormund's mentions in our datasets from the first four episodes:

<iframe width="640" height="559.55" frameborder="0" scrolling="no" src="https://plot.ly/~kyle.frankovich/7.embed?modebar=false&link=false"></iframe>
> percentage of character mentions within each episode's dataset

As you can see, both characters received a huge boost relative to their previous few week's mention frequencies once they locked eyes (Brienne also had a big moment in episode one when she saved Sansa from the Boltons, as we see in her relatively high numbers for that episode). So it looks like [all](http://www.huffingtonpost.com/entry/brianne-and-tormund-romance-game-of-thrones_us_5739cb2ee4b08f96c1839172) [of](http://www.popsugar.com/entertainment/Brienne-Tormund-Romance-Game-Thrones-41333748) [the](http://hellogiggles.com/brienne-tormund-game-of-thrones-shipping/) [articles](https://www.buzzfeed.com/jennaguillaume/sail-this-ship-to-bear-island?utm_term=.akM12M76r#.hyEE5walM) [that](http://www.techinsider.io/game-of-thrones-brienne-tormund-shipping-2016-5) came out following the episode were actually on to something: people are super curious about what's going to happen with these characters.

This is just a small exploration of all of the data we've collected so far. We have a lot of analyses planned, and we're really excited to see what else we can explore in the coming weeks.
