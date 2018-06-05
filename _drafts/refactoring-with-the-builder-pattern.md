---
layout: post
title: Refactoring with the Builder pattern
categories: blog
tags: [xcode, refactoring, patterns, builder]
---

There have been some posts shared recently in the community about common software patterns and how they might help you writing code that is better structured and easier to read and maintain. One such pattern is the Builder Pattern. Simply put, applying this pattern allows for easier creation of complex objects, by hiding those complex details from the calling site.

### The not so nice approach
In one area of our app we had the following code, responsible for creating two rather similar instances of `UIButton`:
{% highlight swift %}
func bottomLeftButtons() -> (firstButton: UIButton?, secondButton: UIButton?)? {

    let firstButton = UIButton(type: .custom)
    let firstImage = R.image.icoPhotoLibrary()
    firstButton.setImage(firstImage, for: .normal)
    firstButton.contentMode = .center

    firstButton.addTarget(self, action: #selector(libraryButtonPressed(sender:)), for: .touchUpInside)

    let secondButton = UIButton(type: .custom)
    let secondImage = R.image.icoCamera()
    secondButton.setImage(secondImage, for: .normal)
    secondButton.contentMode = .center

    secondButton.addTarget(self, action: #selector(cameraButtonPressed(sender:)), for: .touchUpInside)

    return (firstButton, secondButton)
}
{% endhighlight %}

In here, we are executing a few different instructions, needed to setup the button according to the specific needs:
1. create an instance with type `custom`
2. setting an image for the `normal` state
3. defining how the button content is displayed, with `contentMode`
4. adding an action for when the user taps the button

These are just some of the properties that you can set to configure a button, many others could be needed. And in this case, we are dealing with a standard `UIKit` element, probably one of the first controls that you play with when you start developing for iOS, so most of these properties should be known by heart even for the most inexperienced programmer.

### A better approach
That doesn't mean we shouldn't spend some time trying to come up with a way to make it easier to use, or make it more reusable. Because it is (always) a good practice to design your APIs having in mind that someone else might need to read / consume them even if you are the only developer in the project, we are going to extract the responsibility of creating a custom tailored instance of the button into a specific method, using the Builder pattern.

So, we can create an extension of `UIButton` and add a couple of static methods:

{% highlight swift %}
extension UIButton {

    static func buildLibrayButton(withTarget target: Any?, action: Selector, for events: UIControlEvents) -> UIButton {

        let button = UIButton(type: .custom)
        let image = R.image.icoPhotoLibrary()
        button.setImage(firstImage, for: .normal)
        button.contentMode = .center

        button.addTarget(target, action: action, for: events)

        return button

    }

    static func buildCameraButton(withTarget target: Any?, action: Selector, for events: UIControlEvents) -> UIButton {

        let button = UIButton(type: .custom)
        let image = R.image.icoCamera()
        button.setImage(firstImage, for: .normal)
        button.contentMode = .center

        button.addTarget(target, action: action, for: events)

        return button

    }

}
{% endhighlight %}

We only left as parameters what depend on the context where the methods are called, in this case how the button behaves when tapped. This already looks better, we have specific methods that are clear on their intent, making easier to create instances of the desired type of buttons whenever they are needed that will always look the same.

### Ah, I like this!
But we can do better, we can use the power of `enums` to explicitly declare the different type of buttons:

{% highlight swift %}
enum ComposerButtonType {
    case camera
    case library
}
{% endhighlight %}

And then, we can extend the declaration of the enum, by adding the builder method:

{% highlight swift %}
extension ComposerButtonType {

    func buildButton(withTarget target: Any?, action: Selector, for controlEvents: UIControlEvents) -> UIButton {

        let button = UIButton(type: .custom)
        button.addTarget(target, action: action, for: controlEvents)
        button.contentMode = .center

        let image: UIImage?
        switch self {
        case .camera:
            image = R.image.icoCamera()
        case .library:
            image = R.image.icoPhotoLibrary()

        }

        button.setImage(image, for: .normal)
        return button
    }

}
{% endhighlight %}

So now, if we want to create a camera button, we just need to:
{% highlight swift %}
ComposerButtonType.camera.buildButton(withTarget: self,
                                      action: #selector(cameraButtonPressed(sender:)),
                                      for: .touchUpInside)
{% endhighlight %}

### Builders everywhere
This is a great pattern and we find ourselves using frequently, where another good use case is to configure a view controller after creating it from the storyboard as there are no methods to inject its dependencies:

{% highlight swift %}
// BEFORE

override func start() {
    guard let carouselViewController = R.storyboard.main.ticketCarousel() else { return }
    carouselViewController.delegate = delegate
    carouselViewController.ticketStore = ticketStore
    controller?.pushViewController(carouselViewController, animated: false)
}

// AFTER

override func start() {
    let carouselViewController = CarouselViewController(withDelegate: self, ticketStore: ticketStore)
    controller?.pushViewController(carouselViewController, animated: false)
}
{% endhighlight %}

This way it is clear what is needed to properly setup the view controller, making our code less error prone and more reliable.

_(tested in Xcode 9.3 and Swift 4.1)_

Thanks for reading!
