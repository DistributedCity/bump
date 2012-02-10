[Hack of initial partial draft. This document is under serious revision.]

# BuMP
##Bus Message Protocol for an Unrouted Fully Replicating Network — without Masters.

##Bump is a P2P Encrypted Anonymous Distributed Authenticated Replicated Messaging System

BuMP has been inspired by git, NNTP, BitTorrent, SpeakEasy, Pynchon-Gate, and Anon Remailers
of all Types. Of course even more importantly it is an overlay communications system on to of
the Bitcoin based system: [Namecoin](http://dot-bit.bit/Main_Page).

The BuMP reference implementation features an encrypted, fully distributed, anonymous replicated messaging system.

It can be seen as "the perfect echo chamber", where everything is clearly heard everywhere, finding its way around
obstacles, natural — or man-made — just as a pebble dropped in the ocean will raise the sea level everywhere, its
meaning to be clearly seen and understood if you know how to look for it...

##Network Infrastructure

* See the [example messaging proposal](http://dot-bit.bit/Messaging_System) for Namecoin.
* The entire BuMP system is a messaging infrastructure overlayed on the P2P Namecoin system.
* Imagine the Bitcoin network with messages. Sort of, not really, as its so much more than that.
* Peers are regular Mix nodes that also broadcast to other Mix nodes.
* Each peer detects messages addressed specifically to itself or a shared tag.
* A message when decrypted may be only 1 of 2 things:
  1. A message with a final destination specifically for this peer
  2. A relay message which will contain:
    * Always: Another bump msg to be rebroadcast {"tag":"01234","chunk":"467B1gRYAFBhY/lEdfEUc17m7Tgqzknh"}
    * Always: Tau Mixing Variables (See Below)
    * Optionally: A postage field containing Bitcoins, OT Digital Cash, Namecoins, whatever the market wants.

* Since all peers see all messages, recipients of a BuMP maintain anonymity.

##Forward Tau Mixing
Each BuMP Peer acts as part of Tau mixnet in order to offer strong unlinkability between a client and the
messages it inserts/requests from the network.

Tau mixing is a multi latency batching strategy in which clients tag their layer encrypted messages with  'Tau values'.
There are two Tau values; minimum crowd size and minimum time delay. This means that after a Tau mix receives a message,
it will be held until a client defined number of other messages are gathered by the server, and also for a client
defined minimum amount of time.

By virtue of having minimum crowd sizes and various time delays, Tau mixed messages are resistant to end to end
correlation attacks. An attacker engaging in an end to end correlation attack observes the data entering a proxy
and the data leaving a proxy. With a 'first in first out' proxy system, as all low latency systems essentially
are, an end to end correlation attack can easily link data sent and data received. When combined with message
padding, Tau mixing greatly increases the complexity of such an attack. This is because messages from potentially
many clients are received by the mix before a given message is pushed. The order in which messages are received by
a mix no longer correlates with the order messages leave.

One advantage Tau mixing has over less sophisticated mix designs, for example threshold mixing, is resistance to
flushing attacks. Some mix systems gather messages from clients until a certain amount of messages have been received.
The mix then reorders the messages prior to pushing them. In a flushing attack the adversary sends many messages to
a mix which causes the mix to push messages before a secure crowd size has been established. By allowing the client
to select the minimum crowd size on a per message basis (instead of the mix having a static minimum crowd size), and
more so by implementing a minimum time delay (also client selected), Tau mixes are significantly more resistant to
flushing attacks than most other types of mix.

Tau mixing can take place either over a series of multiple nodes, or over a single node (acting as a mix node in
addition to a storage node in this case). If a client sends a message directly to a centralized BuMP node, outside
of the network context, it will be held until its Tau value expires before it is made public. One thing to keep
in mind is that if Alice the client directly broadcasts a message to Bob, she will not get the anonymizing
advantages of Tau mixing if Bob is her adversary/compromised.


##Message Format and Dummy Messages

BuMP messages in the mixing stage consist of four parts; forwarding headers (768 bits), Tau Values (128 bits),
communications data and padding (remainder up to 1 KB total). Messages are standardized sizes so that they all
blend in while mixing. Clients wishing to send larger messages will be required to split them up into multiple
parts. Clients may legitimately desire to hide the volume of their 'streams' of messages. To accomplish this,
time delays between 'bursts' of a given message should be allowed as well as the ability to send dummy messages
set to drop at some point down their path.
Routing

## Routing
Although it is possible a peer to broadcast a message intended for another peer, strong anonymity for the sender
requires routing communications through some number of middle peers.

Messages propagate through out the BuMP network layer in 2 steps:
1 Insertion
2 Mixing

Since ALL peers act as Mixers and Mixing is an act of Insertion it is unknown to observers of the network which is taking place.


#Messages

####Format
The transport encoding of a package must be in JSON format:

     {"tag": "(Fixed size identifier (can be based on a hash)",
      "chunk: "The message"}

####Example 1:
Facebook/Tweet/Lamer style:

     {"tag": "#mynakedlife4all",
      "chunk: "I like big brother!"}


####Example 2:
Unoptimized or example only:

     {"tag": "cecde804bf48c2eb8f1941e0004dd082ab2eefac",
      "chunk:"-----BEGIN PGP MESSAGE-----
              Version: GnuPG v10.4.99 (GNU/Linux)

              jA0EAwMCO5j6AG++w7BgyUI8P8i3WiR4Fgk3L6Z8SqAUBH6TwAQt1v8erZm5EHqM
              Ln5Qc+rB6cA5Yt7rr9YVe+wG3Fmp+dtjpV5CkVeeCo9t07o=
              =ZAAl
              -----END PGP MESSAGE-----"}

####Example 3:
Optimized:

     {"tag": "cecde804bf48c2eb8f1941e0004dd082ab2eefac",
      "chunk:"jA0EAwMCO5j6AG++w7BgyUI8P8i3WiR4Fgk3L6Z8SqAUBH6TwAQt1v8erZm5EHqM
              Ln5Qc+rB6cA5Yt7rr9YVe+wG3Fmp+dtjpV5CkVeeCo9t07o=
              =ZAAl"}

Tags do not have to be unique, but could be seen as a way to "group" related messages together. With as many potentially different Tags as there are molecules in planet Earth, selecting one for your own use should be doable.

The actual interpretation and/or layout of the contents of a Chunk is not defined, but can be any encoding agreed to by the actual sender and receiver, which thus can also include structured and/or encrypted contents.

Note that the packages do not include sender or receiver. It is up to the sender and receiver to agree on which Tags to use for communication between them, as well as any handshake/confirmation handling. The protocol itself does not do so (intentionally).


#### Example 4:
A practical low level example using the command line:

Message:
-----
From: The BuMP Project
Subject: Demo of a group-related BuMP message

This message was first encoded on Linux as follows:
gpg -c -o - message.txt | base64 >> bumpmsg.dat

Then was inserted into the network as follows:
(bump send [TAG] [CHUNK])
bump send 0123456789ABCDE19283726312B7935DFA83C709 bumpmsg.dat

The clear text can be retrieved from the BuMP package like this:
bump get 0123456789ABCDE19283726312B7935DFA83C709 | base64 -d | gpg > decoded

The pass phrase was "look mama, no handles"
-----
#####The corresponding BuMP package:

{"tag" : "0123456789ABCDE19283726312B7935DFA83C709",
"cjA0EAwMC0r3fUktXS1pgycBjP948FzLeCeLOttBwEzUA/QG5+GRRpr1cgdqhNzcsw22iY0YQjWQs
dRKsdktQ/VoV1rMEyg/2I40QV467B1gRYAFBhY/lEdfEUc17m7Tgqzknhb+me25JOOdV4GP3AIsJ
eVVUJGMYfdRK9mypcpFHCNsh5mmceHmm4YmZYIOc5dS8UKph82u6/DIqUbLJ+Idfdo990RGPkBHS
NPWuCMd8qzTn9/0TbPd8zhRzzk6zVNDg72KxUMIJTJwmLJ/v1aFyBAuEYBbvdYym5oVa3VE4KDTt
4MxYTT1Fu0RynlU00XnDfyYsDsF+W2CPSmemSWGXlgKFOfx4BVhB0T3MRSc56NH9bKVekDa/d6N5
P7pXcWvHKHhplVNwURTFnkJOK7mq7vao"}

#####All group participants will look for BuMP messages with the Tag:
0123456789ABCDE19283726312B7935DFA83C709





Use at your own risk: it might eat cute little puppies for breakfast, just for fun...

Speak Easy (SE) is a client side program which aims to be a secure and resilient group communications tool,
utilizing a the BuMP messaging system, while presenting a client interface similar in many ways to a traditional
browser based forum.

BuMP and SE attempt to offer sophisticated protection from a variety attacks, however it is not capable of
offering protection from all potential attacks. Primarily, SE offers protection from communications and signals
intelligence oriented attacks. SE also aids group organization and efficiency, for example down time should be
thing of the past, DDOS strongly protected from and significant points of compromise decentralized.

Additionally, SE will aid in the compartmentalization / cellularization of the networks using it.
SE is also capable of providing less easily defined advantages, for example by offering a shared
resource those participants in the network who could not otherwise afford or implement secure
communications setups will be able to piggy back on the resources of the others, while offering
their own advantages to the network merely by participating in it.

BuMP and SE add additional protections and convenience over the use of Tor, GPG and traditional forums, however we
suggest that it is used in combination with these technologies.  SE offers strong protections which can not be
offered by the low latency Tor network. Although Tor protects from traffic analysis, it is weak to traffic
confirmation. Analysis of the timing characteristics of data flows can distinguish a stream at multiple
points on the network. This allows an attacker who can watch your entry and exit node to link you to the
server you communicate with. Tor has only a few hundred entry nodes and three are selected for use, with
the three selected entry guards changing about once a month. Additionally, it should not be difficult for
a competent attacker to position themselves in such a way that they can monitor the end of the traffic
flow, even if hidden services are utilized. SE protects from traffic confirmation attacks by using a
technique known as Tau mixing; multiple layer encrypted posts are re-ordered at various mixes, with
one layer of encryption being removed at each hop. This allows for a single good mix to protect a message
from being linked to the sender, even if all other points of the messages path are watched. Such time
delay/mixing protects from a large variety of potential traffic confirmation attacks. Additionally,
Tor can be compromised 100% of the time by a global passive adversary, an attacker capable of watching
all links between nodes. SE can withstand a global passive adversary for some period of time and volume
of communications. We suggest that the SE process be contained in layers of isolation, configured to
route traffic through Tor run on the host. We also suggest that all mixes be run as Tor hidden services.

In the area of communications security, SE offers more convenience than direct advantage over solutions
like GPG. Usage is as simple as usage of a traditional forum, however posts and private messages are
automatically encrypted using clearance based network compartmentalization algorithms. All posts are
automatically encrypted, no plaintext is ever present on servers nor is anyone with out a clearance
to view a posts plaintext capable of doing so. We suggest that SE be isolated in layers of
virtualization, with GPG run on the host and used in addition for private messages. This
will force a hacker to compromise SE and break out of multiple layers of isolation in
order to compromise all private keys.

SE offers many tactical advantages over traditional forums. By distributing the communication channels,
SE can protect from random server down time. As long as one of the servers of the distributed network
is operational, bidirectional communications will be possible. SE can also offer strong protection from
DDOS and DOS attacks for the same reason. Additionally, SE removes the strategic incentive to compromise
key individuals tasked with running the communication channels, by securely decentralizing 'responsibility'
for maintaining servers, as well as removing direct ties between server administrators and the specific
groups communicating through them. SE additionally offers the ability to entirely protect communications
from non-authorized individuals, including the very server administrators and data centers offering the
physical infrastructure to the network. SE will also aid in compartmentalization, and likely will
increase threshold security by offering a sophisticated solution with minimal technical skill required for utilization.

(work in progress)