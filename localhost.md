---

Title: Library Operating Systems: from Localhost to Remote Guest

Abstract: Interfacing with lower-level systems using familiar abstractions, rather than alien ones, is a thing of joy  in testing, reasoning, modification, and participation. Library operating systems allow application programmers to approach operating systems programming in a way that's comprehensible, documentable, testable, and hackable with everyday tools. The end result is a whole virtual machine, easily run locally or as a remote guest in a public or private cloud, which is nonetheless smaller than many base container images. Let's democratize systems hacking!

---

I think the shape of the talk is:

* libraries vs executables
  - libraries: components for re-use in multiple software things
    * e.g. scipy, jquery, openssl (this is also an executable), java stl
  - executables: purpose-built runnable things
    * e.g. bc, doom, openssl (also a library), ...
* common programming advice: put as much logic in your libraries as you can
  - it's better to have more reusable bits than non-reusable bits
  - therefore, your executables should largely be interfaces to libraries
* operating system as monolithic executable
  - it does *everything*
  - it might do any one of the things it knows how to do, depending on the environment
  - that makes its behavior very hard to predict and understand
* but operating systems are also, kind of, libraries
  - with a really bad API (sockets, ioctls)
* why?
  - to support the largest set of user operations we possibly can
  - but only sometimes is that desired/appropriate (desktop OS)
  - in a lot of contexts you already know what set of operations you need
* consider a web server running "in the cloud"
  - you really just want it to respond to network requests and interact with a disk, in a deterministic way
  - you're quite a bit more likely to want more sophisticated *application* code than you are to want additional OS functionality
* so if we don't need this maximalist OS that can respond to dynamic requests, can we drop the "executable" bits of the OS?
  - instead of an executable-but-also-kind-of-a-library that runs executables, we have another set of libraries that supports the executable we want
  - our application declares what bits of the OS it needs
  - (and since we're building better worlds, why not use whatever language we want to provide those bits?  why not have multiple options?  why not have multiple interfaces, so we can choose the ones we like best?)
* building OS+application artifacts with library OSs
  - so... this exists :)
  - demonstrate building a "hello localhost :)" static web server
  - build for virtio and run locally + ship to GCP?  time this
* cool things to do with the libraries of library OSs
  - anything we do with libraries normally
  - test them! (continuously!)
  - profile them!
  - run them in a debugger!

---

questions I wish I'd been asked / backup slides
* how is this different from a microkernel?
  - more generally, a "how different from other approaches" slide?  microkernels, containers, exokernels, ?
* what about hypervisors?
* not a wish, but likely: debugging?
  - be ready with some stuff about solo.io/idit's microservices debugging?
