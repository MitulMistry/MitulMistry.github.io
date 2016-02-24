---
layout: post
title:  "Sinatra Project - Simple Blog"
date:   2016-02-24 16:00:00
categories: ruby sinatra
---
After learning the basics of Ruby and ORMs, I've been working on a project leveraging the Sinatra development framework. Along with Activerecord, I've used it to build a simple blogging application and I will describe the process of developing it here.

The project uses basic Model View Controller architecture. It has a few simple models that interact with various routes in the controller which then composes and serves the different blog pages through the views.

**Models:**

The first thing I worked on were the models that would represent the data necessary for the blog to work using Activerecord. This includes `Users` which would write and edit `Posts`, which would in turn have `Tags`. In addition, I used the model `PostTags` as well, but only as an intermediary for a join table to connect `Posts` and `Tags`. This was necessary because a `Post` has many `Tags`, and a `Tag` has many `Posts`, and any time there's a mutual 'has many' relationship between models, a join table is needed.

These associations provide a useful way to list different types of `Posts`. All `Posts` belong to a `User`, so you can easily list all of a `User`'s `Posts`, or all of a `Tag`'s `Posts` as well. Providing an easy way to organize information is critical to building a successful application, and ActiveRecord provides ample ways of doing just that.

*The `Post` model code:*
{% highlight ruby %}
class Post < ActiveRecord::Base
  belongs_to :user
  has_many :post_tags
  has_many :tags, through: :post_tags

  extend FindBySlug

  def slug
    self.title.downcase.gsub(" ", "-")
  end

  def readable_date
    self.created_at.strftime("%b %d, %Y")
  end
end
{% endhighlight %}


**Controllers:**

Unlike a framework like Rails, Sinatra has a unified router and controller. I used a separate controller for each model which helped organize all the different routes and actions. The `Users` controller focuses on signing up and logging in `Users`. The `Posts` controller deals with the majority of the application's functionality, namely the creation, editting, and deletion of `Posts` as well as `Tags` (since `Tags` are created in the same page as `Posts`). Finally, the `Tags` and application  controllers focus on a few utility tasks. As a whole, the controllers work together to facilitate the functionality of the website and process all the data.

*An route/action to render the new post creation form:*
{% highlight ruby %}
get '/posts/new' do
  if logged_in?
    @tags = Tag.all
    erb :'posts/new'
  else
    redirect to '/users/login'
  end
end
{% endhighlight %}


**Views:**

Just like Rails, Sinatra uses `ERB` files (Embedded Ruby) to dynamically generate html web pages and serve requested content. The views correspond to the routes/actions in the associated controllers, so there are views for showing a user page, displaying a login form, and so on. Using Bootstrap for basic frontend styling helps make the application look at least palatable (as opposed to hideous unstyled html). It may be a bit superficial, but it helps the application look like a complete thought.

*The ERB code to display all the posts for a tag:*
{% highlight ERB %}
<h2><%= @tag.name %></h2>
<% @tag.posts.each do |post| %>
  <h3><a href="/posts/<%= post.slug %>"><%= post.title %></a></h3>
  <h4>By: <a href="/users/<%= post.user.slug %>"><%= post.user.username %></a></h4>
  <p><strong><%= post.readable_date %></strong></p>
  <p><%= post.content %></p>
<% end %>
{% endhighlight %}

In conclusion, the project took longer than I expected due to the necessary refactoring to deal with things like processing the form data during post creation and editting as well all the individual gears necessary for even a simple web project like this involving a back end. It was certainly an educational experience and a good step towards learning to build professional grade applications.

You can find the source code for this project [here on Github.][github-repo]

[github-repo]:      https://github.com/MitulMistry/sinatra-simple-blog