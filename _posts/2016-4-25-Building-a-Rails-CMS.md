---
layout: post
title: Building a Rails CMS
tags: learn-co, ruby, rails
---

It's Rails assessment time! I'm chugging along at the [Flatiron School Learn Verified program](https://learn.co), and just completed the Rails section. For this section's final project, I was tasked to a build a content management system with a couple of interesting requirements (complex associations, nested forms, data validations, Omniauth...). I decided to make a Homebrewer app for tracking your homebrewed meads, beers, and wines.

### Has Many Through & Nested Forms

The most challenging part involved building a has-many-through relationship and making a super nested form to assign data all at once. My `Brew` model has many ingredients, and an ingredient can have many brews. They are related through a `recipes` join table, which additionally stores information about the quantity of the ingredient used in a brew. 

I wanted a user to be able to add a new brew with all of its ingredients plus the quantities of each ingredient all at once, in a single form, with the Quantity and Ingredient Name fields displayed in the proper places. I ended up needing to make sure that the Brew model `accepts_nested_attributes_for :recipes`, and Recipe `accepts_nested_attributes_for :ingredient` (note the singular, not plural, ingredient). Then I made a Brew form using the `form_for` helper. Inside of this went a `fields_for :recipes` helper, containing the Quantity field, and nested inside that, a `fields_for :ingredient` helper with the ingredient Name field. In order to create canonical instances of ingredients (instead of creating, say, a new database entry every time a user enters Honey as an ingredient), I made a custom `ingredient_attributes` method in my `Recipe` model which will find the ingredient if it already exists, or a create a new one if it doesn't.

I found these sources particular helpful in my quest to make this super-nested form: 
[Stack Overflow: Has Many Through Nested Forms](http://stackoverflow.com/questions/13506735/rails-has-many-through-nested-form)
[Stack Overflow: How to Edit Attributes of Join Model in Nested Forms](http://stackoverflow.com/questions/2182428/rails-nested-form-with-has-many-through-how-to-edit-attributes-of-join-model)

### Nested Resources

Users have the ability to add notes to each of their brews. Since I never want notes to be accessible outside of the context of the brews they belong to, I nested notes under brews in `routes.rb`:

```resources :brews do 
    resources :notes, only: [:new, :create, :edit, :update, :destroy]
  end ```

  However, the `form_for` helper automatically expects simpler non-nested note routes. In order to keep using the `form_for` magic, and as suggested by a few Stack Overflow sources, I decided I would also declare non-nested `:notes` resources. 

### Omniauth

Initially I wanted to try allowing users to sign in via Google. In the lessons so far I've only practiced hooking up Facebook with Omniauth. Unfortunately I ran into an issue with OpenSSL and security certificates -- which some tutorials actually warned me about -- and attempting to fix the issue led me down a rabbit hole far beyond my current skill level. Instead I ended up sticking with a Facebook login.

An interesting note: be sure to set a `limit: 8` on the `:uid` integer column in the `users` migration to ensure a bigint! Facebook's UIDs are not small.

