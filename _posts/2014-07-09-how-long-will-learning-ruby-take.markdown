---
layout: post
title:  "How long will it take to learn ruby?"
date:   2014-07-09 19:37
categories: beginning
comments: true
---

The structure of the Tealeaf bootcamp course I'm taking is split into 3 modules each I'm told is progressively harder than the previous. I am on the first module 'Introduction to Ruby and Web Development' which is split into a pre course followed by 4 lessons.

I had a bit of a moment today which made me think that although I'm not in the classroom environment a self paced online course may actually be a better option anyway. I ran into a problem where I was just struggling to understand a peice of code I wrote:

{% highlight ruby linenos %}

words = ["demo", "none", "tied", "evil", "dome",
         "mode", "live", "fowl", "veil", "wolf", 
         "diet", "vile", "edit", "tide", "flow", "neon"]

result = {}

words.each do |word|
  key = word.split(//).sort.join
  if result.has_key?(key)
    result[key] << word
  else
    result[key] = [word]
  end
end

result.each do |k,v|
  puts "--------"
  puts "#{k}: #{v}"
end

{% endhighlight %}

Basically the above code is taking the letters from each string in the 'words' array, sorting them in alphabtical order as an anagram and then adding them to a hash. For example the string 'none' is first split as 'enno' and then the program checks to see if the key 'enno' already exists and if it does adds the word 'none' to that key. If the key doesn't exist it will create a new key using the key and then asign 'none' to the newly created key. phew!

The output looks something like this:

--------<br />
demo: ["demo", "dome", "mode"]<br />
--------<br />
enno: ["none", "neon"]<br />
--------<br />
deit: ["tied", "diet", "edit", "tide"]<br />
--------<br />
eilv: ["evil", "live", "veil", "vile"]<br />
--------<br />
flow: ["fowl", "wolf", "flow"]<br />

I understood everything except the else conditional starting on line 25. Thanks to the friendly folk in the #ruby irc channel it was explained to me that:
{% highlight ruby %}
result[key] = [word]
{% endhighlight %}

looks to see if key already exists and if it doesn't it will create it and then asign word to it.

<h2>Taking time to understand before moving on</h2>
Even though I started the day planning on getting stuck into lesson 1 I spent most of my day going over the pre-course stuff a second time to make sure I didn't miss anything the first time. I also spent a good hour or two taking the time to understand that specific peice of code. 

Maybe if I was in the classroom environment there would have been a pressure to move on to keep pace with the course and I might have felt that I was moving on before I was ready. On the other hand maybe one of the tutors would have sat with me until I understood fully the code and it would only have held me up for 20 minutes. 


<h2>Thanks to:</h2>
Each time I post I'll aim to thank anyone that has helped me along the way. Today workmad3 DefV & <a href="http://apeiros.me/">apeiros</a> helped me in the ruby channel on irc. I was struggling to understand the syntax for adding a key and a value based on a conditional if else statement. 