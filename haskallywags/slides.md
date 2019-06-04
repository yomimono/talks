# Library Operating Systems: Functional Programming for the Whole System

Mindy Preston

team website: https://robur.io
project website: https://mirage.io
mastodon: https://wandering.shop/@yomimono

## Quick Intro

me, in early 2014: I'm gonna learn some Haskell
me, six weeks later: this Elm thing sure is shiny
me, two weeks later still: Oooooh, OCaml library operating system
me, in mid-2019: still nerd sniped, still doesn't really know Haskell

## Operating Systems: An Application-Centric View

```
+----------------------------------------+
| User-level Application + Dependencies  |
+----------------------------------------+
           API in Your Language
+----------------------------------------+
|     Runtime (or not!), shared libs     |
+----------------------------------------+
        Sockets, syscalls, & such       
+----------------------------------------+
| OS: useful abstractions (file handles) |
| OS: access to resources (mem, cpu, net)|
+----------------------------------------+
```

## Zoom Out.

```
+-------------+-------------+------------+
| Application | Application | Application|
+----------------------------------------+
| OS: useful abstractions (file handles) |
| OS: access to resources (mem, cpu, net)|
+----------------------------------------+
| ???                                    |
+----------------------------------------+
```


## Enhance.

```
+-------------+-------------+------------+
| Application | Application | Application|
+-------------|-------------|------------+
| OS          | OS          | OS         |
+-------------+-------------+------------+
| Hypervisor: access to resources        |
+----------------------------------------+
```

## What's the OS *really* doing for us?

```
+----------------------------------------+
| User-level Application + Dependencies  |
+----------------------------------------+
           API in Your Language
+----------------------------------------+
|     Runtime (or not!), shared libs     |
+----------------------------------------+
        Sockets, syscalls, & such       
+----------------------------------------+
| OS: useful abstractions                |
| OS: interface to hypervisor            |
+----------------------------------------+
| Hypervisor: access to resources        |
+----------------------------------------+
```

## What's Left Once We Cut Out the Middleman?

```
+----------------------------------------+
| OS: useful abstractions                |
| OS: interface to hypervisor            |
+----------------------------------------+
```

## OS Abstractions: Useful, but Not Nice

hackage.haskell.org on Network.Socket:
> "essentially the entire C socket API is
> exposed through this module ...
> consult your favourite Unix networking book"

## Building on Sand

hackage.haskell.org on Network, the recommended
higher-level abstraction for network connections:
> This module is kept for backwards-compatibility.
> New users are encouraged to use Network.Socket instead.

## What Do We Do with Bad Abstractions?

let's replace them with something more convenient!

## Library Operating System

```
+-------------------------------------------+ \ 
|  User-level Application  |  Dependencies  | |
+-------------------------------------------+ |
        API in Your Language                  | 
+------------+---------+------------+-------+ |
| Networking | Storage | Randomness | ....  | |
+------------+---------+------------+-------+ |
        API in Your Language                  | unikernel
+-------------------------------------------+ |
| Language runtime                          | |
+-------------------------------------------+ |
| Hypervisor interface layer                | |
+-------------------------------------------+ / 
| Hypervisor                                |
+-------------------------------------------+
```

## 

```
+---------------+-------------+------------+
| Application   | Application | Application|
|    and        |-------------|------------|
| Dependencies  | OS          | OS         |
| = unikernel   |             |            |
+---------------+-------------+------------+
| Hypervisor: access to resources          |
+------------------------------------------+
```

## Language Specificity Enabled

+ Haskell library operating system: HaLVM
  - https://github.com/galoisinc/halvm
+ many others: IncludeOS (C++), Clive (Go), LING (Erlang/OTP), ...
+ targeting POSIX abstractions: OSv (which can run the JVM, Python, Node...)

## Some Framing on OCaml

also an ML descendent
<3 types, inference
strict evaluation by default

## Some Honesty About OCaml

has several affordances that exist but are rude to use:
  + global mutable state
  + imperative control structures
  + object system (the O in OCaml)
and something which is beyond rude: Obj.magic

"nice" OCaml code is Haskell-like

## What do the Libraries Look Like?

+ An interface definiton for common operations
  * "check the time", "get a random number", "send a packet", "write a file"
  * (MirageOS: module types)
+ Implementations of that interface
  * "interactions with a btrfs filesystem", "talking over an Ethernet network"
  * (MirageOS: modules)

## A Deeper Dive: Module Types

"how do you get by without typeclasses?"

```ocaml
module type OrderedType = sig
  type t
  val compare : t -> t -> int
end
```

## Given Module X, Make Module Y

```ocaml
module type Map = sig
  type key
  ...
end

module Make (Ord : OrderedType) : Map with type key = Ord.t
```

## "Functors"

```ocaml
module StringMap = Map.Make(String)
```

## Connecting to Remote Hosts

* replace this:

