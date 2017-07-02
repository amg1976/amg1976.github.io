---
layout: post
title:  "WWDC 2017 - what to expect"
date:   2017-06-02 21:00:00 +0000
categories: blog
tags: wwdc siri speaker xcode ipad swift refactoring
---
It's almost that time of the year where the Apple developers community has its biggest event - the WWDC.

This year as usual, the expectation is around what new features the latest version of iOS will bring us, and maybe some hints to what is the future in terms of devices.

I am not very good at guessing, and to be honest, I have been falling back in terms of many of the latest SDKs delivered by Apple. I did some experiments around the Watch and AppleTV, played a bit with the Today widget, but unfortunately never delve deep inside those libraries. And regarding Messages, Siri, Car, Home, etc - I didn't write a single line of code for them.

So, if I'm not in a good position to play the guessing game, I can however do a short list of some of the expectations from the community and maybe share my opinion on them.

### Around the Web

##### Siri Speaker
After Amazon and then Google started selling their devices, I guess it would make sense for Apple to jump into this train. According to some [statistics](http://uk.businessinsider.com/amazon-echo-vs-google-home-sales-estimates-chart-2017-5?r=US&IR=T), in the US alone there are around 35M users of voice-enabled speakers in 2017, which represents an increase of 129%. This shows that is market relatively small, but with growing interest from the public. And by having already the software and most of the hardware needed, it wouldn't be that difficult for Apple to come up with a device as good or even better than the competition.

##### New layout of the iPhones back side
Many leaked pictures of the new iPhones show the cameras in a vertical orientation and a Touch ID sensor underneath the Apple logo. About the cameras I have no idea why they would need to change and honestly, it would look a bit weird to see them in that orientation.

For the sensor, I would hate it! In my job as a developer, most of the times the phone is on top of the table and to start testing some new feature I'm working on, I don't even grab the phone - I unlock it with a simple tap on the sensor and that's it. And I don't think Apple would do that change as it severely changes how people are used to interact and unlock the phone. Of course that doesn't mean anything - previously you needed to swipe to unlock and now it's just tap the sensor and then press the home button. The only way I would agree with this change, was if somehow you could do Touch ID anywhere on the front screen, that would be awesome! And if so, sure, add the sensor to the back also 😂

##### New iPad Pro 10.5-inch
I only ever used an iPad Pro for a minute, so I have no opinion on this one. Probably it makes sense to have all the power and functionalities on a more portable form factor.

##### Dark mode
Well, this one is new to me. It probably is a natural next step from previous features, like Night Shift. And if it's true that future devices will have an OLED screen, as dark modes are more friendly on the batteries.

### What I would like to see

##### Swift refactoring tools on Xcode
Ever since Swift was released, this is one of the major flaws that Apple has yet to address. I know Xcode has now much better support for Swift: in the beginning typing `@@` would make it crash every time. And for me, coming from Portugal to an Irish keyboard layout (in PT layout, `"` are typed the same way as `@` in the IRL layout), it was sooooo easy to make it crash! And if the Live View Hierarchy inspector or the recent Memory Graph tool shows that Apple cares about Xcode, not having the simplest of the refactoring tools makes some of the common developer tasks a bit more tedious and difficult than they could or should be.

##### Real multitasking on iPad
If this is the year where we will see new developments in the iPad arena, I guess having true multitasking would make a lot of sense. Recently I saw a [prototype](https://www.youtube.com/watch?v=UyFUDQ5LLZw) by Federico Viticci for Macstories of how that could look like and I believe implementing it in a similar way would bring the platform to a whole different level. Of course, with a bit of imagination and a lot of careful planning and hard work, you can kind of make it work right now, just look at the awesome work by the team at [Readdle](https://blog.readdle.com/drag-drop-between-readdle-apps-fd079c756b23).
