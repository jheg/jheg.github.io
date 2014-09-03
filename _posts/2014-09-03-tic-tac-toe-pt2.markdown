---
layout: post
title:  "Building 'Tic Tac Toe' part two"
date:   2014-09-03 12:24
categories: programs
comments: true
excerpt: Assignment - To build the game Tic Tac Toe part two.
---

In part one I got the basics of the game control working to a point where I felt I had an excellent foundation in which to build upon. In part two I wanted to start to build in some intelligence for the computer player rather than the computer aimlessly picking random squares and I wanted to tidy up my code - indentation etc. 

I'm going to break it all down but here is my final code:

{% highlight ruby linenos %}
# Initialize board hash
# Draw board
# Choose at random who goes first
# Player/Computer has a turn if square available
# check for winner

require 'pry'

# constants
# rows columns and diagonals
WINNING_LINES = [[1,2,3],[4,5,6],[7,8,9],[1,4,7],[2,5,8],[3,6,9],[1,5,9],[3,5,7]]

# markers
X = 'x'
O = 'o'

# Available squares
def available_squares(squares)
  squares.select {|_,v| v == " "}.keys
end

# draw the board
def draw_board(squares)
  system 'clear'
  puts "#{squares[1]}|#{squares[2]}|#{squares[3]}"
  puts "-----"
  puts "#{squares[4]}|#{squares[5]}|#{squares[6]}"
  puts "-----"
  puts "#{squares[7]}|#{squares[8]}|#{squares[9]}"
end

# check for winner method
def check_for_winner(line, squares)
  if line.find {|l| l.all? {|k| squares[k] == "x"} }
    puts "player WINS!!!"
    true
  elsif line.find {|l| l.all? {|k| squares[k] == "o"} }
    puts "computer wins :("
    true
  end
end

# checks to see if two in a row
def two_in_a_row?(hsh, mrkr)
  if hsh.values.count(mrkr) == 2
    hsh.select{|k,v| v == ' '}.keys.first
  else
    false
  end
end

# player/computer picks square methods
# player 1
def player1(squares)
  if available_squares(squares).any?
    puts "Choose an available square from #{available_squares(squares)}"
    i = gets.chomp.to_i
    if available_squares(squares).include?(i)
      squares[i] = "x"
    else
      player1(squares)
    end
    draw_board(squares)
  end
end

# player 2
def player2(line, squares)
  puts "Computer chooses a square"
  sleep 0.5

  defend_this_square = nil
  attacked = false
  
  # attack 
  WINNING_LINES.each do |l|
    defend_this_square = two_in_a_row?({l[0] => squares[l[0]], l[1] => squares[l[1]], l[2] => squares[l[2]]}, O)
    if defend_this_square
      squares[defend_this_square] = 'o'
      attacked = true
      break
    end
  end
  
  # defend  
  if attacked == false
    WINNING_LINES.each do |l|
      defend_this_square = two_in_a_row?({l[0] => squares[l[0]], l[1] => squares[l[1]], l[2] => squares[l[2]]}, X)
      if defend_this_square
        squares[defend_this_square] = 'o'
        break
      end 
    end 
  end
  squares[available_squares(squares).sample] = "o" unless defend_this_square
  draw_board(squares)
end

# play again?
play_again = 'y'

while play_again == 'y'

  # initialize the empty hash that will store the board squares
  board_squares = {1 => " ",2 => " ",3 => " ",4 => " ",5 => " ",6 => " ",7 => " ",8 => " ",9 => " "}

  # players stored in array so player can be chosen at random
  players = ["player1", "player2"]

  # setting the who goes first variable
  goes_first = players.sample

  # show players the empty board
  draw_board(board_squares)

  # Conditional that checks which loop to execute: player 1 or player 2
  if goes_first == 'player1'
    begin
      break if check_for_winner(WINNING_LINES, board_squares) == true
      player1(board_squares)
      break if check_for_winner(WINNING_LINES, board_squares) == true
      player2(WINNING_LINES, board_squares)
    end until available_squares(board_squares).empty?
  elsif goes_first == 'player2'
    begin
      break if check_for_winner(WINNING_LINES, board_squares) == true
      player2(WINNING_LINES, board_squares)
      break if check_for_winner(WINNING_LINES, board_squares) == true
      player1(board_squares)
    end until available_squares(board_squares).empty?
  end

  sleep 0.5
  puts "GAME OVER"

  # play again?
  sleep 0.5
  puts "Play again? (y/n)"
  play_again = gets.chomp

end
{% endhighlight %}

So this program is effectively in two parts. The first part holds all of the methods and constants required in the game and the second part is the game itself which resides inside a while loop. The while loop basically checks to see if the play_again variable is set to yes or no. 

First off with have a constant variable called WINNING_LINES. This is an array of arrays that is storing the lines on our tic tac toe board. There are 8 lines in total and I will iterate through these arrays when I am checking for certain conditions in my game methods. 

