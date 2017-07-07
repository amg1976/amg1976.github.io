---
layout: post
title: Xcode and the case of the missing protocol methods
categories: blog
tags: xcode stack trace nil any mock protocols subclassing
date: 2017-07-03 22:30:00 +0000
---
A few days ago I run into an odd issue, where I was trying to mock UserDefaults, in order to add some unit tests to a class in our app. I went with this approach as at the time it looked like the easiest approach, so I could add the unit tests and at the same time I didn't need to immediately change the implementation of the class, as it depends directly on the UserDefaults class. Sure, maybe the best option probably would have been defining a protocol with the methods we use in that class, and then the changes on the class would be minimal, but the fact was that it looked like the quick and easiest option to go with at the time.

So, the methods that our class uses from UserDefaults are the following:
{% highlight swift %}
func set(_ value: Any?, forKey defaultName: String)
func set(_ value: Bool, forKey defaultName: String)
func removeObject(forKey defaultName: String)
func object(forKey defaultName: String) -> Any?
func bool(forKey defaultName: String) -> Bool
func synchronize() -> Bool
{% endhighlight %}

And so I proceeded and added mock implementations for all of them, using only in-memory internal storage. Then, just to make sure my mock class worked as expected, I added some unit tests for it. And this was where I found a strange behavior. One of the unit tests tried to store nil for a specific key. My first approach was to simply set a nil parameter at the call site, like:

{% highlight swift %}
func testSettingNilValueRemovesKey() {

  let testString = "BMO"

  if let data = testString.data(using: String.Encoding.utf8) {
    storage.set(data, forKey: key)
    expect(self.storage.object(forKey: self.key)).notTo(beNil())

    storage.set(nil, forKey: key)
    expect(self.storage.object(forKey: self.key)).to(beNil())
  } else {
    assertionFailure()
  }
}
{% endhighlight%}

But the second expectation on the test was failing! That was weird, as the code of the mock method was fairly simple, just testing the input parameter and if it was, I just removed the key from internal storage.

{% highlight swift %}
func set(_ value: Any?, forKey defaultName: String) {
  guard let value = value else {
    internalStorage.removeValue(forKey: defaultName)
    return
  }
  internalStorage[defaultName] = value
}
{% endhighlight%}


That wasn't making sense and so, instead of calling the method with a nil parameter, I tried  creating a local variable of type Any? and then calling the method with that variable. This time, the test passed. After banging my head against the wall for some time, and with help from @nsomar, we found what was actually happening. We noticed that the stack trace was different between both ways of calling the method - when using a local variable the stack trace showed only one call between invoking the method in the unit test and the method on the mock object but when setting nil as a parameter, we were seeing two method calls between them. Before actually invoking my overridden method on the mock class, another method of the class was being called, namely ```set(url:forKey:)```, that I did not override on my mock class.

Why was the call stack being different? What's the difference between both ways of calling the method ```set(data:forKey)```? Using the local variable is almost auto explanatory, we were explicitly creating a variable of type ```Any?``` and so the compiler new exactly what method to call.

But why not with ```nil```? I can't find a proper response, other than what we assumed. That ```Any``` is just a keyword, a construct used by the compiler to delay having to deal with whatever is assigned to it. It would be great to find a proper answer to what is happening here, but for now, the key takeaways of this episode:
- pay more attention to the stack trace, specially the intermediate calls from core APIs, as they can be very helpful in understanding what is happening
- with Swift, almost never is a best approach to subclass, there are other ways to achieve the goal that are safer and more clean to your codebase
- the Swift compiler is really smart and it does some amazing things, but sometimes we can and should help him by being more clear and explicit on the types we want to use

Thanks for reading!

_Also, achievement unlocked, this post was written 35.000 ft above sea level, on my way to some deserved holidays in Portugal!_
