---
layout: post
title:  "Rails Sessions"
date:   2016-06-02 12:15:00
categories: ruby rails
---

When building a web application, usually we want some capacity for a user to create an account, login, and then interact with the site in a way that's tied to their identity. If you login to Amazon, it loads up your cart with the items you selected earlier. If you login to Facebook, it loads your profile and your friends. This is something we take for granted - it's something that's always there and just "works."

But HTTP is stateless. Meaning, you send a request to the server, it sends you a response, and that interaction is done. The server doesn't remember you solely based on the request. So how can we get the server to remember us so we don't have to login with every request we send?

**Cookies**

A cookie is just a string that's stored on your computer with information identifying you and your interaction with a specific site. When you make a request to the server, data is drawn from the cookie and put into the request header when it's sent. This information gives the server a way to know that the request is coming from you.

In Rails, you can use cookies via `sessions`. A `session` is a hash that you can use to store data for a particular interaction with a user. Rails serializes the data in this hash and turns it into a cookie.

Here's an example:
{% highlight ruby %}
# set user_id
session[:user_id] = @user.id

# load the user referenced in the session
@user = session[:user_id]
{% endhighlight %}

Since the cookie is just a string, a user could theoretically manipulate the string before sending it back. To prevent this, Rails cryptographically signs the cookie to ensure it's authentic and untampered with.

**Authentication**

So how would we make sure the user is logged in before serving any data? This could be a simple implementation using `session`:
{% highlight ruby %}
class DocumentsController < ApplicationController
  before_action :require_login

  def show
    @document = Document.find(params[:id])
  end

  def index
  end

  def create
    @document = Document.create(author_id: user_id)
  end

  private

  def require_login
    return head(:forbidden) unless session.include? :user_id   
  end
end
{% endhighlight %}

Authentication is usually a complex process involving securely storing passwords, checking if the user entered the correct password, and it also entails dealing with new user registration and destroying your session by logging out. For me, I prefer not having to rewrite this boilerplate functionality for every project, so usually I just use the [Devise gem][devise].

Once set up, it handles all of these issues and it's quite heavily used and tested so you can rest assured that it's secure. Once you understand the basics of how sessions and authentication work, we can outsource this functionality to a gem like Devise.

You can read more about sessions in the [Rails Guides][rails-guides].

[devise]: https://github.com/plataformatec/devise
[rails-guides]: http://guides.rubyonrails.org/security.html
