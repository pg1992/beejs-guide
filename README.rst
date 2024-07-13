===================================
Beej's Guide to Network Programming
===================================

Stream Sockets (``SOCK_STREAM``)
    Reliable two-way connected communication streams.

Datagam Sockets (``SOCK_DGRAM``)
    Also called "connectionless sockets".

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
