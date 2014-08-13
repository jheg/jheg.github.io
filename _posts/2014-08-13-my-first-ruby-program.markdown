---
layout: post
title:  "Rock, Paper Scissors"
date:   2014-08-13 19:32
categories: programs
comments: true
excerpt: How I built from scratch my first ruby program Rock Paper Scissors
---

So the task was:

<i>To build from scratch, the game Rock Paper Scissors in ruby from what I have learnt from the course so far.</i>

This is the first time I have essentially started with a blank editor and written my own code to achieve a game that is RPS. I started by writing some psuedo code of how I thought it worked logically.

- User chooses an option of P/R/S
- comp selects one at random
- a comparison is made 
- the result is output
- play again?

There were a couple of things I was unsure of at the start but for the most part I was confident I could do it without having to refer to the solution video and the solution code which I'll post at the end of this post. 

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

...and there we have it, a fully working ruby program that I built from scratch! This feels like a real landmark and I'm going to celebrate with a lovely cuppa before checking the solution code in case it puts my effort to shame.

<h2>The solution</h2>

I'll concentrate on sections of the solution which do the same thing differently:

I stored the options in an array whereas the solution uses a hash in a constant. Also I used a while loop to check the user input matched the required input whereas the solution uses begin end until:
{% highlight ruby %}

CHOICES = {'p' => 'Paper', 'r' => 'Rock', 's' => 'Scissors'}

loop do
  
  # player picks
  begin 
    puts "Pick one: (p/r/s)"
    player_choice = gets.chomp.downcase
  end until CHOICES.keys.include?(player_choice)

end
{% endhighlight %}

Next, the computers choice is declared in a similar way to how I did it using the sample method:

{% highlight ruby %}

computer_choice = CHOICES.keys.sample

{% endhighlight %}

The above uses the keys method which returns an array of keys then uses the sample method to get a key chosen at random.

Now that the player and computer choices are known, the comparison can begin and this is the biggest difference between the two solutions. The solution still uses an if else but it uses fewer steps than I did and extracts a large part into a method. I much prefer this version to mine where I effectively hard coded each outcome.

{% highlight ruby %}

def display_winning_message(winning_choice)
  case winning_choice
  when 'p'
    puts "Paper wraps Rock!"
  when 'r'
    puts "Rock smashes Scissors!"
  when 's'
    puts "Scissors cuts Paper!"
  end
end

if player_choice == computer_choice
  puts "It's a tie!"
elsif (player_choice == 'p' && computer_choice == 'r') || 
(player_choice == 'r' && computer_choice == 's') || 
(player_choice == 's' && computer_choice == 'p')
  display_winning_message(player_choice)
else
  display_winning_message(computer_choice)
  puts "Computer won!"
end

{% endhighlight %}

Finally the player is asked if he/she wants to play again:

{% highlight ruby %}

puts "Play again? (y/n)"
break if gets.chomp.downcase != 'y'

{% endhighlight %}

The solution code in full:

{% highlight ruby %}

puts "Welcome to Paper Rock Scissors"

CHOICES = {'p' => 'Paper', 'r' => 'Rock', 's' => 'Scissors'}

loop do
  
  # player picks
  begin 
    puts "Pick one: (p/r/s)"
    player_choice = gets.chomp.downcase
  end until CHOICES.keys.include?(player_choice)

  # computer picks
  computer_choice = CHOICES.keys.sample

def display_winning_message(winning_choice)
  case winning_choice
  when 'p'
    puts "Paper wraps Rock!"
  when 'r'
    puts "Rock smashes Scissors!"
  when 's'
    puts "Scissors cuts Paper!"
  end
end

if player_choice == computer_choice
  puts "It's a tie!"
elsif (player_choice == 'p' && computer_choice == 'r') || 
(player_choice == 'r' && computer_choice == 's') || 
(player_choice == 's' && computer_choice == 'p')
  display_winning_message(player_choice)
  puts "YOU WIN!!"
else
  display_winning_message(computer_choice)
  puts "Computer won!"
end

puts "Play again? (y/n)"
break if gets.chomp.downcase != 'y'

end

puts "Good Bye o/"
{% endhighlight %}

<h2>Thanks to:</h2>
<a href="http://blog.brandon-conway.com">Brandon</a> who is one of the immensely helpful teaching assistants at Tealeaf for checking my code, suggesting little improvements and assuring me the different solutions doesn't always mean a better solution and to Vic for checking my grammar!




