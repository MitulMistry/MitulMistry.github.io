---
layout: post
title:  "Angular Rails App"
date:   2016-07-08 16:00:00
categories: javascript rails
---

`Rails` templates are powerful and give us an easy way to build a front end that integrates with our back end. However, `Rails` templates are also limited. They are largely static, meaning they are rendered by the controller before being sent as a response to the user. But that means a hard page reload in your browser, and that makes it more difficult to make applications that operate seamlessly like many modern websites such as Facebook or Twitter.

As seen in one of my [past projects][jquery-project], we can use `jQuery` to build a single-page application that makes requests asynchronously with `AJAX`. However, it quickly gets complicated and hard to maintain, organize, and develop. There are many JavaScript front end frameworks that have risen over the last several years. For this project, I used [Angular][angular] to build an application for uploading and sharing sketches.

**Integrating Angular With Rails**

`Angular` and `Rails` aren't really designed to work together, so building an application integrating both together requires some finagling. In order to get the functionality we want, we need to manage both front end and back end dependencies. Back end dependencies are easy to manage: we just use gems in our gemfile and install with Bundler. However, front end dependencies are a bit trickier. There are many front end, Angular related gems we could use, but there aren't gems for everything. So instead, we can use [Bower][bower].

First, we install [Bower][bower] by running `npm install -g bower`. Then we isntall [bower-rails gem][bower-rails] through our gemfile. We specify our front end dependencies in a bower.json file. After that, we run `rake bower:install`, and our dependencies will be installed (the default directory being vendor/assets/bower_components). We then have to specify the dependencies in the javascript manifest file (app/assets/javascripts/application.js) to incorporate them in the asset pipeline.

Another gem we can use is [angular-rails-templates][angular-rails-templates] to incorporate our `Angular` templates into the `Rails` asset pipeline.

You can take a look at my Bower dependencies in my [bower.json file here][gh-bower-json] as well as the [app.js file][gh-app-js] explaining them.

**Rails Back End**

The back end is set up similarly to my [last jQuery project][jquery-project], so I won't go into it in depth. 

-Models, controllers - no real views since that will be handled by Angular
-Devise - use Angular Devise dependency through Bower

**Angular Front End**


You can see the source code for the project [here on Github][github-repo].

[jquery-project]: http://mitulmistry.github.io/javascript/rails/jquery-rails-app/
[angular]: https://angularjs.org/
[bower]: https://bower.io/
[bower-rails]: https://github.com/rharriso/bower-rails
[angular-rails-templates]: https://github.com/pitr/angular-rails-templates
[gh-bower-json]: https://github.com/MitulMistry/sketchbook/blob/master/bower.json
[gh-app-js]: https://github.com/MitulMistry/sketchbook/blob/master/app/assets/javascripts/angular-app/app.js
[github-repo]: https://github.com/MitulMistry/sketchbook
