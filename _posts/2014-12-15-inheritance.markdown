---
layout: post
title:  "Inheritance"
date:   2014-12-15 17:00
categories: programs
comments: true
excerpt: What, Where, When, Why Inheritance
---

This post assumes you understand how to create a class and what a class is.

<h4>What's inheritance?</h4>

With the exception of <span class="mono">BasicObject</span> ( The uppermost class in Ruby ) all classes inherit from a superclass. To understand this better lets create two initial classes:

{% highlight ruby linenos %}

class Animal
  attr_accessor :name, :gender

  def initialize(name, gender)
    @name = name
    @gender = gender
  end

  def living
    puts "I breathe"
  end
end

class Human < Animal

  def can_drive
    puts "I can probably drive a car at some point in my life"
  end

end
{% endhighlight %}

At this stage we are just trying to visiualize the inheritance heirarchy and what that does for these newly created classes. Our Human class inherits from our Animal class ( denoted by the less than symbol < ) also stated as, our Human class is a subclass of Animal, and Animal is the superclass of Human. Yet another way, the Human class is the child of the parent class, Animal. What this means is any Human objects have access to any methods, as well as its own, defined in the Animal class.

So quick example of this:

{% highlight ruby %}
# new instance of the Human class created
jason = Human.new('Jason', 'Male')
# => <Human:0x000001020133f0 @name="Jason", @gender="Male">

jason.living # => "I breathe"
jason.can_drive # => "I can probably drive a car at some point in my life"
{% endhighlight %}

Clearly we can see that whilst the living method was defined in the parent class our Human object still has access to it, due to inheritance. 

So hopefully that's pretty straight forward at this stage but if we dig a little deeper we can very quickly begin to understand how some of the things we write in ruby just seem to happen. For example if I call <span class="mono">puts</span> on my Human object look what happens:

{% highlight ruby %}
puts jason # => <Human:0x000001020133f0>
{% endhighlight %}

In this example I've called the <span class="mono">to_s</span> method on my Human class jason using <span class="mono">puts</span>, but if I look in the classes I created I didn't define a <span class="mono">to_s</span> method so where did my object find that method and what was it doing outside of Human and Animal?

It was looking up the inheritance chain just as the Human class did when I called <span class="mono">jason.living</span>. First of all my object looked for the living method in Human and when it didn't find it there it looked in its parent class Animal and found it. So what would have happened if it wasn't there? Well it would have looked in Animals parent class (superclass) which happens to be <span class="mono">Object</span> and if it hadn't have found the method there it would have looked in the parent class of <span class="mono">Object</span> which is the end of the line for this line of inheritance as it reaches the empty class <span class="mono">BasicObject</span> which would have returned nil and thrown a noMethod error.

But when we called <span class="mono">puts</span> on jason it must have found the <span class="mono">to_s</span> method somewhere and indeed it did, inside of <span class="mono">Object</span>. What this essentially means is that when we create a new class we automatically inherit all of the methods up the chain. To find out just how many are inherited do:

{% highlight ruby %}
Human.instance_methods.count # => 61
{% endhighlight %}

We can go a step further and see what methods our class has access to by doing <span class="mono">Human.instance_methods</span>

So there we have a great example of inheritance at work.

<h4>How do we deal with state and inheritance?</h4>
The state of our class instances are defined with instance variables. In our above example it was fairly simple as our child class didn't have any of its own instance variables and so just inherited its parent classes initialize method which contained @name and @gender. Therefor when we created a new instance of Human we only needed to pass in the parent classes two instance variables like this:

{% highlight ruby %}
jason = Human.new('Jason', 'Male')
{% endhighlight %}

That's fine but what if the Human class has it's own instance variables, how would we handle that? Lets look at the above code again with a slight variation:

{% highlight ruby linenos %}
class Animal
  attr_accessor :name, :gender

  def initialize(name, gender)
    @name = name
    @gender = gender
  end

  def living
    puts "I breathe"
  end
end

class Human < Animal
  attr_accessor :name, :gender, :job_title

  def initialize(gender, name, job_title)
    super(name, gender)
    @job_title = job_title
  end

  def can_drive
    puts "I can probably drive a car at some point in my life"
  end

end
{% endhighlight %}

Now you'll notice on line 17 a new initialize method with 3 arguments: 

{% highlight ruby %}
def initialize(gender, name, job_title)
  super(name, gender)
  @job_title = job_title
end
{% endhighlight %}

There are a couple of important factors here. Thie above shows us that if your class has instance variables and inherits from a class that also has instance variables then we have to take the arguments the parent class needs for its own instance variables as well as our own. To do this we must do two things. The first is we need to pass the args into the initialize method and the second is we need to handle them using the 'super' keyword. You'll see in the above code we pass the two args for the parent class into 'super' like this super(name, gender) which leads us to the second important factor which is the order we pass these args into super is very important. The order must match the parents intialize methods order so:

{% highlight ruby %}
# if the order in the parent class is ...
def initialize(name, gender)
  @name = name
  @gender = gender
end

# then the order in super in the child class must be ..
super(name, gender)
{% endhighlight %}

If it is not in this order your object might look something like this:
{% highlight ruby %}
# => #<Human:0x000001010d4588 @name="Female", @gender="Vic", @job_title="keeper">
{% endhighlight %}


<h4>When use inheritance?</h4>
When it seems right to seperate elements of an object into different classes for example, when different objects share common states and behaviours it is probably a good time to think about a superclass and subclass the more intracate details

You should think about using inheritance when much of the behaviour and state of an object is generic and when your object needs state or behaviour that is neccessary for the object concerned but not all objects of the class. For example, a Human object and a Snake object could both have a @name, @gender and be able to breathe, all inherited from our Human class but a Human might have a @job_title and a @pay_scale and be able drive a car all of which do not apply to a Snake object and so should'nt apply to Snake. Inheritance here might work well as Human and Snake are both a specialization of Animal.


<h4>How else can we deal with shared behaviour?</h4>
Another way to deal with shared behaviour is through mixins. For example lets say that we also had a Dog, Cat and Monkey class. Now we have a Human, Snake, Monkey, Dog & Cat and we want to give all but the Snake the ability to jump. We could use inheritance but we would need to write the same jump method into all the classes that needed it (NOT <a title="Do not repeat yourself">DRY</a>) and we couldn't write it in our Animal class becase then Snake would be able to jump and I don't think Snakes can jump. The answer is to write the method once and place it inside a module and then when we needed it we can include it in our class. Like this:

{% highlight ruby %}
module Jumpable
  def jumps
    puts 'I can jump!'
  end
end

class Human
  include Jumpable
end

class Snake

end

class Monkey
  include Jumpable
end
# ...
{% endhighlight %}

In the above example our Human and Monkey objects can jump but our Snake objects cannot.

<h4>When to use inheritance and when to use modules?</h4>
In the examples in this post it was perfect for both inheritance and mixins. Inheritance was perfect for sharing the really general shared states and behaviours like @name, @gender and the breathe method and using the mixin approach was perfect for distributing the jump behaviour for some, but not all, of our animals. I can also think about modules on a wider scale than I can inheritance because I might be able to reuse the Jumpable module throughout my program for instance I might have a ToyFrog class in the zoo giftshop that could use my Jumpable module. Inheritance on the other hand I think of in more of a 'is a specialist of' kind of way - Human, Snake, Monkey, Dog & Cat are all specialists of the Animal class - whilst they need the basics from Animal they have to have their own additional states and behaviours.
