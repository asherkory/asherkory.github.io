---
layout: post
title: Using Super to Extend ActiveRecord
tags: ruby, rails
---

Here's a neat trick I picked up recently. Ruby has a `super` keyword: when you call it inside a method, `super` calls the parent class's implementation of the method with the same name. You can extend ActiveRecord's built-in methods -- like getters and setters -- quite neatly with `super`.

For example, let's say you have a User model with an email_address attribute. When you update a user's email address, you want to send a confirmation email to their new address. 

So, you would explicitly define an `email_address=()` method in the User model. Normally ActiveRecord automatically gives you this method. Inside the method, you'd call `super`. This will call the original implementation of the ActiveRecord setter. Then below that you'd write some code to send a confirmation email (or call a separate method that does that, or whatever -- this example is a bit contrived). At the end of the method, you'd return the new email address.

This is a useful way to add extra functionality to the automatically-generated methods from ActiveRecord without overwriting them entirely.
