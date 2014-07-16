---
layout: post
title:  "Persevering with fixing my ruby program"
date:   2014-07-16 14:37
categories: perseverence
comments: true
---

I had the following exercise to complete as part of my pre course at Tealeaf:

exercise explanation goes here

I wrote the following code and it worked!

code goes here

However I was concerned that this only worked when the hash only had 1 pair. What if I added a another pair to the hash and a new array of data to the array will it still work? 

example code here  of it not working and assigning nil values

I tried this:

code here

That works to an extent but only when there are two pairs in the hash so I needed to find another way. I needed to somehow check to see if the first array was empty and if it was delete it from the array before looping through once more. I headed on over to ruby docs and looked through the available array classes and re wrote my program:


Problem fixed!

<h2>Thanks to:</h2>
canton7, centrx, jhass, apeiros in the ruby channel on irc & gustavo in the campfire chat room at tealeaf.


