---
layout: post
title:  "Building a simple todo Rails app - Part 1"
date:   2015-01-08 21:00
categories: Rails
comments: true
excerpt: The structure of a Rails app.
---

<h2>Planning a Rails app</h2>

<span class="terminal">$ cd dev</span><br />
<span class="terminal">$dev rails new got_to_do_app</span>

<h2>Step 1 - What my app will do</h2>

The plan is to build an app that simply allows users to write a todo list. There are many features I intend to implement but initially I will just get a basic version up and running and then later add further functionality to it. This will be my test app - my Rails playground.

The initial feature set will allow a user to:
<ul>
  <li>Register and Log in<br />A User will comprise a name, email and mobile number</li>
  <li>Add a ToDo task<br />A ToDo task will include a title, description, deadline and timestamps of creation date and updated date</li>
  <li>Comment on Todo items <br />A Comment will include a comment</li>
  <li>Assign categories to Todo items<br />A category will include a name</li>
</ul>

Future iterations will include the ability to:
<ul>
  <li>Add someone to a Todo item</li>
  <li>Send email and possibly sms notifications</li>
</ul> 

<h2>Step 2 - Building out the database and associations</h2>
Rails apps work on a Model, View, Controller principle or MVC for short. This breaks down the tasks of the apllication into three parts each with their own job. The View renders and sends a completed webpage from the server to the user once it has gathered all the information it needs from the database, via the Model, which they get from the Controller. It's easy to visualize with the diagram below.

<img src="/assets/model_view_controller_in_rails.png" width="300" alt="picture of the model view controller workflow in Rails">

For this post we are focusing on the Model and the Database and we'll leave controllers and views until a later post.

Before creating the tables in a database to contain the data we need, we need to visualize what we want to store and how our data relates to each other. 

You can do this on a peice of paper just to sketch out the idea. 

<img src="/assets/rails_app_simple_entity_relationship_diagram.png" width="500" alt="picture of an entity relationship diagram for a small rails app">

The elements of the app have been split into 4 tables plus a join table ( I'll explain the join table shortly ). This diagram helps us with four tasks when building our database tables and building the relationships between each table in the database. 

1. The boxes represent each table in our database
2. The columns in a table are the elements within each box ( id, name, created_at... )
3. The lines that connect the boxes show relationships ( Associations )
4. Establishes where we need foreign keys

<h4>Foreign Keys</h4>
We need two things to make an association, one at the database layer and the other in the model layer. Lets suppose we have created a User called Charlie. Charlie creates a todo item with a title of 'Do the shopping on way home' and another one with the title 'Remember to clean my room'. Already we can see that whilst Charlie ( User ) can have many Todo items a Todo item can only have and belong to one User. In order to create this behaviour in the database we must include what is called a 'foreign key' in the table that is on the one side of the agreement - the todos table. 

The foreign key MUST be named correctly in order for the link to work. It must start with the singular version of the table name followed by an underscore and id like so: user_id ( shown in the diagram above ). Rails will assume that there is a table named users with a column name of id and this will provide the first part of the link.

To summarise:
<ul>
<li>A User has many todos and has many comments</li>
<li>A Todo belongs to a User and has many comments and has many categories</li>
<li>A Comment belongs to a User and belongs to a Todo</li>
<li>A Category has many todos</li>
</ul>

We can pretty much cover all of these points the same way that we did the User-Todo relationship using the foreign key but there is one relationship that doesn't follow the one to many (1:M) pattern. A Todo can have many categories and a Category can have many todos. This is called a many to many (M:M) association and requires a <em>join table</em> to deal with this type of relationship. 

A join table here does the job of connecting the todos table and the categories table. The foreign keys reside in the join table and create a 1:M association from the join table to both the todos table and the categories table. 

In part 2 we will build the tables and the models.

<!-- <h4>Creating tables with migration files</h4>
<span class="terminal">$ rails generate migration create_users.rb</span>





The second part of the link happens when we create the object model. 






ALL IS NOT LOST:
If you accidently forgot to add a column you can simply create a new migration. I forgot to add an sms column to users so ...
 -->



