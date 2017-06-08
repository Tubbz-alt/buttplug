# Usage Examples

To concretize this otherwise theoretical discussion, here are some in-depth examples of how Buttplug implementations could be architected in the wild.


## Library Embedded in a Video Game

First off, a simple example using a single program with an embedded library.

A developer would like to ship a game on Windows, using the Unity Engine, that has some sort of interaction with sex toys. Since we want concrete examples here, let's say it's a version of Tetris that increases vibrator speeds based on how many lines have been made by the player.

Due to the nature of games, the developer would want it to have as little impact on performance as possible. They would also want the server to exist in the game executable, so that it can be shipped as a single package.

In this case, the developer could use a Buttplug library implementation, possibly the C# reference library since this is Unity. Inside the game, device connection configuration could be part of the game settings menus, allow devices to be automatically reconnected on game startup. To communicate with the embedded server during gameplay, C# message objects could be sent to a thread for handling, so that IO timing doesn't lag the game loop.

One of the important things lost by direct library integration is the ability to support new hardware. If a game is simple sending a generic "Vibrate" command, it is basically future-proofed for all toys that will support that command in the future, assuming it has a way to send that message to something that supports the new hardware. If a library is compiled into the game, there would be no way to add this hardware support though. There are multiple solutions to this issue, but those are outside the scope of this example.


## Web Based Hardware Synced Movie Player

Now, a far more difficult scenario. This example tries to build a shotgun to hit as many platforms as possible with as little code as possible.

The goal is to build a web based movie player, that will load movies with synchronization files, and play them back while controlling hardware. We will assume we are working with browsers that give us a minimum of HTML5 Video playback and WebSockets. We want our application to work on as many platforms as possible. The movie player should be capable of talking to as many devices as possible on as many platforms as possible, including desktop and mobile. The main focus for toy support will be Bluetooth LE toys, with all others considered nice to have.

At this point, we have to take operating system and browser capabilities into account.

Operating Systems that have BLE:

-   Windows 10 (Version 15063 and later)
-   macOS (10.6 or later)
-   Linux (with Bluez 5.22 or later)
-   Android (version 5 or later)
-   iOS (LE support versions unknown)
-   ChromeOS (LE support versions unknown)

Web Browsers with WebBluetooth:

-   Chrome 56 on Mac, Linux, Android, ChromeOS

This means that if we implement a Buttplug Server in Javascript using WebBluetooth to access BLE devices, we can target the Chrome web browser and support 2 major desktop platforms, 1 mobile platform, and whatever ChromeOS is. We can also ship this server implementation as part of the movie player application, meaning it will all work as a unit, similar to the game example above. Future-proofing could feasibly happen with CDN hosting of the library via semantic versioning adherence.

Unfortunately, that leaves out Windows and iOS. To maximize ROI on custom support implementation, we're more likely to see more users via Windows than iOS, so we'll concentrate on Windows first.

To talk to Bluetooth LE on Windows 10 requires access to UWP APIs, so following a "When In Rome" philosophy, we can implement a Buttplug Library in C#. On top of this we can build a server exposed via WebSockets, to let the browser application talk to the native server. A native implementation gives us the extra win of USB and Serial, at least, until WebUSB sex toys become a thing.

Going back to the web application itself, this now means the client side will need to connect to one of two different styles of servers. We can use User Agent Detection in the browser to let us know which OS we're on, and then either select the WebBluetooth path or native Windows Websocket path.

To hit iOS, we now have the option of going via a Xamarin based C# app, or a Node.js/Cordova app. There will be some custom implementation on either side, but most of the heavy lifting will have been done before this.

An aside for those wondering why this wasn't all done in Node.js. At the time of this writing, node.js bindings to UWP APIs do exist, but were still iffy at best. Not only that, distributing a native application like the Buttplug Server would've required wrapping in something like nw.js, massively inflating distributable size. Implementing a C# version of the Buttplug Library also gives us a platform into Unity integration.
