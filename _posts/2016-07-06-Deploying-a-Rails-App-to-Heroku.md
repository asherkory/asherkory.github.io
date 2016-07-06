---
layout: post
title: Deploying a Rails App to Heroku
tags: ruby, rails, heroku
---

Playing around with your homemade apps in a local environment is fun, but what happens when you want to share what you've made? Deploy to Heroku. The process is fairly straightforward, and [the official guide](https://devcenter.heroku.com/articles/getting-started-with-rails4) is a great resource.

### The Database

The default database for a new Rails app is SQLite. But Heroku only uses PostgreSQL (for reasons explained [here](https://devcenter.heroku.com/articles/sqlite3)). Since I initially built with app with the default database, I first needed to convert manually to PostgreSQL.

Caveat: If you just have some dummy data in your database and don't care about preserving it, then this simple method should work just fine. On the other hand, if you need to preserve your data, I recommend [this Stack Overflow post](http://stackoverflow.com/questions/6710654/change-from-sqlite-to-postgresql-in-a-fresh-rails-project).

First, in my Gemfile I removed the gem `sqlite3` and replaced it with `pg`, and ran `bundle install`.

Next, I edited `config/database.yml`. If you started with default SQLite it should look something like this:

```
default: &default
  adapter: sqlite3
  pool: 5
  timeout: 5000

development:
  <<: *default
  database: db/development.sqlite3

test:
  <<: *default
  database: db/test.sqlite3

production:
  <<: *default
  database: db/production.sqlite3
```

Change the adapter to `postgresql`. Rename the databases. Now it should look more like this:


```
default: &default
  adapter: postgresql
  pool: 5
  timeout: 5000

development:
  <<: *default
  database: db/hb_development

test:
  <<: *default
  database: db/hb_test

production:
  <<: *default
  database: db/hb_production
```

Make sure [Postgres](http://postgresapp.com/) is running on your local machine. If you're on a Mac, just doubleclick the Postgres app like it's any normal app, and it will run in the background.

Next, run `rake db:setup` and `rake db:migrate`. Tada!

### Gemfile

Heroku requires the `rails_12factor` gem to be placed in the production group of your Gemfile. Be sure to run `bundle install` after adding it! This will "enable features such as static asset serving and logging on Heroku," according to the official docs.

It's also a good idea to specify which version of Ruby you're using at the end of the Gemfile, e.g. `ruby "2.2.1"`.

### Deployment

Make sure you have a [Heroku account](https://signup.heroku.com/devcenter) and you've installed the [Heroku toolbelt](https://toolbelt.heroku.com/). Log in from your command shell with `heroku login`. Heroku relies on git for deploying, so be sure you've commited your latest changes in git.

Run `heroku create` from your app's home directory. Now a new remote has been added! You can push up your code with `git push heroku master`.

Finally, migrate the database: `heroku run rake db:migrate`. You'll notice this looks like the normal rake migration command, but prefaced with `heroku run`. Anything starting with `heroku run` will be executed on a Heroku dyno, [explained here](https://devcenter.heroku.com/articles/dynos).

Want to see it live in your browser? Type `heroku open`!

[Check out the live Homebrewer Helper app.](https://nameless-brushlands-19698.herokuapp.com/)

