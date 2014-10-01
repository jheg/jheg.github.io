---
layout: post
title:  "Procedural BlackJack game"
date:   2014-09-24 11:00
categories: programs
comments: true
excerpt: Assignment - To build the game BlackJack.
---

So the assignment is to create the blackjack game. The <a href="http://en.wikipedia.org/wiki/Blackjack#Rules_of_play_at_casinos">rules</a> of BlackJack. 

Blank page ready I started off by writing some pseudo code for the high level principles of the game. Already I cannot imagine starting to write a program without this as it really gets you into the zone of thinking about how your program is going to operate without cluttering my mind worrying about the actual code.

My high-level pseudo code:

{% highlight ruby %}
# Initialize deck of cards
  # Deal two random cards each to player(s) and dealer
    # Player 'Hits' or 'Stays' or has BJ
      # continue until 'Stay' or 'Bust'
        # Dealer plays 
          # Result 
            # play again?
{% endhighlight %}

Once I'd written this foundation I went into a second level of detail to better understand each step fully and ended up with this:

{% highlight ruby %}
# Initialize deck of cards
  # Deal two random cards each to player(s) and dealer
  # Remove the dealt cards from the deck as they are dealt
    # Player 'Hits' or 'Stays' or has BJ
      # if player has BJ play moves to Dealer
      # if player hits then another card is dealt
      # if total exceeds 21 player busts comp wins, else if player has > 16 && < 20 player can 'Hit' or 'Stay', else 'Hit' 
      # Remove dealt cards from deck array
      # continue until 'Stay' or 'Bust'
        # Dealer plays if Player 'Stayed' or has BJ
          # if dealer cards == BJ && players cards == BJ its a draw
          # else if dealers cards < 16 dealer 'Hits'
          # else if dealers cards > 16 && < 19 random ('Hit' or 'Stay')
          # else if dealers cards >= 19 && <= 21 'Stay'
          # else bust 
            # Result compares 
              # play again?
{% endhighlight %}

With the logic written I dived into writing the methods I'll need for my program to operate. I figured I needed a method that calculates the running total of the players cards, a method for each of the players for hitting or staying, a hit method that adds a new card to the players hand. A few potential tricky elements began to enter my head such as how I'm going to handle the Aces that can be worth 1 or 11. Some thinking time is needed. Anyway this is what I have so far:

{% highlight ruby linenos %}
# multidimensional array holding two identical decks. Two decks to prevent card counting.
decks = [
  ['Ah ', '2h ', '3h ', '4h ', '5h ', '6h ', '7h ', '8h ', '9h ', '10h', 'Jh ', 'Qh ', 'Kh ','Ad ', '2d ', '3d ', '4d ', '5d ', '6d ', '7d ', '8d ', '9d ', '10d', 'Jd ', 'Qd ', 'Kd ', 'As ', '2s ', '3s ', '4s ', '5s ', '6s ', '7s ', '8s ', '9s ', '10s', 'Js ', 'Qs ', 'Ks ', 'Ac ', '2c ', '3c ', '4c ', '5c ', '6c ', '7c ', '8c ', '9c ', '10c', 'Jc ', 'Qc ', 'Kc '],
  ['Ah ', '2h ', '3h ', '4h ', '5h ', '6h ', '7h ', '8h ', '9h ', '10h', 'Jh ', 'Qh ', 'Kh ','Ad ', '2d ', '3d ', '4d ', '5d ', '6d ', '7d ', '8d ', '9d ', '10d', 'Jd ', 'Qd ', 'Kd ', 'As ', '2s ', '3s ', '4s ', '5s ', '6s ', '7s ', '8s ', '9s ', '10s', 'Js ', 'Qs ', 'Ks ', 'Ac ', '2c ', '3c ', '4c ', '5c ', '6c ', '7c ', '8c ', '9c ', '10c', 'Jc ', 'Qc ', 'Kc ']
]

# two empty arrays to store the players hands
player = []
dealer = []


