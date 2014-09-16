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

 ## Approach ##

 To test the custom network stack of another project, I chose to use a black-box fuzzer written in Scapy.  I used a black-box external test, rather than an internal test, to get a realistic picture of how the network stack would behave in response to real-world stimulus from other networked programs.  The fuzzer's randomly generated input effectively allowed me to run many tests and execute many different paths through the network stack's code tree, without having to manually analyze code paths or write specific test cases.

 I proceeded by writing several automatic fuzzing programs for important protocols like TCP, then writing analysis modules for packet traces from these fuzzing sessions to determine which inputs caused problematic behavior from the network stack.

 ## Results ##

 Most protocols that were tested in the bespoke network stack had parsing bugs revealed by the fuzz testing.  Using randomized input allowed for much quicker discovery of broken cases in the parser than would have bene possible by manually creating test cases.

 ## Lessons learned ##

 There were a few drawbacks to my approach.  In some cases, I had to rewrite library functions in Scapy to elicit packets broken enough to trigger interesting behavior in the fuzzed program.  The concepts of "succeeded" and "failed" were particular to each protocol, and so required me to hand-write code for analysis.  In some cases, I had to write simple custom code to enable my tests, because low-level protocols couldn't be tested directly without something on top of them.

 This approach also had some significant benefits.  Most protocols already had parsers in Scapy, so I could write fuzzers with relative ease and speed.  Writing the fuzzing and analysis programs in Python meant I had access to the Python ecosystem and a vast number of helpful Python programmers, which was helpful at a number of points in developing the programs.  When I did find a bug, it was quite easy to use the packet trace of the fuzz test to generate a proof-of-concept that worked against examples of this network stack outside of the lab.

Motivation (3 minutes):

* testing newly-created network stack that hadn't really been in the field
* discover bugs that might lead to poor performance, crashes, or security problems

Approach: (8 minutes)

* black box fuzzer written in Python using the [Scapy](http://bb.secdev.org/scapy/overview) network testing library
* what's a fuzzer? - device for generating random input and feeding it to a program
* what's "black box" mean? - we're interfacing with the compiled and running program rather than writing a test program to use functions from our program; we interact with the program as a user or other network device would
* why a black box fuzzer? we can make the computer find bugs we would never think of; it has more patience for testing than we do. also, since the fuzzer has no knowledge of the codebase, we can use it to test multiple implementations.
* process:
     * write automatic fuzzers for important protocols with complex parse trees & state
     * write analysis modules to see whether tests succeeded or failed

## Results: (7 minutes) ##
* found bugs in DHCP, HTTP, and TCP stacks with varying levels of severity
* drill deep on TCP option parsing bug:
       * focus on & explain randomized generation of TCP options
       * show a few examples of randomized packets
       * show the packet that triggered bounds checking bug
       * explain the results of receiving this packet (complete program crash)

## Lessons learned: (7 minutes) ##
* drawbacks to approach:
     * in some cases, had to rewrite library functions to elicit interestingly broken packets
     * "succeeded" and "failed" is tricky and not generalizable; each test needed its own analysis code
     * in some cases (TCP), had to also write simple custom code to enable tests
* benefits to approach:
    * most protocols already had parsers in scapy, so fuzzers could be set up relatively quickly
    * access to python ecosystem - preaching to the choir but this was really helpful
   * tests could be easily made into proofs-of-concept to run against live code
* you can get a lot of return on very little investment with randomized tools
* parsers are hard!  so is input validation.

##  Questions? (5 minutes) ##

# Additional Notes #

I am a former technical trainer and, more recently, have given lightning talks for Hacker School's Thursday presentations.  I am speaking on testing at Madison CodeChix on 17 September 2014; a link will be posted here once video is available.

I've written several blog posts on the work that inspired this talk, although most of it has been focused on the stack I was testing rather than the tools I used to test it.  There are details in [this blog post](http://somerandomidiot.com/blog/2014/05/22/throwing-some-fuzzy-dice/), as well as [this one](http://somerandomidiot.com/blog/2014/07/09/how-to-set-the-evil-bit/) relevant to the work, but few details will be shared between any blog post and this talk.

# Additional Requirements #

A projector capable of displaying VGA input will be required.
