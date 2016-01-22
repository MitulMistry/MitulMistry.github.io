---
layout: post
title:  "Sinatra Project - Simple Blog"
date:   2016-01-22 22:20:00
categories: ruby sinatra
---
After learning the basics of Ruby and ORMs, I've been working on a project leveraging the Sinatra development framework. Along with Activerecord, I've used it to build a simple blogging application and I will describe the process of developing it here.

The project uses basic Model View Controller architecture. It has a few simple models that interact with various routes in the controller which then composes and serves the different blog pages through the views.

**Models:**

The first thing I worked on were the models that would represent the data necessary for the blog to work using Activerecord. This includes `Users` which would write and edit `Posts`, which would in turn have `Tags`. In addition, I used the model `PostTags` as well, but only as an intermediary for a join table to connect `Posts` and `Tags`. This was necessary because a `Post` has many `Tags`, and a `Tag` has many `Posts`.

**Controllers:**

-to route paths to actions


**Views:**

-erb files using embedded ruby to dynamically serve requested content.
-using Bootstrap for basic frontend


Conclusion



You can find the source code for this project [here on Github.][github-repo]

[github-repo]:      https://github.com/MitulMistry/sinatra-simple-blog