# deals cards 
2.times do
  player.push(decks.sample.delete(decks.sample.sample))
  dealer.push(decks.sample.delete(decks.sample.sample))
end

# calculate total of cards in a hand
def calculate_total(array)
  running_total = 0
  array.each do |card|
    if card.slice(0) == 'A'
      running_total = running_total + 11
    elsif card.slice(0) == 'J' || card.slice(0) == 'Q' || card.slice(0) == 'K' || card.slice(0..1) == '10'
      running_total = running_total + 10
    else 
      running_total = running_total + card.slice(0).to_i      
    end
  end
  running_total
end

# player hit or stay method 
def player_hit_or_stay(running_total, decks, player)
  if running_total == 21
    puts "BLACKJACK!!!!"
    exit
  elsif running_total <= 20
    puts 'Hit(h) or Stay(s)?'
    answer = gets.chomp.downcase
    while answer != 'h' && answer != 's'
      puts 'Press "h" to hit or press "s" to stay'
      answer = gets.chomp.downcase
    end
    if answer == 'h'
      hit(decks.sample, player)
      p player
    end
  end
end

# dealer hit or stay method 
def dealer_hit_or_stay(running_total)
  if running_total == 21
    puts 'DEALER HAS BLACKJACK!'
  elsif running_total < 17 
    puts 'Dealer Hits'
  elsif running_total >= 17 && running_total <= 18
    puts 'Dealer randomally selects h or s'
  elsif running_total >= 19 && running_total <= 20
    puts 'Dealer Stays!'
  end
end

# deal new card method
def hit(deck, hand)
  hand.push(deck.delete(deck.sample))
  calculate_total(hand)
end
{% endhighlight %} 

<h4>The pry-byebug gem</h4>
I was looking for a way to stop my programs at certain points and then step by step move on from the break point. Someone on the #ruby irc chanel linked me to the <a href="https://github.com/deivid-rodriguez/pry-byebug">pry-byebug gem</a>. It does exactly what I needed which is very useful for debugging.

Back to my game..

<h3>Dealing with aces</h3>

So after a few hours short break I returned to my program to tackle the Ace in the pack issue. Basically if the player has an ace in their hand then I want the program to output the two scores, for instance if you had an ace and a 4 you should get a message along the lines of:

<span class="terminal">You have 5 or 15</span>

If you had an ace, 7 and an 8 you should get the message:

<span class="terminal">You have 16</span>

In the second example it should only output one total as the other (11+7+8) exceeds 21 and therefor is unplayable.

So I need to track two totals in case an ace appears at any stage. I can then write some code that will check for an ace and if it exists add 1 to the first total and add 11 to the second.

So I reworked my calculate_total method to this:

{% highlight ruby linenos %}
# calc total
def calculate_total(array)
  running_totals = {:total1 => 0, :total2 => 0}
  array.each do |card|
    if card.slice(0) == 'A'     
        running_totals[:total1] = running_totals[:total1] + 1
        running_totals[:total2] = running_totals[:total2] + 11  
        ace_in_pack = true      
    elsif card.slice(0) == 'J' || card.slice(0) == 'Q' || card.slice(0) == 'K' || card.slice(0..1) == '10'
      running_totals[:total1] = running_totals[:total1] + 10
      running_totals[:total2] = running_totals[:total2] + 10
    else 
      running_totals[:total1] = running_totals[:total1] + card.slice(0).to_i      
      running_totals[:total2] = running_totals[:total2] + card.slice(0).to_i      
    end
  end
  running_totals
end
{% endhighlight %}

The first version of this was just to get the method working in a fashion but now the method has two running totals to handle instances where an Ace is present and there are two possible totals. So my method handles this pretty well by checking if the card is an Ace and if it is increment total1 by 1 and total2 by 11. Excellent!!

Here are some outcomes:


<div class="terminal">
<p>7h, 5s</p>
<p>Your current score is 12</p>
<p>Hit(h) or Stay(s)</p>
</div>

