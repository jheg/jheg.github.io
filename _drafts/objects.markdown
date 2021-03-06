---
layout: post
title:  "What is an object? (in ruby)"
date:   2014-11-10 16:08
categories: programs
comments: true
excerpt: What is an object? What is meant by everything is an object? (in ruby)
---

I feel slightly anxious writing about such a complex subject as object oriented programming having only been studying ruby for a short time but here goes. 

In ruby everything is an object. Programs are made up of many objects and these objects can communicate and interact with one another. 

An object has 'states' and 'behaviours'. In a real world example lets say our object was a person. Our person might have a name, gender and an age (all states) and be able to speak and meet other persons (behaviours). What our person (object) is called, their age and gender is up to us and what our persons can do is also up to us because we create the blueprint for persons when we write our Person class. It might look something like this:

{% highlight ruby linenos %}
class Person
  @@total_number_of_people = 0

  attr_accessor :name, :age, :gender

  # THIS IS WHERE THE STATES ARE CREATED AND STORED IN INSTANCE VARIABLES (@name, @age, @gender)
  # THE @@total_number_of_people IS A CLASS VARIABLE (more on these in a bit)
  # THEY ARE SET BY THE ARGS WE PASS IN DURING INSTANTIATION (more on that in a bit)
  def initialize(name, age, gender)
    @name = name
    @age = age
    @gender = gender
    @@total_number_of_people += 1
  end
  
  # THIS IS AN INSTANCE METHOD
  def meets(person2)
    if self.age > 18 && person2.age > 18 
      Person.new('baby', 0, ['male','female'].sample)
    else
      puts "Don't do that"
    end
  end

  # ANOTHER INSTANCE METHOD
  def speak
    if self.age > 2
      puts "I can talk!"
    else
      puts "wha wha wha wha!"
    end
  end

  # THIS IS A CLASS METHOD (I'VE NOT MENTIONED THESE AS YET)
  def self.total_persons
    @@total_number_of_people
  end

end
{% endhighlight %}

That is an example of how we create a class that can be instantiated to create as many objects as we like. To actually create a new person (an object or instance of the Person class) we do this:

{% highlight ruby %}
jason = Person.new('Jason', 38, 'male')
vic = Person.new('Victoria', 37, 'female')
{% endhighlight %}

So now I have created me and my girlfiend Victoria and stored us in variables jason and vic. Just like real people jason and vic can interact and magic things can happen when people interact. If you look back on the Person class we wrote we can see just what behaviour me and Vic can get up to. We can also check the worlds population using the class method we created that in turn returns the class variable we created.

<h4>CLASS VARIABLES AND CLASS METHODS</h4>
Already our God like Person class can do some amazing stuff like create people and allow them to do things but our Person class can also have a sense of self too. What I mean is it can keep track of things that are not specific to each object it creates like how many people it has created in total. To do that we need a class variable and a class method to call to get the information (again state and behaviour).

To define a class variable we use two @'s like we did in our Person class above (@@total_number_of_people). To define a class method with simple prepend self. to the beginning of our method name like we did for the total_persons method. 

So now lets check the current worlds population:

{% highlight ruby %}
Person.total_persons
# => 2
{% endhighlight %}

How easy was that? 

OK back to our objects - and by the way our Person class itself is an object and it was created from the class Class (Lets move on). We now have a world population of 2 and those 2 people are objects (jason and vic). jason and vic and interact because we made it so in our class with the instance method meets. This method is almost as amazing as the Person class itself as it too creates new life so long as certain conditions are met. We call it like this:

{% highlight ruby %}
baby1 = jason.meets(vic)
{% endhighlight %}

This is simple a variable called baby1 which is assigned whatever the meets method returns and in this case its a brand new baby (a new object).


<h4>WHY DO WE NEED OBJECTS?</h4>
As the worlds population grows and we start to evolve beyond just breeding and speaking our program becomes more complex. For instance not every one speaks English and even for those that do there are many different regional accents and add to that the fact that each persons vocabulary is likely to be different depending on their age and you soon realise that the human race is incredibly complex. Now imagine if we didn't have our wonderful Person class where we centralize and group many of the elements that make up what a person. Without the Perosn class creating new people would create a huge amount of repetitive code and if we wanted the human race to evolve a third rear facing ear we would need to change every single person in the world one by one.

That's about as much as I feel comfortable in talking about regarding OO at the moment.

<h4>A bit more on Object Oriented</h4>
<a href="http://en.wikipedia.org/wiki/Alan_Kay">Alan Kay</a> likened it to a cellular metaphor. Each object (not class) is a cell. It can do some stuff internally, but only very simple things. In order to collaborate with other cells, it sends out a message and cells that are interested in that message perform their actions when they receive it.

A <a href="http://c2.com/cgi/wiki?AlanKaysDefinitionOfObjectOriented">short read</a> of Alan Kays definition of Object Oriented.


