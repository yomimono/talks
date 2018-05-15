# Library Operating Systems: from Localhost to Remote Guest

Mindy Preston

team website: https://robur.io
personal website: https://somerandomidiot.com
mastodon: https://wandering.shop/@yomimono

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

## What do the Libraries Look Like?

+ An interface definiton for common operations
  * "check the time", "get a random number", "send a packet", "write a file"
  * (MirageOS: module types)
+ Implementations of that interface
  * "interactions with a btrfs filesystem", "talking over an Ethernet network"
  * (MirageOS: modules)

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

Note:
We could use a featureful library for storing state that gives us some affordances for examining and changing that state!

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
