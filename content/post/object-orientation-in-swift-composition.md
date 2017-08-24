+++
date = "2014-06-13T12:00:00"
draft = "false"
title = "Object-orientation in Swift - Composition"
slug = "object-orientation-in-swift-composition"

+++

## tl;dr:
Quick example using classes and protocols in Swift.

[**Source on github**](https://github.com/olsio/swiftly)

## Disclaimer
I am a Java developer and not native to Objective-C.

I didn’t even spend a whole day learning Swift so everything stated here is probably wrong and not how Apple intended Swift to be used.

But it is how I intend to use it.

## Composition over inheritance
Learning design patterns mostly boils down to accepting that inheritance is pretty useless and that almost all patterns are based on the mantra [prefer composition over inheritance](http://en.wikipedia.org/wiki/Composition_over_inheritance).

Done right this offers better:

- testing
- code reuse
- separation of concerns

So don’t be afraid to create many files, classes and interfaces.

## Swift
Checking out Swift you can find all the ingredients required to build a nicely composed object (even more as we have extensions). But for now we need [classes](https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/ClassesAndStructures.html) and [protocols](https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/Protocols.html) (basically interfaces in Apple talk).

To check out the other cool stuff like [optionals](https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/OptionalChaining.html), [closures](https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/Closures.html) or [extensions](https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/Extensions.html) just check out the  [Swift language guide](https://developer.apple.com/library/prerelease/ios/documentation/swift/conceptual/swift_programming_language/index.html).

## Flying Dog
![Flying dog](/images/2014/06/5173604051_e725834d11_z.jpg)

[![Creative Commons Attribution-Noncommercial-Share Alike 2.0 Generic License](/images/2014/06/80x15.png)](http://creativecommons.org/licenses/by-nc-sa/2.0/) by [Chris Smith/Out of Chicago](http://www.flickr.com/people/cjsmithphotography/)

To draw up a quick example let’s create some animals and give them abilities. Nicely re-usable abilities obviously, who wants to work more than he has to?

I chose the following animals:

- Dog
- Bird
- FlyingDog

Knowing dogs, birds and flying dogs, we require some barking, chirping, walking and flying. This is where the magic of composition comes into play as barking and flying is something that can be shared between animals but only has to be implemented onces.

- Dogs & FlyingDogs: **Bark**
- Birds & FlyingDogs: **Fly**

To make those abilities interchangeable and re-usable we need to create two protocols that define the abstract behaviour of making a sound and moving.

<script src="https://gist.github.com/olsio/20f9728ba7e0dbb874cc.js"></script>

Those protocols are very simple and only define a method each returning a String.

Using that we can now look into some concrete implementations of those protocols.

<script src="https://gist.github.com/olsio/ee0adb01162647baa7c0.js"></script>

<script src="https://gist.github.com/olsio/23f229727a365cefb3f7.js"></script>

Having the abilities covered we can create our animals. As each animal will be able to make a sound and move (plus being lazy) we start by creating an “abstract” animal that uses our protocols to do the actual sounding and moving. This way all “concrete” animals don’t have to do that work themselves.

<script src="https://gist.github.com/olsio/d0f0deb9724c11c79de8.js"></script>

As you can see the animal class can become any animal it likes to be by just switching abilities. Implementing a dog, bird and flying dog now becomes trivial.

<script src="https://gist.github.com/olsio/d507fd3b5bfac4fdba1f.js"></script>

By simply inheriting from animal we just have to override the default initialiser (constructor) of each animal and plug in the correct behaviours and pass it on to initialiser of our parent animal class.

Last thing to (well normally should be first thing) is to write some unit tests to actually verify that what we just did actually works.

<script src="https://gist.github.com/olsio/ba10ad9a002fa410387e.js"></script>

We see that the dog barks, the bird flies and the flying dog barks and flies.