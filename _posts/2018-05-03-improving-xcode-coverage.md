---
layout: post
title:  "Improving Xcode code coverage report"
categories: blog
tags: [xcode, code coverage, unit tests, app delegate]
---
### Improving Xcode code coverage report
When writing unit tests, we should try to exercise all the scenarios and edge cases of the piece of code we are working on. I believe we should try to write them based only on the public contract of the methods and classes and what we know about how that code should work.

Once you have those tests, you can then look at the actual code being tested and look for special or edge cases that might not being exercised by the initial set of tests. There is one tool in Xcode that can help us detecting those areas that need further testing - Code Coverage reports.

##### Enabling Code Coverage
Enabling Code Coverage in Xcode is very simple:

```Edit scheme > Test > Options > Gather coverage```

We can choose to gather coverage for all targets or just select individual targets, depending on the project structure. Once enabled, every time we run the unit tests, we will get a nice report of all the classes and methods in the selected targets, and what percentage of that code is covered by unit tests.

##### What do we want to fix

However, there is one issue with this report: depending on the type of project and how it is setup, it is possible to have wrong code coverage reports. For Framework projects this shouldn't be an issue, but for an App project where usually we import the App module as testable into the test cases, like this:

```@testable import MyApp```

when we run the tests, any class or method that is executed when launching the app will also show up in the Code Coverage reports, even if there is no explicit test case that executed that code.

##### Replacing the AppDelegate

As pointed [here](http://cleanswifter.com/broken-code-coverage-xcode-fix/), one possible solution to this issue is replacing the App Delegate with a special version just for unit tests, which will do... nothing! Instead of instantiating the original App Delegate, which should be responsible amongst other things for preparing and showing the window and initial view controller, we will instruct the runtime to execute a special version of the App Delegate.

To do so, we will need to:

- create the new version of the App Delegate:

{% highlight swift %}
//
// TestingAppDelegate.swift
//

import UIKit

class TestingAppDelegate: UIResponder { }
{% endhighlight %}

- then we will need to create another file, which will have the code needed to either execute the default App Delegate or the unit tests specific version:

{% highlight swift %}
//
// main.swift
//

import Foundation
import UIKit

let isRunningTests = NSClassFromString("XCTestCase") != nil
let appDelegateClass: AnyClass? = isRunningTests ?
    NSClassFromString("MyAppTests.TestingAppDelegate") :
    NSClassFromString("MyApp.AppDelegate")
guard let finalDelegateClass = appDelegateClass else { exit(1) }

UIApplicationMain(
    CommandLine.argc,
    UnsafeMutableRawPointer(CommandLine.unsafeArgv)
        .bindMemory(
            to: UnsafeMutablePointer<Int8>.self,
            capacity: Int(CommandLine.argc)),
    nil,
    NSStringFromClass(finalDelegateClass)
)
{% endhighlight %}

Notice that, because the App target shouldn't contain the `TestingAppDelegate` nor the unit tests target should contain the `AppDelegate`, we need to get the actual class executing `NSClassFromString(...)`.

- now it should be possible to run the unit tests and have correct coverage reports.

Thanks for reading!
