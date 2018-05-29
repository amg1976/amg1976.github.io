---
layout: post
title: Refactoring with the Builder pattern
categories: blog
tags: [xcode, refactoring, patterns, builder]
---

There have been some posts shared recently in the community about common software patterns and how they might help you writing code that is better structured and easier to read and maintain. One such pattern is the Builder Pattern. Simply put, applying this pattern allows for easier creation of complex objects, by hiding those complexities from the calling site.

In one area of our app we had the following code, responsible for creating two rather similar instances of `UIButton`:
```
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
```


FINAL RESULT:
```
func bottomLeftButtons() -> [UIButton]? {

    var result = [
        ComposerButtonType.library.buildButton(withTarget: self, action: #selector(libraryButtonPressed(sender:)), for: .touchUpInside),
        ComposerButtonType.camera.buildButton(withTarget: self, action: #selector(cameraButtonPressed(sender:)), for: .touchUpInside)
    ]

    return result

}
```
