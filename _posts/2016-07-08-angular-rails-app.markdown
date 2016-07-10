---
layout: post
title:  "Angular Rails App"
date:   2016-07-08 19:30:00
categories: javascript rails
---

Rails templates are powerful and give us an easy way to build a front end that integrates with our back end. However, Rails templates are also limited. They are largely static, meaning they are rendered by the controller before being sent as a response to the user. But that means a hard page reload in your browser, and that makes it more difficult to make applications that operate seamlessly like many modern websites such as Facebook or Twitter.

As seen in one of my [past projects][jquery-project], we can use `jQuery` to build a single-page application that makes requests asynchronously with `AJAX`. However, it quickly gets complicated and hard to maintain, organize, and develop. There are many JavaScript front end frameworks that have risen over the last several years. For this project, I used [Angular][angular] to build an application for uploading and sharing sketches.

**Integrating Angular With Rails**

Angular and Rails aren't really designed to work together, so building an application integrating both requires some finagling. In order to get the functionality we want, we need to manage both front end and back end dependencies. Back end dependencies are easy to manage: we just use gems in our gemfile and install with `bundle install`. However, front end dependencies are a bit trickier. There are many Angular related gems we could use, but there aren't gems for everything. So instead, we can use [Bower][bower].

First, we install [Bower][bower] by running `npm install -g bower`. Then we isntall [bower-rails gem][bower-rails] through our gemfile. We specify our front end dependencies in a bower.json file. After that, we run `rake bower:install`, and our dependencies will be installed (the default directory being `vendor/assets/bower_components`). We then have to specify the dependencies in the javascript manifest file (`app/assets/javascripts/application.js`) to incorporate them in the asset pipeline.

Another gem we can use is [angular-rails-templates][angular-rails-templates] to incorporate our Angular templates into the Rails asset pipeline.

You can take a look at my Bower dependencies in my [bower.json file here][gh-bower-json] as well as the [app.js file][gh-app-js] explaining them.

**Rails Back End**

The back end is set up similarly to my [last jQuery project][jquery-project], so I won't go into it in depth. There are several models including sketches and tags. The sketch model uses [Paperclip][paperclip] to manage file attachments. It's easy to set up file uploads for Paperclip with a Rails form, but with Angular we will have to set up a different solution - namely [ngFileUpload][ngFileUpload].

We'll enable `AJAX` functionality by setting up [Active Model Serializers][active-model-serializers] for `JSON` serialization of our models through our controller actions.

Authentication is handled with [Devise][devise]. Like Paperclip, it's easy to sign in and register with Devise Rails templates, however, we'll again have to set up something different with Angular, using [angular-devise][angular-devise].

**Angular Front End**

Going over all of the front end code would be an exhaustive process, so I'll just give a brief overview.

Since this is a single page application, we have to handle routing for pages on the front end, and for this I used [ui-router][ui-router]. Ui-router states are created in the application configuration, and I did so in the [app.js file][gh-app-js].

I tried to have some of the organization of the Angular part of the project mimic Rails and its RESTful conventions - such as having a sketches index page, a sketch show page, and reusing the sketch form for creation and updating. Each page has a controller associated with it to handle the unique functionality requirements.

In addition, I have a couple Angular [components][components] that act as partials used in [repeaters][repeaters]. There's an artist component displaying an artist's name, information, and link to artist show page, as well as a similar component for sketches.

I put all the logic for actually communicating to the back end in various Angular services - such as [SketchesService][sketches-service] for creating and retrieving sketches, and [TagsService][tags-service] for creating and retrieving tags. The services use [$http][$http] to make GET, POST, PUT, and DELETE requests to interact with the back end models via `AJAX`.

Overall, I found this to be a challenging project, not just due to the complexity of Angular itself, but also due to all the work required to get Angular and Rails to work effectively together. 

You can see the source code for the project [here on Github][github-repo].

[jquery-project]: http://mitulmistry.github.io/javascript/rails/jquery-rails-app/
[angular]: https://angularjs.org/
[bower]: https://bower.io/
[bower-rails]: https://github.com/rharriso/bower-rails
[angular-rails-templates]: https://github.com/pitr/angular-rails-templates
[gh-bower-json]: https://github.com/MitulMistry/sketchbook/blob/master/bower.json
[gh-app-js]: https://github.com/MitulMistry/sketchbook/blob/master/app/assets/javascripts/angular-app/app.js
[paperclip]: https://github.com/thoughtbot/paperclip
[ngFileUpload]: https://github.com/danialfarid/ng-file-upload
[active-model-serializers]: https://github.com/rails-api/active_model_serializers
[devise]: https://github.com/plataformatec/devise
[angular-devise]: https://github.com/cloudspace/angular_devise
[ui-router]: https://github.com/angular-ui/ui-router
[components]: https://docs.angularjs.org/guide/component
[repeaters]: https://docs.angularjs.org/api/ng/directive/ngRepeat
[sketches-service]: https://github.com/MitulMistry/sketchbook/blob/master/app/assets/javascripts/angular-app/services/SketchesService.js
[tags-service]: https://github.com/MitulMistry/sketchbook/blob/master/app/assets/javascripts/angular-app/services/TagsService.js
[$http]: https://docs.angularjs.org/api/ng/service/$http
[github-repo]: https://github.com/MitulMistry/sketchbook