A constant variable is denoted by a leading capital although the convention is to write them all in CAPS. The scope of a constant differs to local variables. You should use a constant variable when its value shouldn't be changed although beware it is possible to change a constant variable in ruby with a little warning from the interpreter.

I have then stored 'x' & 'o' in the constants X and O for use later on in our game methods.

I covered the methods draw_board, available_squares and player1 in part 1 and they remain unchanged. I also wrote a player2 method but in this part I began to build this method out to give it some added power. In addition to these methods I've added two new methods, two_in_a_row and check_for_winner which I'll explain shortly. First lets look at the player2 method before the change.

Before:

{% highlight ruby linenos %}
def player2(squares)
  puts "Computer chooses a square"
  sleep 0.5
  i = available_squares(squares).sample
  squares[i] = "o"
  draw_board(squares)
end
{% endhighlight %}

You can see that at this point the method just outputs a message and then assigns a random square to a local variable named i from the available_squares method. Using the i variable it then assigns an 'o' to the square on the board and then draws the board. 

At this point the computer is hideously disadvantaged as all it can do is select a random available square. It cannot attack and it cannot defend so that is what I needed to build in. 

We'll look at <i>how</i> the two new methods work shortly but for now just know the following: 

- The two_in_a_row method checks to see if there is a combination that exists whereby a player has two of their markers in a line and the remaining square is emtpy. For example true if 'x' ' ' 'x' false if 'x' 'o' 'x'.
- The check_for_winner method checks to see if either a line is filled exclusively with either all 'x' or 'o' for example true if 'x' 'x' 'x' or 'o' 'o' 'o'.

I added a parameter to the method to start with so that we can now pass in the WINNING_LINES as well as the board_squares. 

{% highlight ruby %}
def player2(line, squares)
{% endhighlight %}

I added two new local variables to work with within the method named defend_this_square and attacked and assigned them nil and false respectively.

{% highlight ruby %}
defend_this_square = nil
attacked = false
{% endhighlight %}

Now we want one of three things will happen on the computers turn: 

1. Computer checks to see if it can attack
2. If it cannot attack it will see if it needs to defend 
3. If it doesn't need to defend it will just choose a random available square.

<h3>Computer attacks</h3>

{% highlight ruby linenos %}
# attack 
WINNING_LINES.each do |l|
  defend_this_square = two_in_a_row?({l[0] => squares[l[0]], l[1] => squares[l[1]], l[2] => squares[l[2]]}, O)
  if defend_this_square
    squares[defend_this_square] = 'o'
    attacked = true
    break
  end
end
{% endhighlight %} 

The first part starting on line 2 and ending on line 9 is selecting each array (l) in the WINNING_LINES array. 

Line 3 - For each of the arrays in the WINNING_LINES array we are reassigning defend_this_square with the result of the two_in_a_row method call that runs the check we described above that will either return a integer (which will also evaluate to true) or false. 

Line 4 gets executed if defend_this_square is true (contains a number). If defend_this_square is true on an iteration of WINNING_LINES.each, two_in_a_row must have identified an instance where there were two 'o' and an empty square and it will have returned the empty square number into our defend_this_square variable. 

Line 5 then gets executed which assigns board_squares[defend_this_square] with a 'o' therefor completing a line. 

Next we reassign the attacked varaible as true so that option 2 and 3 do not get executed and finally on line 7 we break out of the loop.

<h3>Computer defends</h3>

If defend_this_square had not have evaluated to true on any of the iterations then the defence check would execute:

{% highlight ruby linenos %}
# defend  
if attacked == false
  WINNING_LINES.each do |l|
    defend_this_square = two_in_a_row?({l[0] => squares[l[0]], l[1] => squares[l[1]], l[2] => squares[l[2]]}, X)
    if defend_this_square
      squares[defend_this_square] = 'o'
      break
    end 
  end 
end
{% endhighlight %}

So here we can see that if the computer had attacked the attacked variable would now equal true and therefor the above would not be carried out. Assuming that the computer didn't attack we once again iterate through each of the WINNING_LINES arrays and check for a two_in_a_row instance for X. If it finds an instance it assigns the empty square to defend_this_square and then assigns board_squares[defend_this_square] with an 'o' to stop the player from completing a line on their next go. After this it breaks out of the loop.

Finally if neither of these are carried out the computer chooses a random availble square:

{% highlight ruby %}
squares[available_squares(squares).sample] = "o" unless defend_this_square
{% endhighlight %}

Unless defend_this_square evaluated to true squares[available_squares(squares).sample] = 'o'.

So there we have the newly updated player2 method and we have a pretty good understanding of what two_in_a_row and check_for_winner does so now let us understand how they work.

<h3>two_in_a_row</h3>

