---
layout: post
title: Building a Ruby Gem
tags: learn-co, ruby
---

I'm currently going through the Flatiron School's [Learn Verified](https://learn.co) program for full stack web development. To wrap up the section on object-oriented Ruby, we're supposed to build a gem that provides a CLI interface for an external data source. (Basically: scrape an existing website, wrap up the data in a CLI, package as a gem installable by anyone.) 

I was pretty excited to work on a project of my own design and implementation, as opposed to the labs with their tight requirements featured so far in the curriculum. 

Being a nerd - specifically, a member of two Star Wars costuming organizations - I came up with an idea pretty quickly: make a CLI to view the membership standards for a costume. What pieces of Luke Skywalker's Tatooine outfit would you need to create in order to join the Rebel Legion? My gem has the answer! (Or rather, [rebellegion.com](http://www.rebellegion.com/) has the answer, and I have awkwardly borrowed it.)

### The gem

[Check it out](https://github.com/shinyrachel/rebel_legion), or install via "gem install rebel_legion". There are some messy parts, I will freely admit, and I look forward to refactoring someday. The scraper only nabs the first page of costumes in each costume category - the Rebel Legion accepts a starting number of characters, as it includes many well-sourced Expanded Universe (now Legends) costumes. And unfortunately, the Rebel Legion is a bit inconsistent in how they display details and requirements for each individual costume, so my data sometimes looks a bit inconsistently messy too. I'm sure there's more I could have done to rectify this. On the whole, though, the code logic itself came together very smoothly.

### Challenges

The code itself was, surprisingly, the easiest part. I suppose the Learn.co curriculum has done a good job preparing us for this sort of project. HTML scraping, object relationships, all that good stuff.

There were a few places where I spent a lot of time googling and researching: directory and file structure (aka where does everything go, how does it relate, what are all these files and folders for, and how do I make sure everything loads properly?), and how to build and release the gem after everything's finished. 

Most sources differed in how to publish the gem, interestingly: some suggested using "gem push", some suggested rake commands, and others a combination to first build the gem, test it, and release it into the wild. Perhaps this is unsurprising, as a Ruby philosophy is "there are many ways to do the same thing; do what you like best".

### Takeaways

Although the project solidified my understanding of what the Learn curriculum has taught up to this point, my biggest takeaways were what weren't explicitly taught: How gems are used on a deeper level, load paths, dependencies, versioning standards... I even picked up a bit about Rake (and Jekyll, this blog platform!).