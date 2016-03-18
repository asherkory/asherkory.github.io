---
layout: post
title: Building a Sinatra App
tags: learn-co, ruby, sinatra, mvc
---

As the second major assessment project for the [Flatiron School Learn Verified program](https://learn.co), I was required to build a Sinatra app following the model-view-controller structure, using a database and ActiveRecord, with user accounts. I've come to look forward to these end-of-section final projects: coming up with an independent idea and implementing it from the ground up has been pretty fun and rewarding.

I decided to follow the theme of my CLI Ruby gem project, and build a "Costume Closet" app for keeping track of a user's replica costumes. The model associations of the app quickly fell into place: users have many costumes, costumes belong to a media source and a user.

After some tough bug-squashing in earlier Sinatra labs, I was surprised by how smoothly this app came together. I tested in the browser as I went with the help of a gem called Shotgun (which allows you to refresh a page to see changes, no need to restart the local server), and a gem called Tux that allows you interact with the database and ActiveRecord in the terminal. But I didn't run into any real brainbending issues. (Just the usual assortment of typos and silly quickly-fixed mistakes.)

I opted this time to concentrate solely on functionality. Yes, this is a web app, but I chose to write only the bare-bones HTML necessary to made the app readable and working. No fancy css or colors -- there will be time for that later, and I believe it's more important to make my app work than to make it pretty.

I can see there are a lot of ways this kind of basic app could be expanded and built upon, and look forward to streamlining the process in Rails -- the next section of curriculum I'll be tackling!