```c
int socket(int domain, int type, int protocol);
/* many integer constants defined below... */
/* on success, a file descriptor is returned *.
/* on error, -1 is returned, and errno is set */

int connect(int sockfd, const struct sockaddr *addr,
            socklen_t addrlen);
/* socklen_t is "in reality an int" */
/* on success, 0 is returned */
/* on error, -1 is returned, and errno is set */
```

## Connecting to Remote Hosts

* with this:

```ocaml
module type TCP = sig
  val create_connection: t -> ipaddr * int -> (flow, error) result io
  (** [create_connection t (addr,port)] opens a TCPv4 connection
      to the specified endpoint. *)
end
```

## What's an Implementation Look Like?

```ocaml
  let create_connection tcp (daddr, dport) =
    Pcb.connect tcp ~dst:daddr ~dst_port:dport >>= function
    | Error e -> log_failure daddr dport e; Lwt.return @@ Error e
    | Ok (fl, _) -> Lwt.return (Ok fl)
```

## Quick Demo

+ code is from https://github.com/mirage/mirage-skeleton

(hello Haskeletons ;)

## Full-Stack Development

+ stuff that makes it harder to dig into software is *bad*
+ having a stack that's easier to understand is *good*

## Substituting Our Own Reality

```
type output =
  | OS_process
  | Minimal_virtual_machine

val build : application -> dependencies list ->
            implementations list -> output

let test_application = build application depends \
    my_implementation::implementations
```

## "Fun" Custom Implementations

+ network interfaces that always have new packets waiting
+ random number generators that read from a list
+ entropy sources that always block
+ filesystems that are always full
+ block devices that are always busy
+ DNS that always returns the IP of put-your-creds-here.party

## Developing Systems like an Application Developer

>Amusing fact: my original motivation for rump kernels (2007) was to make debugging OS kernel drivers less braindead.

--@anttikantee

## Testing a MirageOS implementation

```ocaml
let test_marshal_padding () =
  let buf = Cstruct.create 8 in
  Cstruct.memset buf 255;
  let extract = Cstruct.get_uint8 buf in
  let needs_padding = [ Tcp.Options.SACK_ok ] in
  check 4 (Tcp.Options.marshal buf needs_padding);
  check 4 (extract 0);
  check 2 (extract 1);
  check 0 (extract 2); (* 0 out everything else *)
  check 0 (extract 3);
  check 255 (extract 4); (* but not into random memory *)
  Lwt.return_unit
```

## Testing Network Stack

```
$ ./test.native test tcp_options|grep -v SKIP
[OK]        tcp_options  0   unmarshal broken mss
[OK]        tcp_options  1   unmarshal option w/bogus length
[OK]        tcp_options  2   unmarshal option w/zero length
[OK]        tcp_options  3   unmarshal simple cases
[OK]        tcp_options  4   unmarshal stops at eof
[OK]        tcp_options  5   unmarshal tcp options
[OK]        tcp_options  6   unmarshal random data returns
[OK]        tcp_options  7   marshal unknown value
[OK]        tcp_options  8   marshal when padding is needed
[OK]        tcp_options  9   marshal the empty list
Test Successful in 0.015s. 10 tests run.
```

## Wilder Thoughts

+ state: not just for shell examination anymore

## What if every change to /proc had a commit message?

+ Irmin: library for persistent stores w/built-in snapshot, branching, & reverting mechanisms
  - (presents interfaces that look a lot like git)
  - multiple backends, including in-memory and on-disk

## Visualizing Control Flow

+ What if the scheduler left a log?

## On the Desktop (MirageOS + QubesOS = <3)

+ QubesOS: hypervisor + nice controls for VMs + nice UI
+ individual VMs are heavy - replace some with unikernels!
+ qubes-mirage-firewall (https://github.com/talex5/qubes-mirage-firewall)
+ eye-of-mirage (https://github.com/cfcs/eye-of-mirage)
+ passmenage (https://github.com/cfcs/passmenage)
+ ocaml-ssh-agent (https://github.com/reynir/ocaml-ssh-agent)

## Thanks!

for your ML family open-mindedness :)
and your questions!

## Moar Stuff

## How is this different from a container?

Containers provide segmented views of userspace, with the same Linux kernel

```
+--------------+--------------+
| Application  | Application  |
+--------------+--------------+
| Container    | Container    |
+--------------+--------------+
|        Linux kernel         |
+-----------------------------+
|         Hypervisor          |
+-----------------------------+
```

## What about hypervisors?

* minimizing interfaces: ukvm via solo5 (expose only the functionality you know the unikernel needs)
* fully-static systems: muen

https://github.com/solo5/solo5
https://muen.sk

## Debugging?

* remember all that stuff I said about state?
* not "how do you debug a unikernel" necessarily - a general issue in microservices
* see https://github.com/solo-io/squash
* that said, gdbserver integration is available for some MirageOS targets

## Slides

* it's a markdown file, presented in vim
* slide view provided by https://github.com/sotte/presenting.vim
* this is my equivalent of hand-drawn slides :)