<div class="terminal">
<p>6d, 2c</p>
<p>Your current score is 8</p>
<p>Hit(h) or Stay(s)</p>
</div>

<div class="terminal">
<p>Ah, 4s</p>
<p>Your current score is 5 or 15</p>
<p>Hit(h) or Stay(s)</p>
</div>

<div class="terminal">
<p>Ah, As</p>
<p>Your current score is 2</p>
<p>Hit(h) or Stay(s)</p>
</div>

Great st.... hang on what's going on in the last example? Shouldn't that say you have 2 or 12?

The reason it breaks is because for the first iteration of array.each it adds 1 to total1 and 11 to total2 and on the second iteration it again adds 1 to total1 and 11 to total2 giving a total of 2 for total1 and 22 for total2 and therefore busting total2. 

If I'm dealt two aces I should have:

<span class="terminal">"You have 2 or 12</span>

What my program needs to do in this rare but highly possible scenario is check to see if there is already an ace in the pack and if there is then add 1 to both total1 and total2 not add another 11 to total2.

So I created a new variable local to the calculate_total method that tracks if the is an ace in the pack. Here is the newly written method:

{% highlight ruby linenos %}
# calc total
def calculate_total(array)
  running_totals = {:total1 => 0, :total2 => 0}
  ace_in_pack = false
  array.each do |card|
    if card.slice(0) == 'A'
      if ace_in_pack == true
        running_totals[:total1] = running_totals[:total1] + 1
        running_totals[:total2] = running_totals[:total2] + 1        
      elsif ace_in_pack == false
        running_totals[:total1] = running_totals[:total1] + 1
        running_totals[:total2] = running_totals[:total2] + 11  
        ace_in_pack = true      
      end    
    elsif card.slice(0) == 'J' || card.slice(0) == 'Q' || card.slice(0) == 'K' || card.slice(0..1) == '10'
      running_totals[:total1] = running_totals[:total1] + 10
      running_totals[:total2] = running_totals[:total2] + 10
    else 
      running_totals[:total1] = running_totals[:total1] + card.slice(0).to_i      
      running_totals[:total2] = running_totals[:total2] + card.slice(0).to_i      
    end
  end
  running_totals
end
{% endhighlight %}

Now that's far slicker. Line 4 is where i've declared the ace_in_pack variable to an initial value of false and now in the event of an Ace being present I have added a new conditional to check to see if there is already an Ace in the array. If there is then each total increments by 1 and if there isn't an Ace present then we carry on as normal adding 1 to total1 and 11 to total2 and then reset ace_in_pack to true.

I spent a few days away from my program partly due to time constraints and partly due to allowing myself some thinking time. I came back to it today a finished the program and I'm pretty happy with it but I've learnt that orgainization is so important when designing and executing my programs.

I like to always know where I am and exactly what every line of my program is doing and I found that after a while writing this game I was losing myself in the code and forgetting what part does what and how one method interacts with another. I got there in the end and as a version 1 I suppose it's a nice foundation but I should probably refactor it and adopt the DRY principle in a few places.

My final code: Well before I refactor it I guess.


{% highlight ruby linenos %}
# Initialize deck of cards
  # Deal two random cards each to player(s) and dealer 
  # Remove the dealt cards from the deck as they are dealt
    # Player 'Hits' or 'Stays' or has BJ
      # if player has BJ play moves to Dealer
      # if player hits then another card is dealt
      # if total exceeds 21 player busts comp wins, else if player has > 16 && < 20 player can 'Hit' or 'Stay', else 'Hit' 
      # Remove dealt cards from deck array
      # continue until 'Stay' or 'Bust'
        # Dealer plays if Player 'Stayed' or has BJ
          # if dealer cards == BJ && players cards == BJ its a draw
          # else if dealers cards < 16 dealer 'Hits'
          # else if dealers cards > 16 && < 19 random ('Hit' or 'Stay')
          # else if dealers cards >= 19 && <= 21 'Stay'
          # else bust 
            # Result compares 
              # play again?

