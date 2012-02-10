BuMP is privacy-enhancing message transport mechanism inspired by git, NNTP and BitTorrent. The BuMP reference implementation features an encrypted, fully distributed, replicated messaging system.

It can be seen as "the perfect echo chamber", where everything is clearly heard everywhere, finding its way around obstacles, natural — or man-made — just as a pebble dropped in the ocean will raise the sea level everywhere, its meaning to be clearly seen and understood if you know how to look for it...
Protocol

A more in-depth description of the BuMP protocol details is avalable here.
Messages

Each message package consists of:

        Tag: Fixed size identifier (can be based on a hash)
        Chunk: The message

Tags do not have to be unique, but could be seen as a way to "group" related messages together. With as many potentially different Tags as there are molecules in planet Earth, selecting one for your own use should be doable.
The actual interpretation and/or layout of the contents of a Chunk is not defined, but can be any encoding agreed to by the actual sender and receiver, which thus can also include structured and/or encrypted contents.

Note that the packages do not include sender or receiver. It is up to the sender and receiver to agree on which Tags to use for communication between them, as well as any handshake/confirmation handling. The protocol itself does not do so (intentionally).

The transport encoding of a package can be in any format agreed upon, such as XML, DER or plain text, as long as each package can be clearly separated into Tag and Chunk.
A practical example:

Message:
-----
From: The BuMP Project
Subject: Demo of a group-related BuMP message

This message was encoded on Linux as follows:
echo BuMP 0123456789ABCDE19283726312B7935DFA83C709 > bumpmsg
gpg -c -o - message | base64 >> bumpmsg

The clear text can be retrieved from the BuMP package like this:
tail +2 bumpmsg | base64 -d | gpg > decoded

The pass phrase was "look mama, no handles"
-----
The corresponding BuMP package:

BuMP 0123456789ABCDE19283726312B7935DFA83C709
jA0EAwMC0r3fUktXS1pgycBjP948FzLeCeLOttBwEzUA/QG5+GRRpr1cgdqhNzcsw22iY0YQjWQs
dRKsdktQ/VoV1rMEyg/2I40QV467B1gRYAFBhY/lEdfEUc17m7Tgqzknhb+me25JOOdV4GP3AIsJ
eVVUJGMYfdRK9mypcpFHCNsh5mmceHmm4YmZYIOc5dS8UKph82u6/DIqUbLJ+Idfdo990RGPkBHS
NPWuCMd8qzTn9/0TbPd8zhRzzk6zVNDg72KxUMIJTJwmLJ/v1aFyBAuEYBbvdYym5oVa3VE4KDTt
4MxYTT1Fu0RynlU00XnDfyYsDsF+W2CPSmemSWGXlgKFOfx4BVhB0T3MRSc56NH9bKVekDa/d6N5
P7pXcWvHKHhplVNwURTFnkJOK7mq7vao

All group participants will look for BuMP messages with the Tag:
0123456789ABCDE19283726312B7935DFA83C709

Servers

    Uses an in-memory database to efficiently replicate BuMPs without sending or receiving duplicates, but can also maintain an external database where received BuMPs are permanently stored.
    Built in OpenSSL SSL/TLS for closed groups. Full support for SSL certificate verification is included. This means that a set of bumpservers can maintain a completely private and still distributed, replicated network.
    Built in Web Server:

    Should anyone connect to a bumpserver using a normal web browser then the server will act as a http/https server, returning "404 Not Found".

    As special cases, "tag/01234..." or "id/ABCDE..." will result in a html page with any matching BuMPs that the server has access to. Also, asking for "tag/all" will show ALL accessible BuMPs.

    In fact, a bumpserver can handle most normal http requests and deliver static information from a directory tree, if defined and named by the optional parameter httpdir. Note that this name MUST end with '/'.
    Apart from making the set of current BuMP packets available via http(s), this feature also masks the presence of the additional replicating BuMP network, which is the real purpose of it all.

Peers

    Peers are regular Servers that also point to other Servers replicating each others messages (and databases).
    A server may have many peers.
    After a connection or network problem, a peer will automatically replicate other peers data once reconnected.

Clients

As examples of how to use the core client libraries, several test clients are included, a few of them are:

    chatbump: An interactive way to create, send and receive bumps.
    trackbumps: View (and decode) live bumps on the BuMP network.
    sendbump: Send files (or stdin) as BuMP packages.
    pipebump: As sendbump, but via a named pipe. Use your imagination.
    randombump: Send a message to one server, which will then be replicated.

Libraries

The core C libraries are libbumpclient and libbump. These give core access to the replicated BuMP network, via the three layers:

    The transport layer, which needs information about what peer(s) to connect to, and what SSL certificates and SSL certificate validation (if any) to set up.
    The filter layer, which specifies the set of BuMP packets to receive, based on sets of Tags as well as age and size.
    The packet layer, which needs information about which Tag to use for a BuMP packet. It also includes handling what passwords to use, what encryption to apply, whether to compress the packet or not, for that specific Tag, and vice versa for the BuMP packets coming in.

Download

If you know how, the most recent non-production ALPHA sources are also available from here, by just asking for the correct file name.

Use at your own risk: it might eat cute little puppies for breakfast, just for fun...

[For the archive we are giving a direct link do the downloads]
Applications
BChat - BuMP Chat

IRC like console chat application on top of the BuMP network. Bump Chat.
Misc

Screenshot: BuMP SQLite Database being viewed in Firefox SQLite Manager Extension: Screenshot 1
Contact

bump at mailvault.com

