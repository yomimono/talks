# Library Operating Systems: from Localhost to Remote Guest

Let's democratize systems programming!

Mindy Preston
team: https://robur.io
me: https://somerandomidiot.com
also me: https://wandering.shop/@yomimono

## Where To?

* libraries and executables
* operating systems: why do we need them, anyway?
* building better futures
* positive side effects of radical change

## Libraries and Executables

* libraries: components for re-use in multiple software things
  - e.g. scipy, ncurses, java stl, ...
* executables: purpose-built runnable things that do something specific
  - e.g. bc, doom, atom, word, firefox, ...

## Libraries and Executables

* common programming advice: put as much logic in your libraries as you can
  - it's better to have more reusable bits than non-reusable bits
  - if you do this, your executables might largely be interfaces to libraries

## Libraries and Executables

* libraries + executable code + build system = application
* but that's not all you need...

## Operating Systems

```
+-----------------------------------------+
| Application                             |
+-----------------------------------------+
| OS: scheduler, memory management,       |
| network, filesystems, clock, entropy,   |
| keyboard, video, power, user management,|
| mouse, screensaver, consoles, printers, |
| ...                                     |
+-----------------------------------------+
```

## Operating Systems

* at runtime, provide (or don't) things executables ask for

## Operating Systems

* Is an operating system an executable?
  - it definitely executes
  - it might do any one of the things it knows how to do, depending on the environment

## Operating Systems

* But maybe it's also a library?
  - often with a *really* bad API (sockets, ioctls, the Windows equivalent of those things)
  - or more diplomatically, an API that's opinionated about things you don't care about

## Operating Systems

* why do we care that the API is bad?
* many languages we use to write applications have libraries for interfacing with the OS's APIs, providing nicer abstractions for the application programmer
* but all abstractions are leaky; why can't I use a good API to begin with?

## Operating Systems

* consider a web server running "in the cloud"

## Operating Systems

```
+------------------------+----------------+
| User-level Application |  Dependencies  |
+------------------------+----------------+
| OS: scheduler, memory management,       |
| network, filesystems, clock, entropy,   |
| keyboard, video, power, user management,|
| mouse, screensaver, consoles, printers, |
| ...                                     |
+-----------------------------------------+
```

## Operating Systems

```
+------------------------+----------------+
| User-level Application |  Dependencies  |
+------------------------+----------------+
| OS: scheduler, memory management,       |
| network, filesystems, clock, entropy,   |
| keyboard, video, power, user management,|
| mouse, screensaver, consoles, printers, |
| ...                                     |
+-----------------------------------------+
| Hypervisor: scheduler, memory mgmt,     |
| network, filesystems, clock, entropy,   |
| keyboard, video, power,                 |
| mouse, screensaver, consoles,           |
| ...                                     |
+-----------------------------------------+
```

## Better Futures

* in lots of contexts, the only "drivers" we need are hypervisor drivers
* with a limited scope, replacing existing implementations with something nicer to interface with is less daunting

## Better Futures

* so if we don't need this maximalist OS that can respond to dynamic requests, can we drop the "executable" bits of the OS?
  - instead of an executable-but-also-kind-of-a-library that runs executables, we have another set of libraries that supports the executable we want

## Better Futures

* our application declares what it needs
  - e.g. knowledge of http, a library for session management, disk access, network

## Better Futures

* when we build, pull *everything* in together, and make a whole machine

## Better Futures

* building OS+application artifacts with library OSs
* demo time!

## Side Effects

* cool things to do with the libraries of library OSs
  - anything we do with libraries normally
  - test them! (continuously!)
  - profile them!
  - run them in a debugger!

## Recap

* monolithic operating systems are both executables, and libraries with a really bad API
* in many contexts, you don't need the executable part of the operating system
* you could have nicer abstractions for the library part of the operating system
* library operating system + application + build magic = unikernel + fun

## Thanks!

* to AppNexus for hosting
* to Recurse Center for localhosting
* to you for your attention, questions, & ideas :)

## Moar Stuff

questions I wish I'd been asked / backup slides
* how is this different from a microkernel?
  - more generally, a "how different from other approaches" slide?  microkernels, containers, exokernels, ?
* what about hypervisors?
* not a wish, but likely: debugging?
  - be ready with some stuff about solo.io/idit's microservices debugging?

## Slides

* it's a markdown file, presented in vim
* slide view provided by https://github.com/sotte/presenting.vim
* this is my equivalent of hand-drawn slides :)
