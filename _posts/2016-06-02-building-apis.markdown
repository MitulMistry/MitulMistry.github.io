---
layout: post
title:  "Building APIs"
date:   2016-06-02 16:00:00
categories: ruby rails
---

There's a lot of data out there on the Internet. We can access the data that other providers make available by consuming their `APIs`. An `API` - or application protocol interface - is a way to serve and access data. Accessing data from other sources allows you to use their data to build on their services, provide additional functionality, and use their data in new and inventive ways. This is an external `API`.

However, internal `APIs` are just as common. This makes data more accessible to our own application and associated services, something that's especially useful when using `AJAX` and `JavaScript` frameworks. Building an `API` in Rails is often a straightforward process, especially when the model is simple, since Rails has tools to make the data available in different formats.

**JSON**

Data available through `APIs`, internal or external, often comes in the form of [JSON][json], or JavaScript Object Notation. It's a standard format used for efficient readability - both for developers and parsers. To create an `API` in Rails, all we have to do is take the data from a model and serialize it - or turn it into `JSON` data. We can do that using the `.to_json` method.


{% highlight ruby %}
# posts_controller.rb
# ...
 def post_data
   post = Post.find(params[:id])
   render json: post.to_json
 end
{% endhighlight %}

Here, Rails takes the post and converts it to `JSON` to be served. We can serve different formats using `respond_to`. We can also limit the data we're serving and include associations as well.

{% highlight ruby %}
# posts_controller
# ...
 def show
   @post = Post.find(params[:id])
   respond_to do |format|
     format.html { render :show }
     format.json { render json: @post.to_json(only: [:title, :description, :id],
                             include: [author: { only: [:name]}]) }
   end
 end
{% endhighlight %}

However, as things get more complicated, we might want to find a cleaner way. We can instead use [ActiveModel Serializers][activemodel-serializers]. You include and install the gem, run `rails g serializer <model name>`, and modify the attributes in the new serializer class file. Then, when we respond with `JSON` data, it serializes the model implicitly.

So now you can just do:
{% highlight ruby %}
# posts_controller.rb
# ...
def show
   @post = Post.find(params[:id])
   respond_to do |format|
     format.html { render :show }
     format.json { render json: @post}
   end
 end
 {% endhighlight %}

Now when you build a JavaScript front end that wants to make dynamic `AJAX` requests without reloading, you can do that by accessing these serializer-enabled controller actions.

You can read more about Rails ActiveModel Serializers in the [Rails Guides][rails-guides].

[json]: https://en.wikipedia.org/wiki/JSON
[activemodel-serializers]: https://github.com/rails-api/active_model_serializers
[rails-guides]: http://guides.rubyonrails.org/active_model_basics.html#serialization
