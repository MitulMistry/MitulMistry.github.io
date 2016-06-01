---
layout: post
title:  "jQuery Rails App"
date:   2016-06-01 15:30:00
categories: javascript rails
---

With `Rails`, I've been working on a lot on dealing with the back end through `ActiveRecord` and dealing with Rails templates and [form helpers][forms]. However, modern web applications focus on spontaneous interaction, meaning single page apps with no page reloads. There are a variety of front end frameworks - like [Angular][angular], which I will be working with later - that many of the websites we use every day are built with. However, it's possible to create interactive applications solely with [jQuery][jquery] - a `JavaScript` library that most websites leverage in one way or another.

For this project, I wanted to create a simple note taking application kind of like Google Keep where the user can create and display notes without reloading the page. This creates a smoother, more natural user experience that I achieved with a `jQuery` front end.

**Rails Back End**

Before focus on user interaction, I organized the back end using `Rails` controllers and models. I used [Devise][devise] to set up a user model and authentication since notes wouldn't be public - they needed to be limited to an individual user. After that, I used `rails generate resource` to generate the files for notes and tags. The controllers are set up similarly to those in my [last Rails project][rails-project], with the exception of needing to account for the `AJAX` requests that would be coming from the `jQuery` front end.

`AJAX` stands for Asynchronous JavaScript and XML, but most of the time it's used with `JavaScript` as XML has become deprecated in favor of `JSON` - [JavaScript Object Notation][json].

Here is an example of and action in the notes controller handling both `HTML` and `JSON` requests:

{% highlight ruby %}
def show
  @note = Note.find(params[:id])
  respond_to do |format|
   format.html { render :show }
   format.json { render json: @note }
 end
end
{% endhighlight %}

With `respond_to`, we're able to handle requests for different formats. If we have a link that makes an `HTML` request for a note, we render the note template. However, if we get a request for `JSON`, we render the note in serialized `JSON` format.

What is serialization and how does `Rails` do it? There are a variety of ways, but I used [ActiveModel Serializers][activemodel-serializers] which implicitly turns @note into `JSON` data.

You can take a look at my controllers [here][controllers]. Once the back end is set up, we can move on.

**jQuery Front End**



You can read more about Rails forms in the [Rails Guides][rails-guides].

[forms]: http://mitulmistry.github.io/ruby/rails/rails-forms/
[angular]: https://angularjs.org/
[jquery]: https://jquery.com/
[devise]: https://github.com/plataformatec/devise
[rails-project]: http://mitulmistry.github.io/ruby/rails/rails-project/
[json]: https://en.wikipedia.org/wiki/JSON
[activemodel-serializers]: https://github.com/rails-api/active_model_serializers
[controllers]: https://github.com/MitulMistry/post-it/tree/master/app/controllers
[rails-guides]: http://guides.rubyonrails.org/form_helpers.html
