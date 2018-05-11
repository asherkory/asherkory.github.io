---
layout: post
title: "Singletons and Caching: Diving Into an Error"
tags: ruby, rails
---

I was debugging a Rails API and came across a new-to-me error that seemed, on the surface, a bit obscure. 

Here's the error:
```TypeError (singleton can't be dumped)```

I got this error when trying to POST to the API to create a new resource of a certain type. I traced the error to a controller filter, designed to make sure the requesting user was operating in the correct "realm". The filter makes a call to a separate API dealing with realms. It uses a new in-house SDK gem to make this call, and if successful and instantiates a new realm object from the response. Then the filter caches the realm object. 

Previously, with our old SDK, this worked. So what was different?

Turns out the new SDK uses some fancy metaprogramming to dynamically instantiate new realm objects, and these objects all have singleton classes defined on them. 

This is a great guide to singleton classes in Ruby: [Understanding Ruby Singleton Classes](https://www.devalot.com/articles/2008/09/ruby-singleton)

The key to my error is here:
>"Finally, a word of caution. Once you’ve created a singleton class for an object you can no longer use Marshal.dump on that object. The marshal library doesn’t support objects with singleton classes."

So what is the Marshal library? Marshalling is a serialization process for converting a Ruby object into a byte stream. `Marshal.dump` serializes an object, and `Marshal.load` reconstitutes it: [Marshal library documentation](http://ruby-doc.org/core-2.5.0/Marshal.html) As you can imagine, this is very useful for caching.

And that is why I was seeing an error. The Rails cache, when attempting to store the realm object, called `Marshal.dump` -- which failed because of its singleton class. The ultimate solution involved caching the unique identifier for the realm, instead of the entire realm object.