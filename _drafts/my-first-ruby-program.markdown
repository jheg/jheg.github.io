---
layout: post
title:  "Rock, Paper Scissors"
date:   2014-08-13 19:32
categories: programs
comments: true
---

So the task was:

<i>To build from scratch, the game Rock Paper Scissors in ruby from what I have learnt from the course so far.</i>

This is the first time I have essentially started with a blank editor and written my own code to achieve a game that is RPS. I started by writing some psuedo code of how I thought it works logically.

- User chooses a an option of P/R/S
- comp selects one at random
- a comparison is made 
- the result is output
- play again?

There was a couple of things I was unsure of at the start but for the most part I was confident I could do it without having to refer to the solution video and the solution code which I'll post at the end of this post. 

I was unsure of two things:

1. How to get the computer to randomly select one of the three options 
2. How I would take a letter from the user using the gets.chomp method and compare it to the computers random value.

For my first problem I simply googled 'how do I randomly select an array element in ruby' and stackoverflow held the answer using the .sample method on the array. 

The second problem I assigned user input using gets.chomp to a variable called ans and then ran a conditional statement on its value and depending on the statement assigned a new variable called users_choice to position in the objects array that held the 3 options. Hopefully the code will make this a little clearer.

{% highlight ruby linenos %}

require 'pry'

def say(msg)
  puts "--- #{msg} ---"
end

objects = %w(Paper Rock Scissors)
again = 'y'

while again == 'y'

  puts "Choose One: (P/R/S)"
  ans = gets.chomp
  ans.upcase!

    while ans != 'P' && ans != 'R' && ans != 'S'
      puts "Choose One: (P/R/S)"
      ans = gets.chomp
    end

    if ans == "P"
      users_choice = objects[0]
    elsif ans == "R"
      users_choice = objects[1]
    else
      users_choice = objects[2]
    end
    

  comp = objects.sample
  say("You chose #{users_choice}")
  say("Computer chose #{comp}")


    if users_choice == comp  
      puts "You picked #{users_choice} and computer picked #{comp}"
      puts "its a draw!"
    elsif
      users_choice == "Paper" && comp == "Rock"
      puts "#{users_choice} covers #{comp}"
      puts "You win!"
    elsif users_choice == "Paper" && comp == "Scissors"
      puts "#{comp} cuts #{users_choice}"
      puts "You lose!"
    elsif users_choice == "Rock" && comp == "Paper"
      puts "#{comp} covers #{users_choice}"
      puts "You lose!"
    elsif users_choice == "Rock" && comp == "Scissors"
      puts "#{users_choice} blunts #{comp}"
      puts "You win!"
    elsif users_choice == "Scissors" && comp == "Paper"
      puts "#{users_choice} cuts #{comp}"
      puts "You win!"
    elsif users_choice == "Rock" && comp == "Scissors"
      puts "#{comp} blunts #{users_choice}"
      puts "You lose!"
    end

puts "Play again? (Y/N)"
again = gets.chomp

end

{% endhighlight %}

and there we have it a fully working ruby program that I built from scratch! This feels like a real landmark and I'm going to celebrate with a lovely cuppa before checking the solution code in case it puts my effort to shame.




