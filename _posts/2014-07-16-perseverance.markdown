---
layout: post
title:  "Perseverence"
date:   2014-07-16 14:37
categories: perseverence
comments: true
---

I had the following exercise to complete as part of my pre course at Tealeaf:

<p class="question">Create the data structure that you created in the previous example for Joe Smith using loops instead of assignment. Some helpful methods might be the Array shift and first method.</p>

This was the solution:

{% highlight ruby %}
contact_data = ["joe@email.com", "123 Main st.", "555-123-4567"] 
contacts = {"Joe Smith" => {}} 
fields = [:email, :address, :phone] 

contacts.each do |name, hash| 
  fields.each do |field| 
    hash[field] = contact_data.shift 
  end 
end
{% endhighlight %}

I wanted to know if this still worked if there was more than one pair in the hash and more elements in the contact_data array. 

I changed the hash and array as follows:

{% highlight ruby %}
contacts = {"Joe Smith" => {}, "Jason Hegarty" => {}, "Vic Rance" => {}} 
contact_data = [
 ["joe@email.com", "123 Main st.", "555-123-4567"],
 ["jason@email.com", "124 Main st.", "555-123-4567"],
 ["vic@email.com", "125 Main st.", "555-123-4567"]
] 
{% endhighlight %}

but this didn't work and just assigned nil to each of the keys in the 2nd and 3rd hash pair.

I tried another way but it only worked when there are two pairs in the hash so I needed to find another way. I needed to somehow check to see if the first array was empty and if it was delete it from the array before looping through once more. I headed on over to ruby docs and looked through the available array classes and re wrote my program:

{% highlight ruby %}

contacts.each do |name,hash|
    fields.each do |field|
        if contact_data[0].empty?
          contact_data.delete(contact_data.first)
          hash[field] = contact_data[0].shift
          puts "-----"
          puts contact_data
          puts "-----"  
        else
          hash[field] = contact_data[0].shift
          puts "-----"
          puts contact_data
          puts "-----"
        end
    end
end

puts contacts
{% endhighlight %}

Problem fixed! The puts were only present for the purpose of me checking the state of contact_data after each iteration.

I also posted this in the course forum and Chris posted a leaner solution and giving me a sharp reminder there is much to learn in becoming proficient in ruby:

{% highlight ruby %}


counter = 0
contacts.each do |name, hash|
  fields.each_with_index do |field, idx|
    hash[field] = contact_data[counter][idx]
  end
  counter += 1
end

puts contacts

{% endhighlight %} 

<h2>Thanks to:</h2>
canton7, centrx, jhass, apeiros in the ruby channel on irc, gustavo in the campfire chat room at tealeaf and Chris on the course forum. 


