This is a proposed talk for PyCon 2015; it has not yet been accepted or rejected.

# Title #

Knocking Over Network Stacks with Scapy's fuzz()

# Category #

Testing

# Duration #

I prefer a 30 minute slot

# Description #

Python has great testing libraries, but what about using Python programs to test non-Python projects?  This is the story of how one Python programmer harnessed the power of the venerable Scapy network packet library (and a bit of pluck) to automatically find critical bugs in a non-Python network stack.

# Audience #

Pythonistas interested in automating testing of networked programs, esp. closed-source programs

# Python Level #

Intermediate

# Objectives #

Attendees will find out what fuzzing is, why black-box testing is sometimes appropriate, and see an example of how to do simple testing with Scapy to great reward.  

# Detailed Abstract #

## Motivation ##

 It's sometimes the case that a programmer wants to verify the robustness of a component for which the source is not available, or for which a good testing framework doesn't exist.  Many tools exist for doing this with programs that expect to read from the filesystem, but the set of open-source tools for randomized testing of networked programs is sparser.  Scapy, a Python module for generating, sending, receiving, and dissecting network packets, allows a tester to easily write custom testing modules for arbitrary network protocols.

 A huge number of protocols already have parsers in Scapy, so one can write fuzzers with relative ease and speed.  Writing the fuzzing and analysis programs in Python means one has access to the Python ecosystem and a vast number of helpful Python programmers.  

 Doing black-box testing over the network means that a complete record of the test can be taken with a tool like tcpdump, allowing the tester to construct a non-randomized test for later use when a particular input is discovered to produce unexpected results.  It also gains the user a view of what software components used in the target system do in normal operation, instead of under artificial conditions.

## Approach ##

 Divide and conquer!  Identify what you'd like to test (for example, a certain API endpoint or a service provided by your home router).  Isolate subcomponents of what you're testing, and think about multiple layers if it's applicable (e.g., testing your custom code, testing HTTP, testing TCP, testing IP).  Some documents, if they're available, can help you in determining how to best focus your tests.

 Then, it's test-writing time!  This is where using scapy really shines - it's often possible not to have to write much, because Scapy often already knows how to write messages of the kind you want (e.g. HTTP, UPnP).  Analyzing the results of your tests can be more complicated, depending on how detailed you want to be - if you're just trying to identify crash behavior, this is usually pretty easy.  Scapy can be used for more complicated types of analysis (e.g. "this call should always return 403 Unauthorized and if you ever get anything else back, that's a problem" or "if the conversation didn't contain two instances of the echo string, that's a problem").

## Acting On Information ##

 The output of your analysis is often a set of inputs for which the software does something unexpected.  These are great fodder for bug reports and unit or integration tests for the group that maintains the software you're testing.  Often responsible disclosure is a consideration.


# Outline #

* Motivation (3 minutes)
 - what's a fuzzer?
 - why fuzz?
 - why do black-box testing?
 - why use scapy?
* Approach (20 minutes)
 * identifying targets (1 minute)
 * isolating subcomponents (2 minutes)
 * writing high-level tests (3 minutes, example)
 * writing high-level analyzers (3 minutes, example)
 * high-level test and analysis demo (2 minutes)
 * writing low-level tests (5 minutes, example)
 * writing low-level analyzers (2 minutes, example)
 * low-level test and analysis demo (2 minutes)
* Acting on Information (2 minutes)
 * using test output as unit tests / bug reports (1 minute)
 * responsible disclosure (1 minute)
* Questions (5 minutes)

# Additional Notes #

I am a former technical trainer and, more recently, have given lightning talks for Hacker School's Thursday presentations.  I spoke on testing at Madison CodeChix on 17 September 2014; a small portion of the talk [is available on YouTube](https://www.youtube.com/watch?v=XHsqoHBR9G4&feature=youtu.be).  If more portions are made available by CodeChix, I will link them here as well.

I've written several blog posts on the work that inspired this talk, although most of it has been focused on the stack I was testing rather than the tools I used to test it.  There are details in [this blog post](http://somerandomidiot.com/blog/2014/05/22/throwing-some-fuzzy-dice/), as well as [this one](http://somerandomidiot.com/blog/2014/07/09/how-to-set-the-evil-bit/) relevant to the work, but few details will be shared between any blog post and this talk.

# Additional Requirements #

A projector capable of displaying VGA input will be required.
