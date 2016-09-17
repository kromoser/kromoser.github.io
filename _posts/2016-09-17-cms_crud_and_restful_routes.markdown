---
layout: post
title: "MVC, CMS, and the need for RESTful Routes"
date: 2016-09-17 10:52:00 +4000
---

In [my last post](http://kromoser.github.io/2016/08/31/user_has_many_recipes_needs_a_way_to_store_them/), I discussed the issues and questions around the conception and design of my first Sinatra application, a simple way to store recipes from around the web. The basic way the app works is a user logins in or signs up and then can create, edit or delete recipes, each of which has a title, url, ingredients, and categories. And of course, a user can only modify or delete her own recipes.

The basic database structure of the app is as follows:
  - A user `has_many` recipes; a recipe `belongs_to` a user.
  - A recipe `has_many` ingredients and an ingredient `has_many` recipes.
  - A recipe `has_many` categories and a category `has_many` recipes.
  - A user `has_many` ingredients and categories through recipes.

And the basic user interface is:
  - Users can login and sign up.
  - Users can create, edit, and delete their own recipes.
  - Users can create ingredients and categories by creating or editing a recipe.
  - Users can see a list of all or their recipes, or browse recipes by ingredient or category.

The most revelatory concept I've learned from the Sinatra section of the Learn curriculum is that so much of what we do on the web is just submitting forms and organizing the data the forms provide. So, in The Recipe Box, when a user signs up or logs in, she's submitting a form, and the value of `session[:id]` is set to the user's id. This allows us to protect and personalize our views, because at any point in our controller, we can find out who the user is by querying the Users table with one of the many wonderful and glorious ActiveRecord method `User.find(session[:id])`.

When a user creates a recipe, she is submitting a form that instantiates a new `Recipe` object, and associates its url, ingredients, and categories. Again, through the magic of ActiveRecord, we can do all sorts of great things, assuming our table are set up correctly. We can get an array of all of her recipes:
````
@user = User.find(session[:id])
@recipes = @user.recipes.all
````
We can get every unique ingredient or category in all of those recipes:
````
@user.ingredients.uniq
@user.types.uniq
````
Interestingly, it took me a while to realize how simple the above code could be. Initially, I built out inefficient and clunky helper methods that searched through all of a user's recipes and added their ingredients and categories to an array and then used that array to create a list in my views. But of course ActiveRecord already has that functionality out of the box. That is:
````
class User < ActiveRecord::Base
  has_many :recipes
  has_many :ingredients, through: :recipes
  has_many :types, through: :recipes
````
(I also used some of ActiveRecord's other methods here, like `has_secure_password` and `validates_presence_of`)

While building out my forms and views, I came across the need to do some error handling. It's easy to use `validates_presence_of` a `Recipe` title, but what good does it do if a user doesn't understand why her recipe isn't being created? I used the rack-flash gem, which stores error messages in a hash. I added some markup in my ERB layout template, so that if there were an error message, it would display it at the top of the page:

````
<% if flash.has?(:message) %>
  <div class="error-message"><%= flash[:message] %></div>
<% end %>
````
I got everything working pretty nicely in terms of function, so I moved on to styling the pages with some CSS, and after spending a few fun hours [with this tutorial](https://css-tricks.com/better-tabs-with-round-out-borders/) getting my wrapper div to look just like a recipe card, I did some refactoring, cleaning up, and removing as much logic from my views as possible.

A big part of this project for me was keeping in mind that *no piece of a big puzzle is itself complex.* Interacting with a server is just a matter of submitting a form. Showing all recipes is just a matter of iterating through an array. My application has a good number of routes, but by keeping them RESTful and organized in different controllers, it doesn't get overwhelming. With a puzzle, you might start by separating out pieces by color, so you know where to find everything. And with a web app, every model, view, and controller lives in its own home. When each piece is simple and clear, the whole app, in all its complexity, also becomes clear, manageable, and a pleasure to use.