# 2...9 = face value , 10,J,Q,K = 10, A = 1 or 11
# 4 different suits
# 52 cards in total



require('pry')

#  M   M  MMMM  MMMMM  M   M  MMMMM  MMMM   MMMMM
#  MM MM  M       M    M   M  M   M  M   M  M
#  M M M  MMM     M    MMMMM  M   M  M   M   MMM 
#  M   M  M       M    M   M  M   M  M   M      M
#  M   M  MMMM    M    M   M  MMMMM  MMMM   MMMMM

# deals cards
def deal(player, dealer, decks)
  system 'clear'
  2.times do
    player.push(decks.delete(decks.sample))
    dealer.push(decks.delete(decks.sample))
  end
  display_cards(player)
end

# displays the cards and tell player their score 
def display_cards(hands)
  i = 0
  puts 'PLAYERS CARDS'
  hands.each do |card|
    puts " _____ "
    puts "|     |"
    puts "| #{hands[i]} |"
    puts "|     |"
    puts " ----- "
    i = i + 1
  end
end

# Dealer shows hand
def dealer_shows(dealers_hand, players_hand, running_total1, running_total2)
  system 'clear'
  player_final_score(running_total1, running_total2)
  display_cards(players_hand)
  puts 
  puts
  puts "DEALERS CARDS"
  i = 0
  dealers_hand.each do |card|
    puts " _____ "
    puts "|     |"
    puts "| #{dealers_hand[i]} |"
    puts "|     |"
    puts " ----- "
    i = i + 1
  end
end

def player_final_score(running_total1,running_total2)
  if running_total1 <= 21 && running_total2 <= 21 && running_total2 > running_total1
    return running_total2
  else
    return running_total1
  end
end

# say score
def say_score(hands, person)
  total1 = calculate_total(hands)[:total1]
  total2 = calculate_total(hands)[:total2]
  if total1 == 21 || total2 == 21
    puts 
  elsif total1 == total2 
    puts "#{person} has #{total1}"  
  elsif total1 <= 21 && total2 <= 21
    puts "#{person} has #{total1} or #{total2}"
  elsif total2 > 21
    puts "#{person} has #{total1}"
  end
end

# calc total
def calculate_total(array)
  running_totals = {:total1 => 0, :total2 => 0}
  ace_in_pack = false
  array.each do |card|
    if card.slice(0) == 'A'
      if ace_in_pack == true
        running_totals[:total1] = running_totals[:total1] + 1
        running_totals[:total2] = running_totals[:total2] + 1        
      elsif ace_in_pack == false
        running_totals[:total1] = running_totals[:total1] + 1
        running_totals[:total2] = running_totals[:total2] + 11  
        ace_in_pack = true      
      end    
    elsif card.slice(0) == 'J' || card.slice(0) == 'Q' || card.slice(0) == 'K' || card.slice(0..1) == '10'
      running_totals[:total1] = running_totals[:total1] + 10
      running_totals[:total2] = running_totals[:total2] + 10
    else 
      running_totals[:total1] = running_totals[:total1] + card.slice(0).to_i      
      running_totals[:total2] = running_totals[:total2] + card.slice(0).to_i      
    end

  end
  running_totals
end

