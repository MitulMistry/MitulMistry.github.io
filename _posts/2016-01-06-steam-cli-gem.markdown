---
layout: post
title:  "Steam Top Sellers CLI Gem"
date:   2016-01-13 22:20:00
categories: ruby
---

In my process of learning Ruby, I've been working on a CLI gem that I'll discuss here. My goal was to create a command line interface that could retrieve a list of the currently top selling games at the [Steam Store][steam-store]. Upon retrieving the list, the user would then be able to select a game and get more detailed information.

The original intention was to organize the project through three classes: a `Scraper`, `Game`, and `CLI`. The Scraper would be responsible for scraping the data from the Steam website. The `Game` class would be used to instantiate objects for each game based on data retrieved by the `Scraper`. The `CLI` would then be used to tie the other classes together and create an interface for the user to interact with this data. In execution, most of this would go according to plan, but there would be some changes necessary.

I initially used Nokogiri for the `Scraper` class, then manually navigated through the CSS selectors on the Steam Store frontpage as well as the individual game pages. It was a tedious process, but worked well enough to acquire the necessary data. However, two major roadblocks soon arose:

1) For certain games, the Steam Store would put up an age verification page before allowing the user to continue to the game page, and when using Open-URI to access the page, it would be blocked. I looked into ways of getting around the age verification and it involved creating a cookie, but that was out of scope of what I wanted to accomplish with the project.

2) As soon as I finished the `Scraper` methods, the Steam Store changed its entire website and CSS structure for its holiday sale, and rewriting the methods temporarily only to revert to the old methods when the sale was over seemed tedious and inefficient.

So after doing some research, I was able to find a better way: leveraging the Steam Storefront API. I created a new class, `SteamAPIHandler`, to acquire the JSON data and parse it, using the data to create `Game` objects that could then interact with the `CLI`. This allowed me access to a much larger amount of data and proved to be much more efficient as well. For example, here's an old `Scraper` method:

{% highlight ruby %}
def self.scrape_top_sellers(store_url)
  return_array = [] #empty array that will contain the game hashes to return at the end of the method

  #OPEN URL
  doc = Nokogiri::HTML(open(store_url)) #uses open-uri to open the url, then uses Nokogiri to parse in the html which we will then use to scrape data

  #PARSE PAGE FOR DATA
  doc.css("div.tab_content#tab_topsellers_content div.tab_item").each do |item| #iterates over each game listing on the site, and gets name, location, and url
    return_array << { #pushes a hash of game data onto the array
      title: item.css("div.tab_item_name").text, #uses css to select the appropriate data
      price: item.css("div.discount_final_price").text,
      genres: item.css("div.tab_item_top_tags").text,
      url: item.css("a.tab_item_overlay").collect{ |link| link['href'] }.join,
      }
  end

  return_array.each do |i|
    x = i[:url].split("/") #splits the link into individual elements between the /s
    x.pop #gets rid of the ?info at the end of the link
    i[:steam_id] = x.last #sets the steam id to the end element of the link
    i[:url] = x.join("/") #joins the url and saves it without the ?info
  end

  return_array
end
{% endhighlight %}

...compared to a new `SteamAPIHandler` method that accomplishes the same thing:

{% highlight ruby %}
def self.get_top_sellers
  doc = open("http://store.steampowered.com/api/featuredcategories/?cc=US") #uses Open-URI to get the JSON file, ?cc=US for US currency
  data_hash = JSON.load(doc) #loads the JSON data
  data_hash["top_sellers"]["items"] #selects desired part of hash
end
{% endhighlight %}

In practice, the `CLI`, upon loading, uses the `SteamAPIHandler` to acquire the top sellers and create `Game` objects from the data. It prints out the information on the screen, then, when the user selects a game to get more information on, the `CLI` uses the `SteamAPIHandler` to acquire additional data and print it out. I had a method that acquired the additional information as soon as the `CLI` loaded, but it caused additional load time as it had to make ten queries to the API in succession, as opposed to one at a time where the load time is much less noticeable.

Overall, it was a good learning experience and I accomplished what I had set out to do. You can see the source code for this project [here on Github.][github-repo]

[steam-store]:      http://store.steampowered.com/
[github-repo]:      https://github.com/MitulMistry/steam-top-sellers-cli-gem