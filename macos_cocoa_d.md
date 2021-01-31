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

### Xcode

Xcode is the IDE provided by Apple and ships with a compiler, SDK and all other
tool that are necessary to create applications for all Apple's devices.

### Storyboards

Xcode comes with a built-in feature that allows the developer to graphically
create the GUI of an application. This is called Interface Builder. The GUI is
stored in files separate from the application, basically a serialized form of
the GUI. These files are called `.nib` files and the data is stored in a binary
format. Later support was added for `.xib` files, which is the same idea but the
data stored as XML instead of binary. This is more friendly for source control.
The last incarnation of these files are storyboards. Where `.nib` and `.xib`
files contain the GUI for only a single view, storyboards can contain the GUI
for multiple views. For example, a window and the dialogs that can be opened
from that window.

The advantage of storying the GUI files separately from the application is that
if they are changed, the application does not need to be rebuilt. They also
allow to lazily load the GUI. For example, if the preferences view is stored in
a separate file and the user never opens the preferences view, then it never
needs to be loaded.

### The Cocoa Framework

As part of the SDK is the Cocoa framework. This is the framework used to create
native macOS applications with a GUI. The Cocoa framework is, what's called, an
umbrella framework. That means that it doesn't contain much code of its own but
instead depends on other frameworks for that. Cocoa consists of three frameworks:
Foundation, AppKit and CoreData.

#### Foundation Framework

The Foundation framework contains building blocks for writing Objective-C code,
functionality for dealing with strings, arrays, sockets, files, paths and so on.
It's the closets thing Objective-C has to a standard library.

#### AppKit Framework

Next is the AppKit framework. This is the framework that provides the actual GUI
components, like windows, buttons, text areas, scrollbars and so on.

#### CoreData Framework

Finally there's CoreData, which deals with object persistance and related
functionality. We will not use CoreData for this application.

### header

One problem with these frameworks, and many other of Apple's frameworks, is that
they're implemented in Objective-C. For most languages, to interface with
Objective-C, requires to use the Objective-C runtime. The Objective-C runtime
provides a C API to create classes, instantiate classes, call methods and so on.
It's quite tedious, verbose and difficult to use this API correctly. Except for
Swift, which has been created by Apple to replace Objective-C, D is the only
language which has native language support for interfacing with Objective-C.

In the latest release of D ([DMD 2.095.0](https://dlang.org/changelog/2.095.0.html)),
support for interfacing with Objective-C protocols has been added. This is the
final piece (more or less) to allow to fully interface with Objective-C code
without having to manually call the Objective-C runtime.

## First Implementation

In the first implementation of the application, Xcode will drive the build
process this will be the most idiomatic way for building applications for macOS.

The first step is to create a new project in Xcode. At the Xcode launch screen,
click `Create a new Xcode project`:

![Xcode launch screen](method_1/light/xcode_launch_screen.png).

Then choose the `macOS` platform and the `App` application:

![Xcode project template](method_1/light/xcode_template.png).

For the project options, enter a `Product Name`. Select `Storyboard` in the
`Interface` dropdown. Select `Objective-C` in the `Language` dropdown and finally
click next:

![Xcode project options](method_1/light/xcode_project_options.png).

After that, Xcode will show its main window with the project. Click the play
button in the toolbar to make sure that the project compiles and runs:

![Xcode main window](method_1/light/xcode_main_window.png).

The application should launch within a couple of seconds and show its main
window:

![First window](method_1/light/first_window.png).

