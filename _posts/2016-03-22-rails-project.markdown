---
layout: post
title:  "Rails Project - Storyplan"
date:   2016-04-22 13:00:00
categories: ruby rails
---
Making a Rails application is hard. There are so many components, considerations, and features to be dealt with that the process can quickly become complicated and overwhelming, especially when you're just starting out as a developer. For my first full-fledged Rails project, I'll give an overview on how I broke down the process and attempted to build out the application one piece at a time.

The application is tentatively titled Storyplan and is intended as a tool to help writers plot out stories. Writing a story often requires a great deal of structure, planning, and constituent components, and I wanted to create a website that would make it easier to build and keep track of everything.

**Models**

I started by mapping out the models and migrations. Using the `rails generate resource` command, you can use Rails to generate a model, database migration for that model, and a controller, and I used it to get a head start. My models were as follows:

* `User` - Generated using the Devise gem for authentication.
* `Story` - Belongs to a `User`.
* `Chapter` - Belongs to a `Story`.
* `Character` - Has many `Chapters` through a join table and vice versa, belongs to a `User`.
* `Audience` - Has many `Stories` through a join table and vice versa.
* `Genre` - Has many `Stories` through a join table and vice versa.
* `Comment` - Belongs to both a `User` and a `Story`.

With these models, I was able to set up the associations necessary to do things like show all the `Stories` of a `User` or all the `Characters` in a `Story`. This also makes it easier for a user to explore the different connections of a writer's work.

These models have a variety of validations to ensure bad data isn't entered into the system, as well as class and instance methods to give the models additional functionality beyond being mere data containers.

*The `Story` model:*
{% highlight ruby %}
class Story < ActiveRecord::Base
  belongs_to :user
  has_many :story_genres
  has_many :genres, -> { distinct }, through: :story_genres
  has_many :story_audiences
  has_many :audiences, through: :story_audiences
  has_many :chapters, dependent: :destroy #destroys all chapters belonging to it when the story is destroyed
  has_many :characters, through: :chapters
  has_many :comments

  validates :name, presence: true
  validates :user_id, presence: true
  validates :target_word_count, numericality: { only_integer: true }, allow_blank: true
  validates :overview, length: { maximum: 4000 }

  extend ClassOrderable

  def recent_comments
    self.comments.order('created_at DESC').last(10)
  end
end
{% endhighlight %}

**Routes and Controllers**

Unlike Sinatra, Rails has a separate router from its controllers and uses it to map the routes to actions in those controllers. My routes were mostly resources for the above mentioned models, with a few nested resources to do things like the find all the `Characters` of a particular `User`.

*[See the routes file here.][storyplan-routes]*

As far as controllers are concerned, I used one controller per model with a few miscellaneous controllers thrown in as well to handle other tasks. This is basically a CRUD (Create, Read, Update, Delete) application, so many of the controllers handle similar tasks. So for example, the `Stories` controller handles serving all the stories for the stories index, showing a particular story, serving a form for a new story or editing an old one, processing the creation and update of a story based on input from the form, and destroying a story as well. These correspond to [RESTful routes][restful-routes] (Representational State Transfer): Index, Show, New, Create, Edit, Update, Destroy. This is simply a paradigm to keep things consistent across different web applications and is an example of Rails's philosophy of convention over configuration.

There are other levels of complexity involved, such as authorization (making sure the user creating or updating a story has permission to do so) or white-listing the data received (using [strong parameters][strong-params]) from the form so no improper data is forcibly entered.

*[See the story controller here.][storyplan-story-controller]*

**Views**
For the front end, I used the [Bootstrap-Sass][bootstrap-sass] gem for Rails. It has a very generic look to it, but for now it works to help it look presentable.

The views use a variety of partials to make the display code reusable. For example, for each model, there is a partial that creates a sort of visual card for it that includes pertinent information. I then use a grid system to display collections of that model.

*Code for the story card partial:*
{% highlight erb %}
<% story = item unless story %>
<div class="col-sm-12 col-md-4">
  <div class="thumbnail">
    <div class="caption">
      <h3><%= link_to story.name, story %></h3>
      <p><strong>Written by:</strong> <%= link_to story.user.username, writer_path(story.user) %></p>
      <p><strong>Overview: </strong><%= story_truncated_overview(story) %></p>
      <p><strong>Target word count:</strong> <%= story.target_word_count %></p>
      <% if !story.audiences.empty? %>
      <p><strong>Target audience:</strong>
        <% story.audiences.each do |audience| %>
          <%= link_to audience.name, audience, class: "label label-warning" %>
        <% end %>
      </p>
      <% end %>
      <% if !story.genres.empty? %>
      <p><strong>Genres:</strong>
        <% story.genres.each do |genre| %>
          <%= link_to genre.name, genre, class: "label label-primary" %>
        <% end %>
      </p>
      <% end %>
    </div>
  </div>
</div>
{% endhighlight %}

So when you go to the stories index page, it displays a grid of stories that simply repeat that model partial with new information from each collection item.

**Authentication**

Setting up a secure login/logout system can be challenging considering the number of security threats out there. You can roll your own authentication using [bcrypt][bcrypt] to securely store password hashes, but I wanted something with more functionality built in. That's why I used the [Devise][devise] gem which provides for the creation of its own `User` model and gives you additional tools like email confirmation should you desire them. Most importantly, it's battle tested and commonly used to provide secure authentication for Rails applications.

In addition, I used Devise to provide OAuth authentication for Facebook, so you can securely sign in with a Facebook account as well.

**Conclusion**

Overall, this is just a small overview of what turned out to be a relatively complex project. There's a lot more functionality I'd like to build eventually to make this more of a viable tool for writers, including building more of a front end, but for now it serves its purpose.

You can find the source code for this project [here on Github.][github-repo]

[github-repo]: https://github.com/MitulMistry/rails-storyplan
[storyplan-routes]: https://github.com/MitulMistry/rails-storyplan/blob/master/config/routes.rb
[restful-routes]: http://guides.rubyonrails.org/routing.html
[strong-params]: http://edgeguides.rubyonrails.org/action_controller_overview.html#strong-parameters
[storyplan-story-controller]: https://github.com/MitulMistry/rails-storyplan/blob/master/app/controllers/stories_controller.rb
[bootstrap-sass]: https://github.com/twbs/bootstrap-sass
[bcrypt]: https://github.com/codahale/bcrypt-ruby
[devise]: https://github.com/plataformatec/devise
