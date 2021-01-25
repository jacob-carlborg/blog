# Create a macOS Cocoa Application in Pure D

In this blog post I'm going to show how create a single macOS Cocoa application
in Pure D. The application will show a window with a text area, an input field
and a button. The button will download the HTML code for the web site and show
it in the text area.

The application will be implemented in four different ways. Code wise they will
all mostly be the same, except the last one which will be a bit differently.
The first three will differ mostly in the build process. The four ways the
application will be implemented in are:

* All code written in D with Xcode driving the build process. This will be
    the most idiomatic way when building application for macOS and will be the
    closest how you would do this in Objective-C. The graphical user interface
    (GUI) will be implemented using storyboards.

* The same as the previous way but Dub will be driving the build process. Xcode
    will only be used to create the storyboard. This is will be more like a
    native D project.

* The third way will be similar to the second way. But in this approach we'll
    try to make the project even more idiomatic for a D project.

* The fourth way will build on the third way but it will use pure code to create
    the GUI, instead of a storyboard.


## Background

Xcode is the IDE provided by Apple and ships with a compiler, SDK and all other
tool that are necessary to create applications for all Apple's devices.

As part of the SDK is the Cocoa framework. This is the framework used to create
native macOS applications with a GUI. The Cocoa framework is, what's called, an
umbrella framework. That means that it doesn't contain much code of its own but
instead depends on other frameworks for that. Cocoa consists of three frameworks:
Foundation, AppKit and CoreData. Foundation contains building blocks for writing
Objective-C code, functionality for dealing with strings, arrays, sockets, files,
paths and so on. It's the closets thing Objective-C has to a standard library.
Next is the AppKit framework. This is the framework that provides the actual GUI
components, like windows, buttons, text areas, scrollbars and so on. Finally
there's CoreData, which deals with object persistance and related functionality.
We will not use CoreData for this application.

Storyboards
