---
layout: post
title:  "Rails Project - Storyplan"
date:   2016-03-21 16:00:00
categories: ruby rails
---
Making a Rails application is hard. There are so many compoments, considerations, and features to be dealt with that the process can quickly become complicated and overwhelming, especially when you're just starting out as a developer. For my first full-fledged Rails project, I'll give an overview on how I broke down the process and attempted to build out the application one piece at a time.

The application is tentatively titled Storyplan and is intended as a tool to help writers plan out stories. Writing a story often requires a great deal of structure, planning, and constituent components, and I wanted to create a website that would make it easier to build and keep track of everything.

##Models##

I started out by mapping out the models and migrations. Using the `rails generate resource` command, you can use Rails to generate a model, database migration for that model, and a controller, and I used it to get a head start. My models were as follows:

* `User` - Generated using the Devise gem for authentication
* `Story` - Belongs to a `User`
* `Chapter` - Belongs to a `Story`
* `Character` - Has many `Chapters` through a join table and vice versa, belongs to a `User`
* `Audience` - Has many `Stories` through a join table and vice versa
* `Genre` - Has many `Stories` through a join table and vice versa
* `Comment` - Belongs to both a `User` and a `Story`

With these models, I was able to set up the associations necessary to do things like show all the `Stories` of a `User` or all the `Characters` in a `Story`. This also makes it easier for a user to explore the different connections of a writer's work.

##Routes and Controllers##

Unlike Sinatra, Rails has a separate router from its controllers and uses it to map the routes to actions in those controllers. My routes were mostly resources for the above mentioned models, with a few nested resources to do things like the find all the `Characters` of a particular `User`.


##Views##



You can find the source code for this project [here on Github.][github-repo]

[github-repo]:      https://github.com/MitulMistry/rails-storyplan