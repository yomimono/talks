# Library Operating Systems: from Localhost to Remote Guest

Let's democratize systems programming!

Mindy Preston
https://robur.io

## Where To?

* libraries and executables
* operating systems: why do we need them, anyway?
* building better futures
* positive side effects of radical change

## Libraries and Executables

* libraries: components for re-use in multiple software things
  - e.g. scipy, jquery, ncurses, java stl
* executables: purpose-built runnable things
  - e.g. bc, doom, atom, word, firefox, ...

## Libraries and Executables

* common programming advice: put as much logic in your libraries as you can
  - it's better to have more reusable bits than non-reusable bits
  - if you do this, your executables might largely be interfaces to libraries

## Libraries and Executables

* build your executables, using libraries
* run them on an operating system
  - e.g. windows, linux, bsd, haiku, hurd, ...

## Operating Systems

* Is an operating system an executable?
  - it definitely executes
  - it might do any one of the things it knows how to do, depending on the environment
  - that makes its behavior very hard to predict and understand

## Operating Systems

* But maybe it's also a library?
  - often with a *really* bad API (sockets, ioctls, the Windows equivalent of those things)
  - or more diplomatically, an API that's opinionated about things you don't care about

## Operating Systems

* why do we care that the API is bad?
* many languages we use to write applications have libraries for interfacing with the OS's APIs, providing nicer abstractions for the application programmer
* but all abstractions are leaky; why can't I use a good API to begin with?

## Operating Systems

* why do we build executables for operating systems?
  - to support the largest set of user operations we possibly can
  - but only sometimes is that desired/appropriate (desktop OS)
  - in a lot of contexts you already know what set of operations you need

## Operating Systems

* consider a web server running "in the cloud"
  - you really just want it to respond to network requests and interact with a disk, in a deterministic way
  - you're quite a bit more likely to want more sophisticated *application* code than you are to want additional OS functionality

## Better Futures

* so if we don't need this maximalist OS that can respond to dynamic requests, can we drop the "executable" bits of the OS?
  - instead of an executable-but-also-kind-of-a-library that runs executables, we have another set of libraries that supports the executable we want
  - our application declares what bits of the OS it needs
  - (and since we're building better worlds, why not use whatever language we want to provide those bits?  why not have multiple options?  why not have multiple interfaces, so we can choose the ones we like best?)

## Better Futures

* building OS+application artifacts with library OSs
  - so... this exists :)
  - demonstrate building a "hello localhost :)" static web server
  - build for virtio and run locally + ship to GCP?  time this

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
