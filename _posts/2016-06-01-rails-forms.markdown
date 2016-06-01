---
layout: post
title:  "Rails Forms"
date:   2016-06-01 12:15:00
categories: ruby rails
---

To me, forms in web applications feel antiquated. There are better ways to get data from a user, such as getting access to a user's other accounts (like Twitter or Facebook) via [OAuth][oauth] and populating data from there, but that's not always an option. For now, we still have to input data manually and that means we need to use forms.

Forms are obnoxious. You have to pick out which input types are best for your scenario (text, radio buttons, dropdown lists, etc.). You need to style it with `CSS` so it looks presentable You need to define a route for the form to submit data to the backend. You have to deal with data validations and handle scenarios where users input unacceptable data. And  you have to deal with a host of other miscellaneous issues that come with such complex interaction.

Here's a sample `HTML` form:

{% highlight html %}
<form action="users" method="post" accept-charset="UTF-8">
  <fieldset>
    First name:<br>
    <input type="text" name="firstname"><br>
    Last name:<br>
    <input type="text" name="lastname"><br><br>
    <input type="submit" value="Submit">
  </fieldset>
</form>
{% endhighlight %}

Fortunately, `Rails` can help you generate forms using a few different tools which we'll look at here.

**form_tag**

`form_tag` is a Rails form helper used to help you build a basic form.

Here's a sample `form_tag` form:
{% highlight erb %}
<%= form_tag("/users") do %>
  Name: <%= text_field_tag "name" %><br>
  Email: <%= text_field_tag "email" %>
  <%= submit_tag "Create User" %>
<% end %>
{% endhighlight %}

The actual `HTML` that's produced will look like this:

{% highlight html %}
<form action="/users" accept-charset="UTF-8" method="post">
 <input name="utf8" type="hidden" value="&#x2713;" />
 <input type="hidden" name="authenticity_token" value="TKTzvQF+atT/XHG/7h48xKVdXvILdiPj83XQhn2mWBNNhvv0Oh5YfAl2LM3DlHsQjbMOFVsYEyOwj+rPaSk3Bw==" />
 Name: <input type="text" name="name" id="name" /><br />
 Email: <input type="text" name="email" id="email" />
 <input type="submit" name="commit" value="Create User" />
</form>
{% endhighlight %}

Just with a few simple lines of `ERB`, we're able to create a fully realized form. It has an action and method already defined, as well as an authenticity token which is used to help prevent [cross-site request forgeries][csrf].

However, `form_tag` is limited, specifically when it comes to dealing with forms directly related to backend `models`. If you want a different action method (such as wanting to reuse the same form for creating and editing a model), you have to specify it manually. It also won't populate the form with data that's already there. That's where `form_for` comes in.

**form_for**

`form_for` is like `form_tag` in that it helps you generate a form with `ERB` tags in a view template. However, `form_for` directly connects with a `model` in your Rails application and makes it easy to allow the creation of new `model` instances in your database.

Here's a sample form_for form:
{% highlight erb %}
<%= form_for @post do |f| %>
 <%= f.text_field :title %>
 <%= f.text_area :content %>
 <%= f.submit %>
<% end %>
{% endhighlight %}

This creates the following `HTML`:
{% highlight html %}
<form class="edit_post" id="edit_post" action="/posts/1" accept-charset="UTF-8" method="post">
 <input name="utf8" type="hidden" value="&#x2713;" />
 <input type="hidden" name="_method" value="patch" />
 <input type="hidden" name="authenticity_token" value="nRPP2OqVKB00/Cr+8EvHfYrb5sAkZRtr8f6dzBaJAI+cMceR0fUatcLWd4zdwYCpojW2J3QLK6uyBKeFAgZvmw==" />
 <input type="text" name="post[title]" id="post_title" value="Existing Post Title"/>
 <textarea name="post[content]" id="post_content">Existing Post Content</textarea>
 <input type="submit" name="commit" value="Update Post" />
</form>
{% endhighlight %}

This is something that we can reuse for a create and an edit form, and it will populate data in the form automatically if there's data there. The thing to remember here is that `form_for` needs an instance of the model to use which has to be provided from the `controller`. So for example:

{% highlight ruby %}
def new
  @post = Post.new
end
{% endhighlight %}

Of course now we need to wire these forms up with `controllers` in the backend to actually process the data, but that's a topic for another day.

You can read more about Rails forms in the [Rails Guides][rails-guides].

[oauth]: https://github.com/intridea/omniauth
[csrf]: https://en.wikipedia.org/wiki/Cross-site_request_forgery
[rails-guides]: http://guides.rubyonrails.org/form_helpers.html
