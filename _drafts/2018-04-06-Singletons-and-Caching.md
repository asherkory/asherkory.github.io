---
layout: post
title: Singletons and Caching: Diving Into an Error
tags: ruby, rails
---

I was debugging a Rails API and came across a new-to-me error that seemed, on the surface, a bit obscure. 

Here's the error:
```TypeError (singleton can't be dumped)```

I got this error when trying to POST to the API to create a new resource of a certain type. I traced it to a controller filter, designed to make sure the requesting user was operating in the correct "realm". The filter makes a call to a separate API dealing with realms. It uses our new SDK gem to make this call and instantiates a new realm object, if successful, from the response. Then the filter tries to cache the realm object. 

Previously, with our old SDK, this worked. So what was different?

Turns out the new SDK uses some fancy metaprogramming to dynamically instantiate new objects, and these objects all have singleton classes defined on them. 

This is a great guide on singleton classes in Ruby: [Understanding Ruby Singleton Classes](https://www.devalot.com/articles/2008/09/ruby-singleton)

The key to my error is here:
>"Finally, a word of caution. Once you’ve created a singleton class for an object you can no longer use Marshal.dump on that object. The marshal library doesn’t support objects with singleton classes."

So what is the Marshal library? Marshalling is a serialization process for converting a Ruby object into a byte stream. `Marshal.dump` serializes an object, and `Marshal.load` reconstitutes it: [Marshal library documentation](http://ruby-doc.org/core-2.5.0/Marshal.html) As you can imagine, this is very useful for caching.

And that is why I was seeing an error. The Rails cache, when attempting to store the realm object, called `Marshal.dump` -- which failed because of its singleton class.

(If you pass `raw: true` to a MemCacheStore, it will store the value as a string and bypass Marshalling, but that's not the solution here - instead I needed to just cache something else)
https://apidock.com/rails/ActiveSupport/Cache/Store/fetch