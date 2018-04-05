
got a TypeError when trying to cache an object with singleton methods defined on it, since Marshal.dump (which serializes an object into a string often for cache storage) can't be called on objects with singleton classes
see more on singletons: https://www.devalot.com/articles/2008/09/ruby-singleton


The error:
```TypeError (singleton can't be dumped)```

I traced it to a controller filter where we were making a call to a separate API using our SDK gem, and then trying to cache the response. Previously, with our old SDK, this worked. So what had changed?

Turns out the new SDK uses fancier metaprogramming to dynamically instantiate new objects, and these objects all have singleton classes defined on them. 

This is a great guide on singleton classes in Ruby: https://www.devalot.com/articles/2008/09/ruby-singleton

The key to my error is here:
>"Finally, a word of caution. Once you’ve created a singleton class for an object you can no longer use Marshal.dump on that object. The marshal library doesn’t support objects with singleton classes."

So what is the Marshal library? Marshalling is a serialization process for converting a Ruby object into a byte stream. `Marshal.dump` serializes an object, and `Marshal.load` reconstitutes it. http://ruby-doc.org/core-2.5.0/Marshal.html As you can imagine, this is very useful for caching.

And there's my error! The Rails cache called `Marshal.dump` on my SDK object, which failed because of its singleton class.

(If you pass `raw: true` to a MemCacheStore, it will store the value as a string and bypass Marshalling, but that's not the solution here - instead I needed to just cache something else)
https://apidock.com/rails/ActiveSupport/Cache/Store/fetch