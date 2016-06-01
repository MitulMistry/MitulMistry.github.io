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

There's a lot of code that went into seemingly basic functionality such as creating new notes, displaying notes based on a tag, and pagination without reloading. I'll go over some of it briefly.

This portion of code attaches an event listener to the new tag form and processes the request to create a new form:

{% highlight javascript %}
function attachListeners(){
  //create new tag
  $('form#new_tag').submit(function(event) {
    event.preventDefault(); //prevent form from submitting the default way and reloading page

    var name = $('#tag_name').val();

    if (validator.validateTag(name)) {
      var values = $(this).serialize();
      var posting = $.post('/tags', values);

      posting.done(function(data) {
        $('form #tag_name').val(''); //clear form input
        loadTags();
      });
    }
  });

.........
}
{% endhighlight %}

`event.preventDefault()` is necessary in order to prevent the default behavior of the form, which is to submit and reload the page. Anything with a `$` is shorthand for `jQuery`, so it's invoking a `jQuery` object or function. `var name = $('#tag_name').val();` gets the tag name value from the text input of the form sets it to a variable. It validates it client side with a custom Validator class, and if the data passes, it processes the request.

`var values = $(this).serialize();` serializes the data in the form via `jQuery` and prepares it to be sent. `var posting = $.post('/tags', values);` makes a `jQuery` POST request to the tags controller with the data from the form that has been serialized. Once the posting is finished, we set a `.done` callback to clear the form input field and then call the `loadTags()` function.

{% highlight javascript %}
function loadTags(){
  $.getJSON('/tags').success(function(response){
    var tags = response;

    $('div#tags').html(''); //clear tags

    for(var i = 0; i < tags.length; i++){
      $('div#tags').append(generateTagLink(tags[i]));
    }

    addTagListeners();
  });
}
{% endhighlight %}

Here, we make a `jQuery` `.getJSON` request to ask for `JSON` data from our tags controller. Since we set it up with a `respond_to` earlier, the controller uses `ActiveModel Serializers` to send the tag model as serialized `JSON` data to `jQuery`. Yes, this is a lot of serialization back and forth, but it's necessary if we want two way `AJAX` communication between our front and back ends.

We get our tags as `JSON` data and iterate through it to generate the `HTML` necessary to display each tag. From there, we call the `addTagListeners()` function to add click events to each tag, so when we click on a tag it will then make another `AJAX` request to get all the notes associated with the clicked tag.

There's a lot more to it, but this gives a good sense of the basics of the project. You can take a look at the `JavaScript` portion of the project [here][github-js] and explore some of the additional functionality yourself.

You can see the source code for the whole project [here on Github][github-repo].

[forms]: http://mitulmistry.github.io/ruby/rails/rails-forms/
[angular]: https://angularjs.org/
[jquery]: https://jquery.com/
[devise]: https://github.com/plataformatec/devise
[rails-project]: http://mitulmistry.github.io/ruby/rails/rails-project/
[json]: https://en.wikipedia.org/wiki/JSON
[activemodel-serializers]: https://github.com/rails-api/active_model_serializers
[controllers]: https://github.com/MitulMistry/post-it/tree/master/app/controllers
[github-js]: https://github.com/MitulMistry/post-it/tree/master/app/assets/javascripts
[github-repo]: https://github.com/MitulMistry/post-it
