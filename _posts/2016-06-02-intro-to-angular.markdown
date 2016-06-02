---
layout: post
title:  "Intro to Angular"
date:   2016-06-02 14:30:00
categories: javascript angular
---

Using a combination of Rails templates and `jQuery`, we can create a decent interactive front end for our applications. But this process can quickly become ugly and limited, devolving into a mess of `ERB` tags and `JavaScript` functions. This is one of the reasons for the evolution of front end frameworks, one of them being the Google backed [Angular JS][angular].

Angular exists to help developers build single-page applications. It provides structure and tools to help solve common problems without having to reinvent the wheel with every application.

Without `JavaScript` and JS frameworks, everything is rendered on the server. For example, through the `MVC` paradigm of Rails, the controller renders a view and sends it back as a response. But if you want to make make small changes and see different results - such as adjusting the price range while shopping for a product - you would have to initiate a page reload with every request.

With Angular, not only does it handle routing so you can load different pages without a page reload, it allows you to build dynamic views that react to your input spontaneously.

**MVVM**

While Rails utilizes the `MVC` paradigm - model, view controller - Angular utilizes `MVVM` - model, view, viewmodel. The controller is replaced with the viewmodel, which is responsible for all view logic.

**Data Binding**

In order to create views that are dynamic and respond to user input, the concept of data binding is employed. This means that data from one source - such as an input field - is bound to something else - like a text box - so it displays your text spontaneously, something that couldn't really be done in Rails without `JavaScript`.

In addition, two way data binding is used to keep data from a view synchronized with the data in a model. You can read more about data binding [here on the Angular docs][data-binding].

Angular is an extensive framework with a lot of moving parts. You can delve deeper by taking a look at the [Angular documentation][angular-docs].


[angular]: https://angularjs.org/
[data-binding]: https://docs.angularjs.org/guide/databinding
[angular-docs]: https://docs.angularjs.org/guide/introduction