{% highlight ruby linenos %}
# checks to see if two in a row
def two_in_a_row?(hsh, mrkr)
  if hsh.values.count(mrkr) == 2
    hsh.select{|k,v| v == ' '}.keys.first
  else
    false
  end
end
{% endhighlight %}

For this method to work we want to pass in two parameters, a hash and a marker. The marker is either the X or O constant variable declared at the top of our program. The hash is a little more complex.

This hash is created in the player2 method like so:

{% highlight ruby %}
WINNING_LINES.each do |l|
  defend_this_square = two_in_a_row?({l[0] => squares[l[0]], l[1] => squares[l[1]], l[2] => squares[l[2]]}, O)
end
{% endhighlight %}

So lets say we are on the iteration of WINNING_LINES.each where l is [4,5,6] and square numbers 4 & 6 contained 'o' and 5 was empty:

{% highlight ruby %}
{l[0] => squares[l[0]], l[1] => squares[l[1]], l[2] => squares[l[2]]}
{% endhighlight %}

would be

{% highlight ruby %}
{ 4 => 'o', 5 => ' ', 6 => 'o'}
{% endhighlight %}

The above is the hash which is passed into two_in_a_row. Next we ask if the values of X appears twice in the hash and if it does we select and return the first key that equals ' ' in our example it would return the integer 5. If the condition is not met the method returns false.

<h3>check_for_winner</h3>

{% highlight ruby linenos %}
# check for winner method
def check_for_winner(line, squares)
  if line.find {|l| l.all? {|k| squares[k] == "x"} }
    puts "player WINS!!!"
    true
  elsif line.find {|l| l.all? {|k| squares[k] == "o"} }
    puts "computer wins :("
    true
  end
end
{% endhighlight %}

In order for us to check if a player has 3 in a row we need to pass in the arrays in WINNING_LINES and the board_squares. Lines 3 to 5 and lines 6 to 8 essentially do the same thing in as much as one tests to see if player has won and the second tests to see if the computer has won and outputs a message if either is true.

So lets look at the first in detail.

{% highlight ruby %}
line.find {|l| l.all? {|k| squares[k] == "x"} }
{% endhighlight %}

line is going to be our WINNING_LINES array and I am using the .find method to iterate through each array (l), and if all the elements in that array equal 'x' then it is true and the block is executed:

{% highlight ruby %}
puts "player WINS!!!"
true
{% endhighlight %}

<h3>The game loop</h3>

Now we have all of the methods and constants our game requires to function lets call them in to our game loop. 

{% highlight ruby linenos %}
# play again?
play_again = 'y'

while play_again == 'y'

  # initialize the empty hash that will store the board squares
  board_squares = {1 => " ",2 => " ",3 => " ",4 => " ",5 => " ",6 => " ",7 => " ",8 => " ",9 => " "}

  # players stored in array so player can be chosen at random
  players = ["player1", "player2"]

  # setting the who goes first variable
  goes_first = players.sample

  # show players the empty board
  draw_board(board_squares)

  # Conditional that checks which loop to execute: player 1 or player 2
  if goes_first == 'player1'
    begin
      break if check_for_winner(WINNING_LINES, board_squares) == true
      player1(board_squares)
      break if check_for_winner(WINNING_LINES, board_squares) == true
      player2(WINNING_LINES, board_squares)
    end until available_squares(board_squares).empty?
  elsif goes_first == 'player2'
    begin
      break if check_for_winner(WINNING_LINES, board_squares) == true
      player2(WINNING_LINES, board_squares)
      break if check_for_winner(WINNING_LINES, board_squares) == true
      player1(board_squares)
    end until available_squares(board_squares).empty?
  end

  sleep 0.5
  puts "GAME OVER"

  # play again?
  sleep 0.5
  puts "Play again? (y/n)"
  play_again = gets.chomp
end
{% endhighlight %}

We covered up to line 19 in part 1. Assuming that player1 goes first (it's the same for player2) we then go to line 20 where our begin end until starts. First off on line 21 we check to see if check_for_winner is true and if it is we break out and end game. If it is not true the player1 method is invoked and after that we check to see if check_for_winner is true again. We break if it is and we move on to player2 method if it is not. We continue this loop until check_for_winner is true or no are no available squares left.

<h2>Tidying up and tweaks</h2>
The ever helpfull guys over on the #ruby channel at irc made the following observations that I've duly corrected.

- Don't reassign constants in a loop, they should be constant values so put them at top of the script
- else; false; end is equiv to else; return false; end as its the last expression of a method so no need for the return
- General indentation that I'd missed.


<h3>Thanks:</h3>
Brandon at Tealeaf for reading and running my code and making suggestions for improvement. Hanmac, tobiasvl, jhass, shevy & workmad3 and the #ruby irc channel in general for always being on hand to help out without ever seeming to want anything in return.

