---
layout: post
title: AngularJS Final Project
tags: angular, rails
---
I'm wrapping up the [Flatiron Learn Verified](https://learn.co) curriculum final project: an AngularJS app powered by a Rails API. I chose to build a choose-your-own-adventure game app, with inventory and character creation features.

### More Rails Associations
This time I got to use a new-to-me ActiveRecord relationship in my models: the many-to-many self-referential association. For example, think of Twitter. Users can follow many other users, but can also be followed by many other users themselves. I created storycards -- each an individual segment of the choose-your-own-adventure storyline -- which could have multiple children storycards (the story choices), and multiple parents. I found [this article by @jbmilgrom](https://medium.com/@jbmilgrom/active-record-many-to-many-self-join-table-e0992c27c1e#.ewbjc6jci) very helpful. This necessitated building a join table `choices` and referring to storycards as parents and children. The final association in the `Storycard` model looks like this:

```ruby
  has_many :parents, through: :parent_choices, source: :parent
  has_many :parent_choices, foreign_key: :child_id, class_name: "Choice"

  has_many :children, through: :child_choices, source: :child
  has_many :child_choices, foreign_key: :parent_id, class_name: "Choice"
```

### Getting Angular and Rails to Play Nicely Together

ngResource is an Angular module that provides helpful ways to interact with RESTful APIs. Particularly useful is the way you can declare a "class" object and then interact with it using familiar-sounding actions like `get`, `save`, and `delete`. 

However, I discovered that ActiveModel JSON serializers don't play perfectly well with $resource. For example, the $resource `query()` action makes a GET request to an API endpoint and expects an array of objects in return. But ActiveModel serializes a collection such that it returns an object containing an array of objects. I didn't end up using the `query()` action, as useful as it seemed at first.

One other important takeaway from integrating Angular with Rails came when I was working on dealing with data from form submissions. Technically Angular doesn't make an actual form submission -- it makes a POST (or PUT) request to an API endpoint. And Angular does not send along the authenticity token that Rails expects from its own forms. You must include `skip_before_filter :verify_authenticity_token` in the Rails controller to bypass Rails' default behavior.

### Future Improvements

This project ended up conceptually more ambitious than I expected. I have many ideas for features I could implement in the future, or ways to improve what I've already built. A few things on the list:

- Automatically remove items from the inventory when they're used up in-game. For example, if you bribe someone in the story, your money should disappear from your inventory.
- When removing items from inventory, instead of an alert pop-up, a DOM element or a modal should temporarily appear to notify you.
- Allow for saving your game progress. Incorporate your created character's properties into the game text.
- Transform this app into a framework for creating your own choose-your-own-adventure games. Implement user authentication, and add an admin panel for creating storycards, the branching story tree, and other game components.