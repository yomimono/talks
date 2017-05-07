# DHCP: IT'S MOSTLY YELLING!!!

  Me: Mindy Preston
  Twitter: @mindypreston
  GitHub: @yomimono
  Pronoun.is/she?or=they

# where am I, what is this?

  first boot on a new network!
  how can we send traffic?

  we need some information first!

# unicast IP address

  set source address so we can get replies

# default gateway

  who takes our traffic to the next hop?

# subnet mask

  which traffic needs to leave this network?

# how do we get help?

  ask on local network?

  how can we do that without an ip?

# The solution: YELLING

  special addresses you can use in IP packets!

# yelling on an IPv4 network!!

  destination of all 1's (255.255.255.255)
  
  "limited broadcast"

# yelling on an IPv4 network!!

  source of all 0's (0.0.0.0): "...me?"

  to reply, send to 255.255.255.255!
  
# what about on the Ethernet level?

  source is always known!

  broadcast: all 1's (ff:ff:ff:ff:ff:ff)

# HOW TO YELL

  Ethernet layer: $me -> ff:ff:ff:ff:ff:ff

  IPv4 layer: 0.0.0.0 -> 255.255.255.255

# bootstrapping non-yelling!

  that's DHCP!

  yell loud, someone will help you!

# DHCP!!

  client broadcasts: UDP destination port 67

  server broadcasts: UDP destination port 68

# conversations when everyone's yelling

  each message needs a transaction ID (xid)!

# keeping track of what's happening

  each message also needs a message type!

# LET'S MAKE SOME NOISE

DHCPDISCOVER!!

HELLO THIS IS xid 0x12345678!!! 
  I AM DISCOVERING!!  PLZ HELP!!
  PLEASE GIVE ME AN IP
  AND ALSO A DEFAULT GATEWAY
  AND SOME DNS SERVERS!!

# OK I HEARD YOU JEEZ

DHCPOFFER!!

HI THIS MESSAGE IS FOR xid 0x12345678 !
  I'M A DHCP SERVER AND HERE IS MY IP!!
  I'M OFFERING YOU THIS IP ADDRESS!
  HERE IS THE NETWORK'S SUBNET!
  HERE IS A DEFAULT GATEWAY!!
  AND SOME DNS SERVERS!
  YOU CAN USE THIS FOR SOME NUMBER OF SECONDS!!

# OK THANKS SOUNDS GOOD

DHCPREQUEST!!

HI THIS IS xid 0x12345678!!
  I REALLY LIKE THIS IP ADDRESS!!
  IT CAME FROM THE SERVER WITH THIS IP!!
  I'D LIKE TO REQUEST THAT I USE IT FOR A WHILE!
  ALSO THIS SUBNET
  AND THIS GATEWAY
  AND THESE DNS SERVERS!!

# OK GREAT THANKS A LOT

DHCPACK!!

HI THIS MESSAGE IS FOR xid 0x12345678 !
  I'M A DHCP SERVER AND HERE IS MY IP!!
  I ACKNOWLEDGE YOU HAVE THAT IP ADDRESS!!
  YOU CAN HAVE IT FOR ONLY SO LONG!!
  OH YEAH ALSO PLEASE USE THIS SUBNET
  AND THIS GATEWAY
  AND THESE DNS SERVERS!!

# all set!

and then we don't need to yell any more. :)

# let's see it!

computers are yelling RIGHT NOW!!

# other DHCP options!
 
 42 : NTP servers! (hi joel!)
252 : web proxy autodiscovery!

# what can go wrong?

DHCPNAK!!
  HI THIS MESSAGE IS FOR 0x12345678
  ACTUALLY YOU CAN'T HAVE THAT IP I GAVE YOU

# nobody's listening??

  169.254.1.1 :(

# going rogue

  yelling is common, shushing is rare

# audience participation

  accidental participation is unlikely

  (but possible)

# thanks!

  slides: https://github.com/yomimono/talks
  DHCP resources:
    RFC 2131 (state machine)
    RFC 2132 (common options)
  my favorite DHCP library:
    https://github.com/mirage/charrua-core

# BUT WAIT

why are configurations only valid for so long?

# legacy ip!!

dhcpv6 exists!

# AND ALSO

giving up your lease!
