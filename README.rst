===================================
Beej's Guide to Network Programming
===================================

Stream Sockets (``SOCK_STREAM``)
    Reliable two-way connected communication streams.  Used by TCP, *The
    Transmission Control Protocol* (see `RFC 793
    <https://tools.ietf.org/html/rfc793>`_).  TCP makes sure your data arrives
    sequentially and error-free.

    The IP, *Internet Protocol*, is the routing portion (`RFC 791
    <https://tools.ietf.org/html/rfc791>`_) of both TCP/IP and UDP/IP.

Datagam Sockets (``SOCK_DGRAM``)
    Also called "connectionless sockets".  Used by UDP, *User Datagram
    Protocol* (see `RFC 768 <https://tools.ietf.org/html/rfc768>`_).  The data
    may arrive out of order, but it will be correct in case it arrives.

Layered Network Model
    ISO/OSI 7 layers, which is very generic, is something like:

    * Application
    * Presentation
    * Session
    * Transport
    * Network
    * Data Link
    * Physical

    A layered model more consistent with Unix might be:

    * Application Layer (telnet, ftp, etc.)
    * Host-to-Host Transport Layer (TCP, UDP)
    * Internet Layer (IP and routing)
    * Network Access Layer (Ethernet, Wi-Fi, or whatever)

IPv4
    4 octets separated by dots: ``127.0.0.1``.  Equivalent to ``2^32`` possible
    addresses.

    The special loopback address is represented by ``127.0.0.1``.

    We can declare subnets in IPv4 by defining what is the *network* portion of
    the address and what is the *host* portion of the address.  For example,
    let's say that the first three bytes of an address is the network portion,
    and the last byte is the host portion, so an address like ``192.0.1.12``
    would be the host ``12`` on network ``192.0.1.0``.

    One-byte networks (a.k.a. *Class A* network), could have up to ``2^24``
    (approximately 16 millions) hosts; two-byte networks, or *Class B*
    networks, would have ``2^16`` hosts; and three-byte, or *Class C*,
    networks, could have up to ``2^8`` hosts.

    You can represent a subnet by using a *netmask*, which tells you how many
    bits you bitwise AND with the IP address to represent the network.  For
    example, you could have the netmask ``255.255.255.0`` to say that the first
    24 bits represent the network.  A simpler way is to just say how many bits
    represent the network portion by appending ``/24``, e.g. ``192.0.1.12/24``.

IPv6
    2-byte chunks represented by 4 hexadecimal numbers separated by colon, e.g.
    ``2001:0db8:c9d2:0012:0000:0000:0000:0051``.  This representation has a
    total of ``8 * 16 = 128`` bits, which is equivalent to ``2^128`` possible
    addresses.

    Leading zeroes can be suppressed, and whole chunks of zeroes can be
    compressed by 2 colons, e.g. ``2001:0db8:c9d2:0012:0000:0000:0000:0051 ==
    2001:db8:c9d2:12::51``.  The special loopback address, which means *the
    machine I'm running on now*, is represented by
    ``0000:0000:0000:0000:0000:0000:0000:0001 == ::1``.

    We can represent IPv4 addresses in IPv6 by prepending the IPv4 address with
    ``::ffff:``, e.g. ``192.0.2.33 -> ::ffff:192.0.2.33``.

    You can represent a subnet my appending the number of bits of the netmask
    after the address, e.g. ``2001:db8::/32`` or
    ``2001:db8:5413:4028::9db9/64``.

Port numbers
    If there are two or more applications in a computer using TCP or UDP, then
    it is not sufficient to specify the IP address of the computer.  That's why
    we have *port numbers*, which are 16-bit numbers used to differentiate
    applications in a host.  For example, Web Servers use port 80 to serve web
    pages using HTTP, or 443 for HTTPS.  Port numbers ``<1024`` are generally
    reserved for special applications and processes that use those ports may
    need special OS privileges.

Byte order
    There are two possible ways to store multi-byte data in memory:

    #. **Big-endian**: the MSB (*Most Significant Byte*) is stored first at
       lower addresses, e.g. ``b3cf`` would be stored as ``b3`` in memory
       address ``a``, and ``cf`` would be stored in memory address ``a + 1``.
       Hence the name *Big-endian*, because the *big end* is stored first.
    #. **Little-endian**: the opposite of *Big-endian*, where the LSB (*Least
       Significant Byte*) is stored first, e.g. ``b3cf`` is stored in memory as
       ``cf`` in address ``a`` and ``b3`` in address ``a + 1``.

    *Network Byte Order* is the byte order that network applications
    communicate.  It was defined (arbitrarily) to work in **big-endian**, which
    means that the MSB is sent first.  Conversely, the host may work with a
    different *endianness* (e.g. Intel processors work with
    *little-endian*, and you can check your host by running the ``lscpu``
    command which is part of the ``util-linux`` package in Arch Linux), which
    is known as *Host Byte Order*.  Applications must be aware about this in
    order to be portable, that's why we should never trust the *Host Byte
    Order* and use convenient functions to convert between each representation.
    Those functions are:

    ============= ===========================
     Function      Description
    ============= ===========================
     ``htons()``   host to network ``short`` 
     ``htonl()``   host to network ``long``  
     ``ntohs()``   network to host ``short`` 
     ``ntohl()``   network to host ``long``  
    ============= ===========================

``struct``\ s and descriptors
    A *socket descriptor* is just an

    .. code-block:: c

        int

    Which is deceptively simple...  But since everything in Unix is a file (or,
    more precisely, a *file descriptor*) then it is not that weird.

``struct addrinfo``
    Before creating connections and all that fun stuff, we need a way to
    to store address information, and also to retrieve it.

    .. code-block:: c
        :linenos:

        struct addrinfo {
            int              ai_flags;     // AI_PASSIVE, AI_CANONNAME, etc.
            int              ai_family;    // AF_INET, AF_INET6, AF_UNSPEC
            int              ai_socktype;  // SOCK_STREAM, SOCK_DGRAM
            int              ai_protocol;  // use 0 for "any"
            size_t           ai_addrlen;   // size of ai_addr in bytes
            struct sockaddr *ai_addr;      // struct sockaddr_in or _in6
            char            *ai_canonname; // full canonical hostname

            struct addrinfo *ai_next;      // linked list, next node
        };

