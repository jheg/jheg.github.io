---
layout: post
title:  "Building 'Tic Tac Toe' part one"
date:   2014-08-25 12:45
categories: programs
comments: true
excerpt: Assignment - To build the game Tic Tac Toe.
---

Following on from the assignment to build the paper rock scissors game I was tasked with trying to build the game Tic Tac Toe. As with the previous mini project this was to built from scratch from what I learnt from the course so far. 

Game Logic:

- Draw board
- Decide who goes first
- Player takes a turn and picks from available squares
- Check to see if winner 
- Check if any squares are left 
- Player 2 takes a turn 
- Play again?


My approach to the build 

I think the most complex part of this was going to be building the checks to see if a player had won after their go so I decided to leave this until part two. I wanted to get the basic loop of the game working smoothly from program execution to game end including the following compontents:

- Game start and play again?
- Initializing the empty squares
- Drawing the board
- Deciding who would go first at random
- Building in a slight delay for the computers go so as to improve the UX for the player. The game demo in the course video instantly executed the computers go as soon as the player had gone making a slightly confusing UX for the player
- Checking which squares were available for selection and telling the player which these squares were including a check to ensure the player had selected an empty square
- Output a message that the game has ended

<h2>Step 1: Start of game and play again</h2>

I will basically wrap my entire game inside a while loop that says while the variable 'play_again' equals 'y' start game and at the end of each game store the players decision to play again inside the 'play_again' variable.

{% highlight ruby linenos %}
# play again?
play_again = 'y'

while play_again == 'y'

# the code for my game will go here

sleep 0.5
puts "GAME OVER"

# play again?
sleep 0.5
puts "Play again? (y/n)"
play_again = gets.chomp

end

{% endhighlight %}

The game starts when the file is executed because line 4 checks the 'play_again' variable to see if it evaluates to true and if so starts the game. After the game has finished a message is displayed informing the player the game is over and asks do they wish to play again. The players answer is obtained using gets.chomp which stores the users input as a string into the variable - the while loop then checks again and either restarts or quits.

<h2>Step 2: Storing the squares</h2>

Using a hash to store the squares seemed like a good idea as I could use the square numbers as the keys and store the users markers (x or o) as the values.

{% highlight ruby %}
# initialize the empty hash that will store the board squares
board_squares = {1 => " ",2 => " ",3 => " ",4 => " ",5 => " ",6 => " ",7 => " ",8 => " ",9 => " "}
{% endhighlight %}

The space in the values are intentional so that when it is replaced with either an 'x' or 'o' it occupies the same width in the grid and avoids throwing out the formatting.

<h2>Step 3: Writing a method that checks for available squares (squares that equal " ")</h2>

If I don't have a means of checking if a square is empty I am unable to stop the player or computer from picking the same square so the purpose of this method is to return an array of squares (the keys from our board_squares hash) that contain " ".

{% highlight ruby %}
# Available squares
def available_squares(squares)
  squares.select {|_,v| v == " "}.keys
end
{% endhighlight %}

What is happening here is that we will be able to pass into this method our board_squares hash and select from it key value pairs where pair equals " " returning the key into an array for use in our program. The squares parameter I orignially named 'b' but as correctly pointed out by Brandon at Tealeaf Ruby is an expressive language and so you should be afraid to use more descriptive naming conventions so they make sense when I or someone else looks at the code in six months time. It is also considered best practice that if you are not using an argument in the code block then it should be written as a single underscore so it indicates it is not used.

<h2>Step 4: Creating an array to store player1 and player2</h2>

This will help me select at random who goes first later on - player or computer

{% highlight ruby %}
# players stored in array so player can be chosen at random
players = ["player1", "player2"]
{% endhighlight %}

<h2>Step 5: Drawing the board</h2>

Before I started this assignment I spent days thinking about how I could draw the board and dynamically update it after each iteration of the game loop. I had to check part of the solution video for this part as I couln't quite grasp how this was going to work. It really was quite simple once I reviewed the video though simply drawing the board using puts and using string interpolation to store the current status of the board_squares hash. All of this is stored in a method called draw_board.

{% highlight ruby linenos %}
# draw the board 
def draw_board(squares)
  system 'clear'
  puts "#{squares[1]}|#{squares[2]}|#{squares[3]}"
  puts "-----"
  puts "#{squares[4]}|#{squares[5]}|#{squares[6]}"
  puts "-----"
  puts "#{squares[7]}|#{squares[8]}|#{squares[9]}"
end
{% endhighlight %}

I'm certain I could have drawn a much more elaborate board but this works well enough. Without the system 'clear' the game becomes a bit messy and stacks each iteration of the game loop on top of each other repeatedly printing out the board. system 'clear' keeps the game nice and tidy.

<h2>Step 6: Writing the start of the methods where player or computer picks a square</h2>

Here I created two methods, one for each player. The first method is for player and starts with a conditional check that we will use to see if there are any empty squares in our board_squares hash. If this evaluates true the program continues to line 4 and asks the player to choose a square. I used string interpolation to pass in the available_squares method that returns an array of the squares left for selection helping the player make their choice. Using gets.chomp.to_i We then store the players answer into the variable 'i'. The to_i converts from string to interger. We then run an if else statement to check if the players choice is available for selection and if it then assigns the square with an 'x'. If the conditional is false the program runs again and asks the player to choose a square. Once finished the updated board is displayed.

The second method handles the computers go. First off a message informing the player the computer is taking a turn. Next I used 'sleep 0.5' to give the feeling of a more natural pace to the game rather than the everything happening instantly. The computer then selects a random available square using the .sample method and stores it in the 'i' variable. We the pass this variable into the board_squares hash and assign it an 'o'. Finally we display the updated board.

{% highlight ruby linenos %}
# player/computer picks square methods

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

def player2(squares)
    puts "Computer chooses a square"
    sleep 0.5
    i = available_squares(squares).sample
    squares[i] = "o"
    draw_board(squares)
end
{% endhighlight %}

<h2>Step 7: The game begins</h2>

The game begins by storing the person to go first in a variable called goes_first by taking a random sample from our players array and draws the empty board to the screen.

{% highlight ruby %}
# setting the who goes first variable
goes_first = players.sample

# show players the empty board
draw_board(board_squares)

{% endhighlight %}

We decide who goes first by running an if elsif to check which player is stored in the goes_first variable then
we simply invoke each players turn by calling the methods in a loop that runs until there are no squares left. 

{% highlight ruby linenos %}

# Conditional that checks which loop to execute: player 1 or player 2
if goes_first == 'player1'
  begin
    player1(board_squares)
    player2(board_squares)
  end until available_squares(board_squares).empty?
elsif goes_first == 'player2'
  begin
    player2(board_squares)
    player1(board_squares)
  end until available_squares(board_squares).empty?
end

{% endhighlight %}

Things are working smoothly so far and the game is beginning to take shape nicely. I'll finish the game in part two.