# player hit or stay
def player_hit_or_stay(running_total1, running_total2, decks, player, dealer)
  say_score(player, "Player")
  if running_total1 > 21 && running_total2 > 21 
    puts "YOU BUST!"
    puts "Dealer wins"
    sleep 1.5
    puts "GAME OVER :("
  elsif running_total1 == 21 || running_total2 == 21 
    puts "BLACKJACK!!!!"
    running_total1 = calculate_total(player)[:total1]
    running_total2 = calculate_total(player)[:total2]
    player_score = player_final_score(running_total1, running_total2)    
    running_total1 = calculate_total(dealer)[:total1]
    running_total2 = calculate_total(dealer)[:total2]
    dealer_score = player_final_score(running_total1, running_total2)
    puts say_score(dealer, "Dealer")
    sleep 1
    result(player_score, dealer_score)
  elsif running_total1 <= 20 || running_total2 <= 20
    puts 'Hit(h) or Stay(s)?'
    answer = gets.chomp.downcase
    while answer != 'h' && answer != 's'
      puts 'Press "h" to hit or press "s" to stay'
      answer = gets.chomp.downcase
    end
    if answer == 'h'
      sleep 1
      hit(decks, player)
      system 'clear'
      display_cards(player)
      running_total1 = calculate_total(player)[:total1]
      running_total2 = calculate_total(player)[:total2]
      player_hit_or_stay(running_total1, running_total2, decks, player, dealer)
    elsif answer == 's'
      dealer_shows(dealer, player, running_total1, running_total2)
      puts "You stayed on #{player_final_score(running_total1, running_total2)}"
      player_score = player_final_score(running_total1, running_total2)
      sleep 1
      running_total1 = calculate_total(dealer)[:total1]
      running_total2 = calculate_total(dealer)[:total2]
      puts say_score(dealer, "Dealer")
      sleep 1
      dealer_hit_or_stay(decks,running_total1,running_total2, player, dealer, player_score)
    end
  end
end

# dealer hit or stay
def dealer_hit_or_stay(decks, running_total1,running_total2, player, dealer, player_score)
  if running_total1 > 21 && running_total2 > 21 
    puts "Dealer BUST, You WIN!!!!!"
    sleep 0.5
    puts "Game Over :)"
  elsif running_total1 == 21 || running_total2 == 21
    puts 'DEALER HAS BLACKJACK!'
    running_total1 = calculate_total(dealer)[:total1]
    running_total2 = calculate_total(dealer)[:total2] 
    dealer_score = player_final_score(running_total1, running_total2)
    result(player_score, dealer_score)
  elsif running_total1 < 17 && running_total2 < 17
    puts 'Dealer Hits'
    hit(decks, dealer)
    sleep 2
    dealer_shows(dealer, player, running_total1, running_total2)
    puts "You stayed on #{player_score}"
    say_score(dealer, "Dealer")
    running_total1 = calculate_total(dealer)[:total1]
    running_total2 = calculate_total(dealer)[:total2] 
    dealer_hit_or_stay(decks, running_total1,running_total2, player, dealer, player_score)
  elsif running_total1 >= 17 && running_total1 <= 18
    gamble = ['yes', 'no']
    if gamble.sample == 'yes'
      puts 'Dealer Hits'
      hit(decks, dealer)
      sleep 2
      dealer_shows(dealer, player, running_total1, running_total2)
      running_total1 = calculate_total(dealer)[:total1]
      running_total2 = calculate_total(dealer)[:total2] 
      dealer_hit_or_stay(decks, running_total1,running_total2, player, dealer, player_score)
    elsif gamble.sample == 'no'
      puts 'Dealer Stays'
      sleep 2
      running_total1 = calculate_total(dealer)[:total1]
      running_total2 = calculate_total(dealer)[:total2] 
      dealer_score = player_final_score(running_total1, running_total2)
      result(player_score, dealer_score)
    end
    # if s result(player_score, dealer_score)
  elsif running_total1 >= 19 && running_total1 <= 20 || running_total2 >= 19 && running_total2 <= 20
    puts 'Dealer Stays!'
    running_total1 = calculate_total(dealer)[:total1]
    running_total2 = calculate_total(dealer)[:total2] 
    dealer_score = player_final_score(running_total1, running_total2)
    result(player_score, dealer_score)
  end
end

# deal new card
def hit(deck, hand)
  hand.push(deck.delete(deck.sample))
  calculate_total(hand)
end

# result calc
def result(player_score, dealer_score)
  if player_score == dealer_score
    puts "Both player got #{player_score}"
    puts "It's a DRAW!"
  elsif player_score > dealer_score
    puts "#{player_score} beats #{dealer_score}"    
    puts "YOU WIN!!!"
  elsif dealer_score > player_score
    puts "#{dealer_score} beats #{player_score}"
    puts "Dealer wins :("
  end
end


#  GGGGG  GGGGG  G   G  GGGGG
#  G      G   G  GG GG  G
#  G  GG  GGGGG  G G G  GGG
#  G   G  G   G  G   G  G
#  GGGGG  G   G  G   G  GGGGG


# play again 
play_again = 'y'
game_streak = 0
results = { :wins => 0, :losses => 0, :draws => 0 } # store history


while play_again == 'y'
  # initialize decks
  decks = ['Ah ', '2h ', '3h ', '4h ', '5h ', '6h ', '7h ', '8h ', '9h ', '10h', 'Jh ', 'Qh ', 'Kh ','Ad ', '2d ', '3d ', '4d ', '5d ', '6d ', '7d ', '8d ', '9d ', '10d', 'Jd ', 'Qd ', 'Kd ', 'As ', '2s ', '3s ', '4s ', '5s ', '6s ', '7s ', '8s ', '9s ', '10s', 'Js ', 'Qs ', 'Ks ', 'Ac ', '2c ', '3c ', '4c ', '5c ', '6c ', '7c ', '8c ', '9c ', '10c', 'Jc ', 'Qc ', 'Kc ', 'Ah ', '2h ', '3h ', '4h ', '5h ', '6h ', '7h ', '8h ', '9h ', '10h', 'Jh ', 'Qh ', 'Kh ','Ad ', '2d ', '3d ', '4d ', '5d ', '6d ', '7d ', '8d ', '9d ', '10d', 'Jd ', 'Qd ', 'Kd ', 'As ', '2s ', '3s ', '4s ', '5s ', '6s ', '7s ', '8s ', '9s ', '10s', 'Js ', 'Qs ', 'Ks ', 'Ac ', '2c ', '3c ', '4c ', '5c ', '6c ', '7c ', '8c ', '9c ', '10c', 'Jc ', 'Qc ', 'Kc ']
  
  # empty arrays to hold all players cards
  player = []
  dealer = []   

  # Deal two card to each player
  deal(player, dealer, decks)

  # player takes turn
  total1 = calculate_total(player)[:total1] 
  total2 = calculate_total(player)[:total2] 
  player_hit_or_stay(total1,total2, decks, player, dealer)
  total1 = calculate_total(player)[:total1] 
  total2 = calculate_total(player)[:total2] 
  p = player_final_score(total1, total2)
  total1 = calculate_total(dealer)[:total1] 
  total2 = calculate_total(dealer)[:total2] 
  d = player_final_score(total1, total2)


  sleep 0.5
 
  if d > 21
    results[:wins] = results[:wins] + 1
  elsif p <= 21 && p > d
    results[:wins] = results[:wins] + 1
  elsif p == d
    results[:draws] = results[:draws] + 1
  elsif d <= 21 && d > p
    results[:losses] = results[:losses] + 1
  elsif p > 21
    results[:losses] = results[:losses] + 1
  end
    
  game_streak = game_streak + 1
  per_wins = (results[:wins].to_f / game_streak.to_f * 100).round(2)
  puts "_____________________________________"
  puts "_____________________________________"
  puts "Current game streak is #{game_streak}"
  puts "You have won #{results[:wins]}, lost #{results[:losses]} and drawn #{results[:draws]}"
  puts "You have won #{per_wins}% of the games you have played"
  puts "_____________________________________"
  puts "_____________________________________"

  # play again?
  puts "Play again? (y/n)"
  play_again = gets.chomp

end
{% endhighlight %}

I built in some pretty neat features too like making the dealer not follow a scripted method of hit or staying. I also allowed Ace to be worth 1 and 11 and giving the player an option to play both totals. Finally whilst in the game loop the game stores your game streak, wins, losses and draws total and outputs the percentage of wins you have.



